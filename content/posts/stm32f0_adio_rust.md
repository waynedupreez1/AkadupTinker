---
title: "STM32F0xx DigitalIO AnalogIn Rust"
date: 2020-09-13T08:42:00+12:00
showDate: true
draft: false
tags: ["software","stm32f0_adio"]
---

# Description

As mentioned in [STM32F0xx DigitalIO AnalogIn PCB](/AkadupTinker/posts/stm32f0_adio) one of the main reasons for me to take on this project is to learn some more rust lang. If rust is useful in an embedded setting I definitely will not subject myself to C again anytime soon, just kidding I like you C....nah not really.


# Setup
### Hardware

1. Target => STM32F030

This seemed like a pretty reasonable Cortex M0 chip the variation I use sports around 35 useful pins, 64K ROM and 8K RAM. The best reason for this is the cost of around 1USD for the low quantities I am buying.

2. Programmer => STlinkV2

The cheap Aliexpress variation which can be had for around 3USD.

### Software

1. IDE => VScode

I have been using this for a while now and is my main driver for python, c# and rust development.

2. OS => Ubuntu KDE 20.04 (Kubuntu)

Recently updated from Ubuntu KDE 18.04. Pretty stable though I have had some interesting things happens especially when docking and undocking.

### Getting Started Resources

There are a few other bits that needs installation like rust, GDB, OpenOCD etc.

The following resources are definitely worth checking out:

1. [Rust Embedded Book](https://rust-embedded.github.io/book/intro/index.html)
2. [Rust Embedded Discovery Book](https://docs.rust-embedded.org/discovery/)
3. [Cortex-M quick start](https://github.com/rust-embedded/cortex-m-quickstart)


# Debugging and Testing

After installing and setting up everything we can finally start writing code. 

Hang on! How will we test our code? 

### Hardware level debugging.

In the resources as linked above you will be guided to get a debug instance setup in vscode. 
This instance can be used to step through the code as the MCU executes it.
I made some modifications to this, this is one of the debuggers in my launch.json file.

```TOML
{
    /* Configuration for the STM32F030*/
    "type": "cortex-debug",
    "request": "launch",
    "name": "Debug (OpenOCD)",
    "servertype": "openocd",
    "cwd": "${workspaceRoot}",
    "preLaunchTask": "Build Debug (STM32)",
    "postDebugTask": "",
    "runToMain": true,
    "executable": "./target/thumbv6m-none-eabi/debug/${workspaceFolderBasename}",
    "device": "STM32F030C8T6",
    "configFiles": [
        "openocd.cfg",
    ],
    "svdFile": "${workspaceRoot}/.vscode/STM32F0x0.svd",
    "showDevDebugOutput": false,
    "internalConsoleOptions": "openOnSessionStart",
    "debuggerArgs": [
        "-x",
        "loader.gdb"
    ],
},
```

### Unit Testing.

Could we do unit testing by having the test code run on the host?
Yes we can but we need some cfg_attr. As seen below when not running the unit tests compile with no_std, no_main also do not import particular modules.

```rust
#![cfg_attr(test, allow(unused_imports))]
#![cfg_attr(not(test), no_std)]
#![cfg_attr(not(test), no_main)]

// set the panic handler
extern crate cortex_m_semihosting;
#[cfg(not(test))]
extern crate panic_semihosting;

#[cfg(not(test))]
use cortex_m_semihosting::hprintln;
use panic_semihosting as _;
```

# Code Size whilst Debug

I only have around 1 - 1.5k LOC and I am running out of space on the 64k ROM chip.

How can this be? I asked myself. 
If this is the size of a small codebase rust might not be all that useful in an embedded setting.

### The solution

Add optimization!!!

It seems like LLVM and rust includes tons of debug data in the binary when unoptimized what solved this problem for me was to add the following into my cargo file.

```TOML
[profile.dev]
opt-level = "z"
debug = true
debug-assertions = true
overflow-checks = true
lto = false
panic = 'unwind'
incremental = true
codegen-units = 256
rpath = false
```
Normally the opt-level = 0.


# RTOS and other embedded specific libraries

RTOS purely written in rust. Even though RTIC is not as stable as something like FREERTOS I found this to be perfect for what I was wanting to do. One of the main things this takes care of is the sharing of data between priority based interrupts, something that can easily lead to race conditions.

Check it out here: [RTIC](https://rtic.rs/0.5/book/en/preface.html)

Embedded Libraries: [Embedded Libs](https://github.com/rust-embedded/awesome-embedded-rust)

# Actual Code Example

Below is an example of the serial module used in this project.
You will notice the usage of traits and trait bounds, these are similar constructs to interfaces in c#.

The mod test section contains all of the unit tests for this module.

```rust
use embedded_hal::{digital, serial};

use crate::buf;
use buf::{Buf, IBuf};

use crate::dev;
use dev::IDev;

use crate::error;
use error::BufErr;
use error::DevErr;

use nb::block;

/// Serial Device
pub struct DevSerial<USART, TXEN, RXEN, SENDBUF, RECVBUF> {
    interface: USART,
    tx_en: TXEN,
    tx_buf: SENDBUF,
    rx_en: RXEN,
    rx_buf: RECVBUF,
}

impl<USART, TXEN, RXEN, SENDBUF, RECVBUF> DevSerial<USART, TXEN, RXEN, SENDBUF, RECVBUF>
where
    USART: serial::Write<u8> + serial::Read<u8>,
    TXEN: digital::v2::OutputPin,
    RXEN: digital::v2::OutputPin,
    SENDBUF: IBuf<u8>,
    RECVBUF: IBuf<u8>,
{
    pub fn new(interface: USART, tx_en: TXEN, tx_buf: SENDBUF, rx_en: RXEN, rx_buf: RECVBUF, ) -> Self {
        DevSerial {
            interface: interface,
            tx_en: tx_en,
            tx_buf: tx_buf,
            rx_en: rx_en,
            rx_buf: rx_buf,
        }
    }
}

impl<USART, TXEN, RXEN, SENDBUF, RECVBUF> IDev for DevSerial<USART, TXEN, RXEN, SENDBUF, RECVBUF>
where
    USART: serial::Write<u8> + serial::Read<u8>,
    TXEN: digital::v2::OutputPin,
    RXEN: digital::v2::OutputPin,
    SENDBUF: IBuf<u8>,
    RECVBUF: IBuf<u8>,
{
    /// Receive Byte
    fn get_recv_byte_mut(&mut self) -> Result<(), DevErr> {
        //Read serial port
        let data = block!(self.interface.read()).map_err(|_| DevErr::SerialErr)?;

        //Check if buffer is overflowing if it is then clear the buffer
        //This should not happen often unless we get lots of garbage
        //on the comms line
        match self.rx_buf.write_val_mut(data) {
            Ok(_) => {}
            Err(BufErr::Overflow) => { self.rx_buf.clear_mut()?; }
            _ => panic!("Empty"), //This should never happen
        };

        Ok(())
    }

    /// Return receive buffer as slice
    fn get_recv_buf_as_slice(&self) -> Result<&[u8], DevErr> {
        Ok(self.rx_buf.get_as_slice()?)
    }

    fn clear_recv_buf_from_first_to_val_mut(&mut self, val: u16) -> Result<(), DevErr> {
        self.rx_buf.clear_from_first_to_val_mut(val)?;

        Ok(())
    }

    /// Clear receive buffer
    fn clear_recv_buf_mut(&mut self) -> Result<(), DevErr> {
        self.rx_buf.clear_mut()?;

        Ok(())
    }

    /// Transmit a single u8 from buffer this will
    /// remove the transmitted byte from the buf
    /// This is useful for interupts
    fn transmit_single_u8_mut(&mut self) -> Result<(), DevErr> {
        block!(self.interface.write(self.tx_buf.get_first_val_mut()?))
            .map_err(|_| DevErr::SerialErr)?;

        Ok(())
    }

    /// Write a single u8 into the send buffer
    fn write_u8_to_send_buf_mut(&mut self, data: u8) -> Result<(), DevErr> {
        self.tx_buf.write_val_mut(data)?;

        Ok(())
    }

    /// Return send buffer as mut slice
    fn get_send_buf_as_mut_slice_mut(&mut self) -> Result<&mut [u8], DevErr> {
        Ok(self.tx_buf.get_as_mut_slice_mut()?)
    }

    /// Clear send buffer
    fn clear_send_buf_mut(&mut self) -> Result<(), DevErr> {
        self.tx_buf.clear_mut()?;

        Ok(())
    }
}

#[cfg(test)]
mod tests {

    #![allow(non_snake_case)]

    use super::*;
    use embedded_hal::digital::v2::{InputPin, OutputPin};
    use embedded_hal_mock::pin::{
        Mock as PinMock, State as PinState, Transaction as PinTransaction,
    };
    use embedded_hal_mock::MockError;

    use embedded_hal::blocking::serial::Write;
    use embedded_hal::serial::Read;
    use embedded_hal_mock::serial::{Mock as SerialMock, Transaction as SerialTransaction};

    use heapless::consts::{U128, U16, U256, U32, U64}; // type level integer used to specify capacity

    #[test]
    fn callGetRecvByteMut_set3ElementReturn3_expect3ElementsInOrder() {
        // Setup
        let dio_expectations = [PinTransaction::set(PinState::High)];

        // Create pin
        let tx_en_mock = PinMock::new(&dio_expectations);
        let rx_en_mock = PinMock::new(&dio_expectations);

        let first = 10;
        let second = 20;
        let third = 30;

        // Configure expectations
        let serial_expectations = [SerialTransaction::read_many([first, second, third])];

        let serial_mock = SerialMock::new(&serial_expectations);

        let tx_buf = buf::Buf::<u8, U16>::new();
        let rx_buf = buf::Buf::<u8, U16>::new();

        let mut rs485 = DevSerial {
            interface: serial_mock,
            tx_en: tx_en_mock,
            tx_buf: tx_buf,
            rx_en: rx_en_mock,
            rx_buf: rx_buf,
        };

        rs485.get_recv_byte_mut().unwrap();
        rs485.get_recv_byte_mut().unwrap();
        rs485.get_recv_byte_mut().unwrap();

        //println!("{:?}", rs485.get_recv_buf_as_slice().unwrap());

        //Test
        assert_eq!(first, rs485.get_recv_buf_as_slice().unwrap()[0]);
        assert_eq!(second, rs485.get_recv_buf_as_slice().unwrap()[1]);
        assert_eq!(third, rs485.get_recv_buf_as_slice().unwrap()[2]);
    }

    #[test]
    fn callClearRecvBufMut_set3ElementsThenClear_expectEmptyBuf() {
        // Setup
        let dio_expectations = [PinTransaction::set(PinState::High)];

        // Create pin
        let tx_en_mock = PinMock::new(&dio_expectations);
        let rx_en_mock = PinMock::new(&dio_expectations);

        let first = 10;
        let second = 20;
        let third = 30;

        // Configure expectations
        let serial_expectations = [SerialTransaction::read_many([first, second, third])];

        let serial_mock = SerialMock::new(&serial_expectations);

        let tx_buf = buf::Buf::<u8, U16>::new();
        let rx_buf = buf::Buf::<u8, U16>::new();

        let mut rs485 = DevSerial {
            interface: serial_mock,
            tx_en: tx_en_mock,
            tx_buf: tx_buf,
            rx_en: rx_en_mock,
            rx_buf: rx_buf,
        };

        rs485.get_recv_byte_mut().unwrap();
        rs485.get_recv_byte_mut().unwrap();
        rs485.get_recv_byte_mut().unwrap();

        rs485.clear_recv_buf_mut().unwrap();

        //println!("{:?}", rs485.get_recv_buf_as_slice().unwrap());

        let test: &[u8] = &[];

        //Test
        assert_eq!(test, rs485.get_recv_buf_as_slice().unwrap());
    }
}

```

### __And Thats it until the next one.__
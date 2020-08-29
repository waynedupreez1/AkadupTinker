---
title: "Eeprom 34c04"
date: 2020-03-08T11:38:00+12:00
showDate: true
draft: false
tags: ["software"]
---

# Description

So after geting some PCB's made I decided to try my hand at writing drivers for the 34C04 EEPROM
I2C chips that I use to identify each PCB plugged into the backplane board.

I decided to write this in rust as my C language foo is not so great, also rust is cool 
and I want to try it out for embedded work as I already use it for pc code development.

My setup is as follows:

1. IDE = VScode.
2. OS = Ubuntu - I run ubuntu at home and virtualize Windows where needed.
3. Target = STM32 - Blue Pill

# Issues

The first issue was getting it setup nicely with GDB for debugging, fortunatly the web is full of people doing it.
The second issues was to set it up in such a way that my unit test code is compiled and ran on my workstation and the
the application code is compiled and ran on the target, I managed to figure it out.

# Some example code:

```rust
let address = eeprom34c04::SlaveAddr::A2A1A0(false, true, true);

let mut eeprom = eeprom34c04::Eeprom34c04::new_34c04(i2c_setup, address);

let memory_address = 0x0F;

let data = 0xF0;

eeprom.write_byte(memory_address, data).unwrap();

delay.delay_ms(5u16);

let read_data = eeprom.read_byte(memory_address).unwrap();
```

## [Link to Github 34C04 repo](https://github.com/waynedupreez1/eeprom34c04-rs)

### __And Thats it until the next one.__
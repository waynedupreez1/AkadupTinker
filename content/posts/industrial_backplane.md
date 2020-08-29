---
title: "Industrial BackPlane"
date: 2020-03-08T11:38:00
showDate: true
draft: false
tags: ["hardware"]
---

# Description

I have managed to get some PCB's designed based on my requirements. 
Here is some 3D models of the first few boards I am getting made.

# Backplane PCB

This board is essentially the main component all other boards will plug into.

### Some features I tried to nail on this one:

1. Allowed for up to 4 boards to be plugged in.
2. Each slot is addressable.
3. Provide connectors for 24V, 3V3 and 5V to be connected directly into the backplane (This could be replaced by a plug in board).

By convention the CPU will be plugged into slot 1 and the PSU will be plugged into slot 4 though
this is just by convention and allowed space the backplane board have none of these restrictions
built in.

![Backplanev00](/gallery/backplanev00.jpeg)

# CPU PCB (RPI and STM32)

This is a board specifically designed for Raspberry Pi and STM32 based "Blue Pill" to interface with
the backplane. I however imagine there will be a CPU board designed for each SBC/Microcontroller that
needs to be interfaced with the backplane.

Some features I tried to nail on this one:

1. Allow the controller to read what it is. This is done via an EEPROM chip.
2. Expose all the low level peripheral(SPI, Serial, PWM etc) to the rest of the backplane.

By convention the CPU will be plugged into slot 1 and as explained above the backplane has no restriction that
forces this other than physical space.

![Cpu_STM32v00](/gallery/cpu_stm32v00.jpeg)

### __And Thats it until the next one.__
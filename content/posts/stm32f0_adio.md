---
title: "STM32F0xx DigitalIO AnalogIn PCB"
date: 2020-08-30T09:45:00+12:00
showDate: true
draft: false
tags: ["hardware","stm32f0_adio"]
---

# Description

After killing my backplane idea I decided to try my hand at a distributed IO type system.

# STM32F0 ADIO PCB

I decided to use a stm32F030 mcu on this for 2 reasons: 1. its cheap about 1USD 2. rust is fully supported.

There is also a few selectable options set with solder bridges, I will talk about these options all through the post.

# Digital:

I allowed for 8 selectable inputs or outputs.

### Inputs

![Input Circuit](/AkadupTinker/gallery/input.png)

The inputs can all accept 5 - 30V.


### Outputs

![Output Circuit](/AkadupTinker/gallery/output_lm393.png)

Four of the outputs have an LM393 as ouputs with selectable voltage range.

![Output Circuit Transistor](/AkadupTinker/gallery/output_lm393_transistor.png)

Four of the outputs have an LM393 driving a 200mA push pull output transister also voltage selectable

# Analog:

I allowed for 4 inputs all with +-10V input and 4-20mA selectable.

Two of these inputs also have controllable gain amplifiers.

![Input with Amplifier](/AkadupTinker/gallery/analog_in_with_amp.png)

# Communication

### Wired

Dedicated RS485

### Wireless

Interface for the following cheap(around 1USD) wireless board that can be bought from aliexpress etc.

1. ESP32-01 -> Wifi 
2. NRF24L01 -> 2.4G transeiver
3. RA-01 -> 433Mhz transeiver

# PCB Dimensions

This board is 50x60mm 

![STM32F0 ADIO](/AkadupTinker/gallery/stm32_adio.png)


### __And Thats it until the next one.__
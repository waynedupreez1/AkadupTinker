---
title: "Killing the Industrial BackPlane"
date: 2020-08-30T09:00:00+12:00
showDate: true
draft: false
tags: ["hardware","industrial_backplane"]
---

# Description

Killing of the Industrial backplane and the reasons why.

# Reasons

The main reason for killing this project is because of economics, let me explain.

The idea with this system was to create a generic backplane, then create a cpu module for every controller board that we want to interface with the backplane. 
These cpu modules could technically be anything stm32 blue pill's or raspberry pi's or any cheap controller. 
The typical size of the cpu module would be about 50mm x 50mm which would cost 15USD for a 4 layer board.

Now I always had in mind that the number 1 reason for using this system would be because it would effectively industrialize the inputs and outputs for the cheap controllers available.

The cost for this system can easily be reduced by creating a 50mm x 50mm board that already contains industrialized inputs and outputs. Many of these can then be linked up to have as many IO as neccesary. This then becomes similar to distributed IO systems.

I have opted to try my hand at distributed IO.


### __And Thats it until the next one.__
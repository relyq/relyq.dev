---
layout: ../../layouts/MarkdownPostLayout.astro
title: "HD44780 lcd driver"
pubDate: 2023-03-18
description: "hd44780 lcd display library for atmega328p"
author: "relyq"
type: "project"
tags: ["electronics", "embedded"]
---

in feb 2021 i wanted to learn more about low level microcontroller programming so i made a driver for the hd44780 lcd display controller.

i spent a week reading the atmega328p and hd44780 datasheets as well as learning about in-circuit serial programming, and the tools needed to cross-compile and upload the firmware to the microcontroller, such as makefiles, avr-gcc and avrdude.

i also had to learn about timings and pulses to communicate properly with the hardware.

it supports 8 bit-11 pins, 4 bit-7 pins, and 4 bit-6 pins modes with a simple 6 methods interface to write strings to the display or send commands.

## <a href="https://github.com/relyq/lcd_driver" target="_blank" rel="noopener noreferrer">source</a>

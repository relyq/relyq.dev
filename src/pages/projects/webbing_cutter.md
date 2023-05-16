---
layout: ../../layouts/MarkdownPostLayout.astro
title: "webbing cutter"
pubDate: 2023-03-18
description: "webbing cutter/stepper motor tester"
author: "relyq"
type: "project"
tags: ["electronics", "embedded"]
---

in november 2020 i was asked to make a firmware for a webbing cutter to make dog leashes. the frame of the machine was never finished but the controller firmware works as intended.

it has a 16x2 character lcd screen and a 4x4 matrix keypad.

you can set the strip length & number of strips to cut, and save up to four different programs to eeprom.

if all four programs are loaded, the controller will do all jobs in sequence.

the keys # and \* are used to confirm and delete respectively, and the keys A, B, C, D are used to select programs

it uses a stepper motor to move the strip forward and a 5v servo plus a limit switch to cut the strip with a hot wire

later on i was requested to modify the code to a stepper motor tester.
this later version changes the strips/length settings to distance, feedrate, acceleration, and direction.

## [source](https://github.com/relyq/webbingcutter)

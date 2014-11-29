---
layout: post
title:  "Nodebot Part 1 - Planning"
subtitle: "Or, making a bunch of uninformed decisions."
date:   2014-11-28 21:09:21
---

## Introduction to Robotics

A few months ago--having a basic understand of 1) electronics, 2) robotics, and 3) javascript--I decided I should definitely build a robot. Being an impulsive millenial I thought it best to begin my quest without actually learning anything new. I am a special genius that can do anything.

With a pile of microcontrollers and cashmoneydollars in hand, I began buying parts and bandsaw blades. As you do.

<div style="background-image: url('/img/mean-steve-front.jpg')" class="hero-image"></div>

## A brief foray into microcontrollers

Choosing the right microcontroller is like choosing a fine wine. You buy a bunch of them, drink all of them, and then when you wake up the next morning you close the blinds and ask everyone to be quiet.

There are probably at least a dozen microcontrollers out there. Maybe even more, I'd have to check. These are the ones I've tried so far:

* Arduino Uno
* Raspberry Pi Model B
* Arduino Yun
* **Intel Galileo**
* Tessel

These are great microcontollers (well, except for the Yun maybe). But we're building a nodebot, so we have a few requirements:

1. Can run Node.js
2. Can function untethered to a computer
2. At *least* 2 PWM pins - one for each motor
3. Wifi is a plus

The Arduino Uno can run Node, but only when tethered to a computer. The Raspberry Pi only has 1 PWM pin. The Tessel is probably up to the task, but the runtime is a work in progress and it's a little light on GPIO pins. This leaves us with the Arduino Yun and the Intel Galileo. The Yun is actually the perfect candidate on paper. Unfortunately I could never get it to work (more on that later). Luckily the Galileo is only slightly less perfect (more on that later).

## Node dot javascript

I started my college career as an electrical engineering student, and spent some time programming microcontrollers in C. Based on that experience, I never want to actually learn C. The space in my brain is better reserved for 30 Rock quotes and taco recipes. Javascript, on the other hand, I use every day (I also use 30 Rock quotes every day).

[Johnny-Five](https://github.com/rwaldron/johnny-five/) is the library I'm using to program my nodebot. Why? Well for one thing it's called Johnny-Five. It's also developed at Bocoup, which I am a big fan of. It's open source, it's actively developed by some great people, it's really good. It also supports -- or has plugins to support -- every board I've tried (Yun is in progress and no one is happy about it).

If you're interested in seeing the code that controls the robot, [it's all here](https://github.com/frxnz/nodebotnik/). We'll go through this in a little more detail in a future post.

## Basic principles of design

For my first robot I wanted to keep things pretty simple. I wanted something that I could build upon over time, but would be simple to get started. I decided to go with a 2-wheel design, using standard DC gear motors. DC motors are very easy to use, and the 2-wheel design means I don't have to worry about steering mechanisms -- spin the wheels at different speeds and the robot will turn.

Power is another thing I needed to consider. I decided to power the Galileo and the motors separately. This makes the wiring a *little* more complicated, but it means I can play with the motor voltage without having to worry about frying the controller. We'll talk more about power later.

<iframe class="vine-embed" src="https://vine.co/v/OiOViXFHBX0/embed/simple?related=0" width="600" height="600" frameborder="0"></iframe><script async src="//platform.vine.co/static/scripts/embed.js" charset="utf-8"></script>

## How did that whole not really planning thing go?

Good decisions I've made up to this point:

1. Intel Galileo (sort of)
2. Johnny-Five
3. 2-wheel design
4. Separate motor and board power supplies

Bad decisions I've made up to this point:

1. Thinking I only needed a 1amp board supply
2. Not calculating motor torque requirements
3. Not distributing weight evenly

The Galileo works pretty well. Had I started about a month later I probably would have used an Intel Edison instead. We'll dive deeper into this in a later post, but basically the Edison (and the Galileo Gen. 2) fixes all the issues I had with the Galileo.

I'm powering the board with a 5V 1A Jackery charger. The H-Bridge on its own tops out at about 1amp. Between the board and the RGB LED I had hooked up, I was definitely maxing out my power supply. When the LED was blinking the motors would kind of pulse. This was not the desired outcome.

I bought 175RPM DC gearmotors for my robot. Initially I was powering them with a 9V battery. The thing wouldn't budge on carpet, and was sluggish on hardwood. I switched to a 12V supply (8xAA batteries) which made a huge difference, but it was still sluggish on carpet. A lower gear ratio would probably yield better results on carpet. I also didn't balance the load well, which meant the wheel with more weight on it would spin a bit slower. This made straight lines difficult.

## Futurebot

The next steps will be to

* Swap out the Galileo for an Edison
* Swap out the gearmotors with high-torque continuous-rotation servos
* Swap out the AA batteries with a 4S LiPo
* Swap out the 5V 1A Jackery with a 2A model
* Add a couple infrared sensors to make it semi-autonomous

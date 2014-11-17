---
layout: post
title:  "Building a Nodebot - Part 1"
subtitle: "Learn from my mistakes, and make some of your own."
date:   2014-11-14 21:09:21
---

## Introduction to Robotics

At some point a couple months ago I decided I wanted to build a robot. A nodebot, to be specific. I had a handful of microcontrollers, a basic understanding of how robots work, and nearly a decade ago I spent 4 semesters nearly failing electrical engineering courses.

With these tools in hand, I decided to do no further research and just start building a robot. I bought a bunch of robot-y parts, built a chassis, and started hooking wires up. This is my journey.

In the photo below you can see four of the mistakes I made. That's not even all of them.

<div style="background-image: url('/img/mean-steve-front.jpg')" class="hero-image"></div>

## So many microcontrollers

My nodebot needed a brain. I had been playing around with Arduinos and such for a while, and they run Node real good for the most part. It was just a matter of picking the right one.

There are probably at least a dozen microcontrollers out there. Maybe even more, I'd have to check. These are the ones I've tried so far:

* Arduino Uno
* Raspberry Pi Model B
* Arduino Yun
* **Intel Galileo**

These are great microcontollers (well, except for the Yun maybe). But we're building a nodebot, so we have a few requirements:

1. Can run Node.js
2. Can function untethered to a computer
2. At *least* 2 PWM pins - one for each motor
3. Wifi is a plus

The Arduino Uno can run Node, but only when tethered to a computer. The Raspberry Pi only has 1 PWM pin. This leaves us with the Arduino Yun and the Intel Galileo. The Yun is actually the perfect candidate on paper. Unfortunately I could never get it to work (more on that later). Luckily the Galileo is only slightly less perfect (more on that later).

## Node dot javascript

I'm a web developer. I've written C before (remember those electrical engineering classes I was talking about?) I have no interest in actually learning C. The space in my brain is better reserved for 30 Rock quotes and taco recipes. Javascript, on the other hand, I use every day.

[Johnny-Five](https://github.com/rwaldron/johnny-five/) is the library I'm using to program my nodebot. Why? Well for one thing it's called Johnny-Five. It's also developed at Bocoup, which I am a big fan of. It's open source, it's actively developed by some great people, it's really good. It also supports -- or has plugins to support -- every board I've tried (Yun is in progress and no one is happy about it).

## Basic principles of design

For my first robot I wanted to keep things pretty simple. I wanted something that I could build upon over time, but would be simple to get started. I decided to go with a 2-wheel design, using standard DC gear motors. DC motors are very easy to use, and the 2-wheel design means I don't have to worry about steering mechanisms -- spin the wheels at different speeds and the robot will turn.

Power is another thing I needed to consider. I decided to power the Galileo and the motors separately. This makes the wiring a *little* more complicated, but it means I can play with the motor voltage without having to worry about frying the controller. We'll talk more about power later.

<iframe class="vine-embed" src="https://vine.co/v/OiOViXFHBX0/embed/simple?related=0" width="600" height="600" frameborder="0"></iframe><script async src="//platform.vine.co/static/scripts/embed.js" charset="utf-8"></script>

## Decision Tally

1. Intel Galileo
2. Johnny-Five
3. 2-wheel design
4. DC gear motors
5. Separate motor and board power supplies

So far we've only made 1 bad decision and 2 poorly informed decisions! Stay tuned to find out!
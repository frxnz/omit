---
layout: post
title:  "Nodebot Part 2 - Refinement"
subtitle: "Something about learning from your mistakes."
date:   2014-12-30 21:30:10
---

## Building a Better Robot

When I first set out on this journey, I had no idea what I was doing. I knew I wanted to build a javascript-powered robot, but that was it. After 6 weeks, several soldering-iron burns, and a few late nights, I'm happy to say I still have no idea what I'm doing.

When you begin a project with no plans, with no goals, the finish line keeps changing. You get to a certain point and you stop, and you think "hey I could make my robot do this", and then you start working on that. This turns a lot of the decisions you made previously into mistakes. Mistakes are good. Make lots of them.

But also, read spec sheets, and learn how to calculate torque. Otherwise you're going to waste a lot of money on motors.

## Improvement #1 - Intel Edison

Originally I was using an Intel Galileo (gen. 1) to control the bot. The original Galileo was a source of endless frustration, mostly because I couldn't get the wifi to work. If you do anything with nodebots, you will quickly learn to hate wifi.

The Edison came out shortly after I start building, and after a couple weeks of trying to get the wifi working on the Galileo I broke down and bought one (built-in wifi? yes please). The Edison has a few other advantages over the Galileo (even the Gen. 2):

* Built-in BLE
* The board itself is very compact, and additional breakouts don't increase the footprint much
* Draws very little power. The board itself can be powered with a 3.4v LiPo.

Getting the Edison set up was a breeze. Node comes pre-installed, so once you've got wifi configured you're off and running. Under the hood it's very similar to the Galileo, and within an hour or so I had it running my robot script.

### Light issues

The Edison great, but it has one major shortcoming: It only has 4 PWM pins. With the servos taking up 2 of the pins, it's hard to add anything else to the bot. For example, I wanted to add an RGB status LED, but that would require 3 PWM pins. Adding an I2C PWM driver could solve that problem, I'm also reconsidering using the Tessel with the servo module I already have.

## Improvement #2 - High-Torque Servos

My robot is fairly heavy. It's made of MDF and is fairly large. It also uses a little ~1/2" caster as the rear wheel, which tends to drag more than roll. The original motors I bought were tiny high-RPM DC motors. Even at 12V there just wasn't enough torque.

So I bought some high-torque continous servos. Not only does this give me more torque, it takes the motor controller out of the equation, which simplifies the circuit quite a bit. The servos also run off 6V, which is a bit easier to manage, battery-wise.

Despite requiring half the power, the gearing of the servos allows the robot to move much faster on most surfaces. Thick rugs still pose a problem (because of the ball-bearing caster), but it handles most terrain quite well.

### Not quite there

I'm very happy with the performance of the servos, but I'm definitely pushing their limits. Any additional load is definitely going to get into stall territory, and I can't increase the power at all. The next step will probably be to go back to DC motors, and get something with a little (or a lot) more power.

## Improvement #3 - Infrared Proximity Sensors

<div style="background-image: url('https://pbs.twimg.com/media/B5zDlzvIYAIdNIr.jpg:large')" class="hero-image"></div>

Controlling a robot is hard, so why not make it control itself? I added two short-range infrared sensors to the front of the bot, which allows it to detect obstacles at about 2-12 inches, and change direction to avoid them. This is the only control mechanism on the robot currently. It's kind of nice because it doesn't require wifi or bluetooth or anything.

### Looking forward

You can see the sensors in the photo above. Getting the angle just right is tricky, and it has trouble with very low objects, or walls that are at sharp angles to it. Angling them out more and possibly adding a third sensor in the middle would probably solve those problems.

What you can't see in the photo is all the superglue that would eventually be holding the sensor on the left together.

## FUTUREBOT

This little fella has come a long way, but I'm not done yet. In addition to fixing all the issues from above, I'm planning some other upgrades. I'll probably ditch the MDF chassis for something a little more modular/flexible. I'd also like to improve the steering mechanism, and add some sort of location awareness.

Enjoy this video of him not running into my shelf thing...

<iframe class="vine-embed" src="https://vine.co/v/OHHEuF6Bag3/embed/simple?related=0" width="600" height="600" frameborder="0"></iframe><script async src="//platform.vine.co/static/scripts/embed.js" charset="utf-8"></script>

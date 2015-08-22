---
layout: post
title:  "Johnny-Five: A Beginners Guide To Backpacks"
subtitle: "I, too, see that you want to do more with your bot."
date:   2015-08-20 21:09:21
---

It's early days, but Johnny-Five is bringing a whole new world of components to your nodebot. A near-limitless world where up is down and down is written in JavaScript.

## What are backpacks?

Johnny-Five is an asynchronous, event-based framework. Because of this there are certain things that it's either bad at, or simply can't do. Things that are highly timing-sensitive, or that use protocols that Node [firmata](https://github.com/jgautier/firmata) doesn't support. Things like NeoPixels, certain Ping sensors, Nintendo DS touch screens, and so on.

Backpacks are AVRs, or microcontollers, that run a special firmware designed to operate the component and communicate with Johnny-Five. The firmware is standard C++ or Arduino code with an additional layer for sending and receiving commands over I<sup>2</sup>C. Because much of the processing and data handling is done in Johnny-Five, this firmware tends to be much smaller than an equivalent program written entirely in C++. This means we can use much smaller AVRs.

While many of these backpacks could be little more than an ATtiny85, smaller Arduinos like the Arduino Pro Mini or Arduino Nano are a little more user-friendly. You can get these Arduinos for as little as $2, you don't have to worry about wiring, and you get a free Arduino-compatible bootloader.

## Why backpacks?

Historically things like NeoPixels and Ping sensors have required a custom version of Firmata, and were mostly not supported on platforms other than Arduino. If you had a project where you wanted NeoPixels *and* Ping sensors, you were stuck writing your own C++ library. Johnny-Five wants you to write JavaScript, not C++.

With backpacks you'll never need a custom Firmata, and your components will work on any platform that supports I<sup>2</sup>C ([most of them do](http://johnny-five.io/platform-support/)). Each backpack has its own firmware, which means you can run as many different components as you can fit on an I<sup>2</sup>C bus (which is a lot).

## How do they work?

To demonstrate how backpacks work, let's take a look at [node-pixel](https://github.com/ajfisher/node-pixel). Node-pixel is a library for controlling NeoPixels with JavaScript. It's amazing. For this demo we'll keep things simple and use an Arduino Uno as our host, and an Arduino Nano as our backpack.

### Load the firmware

First we need to load the firmware onto the backpack. If you've loaded a sketch onto an Arduino before, you know exactly what to do. In the Arduino IDE open `node-pixel/firmware/build/backpack/backpack.ino`, make sure you've got your board and port right, and upload the sketch to the Arduino Nano.

### Attach the backpack

The next step is to wire up the backpack. First you need to connect I<sup>2</sup>C pins. Connect SDA on the host microcontroller to SDA on the backpack, then connect SCL on the host microcontroller to SCL on the backpack. On most Arduino and Arduino-compatible boards SDA is pin A4, and SCL is pin A5, but check the pinout for your board if you're not sure.

![Attach I2C Bus](/img/backpack/backpack_i2c.png)

Next attach a ground pin on the backpack to ground on the host board. Finally, you can typically power the backpack directly from the host board. Find a 5V pin (or 3V3 if you're using a 3.3V backpack) and run it to the VIN or RAW pin on your backpack.

![Attach Power](/img/backpack/backpack_power.png)

### Hook up the NeoPixels

If you're new to NeoPixels, I highly recommend you read the [NeoPixel Überguide](https://learn.adafruit.com/adafruit-neopixel-uberguide/overview). NeoPixels are sensitive, and it's easy to damage them. I won't go into detail here, so read that first if you're not a NeoPixel pro.

All we have left to do is attach data to a pin on the backpack, and hook up (external) power to the strip. NeoPixels use a lot of power, so it's always a good idea to power them externally. Just make sure every piece of the circuit shares a common ground.

![Attach NeoPixels](/img/backpack/backpack_neopixels.png)

### Run your program

Now you're ready to run your Johnny-Five program just like you've always done. Just make sure you have Johnny-Five and node-pixel installed (`npm install johnny-five node-pixel`). We'll use this program for our demo:

<pre title="pixel.js"><code class="language-javascript">
var five = require("johnny-five");
var pixel = require("node-pixel");

var board = new five.Board();

var fps = 60;

board.on("ready", function() {

    var strip = new pixel.Strip({
        data: 6,
        length: 55,
        board: this,
        controller: "I2CBACKPACK"
    });

    strip.on("ready", function() {

        var colors = ["magenta"];
        var current_colors = [0,1,2,3,4];
        var current_pos = [0,1,2,3,4];
        var blinker = setInterval(function() {
            strip.color("#000");

            for (var i=0; i< current_pos.length; i++) {
                if (++current_pos[i] >= strip.stripLength()) {
                    current_pos[i] = 0;
                    if (++current_colors[i] >= colors.length) current_colors[i] = 0;
                }
                strip.pixel(current_pos[i]).color(colors[current_colors[i]]);
            }

            strip.show();
        }, 1000/fps);
    });
});
</code></pre>

### Et voila!

Run `node pixel.js` and suddenly: NeoPixels controlled by JavaScript!

<iframe src="https://vine.co/v/eDpBOv39jvr/embed/simple" class="vine-embed" width="600" height="600" frameborder="0"></iframe><script src="https://platform.vine.co/static/scripts/embed.js"></script>

And that, friends, is how backpacks work in the Johnny-Five ecosystem. If you have any questions about or want to contribute to a backpack-related project, join us in the [nodebots-interchange](https://gitter.im/ajfisher/nodebots-interchange) Gitter channel.

## Current Backpacks

Check out these awesome backpacks made by awesome people:

* [node-pixel](https://github.com/ajfisher/node-pixel) by [ajfisher](https://github.com/ajfisher)
* [DSTouch](https://github.com/rwaldron/j5-ds-touch) by [rwaldron](https://github.com/rwaldron)

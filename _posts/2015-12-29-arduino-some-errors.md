---
layout: post
title: Arduino in Linux - Some common errors
---

I wanted to run Arduino IDE in ubuntu 14.04. Though it earlier looked simple but while I wrote a simple blinky program following error occured.

```
avrdude: ser_open() : can’t open device “/dev/ttyUSB0” : Permission Denied 
ioctl(“TIOCMGET”): Inappropriate ioctl for device
```
<!--more-->

Note the device name `/dev/ttyUSB0` is a kind of module that caters to the connectivity and serial communication of the arduino board here.

So I began googling and after few searches I found the solution for the problem.

On the command line I wrote:

```
$sudo usermod -a -G dialout <username>
$sudo chmod a+rw /dev/ttyUSB0
```
where `<username>` is the name of your username in ubuntu. 

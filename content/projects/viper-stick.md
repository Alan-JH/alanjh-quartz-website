---
title: Viper Sidestick
date: 2022-12-16
---
#FlightSim

[Thingiverse make](https://www.thingiverse.com/make:875888)

In October '20, I started building my own joystick. I had been using a Saitek X52 Pro, which has a single spring for centering and no axis separation, which meant a deadzone in centering, and no feedback for crossing an axis. I found a [gimbal design by olukelo](https://www.thingiverse.com/thing:2496028)  that used cam centering and separated axes, but had no sensor mounts. I used [JFlyer81's F-16 joystick grip design](https://www.thingiverse.com/thing:4544115)  because I can't design anything remotely ergonomic. I had to print an adapter to make the two work together, and built all the electronics myself.

![[viper-gimbal-parts.jpg|Gimbal parts]]
### Grip

I first printed the grip in PLA and spent a few hours sanding it smooth, by going from 120 grit to 2000 grit sandpaper, but I stripped one of the tapped holes inside and had to reprint.

![[viper-stick-assem.jpg]]

For the reprint, I decided to use gap filler primer along with sanding to give a smoother finish. Another member of the HOTAS discord recommended that I use automotive epoxy black paint for its durability.

![[viper-stick-primed.jpg]]![[viper-stick-painted.jpg]]

The switches and hats all use 6mm tact switches in 3D printed enclosures, and the dual stage trigger uses two switches and a pen spring.

### Sensor mounts

I had to design my own sensor mounts into the gimbal, so I used 5mm diameter 10mm long neodymium magnets in slots in the gimbal parts, and mounted KMA210 magrez sensors over them. Hall sensors would not work in this configuration, as they are best used when sandwiched between magnets, not placed over them.

![[viper-base.jpg|Testing the sensor mount]]![[viper-base-covered.jpg|With cover and strain relief]]![[viper-base-final.jpg|With additional hot glue for strain relief]]

### Grip electronics

For the buttons in the grip, I used three shift registers mounted on veraboard to shift the 23 button inputs out over three data lines. I had to take wire from CAT5 ethernet cable because the 26 gauge wire I had was too thick to stuff inside the grip.

![[viper-stick-insides.jpg]]

### Base electronics

For the base electronics, I used an Arduino Pro Micro running MMJoy2 to act as a USB HID device, and an MCP3208 12 bit ADC to get a higher resolution reading out of the KMA210 sensors. I put these on perfboard and used JST-XH connectors for the sensors and grip.

![[viper-base-electronics.jpg]]

### Final assembly

The final product

![[viper-final-1.jpg]]![[viper-final-2.jpg]]![[viper-final-3.jpg]]
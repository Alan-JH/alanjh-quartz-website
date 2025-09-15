---
title: TKL-86 Keyboard
date: 2022-12-05
---
#PCB

Right before junior year, I decided to build my own keyboard. I chose Kailh Box Browns for the main keys and Kailh Box Jades for some of the modifier keys. I chose a custom 86 key tenkeyless layout, since I never used number pad and wanted the extra desk space, but also wanted the insert, home, delete, etc. keys. I used [KLE PCB Generator](https://github.com/jeroen94704/klepcbgen)  to generate the switch layout, then embedded an ATMega32u4 IC directly onto the board.

I ordered it and spent about an hour drag soldering the ATMega32u4 on (since this was before I had a hot air station), and another hour soldering all 86 SMD diodes. This was my second time drag soldering, the first time being the F-18C UFC.

![[keeb-solder.jpg]]

I designed up a 3D printed case, and went through several iterations trying to get the tolerance right. I ended up doing a lot of snipping with pliers to get the stabilizers to not get stuck, and the case is a little warped, but it works. I also added in tact switches for media control buttons in the top right. Finally, I generated a firmware in QMK and flashed it.

![[keeb-final.jpg|The finished product]]

I still daily drive this keyboard, and it continues to annoy my parents and anyone else who happens to be in the same room while I'm typing.
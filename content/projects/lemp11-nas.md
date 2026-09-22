---
title: Lemur Pro 11 -> NAS Conversion
date: 2026-09-19
---
System76 Lemur Pro 11 has been my daily driver laptop for four years, before upgrading to a Framework laptop 13. Since March it has been sitting around doing very little, and I thought I might try converting it to a mini NAS for offsite backup.

Why?
- Laptops in general but *especially* the lemp11 pro have excellent idle power draw. The i5 1235U achieves 1-2.5W at lowest C state idle, but at regular system idles is 4-8W. The Lemur Pro 11 is especially efficient with Pop! OS, and I would often get 10-12 hours of regular use out of the 73Wh pack at beginning of life. This is compared to my current offsite NAS which is an HP Compaq Pro 6300 SFF that idles at around 60W from the wall in TrueNAS. A laptop setup could conceivably draw less than 20W typical, cutting energy costs by a third.
- The battery pack can act as its own compact UPS, if SATA power supply is thought through carefully
- Built in display and keyboard makes for compact shell access. The laptop itself is also far more compact than the Compaq Pro 6300 SFF.
- It's almost free, no need to buy a Raspberry Pi or N100 mini PC or new DDR5 RAM. Just need to buy SATA to M.2 adapter, SATA power, and power supply circuitry.

lemp11 Pros:
- The lemp11 also has **two** M.2 2280 slots, not just one. This means we effectively have a PCIE slot that can be used for SATA expansion or even other PCIE expansion applications
- Chassis is fairly easy to take apart

lemp11 Cons:
- No builtin Ethernet NIC, so you need to use USB to Ethernet (discouraged) or replace the M.2 2230 wifi card with an Ethernet NIC
- Motherboard and other components do not have good board level documentation, so probing is required
- Support for TrueNAS on this specific model is unknown

![[lemp11-pro-motherboard.png]]

Here is the motherboard with a PH516 M.2 to SATA adapter installed. On the left M.2 slot is the wifi key, in the middle are the CPU and one 8GB DDR4 SODIMM (another 8GB is soldered on the board itself) and on the right are the WD Blue SN570 drive that came with the laptop, and battery power connector.

Immediately we are faced with a couple questions. First, do we keep the display and keyboard or do we discard them to aim for an even more compact footprint and custom case? Second, do we power the drives off of the battery, or off of the 19V jack? Off of the battery means probing to find either the battery rail or the protected system rail and soldering a wire to it, and hoping that drive transients are not enough to trip overcurrent protections, since 3.5" drives draw much more power than a laptop is deigned to provide to any of its devices. The 19V jack gives us freedom to more or less draw as much as we want as long as the external power supply can handle it, but there is risk of losing power to the drives mid write and causing issues, and the USB C PD charge connector cannot be used.

## Design

I decided to remove the display and keyboard in favor of a more compact solution, because the display isn't very useful anyways for TrueNAS and I'll want a JetKVM either way for remote access. The mainboard almost perfectly fits over two 3.5" drives placed lengthwise, and the battery cable can be folded so the battery sits on top of the mainboard (though in practice we'll want an air gap to keep the CPU heat from reaching the battery). The only downside to this is needing an M.2 extender to be able to use the second M.2 slot, which would otherwise hang off of the board. This is nice to have anyways because I can essentially tape down the M.2 2230 male part of the extender and have a proper screwed connection for the female part of the extender without having to measure and tolerance M.2 mounting screws.

Also necessary is a Wifi NIC. I bought an Intel I226-V based 2.5G NIC that fits into an M.2 A+E key. 

I plan to stack the board, battery, and HDDs, and design a custom drive backplane to provide power to it all, which taps off of the system internal power rail. Cooling will be provided by two 60mm or 40mm fans on each side, flowing air through the enclosure lengthwise and providing airflow to all components, so hopefully none overheat.

### System rails

![[lemp11-markup-1.jpg]]

I probed around to find the battery raw, system power rail, and the 19v jack input rail. The 19V jack is in yellow and pretty obvious since it's a DNP protection diode on a copper pour direct from the jack on the right. It read ~0V when on battery power or with battery disconnected and 19V with DC jack plugged in. Obviously.

The battery raw is also pretty obvious in red, big copper pour with some capacitors right by the battery connector. This read ~7.8V without external power applied and ~8.8V with external power applied. With the battery disconnected this still reads ~8.8V when external power is applied.

The system power rail is, I believe, orange, which appear to be a pair of input or output capacitors for a buck converter (if I had to guess, input, based on what looks like a FET sitting right between it and the inductor). With the battery connected this had 19V when power was applied to the DC jack and ~8.3V when power was not applied to the DC jack. WIth the battery disconnected it had 19V when external power was applied.

Bonus, the USB C PD charging functionality also provides 19V (all of the described behavior above is the same except for the DC jack pad). The green pad in the bottom right near the USB C connector exposes this power. It appears the green and red pads are diode OR'd together, as powering one does not power the other, sensibly.

### Firmware changes

We need a few custom firmware feature implementations, since the system76 bios is barebones and only exposes boot options.

1. AC Power restore boot - boot the system whenever AC is applied to the power adapter regardless of whether the laptop had gracefully shut down or had run out of battery previously
2. ~~Wake on LAN~~ - Keep our I226-V powered while shut down and enable wake on LAN signal. Initially I wanted this feature to be able to boot from command from e.g. a JetKVM but it turns out that the M.2 A+E key's wake signal isn't even connected to the EC chip that is capable of waking the system. So I had to ditch this idea and instead will rely on a Shelly plug to power cycle and use AC power restore boot to boot if necessary.
3. ~~Scheduled boot up~~ - Initially I wanted to be able to schedule boot up so that if any of the two prior features fail the machine automatically boots up at a specific time of day according to the CMOS clock, but this is much more complicated to implement than I expected and not worth doing
4. Battery power limits - Set battery charge/fully charged thresholds to 60% and 75%. Also possible to do in software but nice to do it in firmware. Without this the battery stays at 100% and risks puffing up over time. 60% to 75% provides hysteresis that reduces how often the pack cycles.

I basically Claude Code'd the firmware changes up in half an hour and applied them in stages, with checks of functionality in between. Github repo here: https://github.com/Alan-JH/ec/tree/lemp11-mods

While doing this, I found out that the I226-V M.2 A+E key that I bought off of amazon does not actually connect the CLK_REQ line to the I226-V chip, so the laptop's CLKREQ line on the M.2 A+E key was left floating high and the laptop wasn't providing a CLK to the M.2 key. So I had to do a little microsoldered wire mod to the I226-V M.2 key and short the CLKREQ contact (which as far as I could tell was not connected to anything on the actual M.2 key) to ground. 

![[lemp11-i226v-1.png]]

![[lemp11-i226v-2.png]]

After all that, LAN worked off of the chip. Unfortunately the M.2 A+E key on the lemp11 does not connect the wake signal to the EC, so Wake on LAN is not possible. This might be doable with hardware mods, but isn't really worth it. 

Sidenote: It's pretty incredible how well Claude Code set up the firmware to a flashable state. I really shouldn't have trusted it as much as I did, but I was in a big hurry and I figured worst case I'd have to buy an Atmega 2560 external programmer and flash the base firmware back if something broke. But nothing ever broke boot functionality. 
### Housing Concepts

Once the firmware was a proven concept, I disassembled what remained of the laptop and started measuring mounting holes for the case. I wanted to build a sort of stacked layout, with the battery on top, motherboard stacked below it with an air gap (CPU facing down), and HDDs on the bottom with a larger air gap for cable routing and main airflow. 

![[lemp11-mobo.png]]

I traced out the motherboard on graph paper to find dimensions between board outline and mounting holes. The two central mounting holes just happened to be horizontally aligned and exactly 4.5 inches apart (each square on the graph paper was 1/4"). I used these as my datum for most dimensions off the grid paper, and built out a CAD to 3D print models and check hole alignment. 

For the main housing itself I considered buying a 6x3x1/8" profile box stock 14" long of 6061 Al to save cost, which I can source for $60, but weighs 2lbs and has no anodization available, and would require me to drill all holes and openings myself. Slightly more expensive but potentially more visually appealing would be to design and order bent sheet metal parts at e.g. Sendcutsend with anodize. Either way I would size to be able to fit two 60mm exhaust fans on one end of the box, with air being pulled lengthwise across it. The box stock would require all internals to be assembled on a sliding structure before installation, while bent sheet metal can be installed in two parts without the need for sliding. The latter is especially attractive if I want to, for example, TIM the battery to the housing to ensure good cooling. Sheet metal would also be lighter, since I could use thinner materials than are available in box stock.

***WIP, TO BE CONTINUED***
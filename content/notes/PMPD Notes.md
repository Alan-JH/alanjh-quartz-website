---
date: 2026-10-05
title: PMPD Notes
---
### Plasma accelerator vs Ion accelerator

Gridded ion: Ionize mass, then push ions out using E field between two gridded plates, usually very close together and "focusing" the ions to mostly go through the holes in the grid. Like to use heavier noble gases because low ionizing energies per mass. Very efficient, most energy goes into accelerating the ions out.

To prevent ions from coming back and impacting at km/s due to negative charge on spacecraft, we place a neutralizer. Use a hot material that throws off electrons to neutralize the plume of the exhaust. 

Because contains positively charged particles, end up shielding other particles, nonlinear E field between the two plates, to the point where you may have a voltage higher at the first plate than the ion source, which doesnt accelerate ions out, and traps ions within the ion source. This pushes requirement for meters of grid area for millinewtons of force. 

voltage times current of grid is close to the kinetic power coming out, but not 100% complete. Ionization energy and neutralizer and ionizer are both inefficiency. Ionization source typically also contains a neutralizer

Grid voltage is strictly proportional to exhaust velocity. 800-ish volts -> 30km/s

Current essentially set by particle flux over area.

Another source of inefficiency is you wont ionize everything, you might get neutral particles, and sometimes you might get 2+ ionized particles. We dont want this because more ionization energy required, and it runs at higher velocity than ideal by sqrt(2).
- This is not good because youve spent 2x energy to increase momentum by sqrt(2) and therefore delta V by sqrt(2)

If we can accelerate plasma with magnetic field we can keep the area neutral charge


Traditional pulsed plasma thrsuter (PPT)
- Propellant is solid, vaporize a little bit of it with a high voltage ignitor on the end and then use anode and cathode to drive it like a rail gun
- Linear rails
- F ~ IxB
- current lines not perfectly straight due to hall effect
- Solid propellant erodes away so thrust is not constant over time
- Typically around 1kV and thousands of amps during the pulse
- OOM 0.1mN thrust, 1500s ISP, 15% power efficiency, 1W power avg

MPD steady state thruster:
- Pumping and blowing forces
- Blowing is the same as plasma thruster
- Pumping force is from magneticonstriction
	- Bunch of current going together, forces will act to pull them together
	- Produces region of high pressure plasma at the tip of the cathode, pushing the gas away from the cathode and reacting the cathode in the direction of thrust. 
- No one knows the best shape yet, but usually a conical nozzle w/ anode in center. 
- Usually runs off of gas propellant, simpler because no need to vaporize it.
- Needs thousands of amps and thosuands to millions of watts, thousands of watts of thermal energy going into the thruster, near impossible to cool
	- Metal will boil, will also sputter. 
- Arc goes to a micron size contact point on the cathode, which is the cathodic arc. Gets very hot. Cathode can emit electrons, which we dont want because they are lighter than ions and dont contribute as much to efficiency.
	- We prevent this erosion by maintaining 10s of atm of pressure at the contact point, which spreads the ions out against the constricting magnetic force. 
- Super high efficiency
- Typically hundreds of volts and thousands of amps
- OOM 1N thrust, 1MW power, 5000s ISP, 40% power efficiency

PMPD:
- Combine ignitor and pulse concept of PPT with mechanics of MPD to reduce the power
- Solves the thermal constraint
- Charge capacitor to provide kA discharge
- Use liquid into a frit, which gets vaporized. Dont use gas + valves because inefficient
- OOM 0.5mN, 3000s ISP, 10W power, 40% efficiency theoretically

Ignitor: glass tube method porous glass on the end, glass tube contains propellant, percolates through the porous glass, generates a thin layer of prop on the outside surface of the frit. Filaments on the inside of the frit and one filament on the outside of the frit. Since ASCENT is an ionic liquid, apply current from the distributed internal filaments to the one external filament to vaporize and generate gas around the outside filament, generates some plasma that expands outwards. Glasss frit has advantage of not causing late stage prop ablation. Somewhat self regulating because once vaporize prop more will come in to fill the gaps and bring back the miniscus on the outside of the frit. 
negative pulse the ignitor to avoid prop attaching to the ignitor instead of the negatively charged anode. 

cathode is tungsten for erosion resistance, anode is copper for conductivity

field emission electric propulsion (electrospray) - done by GPDM
statically charged propellant at an atomically sharp point will get pushed off like a spray, then a plate that electrostatically accelerates them. Similar to gridded ion but more compact. 


Hall effect- similar to gridded ion in that you have anode/gas distributor at the inside end of the thruster, positive relative to infinity (negative electrode). Ions get repelled away, neutralizer on the outside that either combines with plume or gets sucked in towards the anode. The latter causes ionization of the gas. Magnetic coils on the thruster body that produces a magnetic field from the outside into the inside pole., circulating a hall effect current around the thruster's center pole. This traps electrons in the mouth of the thruster, which generates the neutral acceleration region. Some consider it emag, some estatic, but it doesnt use magneitc forces so its basically estatic. 

Anode gets super hot. Unsolved physics problem called anomalous electron current.


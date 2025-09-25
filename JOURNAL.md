Today, we're going to be designing our own dev board, using one of the most popular and beginner friendly SoC's, the RP2040. This guide doesn't serve as just a tutorial, but also as an opportunity to learn what everything on the PCB fundamentally does, and what every single component on your PCB is actually for!

Now let's start off with the basic question, what's an SoC! An SoC or system on chip, basically has all the basic components like SRAM, processors, USB controllers, and other peripherals you'll break out onto your board. The RP2040 is a good starting microcontroller, because the datasheets are simple, it's low-cost, has good on-chip memory and is really flexible with plenty of IO's.

Now let's get right into it, we'll be using KiCad for this tutorial, and I would suggest completing the hackpad tutorial and maybe a keyboard before trying to make your own devboard, not because you won't be able to make it, but you'll understand how it works a bit better.

So create a new KiCad project by going:
`File -> new project, and choosing your name/folder for the project`

After that, double click your schematic to start working on your PCB. PCB's essentially have 2 main parts, the schematic, and the actual PCB. 

The schematic is basically a wiring diagram, that shows how everything will connect, but isn't like exactly where the components are placed or how thick your traces are, it's solely to show how everything is wired, not where.

ADD IMAGE HERE

The PCB editor is where you'll place down all your components and route everything for when you get it actually manufactured.

ADD IMAGE HERE OF FINAL THING

So enter in your schematic, and then tap "a", this will open up the symbol library, which is the place where you can find component blocks that you'll wire together to form the schematic for your project. Search for the RP2040, and just place it down in the center of your schematic.

![[Pasted image 20250925070320.png]]
![[Pasted image 20250925070335.png]]
You'll notice the symbol and actual component are 2 different things if you look at the first screenshot. The symbol just tells you all the pins on the component, and how they'll be wired to what.

Our entire schematic will consist of 5 main elements: power, flash storage, the crystal oscillator, I/O (input/outputs) and a surprise element you'll be challenged to add at the end! The raspberry pi datasheet explains how all of this will pretty much be wired, and I'm kind of just here to explain exactly how it all works too.

So first let's talk about power!

![[Pasted image 20250925071047.png]]

You'll notice that the RP2040 has capacitors, these are called decoupling capacitors. These capacitors are used for 2 main things, filtering out power supply noise and giving a local power supply if components need it at short notice. You can think of it like a stream of water, without the capacitors it can be jittery and unpredictable, but with the capacitors, the stream smoothens out, making your PCB function more reliable.

You usually want to put one 0.1uF (or 100nF, the F stands for Farads) decoupling capacitor per power pin, but it's fine to deviate a bit from that, but that's the most optimal way of doing it and what we're going to do.

We're also going to put a 1uF decoupling capacitor on each power line. You'll notice that the RP2040 has a +1V1 (1.1V) and a 3V3 (3.3V) line, we want to put a 1uF decoupling capacitor per line, to act as a larger reservoir and to smoothen out *larger* ripples that could occur. With this combination, we'll filter out nearly all the noise and have a smooth functioning PCB.

So go back into your schematic and then tap on the "Draw Wires" icon to connect the VREF_VOUT and DVDD, and then separately connect the IO_VDD, USB_VDD, ADC_AVDD and VREG_IN, because these pins use different voltage lines.

![[Pasted image 20250925072335.png]]

Then tap "p" to open up the POWER symbol library (you can also tap a, but searching in p will be faster because there's less symbols), and search for "1V1" and "3V3" and place the 1.1V on the VREG_VOUT and DVDD, and 3.3V on the IOVDD and those other pins.

![[Pasted image 20250925072502.png]]

Now schematic good practices is to always put at least a small wire between symbols like this for clarity. 

Now that we have our power symbols in, we're going to add the decoupling. You could technically wire them like the screenshot I showed before, but I prefer to separate them because you use less wired which I find looks cleaner, but it's up to personal preference.

You'll also notice that the symbol contains less pins than the symbol the RP2040 datasheet has, this is because symbols in KiCad tend to not repeat the same pins, so they just merge like all the same VDD pins into one.

But using the RP2040 datasheet as reference, we know that there's 8 IO VDD pins, so eight 0.1uF decoupling, and one 1uF cap because we're wiring the entire 3.3V line and need to smooth out the larger ripples. So let's just place all those in!

Again type "a" and search for "c" (the shorthand for capacitor). Make sure to double tap the capacitors to add a value, and make eight of them 0.1uF, and one of them 1uF.

![[Pasted image 20250925102628.png]]

These are the decoupling capacitors for the 3.3V line, now we need to do the caps for the 1.1V line. There's 2 VDD pins, so two, 0.1uF caps, and we need one for the line too, so a 1uF cap aswell:

![[Pasted image 20250925103109.png]]

Now we have all of our power decoupling. We also need to connect GND to the SoC, this is pretty self-explanatory, but it allows power to actually flow properly.

![[Pasted image 20250925103224.png]]

We have our power decoupling, but we don't actually have a power source yet or a way to program our devboard yet, so let's do that now. I'm going to be using USB-C because it's standard, fast and I kind of want to add a motor driver to my board for fun!

So tap "a", type in whatever receptacle you want, and add it in. Make sure you pick "receptacle" and not plug because a plug would plug into your laptop instead of having a cable plug into it.

![[Pasted image 20250925103733.png]]

Now let's explain each of these pins:
- SHIELD/GND will both go to ground, shield is conductive material wrapped around the data pins on the receptacle, and this just improves EMI by grounding it.
- D+/D- are the data pins, these transfer data to/from the USB-C receptacle. You'll want to connect the D-'s and D+'s together so that they both transfer data.
- CC1 and CC2 basically tell the receptacle to allow power to go through to power the board. These by standard (the datasheet tells you) are pulled down (go to GND) through 5.1K resistors.
- VBUS is the 5V input, this will need to be stepped down to 3.3V to power our MCU (microcontroller)

Now that we know what everything does, let's wire it up. Shield/GND go to GND:

![[Pasted image 20250925105339.png]]

D+ and D- are attached to their relative pair, and then will go into the MCU, but for now, we'll just have a net label going out of them. Net labels are basically like little teleporters, that allow you to say that something is wiring, without manually putting a wire between them.
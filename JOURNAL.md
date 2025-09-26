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

![Pasted image 20250925070320.png](journal/Pasted%20image%2020250925070320.png)
![Pasted image 20250925070335.png](journal/Pasted%20image%2020250925070335.png)
You'll notice the symbol and actual component are 2 different things if you look at the first screenshot. The symbol just tells you all the pins on the component, and how they'll be wired to what.

Our entire schematic will consist of 5 main elements: power, flash storage, the crystal oscillator, I/O (input/outputs) and a surprise element you'll be challenged to add at the end! The raspberry pi datasheet explains how all of this will pretty much be wired, and I'm kind of just here to explain exactly how it all works too.

So first let's talk about power and some schematic good practices!

![Pasted image 20250925071047.png](journal/Pasted%20image%2020250925071047.png)

You'll notice that the RP2040 has capacitors, these are called decoupling capacitors. These capacitors are used for 2 main things, filtering out power supply noise and giving a local power supply if components need it at short notice. You can think of it like a stream of water, without the capacitors it can be jittery and unpredictable, but with the capacitors, the stream smoothens out, making your PCB function more reliable.

You usually want to put one 0.1uF (or 100nF, the F stands for Farads) decoupling capacitor per power pin, but it's fine to deviate a bit from that, but that's the most optimal way of doing it and what we're going to do.

We're also going to put a 1uF decoupling capacitor on each power line. You'll notice that the RP2040 has a +1V1 (1.1V) and a 3V3 (3.3V) line, we want to put a 1uF decoupling capacitor per line, to act as a larger reservoir and to smoothen out *larger* ripples that could occur. With this combination, we'll filter out nearly all the noise and have a smooth functioning PCB.

So go back into your schematic and then tap on the "Draw Wires" icon to connect the VREF_VOUT and DVDD, and then separately connect the IO_VDD, USB_VDD, ADC_AVDD and VREG_IN, because these pins use different voltage lines.

**Now before we go further, remember that all power labels face UPWARDS, and all ground labels face DOWNWARDS, this isn't necessary, but it's good schematic practices that you should always follow**

![Pasted image 20250925072335.png](journal/Pasted%20image%2020250925072335.png)

Then tap "p" to open up the POWER symbol library (you can also tap a, but searching in p will be faster because there's less symbols), and search for "1V1" and "3V3" and place the 1.1V on the VREG_VOUT and DVDD, and 3.3V on the IOVDD and those other pins.

![Pasted image 20250925072502.png](journal/Pasted%20image%2020250925072502.png)

Now schematic good practices is to always put at least a small wire between symbols like this for clarity. 

Now that we have our power symbols in, we're going to add the decoupling. You could technically wire them like the screenshot I showed before, but I prefer to separate them because you use less wired which I find looks cleaner, but it's up to personal preference.

You'll also notice that the symbol contains less pins than the symbol the RP2040 datasheet has, this is because symbols in KiCad tend to not repeat the same pins, so they just merge like all the same VDD pins into one.

But using the RP2040 datasheet as reference, we know that there's 8 IO VDD pins, so eight 0.1uF decoupling, and one 1uF cap because we're wiring the entire 3.3V line and need to smooth out the larger ripples. So let's just place all those in!

Again type "a" and search for "c" (the shorthand for capacitor). Make sure to double tap the capacitors to add a value, and make eight of them 0.1uF, and one of them 1uF.

![Pasted image 20250925102628.png](journal/Pasted%20image%2020250925102628.png)

These are the decoupling capacitors for the 3.3V line, now we need to do the caps for the 1.1V line. There's 2 VDD pins, so two, 0.1uF caps, and we need one for the line too, so a 1uF cap aswell:

![Pasted image 20250925103109.png](journal/Pasted%20image%2020250925103109.png)

Now we have all of our power decoupling. We also need to connect GND to the SoC, this is pretty self-explanatory, but it allows power to actually flow properly.

![Pasted image 20250925103224.png](journal/Pasted%20image%2020250925103224.png)

We have our power decoupling, but we don't actually have a power source yet or a way to program our devboard yet, so let's do that now. I'm going to be using USB-C because it's standard, fast and I kind of want to add a motor driver to my board for fun!

So tap "a", type in whatever receptacle you want, and add it in. Make sure you pick "receptacle" and not plug because a plug would plug into your laptop instead of having a cable plug into it.

![Pasted image 20250925103733.png](journal/Pasted%20image%2020250925103733.png)

Now let's explain each of these pins:
- SHIELD/GND will both go to ground, shield is conductive material wrapped around the data pins on the receptacle, and this just improves EMI by grounding it.
- D+/D- are the data pins, these transfer data to/from the USB-C receptacle. You'll want to connect the D-'s and D+'s together so that they both transfer data.
- CC1 and CC2 basically tell the receptacle to allow power to go through to power the board. These by standard (the datasheet tells you) are pulled down (go to GND) through 5.1K resistors.
- VBUS is the 5V input, this will need to be stepped down to 3.3V to power our MCU (microcontroller)

Now that we know what everything does, let's wire it up. Shield/GND go to GND:

![Pasted image 20250925105339.png](journal/Pasted%20image%2020250925105339.png)

D+ and D- are attached to their relative pair, and then will go into the MCU, but for now, we'll just have a global label going out of them. Global labels are basically like little teleporters, that allow you to say that something is wiring, without manually putting a wire between them. 

Technically global labels are meant to be used between different schematic sheets and net labels would be the correct thing to use here, but I find global labels are cleaner if you only have one schematic for your PCB.

To do this, tap global label in the right hand toolbar, type in the name for your label (USB_D+ and USB_D-), and add them to the pins:

![Pasted image 20250925164006.png](journal/Pasted%20image%2020250925164006.png)
Next, pulldown the CC pins through a 5.1K resistor to GND to enable power to go through the USB-C receptacle. Open up the symbol library, and then type "r" the shorthand for resistors, and then place it down and edit the value to be 5.1K:

![Pasted image 20250925163923.png](journal/Pasted%20image%2020250925163923.png)

**Remember to follow proper schematic good practices, and to have clearly visible values, and labels for your components**. Feel free to edit the text of stuff to make your schematic cleaner, just don't make stuff *too* small.

Now we just need to wire in the input voltage, but the thing is, the voltage of USB-C is 5V, while the voltage that the RP2040 uses as input, needs to be 3.3V so you don't cook it. To achieve this, we'll use what's called an LDO, or a Low-Dropout Regulator to take the voltage down.

Specifically, we'll be using the **NCP1117**, a classic and reliable *fixed* voltage regulator. A fixed voltage regulator is handy here, because we only need to go down to 3.3V instead of like 1.5V per say or something random, and it uses less components. We'll also be using the SOT-223 footprint (or package is the common term) because it's small and we don't really have any thermal issues with a devboard.

So add in the **NCP1117-3.3_SOT223** symbol, wire GND and attach VBUS to the VI (voltage input) of the LDO.

![Pasted image 20250925163857.png](journal/Pasted%20image%2020250925163857.png)

Remember to always keep your schematic clean and feel free to use up quite a bit of space. Now like the decoupling capacitors on our RP2040, we need capacitors on the LDO. But we don't need fine decoupling capacitors for precise input lines into an MCU, and instead we need **bulk** capacitors, to handle the large voltage ripples when moving a voltage down.

So we need to place two, 10uF capacitors on each side of the LDO, for input/output, so add them into your schematic:

![Pasted image 20250925163830.png](journal/Pasted%20image%2020250925163830.png)

Next, we want to add our power labels to the LDO, we'll put a VBUS label **before** the LDO/Bulk cap, and a +3V3 label to the VO (voltage out) of the LDO. We might use 5V to power some other devices so we'll want to provide a power line for that too:

![Pasted image 20250925163756.png](journal/Pasted%20image%2020250925163756.png)

Now to finish off the USB-C wiring, we need to make sure the MCU receives the data lines. It's standard to have these going through 27 ohm resistors into the MCU to prevent distortions of the signals at high speeds, these are called *termination resistors*.

So wire the USB D+ and D- pairs into the MCU USB_DP and USB_DM (the P is for + and the M is for -) through 27 ohm resistors:

![Pasted image 20250925164239.png](journal/Pasted%20image%2020250925164239.png)

Now USB D+ and D- are actually what's called "bidirectional", this means that they work both ways. You don't actually need to specify this, but good schematic practices is to make sure your global labels reflect that. Currently they're just set as "inputs" because the triangle is facing inwards, so double click on all the D+ and D- labels and set them to bidirectional:

![[Pasted image 20250926064330.png]]

Now to make our USB and other peripherals actually work properly, we need to have what's called a clock oscillator. This is a little piezoelectric quartz crystal that vibrates very precisely, and then it's amplified and fed into the MCU to act as a clock signal that controls the digital peripherals.

For example, you definitely want a crystal oscillator if you're using USB-C, because the data needs to come in at specific times, so it makes sure no data is incorrectly received. Because the clock is such a precise component, you want to wire it really carefully. That means it should be as close to the MCU as possible on the PCB (schematic doesn't matter, it's just a reference), and it needs really small capacitors, to smooth tiny voltage ripples that could affect the signals.

First add the global labels to the MCU XIN and XOUT, just called their relative name. XOUT is the output from the crystal so an input to the MCU, and XIN is an output from the MCU to help the crystal oscillate properly:

![[Pasted image 20250926064501.png]]

Remember to accurately represent your global label direction, but just keep in mind it doesn't actually change your schematic, it's only for whoever is reading it!

Based off the RP2040 datasheet, we're going to be using a 12 MHz crystal with two, 15pF decoupling capacitors. **Make sure to use the crystal footprint with 4 pins and 1 and 3, as the input/output pins so pay attention to the symbol I use**:

![[Pasted image 20250926063705.png]]

Pins 2, and 4 just go to GND, pins 1 and 3 need a 15pF cap in series, and XOUT will have a 1K resistor. This resistor is called a damping resistor and it prevents the crystal from being damaged and ensures good signal integrity:

![[Pasted image 20250926064920.png]]

Remember all your schematic good practices and make sure everything looks clean.

We haven't actually seen these types of caps yet, these are called external load capacitors, and they're placed in series with the crystal I/O's, these basically just ensure that the crystal resonates at it's proper frequency, I'd suggest researching a bit more if you're interested!







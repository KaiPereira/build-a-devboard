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

## Starting the schematic

So enter in your schematic, and then tap "a", this will open up the symbol library, which is the place where you can find component blocks that you'll wire together to form the schematic for your project. Search for the RP2040, and just place it down in the center of your schematic.

![Pasted image 20250925070320.png](journal/Pasted%20image%2020250925070320.png)
![Pasted image 20250925070335.png](journal/Pasted%20image%2020250925070335.png)
You'll notice the symbol and actual component are 2 different things if you look at the first screenshot. The symbol just tells you all the pins on the component, and how they'll be wired to what.

Our entire schematic will consist of 5 main elements: power, flash storage, the crystal oscillator, I/O (input/outputs) and a surprise element you'll be challenged to add at the end! The raspberry pi datasheet explains how all of this will pretty much be wired, and I'm kind of just here to explain exactly how it all works too.

So first let's talk about power and some schematic good practices!

![Pasted image 20250925071047.png](journal/Pasted%20image%2020250925071047.png)

You'll notice that the RP2040 has capacitors, these are called decoupling capacitors. These capacitors are used for 2 main things, filtering out power supply noise and giving a local power supply if components need it at short notice. You can think of it like a stream of water, without the capacitors it can be jittery and unpredictable, but with the capacitors, the stream smoothens out, making your PCB function more reliable.

You usually want to put one 0.1uF (or 100nF, the F stands for Farads) decoupling capacitor per power pin, but it's fine to deviate a bit from that, but that's the most optimal way of doing it and what we're going to do.

We're also going to put a 1uF decoupling capacitor on each power line. You'll notice that the RP2040 has a +1V1 (1.1V) and a 3V3 (3.3V) line, we want to put a 1uF decoupling capacitor per line, to act as a larger reservoir and to smoothen out *larger* ripples that could occur. With the RP2040, these 1uF capacitors are mostly to help provide a stable 1.1V supply. With this combination, we'll filter out nearly all the noise and have a smooth functioning PCB.

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

## Working on USB-C

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

![Pasted image 20250926064330.png](journal/Pasted%20image%2020250926064330.png)

## The crystal oscillator

Now to make our USB and other peripherals actually work properly, we need to have what's called a clock oscillator. This is a little piezoelectric quartz crystal that vibrates very precisely, and then it's amplified and fed into the MCU to act as a clock signal that controls the digital peripherals.

For example, you definitely want a crystal oscillator if you're using USB-C, because the data needs to come in at specific times, so it makes sure no data is incorrectly received. Because the clock is such a precise component, you want to wire it really carefully. That means it should be as close to the MCU as possible on the PCB (schematic doesn't matter, it's just a reference), and it needs really small capacitors, to smooth tiny voltage ripples that could affect the signals.

First add the global labels to the MCU XIN and XOUT, just called their relative name. XOUT is the output from the crystal so an input to the MCU, and XIN is an output from the MCU to help the crystal oscillate properly:

![Pasted image 20250926064501.png](journal/Pasted%20image%2020250926064501.png)

Remember to accurately represent your global label direction, but just keep in mind it doesn't actually change your schematic, it's only for whoever is reading it!

Based off the RP2040 datasheet, we're going to be using a 12 MHz crystal with two, 15pF decoupling capacitors. **Make sure to use the crystal footprint with 4 pins and 1 and 3, as the input/output pins so pay attention to the symbol I use**:

![Pasted image 20250926063705.png](journal/Pasted%20image%2020250926063705.png)

Pins 2, and 4 just go to GND, pins 1 and 3 need a 15pF cap in series, and XOUT will have a 1K resistor. This resistor is called a damping resistor and it prevents the crystal from being damaged and ensures good signal integrity:

![Pasted image 20250926064920.png](journal/Pasted%20image%2020250926064920.png)

Remember all your schematic good practices and make sure everything looks clean.

We haven't actually seen these types of caps yet, these are called external load capacitors, and they're placed in series with the crystal I/O's, these basically just ensure that the crystal resonates at it's proper frequency, I'd suggest researching a bit more if you're interested!

## Flash storage

Now lot's of SoC's include flash storage, but the RP2040 actually doesn't, so we need to add on our own flash storage! You can think of flash storage as like a faster version of an HDD, with less power consumption, more reliability but is usually a bit more expensive.

Sadly, the RP2040 only supports up to 16mb of memory, so we'll just use a quad SPI flash memory IC (integrated circuit, those little chips on a board) like the **W25Q128JVS** used in the datasheet.

Now before we actually add it to our schematic, let's talk about what SPI is. If you continue to build PCB's, you'll see this communication interface very often, it's basically just a standardized way of transferring data. The signal comes out of the master, and then goes into slave devices. The master is our MCU in this case, and the slave, is our flash memory.

![Pasted image 20250926085431.png](journal/Pasted%20image%2020250926085431.png)
It has 4 major pins you need to understand:
- MOSI - Master output, slave input
- MISO - Master input, slave output
- SCLK - Clock signal (remember that oscillator we added to our board, this will basically do that for other devices)
- SS/CS - Slave select, let's you choose what device you're communicating with

So you usually need to have all 4 of those, and then you can add SS pins as you wish if you want to communicate with more and more devices.

**But we're actually using quad SPI in this case.** 

![Pasted image 20250926090037.png](journal/Pasted%20image%2020250926090037.png)

Quad SPI uses the same CLK and CS pin, but has 4 IO pins, so it can transfer data, 4x as fast as SPI, which is ideal for flash memory, but it does take up more pins, so that's why it's not always used. 

Now you can't just attach SPI to any GPIO, you have to use what's called a hardware controller, which you can imagine, is like a little block on the RP2040 SoC that is specifically meant for SPI. There are 2 SPI controllers on the RP2040, so we're going to use them for our flash memory. You can also technically do SPI via software, but it just makes way more sense to use the actual controller provided.

So add a global label to the QSPI pins with their relative name, IO's are bidirectional, and CLK and CS/SS are inputs to the slave (the flash memory) or outputs from the MCU.

![Pasted image 20250926091004.png](journal/Pasted%20image%2020250926091004.png)

Next, add in our flash memory IC (chip), **W25Q128JVS**, and wire up all the QSPI pins, and put GND to GND, and VCC to 3.3V:

![Pasted image 20250926091609.png](journal/Pasted%20image%2020250926091609.png)

Next, we need to add our 100nF/0.1uF decoupling capacitor to our VCC line to filter high-frequency noise. And then, we're going to add a button to the CS line, so that we can enter what's called BOOTSEL mode.

Based off of the RP2040 datasheet, if the QSPI SS pin, see's a 0 or GND when it's booting up, it'll go into BOOTSEL, where it will appear as a USB device on our computer so that we can copy code onto it to set it up.

![Pasted image 20250926093017.png](journal/Pasted%20image%2020250926093017.png)

Now there's 2 resistors you're probably wondering about here, the pullup to 3.3V, and the one in series with the button.

The pullup to 3.3V is important, because usually the QSPI pin will show up as 3.3V to the flash memory, but during bootup, you can't guarantee that it will, because the pin isn't active, so you might have some weird thing that happens with your board. The 10K resistor is just standard that the RP2040 datasheet wants us to use (and is also pretty commonly used to filter noise and stuff).

The 1K resistor in series limits the amount of current that can flow in this part of the circuit to prevent damage to the CS pin.

And just like that, we have our button and decoupling in, and our flash memory is completed!

## Breaking out I/O Headers

Now we have all the components for our board to actually work, so we just need to breakout all the GPIO's on the RP2040, onto header pins so that we can use them in our circuit and whatnot!

But before we do this, let's just make sure we attach TESTEN to GND on the RP2040, this pin is just meant for factories to make sure that the RP2040 SoC actually works before sending them out.

Next, we'll label all the other pins we haven't broken out (all the GPIO's, SWCLK and SWD), with their relative name on the RP2040. These are all bidirectional pins except the SWCLK pin, which is a clock output from the SoC:

![Pasted image 20250928011101.png](journal/Pasted%20image%2020250928011101.png)

Next, we're going to add the actual header pin symbols into our schematic. You can technically do this whoever you want, but I'm going to adhere to the raspberry Pi Pico pinout:

![Pasted image 20250928145822.png](journal/Pasted%20image%2020250928145822.png)

So add in a two, 1x20 header pin symbols, and one 1x3 header pin symbol, I just used generic symbols, but you could use pin header symbols if you want, it's just up to preference:

![Pasted image 20250928150705.png](journal/Pasted%20image%2020250928150705.png)

Usually you don't want to make your symbol layout look exactly like your PCB, but I think it makes it more obvious so that we don't mess up our pinout!

Next, we'll just add in all the pins, and we'll just leave out the ones we don't know yet like VSYS, 3V3_EN and ADC_VREF, I'll explain those after:

![Pasted image 20250928205318.png](journal/Pasted%20image%2020250928205318.png)

Now the Pi Pico can actually be powered by a battery, but we're not implementing a battery (if you want to, check out the Pi Pico datasheet), so there's a diode on the VBUS power line, so they have a VSYS line after the diode and a VBUS line before it, but because we don't need a diode, we don't need VSYS.

This also means we don't need 3V3_EN, and then ADC_VREF is kind of just another thing to give a reference voltage to ADC, but it isn't really necessary, and we're just making a simple devboard so we won't use it.

Because we have these free pins, and also some GPIO's still left, let's just fill these pins with some GPIO's. I'm going to move the ADC pins up, and then fill the other pins with GPIO's. I also want to use GPIO29 which is an ADC pin and replace GPIO25 with that just so we get the added ADC pin:

![Pasted image 20250928210525.png](journal/Pasted%20image%2020250928210525.png)

Because of this, you'll want to just no-connect GPIO25 on the MCU, just to tell KiCad and others that we're not using that pin:

![Pasted image 20250928210731.png](journal/Pasted%20image%2020250928210731.png)

If you want to add battery support, you can do so yourself, but I'm keeping to a minimum framework. And just like that, we have all of our header pins in!
## Finishing up the schematic

Now that we have our I/O headers in, we're actually finished with all the symbols in our schematic, this is how your schematic should look:

![Pasted image 20250928210917.png](journal/Pasted%20image%2020250928210917.png)

Now to organize our schematic, even more, let's separate our design into different blocks using the text boxes in the schematic editor. When doing this, you usually want to place your component blocks by flow of your PCB. So if you could image, power flows in through the USB, so we'll put that in the corner, the MCU should be center because it's the fundamental of the PCB, and then the other stuff can just be organized around:

![Pasted image 20250928211031.png](journal/Pasted%20image%2020250928211031.png)

You don't have to do this, but I feel like it keeps everything nice and clean!

Next, run ERC to just make sure you don't have any unconnected or weird stuff happening in your schematic. The only error you might get is **Input Power pin not driven by any Output Power pins**. You can just ignore this error, it's basically just the fact that we're labelling our power as bidirectional, and with no input/output, but we know that the MCU takes in 3.3V and that the USB-C outputs 3.3V, so we're totally fine to ignore it.

![Pasted image 20250928211106.png](journal/Pasted%20image%2020250928211106.png)

## Footprint time!

Now that we've finished out schematic, we need to start working on the actual PCB. The first thing you need to do for the actual PCB, is to add in all the footprints for your components.

A footprint on a PCB basically just defines it's pads, outline, etc, that your component needs in order to be solder able on a PCB. So just tap on the **assign footprints** tab in the top toolbar to open up the footprints tab:

![Pasted image 20250928211235.png](journal/Pasted%20image%2020250928211235.png)

Now before we add in our footprints, let's talk about standard imperial sizes of SMD components, and SMD vs THT components.

So if you don't know, there's SMD components, which are surface mount, which means that the components are attached to the surface of the PCB like caps, and then there's THT components, which are soldered *through* the board, these are things like the headers.

For SMD footprints, you'll want to understand what the imperial sizes are:
- 0402 are the smallest footprint we'll have on our PCB, these are tiny footprints and anything smaller than this becomes too small to easily solder, these are good for low current applications, and are fine for our fine signal decoupling.
- 0603 footprints are a bit larger than 0402, and are better for slightly higher current and will maintain better physical stability for the larger decoupling needed for 10uF caps and such.
- 0805 footprints are pretty large and are really just needed in higher current applications, we won't be using any of these because we don't have any crazy large caps/components

So all of our 0.1uF/1uF/resistors will be 0402, and then the 10uF caps will be 0603, so just filter in the search bar for 0402/0603, and choose the resistor/capacitor footprint for the relative component:

![Pasted image 20250928211327.png](journal/Pasted%20image%2020250928211327.png)

Now these other components need to usually be found on LCSC and then you go into the datasheet to find the footprint, and then add it in, but I'm decently experienced and know what footprints to use already, so you can just copy what ones I'm using or [find your own](https://jlcpcb.com/parts) if you want and add them in:

![Pasted image 20250928211526.png](journal/Pasted%20image%2020250928211526.png)

These are my thought process behind the other components, JLCPCB has what's called basic and extended parts, and extended parts cost $3 each to add to a PCB because they have to be loaded into the assembly machines, this will be important here:
- **USB_C_Receptacle_HRO_TYPE-C-31-M-12**: JLCPCB doesn't have any basic part USB-C receptacles, so I just chose this one I kind of like from a previous board. [PART](https://jlcpcb.com/partdetail/Korean_HropartsElec-TYPE_C_31_M12/C165948)
- **PinHeader_1x20_P2.54mm_Vertical**: This is just the proper size header pins we need, they should be through hole/THT to be stronger instead of SMD, I mean if you wanted to, it could be SMD though. The part is just pin headers I'll buy separately
- **SW_Push_SPST_NO_Alps_SKRK**: This is a small SMD size button footprint found in the JLCPCB *basic* library, so it doesn't cost anything extra and is pretty compact. This isn't actually the EXACT footprint, but it's close by like .1mm, and I found it by just scrolling through footprints with some filters. [PART](https://jlcpcb.com/partdetail/XUNPU-TS_1088AR02016/C720477)
- **Crystal_SMD_3225-4Pin_3.2x2.5mm**: I found this crystal on JLCPCB basic parts, and looked at the datasheet to find the footprint. You really have to make sure your crystal footprint pinout is proper because lots of people accidentally use the wrong footprint or symbol. [PART](https://jlcpcb.com/partdetail/YXC_CrystalOscillators-X322512MSB4SI/C9002)

Now we actually need to modify our crystal schematic a bit because of the part we chose on JLCPCB has a load capacitance is slightly different, so we actually need 33pF caps. You can just search up the math if you want to learn how to do this:

![Pasted image 20250928023842.png](journal/Pasted%20image%2020250928023842.png)

And just like that, our schematic and footprint selection is finished, so we can actually get to the real fun stuff.. the PCB!

## Let's design a PCB

Now that all that stuffs done, tap the switch to PCB editor button on the far right of the top toolbar!

This will bring you into a new editor you haven't seen yet, this is where we'll actually place down the components on our PCB, and route everything.

So in the top toolbar, tap the **update PCB from schematic or F8**, and then tap the **update PCB** button that shows up, to bring in all the components into your PCB, and just put them all in the top left corner of your PCB:

![Pasted image 20250928211730.png](journal/Pasted%20image%2020250928211730.png)

You might get some warnings which can be ignored usually (I just got some pin warnings which are fine), but there shouldn't be any errors.

Now you'll see our actual components on the PCB, our USB-C, the RP2040, the button, crystal, LDO, flash, headers and our caps/resistors!

## PCB Layout

Now before we actually lay out all of our components, we need to define our PCB outline, holes, etc. So using the datasheet as a reference, we'll place down everything accordingly. Start with the board outline, and then do holes and stuff.

![Pasted image 20250928214035.png](journal/Pasted%20image%2020250928214035.png)

To add in a board outline, **tap on the Edge.Cuts layer** and then tap on **Draw Rectangles**, and then just put whatever size rectangle you want. After that, we'll add in the proper size from the datasheet, which is 21x51mm, so tap on the rectangle, then tap **"e"** and use the **By Center and Size** tab to do this:

![Pasted image 20250928214259.png](journal/Pasted%20image%2020250928214259.png)

Next, we'll align the header pins onto our PCB by using the position tool. So right click on one of the header pins, go **Positioning Tools -> Position Relative To**, and then go **Select Point** and tap one of the top corners of the board outline. And then using the datasheet, align the X to **1.61/-1.61** based off of the side, and the Y to **1.37**:

![Pasted image 20250928225148.png](journal/Pasted%20image%2020250928225148.png)

(I actually misaligned my header pins in this screenshot which I fix later, but just put J2 as the first header, and J3 as the second one, so it's easier to route)

Next, we need to put our bottom header in, these are aligned to Y **-1.61** and the X should be centered so **7.96** (10.5 is the center, minus 2.54 the pin spacing), and use the bottom left/right as reference (make sure it's flipped horizontally when aligning):

![Pasted image 20250928225551.png](journal/Pasted%20image%2020250928225551.png)

Next, I'm going to put in the RP2040 dead center, but with the Y slightly farther down, because there's more components above the Pico than below, so I want a bit more space for signals, I'm going to put it down an extra 4mm, but you can do how much you want. 

![Pasted image 20250928230231.png](journal/Pasted%20image%2020250928230231.png)

Then, I'm going to center the USB-C, down a bit to the top of the devboard:

![Pasted image 20250928230453.png](journal/Pasted%20image%2020250928230453.png)

Now looking at the flash memory and LDO IC, they're really big, so let's use different components for them:

![Pasted image 20250928231949.png](journal/Pasted%20image%2020250928231949.png)

I'm going to switch to the MCP1700 LDO, which is smaller, but does handle less current (250ma), so if you plan on drawing more current, you might want to use a different LDO. So just replace the NCP1700 with the **MCP1700x-330xxTT**:

![Pasted image 20250929111310.png](journal/Pasted%20image%2020250929111310.png)

And then, we're going to change the flash memory to what the Pi Pico uses and has a slightly smaller package, which is the **W25Q16JVZPIQ TR** and uses the **Package_SON:Winbond_USON-8-1EP_3x2mm_P0.5mm_EP0.2x1.6mm** footprint, which isn't the exact footprint, but should work fine:

![Pasted image 20250929112550.png](journal/Pasted%20image%2020250929112550.png)

Now your footprints should be much better:

![Pasted image 20250929112732.png](journal/Pasted%20image%2020250929112732.png)

Anyways next, we're going to organize our parts onto the PCB (I also fixed my header pins and MCU orientation in this step). The LDO is going to go really close to the USB-C VBUS, and the flash storage will go close to the RP2040's QSPI pins:

![Pasted image 20250929113626.png](journal/Pasted%20image%2020250929113626.png)

I use exact positioning when doing things like this, but you can just draw them on if you want, I just like everything to be nicely symmetrical.

Next, I'm going to put the crystal on. The crystal should be very close to the RP2040 XIN/XOUT pins because it's a very precise signal, and the load capacitors should be RIGHT next to the pins too. You can then just put the resistor right by the XOUT pin of the RP2040:

![Pasted image 20250929114427.png](journal/Pasted%20image%2020250929114427.png)

Now I'm going to put all the decoupling capacitors on my board. **Decoupling capacitors should be as close as possible to the pins they're decoupling**, the larger the cap is, the farther it can be, but try to keep them close to their pins.

Also feel free to mess with layout a bit during this step just so everything fits in efficiently! Try to use whatever capacitor you used in your schematic for organization purposes.

First I usually group all the caps that go together, and then I usually either start with the SoC caps, or components caps, I'm going to start with the components caps:

![[Pasted image 20250929120135.png]]

**Remember, caps go by whatever they're decoupling**. Now all the RP2040 caps are grouped together, and this is because it's just a general rule to have one cap per VDD pin, and then the larger cap/bulk cap near the group of them:

![[Pasted image 20250929121131.png]]

This is the layout I decided on, some of my thought process for this layout was:
- Leave enough space to route the USB differential pair
- Be able to route QSPI without via's for fast signals
- Leave enough space by the crystal to be able to route those traces

And I'll still definitely actively update it while I route my traces, but this is a good starting point.







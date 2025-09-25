Today, we're going to be designing our own dev board, using one of the most popular and beginner friendly SoC's, the RP2040. This guide doesn't serve as just a tutorial, but also as an opportunity to learn what everything on the PCB fundamentally does, and what every single component on your PCB is actually for!

Now let's start off with the basic question, what's an SoC! An SoC or system on chip, basically has all the basic components like SRAM, processors, USB controllers, and other peripherals you'll break out onto your board. The RP2040 is a good starting microcontroller, because the datasheets are simple, it's low-cost, has good on-chip memory and is really flexible with plenty of IO's.

Now let's get right into it, we'll be using KiCad for this tutorial, and I would suggest completing the hackpad tutorial and maybe a keyboard before trying to make your own devboard, not because you won't be able to make it, but you'll understand how it works a bit better.

So create a new KiCad project by going:
`File -> new project, and choosing your name/folder for the project`

After that, double click your schematic to start working on your PCB. PCB's essentially have 2 main parts, the schematic, and the actual PCB. 

The schematic is basically a wiring diagram, that shows how everything will connect, but isn't like exactly where the components are placed or how thick your traces are, it's solely to show how everything is wired, not where.

ADD IMAGE HERE

The PCB editor is where you'll place down all your components

ADD IMAGE HERE OF FINAL THING
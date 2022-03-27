# README

The idea for this project came up after coming across a writeup by ollie242 on Instructables detailing the construction of a custom Ultrabay module for a Lenovo Thinkpad T420 by integrating a Raspberry Pi Zero 2 W.

https://www.instructables.com/Re-purpose-Optical-Drive-With-RPi/

With optical drives falling out of the mainstream, the Ultrabay on many older and still popular Thinkpads are being upgraded to hold secondary hard drives. However, at the same time web storage platforms like Dropbox, Google Drive and Onedrive are making large secondary hard drives less of a necessity. This begs the question, what can be done with the extra space in these older Thinkpads where the Ultrabay usually lives?

Building a Raspberry Pi into an Ultrabay module means being able to use all of the functionality of such a little computer on the go. The concept of using the Ultrabay for something more than just an optical drive or hard drive is pretty enticing. 

In ollie242's implementation there are a number of shortcomings that could be addressed relatively easily to create an even more powerful and easy to use RPi on the go. 

## Shortcomings

1. Interfacing with the Pi

   In ollie242's design, connecting to the Pi is done via VNC over the network, which necessitates connecting both the host laptop and the Pi to the same wifi network. The setup for this is kind of clunky, but it allows for full remote desktop from the host machine and therefore running GUI programs on the Pi.

   This means that the Pi must always be connected to a network to communicate with the host machine, no matter what. Preferably, it would be better to have a direct, reliable connection between the Pi and the host laptop without requiring a network connection.

2. Power draw

   By powering the Pi directly from the Slimline SATA on the Ultrabay, the Pi always receives power as long as the host machine is powered on. Having the ability to turn the Pi on and off without having to turn off the host machine would be ideal. This way the Pi could be disabled to save power and then enabled when in use. With ollie242's design, turning the Pi off over VNC is possible, but turning it back on afterwards is not so easy as far as I can tell. 

## Plan

I'm developing this project on my Thinkpad T400, which has the Serial Ultrabay Slim. I'm going to start by developing a proof of concept, integrating all of the design goals first, then moving onto making a sell contained Ultrabay module that would be compatible with any Serial Ultrabay Slim or Serial Ultrabay Slim Enhanced. Other form factors of  the Ultrabay could probably be supported in the future based on this design with just the physical frame of the module changing between form factors. 

To fix the communication problem with the Pi, I'd like to communicate with the module over serial. A serial connection to the Pi would allow for direct terminal control, and then VNC could be started from there once the Pi is connected to a network, just like in ollie242's build. 

Fixing the power switching is another question. There's a couple options for this;

- Include a physical switch on the faceplate of the Ultrabay to connect and disconnect power from the Pi (just like the network switch on older Thinkpads)
- Create a software solution using the existing "Eject" functionality to switch off an internal relay thereby disconnecting power from the Pi.

For testing and development, I'll probably end up designing both of these methods in, as it's better to leave the functionality there then not to have it. 

## Next Steps  

Looking at the documentation on the Ultrabay, it appears that the interface in my T400 is simply a Slimline SATA connector. As this implementation of the Ultrabay also supports a battery module, there's also a secondary connector for handling charging and discharging. 

We can get +5v power and GND from the Slimline port to power the Pi, but neither the battery interface nor the Slimline port exposes a serial connection to the Ultrabay. Maybe one could be hacked in using the battery interface, but I don't know enough about this interface to know if it could be addressed as a COM port in the operating system. There isn't a way to make the SATA port function as a COM port either, meaning we have to look elsewhere for a Tx and Rx line. 

To get the device working easily then, we'll have to use an existing USB port to provide our serial interface. An external USB Micro-B connector with the Tx and Rx pins wired into GPIO pins 8 and 10 respectively. This isn't an optimal solution, but allows us to use the existing USB ports on the machine to interface with the Pi. 
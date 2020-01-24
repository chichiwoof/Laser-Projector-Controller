# Laser-Projector-Controller
Onboard control for a laser projector

DISCLAIMER: This is not for controlling the laser modulation or galvos!


This is code for an Arduino microcontroller designed to read the current state of the SHUTTER and INTERLOCK from a standard ILDA connector.

The code does the following.

IF the SHUTTER signal is OPEN (+5v) AND the INTERLOCK is a COMPLETE CIRCUIT, only then will a relay close providing power to the laser(s). 

If at any point either of these states changes (shutter closes or interlock is broken) the relay immediately opens (its default un-powered state) and the lasers power is interrupted.

If "shutter" is closed there is a delay before you can turn the power to the lasers back on and the interlock and shutter have to be in the proper states or they will remain off.

If the interlock fails, the :shutter: is closed and remains closed until the interlock issue has been reolved and a safety delay has expired.

Information is printed to serial regarding the state of the system.

*For safety the GALVO power should NEVER be interrupted*

The Arduino pins are as follows

- ShutterSignal = The signal coming from the ILDA shutter signal. +5v = Shutter OPEN (this is an input pin for reading the shutter voltage)
- ShutterPin = Pin used to control the relay state (Output pin for sending signal to the relay
- InterlockOut = set to +v5 to send a constant signal over the interlock loop (+5v output pin)
- InterlockIn = Reads the signal from the interlock loop sent from the InterlockOut pin(input pin)


# How to use this sketch 
**flash laser_control.ino to the arduino board**
Information on getting started with Arduino can be found here (it's easy!)
https://www.arduino.cc/en/Guide/HomePage


**The shuttert**
A circuit should be configured so that the ILDA Shutter pin from the connector (pin 13 from the ILDA DB25) is connected to the shutterSignal pin of the arduino. The shutterPin pin should go to your relay signal pin. the ILDA ground pin (ILDA DB25 pin 25)should be connected to a common ground. 

The relay and the Arduino both need 5v and can share the same power supply. Any loss of power to the arduino or relay should cause the shutter to close. The Arduino board should share a ground with the ILDA DB25 connector

**The interlock circuit**

The interlockOut pin should connect to pin 4 of ILDA DB25 and the interlockIn pin should connect to pin 17 of the ILDA DB25. This is how the Arduino detects if there is a closed circuit. A 10k resistor should be connected from the interlockIn pin to ground to aleviate any float voltage when the circuit is broken. 

You can use the interlock in a number of ways. 
- enclosure sensors
- safety shutoff button
- anything requiring a circuit to be complete

**Safety delay times**

The delay times for each delay type are configurable at the top of the sketch. note that SHUTTER_DELAY should be the shortest, followed by INTERLOCK_DELAY and BOOT_DELAY should be the longest (all in milliseconds). 

If your show controler closes the shutter during the show (for blank periods) you will need to set the SHUTTER_DELAY based on your controllers specs. If the SHUTTER_DELAY is low (under 2 seconds) you should NOT use the shutter to control DC power to the lasers as switching the DC power off and on to quickly could damage the lease driver you. Instead use an alternate method for controling the shutter (manual shutter, ground modulation signal). 

MESSAGE_DELAY can be whatever you want as it only controls how often messages repeat in the serial readout. 

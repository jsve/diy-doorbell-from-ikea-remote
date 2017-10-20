# Why
The plan for the future is to include some kind of camera / remote lock solution for my door. Probably through HomeKit.

For now, and the scope for this repo, I'm limiting this to the hardware modifications needed to get a signal when the doorbell button is pressed.

# How
I had some preferences going into this:
 - Hardware has to be easy to get a hold of (short or no delivery times)
 - I don't want to spend alot of money on this
 - Everything should be hidden away once everything is mounted
 - I would like to spend as little time as possible reverse engineering or learning about specific boards or do alot of coding myself (like using an arduino or other "blank" boards)
 - No visual modifications should be made from the outside
 - Except for battery-changes, this should be maintanence free for a long time (5-10 years)
 - Pairing and battery changes shall be as easy as possible and not require moving any other units (I need to be able to take out and move the doorbell unit to the gateway, and not the other way around)

 # What

 ## Choosing hardware
 I decided to use the [IKEA TRÅDFRI remote](http://www.ikea.com/se/sv/catalog/products/30338849/). This checks off most of my preferenses:
  - I can buy it in store.
  - Its kind of cheap 
  - A rough google search reveals that this has been modded before, so the guesswork for making this work is minimal.
  - I have a few IKEA units allready and I kind of like them. A big plus is that this means that connecting it to HomeKit will be alot easier than choosing something not ZigBee.

_I'm just going to use the "on/off" button from this remote, since the other ones have features to support "long press" and other stuff that don't translate well to a doorbell._

Using a xiaomi button or similar might be cheaper, but delivery times to Sweden is several weeks. I haven't read about anyone modding the latest version either, which would require me to put in more work.

## Opening up the TRÅDFRI remote
There are two ways to get into the remote. You can either break your way in, from the buttons-side, like [this guy](https://www.automatiserar.se/ikeas-tradlos-med-arduino/) did. The other way in is through the side, using a saw or a dremel, like [this guy](https://www.youtube.com/watch?v=olxPqiJcUAQ).

I chose the second way - the dremel. On the side of the remote there is a small ridge in the grey plastic. This is pretty much where you want to make the cut. Maybe a little bit towards the "battery side", which will reduce the risk of damaging any components.

> TODO: I'll add a picture of the destruction here later.

I think I prefer opening the remote like I did. This means that the remote still has all the buttons still working. This is really usefull for debugging and checking that everything works. For other projects this might be usefull if you want to be able to use the buttons as a double command setup, like [this](https://www.youtube.com/watch?v=olxPqiJcUAQ).

### Don't lose the pair button!
You definitely don't want to lose the pair button. Not now, and not in 5 years. I solved this with some trusty duct tape:

<img src="img/dont_lose_pair.jpg?raw=true" height="400" alt="Duct taped button to the remote" title="Duct taped button to the remote">

## Basic design
Opening up the remote is kind of the hardest part here (if you know how to solder). The rest is basicly just connecting two wires to the back of the remote and connecting theese to the wall-switch on the outside of the door.

<img src="img/soldered_remote.jpg?raw=true" height="400" alt="Basic design" title="Basic design">

What you want is one wire connected to the fourth pin from the left on the main IC (careful not putting to much heat) and one on the small plate next to the battery. If you connect your new wires (like through a switch) that mimics someone puching the "on/off" button. Basicly:

<img src="img/basic_design.png?raw=true" height="400" alt="Basic design" title="Basic design">

## Done
Connecting the wires, like shown in the basic design above, to the doorbell switch gives me the funcitonality I wanted. However, this means that the main bell is no longer working. If you are fine with that, you are done.

For me, I dont have 100% confidence in the chain:
 - ZigBee -> homebridge -> HomeKit -> Apple TV (HomeHub) -> iPhone

Thats why I went on to the "advanced" design, as shown below.

# ZigBee doorbell 2.0 a.k.a "The advanced version"
This chapter is based on the doorbell that was already mounted at my place. This bell is based on a pretty basic design:
A battery is used to activate a coil / electro-magnet that pulls an "arm" that hits the bell (basicly a relay). When the "arm" is pulled it breaks the cicuit, causing the "arm" to fall back. This closes the circuit again and it all starts over - causing the ringing sound.

<img src="img/original_doorbell_circuit.png?raw=true" height="400" alt="Original doorbell" title="Original doorbell">

## How to connect the TRÅDFRI?
The TRÅDFRI circuit from the last chapter can't just connect to the doorbell switch when the original bell is using this. The original doorbell uses 4,5 V which would seriously mess up some of the IKEA electronics.

At first I figured I could just connect a relay after the original switch and connect the remote to that. However, since the original circuit is constantly switching between closed and open, with the ringing, I would get something like 100 notifications a second when the doorbell switch is pressed.

## Connecting the TRÅDFRI
What you want to do is seperate the doorbell switch from the bell. The doorbell switch can then be used to seperately activate the bell and the TRÅDFRI remote. I decided on doing this using a relay (or two) to keep the circuit simple and make it easy to understand even when I see it next, in a couple of years.

In my case I also wanted to make some modifications to the bell circuit itself. The original design allowed the bell to ring as long as the button was pressed - super annoying! For this reason I added a few components to break the bell-circuit after a couple of milliseconds. 

Ideal would be to add a larger delay, so that even if the bell is pressed repeatidly it will only fire once a minute or something. This is a bit above my knowledge about circuits like this, but I'm guessing it would invole adding a few more, larger and more complicated components (placing it outside the scope of this project).

## The end design
After alot of experimenting with different relays, capacitors and resistors i landed in something like this:

<img src="img/my_design.png?raw=true" height="600" alt="My design" title="My design">

## NB!
Note that if you have some other doorbell-circuit you want to include, and don't want the delay-stuff, you can skip the capacitor and the resistor. You also want to connect the bell-circuit to the normally open side in the relay.

> TODO: add a picture of the finished product.



# For other / future projects
For anyone wanting to map all of the buttons from the remote, there are some information in the links above, but also [here](http://www.jakobbieling.de/projects/tradfri/). 
---
title: "An Actual Smart Thermometer"
date: 2018-04-25T16:36:32.00Z
draft: false
---

We've recently moved to a house rather than a flat[0]. I've noticed that adjusting radiators to get equal temperatures in various rooms is an art not a science. I know the Nest exists but it's not smart enough. Here's what I want:
 
Firstly just ditch the cloud. The small use case of turning your boiler on/off remotely are vastly outweighed by the internet of shit.
 
Our thermostat is positioned in the hall which has no radiator so there's quite a delayed response. The front door is very close to the thermostat which is going to effect the reading. I'd like thermometers in every room so counteract this. Little device just pinging temperatures would be pretty low power so could conceivably be battery powered.
 
A nice local front end. This means the solution will have to be connected to the network but there'd be no need to give it WAN access. On this front end would be a lot of time series data on room temperature. Using this we could manually adjust radiators. We could monitor battery levels of devices through this too.
 
Automagical radiator valves. Use that lovely data to dynamically open and close the valve to achieve the desired temperatures. Maybe some machine learning here, identify rooms that get cold abnormally quickly so you can take action and add extra insulation? If you're lucky enough to have some cooling system, we could connect that too.
 
Connect everything to eachother with a mesh network so it doesn't rely on WiFi.
 
I think I could build some of this right now. Wireless thermometers exist and so do computers. I'd like it in a nice package and with basically no work. One problem I think will be that most companies will connect it to the cloud to get my data and lock me in forever, boo!
 
[0] - living in a big apartment complex seems to provide a degree of consistency in temperatures. Which is nice unless it gets too warm in summer, classic British homes not having air con.


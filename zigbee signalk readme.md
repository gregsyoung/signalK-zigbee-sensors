# Zigbee sensors with Signalk 

Zigbee is a low power wireless protocol for enabling sensors and other devices for home automation & telemetry/control.  Zigbee door/window sensor are inexpensive and readily available from a variety of sources, driven by the home automation market. Door/window sensors are battery powered and can be stuck onto the frames of hatches, doors, portholes etc to provide an indication to signalk of the state (eg open/closed)

![image](https://github.com/gregsyoung/signalK-zigbee-sensors/blob/main/porthole_sensor.jpg)
![image](https://github.com/gregsyoung/WilhelmSK/blob/3ff1d6e42ee1e675eebfd41a922053dbab9622ad/aqara%20door%20window%20sensor.jpg)


## Zigbee2MQTT software

This software provides a "bridge" between zigbee devices (connected via a "zigbee coordinator" - see below) & an MQTT server running on RPi.
Note: use signalK-MQTT plugin to install/run the local MQTT server.

<https://www.zigbee2mqtt.io/guide/getting-started/>

## Zigbee Coordinator
ZigBee Coordinators (ZCs) are the only ZigBee device-type that can form (as opposed to join) a network. Or in other words is like a "gateway" for Zigbee devices to connect to RPi /signalK.
There are various Zigbee adaptors available - however for this purpose a simple USB device that just passes the information to the RPi will suffice.
I have used a "sonoff zigbee 3.0 USB ZBdongle-P".
The dongle needs to be flashed with "Z-stack coordinator firmware (see above guide) and further details below.

![image](https://github.com/gregsyoung/WilhelmSK/blob/main/zigbee%20coordinator.jpg)

https://github.com/Koenkk/Z-Stack-firmware/blob/master/coordinator/README.md

## Zigbee Setup
After following above Zigbee2MQTT guide, for installation & configuration, this will then create a "frontend on port 8080" - that provides a web page for adding & configuraing zigbee devices.
(Use your same RPi IPaddress with :8080 appended, note this can be changed as required)

![image](https://github.com/gregsyoung/signalK-zigbee-sensors/blob/main/zigbee2mqtt%20devices.JPG)

On the top row - click on "permit  join all" , this puts the zigbee stick into "pairing" mode. Then likewise on your sensors, click on the pairing button (or initiate pairing - it depends on zigbee device) For the Aqara zigbee window/door sensors, you click the small button on its side. You should see the sensor appear in the list. Give it a "friendy name".

![image](https://github.com/gregsyoung/signalK-zigbee-sensors/blob/main/zigbee2mqtt%20settings%20devices.JPG)

Configure the MQTT "base topic" & the MQTT server details. The sensor will now be sending data to the MQTT server, however it cannot be read directly by SignalK. This is done via a simple NodeRed flow.

## NodeRed flow
The MQTT message has a number of data components embedded and needs to be parsed into signalK path/s.
This is done using an "MQTT IN" node and some other nodes to extract the "state" of the sensor (open/closed) and populate a path for signalK.

![image](https://github.com/gregsyoung/signalK-zigbee-sensors/blob/main/nodered%20parse%20mqtt.JPG)

![image](https://github.com/gregsyoung/signalK-zigbee-sensors/blob/main/nodered_zigbee2mqtt_flow.json)

![image](https://github.com/gregsyoung/signalK-zigbee-sensors/blob/main/zigbee_path_signalk.JPG)

repeat as many times as required, once for each zigbee sensor.

## Display of Hatch/Porthole Status
In the example below, Im using WilhelmSK (IOS devices) with a background image (line drwaings of boat) and "LED guages" placed over the top of the image at locations of hatches etc. The guage is defined to be transparent when off & "red" when "on" (path = 1)
Note that the zigbee sensor path is ONLY refreshed when the contact sensor closes/opens (& at a less frequent rate for battery? or other updates), accordingly its best to ignore the "freshness" of the data, as it can be many hours old - if you havent opened/closed the hatch/window.

![image](https://github.com/gregsyoung/signalK-zigbee-sensors/blob/main/boat_status.jpg)
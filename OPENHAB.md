# Integrate wmbusmeters with openHAB 
<https://www.openhab.org/>
## Tested with openHAB 5 on a Raspberry Pi4 in July 2025

### Major Steps:
#### 1. Install MQTT Broker
   
Run `openhabian-config` and use menu `20,23` to install a Mosquitto Broker.

#### 2. Configure wmbusmeters

Configure device (example for a nanoCUL device) and add MQTT send command to config file /etc/wmbusmeters.conf
```
device=/dev/ttyUSB0:cul:t1
shell=/usr/bin/mosquitto_pub -h localhost -t /wmbusmeters/$METER_ID -m "$METER_JSON"
```
Add a file for your device, you may be able to find device information with the command `wmbusmeters auto:t1`

Example for a Diehl Hydrus water meter with AES encryption (use your own id and key!), file name `/etc/wmbusmeters.d/water´
```
name=water
id=12345678
key=00112233445566778899AABBCCDDEEFF
driver=hydrus
```

#### 3. configure openHAB
Install `MQTT Binding` and `JSONPATH transformation`.

Create a thing `MQTT Broker` usually for `localhost`.

Create a thing `Generic MQTT Thing` for the installed bridge `MQTT Broker`

Configure one or more channels for your `Generic MQTT Thing`.

Example channel settings for a Diehl Hydrus water meter:
```
"Channel Type": Number Value 
"MQTT State Topic": /wmbusmeters/12345678
"Show Advanced": on
"Unit Of Measurement": m3
"Incoming Value Transformations": JSONPATH:$.total_m3
```
Configure your items derived from the channels as desired.

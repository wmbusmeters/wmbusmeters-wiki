# Integrate wmbusmeters with DOMOTICZ
<http://www.domoticz.com/>

Create dummy hardware and device
1. Go to Setup -> Hardware
  - Name: anything, e.g. "wmbusmeter_sensors" (this name is only used internally)
  - Type: "Dummy (does nothing, use for virtual switches only)"
  - Click on 'Add'
2. Create Virtual Sensors
  - Click on 'Create Virtual Sensors' from the table of hardware above
    - Name: anything, e.g. "Water Consumption" (this name is displayed on the dashboard etc.)
    - Sensor type: "Custom Sensor"
    - Axis Label: "m3"
  - Click 'Ok'
3. Get idx of relevant virtual sensors
  - Go to Setup -> Devices
  - Note the Idx of your virtual sensor

Setup MQTT
1. Go to Setup -> Hardware
  - Name: anything, e.g "MQTT"
  - Type: MQTT Client Gateway with LAN interface
  - Remote Address: IP/name of MQTT server, e.g. "localhost"
  - Port: 1883
  - Everything else should be ok, so click on the Add button at the bottom.

Option 1 - preffered

In your meter configuration located in /etc/wmbusmeters.d/ add MQTT configuration, where XYZ is Idx of your virtual sensor
```
shell=/usr/bin/mosquitto_pub -h localhost -t domoticz/in -m "{\"idx\":XYZ,\"svalue\":\"$METER_TOTAL_M3\"}"
```
When data is sent by wmbusmeters check in domoticz WUI under Utility tab if created sensor is updated with new value

Option 2 - more hackinsh

```diff
- Steps below involves modification of sqlite DB - remeber to make backups before modifications!
```
Change sensor ID in domoticz 
1. Stop domoticz and make backups!!!!
2. Find location of your domoticz installation and open db - `sqlite3 domoticz.db`
3. Select from db your sensor just to verify that it is correct one (ID is idx from gui) - 
```
SELECT * FROM DeviceStatus WHERE ID = 2;
```
and check also sensor ID from wmbusmeters config to verify that ID is not taken (should not return anything) -
```
SELECT * FROM DeviceStatus WHERE ID = 01234567;
```
4. Update domoticz virtual sensor ID to ID of meter that you added to wmbusmeters config - 
```
UPDATE DeviceStatus SET ID = 01234567 WHERE ID = 2;
```
5. Select once more meter to verify changes - 
```
SELECT * FROM DeviceStatus WHERE ID = 01234567;
```
6. Exit db - 
```
.exit
```
7. Start domoticz

Repeate steps above for each sensor

In /etc/wmbusmeters.conf add MQTT configuration
```
shell=/usr/bin/mosquitto_pub -h localhost -t domoticz/in -m "{\"idx\":$METER_ID,\"svalue\":\"$METER_TOTAL_M3\"}"
```
When data is sent by wmbusmeters check in domoticz WUI under Utility tab if created sensor is updated with new value

# Integrate wmbusmeters with OpenHAB 
<https://www.openhab.org/>

Add MQTT send command to config file /etc/wmbusmeters.conf

```
shell=/usr/bin/mosquitto_pub -h localhost -t /wmbusmeters/$METER_ID -m "$METER_JSON"
```

1. Install MQTT binding
2. Config mqtt.things, and change 12345678 to the proper meter id.
```
Bridge mqtt:broker:newBroker [ host="localhost", secure=false ] {
    Thing mqtt:topic:meters "Meters" {
        Channels: 
        Type string : meter_1 "Main meter" [stateTopic="/wmbusmeters/12345678", transformationPattern="JSONPATH:$.total_m3"]
    }
}
```

3. Config mqtt.items

```
String meter_1 "Main meter" {channel="mqtt:topic:meters:meter_1"}
```

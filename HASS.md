# Integrate wmbusmeters with HASS
<https://www.home-assistant.io/>

## Home Assistant Operating System

When using Home Assistant Operating System it gives the opportunity to use wmbusmeters as an add-on. Please refer to [this documentation](https://github.com/wmbusmeters/wmbusmeters-ha-addon/blob/main/wmbusmeters-ha-addon-edge/DOCS.md) for wmbusmeters add-on configuration and usage.

## Home Assistant Container

When using Home Assistant Container or other manual installation methods, please refer this guide.

Add MQTT send command to config file /etc/wmbusmeters.conf
```
shell=/usr/bin/mosquitto_pub -h localhost -t wmbusmeters/"$METER_ID" -m "$METER_JSON"
```

Add sensor configuration to Home Assistant configuration file (as example for water meter readings).
Change the topic to match the meter id.

```
mqtt:
  sensor:
    - name: "meter_name"
      state_topic: "wmbusmeters/12345678"
      unit_of_measurement: "m³"
      device_class: water
      state_class: total
      value_template: "{{ value_json.total_m3 }}"
```

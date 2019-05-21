How to integrate wmbusmeters with iobroker.

This is not confirmed, but it might work. 

   1 Install MQTT Broker/Client adapter
   
   2 Configure and start MQTT adapter
   
   3 Add to /etc/wmbusmeters.conf
   
   4 shell=/usr/bin/mosquitto_pub -h localhost -t wmbusmeters/$METER_ID -m "$METER_TOTAL_M3"
   
   5 When wmbusmeters sends data, check under Objects -> mqtt.0/wmbusmeters/[meter_id] and you should see value reported

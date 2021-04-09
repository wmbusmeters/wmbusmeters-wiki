Reading an izar/sappel/diehl water meter
========================================

The serial number printed on the meter is not the identity provided by
the wmbus standard Datalink Layer or Transport Layer. But by listening
to all izar meters in your vicinity like this:

```
wmbusmeters --fornat=json auto:t1 MyWater izar '*' NOKEY
```

(Or instead of NOKEY, use the key you have for your meter.)

You will get telegrams with this information:

```
{"media":"water","meter":"izar","name":"wasser/P15_Main","id":"12345678","prefix":"C19SB","serial_number":"053746","total_m3":251.887,"last_month_total_m3":249.493,"last_month_measure_date":"2021-02-28","remaining_battery_life_y":13.5,"current_alarms":"no_alarm","previous_alarms":"no_alarm","transmit_period_s":8,"manufacture_year":"2019","timestamp":"2021-03-10T16:15:13Z","device":"rtlwmbus[0000616]","rssi_dbm":11}
```

In this case the prefix and the serial number from within the json is printed on the meter.
Then you can extract the id (12345678) in the json above. This is the id that wmbusmeters
wants to identify your meter.

You can now run:

```
wmbusmeters --format=json auto:t1 MyWater izar 12345678 NOKEY
```

or use 12345678 in your config files.

![Water meter](/img/izarwatermeter.jpg?raw=true)
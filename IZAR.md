Reading an izar/sappel/diehl water meter
========================================

You cannot easily know the id needed for wmbusmeters
to pickup the telegrams from your izar meter, since
the numbers printed on the meter itself do not match the wmbus id.

However the numbers printed on the meter are available in the json.
If you try:

```
wmbusmeters --fornat=json auto:t1 MyWater izar '*' NOKEY
```

(or instead of NOKEY, use the key you have for your meter) then you will get telegrams with this information:

```
{"media":"water","meter":"izar","name":"wasser/P15_Main","id":"12345678","prefix":"C19SB","serial_number":"053746","total_m3":251.887,"last_month_total_m3":249.493,"last_month_measure_date":"2021-02-28","remaining_battery_life_y":13.5,"current_alarms":"no_alarm","previous_alarms":"no_alarm","transmit_period_s":8,"manufacture_year":"2019","timestamp":"2021-03-10T16:15:13Z","device":"rtlwmbus[0000616]","rssi_dbm":11}
```

In this case the prefix and the serial number from within the json is
printed on the meter, thus this is a telegram from your meter. Then
you can extract the id (12345678) from the json above and use this id
for wmbusmeters. You can now run:

```
wmbusmeters --format=json auto:t1 MyWater izar 12345678 NOKEY
```

or use 12345678 in your config files.

![Water meter](img/izarwatermeter.jpg?raw=true)
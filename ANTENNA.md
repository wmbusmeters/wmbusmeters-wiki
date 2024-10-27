Best setup for receiving wmbus over long distances
==================================================

After some personal, subjective and very rudimentary testing, the top three best receivers
of radio signals are:

The new iu891a dongle with the ANT-868-CW-HWR-SMA from TE Connetivity / Linux Technologies.
(this is the recommended antenna if you buy from the iu891a from https://shop.imst.de)

The Metis-II with the ANT-868-CW-HWR-SMA performs almost as well as the iu891a.

A metal shielded original rtlsdr.com dongle (can be purchased here: rtl-sdr.com) and
the ANT-868-CW-HWR-SMA antenna from TE Connectivity / Linx Technologies.
(Or a slightly less performant, but still good dipole antenna http://qlrs.pl/antena-lrs-868mhz-dipol-elastyczna-23mm)

However the rtlsdr uses a lot of CPU power and energy to decode the radio, on the other hand
you can decode s1,t1 and c1 at the same time, if you need this....

With these three setups I can place the antenna inside my house (a
wooden house, thin walls) and receive measurements from the multical21
c1 water meter 1.5 meter down in the ground, inside a concrete
cylinder a good 35 meters away from the house.

The im871a dongle (is no longer produced) was also good solution, it could receive 30m away
from the meter, but must be outside of the house. Pretty good
considering that the antenna inside the dongle is tiny compared to the
dipole antenna above. The amb8465 dongle (is no longer produced) could receive from 15m away
and the nano-cul does seem to have problems receiving C1 telegrams since
it can only receive telegrams when sitting on top of the concrete cylinder.
Note that you should not use the nano-cul for wmbus anyway, since it cannot receive long wmbus telegrams!

![Dipole antenna](img/dipoleantenna.jpg?raw=true)

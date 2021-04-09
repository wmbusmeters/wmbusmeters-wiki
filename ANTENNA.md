Best setup for receiving wmbus over long distances
==================================================

After some personal and very rudimentary testing, I have found
that the setup that can receive from the longest distance is by
using an rtlsdr.com dongle (can be purchased here: rtl-sdr.com) and a tuned
dipole antenna (can be purchased here:
http://qlrs.pl/antena-lrs-868mhz-dipol-elastyczna-23mm) and using the
latest version of rtl_wmbus (which can decode S1,T1 and C1 at the same time.)

![Dipole antenna](img/dipoleantenna.jpg?raw=true)

With this setup I can finally place the antenna inside my house (a
wooden house, thin walls) and receive measurements from the multical21
c1 water meter 1.5 meter down in the ground, inside a concrete
cylinder a good 35 meters away from the house.

The im871a dongle is the second best solution, it can receive 30m away
from the meter, but must be outside of the house. Pretty good
considering that the antenna inside the dongle is tiny compared to the
dipole antenna above. The amb8465 dongle can receive from 15m away
and the nano-cul does seem to have problems receiving C1 telegrams since
it can only receive telegrams when sitting on top of the concrete cylinder.

I am interested in the reception capabilities of a hardware chip like
im871a and a tuned dipole antenna (instead of the tiny antenna inside
the dongle) and I have such a setup available and will update this
page when I have made some more testing.
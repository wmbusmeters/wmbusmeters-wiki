# Use nanoCUL device for wMBus reception with wmbusmeters

nanoCUL is a USB wMBus interface based on Arduino Nano (or a clone) and CC1101 module. You can build one yourself with a little bit of tinkering skills or buy a finished unit from one of the vendors. You'll probably want a 868 MHz version for wMBus. It supports both C1 and T1 modes with the correct firmware. The device you have likely requires a firmware upgrade, unless your vendor flashed it with software that you requested.

This guide will help you to flash your nanoCUL device with a firmware that works nicely with wmbusmeters.

## Firmware

The Arduino Nano is an ATmega328P based device that requires a firmware. If you buy a commercially available nanoCUL device chances are that it has either a firmware that does not support wMBus, does not support C1 mode or has a too small TTY buffer for longer wMBus frames. So you might have to flash the firmware for your nanoCUL even if you bought a ready device.

There are two firmwares available for a nanoCUL: the original [culfw](http://culfw.de/culfw.html) and a modified version of it called [a-culfw](https://github.com/heliflieger/a-culfw). You can build the firmware yourself from the source code but that's outside the scope of this guide. The firmware provided on this page is created based on the last available release of culfw (r568) since that offers support for wMBus C1 mode too. The wMBus mode consumes quite a lot of memory and the ATmega328P does not have a lot so any extra radio modes supported by the culfw were disabled. Some wMBus telegrams are quite lengthy, so the standard receive buffer size (TTY_BUFSIZE parameter in board.h) was increased to 300 bytes. This enables you to receive 148 byte long wMBus telegrams. 

Firmware can be downloaded here:
[nanoCUL_r568_mbus_c1t1_bufsize300.zip](https://github.com/weetmuts/wmbusmeterswiki/files/7623213/nanoCUL_r568_mbus_c1t1_bufsize300.zip)

A board.h file is provided, in case you're interested in looking at the options used or want to modify the firmware. If you want to do that, download the culfw [firmware tarball](https://sourceforge.net/code-snapshots/svn/c/cu/culfw/code/culfw-code-r568-trunk.zip) and replace the board.h in culfw/Devices/nanoCUL folder and run make in that folder.

### Flashing

I have flashed my nanoCUL on a Linux system (be it a PC or a Raspberry Pi, the installation is similar) running Ubuntu-like operating system. First thing you need to do is to install the avrdude software that can be used for flashing. It's also available for Windows command line but I have not tried it.

```sudo apt install avrdude```

Now plug in your nanoCUL device to your USB port and make sure you see the USB serial adapter of the nanoCUL:

```
$ lsusb
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 006: ID 0403:6001 Future Technology Devices International, Ltd FT232 Serial (UART) IC
Bus 001 Device 002: ID 2109:3431 VIA Labs, Inc. Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

Mine has the FT232 serial adapter that's readily supported by Linux. Since I don't have any other USB serial adapters my device name is /dev/ttyUSB0:

```
$ ls -l /dev/ttyUSB*
crw-rw---- 1 root dialout 188, 0 Nov 29 20:53 /dev/ttyUSB0
```

Now it's time to flash the firmware. Place the hex file into your Linux machine and run avrdude using your device name and the filename of the firmware like this:

```
$ avrdude -D -p atmega328p -P /dev/ttyUSB0 -b 115200 -c arduino -U flash:w:nanoCUL_r568_mbus_c1t1_bufsize300.hex

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.01s

avrdude: Device signature = 0x1e950f (probably m328p)
avrdude: reading input file "nanoCUL.hex"
avrdude: input file nanoCUL.hex auto detected as Intel Hex
avrdude: writing flash (12320 bytes):

Writing | ################################################## | 100% 4.65s

avrdude: 12320 bytes of flash written
avrdude: verifying flash memory against nanoCUL.hex:
avrdude: load data flash data from input file nanoCUL.hex:
avrdude: input file nanoCUL.hex auto detected as Intel Hex
avrdude: input file nanoCUL.hex contains 12320 bytes
avrdude: reading on-chip flash data:

Reading | ################################################## | 100% 4.16s

avrdude: verifying ...
avrdude: 12320 bytes of flash verified

avrdude: safemode: Fuses OK (E:00, H:00, L:00)

avrdude done.  Thank you.
```

## Initial verification

Now that you have your stick flashed you can use it with wmbusmeters. Easiest is to run wmbusmeters from the command line and make it print all diagrams it receives. Here's an example command that makes nanoCUL listen to all C1 frames and print them (change c1 for t1 if your wMBus meter transmits in T1 mode):

```
wmbusmeters --debug --logtelegrams --verbose /dev/ttyUSB0:cul:c1
(wmbusmeters) version: 1.5.0-8-g67e0072
(config) using device: /dev/ttyUSB0:cul:c1
(config) number of meters: 0
(lookup) with file/hex "/dev/ttyUSB0"
(serial) check if /dev/ttyUSB0 can be accessed
(serial) tty /dev/ttyUSB0 can be accessed
(serial) EVENT thread interrupted
(serial) before config: /dev/ttyUSB0 speed(B38400) input() output() control(CLOCAL|CREAD|CSIZE) local() special_chars() modem(DTR|RTS)
(serial) after config:  /dev/ttyUSB0 speed(B9600) input() output() control(CLOCAL|CREAD|CSIZE) local() special_chars() modem(DTR|RTS)
(serialtty) opened /dev/ttyUSB0 fd 3 (detect amb8465)
(serial) received binary ""
(amb8465) sending 6 bytes attempt 0
(serial /dev/ttyUSB0) sent "FF0A02008077"
(amb8465) sent 6 bytes OK
(serial) EVENT thread interrupted
(amb8465) reading response... 1
(serial) received binary ""
(amb8465) reading response... 2
(serial) received binary ""
(amb8465) reading response... 3
(serial) received binary ""
(amb8465) are you there? no.
(serialtty) closed /dev/ttyUSB0 (detect amb8465)
(serial) EVENT thread interrupted
(serial) before config: /dev/ttyUSB0 speed(B9600) input() output() control(CLOCAL|CREAD|CSIZE) local() special_chars() modem(DTR|RTS)
(serial) after config:  /dev/ttyUSB0 speed(B57600) input() output() control(CLOCAL|CREAD|CSIZE) local() special_chars() modem(DTR|RTS)
(serialtty) opened /dev/ttyUSB0 fd 3 (detect im871a)
(serial) received binary ""
(serial /dev/ttyUSB0) sent "A5010F00"
(serial) EVENT thread interrupted
(serial) received binary ""
(im871a/im170a) are you there? no.
(serialtty) closed /dev/ttyUSB0 (detect im871a)
(serial) EVENT thread interrupted
(serial) before config: /dev/ttyUSB0 speed(B57600) input() output() control(CLOCAL|CREAD|CSIZE) local() special_chars() modem(DTR|RTS)
(serial) after config:  /dev/ttyUSB0 speed(B19200) input() output() control(CLOCAL|CREAD|CSIZE) local() special_chars() modem(DTR|RTS)
(serialtty) opened /dev/ttyUSB0 fd 3 (detect rc1180)
(serial /dev/ttyUSB0) sent "00"
(serial) EVENT thread interrupted
(serial) received binary ""
(serial /dev/ttyUSB0) sent "30"
(serial) EVENT thread interrupted
(serial) received binary ""
(serialtty) closed /dev/ttyUSB0 (detect rc1180)
(rc1180) are you there? no.
(serial) EVENT thread interrupted
(serial) before config: /dev/ttyUSB0 speed(B19200) input() output() control(CLOCAL|CREAD|CSIZE) local() special_chars() modem(DTR|RTS)
(serial) after config:  /dev/ttyUSB0 speed(B38400) input() output() control(CLOCAL|CREAD|CSIZE) local() special_chars() modem(DTR|RTS)
(serialtty) opened /dev/ttyUSB0 fd 3 (detect cul)
(serial /dev/ttyUSB0) sent "560A0D"
(serial) EVENT thread interrupted
(serial) received binary "3F202820697320756E6B6E6F776E2920557365206F6E65206F66204220622043206520462047204B206C204D20522054207420562057205820780D0A"
(cul) probe response "? ( is unknown) Use one of B b C e F G K l M R T t V W X x<0D><0A>"
(serial /dev/ttyUSB0) sent "560A0D"
(serial) EVENT thread interrupted
(serial) received binary "5620312E3637206E616E6F43554C3836385F723536380D0A"
(cul) probe response "V 1.67 nanoCUL868_r568<0D><0A>"
(serialtty) closed /dev/ttyUSB0 (detect cul)
(cul) are you there? yes
(serial) EVENT thread interrupted
(main) opening /dev/ttyUSB0:cul:c1
Started config cul on /dev/ttyUSB0 listening on c1
(cul) on /dev/ttyUSB0
(serial) EVENT thread interrupted
(serial) before config: /dev/ttyUSB0 speed(B38400) input() output() control(CLOCAL|CREAD|CSIZE) local() special_chars() modem(DTR|RTS)
(serial) after config:  /dev/ttyUSB0 speed(B38400) input() output() control(CLOCAL|CREAD|CSIZE) local() special_chars() modem(DTR|RTS)
(serialtty) opened /dev/ttyUSB0 fd 3 (cul)
(main) regular reset of cul  on /dev/ttyUSB0 will happen every 82800 seconds
(cul) set link mode c
(serial /dev/ttyUSB0) sent "6272630A0D"
(serial) EVENT thread interrupted
(serial) received binary "43"
(cul) checkCULFrame "C"
(cul) no eol found yet, partial frame
(serial) received binary "4D4F44450D0A"
(cul) checkCULFrame "CMODE<0D><0A>"
(cul) no leading 'b' so it is text and no frame
(cul) received "CMODE"(serial /dev/ttyUSB0) sent "5832310A0D"
(wmbus) no alarm (expected activity) for cul
(serial) registered regular callback HOT_PLUG_DETECTOR(0) every 2 seconds
(serial) EVENT thread interrupted
No meters configured. Printing id:s of all telegrams heard!
(serial) waiting for stop
(serial) received binary "623636343434393641313036343033353531343337433533333732353133343530313534393641303030"
(cul) checkCULFrame "b6644496A106403551437C5337251345015496A000"
(cul) no eol found yet, partial frame
(serial) received binary "37393830303530303534463543333435383032413030323135303435393435394631453339463745354330373233323133434138453641324346383438"
(cul) checkCULFrame "b6644496A106403551437C5337251345015496A0007980050054F5C345802A002150459459F1E39F7E5C0723213CA8E6A2CF848"
(cul) no eol found yet, partial frame
(serial) received binary "3239413539353935323844363033424246453135313731463039454332344243334139413736353039363142453741443439313345413343434643354131464439414630453946363843393231464439353430373832393036444643373443314343374433323430433144454238384344323145324242"
(cul) checkCULFrame "b6644496A106403551437C5337251345015496A0007980050054F5C345802A002150459459F1E39F7E5C0723213CA8E6A2CF84829A5959528D603BBFE15171F09EC24BC3A9A7650961BE7AD4913EA3CCFC5A1FD9AF0E9F68C921FD9540782906DFC74C1CC7D3240C1DEB88CD21E2BB"
(cul) no eol found yet, partial frame
(serial) received binary "33354443433745443244353335383234310D0A"
(cul) checkCULFrame "b6644496A106403551437C5337251345015496A0007980050054F5C345802A002150459459F1E39F7E5C0723213CA8E6A2CF84829A5959528D603BBFE15171F09EC24BC3A9A7650961BE7AD4913EA3CCFC5A1FD9AF0E9F68C921FD9540782906DFC74C1CC7D3240C1DEB88CD21E2BB35DCC7ED2D5358241<0D><0A>"
(cul) checkCULFrame RSSI_RAW=65
(cul) checkCULFrame LQI=65
(wmbus) trimming frame A "6644496A106403551437C5337251345015496A0007980050054F5C345802A002150459459F1E39F7E5C0723213CA8E6A2CF84829A5959528D603BBFE15171F09EC24BC3A9A7650961BE7AD4913EA3CCFC5A1FD9AF0E9F68C921FD9540782906DFC74C1CC7D3240C1DEB88CD21E2BB35DCC7ED2D535"
(wmbus) ff a dll crc 0-9 c533 ok
(wmbus) ff a dll crc mid 12-27 5802 ok
(wmbus) ff a dll crc mid 30-45 8e6a ok
(wmbus) ff a dll crc mid 48-63 ec24 ok
(wmbus) ff a dll crc mid 66-81 fd9a ok
(wmbus) ff a dll crc mid 84-99 7d32 ok
(wmbus) ff a dll crc final 102-114 d535 ok
(wmbus) trimmed 14 dll crc bytes from frame a and ignored 0 suffix bytes.
(wmbus) trimmed frame A "6644496A1064035514377251345015496A0007980050054F5C34A002150459459F1E39F7E5C0723213CA2CF84829A5959528D603BBFE15171F09BC3A9A7650961BE7AD4913EA3CCFC5A1F0E9F68C921FD9540782906DFC74C1CC40C1DEB88CD21E2BB35DCC7ED2"
(cul) received full T1 frame
(wmbus) parseDLL @0 103
(telegram) DLL L=66 C=44 (from meter SND_NR) M=6a49 (ZRI) A=55036410 VER=14 TYPE=37 (Radio converter (meter side)) (driver unknown!) DEV=cul RSSI=-42
(wmbus) parseELL @10 93
(wmbus) parseNWL @10 93
(wmbus) parseAFL @10 93
(wmbus) parseTPL @10 93
(telegram) TPL CI=72 ACC=98 STS=00 CFG=0550 (AES_CBC_IV nb=5 cntn=0 ra=0 hc=0) ID=51345015 MFT=496a VER=00 TYPE=07 (Water meter)
Received telegram from: 55036410
          manufacturer: (ZRI) ZENNER International, Germany (0x6a49)
                  type: Radio converter (meter side) (0x37)
                   ver: 0x14
      Concerning meter: 15503451
          manufacturer: (ZRI) ZENNER International, Germany (0x6a49)
                  type: Water meter (0x07)
                   ver: 0x00
                device: cul
                  rssi: -42 dBm
                driver: unknown!
```

Press Ctrl-C to stop the execution. There are a few important points you can learn from this printout.

wmbusmeters was able to connect to the nanoCUL and the firmware r568 is running on it:

```
(cul) probe response "V 1.67 nanoCUL868_r568<0D><0A>"
(serialtty) closed /dev/ttyUSB0 (detect cul)
(cul) are you there? yes
```

The nanoCUL firmware supports C1 mode:

```
(cul) received "CMODE"(serial /dev/ttyUSB0) sent "5832310A0D"
```

wmbusmeters is listening to everything it can hear:

```
No meters configured. Printing id:s of all telegrams heard!
```

And finally we can see that the nanoCUL has received something and wmbusmeters is able to tell that this particular frame was coming from Zenner water meter. Everything looks good and your nanoCUL is working!

Now you can proceed configuring wmbusmeters normally and set up your meters.

## nanoCUL vendors

I bought my nanoCUL from a company called [smart-home-komponente.de](https://www.smart-home-komponente.de/nano-cul/nano-cul-868/). I've also provided them with the firmware I created for myself, so perhaps they can flash it for you on purchase. I get no incentive in mentioning them here.

## Troubleshooting

### TTY Buffer Too Small

If you do get an error such as:

```
(cul) bad hex "6644496A106403551437C5337251345015496A00073B005005CE0607C429BAC37E8E51209BAEA0F3B6DEC8F47487DB9697D3760960AFA2E53813F0ED9B555AEBC7CD9EEAF9AE50AA5555C8F256F701D35F21D72DAAFE161E83E91B994AA5AE2C74210775B3E44BF5E8FC49091"
(cul) warning: the hex string is not proper! Ignoring telegram!
(cul) error in received message.
```

This likely means that the wMBus telegram was larger than what can be fit into the TTY buffer of the nanoCUL. The firmware linked here has a buffer size of 300 bytes that's about the maximum I could fit in the small RAM. It should be possible to receive a wMBus telegram of 148 bytes with this firmware.

If you want to grow the buffer size and compile your new firmware modify this line in board.h before compiling:

```
#define TTY_BUFSIZE             300
```

### avrdude Fails Verification of Flash

For some reason avrdude was giving me verification errors such as the one below often when flashing. I simply retried until I did not get errors. The stick seemed to work even with those errors though.

```
$ avrdude -D -p atmega328p -P /dev/ttyUSB0 -b 115200 -c arduino -U flash:w:nanoCUL_r568_mbus_c1t1_bufsize300.hex

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.01s

avrdude: Device signature = 0x1e950f (probably m328p)
avrdude: reading input file "nanoCUL.hex"
avrdude: input file nanoCUL.hex auto detected as Intel Hex
avrdude: writing flash (14760 bytes):

Writing | ################################################## | 100% 5.56s

avrdude: 14760 bytes of flash written
avrdude: verifying flash memory against nanoCUL.hex:
avrdude: load data flash data from input file nanoCUL.hex:
avrdude: input file nanoCUL.hex auto detected as Intel Hex
avrdude: input file nanoCUL.hex contains 14760 bytes
avrdude: reading on-chip flash data:

Reading | ################################################## | 100% 4.98s

avrdude: verifying ...
avrdude: verification error, first mismatch at byte 0x1b86
         0xeb != 0xef
avrdude: verification error; content mismatch

avrdude: safemode: Fuses OK (E:00, H:00, L:00)

avrdude done.  Thank you.
```

### stk500_recv(): programmer is not responding Error When Flashing

Many guides tell you to use baud rate of 57600 bps but I got *stk500_recv(): programmer is not responding* errors unless I changed the baud rate to 115200 on the avrdude command.

```
$ avrdude -D -p atmega328p -P /dev/ttyUSB0 -b 57600 -c arduino -U flash:w:nanoCUL_r568_mbus_c1t1_bufsize300.hex
avrdude: stk500_recv(): programmer is not responding
avrdude: stk500_getsync() attempt 1 of 10: not in sync: resp=0x00
```

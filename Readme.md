# ATmega328P-xx XTAL-less that is recognized as Arduino Pro Mini
There is an electronics kit manufacturer that designs kits with ATmega328P-PU with no crystal (XTAL-less) and in the instructions it prompts you to program it as an Arduino Pro Mini 3V3 8MHz.

I searched on the web but I didn't find an answer. 
How is this done?
Well, I have on hand two chips from that vendor pre-programed.
Since I didn't have my Atmel Studio 7 installed on my computer, I decided to use the avrdue.exe that comes with Arduino IDE.
First, I read the uses in a "safe-mode" with an Arduino UNO programmed as ArduinoISP, and I have a 28DIP ZIF socket programmer on top of it (with a 10uF capacitor between RESET and GND)
https://shop.evilmadscientist.com/productsmenu/253
I ran the following command in the command prompt (CMD), where the portable version arduino-1.8.12 with avrdude is, and COM6 is  my ArduinoISP assigned serial port.

C:\Users\user\AppData\Local\Programs\arduino-1.8.12\hardware\tools\avr/bin/avrdude -CC:\Users\user\AppData\Local\Programs\arduino-1.8.12\hardware\tools\avr/etc/avrdude.conf -v -patmega328p -cstk500v1 -PCOM6 -b19200 -U lfuse:r:-:i

A chip programed with Arduino Uno Bootloader

avrdude: safemode: lfuse reads as FF

avrdude: safemode: hfuse reads as DE

avrdude: safemode: efuse reads as FD

avrdude: safemode: Fuses OK (E:FD, H:DE, L:FF)

I ran the same command on the chip that is a "XTAL-less Arduino Pro Mini" to read the fuses.

C:\Users\user\AppData\Local\Programs\arduino-1.8.12\hardware\tools\avr/bin/avrdude -CC:\Users\user\AppData\Local\Programs\arduino-1.8.12\hardware\tools\avr/etc/avrdude.conf -v -patmega328p -cstk500v1 -PCOM6 -b19200 -U lfuse:r:-:i

avrdude: safemode: lfuse reads as E2

avrdude: safemode: hfuse reads as DA

avrdude: safemode: efuse reads as FE

avrdude: safemode: Fuses OK (E:FE, H:DA, L:E2)

I used the Arduino IDE to burn a Pro Mini bootloader blank ATmega328P-PU and copied the two commands.
I modified the first command to set the fuses for E:FE, H:DA, L:E2
Next, I ran the two commands in the CMD.

C:\Users\user\AppData\Local\Programs\arduino-1.8.12\hardware\tools\avr/bin/avrdude -CC:\Users\user\AppData\Local\Programs\arduino-1.8.12\hardware\tools\avr/etc/avrdude.conf -v -patmega328p -cstk500v1 -PCOM6 -b19200 -e -Ulock:w:0x3F:m -Uefuse:w:0xFE:m -Uhfuse:w:0xDA:m -Ulfuse:w:0xE2:m 

C:\Users\user\AppData\Local\Programs\arduino-1.8.12\hardware\tools\avr/bin/avrdude -CC:\Users\user\AppData\Local\Programs\arduino-1.8.12\hardware\tools\avr/etc/avrdude.conf -v -patmega328p -cstk500v1 -PCOM6 -b19200 -Uflash:w:C:\Users\user\AppData\Local\Programs\arduino-1.8.12\hardware\arduino\avr/bootloaders/atmega/ATmegaBOOT_168_atmega328_pro_8MHz.hex:i -Ulock:w:0x0F:m

I confirmed it the fuses are as expected by reading them.

C:\Users\user\AppData\Local\Programs\arduino-1.8.12\hardware\tools\avr/bin/avrdude -CC:\Users\user\AppData\Local\Programs\arduino-1.8.12\hardware\tools\avr/etc/avrdude.conf -v -patmega328p -cstk500v1 -PCOM6 -b19200 -U lfuse:r:-:i

avrdude: safemode: lfuse reads as E2

avrdude: safemode: hfuse reads as DA

avrdude: safemode: efuse reads as FE

avrdude: safemode: Fuses OK (E:FE, H:DA, L:E2)

I removed the chip, and placed in on a breadboard with no XTAL.
I used an FTDI cable connected the RTS (or DTR) with 100nF ceramic capacitor and 100k pull up resistor.
I used the 5V from the FTDI cable as Vcc power for the ATmega328P-PU, 
Connected PB5 to an LED with an 1k resistor in series.
Selected in Arduino IDE Pro Mini 3V3 8MHz and loaded the Blink.ino sketch.
The LED was flashing as expected with no XTAL.

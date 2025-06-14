# SOVOL / COMGROW dryer firmware

The SOVOL SH01 comes with awkward firmware which runs for a maximum of 12 hours at a time, at which point the device switches the heater off. The heater must be run with the box lid open, to allow humidity to escape. Likewise the box lid must then be closed at the end of the drying process.

When drying spools of filament, you might want to run the dryer for a day or two just to be sure, but if the limit is set to 12 hours, you're required to reset the device quite frequently, which requires remembering or setting alarms. If you forget to reset the device within 12 hours, as the heater switches off and the box lid remains open, you run the risk of the filament capturing humidity
again and having to repeat the drying process.

Increasing this runtime limit is the key to happy filament drying.

<img src="./sovol-sh01.jpg" width="200" alt="SOVOL SH01" />

## Firmware files

### fw-v1.hex

* [download fw-v1.hex](./fw-v1.hex)

This is the original firmware.

* single push to power on
* temperature: 40C (default), 45C, 50C
* runtime: 6 - 12h in 1h increments (6h default)

### fw-v2.hex


* [download fw-v2.hex](./fw-v2.hex)

This is the firmware which ships with some later models.

* push-and-hold to power on
* temperature: 40C, 45C, 50C (default)
* runtime: 6 - 48h in 2h increments (6h default)

### fw-72h-*c.hex

* [download fw-72h-50c.hex](./fw-72h-50c.hex)
* [download fw-72h-45c.hex](./fw-72h-45c.hex)
* [download fw-72h-40c.hex](./fw-72h-40c.hex)

This is a modified firmware with sensible defaults. Push the Ⓜ️ button 3 times to start, no need to keep pushing ⬆️. This is the one I run.

* push-and-hold to power on
* temperature: 40C, 45C, 50C (see filename for default)
* runtime: 8 - 72h in 8h increments (72h default)

## Flashing your dryer

> [!CAUTION]
> Attempting to program your dryer may cause irreparable damage. I make no guarantees. Proceed at your own risk.

You will need:

* A SOVOL SH01 (SH02 may also work - check the chip)
* A Phillips screwdriver
* A Windows PC (or VM with access to USB)
* A USB to serial adapter (keep reading, some don't work)

### Serial adapters

> [!IMPORTANT]
> Which USB to serial adapter you use is critical.
>
> Working:
> * DSD TECH SH-U09C5 FT232NL:
> [amazon.com.au](https://amzn.to/3Puw7qN) |
> [amazon.ca](https://amzn.to/4fSzna0) |
> [amazon.de](https://amzn.to/4j7NfQK) |
> [amazon.es](https://amzn.to/40oVKj4) |
> [amazon.fr](https://amzn.to/3Wa2U8g) |
> [amazon.it](https://amzn.to/3FywY8F) |
> [amazon.nl](https://amzn.to/3BZ6hYX) |
> [amazon.pl](https://amzn.to/40qX1WJ) |
> [amazon.se](https://amzn.to/3PrbuMe) |
> [amazon.co.uk](https://amzn.to/40sdKsO) |
> [amazon.com](https://amzn.to/40t351c) (affiliate links, cookie valid for any purchases within 24h)
> * [Flipper Zero](https://shop.flipperzero.one/)
> * AliExpress CH340-based [with RTS pin soldered](https://github.com/rcambrj/sovol-dryer-firmware/discussions/9#discussion-8419312)
>
>   <img src="aliexpress-ch340.png" width="200" alt="stock photo of an AliExpress CH340-based USB to serial adapter" />
>
> Not working:
> * [Adafruit FTDI Friend FT232RL](https://www.adafruit.com/product/284)
> * [BAITE BTE13-007 CP2102 bridge](https://www.taydaelectronics.com/datasheets/files/A-1991.pdf)


1. Remove the four screws and remove the dryer tray

	<img src="./tray-screws.webp" width="400" alt="4 dryer tray screws" />

2. Confirm that the chip on your board is a `HC32F005x6xx`. The numbering is small and in an awkward location, try taking a photo with your phone. If the chip does not match, these firmware files will likely cause irreparable damage. Do not proceed.

3. Configure your USB to serial adapter to send 3.3V

4. Connect your USB to serial adapter to the dryer

	| SH01 pin | serial adapter pin | Flipper Zero pin |
	|----------|--------------------|------------------|
	| 3V3      | VCC                | Pin 9 (3V3)      |
	| GND      | GND                | Pin 11 (GND)     |
	| SWDIO    | TX                 | Pin 13 (TX)      |
	| SWDCK    | RX                 | Pin 14 (RX)      |
	| NRST     | RTS                | Pin 2 (A7)       |

	<img src="./swd-pins.jpg" width="400" alt="SWD pins"/>

5. Connect your USB to serial adapter to your Windows machine.

6. Download the HDSC MCU programmer https://github.com/Xinyuan-LilyGO/T-HC32/tree/main/tools (HDSP ISP.zip)

7. Configure the programmer ([manual in english](./hdsc-mcu-programmer-manual-en.pdf)):

	a. Click the first menu dropdown and select English.

	  <img src="./hdsc-mcu-programmer-menu.png" alt="HDSC MCU Programmer menu to select English" />

	b. Configure the MCU as `HC32L110x6xx/HC32F005x6xx`

	c. Select a baud rate, slower will be more reliable

	d. Download and select a [firmware file](#firmware%20files)

	e. Configure the COM port to be your USB to serial device (replug it if you're not sure)

	f. Select the `Erase`, `Blank Check` and `Program` checkboxes

	g. Push the `Upload` button (the left one) to download the current firmware from the chip (you will be prompted for a location to save the file) or the `Execute` button (the right one) to write the selected firmware to the chip

	<img src="./hdsc-mcu-programmer-zh.png" alt="HDSC MCU Programmer in Chinese" />

	<img src="./hdsc-mcu-programmer-en.png" alt="HDSC MCU Programmer in English">

8. That's it! Now keep reading...

### Sharing your findings

Please [report your success / failure](https://github.com/rcambrj/sovol-dryer-firmware/discussions/new?category=reports-of-success-failure&title=It+worked+/+didn't+work&body=*+Dryer+brand+%26%20model:%20eg.%20SOVOL%20SH01%0A*%20Chip%20identifier:%20eg%20HC32F005x6xx%0A*%20USB%20to%20serial%20adapter:%20eg.%20DSD%20TECH%20SH-U09C5) so that we can all learn from it.

## Credits

anselor_557733 for [the photo of the inside of the dryer](https://www.printables.com/model/734639-custom-insert-for-sovol-sh01-comgrow-filament-drye). I'm really lazy.
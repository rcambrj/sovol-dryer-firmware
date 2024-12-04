# SOVOL / COMGROW dryer firmware

The SOVOL SH01 comes with awkward firmware which runs for a maximum of 12 hours at a time, at which point the device switches the heater off. The heater must be run with the box lid open, to allow humidity to escape. Likewise the box lid must then be closed at the end of the drying process.

When drying spools of filament, you might want to run the dryer for a day or two just to be sure, but if the limit is set to 12 hours, you're required to reset the device quite frequently, which requires remembering or setting alarms. If you forget to reset the device within 12 hours, as the heater switches off and the box lid remains open, you run the risk of the filament capturing humidity
again and having to repeat the drying process.

Increasing this runtime limit is the key to happy filament drying.

![SOVOL SH01](./sovol-sh01.jpg)

## Firmware files

### fw.hex

[download](./fw.hex)

This is the original firmware.

* single push to power on
* temperature: 40C (default), 45C, 50C
* runtime: 6h (default), 7h, 8h, 9h, 10h, 11h, 12h

### fw-48h.hex

[download](./fw-48h.hex)

* push-and-hold to power on
* temperature: 40C, 45C, 50C (default)
* runtime: 6h (default), 8h, 10h, 12h, 14h, 16h, 18h, 20h, 22h, 24h, 26h, 28h, 30h, 32h, 34h, 36h, 38h, 40h, 42h, 44h, 46h, 48h

## Flashing your dryer

> [!CAUTION]
> Attempting to program your dryer may cause irreparable damage. I make no guarantees. Proceed at your own risk.

You will need:

* A SOVOL SH01 (SH02 may also work - check the chip)
* A Phillips screwdriver
* A Windows PC (or VM with access to USB)
* A USB to serial adapter (keep reading, some don't work)

> [!IMPORTANT]
> Which USB to serial adapter you use is critical.
>
> Working:
> * [DSD TECH SH-U09C5 FT232NL](https://www.amazon.nl/-/en/dp/B07WX2DSVB)
> 
> Not working:
> * [Adafruit FTDI Friend FT232RL](https://www.adafruit.com/product/284)
> * [BAITE BTE13-007 CP2102 bridge](https://www.taydaelectronics.com/datasheets/files/A-1991.pdf)


1. Remove the four screws and remove the dryer tray

	![4 dryer tray screws](./tray-screws.webp)

2. Confirm that the chip on your board is a `HC32F005x6xx`. If the chip does not match, these firmware files will likely cause irreparable damage. Do not proceed.

3. Configure your USB to serial adapter to send 3.3V

4. Connect your USB to serial adapter to the dryer

	```
	3V3   => VCC
	GND   => GND
	SWDIO => TX
	SWDCK => RX
	NRST  => RTS
	```

	![SWD pins](./swd-pins.jpg)

5. Connect your USB to serial adapter to your Windows machine.

6. Download the HDSC MCU programmer https://github.com/Xinyuan-LilyGO/T-HC32/tree/main/tools (HDSP ISP.zip)

7. Configure the MCU programmer:

	a. Click the first menu dropdown and select English.

	b. Configure the MCU as `HC32L110x6xx/HC32F005x6xx`

	c. Select a baud rate, slower will be more reliable

	d. Download and select a [firmware file](#firmware%20files)

	e. Configure the COM port to be your USB to serial device (replug it if you're not sure)

	f. Push the `Upload` button (the left one) to download the current firmware from the chip (you will be prompted for a location to save the file) or the `Execute` button (the right one) to write the selected firmware to the chip

	![HDSC MCU Programmer](./hdsc-mcu-programmer.png)

8. [Report your success / failure](https://github.com/rcambrj/sovol-dryer-firmware/discussions/new?category=reports-of-success-failure), please report the following:

	```
	* Dryer brand & model: eg. SOVOL SH01
	* Chip identifier: eg HC32F005x6xx
	* USB to serial adapter: eg. DSD TECH SH-U09C5
	```


## Improving the firmware further

Do you know how to decompile+recompile these files? Please help! It'd be great to further improve them.

## Credits

anselor_557733 for [the photo of the inside of the dryer](https://www.printables.com/model/734639-custom-insert-for-sovol-sh01-comgrow-filament-drye). I'm really lazy.
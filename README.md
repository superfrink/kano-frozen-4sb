Kano Frozen 2 Coding Kit documentation

# Product

## Summary

A flat hexagon around 10 cm across.  There are four proximity sensors placed equidistant around the outside.  There are eight LEDs around the outside and one in the center that light with varying brightness depending on the proximity detected by the nearest sensor.  The device connects via BlueTooth.  There are some software applications that use the device for interacting with the software.  There is one button that when held for three seconds puts the device to sleep.  Pressing the button once wakes the device.  The device is powered by two AA batteries.

## Identification Information

* FCC ID: `2ATYD-1012`- https://fccid.io/2ATYD-1012/
* IC ID: `25240-1012`- enter as the `Certification Number` on https://sms-sgs.ic.gc.ca/equipmentSearch/searchRadioEquipments?execution=e1s1&lang=en
* The PCB is labeled with: `KANO K4SB V1.0`, `P/N: HG-C-RB265C`, and `19G2011A`

# Hardware Information

* SOC: Nordic nRF52832 - https://www.nordicsemi.com/products/nrf52832
* PW546A 4-channel switch for I2C and SMBus.  Perhaps this allows the SOC to communicate with one proximity sensor at a time?
* The proximity sensors appear to use use IR reflection?
* The 9 LEDS that light with proximity are RGB.
* There is one blue LED that indicates BlueTooth connectivity state.

## Circuit Board
* The PCB has pads for an un-populated USB connector labeled `USB1`.
* There are un-populated through-holes for an 8-pin connector labeled `PROG1`.
* Next to `PROG1` are nine pads labeled:
  * `CK`
  * `BT`
  * `TS`
  * `US`
  * `GD`
  * `IO`
  * `RT`
  * `SO`
  * `3V`
* The sleep/wake button is labeled `SW1`.
* There are four un-populated pads labeled `SW2`.
* There are many test pads labeled `TP1` through `TP20`.
* Next to the `USB` pads there are two pads labeled `VB1` and `VB2`.

# BlueTooth Information

The `nRF Connect` app for Android from Nordic sees the device as `Kano-4SB-4c-2e-62` (`4c-2e-62` are the last 3 octets in the MAC address) when scanning for devices.

## UUID 0x1800
FIXME: incomplete

## UUID 0x1801
FIXME: incomplete

## UUID 0x1801
FIXME: incomplete

## UUID 11a70100-f691-4b93-a6f4-0968f5b648f8

| UUID                                 | R, W, RW, N | Value            | Purpose                 |
| ------------------------------------ | ----------- | ---------------- | ----------------------- |
| 11a70101-f691-4b93-a6f4-0968f5b648f8 | R           | "Kano Computing" |                         |
| 11a70102-f691-4b93-a6f4-0968f5b648f8 | R           | "1.1.1"          |                         |
| 11a70103-f691-4b93-a6f4-0968f5b648f8 | R           | "0"              |                         |
| 11a70104-f691-4b93-a6f4-0968f5b648f8 | R           | "0"              |                         |

## UUID 11a70200-f691-4b93-a6f4-0968f5b648f8

| UUID                                 | R, W, RW, N | Value   | Purpose                 |
| ------------------------------------ | ----------- | ------- | ----------------------- |
| 11a70201-f691-4b93-a6f4-0968f5b648f8 | R, N        | 4 bytes | proximity sensor values |

### 11a70201-f691-4b93-a6f4-0968f5b648f8

* Each of the four bytes indicates the proximity sensor value for one of the four sensors.  The value ranges from `00` (object close to sensor) to `FF` (no object close to sensor).  `FF` appears to be around 20 cm distance.  Have not yet tested to see if the value changes linearly with distance.

Notification is supported.  When enabled the values will be streamed.

## UUID 11a70300-f691-4b93-a6f4-0968f5b648f8

| UUID                                 | R, W, RW, N | Value                  | Purpose                 |
| ------------------------------------ | ----------- | ---------------------- | ----------------------- |
| 11a70301-f691-4b93-a6f4-0968f5b648f8 | R, W        | twenty bytes, all 0x00 | control registers?      |
| 11a70302-f691-4b93-a6f4-0968f5b648f8 | W           |                        | keep alive              |
| 11a70303-f691-4b93-a6f4-0968f5b648f8 | R           | 0x55                   | battery voltage         |
| 11a70304-f691-4b93-a6f4-0968f5b648f8 | R           | 0x19                   | global brightness       |

### 11a70301-f691-4b93-a6f4-0968f5b648f8

* The initial state is all `0x00`.
* Writing a few byte changes the first of 20 bytes.  Writing `0x01 0x55 0x00 0x55` then reading returns
```
0x01 0x55 0x00 0x55
0x00 0x00 0x00 0x00
0x00 0x00 0x00 0x00
0x00 0x00 0x00 0x00
0x00 0x00 0x00 0x00
```
* It appears writing any non-zero byte to the first byte will set the byte to `0x01`.  Writing `0xFF`then reading returns the same result as writing `0x01` then reading.

* The next 3 bytes control the varying brightness of the different RGB colours.

| Byte | Purpose                                                                             |
| ---- | ----------------------------------------------------------------------------------- |
| 0x00 | Setting to non-zero turns the 9 RGB LEDs off.  Reading sensor values is unaffected. |
| 0x01 | One of the RGB colours                                                              |
| 0x02 | One of the RGB colours                                                              |
| 0x03 | One of the RGB colours                                                              |
| 0x04 |                                                                                     |
| 0x05 |                                                                                     |
| 0x06 |                                                                                     |
| 0x07 |                                                                                     |
| 0x08 |                                                                                     |
| 0x09 |                                                                                     |
| 0x0A |                                                                                     |
| 0x0B |                                                                                     |
| 0x0C |                                                                                     |
| 0x0D |                                                                                     |
| 0x0E |                                                                                     |
| 0x0F |                                                                                     |
| 0x10 |                                                                                     |
| 0x11 |                                                                                     |
| 0x12 |                                                                                     |
| 0x13 |                                                                                     |
| 0x14 |                                                                                     |

### 11a70303-f691-4b93-a6f4-0968f5b648f8

* On new batteries started around `0x5B`, appears to decline with battery use.

### 11a70304-f691-4b93-a6f4-0968f5b648f8

Brightness of the 9 RGB LEDS.

* Defaults to `0x19`.
* Writing `0x00` then reading returns `0x0A` implying this is the lowest allowed brightness.
* Writing `0xFF` then reading returns `0x64` implying this is the highest allowed brightness.
* Does not appear to affect distance of the proximity sensors.

## UUID 0xFE59
FIXME: incomplete


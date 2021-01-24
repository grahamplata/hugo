---
title: "🏖️ My Digital Sandbox"
date: "2021-01-22"
author: grahamplata
excert: "Finding my perfect homelab setup."
tags: ["homelab", "ops", "side-project"]
---

My homelab and homelab accessories
This is a page where I maintain the status of the devices in my “Homelab”

- [Bill of Materials](#bill-of-materials)
  - [Why PI...?](#why-pi)
    - [Raspberry Pi 4 Model B 4GB](#raspberry-pi-4-model-b-4gb)
- [My RPi prep](#my-rpi-prep)
  - [Flash the OS to the SD card](#flash-the-os-to-the-sd-card)
    - [Download Raspbian Lite](#download-raspbian-lite)

---

## Bill of Materials 

| Quantity | What    | Item                                                                                                                                            |  Price |       Total |
| :------- | :------ | :---------------------------------------------------------------------------------------------------------------------------------------------- | -----: | ----------: |
| 4        | Compute | [Raspberry Pi 4 Model B 4GB](https://www.amazon.com/gp/product/B07TC2BK1X/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1)                     | $65.99 | **$263.96** |
| 1        | Case    | [GeeekPi Case with Cooling Fan Heatsink,](https://www.amazon.com/gp/product/B07MW24S61/ref=ppx_yo_dt_b_asin_title_o00_s02?ie=UTF8&psc=1)        | $22.99 |  **$22.99** |
| 1        | Storage | [Sabrent USB 3.0 to SSD](https://www.amazon.com/gp/product/B011M8YACM/ref=ppx_yo_dt_b_asin_title_o00_s01?ie=UTF8&psc=1)                         |  $8.99 |   **$8.99** |
| 2        | Storage | [SanDisk 32GB 2-Pack 2x32GB ](https://www.amazon.com/gp/product/B087JCL881/ref=ppx_yo_dt_b_asin_title_o00_s01?ie=UTF8&psc=1)                    | $15.99 |  **$31.98** |
| 1        | Network | [D-Link Ethernet Switch, 8 Port Gigabit](https://www.amazon.com/gp/product/B000BCC0LO/ref=ppx_yo_dt_b_asin_title_o00_s01?ie=UTF8&psc=1)         | $31.99 |  **$31.99** |
| 1        | Network | [Cable Matters 5-PackCat 6 Cable) in Black 1 ft](https://www.amazon.com/gp/product/B00C4U030G/ref=ppx_yo_dt_b_asin_title_o00_s02?ie=UTF8&psc=1) | $10.99 |  **$10.99** |
| 2        | Power   | [USB A to Type C [3-Pack 1ft]](https://www.amazon.com/gp/product/B08G1HS6SL/ref=ppx_yo_dt_b_asin_title_o00_s02?ie=UTF8&psc=1)                   |  $9.99 |  **$19.98** |
| 1        | Power   | [Anker 60W 6 Port USB](https://www.amazon.com/gp/product/B00P936188/ref=ppx_yo_dt_b_asin_title_o00_s01?ie=UTF8&psc=1)                           | $25.99 |  **$25.99** |
|          |         |                                                                                                                                                 |        | **$416.87** |

### Why PI...?

The Raspberry Pi is incredible value for money. Find me an alternative and I will swap to it.

#### Raspberry Pi 4 Model B 4GB
```bash
1.5GHz 64-bit quad-core ARM Cortex-A72 CPU ( ARM v8, BCM2711B0)
4GB RAM (LPDDR4)
On-board wireless LAN - dual-band 802.11 b/g/n/ac
On-board Bluetooth 5.0, low-energy (BLE)
2x USB 3.0 ports, 2x USB 2.0 ports
Gigabit ethernet
40-pin GPIO header
2× micro-HDMI ports (up to 4Kp60 supported)
DSI display port, CSI camera port
Combined 3.5mm analog audio and composite video jack
Micro-SD card slot
USB-C power
```

## My RPi prep

### Flash the OS to the SD card
There is no need to make things complicated by messing about with **bespoke** operating systems. 
The Raspberry Pi team have done a great job with Raspbian and for a headless system Raspbian Lite is easy to use and quick to flash.

```
Raspberry Pi OS Lite
Release date: January 11th 2021
Kernel version: 5.4
Size: 438MB
```
- [Download](https://downloads.raspberrypi.org/raspios_lite_armhf/images/raspios_lite_armhf-2021-01-12/2021-01-11-raspios-buster-armhf-lite.zip)
- [Download torrent](https://downloads.raspberrypi.org/raspios_lite_armhf/images/raspios_lite_armhf-2021-01-12/2021-01-11-raspios-buster-armhf-lite.zip.torrent)


#### Download Raspbian Lite
Mount the SD card and create a text file named ```"ssh"``` in the freshly created boot partition.
- On MacOS you can usually type in: sudo touch /Volumes/boot/ssh for this step
```TODO: Attachs shell script....```

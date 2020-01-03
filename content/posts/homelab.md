---
title: "🏖️ My Digital Sandbox"
date: "2019-01-15"
author: grahamplata
excert: "Finding my perfect homelab setup."
tags: ["homelab", "ops", "side-project"]
---

This is a page where I maintain the status of the devices in my “Homelab”. I will list my current systems and setups here.

## Device

Pine64 - acquired from kickstarter

```bash
– 1.2 Ghz Quad-Core ARM Cortex A53 64-Bit Processor.
– Dual I/O expansion slots
– Dual Core Mali 400 MP2 Graphics card
– 2GB DDR3 Memory
– Integrated Display engine with HDMI 1.4a
– 10/100/1000Mbps Ethernet Port
```

### Hardware / Software

- **[Pine64](https://www.kickstarter.com/projects/pine64/pine-a64-first-15-64-bit-single-board-super-comput)** - The PINE64 is a quad-core ARM 64-bit based computer
- **[Armbian](https://www.armbian.com/)** - A lightweight Debian or Ubuntu based distribution specialized for ARM developing boards.
- **[Docker](https://www.docker.com/)** - Docker is a tool for automating the deployment of applications as portable, self-sufficient containers

### Setup Guide

> Download **[Armbian](https://www.armbian.com/)** and write the image using **[Etcher](https://www.balena.io/etcher/)**. You may need to extract the image before writing to the sd card.

> Upon completion insert the SD card into the PINE64, connect an ethernet cable and power it up.

> Login as root via SSH and use password 1234. You will be prompted to change this password at first login. You will then be asked to create a normal user account that is sudo enabled.

```bash
 ____  _             __   _  _
|  _ \(_)_ __   ___ / /_ | || |
| |_) | | '_ \ / _ \ '_ \| || |_
|  __/| | | | |  __/ (_) |__   _|
|_|   |_|_| |_|\___|\___/   |_|

Welcome to ARMBIAN 5.70 stable Ubuntu 18.04.1 LTS 4.19.13-sunxi64
System load:   0.08 0.13 0.09  	Up time:       5:18 hours
Memory usage:  45 % of 993MB  	Zram usage:    49 % of 496Mb
CPU temp:      34°C
Usage of /:    25% of 29G

[ 0 security updates available, 52 updates total: apt upgrade ]
Last check: 2019-01-26 18:29

Last login: Sat Jan 26 19:55:35 2019 from 192.168.1.155

[ Kernel was updated, please reboot ]

root@pine64:~#
```

### Containers

Operating-system-level virtualization, also known as containerization, refers to an operating system feature in which the kernel allows the existence of multiple isolated user-space instances. Such instances, called containers

Below you can find a list of services I wish to utiluize on my home network.

- **oznu/unms:armhf** - Arm based Ubiquiti Network Management service which allows you to monitor, configure, upgrade and manage UBNT devices
- **gitlab/gitlab-ce** - GitLab is a web-based Git-repository manager
- **portainer/portainer** - Portainer is a lightweight management UI which allows you to easily manage your Docker
- **ghost:1-alpine** - Ghost is a free and open source blogging platform

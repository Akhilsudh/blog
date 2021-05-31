---
title: Solving Elementary OS Freya Battery Draining Problem Post Shutdown
tags: Linux How-to
---

Recently I switched to Elementary OS Freya from windows as my primary OS.Ever since that day my HP Pavillion Laptop has always had this battery draining problem.. You see, after I switch off my laptop and try switching it on after a few days the battery is all out, and doesn’t switch on at all -_- . I’ve never experienced this problem in my previous Windows 8.1 installation at all!!!

Later I found out that removing the laptops battery after switching the laptop off and putting it back on stops this draining problem. But this solution was obviously tedious and one might even forget to remove the battery some day :(.

After doing some research I found out that the reason behind this draining is due to a feature called Wake-On-Lan ‘WoL’ (A feature where the WiFi is kept on even after shutdown so that this computer can be switched on remotely). In order to solve this battery issue we must disable WoL.

Firstly check if WoL is enabled by running the following command in the terminal:
```sh
$ cat /sys/class/net/eth0/device/power/wakeup
```
If the output is “disabled” then WoL is disabled and the battery draining issue is because of something else. In case the output is “enabled” then follow the steps given below.
Some laptops have an option in the BIOS to disable WoL, but like mine some don’t. I’ll be showing you how to disable WoL using a tool called “ethtool”. To install ethtool execute the following commands in a terminal :
```sh
$ sudo apt-get update
$ sudo apt-get install ethtool
```
After installation you can disable WoL by running the following command:
```sh
$ sudo ethtool -s eth0 wol d
```
This disables the WoL feature and the Battery draining problem is solved “Temporarily”. Its temporary because once you switch the laptop back on, it gets enabled again!! To avoid this from happening, update the rc.local file in /etc/rc.local and ‘insert’ the following line to that file before the “exit 0” line :
```sh
$ <path_where_ethtool_is_installed>/ethtool -s eth0 wol d
```
This makes sure that on every startup WoL is disabled and the problem is solved!!! Cheers :D

---

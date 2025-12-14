---
layout: post
title: "Malicious USB Devices"
author: "James Habben"
tags: Malware
---

I put together some slides over a year ago after working several cases involving suspicious USB devices. The slides cover some studies that threw USB devices on the ground, and a couple scenarios from the Verizon Data Breach Digest (shameless promo). There is a lot of significance in these links, and presenting these slides has shown me that this threat is very widely underappreciated.

We, and InfoSec and even more specifically as DFIR specialists, are not immune to an attack conducted through a USB device. Some of us hold a more traditional stance that the forensic workstation remains unconnected from any network connection, but this is an increasingly difficult stance to hold in more recent years. The source evidence drives are getting larger and larger, and this is forcing examiners to take advantage of network storage solutions. We are also seeing many tools with increasing reliance on network for collection and analysis through integrations with other products.

I worked with some others on my team to develop a forensic methodology that has been tried and true. It gives us the best chance of preserving any data that may exist on this USB device and protects our forensic infrastructure from any potential attack. I am putting this methodology in this blog post in an effort to get it out to a wider audience than the folks that have sat through one of my talks. You can see a video from BsidesSLC if you would like, or feel free to contact me and I would be very happy to head out somewhere to give the talk live.

##### The High Level Methodology

- Collect image
- Collect volatile data
- Analyze file contents
- Analyze volatile data
- Collect firmwar

##### The Methodology Steps

- Collect Image
- Physical machine
- Linux forensic boot cd
- Hardware USB write-blocker
- dd, dcfldd, linen, etc
- Collect Volatile Data
- Physical machine
- Windows OS - Small HDD, forensic wipe (0x00)
- Software USB write-blocker
- Collect before images: HDD &amp; RAM
- Prep volatile collection tools &amp; scripts
- Start PowerShell diff-pnp-devices.ps1*
- Insert USB, wait for a minute
- Finish diff-pnp-devices.ps1
- Finish volatile tools &amp; scripts
- Collect image: HDD &amp; RAM
- Analyze File Contents
- Automated AV scans
- IOC Searches
- File format specific tools
- Analyze Volatile Data
- Compare disk images
- Compare RAM images
- Review new devices from diff-pnp-devices.ps1
- Look for evil
- Collect Firmware
- Only needed if device has additional device
- Identify controller chip - ChipEasy
- Acquire correct tool to dump firmware
- Reverse engineer firmware

##### The PowerShell Tool

The script mentioned in this methodology is in my GitHub. It does a very simple thing.

https://gist.github.com/JamesHabben/

- Get a list of PnP devices
- Wait for user to continue after inserting USB
- Get another list of PnP devices
- Compare the two lists and print the differences

##### Takeaway

These USB devices can do a log of damage, and I continue to see a lot of very surprised faces during my talks. It is a real threat (Stuxnet!) and it needs to be accounted for in your incident response.

Please reach out if you have questions.

James Habben

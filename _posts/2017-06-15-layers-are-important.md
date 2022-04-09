---
layout: post
title: "Layers Are Important"
author: "James Habben"
tags: Layers
---

We in InfoSec chant it often and for some of us it might even be a daily mantra. “Use Multi-Factor Authentication!” (MFA) Sometimes called Two Factor Authentication (2FA), it adds an additional layer of security to your organization that almost allows for the use of ‘password’ as a password.

If you keep up with the Verizon Data Breach Investigations Report, you should already know that user credentials are the most sought after piece of information over all the incidents. With that kind of data to support a solution, it is still a bit surprising how many organizations out there are exposing services to the public internet without the extra layer(s) of authentication.

##### More Layers

As great as MFA/2FA is, it will not eliminate all of your problems. I had a troublesome case recently that involved phishing, exposed web services, Remote Access Tools (RAT), stolen credentials, and more. The part that made it really scary was how the attackers were able to figure out the infrastructure enough to almost get VPN access.

The attackers got access to email. Through email they were able to social engineer their way into quite a few areas. One of those areas was how employees obtain the token software and keys for VPN access. Let me restate that with a little more clarity. The attackers requested and got access to VPN tokens used as a part of the MFA/2FA protection.

The process of getting approved for VPN was quite a lengthy one, I know since I had to go through it for remote access as a part of the incident management. After struggling to get myself access, I was astounded at the fact that attackers were able to get so far. It took me quite a while to work through the protections even with the guys on the phone walking me through it all.

##### Simple Works

You know what stopped the attackers? A registry key. Nothing functional. Just a simple registry key that they inject on company assets. The VPN login process has a full posture check on validating your patches, anti-virus program version, firewall configuration, agent installs, etc. and part of that process includes checking for the existence of a simple registry key.

It might sound silly amidst discussions about all this high tech prevention and machine learning analysis, but sometimes simple works. Don’t overlook the basic protections. They add layers of protection that just might actually be the one piece that saves the day.

James Habben

---
layout: post
title: "Parsing CFBundleURLSchemes from MacOS Apps"
author: "James Habben"
tags: MacOS
---

Several days ago, Objective-See shared details about an attack vector used by advanced attackers to target MacOS users. If you haven’t read about it, I encourage you to do that now since this post really won’t make a lot of sense otherwise. It is a very creative way to gain remote execution.

##### Quick Review

1. Applications on MacOS are distributed as ‘.app files’ and they are really just folders that MacOS displays as files.
2. Application .app folders have a prescribed internal architecture since MacOS parses many of the files for functionality.
3. Plists are settings files that can store many formats of name value data pairs (somewhat similar to the registry in Windows world).
4. All the points from the Objective-See blog about the attack chain.

##### Defense Approach

There are all kinds of ways to attempt to control this type of attack. One area that came to my mind was using a packet capture device to parse downloaded files for the required ‘info.plist’ file needed for this attack. Not on this post though, maybe another post.

##### Forensic Approach

When analyzing a computer(s) for attacks, we rely on tools to do the monotonous work of pulling data from known locations. I found this attack interesting and decided to build one of these tools. It is standalone since I don’t know of any regripper like tools for MacOS. Drop a comment if I am uninformed.

My approach is written in Python so it can be run on multiple OS platforms, and requires a MacOS drive to be mounted or files/folders to be copied to some drive. The script looks for ‘info.plist’ files inside a ‘content’ folder inside another folder ending in ‘.app’. Essentially ‘*.app/content/info.plist’, since there can be a whole lot more ‘info.plist’ files spread all over the place.

Once the proper plist file is located, it looks for a ‘CFBundleURLTypes’ value to ensure the application is attempting to register a URL handler. Then it looks for a ‘CFBundleURLSchemes’ value to get the handler prefix. Application can claim multiple URL handlers.

The default output is simple JSON data that is really more like CSV data, only hipper. Use pip to install pandas and give it a ‘-g’, and you will get a grouped list of handler prefixes with a count of how many applications are registering that prefix.

##### Enterprise Approach

I haven’t had a chance to test this yet, but theoretically this script would work as a sensor in Tanium to scan an enterprise at scale and identify all URL handlers attempting to be registered by applications on endpoints. The benefit with the enterprise scale of scanning is the ability to stack these URL handlers across multiple endpoints and identify the less frequent handlers more likely to be used for this type of attack.

##### Important Note

This python script parses the application files themselves and does not query MacOS for the live handlers currently registered. The linked blog post gives the command to do that.

Find the script here: https://github.com/JamesHabben/HelpfulPython/blob/master/list- mac-app-urls.py

Let me know if you see any modifications or improvements to make this more helpful.

James Habben

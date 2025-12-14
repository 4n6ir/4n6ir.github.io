---
layout: post
title: "Show Your Timezone in X-Ways"
author: "James Habben"
tags: X-Ways
---

I posted earlier about how to enable EnCase to show the timezone for all of the timestamps that it displays. I wanted to follow that up with a post on how that can be accomplished with X-Ways Forensic (XWF) as well.

##### Showing Your Timezone

This is a pretty simple one. Don't do anything. The default setting already has the timezone offset displayed with times. Well, you have to do one small thing, and that is to expand the column. XWF has it displayed in a slightly greyed color and all you have to do is make the column wider to show it.

![xwf-time-display](/images/2018/04/xwf-time-display.png)

##### Setting Time Zones

I haven't done extensive testing on this, but it seems that XWF is similar to EnCase in that it takes the timezone setting of the machine your are running on to use inside the case.

To change that setting, use the 'Options' menu and select 'General Options'. In there, you will find a button at the button at the bottom of the window for 'Display Time zone...'. Click that.

![xwf-time-general](/images/2018/04/xwf-time-general.png)

Once you have that window open, choose your timezone and click OK and OK.

![xwf-time-timezone1](/images/2018/04/xwf-time-timezone1.png)

Edit: @jarlethorsen on Twitter gave me a couple more ways to change the timezone.

You can set the timezone with a right click at the top of the case tree in the Case Data window on the left of the screen. Choose the 'Properties...' option.

![xwf-time-click](/images/2018/04/xwf-time-click.png)

On that window, you have two options. Set the timezone for the entire case (orange arrow), or unlock the option (pink arrow) to set the timezone for each evidence file or even for each partition of each evidence file.

![xwf-time-case](/images/2018/04/xwf-time-case.png)

If you check the box, then you do another right click &gt; properties on each item you need to change the setting for and you will get this window.

![xwf-time-partition](/images/2018/04/xwf-time-partition.png)

Thanks for reading!

James Habben

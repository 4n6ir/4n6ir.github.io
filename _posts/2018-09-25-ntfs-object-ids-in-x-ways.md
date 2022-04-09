---
layout: post
title: "NTFS Object IDs in X-Ways"
author: "James Habben"
tags: X-Ways
---

Another post in the series of using commercial tools to access Object IDs, only this time switching over to X-Ways Forensic (XWF). I don’t think that it is any secret that EnCase has been my primary forensic tool suite for a really long time, but I like to have options and XWF is one I have available. This will be a pretty short post and mini-series, however, because it doesn’t seem that XWF does much with Object IDs other than simply display them.

##### Using XWF to View Object IDs

Open up a preview on a disk, and browse to a file or folder that you know has an Object ID assigned to it. Click on the ‘Details’ tab in the bottom pane and scroll all the way down. If you don’t have the viewers properly setup, then you have to configure those first.

![xwf-objid](/images/2018/09/xwf-objid.png)

##### Comparing XWF to EnCase

This section is the one that will bring the hater comments, but I will put it in anyways. Let me state here that I think XWF has a lot of great and unique features, many that EnCase doesn’t, and I like having it as an option for those times.

Let’s borrow the image from my previous EnCase post:

![encase-attr-objid-long](/images/2018/09/encase-attr-objid-long.png)

The Object ID shows the same value. The parsed values match up as well (0x2bb==699).

Always great to see validation across tools, not that this is a really hard problem.

The point that caught my attention was the missing ‘Birth’ IDs. EnCase shows three total values here, and I haven’t been able to locate either ‘Birth Volume ID’ or ‘Birth Object ID’ in XWF. Let me know if you know where these are located!

##### XWF Filtering on Object ID

One of the things I really like about XWF is the ability to add columns for many of the fields that we might want to quickly see and even filter on. When the column is available, it makes for extremely quick filtering by clicking on the funnel in the column header (much faster than creating a condition in EnCase). Unfortunately for us on this topic, there is no column available for the Object IDs.

![xwf-objid-columns](/images/2018/09/xwf-objid-columns.png)

XWF will show you the associated Object ID, but it doesn’t seem to give you the option to narrow down your file sets based on those having or not having Object IDs assigned. If you are following Dave’s quest, you know that these IDs get created as part of the link tracking system and there are numerous user actions that are associated with their existence. If you know the file you are looking for, then XWF will show you. If you are looking for any files that have the IDs, then you will have to use EnCase or one of the many open source options.

James Habben

---
layout: post
title: "NTFS Object IDs in EnCase"
author: "James Habben"
tags: EnCase
---

Over on the Hacking Exposed Computer Forensics blog, David Cowen has been posting up weekly challenges. I love that he is investing in the DFIR community (literally with $100 prizes).

He posted a challenge on September 9, 2018 for readers to develop a python script to parse the NTFS $ObjId:$O alternate data stream. He apparently didn’t get any takers since on September 15, 2018 he put up a short post stating exactly that.

##### Commercial Solution

I am all for Open Source and Free Software options in the DFIR community, and I also frequently contribute to that collection through my various GitHub repositories. I have also spent an insane amount of time working with EnCase in my years past, so I wanted to show the way to view the data related to Dave’s challenge in a tool that some of you might have available.

Don’t blink!

Here are the steps to see the Object IDs that are assigned to files in EnCase v7+:

1. Load your local preview or evidence file into the evidence tab
2. Click on the evidence name to have EnCase start parsing the file system
3. Find a file you know to have an Object ID
4. Click the Attributes tab in the view pane

Here is what that looks like:

![encase-attr-objid](/images/2018/09/encase-attr-objid.png)

You can also see that EnCase parses the GUID and displays the various components. Just expand the field, or hover the mouse over like this:

![encase-attr-objid-long](/images/2018/09/encase-attr-objid-long.png)

This was just a short post for now. Next one, I will show how to build a condition to narrow down the view to only those files having Object IDs assigned.

James Habben

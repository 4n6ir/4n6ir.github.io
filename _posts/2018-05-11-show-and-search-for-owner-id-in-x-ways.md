---
layout: post
title: "Show and Search for Owner ID in X-Ways"
author: "James Habben"
tags: X-Ways
---

I previously wrote about the digital forensic artifact left behind by a user creating a file on a Windows computer with NTFS. I also showed how to display the owner ID and search for all files owned by that ID. In this post, I am showing how to accomplish the same tasks in W-Ways Forensics (XWF). It is a much shorter post because it is much easier to do.

##### Owner ID in Details

The first way to see the Owner ID in XWF is by viewing in the Details tab in the bottom pane. Select a file and click on the details tab. This view is pretty similar to EnCase, but it only works if you have the separate viewer component enabled.

![xwf-details-owner](/images/2018/05/xwf-details-owner.png)

##### Owner ID in a Column

The next way is a nice add that EnCase cannot do. You can turn on the column to show the name or ID while browsing around in the folders and files. Just open the dialog for columns and type in a value. If you have accounts, such as domain users, that aren’t resolving, the column width will have to be a bit wider.

![xwf-col-setting](/images/2018/05/xwf-col-setting.png)

Once you give it a number, you can manually adjust the width to best show the values you have in this case.

![xwf-col-view](/images/2018/05/xwf-col-view.png)

##### Filtering on Owner ID

Since XWF shows the values in the column format, it is easy to filter on specific values. Click on the funnel icon and you get this window.

![xwf-filter-box](/images/2018/05/xwf-filter-box.png)

Set that User ID to the value you want, and do a recursive view of the file system. Boom!

James Habben

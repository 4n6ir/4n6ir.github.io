---
layout: post
title: "Show Your Timezone in EnCase"
author: "James Habben"
tags: EnCase
---

A question came up on my team about how to adjust time zones on evidence in EnCase. I figured I would put together a short post in case it might help others.

##### Setting Time Zones

When you start a case with EnCase, it grabs the timezone that is currently being used by the workstation you are running it on. All of the evidence that you bring into that case is assigned that same timezone.

You can apply a timezone change at a couple levels. First, directly to an evidence file. Second, to multiple evidence files. In EnCase v6, you open a case directly to whats called the 'entries' view. Entries are a generic name given to refer to any object inside an evidence file such as files, folders, alternate data streams, NTFS meta files, partitions, etc. even including the evidence file itself. Starting in EnCase v7 (and carried into v8), you are dropped in the 'evidence' view and must interact with that list in order to enter the 'entries' view. Whatever version you are using, go into the entries view.

To set the timezone, decide if you want an evidence specific or global change. Then right click on the evidence name or the 'entries' item at the top of the tree. Towards the bottom find the 'Device' sub-menu, then choose the 'Modify time zone settings...' option.

![encase-tz-rc](/images/2018/04/encase-tz-rc.png)

A small window will pop up to show the list of time zones that EnCase has available. If you are examining a computer that isn't properly patched with the current Daylight Saving Time setting, you can force that.

![encase-tz-window](/images/2018/04/encase-tz-window.png)

Click OK, and the times showing in EnCase will all be adjusted without having to do anything further.

##### Showing Your Timezone

I encourage everyone reading this to update this setting. Digital forensics requires us to be very accurate and specific. It tells EnCase to attach the timezone setting to every date that is displayed. It has saved me from a situation of reporting an incorrect time more than once. After changing this setting, your dates will look like these. I typically keep the columns smaller and only expanded the 'Last Accessed' field to show the full value.

![encase-tz-dates](/images/2018/04/encase-tz-dates.png)

To make this change, find the 'Tools' menu in the bar at the top, and choose the 'options' option. Then click on the 'Date' tab. Check the box at the top of that tab page.

![encase-tz-show](/images/2018/04/encase-tz-show.png)

Thanks for reading!

James Habben

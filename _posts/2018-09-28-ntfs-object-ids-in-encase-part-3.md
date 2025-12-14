---
layout: post
title: "NTFS Object IDs in EnCase - Part 3"
author: "James Habben"
tags: EnCase
---

In a previous post, I showed you how to make a condition to find all files in an NTFS volume that have Object IDs associated with them in NTFS. In this post, I will be showing you how to create a condition to search through the values of the Object IDs to filter on specific strings.

The condition I build below is designed to search for a provided value, and then remove that from the filtered list. The goal is to allow the examiner to use this to identify files that were created on computers with di!erence MAC addresses.

In the image below, I found 229 files that have Object IDs but don’t have my VM’s MAC. The previous condition found 442 total files on this disk with Object IDs. Many Windows files were in the list, but the EnCase.exe file jumped out. MAC address of the engineer that executed the build process?

![encase-objid-c2-result](/images/2018/09/encase-objid-c2-result.png)

##### Using EnCase Conditions to Search Inside Object IDs

Similar to the last condition, this requires the mini-filter feature inside the condition to dig inside the attributes for each file. Here is how to build that condition:

1. Find the conditions pane in the bottom right corner and click on the ‘user’ folder. Use the ‘new’ option in the toolbar or mouse right click to open the condition dialog. I created a folder to organize a bit. EnCase throws an error if you try to create a new condition in the ‘default’ folder.
2. Click on the ‘filters’ tab and then double click on the ‘AttributeValueRoot’ item in the list.

![encase-objid-c1-filters](/images/2018/09/encase-objid-c1-filters.png)

3. Five things to do in this window. We need to give this a unique name since it will show up in the property list later. I chose to use the FullPath property to reduce false positives over using a name only check. The path I used is based on what EnCase displays when viewing in the attributes tab of the details pane.

	1. Name mini-filter ‘zFindObjectId’
	2. Use ‘new’, choose ‘fullpath’, choose ‘find’, type ‘object identifiers\own id’ in the value
	3. Use ‘new’, choose ‘value’, choose ‘Find’, type ‘NOT value’, and check ‘prompt for value’
	4. Right click on ‘Value find [NOT value]’ and choose the ‘Not option’
	5. Right click on the ‘Main’ item at the top of the tree and use ‘change logic’ to flip the ‘or’ to ‘and’
	6. Click ‘ok’

![encase-objid-c2-filter-terms](/images/2018/09/encase-objid-c2-filter-terms.png)

4. Back on the main condition window, click on the conditions tab.
	1. Use the ‘new’ option
	2. Scroll to the bottom of the list and click on ‘zHasObjId’
	3. Click on ‘has a value’
	4. Click ‘ok’

![encase-objid-c2-term](/images/2018/09/encase-objid-c2-term.png)
	
5. Name your condition and click ‘ok’

![encase-objid-c2-final](/images/2018/09/encase-objid-c2-final.png)

Let me know if you find other uses to search for using this condition. I would love to read about it.

James Habben

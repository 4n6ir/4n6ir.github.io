---
layout: post
title: "Blocks in Practical Use"
author: "James Habben"
tags: Malware OSINT
---

Last week, John Lukach put up a post about how to use some tools to find pieces of files left behind after being deleted, and even partially overwritten. I wanted to put together a short post to give a practical use of this technique for a recent case of mine.

##### The Case

The request came in as a result of an anomaly detected in traffic patterns. It looked like a user had uploaded a significant amount of data to a specific cloud storage website. My client identified the suspected machine, and got an image to preserve the data. Unfortunately, this anomaly wasn't caught right away, so a couple months passed before action was taken. They took a pass on the image with some forensic tools, but they weren't able to identify anything relating to this detection, so they asked me for a second look.

##### My Approach

I started off with a standard pass of the forensic tools. Not because I don't trust their team, but because tool versions can have an effect on the artifacts that get extracted and I wanted to be sure of my findings. Besides, if you don't charge for machine time, then this really doesn't add much in the way of cost in the end. This process did two things for me. The first thing I got from it was that I could not find any artifacts related to the designated website. The second thing was that the history and cache looked very normal. In other words, it didn't look like the user had cleared any history or cache in an effort to clean up after themselves.

Of course, I am now thinking that they could have done a targeted cleanup and deleted very specific items from their browser. I get some reassurance that this was not the case by usage of some of the deep scan options available in the forensic tools that I used. These tools will scrounge through unallocated clusters, on command, in search of data patterns that are potential matches for deleted and lost internet artifacts. I found none, again, in this more extensive search.

Let's put on our tin foil hats now, and really go for it. The user could have wiped and deleted individual files. Then gone into the history and cache lists to remove the pointers of those files. This would take care of the files that are still currently allocated. What about all those files that get cataloged in the cache, but quickly discarded because the server instructed this by sending some cache control headers? These files would have been lost to unallocated clusters at some point before the user thought about their cleanup actions. The only way to cover those track would be to use a tool that can do wiping of unallocated clusters. If the user went to this extent, dare I say they deserve to get away with it?

##### What If?

My client had a secondary thought in the event that we were not able to find traces of user action involved with this detection. They wanted to see if the system was infected with some malware that would have generated this traffic. I did the standard AV scans with multiple vendors and didn't find anything. I followed that up with a review of the various registry locations that allow for malware to persist and autostart.

Verizon has a great intel team, and I asked them for information on possible campaigns involving the designated website. They gave me some great leads and IOCs to search for, but they did not pan out in this case. What a great resource to have!

After reviewing all of this, I was not able to find any indications of a malware infection, much less one that was capable of performing the suspected actions.

##### Points to Disprove

Here is the point where block hashing can make a real difference. I will show you how I used block hashing to disprove (as much as the available data will allow) three different points.

- Lost Files - User deleted individual history and cache entries to cleanup their tracks and the file system lost track of the related files
- Unallocated - User went psycho-nutjob-crazy and wiped unallocated clusters after deleting records
- Malware - User wasn't involved and malware done the dirty deed

##### Disprove Lost Files

In order to prove the user did not delete these selective entries from the browser history and cache, I need to find the files on disk, most likely in unallocated clusters. The file system no longer has metadata about them, so the forensic tools will not discover them as deleted or orphaned files. I could carve files from unallocated clusters using the known headers of various file types, but this is a very broad approach. I want to identify specific files related to this website, not discover any and all pictures from unallocated.

I start by using HTTrack to crawl the designated website. I want to pull as many files down as I can. These are all the files that a browser would see, and download, during normal interactions with the website. I got a collection of a few hundred files of various types: JPEG, GIF, SWF, HTML, etc.

The next step is to split and block hash these files. On this step, I used EnCase and the File Block Hash Map Analysis (FBHMA) Enscript written by Simon Key of EnCase Training. FBHMA has the ability to do both sides of the block hashing technique and presents an awesome graphic for partially located files. I applied it against my collected files, and then applied those hashes against the rest of the disk. The result was zero matches.

This technique is not affected by the amount of fragmentation or the amount of overwriting, as long as there are some pieces left behind. The only way to beat this technique is to completely overwrite the entire set of blocks for all of the files in question. A very unlikely scenario in such short time without a deliberate action.

##### Disprove Unallocated

This point takes a little more work to disprove, and it might be subject to your own interpretation. The idea here is that files have been lost to unallocated clusters without control. The user was not able to do a targeted wipe of the file contents before deletion, so the entire area of unallocated clusters would have to be wiped to assure cleanliness.

The user could make a pass with a tool that overwrites every cluster with 0x00 data. This is enough to clear the data from forensic tools, but it can leave behind a pretty suspicious trail. If you used block hashing against this, it would quickly become apparent that the area was zeroed out. Bulk Extractor provides some entropy calculations that make quick work of this scenario.

The other option, and more likely to be used, is to have the wipe tool write random data to the cluster. Most casual and even 'advanced' computer users picture common files like zip and JPEG to be a messy clump of random data thrown together in some magic way that draws pictures or spells words. In DFIR, we learn early on that seemingly random data is not actually random. There are recognizable structures in most files, and unless the file involves some type of compression, it is not truly random when measured in terms of entropy. My point in all of this is that a wiping pass that writes random data would cause every cluster of unallocated to show high entropy values. This would raise eyebrows because this is not normal behavior. Some blocks contain compression or encryption and would show high entropy, but others are plain text which registers rather low on the entropy scale.

So with a single pass of Bulk Extractor and having it calculate hashes and entropy, I was able to determine that 1) unallocated clusters was not zeroed out and 2) unallocated clusters was not completely and truly random. It, again, looked very normal.

##### Disprove Malware

The last point to disprove was the existence of malware on the system. I already established that there was no indications showing of allocated malware, but I can't ignore the possibility of there having been some malware installed which later got deleted or uninstalled, for some reason. Again, without a full and controlled wiping of this data off the drive, it would leave artifacts in unallocated clusters for me to find with block hashing.

This step is a much larger undertaking. It's because I am employing the full 20+ million samples that are generously shared by VirusShare.com and users. I didn't to do all of the work, though, because John has done that for us all. He has a GitHub repo with all the block hashes from VirusShare. Just be sure to remove the NSRL block hashes since those malware authors can be quite lazy in copying code from other executable files. It's a beast to get up and running, but well worth the effort. Maintaining it is much easier after it's built.

Now I have a huge data set of known malware, adware, and spyware sample files, and various payload transports like DOC and PDF files. If there was anything bad on this system, my data set is very likely to find it. The result was no samples identifying more than a single block on the disk. You have to allow for a threshold of matches with this size of sample data. A single matching block from a possible 1000 blocks for sample file of 500kb is not interesting.

##### Value of Blocks

This turned out to be a rather long post, but oh well. I hope this helps you to see the value in the various techniques of applying block level analysis in your cases. The incoming data in our cases is only getting larger, and we need to be smart about how we analyze it. Block hashing is just one way of letting our machines do the heavy work. Don't be afraid to let your machine burn for a while.

You may be thinking to yourself that this is not a perfect process when it came to the malware point, and I may agree with you. We are advancing our tools, but we need to advance our understanding and interpretation of the results as well. For now, we handle that interpretation as investigators but the tools need to catch up. Harlan recently posted about this as a reflection on OSDFcon and it's worth a read and consideration.

Happy Hunting!

James Habben

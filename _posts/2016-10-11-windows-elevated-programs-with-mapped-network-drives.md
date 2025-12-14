---
layout: post
title: "Windows Elevated Programs with Mapped Network Drives"
author: "James Habben"
tags: Windows
---

This post is about a lesson that seems to be one that just won’t sink into my own head. I run into the issue time and time again, but I can’t seem to cement it in to prevent the issue from coming up again. I am hoping that writing this post will help you all too, but mostly this is an attempt to really nail it into my own memory. Thanks for the ride along! It involves the Windows feature called User Access Control (UAC) and mapped network drives.

Microsoft changed the behavior of security involving programs that require elevated privileges to run properly. Some of you may be already thinking about why I haven’t just disabled the whole UAC entirely, and I can understand that thought. I have done this on some of my machines, but I keep others with UAC at the default level for a couple of reasons. 1) It does provide an additional level of security for machines that interface with the internet. 2) I do development with a number of different scripts and languages and it is helpful to have a machine with default UAC to run tests against to ensure that my scripts and programs will behave as intended.

One of those programs that I use occasionally is EnCase. You can create a case and then drop-and-drop an evidence file into the window. When you try this from a network share, however, you get an error message stating that the path is not accessible. The cause of this has to do with Windows holding different login tokens open for each mode of your user session. When you click that ‘yes’ button to allow a program to run with the elevated privileges, you have essentially done a logout and login under a completely different user. That part is just automated in the background for user convenience so you don't have to actually perform the logout.

Microsoft has a solution that involves opening an elevated command prompt to use ‘net use’ to perform a drive mapping under the elevated token, but there is another way to avoid this that makes things a little more usable. It just involves a bit of registry mumbo jumbo to apply the magic.

You can see in the following non-elevated command prompt that I have a mapped drive inside of my VM machine that exposes my shared folders.

![d1b31-cmd-net-use](/images/2016/10/d1b31-cmd-net-use.png)

Now in this elevated command prompt, you will find the lack of a mapped drive. Again, this is a shared folder through VMware Fusion, but the same applies for any mapped drive you might encounter.

![472cb-cmd-priv-net-use](/images/2016/10/472cb-cmd-priv-net-use.png)

The registry path that unlocks easy mode is in the following location:

- HKeyLocalMachine\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\EnableLinkedConnections

Give that reg value a DWORD value of 0x1 and your mapped network drives will now show up in the elevated programs just the same as the non-elevated programs.

![55349-cmd-priv-reg](/images/2016/10/55349-cmd-priv-reg.png)

Here is the easy way to make this change. Run the following command at the command prompt:

- reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v EnableLinkedConnections /t reg_dword /d 1

Then you can run the following command to confirm the addition of the reg value:

- reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System

Mostly I am hoping that this helps me to remember this without having to spend time consulting Aunti Google, but I also hope this might give you some help as well.

James Habben

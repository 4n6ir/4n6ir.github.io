---
layout: post
title: "Building Python Packages, By a Novice"
author: "James Habben"
tags: Python
---

I am excited to see that Evolve has been getting some use by more and more people. It has gained enough use and attention to even get the attention of SANS. They want to include Evolve in their SIFT workstation build. This is by no means an endorsement by SANS, but it means a lot to an open source developer to know that their tools are being used and helpful.

The requirement of Evolve making it into SIFT is that it needs to be installed from the Python Package Index (PyPI). This is a very reasonable requirement since it makes the maintenance of SIFT a much more reasonable project. A project, may I add, that is also available for free and maintained in the free time of the volunteers. Thanks!

I started reading about Python packages and distribution, and found that it is very capable and very flexible, almost too much. It has been a challenge for me to squeeze in the reading and testing between the demands of my full time job, but I finally stumbled my way through it to a final version. There are many tutorials available that explain the basics of Python packages, but I had a little different problem than what most Python projects face, I guess. I wanted to write this all down to share my experience for any of you that might benefit.

##### El Problemo

First of all, I am not a full time developer, nor am I a master of Python. I think this may be the root of my problem! I started Python, like many others, with assembling pieces of others scripts to make a solution to the problem I was facing at the time. Because of my background with so many other languages, it was a fairly short phase of learn the environment and intricacies of Python before I was able to start from scratch and StackOverflow my way through. I took on Evolve as a way to expand further, and to solve another problem. That is the mother of invention, after all. It has been a fun and rewarding experience, with many thanks to all of you supporting it!

Put me aside now, and let’s talk about the technical problem with packaging Evolve. A typical package available from PyPI is python code. You get a little more exotic when you find the author including some code written in C or C++ to make for a more efficient function. This code requires compiling, but the PyPI can handle it, and does it well (except for on Windows). Where Evolve presented the problem is in the HTML, CSS, JS, and images used in the web interface. These aren't considered code by the Python packager, so it was a challenge for me to get them included.

##### Basic Building

I don’t want to rehash the basic build process since there are already many very well written tutorials out there to explain that. Instead, I will include a short list of some links that were helpful during my journey here.

##### Creating the setup.py file to start it all off

https://docs.python.org/2/distutils/setupscript.html

This tutorial was very helpful in building the basics of the setup.py file. It explains most of the properties well. The part that I found lacking was in the sections for including extra non-python code in the package.

##### The trouble with including non-python files

http://blog.codekills.net/2011/07/15/lies,-more-lies-and-python-packaging-documentation-on--package_data-

C/O

http://stackoverflow.com/questions/7522250/how-to-include-package-data-with-setuptools-distribute

This was a great help in truly understanding what I thought I understood after reading the Python docs. It also helped keep me sane and moving forward!

##### Including some other files

http://stackoverflow.com/questions/9654694/where-are-package-data-files

This helped me somewhat. I was able to get the folder of HTML files included, but it was a rather manual process. I knew I could fall back on this, but I figured that there must be a better way. We are doing programmer things, after all.

##### More on including other files

https://wiki.python.org/moin/Distutils/Tutorial

Another good tutorial on the packaging process, but it didn’t fully register with what I needed.

##### Yet another on the process of building

https://www.digitalocean.com/community/tutorials/how-to-package-and-distribute-python-applications

This is the article that finally made things fit together. In fairness to the others, I think it just took some time to sink in.

##### Highlighted Points

Here are some points I thought I would share. Some of these may be obvious to you already, but I had trouble getting a full grasp of the exact requirements. These are not listed in any particular order of importance.

##### sdist and bdist use different properties

As stated in one of the articles, there are different ways to package your project. You can distribute the source code with sdist, or you can build it into a binary with bdist. Each of these methods uses different properties from inside the setup.py file. Be aware of which method you are using to distribute, and which properties are associated with each.

##### __init__.py is a critical file, even if it’s blank

In building the Morph feature of Evolve, I found that I had to have an __init__.py file in the morphs directory for proper Python function. You can look at both of these files that are in the project (project root and the morphs folder), and you will see they are both essentially blank. There is function behind having them, and they provide more function by placing code inside. I don’t need that function though, so they remain empty. In the process of making this package, I found that the __init__.py file is needed for the build process to recognize that the folder includes other Python files of code that need to be included in the package.

I am using classes in Evolve, but only for the Morphs. It’s something I want to address in the future, but moving the main code into classes will require time in refactoring and testing that I just don’t have quite yet.

##### MANIFEST is not MANIFEST.in

Bonehead move on my part, but this was the final blocker to me getting this venture to work. I read through many articles talking about modifying the MANIFEST.in file to search for other files to include. I made the bad assumption that MANIFEST was the file being specified. WRONG. The MANIFEST.in file is a template that is used during the build process. The MANIFEST file is written by the build process, as a log of the files included in the distribution package. To make me look even dumber, the first line in the file says ‘# file GENERATED by distutils, do NOT edit’…

##### Use distutils instead of setuptools

There are a few shortcomings in setuptools that I read about at the start of this project that were addressed in distutils. In typical Python and open source fashion, a problematic library was fixed, but moved to be named differently. Rants aside, just use the newer distutils and things will be much smoother.

##### That’s It

Again, enough tutorials out there already to explain this process. They do a pretty good job, but I ran into troubles including the extra files in Evolve. I hope this helps one of you to not pull your hair out when trying to build your Python package. Share any other tips you have below in the comments!

James Habben

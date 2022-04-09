---
layout: post
title: "Evolve Version 1.6"
author: "James Habben"
tags: Volatility
---

![evolve-logo](/images/2017/11/evolve-logo.png)

New features in #EvolveTool!

##### API Doc

The new part of this feature is the HTML to outline all the various URLs that can be used to interact with Evolve. They have mostly been there in the background already, although a couple of the URLs are new. These URLs give the ability to have Evolve work in a sort of headless mode. You can use any scripting language that can GET or POST. The return data is in JSON format.

![evolve-api](/images/2017/11/evolve-api.png)

##### Plugin Search

The plugin list for Volatility commands keeps growing thanks to the great support by the core dev team and all the gracious developers in the community. I’m sure the annual contest giving away money had no part in it either. Anyways, I figured it would be helpful to have the ability to run a quick search over the plugin list where you can type a part of what you are looking for. It doesn’t support any fancy matching though, and just puts wildcards in front and behind whatever you type. It searches while you type and you can use [ESC] or click the X to the right to quickly clear the box.

Try typing ‘dump’ and you will get a list of those plugins that Evolve doesn’t yet support:

![evolve-plugin-search](/images/2017/11/evolve-plugin-search.png)

##### Teaser: Volatility Command Line Options

Speaking of Volatility plugins that aren’t supported in Evolve, I was able to dig into the Volatility core and determine where those options are stored in Object Oriented (OO) data structures. You will see a couple new URLs listed in the API doc that take advantage of this new found knowledge.

The first is a list of all the default options that Volatility has. You can see those by running ‘<strong>vol.py -h</strong>’ in the shell, or accessing the API here:

![evolve-options](/images/2017/11/evolve-options.png)

The second builds on the above URL to get more specific options that any of the plugins are allowed to add into the list to accept during processing. You can display the full collection of options with the plugin specifics listed at the bottom with ‘<strong>vol.py dumpregistry -h</strong>’ or you can get only the specific options that each plugin adds by accessing this API:

![evolve-options-plugin](/images/2017/11/evolve-options-plugin.png)

##### Some Background

I originally took on the project of making Evolve to learn Python. I wanted to build something that required research and learning, and something that would make me stretch. I could have written this project in any language and just made calls to vol.py to get things running. I’ve seen many of these projects pop up over the years and they work great. I decided to fully integrate with Volatility to better learn Python and have more power and control over how I hand off processing jobs. That decision has caused some headaches, so I try to share the solutions when I can.

The challenge in here is that Volatility uses a library for parsing command line options that is built into Python. This setup works great for the scenario that Volatility is typically run, at a command prompt, where the user has to supply all those parameter names and values up front. It doesn’t make it so easy to fetch a list of the various options any of the plugins might want to take advantage of because those options aren’t built into OO to just get.

The plugins written for Volatility interface with optparse to add in the recognition for the short and long parameter designations. The optparse object is a member of Volatility's ConfObject class, but not really integrated.

To get to the list of default options is pretty straight forward. You have to build a ConfObject anyways when integrating with Volatility, and the default options all come along as it is built.

To get the options that any of the plugins add on top of the default, you have to utilize the ConfObject again, but as a parameter when initiating the plugin of choice. The result is a full list of all the options that are now available, including those earlier found default options. You have to do the work of differentiating the newly added from the defaults. To prepare for doing this, I created a second ConfObject and pulled the new list in.

The next challenge is the structure of the options being held in the optparse object isn’t really straight forward. The items are not provided in a list, so you can’t do much with them as they sit. Fortunately they are iterable, so that allows for Python to use in as a collection in a for loop. You can see the debug view getting to one of these options here:

![evolve-options-debug](/images/2017/11/evolve-options-debug.png)

I chose not to deal with all of those properties since I don’t think the Volatility plugins have that much ability to manipulate, but I will doing some further testing on this. If there are more properties that are needed in Evolve, they are fairly simple to add into the JSON return at this point.

After grabbing the handful of properties into a dictionary, I stuff that dictionary into a tuple. You can read more about the differences since I won’t go into that here. The tuple made it easier to work with, and I don’t have the need to change that object.

With a tuple of options from two ConfObjects, I could now determine which of those options were added by the provided plugin. Now I had to repeat that process for every plugin available in Volatility, and I am very thankful for loops and automation.

Check it out on GitHub.

https://github.com/JamesHabben/evolve

I hope you find these new features helpful, and the upcoming features exciting. Please reach out if you have any questions or feature suggestions for Evolve.

James Habben

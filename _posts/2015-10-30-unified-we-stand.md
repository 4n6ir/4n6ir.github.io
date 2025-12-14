---
layout: post
title: "Unified We Stand"
author: "James Habben"
tags: Volatility
---

Big news happened at #OSDFcon this week. Volatility version 2.5 was dropped. There are quite a number of features that you can read about, but I wanted to take a few minutes to talk about one feature in particular. There have been a number of output options in the past versions of Volatility, but this release makes the different outputs so much easier to work with. The feature is called Unified Output.

This post is not intended to be a ‘How To’ of creating a Volatility plugin. Maybe another day. I just wanted to show the ease of using the unified output in these plugins. If you are feeling like taking on a challenge, take a look through the existing plugins and find out which of them do not yet use the unified output. Give yourself a task to jump into open source development and contribute to a project that has likely helped you to solve some of your cases!

Let me give you a quick rundown of a basic plugin for Volatility

##### Skeleton in the Plugin

The framework does all the hard work of mapping out the address space, so the code in the plugin has an easier job of discovery and breakdown of the targeted artifacts. It is similar to writing a script to parse data from a PDF file verses having to write code into your script that reads the MBR, VBR, $MFT, etc.

To make a plugin, you have to follow a few structural rules. You can file more details in this document, but here is a quick rundown.

https://code.google.com/p/volatility/wiki/PluginInterface22

- You need to create a class that inherits from the base plugin class. This gives your plugin structure that Volatility knows about. It molds it into a shape that fits into the framework.
- You need a function called calculate. This is the main function that the framework is going to call. You can certainly create many more functions and name them however you wish, but Volatility is not going to call them since it won’t even know about them.
- You need to generate output.

Number 3 above is where the big change is for version 2.5. In the past versions, you would have to build a function for each format of output.

For example, you would have a render_text to have the results of your plugin output basic text to stdout (console). Here is the from the iehistory.py plugin file. The formatting of the data has to be handled by the code in the plugin.

https://github.com/volatilityfoundation/volatility/blob/master/volatility/plugins/iehistory.py#L187

![fa598-img5](/images/2015/10/fa598-img5.png)

If you want to allow to CSV output from that same plugin, then you have to create another function that formats the output into that format. Again, the formatting has to be handled in the plugin code.

https://github.com/volatilityfoundation/volatility/blob/master/volatility/plugins/iehistory.py#L204

![48466-img10](/images/2015/10/48466-img10.png)

For any other format, such as JSON or SQLite, you would have to create a function with code to handle each one.

##### Output without the Work

With the unified output, you define the column headers and then fill the columns with values. Similar to creating a database table, and then filling the rows with data. The framework then knows how to translate this data into each of the output formats that it supports. You can find the official list on the wiki, but I will reprint the table for a quick glance while you are reading here.

https://github.com/volatilityfoundation/volatility/wiki/Unified-Output#standard-renderers

![e6af5-img15](/images/2015/10/e6af5-img15.png)

There is a requirement in using this output format and it is in the similar fashion to building a plugin in the first place.

- You need to have a function called unified_output which defines the columns
- You need to have a function called generator which fills the rows with data

##### Work the Frame

The first step in using the unified output is setting up your columns by naming the headers. Here is the unified_output function from the same iehistory.py plugin.

https://github.com/volatilityfoundation/volatility/blob/master/volatility/plugins/iehistory.py#L132

![7b646-img20](/images/2015/10/7b646-img20.png)

Then you define a function to fill each of those columns with the data for each record that you have discovered. There is no requirement on how you fill these columns, they just need the data.

![48a71-img25](/images/2015/10/48a71-img25.png)

The other benefit from this unified output is that a new output format can be easily added. You can see the existing modules, and add to it by writing code of your own. How about a MySql dump file format? Again, dig in and do some open source dev work!

https://github.com/volatilityfoundation/volatility/tree/master/volatility/renderers

##### Experience the Difference

Allow me to pick on the guys that won 1st place in the recent 2015 Volatility plugin contest for a minute. Especially since I got 2nd place behind them. Nope, I am not bitter… All in fun! They did some great research and made a great plugin.

https://github.com/volatilityfoundation/community/tree/master/ShimcacheMemory

When you run their plugin against a memory image, you will get the default output to stdout.

![cdae6-img30](/images/2015/10/cdae6-img30.png)

If you try to change that output format to something like JSON, you will get an error message.

![74fdf-img35](/images/2015/10/74fdf-img35.png)

The reason for this is because they used the previous version rendering. The nice part is if they change the code to add JSON output, the unified output would also support SQLite and XLS or any other rendering format provided by the framework. Thanks to the Fireye guys for being good sports!

Now, I will use one of the standard plugins to display a couple different formats. PSList gives us a basic list of all the processes running on the computer at the time the memory image was acquired.

Here is the standard text output.

![26299-img40](/images/2015/10/26299-img40.png)

Here is JSON output. I added the --output=json to change it. It doesn't look that great in the console, but it would be great in a file to import into some other tool.

![8f65f-img45](/images/2015/10/8f65f-img45.png)

Here is HTML output. Again, the change in with --output=html.

![75554-img50](/images/2015/10/75554-img50.png)

##### Hear My Plea

Allow me to get a shameless plug for my own project now. Evolve is a web based front end GUI that I created to interface with Volatility. In order for it to work with the plugins, they have to support SQLite output. The easiest way of supporting SQLite is to use the new unified output feature. The best part is that it works for a ton of other functions as well, with all the different formats that are supported.

https://github.com/JamesHabben/evolve

If you have written, or are writing, a plugin for Volatility, make it loads better by using the unified format. We have to rely on automation with the amount of data that we get in our cases today, so let’s all do our part!

Thanks to the Volatility team for all of their hard work in the past, now, and in the future to come. It is hard to support a framework like this without being a for-profit organization. We all appreciate it!

James

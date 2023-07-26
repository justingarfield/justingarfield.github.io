---
layout: post
title: BrowserSync – Part 1 - Installation, Setup, and the CLI
date: 2015-02-18 00:00:00 -0500
categories: 
tags:
excerpt: BrowserSync – Part 1 - Installation, Setup, and the CLI
---

{% capture warning_note %}
<p><strong>This is an archived and/or unfinished post from an old blog that I carried forward just to keep the information around.</strong></p>
{% endcapture %}
{% include archive-unfinished-bubble.html content=warning_note %}

# What is BrowserSync?
According to its website: “BrowserSync makes your tweaking and testing faster by synchronising file changes and interactions across multiple devices. It’s wicked-fast and totally free.”

# What does that mean for me?
When developing a new site, you often have to target multiple browsers, and now even devices. Pressing refresh and performing other actions across all of these environments can become quite tedious and consume a lot of development time. BrowserSync allows you to overcome this burden and have every environment update when you save changes to your design or perform other actions like filling out a form or scrolling. I highly recommend watching the quick 1-2 minute video on the [library’s website](http://www.browsersync.io/) to clarify what this means.

# Installing BrowserSync
Node.js + npm is the easiest way to install and get up-and-running with BrowserSync. However, you can also include it in your JavaScript project(s) and call its API directly (more on that later). If you’re unsure what Node.js or npm are, I highly recommend visiting http://nodejs.org/about/ and https://www.npmjs.com/about respectively. If you don’t currently have node.js installed you can grab an official installer for your platform located at http://nodejs.org/download/. To install BrowserSync using npm (once node.js is installed), open a Terminal / Command Prompt and type the following: npm install -g browser-sync

# A Quick Example
Create a new folder somewhere and place a blank text-file named index.html inside of it. Place the following code inside of the newly created index.html file:

```html
<html>
    <head>
        <title>BrowserSync Demo</title>
    </head>
    <body>
        <h1>BrowserSync Demo</h1>
    </body>
</html>
```

Now navigate to a Command Prompt or Terminal to the folder where you saved the index.html file, and assuming you’ve installed node.js + BrowserSync at this point, type:

```bash
browser-sync start --server --files *.html
```

You should then get output similar to this, confirming that BrowserSync is serving your static index.html file (and any other files in the directory)

```bash
[BS] Access URLs:
 --------------------------------------
       Local: http://localhost:3000
    External: http://192.168.12.10:3000
 --------------------------------------
          UI: http://localhost:3001
 UI External: http://192.168.12.10:3001
 --------------------------------------
[BS] Serving files from: ./
[BS] Watching files...
```

Now navigate to http://localhost:3000 in a browser and you should see the HTML file saying “BrowserSync Demo”. Keep your browser open and then edit and save the index.html with some new contents. You should see it display a message in the browser indicating a change is syncing. You’ll then see the change(s) made in the index.html in the browser. You should also see BrowserSync detect the change in the console like so…

```bash
[BS] File changed: F:\Development\browser-sync-setup-and-configuration\index.html
```

Now open a second browser and try again. Notice that when you make changes this time that BOTH browser windows get the update? Pretty nifty huh? No more pressing refresh anymore! Now let’s kick it up a notch…grab a tablet or smart-phone if you have one, make sure it’s connected to your network via WiFi (so it can see the machine running BrowserSync) and navigate the browser on it to the “External” address listed in the BrowserSync console. In this case it’s my local IP Address of this machine on port 3000 which is http://192.168.12.10:3000 Now make another change to the index.html. Notice that even your mobile device updates too!

# Why using -–files=”*” isn’t a good idea
It may be tempting to simply use the following to test with BrowserSync…

```bash
browser-sync start --server --files="*"
```

This will certainly detect any changes in more types of files than simply .html, including any .css or .js files you may add, but it will also detect any other types of files you add, even if you create a blank text file. This can lead to ludicrous amounts useless synchronizations and is not recommended. You will notice this occurring if you watch the console window and see entries like this…

```bash
[BS] File changed: F:\Development\browser-sync-setup-and-configuration\New Text Document.txt
```

# Some Useful Options
When using BrowserSync from the command-line, you can specify additional options to have it use when launching, a few of the options I’ve found useful are listed here, but more can be found by either visiting the BrowserSync Command Line Usage page or by running the BrowserSync like so…

```bash
browser-sync --help
```

## –-files
This will probably be your most important option being passed to BrowserSync. Without a list of files, BrowserSync doesn’t know what it should watch for changes, and therefor just sits a static web-server without it. You can feel free to use wildcards with this option, as it’s usually easier. A good starting point that will monitor the current directory for .html files, a css directory for .css files, and a js directory for .js files would be:

```bash
browser-sync start --server --files="js/*.js, css/*.css, *.html"
```

If you want BrowserSync to monitor all sub-folders for .html files, including the current directory, you could use something like:

```bash
browser-sync start --server --files="**/*.html"
```

If you want BrowserSync to monitor a folder named js and all of its sub-folders for .js files, including the current directory, you could use something like:

```bash
browser-sync start --server --files="js/**/*.js"
```

## –-no-open
If you already have a bunch of web browsers that were pointed to your server or wish to simply not have BrowserSync launch your default browser when it starts, include the –no-open option like so:

```bash
browser-sync start --server --files="*.html" --no-open
```

## –-index
If you’re using the auto-launch web browser function, then you may want to specify a different index page to use when loading so you don’t have to navigate anywhere. This can be done with the –index option like so:

```bash
browser-sync start --server --files="*.html" --index="AnotherIndexPage.html";
```

## –-port
If you’re having issues with the default port that BrowserSync chooses, you can specify the –port option like so:

```bash
browser-sync start --server --files="*.html" --port=6010
```

# What’s next?
This has been Part 1 of a multi-part series I’ll be posting on BrowserSync. In the next article, we’ll take a look at the control panel that comes with BrowserSync and see what some of its more advanced features are.

# Feedback
If you found this article helpful, confusing, misleading, requiring more information, etc…PLEASE leave constructive feedback so I can correct the article. With so many libraries popping up daily and new language features, I’m bound to get things wrong at some point!
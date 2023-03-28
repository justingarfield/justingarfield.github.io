---
layout: post
title: Internet Explorer and the mysterious @import issue resolved...Maybe?
date: 2008-06-30 00:00:00 -0500
categories: Development
tags: \@import, css, ie, internet explorer, invalid argument, missing styles
---
Well, I spent quite a bit of time trying to figure out what was going on with IE this time…I use Dojo 1.1 for the application I’m building at work, and with their theming system for Dijit, they have a tundra.commented.css file, which includes each individual widget’s corresponding CSS file via an @import statement.

Well, I thought, “Hey, this is pretty cool and easy to manage, so I’ll do it this way for my widgets as well for development purposes (we compile our CSS into separate, compressed files, afterward)…Later on that day, QA came to me and said “Hey, this thing here isn’t showing up!” and “I can’t see this dialog anymore, do you know where it went?!” Sure enough, I went over to look at my QA colleagues’ screen, and things looked all mangled and wrong…But only in IE! What the heck could be going on?!

Come to find out, after using the awesome IE Development Toolbar (yes, I’m being sarcastic), the styles weren’t even loaded into the browser!! Well, WTF?! So I cleared my cache, deleted all cookies, etc., just to make damn sure nothing funky there was happening…(refresh)…Same thing, again.

After a while of fussing with style sheets, checking other browsers, getting the generic -218760-whatever error error code, etc. I finally decided to include them all via <link> tags. Now a new error, only, a run-time error this time! “Invalid Argument.” and then..BAM! a second error! “There is not enough free memory to perform this operation.” Again…WTF?!

So, at this point, I was desperate and decided to try including them via JavaScript. What’s that syntax again? document.createStylesheet? Hmmm…Better go look it up…”Whoa, what in the name is this?! [You can only create 31 stylesheets with document.createStylesheet, but yet can add as many as you want if you do a document.createElement(“STYLE”) and append it to the DOM?!](http://msdn.microsoft.com/en-us/library/ms531194(VS.85).aspx) Hmmmmm….”

So now, I was curious, and imported only 31 stylesheets for widgets that I knew would show right away…Sure enough, everything I specified, show up perfectly! Then, to take it a step further, I decided to split out the master CSS file with all of the @imports, into 2 files, and then import those…Amazingly, it worked!

So there you have it, the answer to fixing the extremely ridiculous, and hardly documented, IE CSS @import issue.

Lessons learned

IE seems to support only 31 @import statements per CSS-file, <link></link> tags on a given page, or creations of stylesheets via document.createStylesheet
You may noticed styles start getting ‘lost’ or ‘messed-up’, or you may get the errors “Invalid argument.” or “There is not enough free memory to perform this operation.” when this limit is reached.
To get around this limitation…
Split your @imports into 2 or more files, and then load those files.
Use document.createElement(“style”) statements and append those items to the HEAD element
IE most likely uses the same internal methods for @imports and/or <link></link> tags, that document.createStylesheet uses, due to this 31-limit.
Resources

`createStyleSheet` Method – [http://msdn.microsoft.com/en-us/library/ms531194(VS.85).aspx](http://msdn.microsoft.com/en-us/library/ms531194(VS.85).aspx)

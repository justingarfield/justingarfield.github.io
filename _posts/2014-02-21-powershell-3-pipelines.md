---
layout: post
title: PowerShell 3 - Pipelines
date: 2014-02-21 00:00:00 -0500
categories: 
tags:
---
# Who is this article for?
This is a pretty in-depth article about Pipelines in PowerShell. If you’re unfamiliar with writing PowerShell scripts, .NET classes, and/or Development in-general, this article may end up being confusing for you and I’d recommend starting with something a little more basic first.

# What does this article cover?
* What is a Pipeline?
* PowerShell is not a Pipeline
* Where does a Pipeline sit in the PS environment?
* Pipeline classes provided by .NET

# What is a Pipeline?
If our first stop is the MSDN regarding the System.Management.Automation.Pipeline class, we end up with:

> Represents the base functionality of a pipeline that can be used to invoke commands
>
> -- <cite>[source](http://msdn.microsoft.com/en-us/library/system.management.automation.runspaces.pipeline(v=vs.85).aspx)</cite>

Not very helpful, as you still have no freakin’ clue what a Pipeline is at this point; but a few key things to pick out of this description will help us move forward…

- "Represents the Base Functionality"

We now know that this class happens to represent the base functionality of a Pipeline. Granted this still doesn’t inform us what a Pipeline is, or does, it at least tells us that other classes are likely to be derived from this class. If you dig in with ILSpy, low-and-behold, the class itself is marked abstract, and we can see that other classes do-indeed derive from it! That’s about all you need to know about the class itself for now; I just wanted to demystify that part of the class description in the interim.

![Pipeline class - ILSpy](/img/posts/2014-02-21-powershell-3-pipelines/01.png)

- "Can be used to Invoke Commands"
What commands you ask? Well, the Pipeline ends up turning every Script, Command (and parameters), etc. you punch into the PowerShell Console / ISE into a System.Management.Automation.PSCommand object under-the-hood. It also stacks them on top of one-another as they enter the Pipeline like so…

![Pipeline class - ILSpy](/img/posts/2014-02-21-powershell-3-pipelines/02.png)

# PowerShell is not a Pipeline
I see a lot of people online getting confused over whether the PowerShell class itself is actually a Pipeline. Much to everyone’s dismay, it is not. It’s used to interact-with and manipulate things to be placed into a Pipeline. Pipelines are instantiated inside of Runspaces and then there’s a special inner-class on the PowerShell class itself that grabs a handle to a Pipeline, and then your Commands get placed into that Pipeline instance.

# Where does a Pipeline live in the PS environment?
Pipelines are created by Runspaces. Runspaces live inside of Host Applications. PowerShell objects can manipulate the Pipelines inside of Runspaces by using a Worker object that talks to a Runspace and negotiates for a Pipeline to process its Commands. This is what it looks like...

![Pipeline class - ILSpy](/img/posts/2014-02-21-powershell-3-pipelines/03.png)

# Pipeline classes provided by .NET
* System.Management.Automation.RemotePipeline
  * System.Management.Automation.Runspaces.Pipeline
    * System.Management.Automation.Runspaces.PipelineBase
      * System.Management.Automation.Runspaces.LocalPipeline

This hierarchy most likely seems confusing. For myself, this was very confusing; due to the fact that the RemotePipeline class actually inherits upward in the name-spacing of all these corresponding classes. It inherits from Pipeline itself, but doesn’t utilize PipelineBase or anything below that.

# `System.Management.Automation.Runspaces.Pipeline`
Let’s start our journey at the abstract Pipeline class itself. Everything else provided by .NET regarding Pipelines starts from this class, so it’s worth understanding to understand Pipelines as a whole.

To be continued...

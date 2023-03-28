---
layout: post
title: PowerShell 3, Runspaces, and Callbacks
date: 2014-02-20 00:00:00 -0500
categories: 
tags:
---
# Breaking down the MSDN barrier
Let’s face it, finding the correct information across MSDN can be a nightmare. Things are constantly getting out-of-date, half the examples leave you a non-functioning example, and nothing is ever in a single place. To that end, I’ve picked a few items I feel should be broken down and understood before proceeding any further in this article. If you can’t grasp these classes and concepts, then the rest of the article will be very painful for you.

# `System.Management.Automation.PowerShell`
> Provides methods that are used to create a pipeline of commands and invoke those commands either synchronously or asynchronously within a runspace. This class also provides access to the output streams that contain data that is generated when the commands are invoked. This class is primarily intended for host applications that programmatically use Windows PowerShell to perform tasks. This class is introduced in Windows PowerShell 2.0.
>
> -- <cite>[source](http://msdn.microsoft.com/en-us/library/system.management.automation.powershell(v=vs.85).aspx)</cite>

## Pipeline of Commands
Every command (cmdlet) you’re running in PowerShell is being shoved into a Pipeline that’s eventually Invoked. The Pipeline starts off in a “closed / dormant” state until someone decides to open it up and let everything flow out in the order it went in. This is much like having a nozzle at the end of a hose, you turn the faucet on and let the water (cmdlets) fill up the hose, then when you open the nozzle (Invoke) it can finally start exiting the hose and watering (Executing) whatever it needs (the biggest different being that the input (faucet) valve gets shut the moment you Invoke the Pipeline).

## Runspace
I like to think of a Runspace similar to that of a Thread. It’s like being in your own compartment, with your own Global and Module-level session (citation?), as well as having the ability to be pooled and throttled. Every Command Pipeline you invoke in PowerShell is utilizing a Runspace one-way or another. An important tenant you’ll learn more about later regarding Runspaces, is that a they carry along an InitialSessionState once opened. This InitialSessionState defines what’s allowed to be run, what modules are available, etc. in the corresponding Pipeline that uses the Runspace to execute.

## Access to Output Streams
Usually we’ll want to know the result of an operation (or set of operations). In order to do this when working with a PowerShell instance and its corresponding Pipeline, it’s good to know ahead of time that we can plan to capture this information at this level of interaction with all of the objects we’ll be using throughout this guide.

## Host Applications
A Host Application is a lot more simple than it can initially seem. Did you build a C# Console Application that instantiates and uses a System.Management.Automation.PowerShell object? You wrote a Host Application. Did you build an MVC Site that instantiates and uses a System.Management.Automation.PowerShell object? You wrote a Host Application. Did you write a PowerShell Script that instantiates and uses a System.Management.Automation.PowerShell object? You wrote a Host Application. You get the point…

# `System.Management.Automation.Runspaces.Runspace`
> A runspace is the operating environment where the command pipeline of the [PowerShell](http://msdn.microsoft.com/en-us/library/system.management.automation.powershell(v=vs.85).aspx) object is invoked. This class provides methods for opening the runspace, creating single and nested pipelines for the runspace, and closing the runspace.
>
> -- <cite>[source](http://msdn.microsoft.com/en-us/library/system.management.automation.runspaces.runspace(v=vs.85).aspx)</cite>


## Operating Environment
What they mean by Operating Environment, is more-or-less the InitialSessionState mentioned above that will be carried along in a Runspace. Pretty much, if you didn’t tell the Runspace that consumers are allowed to access certain commands by passing them in the InitialSessionState, when a PowerShell object uses the Runspace to Invoke its Pipeline, an error will be thrown since those commands are now unavailable to the Pipeline.

## Opening the Runspace / Closing the Runspace
Runspaces are similar to Threads, Database Connections, Recordsets, etc…You must both Open and Close them for proper use! Lots of people seem to forget to close their Runspaces in a lot of examples I’ve seen, so as a good rule-of-thumb…Always add a new line of code for your .Close() statement(s) immediately after typing their corresponding .Open() statement(s).

## Creating Pipelines
What the hell? I thought the PowerShell object created Pipelines! I’m confused. No worries, the way the Class descriptions are worded on MSDN and how the classes interact can make this somewhat confusing. The PowerShell class has methods to Add to a “Pipeline of Commands” where-as the Runspace class creates the actual Pipeline those commands get placed into. Yes, this means when writing your code you will need to inform the PowerShell object of any pre-configured Runspaces you’ve created BEFORE adding Scripts, Commands, etc. into the Pipeline; if you forget this step, you’ll end up with a new, blank, Runspace, and your code will most likely fail inside of it.

# `System.Management.Automation.Runspaces.RunspacePool`
> Maintains a group of runspaces that have the same characteristics and can be opened and closed on an as-needed basis. Runspace pools can be used to execute multiple commands concurrently, with each command invoked in a different runspace. This class is introduced in Windows PowerShell 2.0.
>
> -- <cite>[source](http://msdn.microsoft.com/en-us/library/system.management.automation.runspaces.runspacepool(v=vs.85).aspx)</cite>

## Maintains a Group with Same Characteristics
This simply means that every time you ask the RunspacePool for an instance of a Runspace, it’s going to give you a Runspace that contains the same Operating Environment (InitialSessionState) as the rest of the Runspaces contained in the pool.

## Opened and Closed on an as-needed basis
As mentioned earlier regarding the Runspace class, they need to be opened and closed accordingly.

## Execute Multiple Commands Concurrently
This is the heart of this article and is what we’ll be relying on to handle our parallel processing going forward. For now I’m simply just emphasizing that this is where that comes into play later on.

# `System.Management.Automation.Runspaces.RunspaceFactory`
> Provides a means to create a single runspace or a pool of runspaces.
>
> -- <cite>[source](http://msdn.microsoft.com/en-us/library/system.management.automation.runspaces.runspacefactory(v=vs.85).aspx)</cite>

## Create your Runspaces Here
That’s all you’ll have to worry about with this class. RunspaceFactory is simply here to aid us in the creation of a new Runspace or RunspacePool, depending on what our situation calls for.

# FAQ

## Is an instance of the `System.Management.Automation.PowerShell` class a Pipeline as well?
I see a lot of people online getting confused over what the PowerShell class actually is / does. Some people call it a Pipeline, which is actually incorrect since it’s used to interact-with and manipulate things to be placed into a Pipeline. Pipelines are instantiated inside of Runspaces and then there’s a special inner-class on the PowerShell class itself that grabs a handle to a Pipeline eventually, and then your Commands get placed onto that Pipeline.

# Should I instansiate the `System.Management.Automation.Runspaces.Runspace` class directly?
The long and the short of it is, No. There’s a reason the RunspaceFactory exists, and I highly recommend you exhaust all avenues on that side of things before instantiating this class yourself.

Can a single Runspace contain more than one Pipeline?
No.
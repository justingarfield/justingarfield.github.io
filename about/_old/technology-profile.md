+++
title = "Technology Profile"
type = "page"
+++

# Web / Front-End / UI-UX

## HTML

I've been accustomed to HTML for quite some time. I first dabbled in HTML 3.01 back when I was in 5th grade and was instantly addicted. It was so cool to create a web page back in Netscape on Windows 3.1 and be able to share it with the world on hosting providers like GeoCities (yeah...you know what I'm talking about). Over time I've grown a passion for properly written HTML and take pride in working on projects that follow semantics and apply things like ARIA roles for users with disabilities. Whenever I have a question about a tag's usage (or any markup for that matter), I never shy away from taking a trip to W3.org and finding the answer.

## CSS

I first started applying CSS to things when it was still reveision 1, you know, when you would do everything inline? Yeah, that was a thing... Since then I've seen it go through its second, and now third revisions. I've seen some crazy things in relation to CSS, from DXImage transforms that are native to Internet Explorer for PNG transparency, to learning mechanics about browser innards troubleshooting @import issues. It has come quite a ways and it's refreshing to see things like LESS, SASS, and Stylus being actively contributed and fostered by so many projects.

## JavaScript

I've spent many years working with JavaScript on multiple projects and across many different stacks. I've trudged through the old days of inline onclick handlers in HTML, handling things like event-bubbling manually, long before the days of package managers and all of the awesome frameworks available today.

Before the term SPA was coined and things like jQuery / Dojo / Angular / Auerlia existed, I wrote what I thought was a very cool front-end, polling-based website widget that allowed a company's sales representatives to take chat messages from their web site and properly relay it bidirectionally between the site and their instant messenger of choice. It supported Yahoo! Messenger, AOL Instant Messenger, and MSN Messenger.

After that I wrote a highly successful small-medium sized business application from scratch using Dojo Toolkit and helped modify and contribute to that porject while building out the product at-hand. Some of my contributions to the Dojo Toolkit included shims to allow Netscape 7.2+ and Safari 2 browsers function and render properly, as well as contribute thoughts and ideas at meetups around the Boston and DC areas.

## LESS / SASS / STYLUS

I'm just going to lump all of these utilities together since they're very similar in nature. I've had more experience working with LESS / SASS due to my history of working with Bootstrap and doing some local testing between all three to see which I preferred more. In the end, I personally didn't mind one over the other for the purposes of what I was attempting to accomplish. There are some minor differences that would cause me to choose one or the other in some cases, but those would be very niche occurrences.

## Dojo Toolkit

I used the Dojo Toolkit from its beta days up-through the version 2 release for a heavily-used SPA. After evaluating some of the competing options at the time, I decided upon Dojo due to its enterprise-like layout and handling of namespacing and modules (this was still some uncharted territory back then). It's widget and layout systems were also some of the more sane options at the time too. Competitors I decided against were YUI, Tibco GI, and straight jQuery UI.

Dojo was one of the first projects I also got very involved in, meeting contributors at conferences and hacking on code while drinking and discussing the future of things. My experience with their team was a great one, and the people working on it were very vested. I contributed some browser compatability shims to the community for older browsers and also answered questions in the IRC channel for help.

## cujo.js

One of the people I worked with on the Dojo Toolkit project, went on to work on a set of libraries called cujo.js. A lot of brilliant minds contributed work to this codebase, and I highly recommend people check it out for a spin on developing javascript-based projects. The way the libraries remove the DOM as a first-class citizen is also very nice and de-couples things eventremly well for testing purposes and re-use. This was the first framework I used Promises with, and it implements them very nicely with wire.js.

## Aurelia

The prior version of this web site was written using a version of Aurelia that was still technically in development at the time and not a stable candidate. I really enjoy how Aurelia embraces ES6+ and tries to stay true to JavaScript (I think it's silly to put TypeScript on top of JavaScript if you never have any intentions of porting to something other than JavaScript)

## Angular 2

My Experience with Angular 2 has been building out the Tour of Heroes tutorial and then expanding that to test out E2E testing which is documented later on in the Angular documentation. To me this is very similar to Aurelia in-regard to ES6 Imports and core-level things like that, but takes a radical turn when you get into the TypeScript layer and how Angular has its own internal importing mechanisms and conventions. Overall though, very similar.

## jQuery Core and UI

I used jQuery Core and UI heavily up through version 2 on a lot of projects (if I introduce certain widgets through Foundation, this site will have to as well). I've dug through thousands of lines of code to understand its inner-workings on some solutions and also have gone through the days of it not being fully Promises/A+ compliant and dealing with nuances like that over time. It has definitely matured over the years, and still has its place, but I think frameworks like cujo.js, Aurelia, and Angular are quickly making it a moot-point in the SPA realm at this point.



# Development

## C\#

C# entered my world around the time .NET 1.1 was hitting. I started building a bridge and porting an application from Classic ASP w/ VBScript over to C# and ASP.NET. I think C# is an excellent language, and it's becoming even more interesting as they add new language features like lambdas and other things they help keep up with the ever-changing demands of the software world and patterns people are wanting to adopt.

## Java 

The first time I used Java was on a project where I inherited the server-side code after having been the front-end UI person for a year. Having had experience in C# already, I found it very easy to make a jump to Java. I absolutely enjoyed the language, and I still wish to-this-day that C# would implement checked exceptions, I think those are brilliant!

## ANSI C

I've never had a chance to use C in a production environment, but from my experience working on my custom operating system, I really enjoy. Sometimes I get frustrated with pointers all over the place, but at the end of the day, it's truly nice having such low-level control over everything. I recently had to work through issues going from Assembly to C and vice-versa to handle IRQs, and it was challenging, but uncovered so much about how programs operate and interact with our operating systems.

## XML / XSLT / XSD

Tried and true, I've dealt with plenty of XML payloads, transforms, and schemas. I've mainly had to work with XML in regard to Web Services, Configuration Files, and B2B transactions. I feel XML is quickly being deprecated by JSON and smaller protocols in the world of Micro-services, but still has its place when integrating with, or bridging, older systems that must remain as systems of record (mainframes at a hospital for instance...those aren't cheap systems to replace or convert from)

## Perl

Perl was the first language that I learned to build a dynamic web page with server-side processing and persistence. Through the old trusty common gateway interface, I built a simple auction web-site for a local consignment center, allowing viewers to reserve an item through the web site and flag it as on-reserve, as well as notify the business to put it in a holding area.

My last project using Perl was to convert a PHP-based mass emailer to something that would process quicker and handle larger volume. I was able to crank-out 100x more email in the same amount of time as the PHP-based solution by having more direct access to the SMTP stack through the Perl libraries.

## LUA

LUA is a great scripting language in my opinion. I've seen it implemented in a good chunk of products I consume reguarly (mainly World of Warcraft and a Minecraft Modification). I recently wrote a World of Warcraft addon to override default game behaviors and automate some things for players. I like how open and easy it is to find information on how LUA is / should be implemented, which makes it easy to learn the language and find answers quickly.

## Visual Basic 3-6 / VBScript / VB.NET

My first experience with Visual Basic was in Windows 3.1 using VB3. From that time, all the way up through VB 6, I created Win Forms desktop applications to help automate tasks. I then moved onto VBScript inside of Classic ASP pages, building dynamic web pages for eCommerce sites and even an entire ERP solution. As far as VB.NET goes, I have had limited experience with it, and at that point was helping port something to C# anyway. Over time I've lost my interest in the language and rarely see it being using anymore.

# Information Technology / Back-End

## PowerShell

My experience with PowerShell thus far has been in the realm of automating tasks for either deployment of software and CI, as well as building out full Cloud infrastructures using Azure PowerShell DSC to create SharePoint Farms, and also provisioning entire Office 365 Tenanats with things like DLP and IRM.

## Microsoft Azure

I've had experience across the board with Azure at this point. From an IT / DevOps / Development perspective, I've come into contact with it from all fronts. My heaviest focus was on the IT side of things, provisioning IaaS components, automating processes, migrating on-prem services to the cloud, and testing out features like DevTest Labs, DNS, and AD-as-a-Service while they were still in Preview.

## Amazon Web Services

I used to host my personal site and an LLC site through AWS using Elastic Beanstalk, IAM, Route 53, S3, and Simple Email Services. I really enjoyed using these services, and thougt the process was simple enough. Found it was very similar in offerings to Azure and other cloud providers.

## PowerShell Desired State Configuration (DSC)

I used DSC to provision SharePoint 2010 and 2013 farms for Engineering teams inside of Azure. This allowed me to quickly template out different configurations and test them quickly without a bunch of manual intervention. It proved to be an extremely powerful tool.

I also went through the process of custom-building a DSC resource that would handle SharePoint 2010, since the xSharePoint team only handled 2013 forward. I based it off of the existing xSharePoint resource, but also had to do a lot of investigation using tools like ProcMon to see what the 2010 installer was attempting to touch and required for the exact prerequisites.

## SharePoint 2010 / 2013 / Online

One of the companies I worked for was heavy into SharePoint. I'm sure we've all encountered this beast more-or-less at some point. My responsibilites were creating a 2010 Cloud DR solution for an on-prem instance, provisioning a new 2010 / 2013 Engineering farm in Azure IaaS, migrating 10-years worth of data from a 2010 instance to SharePoint Online, and I also created a custom DSC module to allow me to automate installation and provisioning through Azure DSC.

## Office 365 / Exchange Online

Wrote an entire suite of PowerShell scripts to provision a company's tenant and configure things like DLP, IRM, and MDM. A good deal of time was spent dealing with Exchange Online configuration and provisioning, as well as integrating certain points with InTune and MDM.

## Skype for Business

Skype for Business was a very tense but rewarding experience for me. The service had just come out of Preview on Office 365, and word came from our vendor that they were being purchased and we needed to make a decision on where to port our Lync 2013 services. Within 3-weeks I got all of the powershell written, accounts provisioned with proper licenses, and resovled CSR issues between Microsoft and some of the carriers involved. Overall the entire company had a 3-second outage during cut-over for messaging and VOIP communications, and was right back into action.

## Azure Active Directory (AAD) / Active Directory (AD)

## AD Federated Services / Web Application Proxy

## Windows InTune

## Internet Information Services (IIS)

## Windows Client / Server

## Linux

Lately I've been all over the board in the land of Linux. Working on writing my own custom OS in C++ has really shined a new light on Linux for me. I've been dabbling in Arch Linux, Linux Mint, and Ubuntu recently, and the new openSUSE seems nice as well. Arch is by-far my favorite though, as I prefer the "use what you need" mentality vs. throwing the entire kitchen sink in memory.

My latest Linux-based project is setting up a Dell PowerEdge to run KVM, KVM-Qemu, libvirt, Docker, and a few other hypervisor-related features. All of the plumbing is in place at this point, network cards are bonded for load-balancing / fail-over, and all that's left is deciding what I want to run for VMs on it.

## iOS / OSX

I've had to support iOS and OSX on mobile, desktop, and laptop environments for end-users. While not my strong-suit, I know my way around and understand enough to get most tasks and troubleshooting accomplishing without issue. I owned a MacBook Pro and a Mac Mini both running Leopard at one point, and acutally preferred them for front-end UI work.

## Novell Netware

When I was growing up with computers and networking, Novell Netware was THE go-to server solution and NLMs were awesome (seriously, who wanted to support NT 3.51?). I dealt with versions 3.11 through 4.11, which meant I got to experience the cut-over from Bindery to NDS. The 3.11 servers I managed at my school district were even networked together using BNC, coax, and terminators on a BUS network accessed through a bridge!

## Wildcat BBS

## Netscape Proxy Server

## Apache Web / Tomcat App Server

## SQL Server Reporting Services (SSRS)

# Internet of Things

## Arduino

I currently own an Arduino Uno and multiple peripherals to tinker around with. My latest concoction is 

## Raspberry Pi 2


# Databases

## T-SQL

What can I say? I think we've all been there at some point in our career. I've had to rip apart dynamic T-SQL statements over 12,000 lines long in some projects, deal with insane JOINs, query hints, eliminating SUB-SELECTs, and much more.

## Microsoft SQL Server 7, 2000, MSDE, 2005, 2008(R2), 2012(R2)

A lot of my heavy database work has been in SQL Server. It's popular, it's still all over the place, and you pretty much have to pry it out of peoples dead hands before they're willing to consider alternatives. I feel SQL Server still has its place, and will most likely never go away (some people can't afford eventual consistency due to contractual obligations or otherwise).

I'm used to writing simple to complex queries, building dynamic SQL statements, managing insane JOINs and UNIONs, dealing with triggers that call SPROCs that call imported CLR code that do all kinds of other crazy stuff. I'm also used to coming from the bottom-up and dealing with issues related to how tables are paging, troubleshooting IO bottlenecks, hunting down and elminating dead-locks, and working through why things are getting insane query execution plans.

## Azure MSSQL

While very similar to the on-prem version of SQL Server that we all know and love (or hate), Azure MSSQL is a great way to move certain databases into the cloud. It does have it's caveats though, which I found made migrating some solutions harder than others. For example, I've had lots of clients using query hints (with NOLOCK) that SQL in Azure just cannot support (think about sharding and scaling with some of these hints...just can't possibly work)

## MySQL

The largest install of MySQL that I've ever dealt with included both MyISAM and InnoDB tables that required replication across many nodes. The databases were backing a site that received large volumes of concurrent traffic and needed to have at least 31-days of straight up-time for any given run of a game bracket. Originally the two table engine types were hosted in the same databases, but as volume increased it was decided to split out the InnoDB tables to their own set of servers and reduce the stress on the MyISAM side of the house since the application was more read-intense and less transaction-instense anyway. I really enjoy working with MySQL, which last I checked is now MariaDB and continues the product forward in the open-source nature it started with.

## Redis

Redis is the only major caching solution I've had the pleasure of working with that wasn't already rolled into a massive framework like .NET. I found it to be extremely simple to configure and communicate with, and I've also enjoyed how easy it is to deploy nodes in Docker or Azure directly.

## SQLite

I utilized SQLite at one point as a small, embedded application cache for a solution I was building. It didn't play too big of a role, but it did it's job properly and I never ran into any issues for the purposes of my use-case.

## Amazon Azure / AWS / Salesforce PaaS

## Microsoft WIF / WCF / MVC / Web API / ASP.NET 1.0 to 4.5, J2EE, node.js


# System Integrations

The B2B space loves its system integrations, and that holds true to today! Why have multiple systems of record if you don't have to?

## Google Calendar Integration

## Salesforce.com Integration

## ARIA Payment Services Integration

## Rustici SCORM Engine

## QuickBooks and QB SDK Integration



Tools and Services: Visual Studio, Visual Studio Team Services, IntelliJ IDEA, Websphere Application Server Toolkit (AST), Eclipse, JIRA, GitHub / Gitter, GIT, npm, bower, gulp, Nuget, Apache SOLR, (N)Hibernate 2.x+, Jenkins, Maven, Selenium, TestNG, Subversion, Trac, ANT, Gradle

Additional Technologies: , SAML, STS, Federated Logon, Mozilla Rhino, YUI Compressor

## OS/2 Warp

What can I say? I found a box with the installation diskettes (that's right diskettes!!) in a closet at one of my jobs, and I just had to try it out. I was underwhelmed and quickly lost interest.

## DEC VAX and Alpha Systems

The hospital I worked at relied on multiple VAX machines and an Alpha mainframe to run a lot of the scheduling and billing software across all of our locations and offices. My touch-points with these systems were building out printer queues to allow jobs to be sent to printers backed by Lantronix TCP/IP printer servers out in the field. This was some foreign territory for me, but was a great learning experience outside of the Microsoft and Novell bubbles I was in at the time.

## BSD-based Netscape Proxy Server

I had to work with a vendor for multiple-years at one point on a Netscape Proxy Server that handled all internet and mail traffic for 1500+ nodes over a single T1. It also had a bank of 20 USR Dial-up modems for remote access, which was pretty incredible at the time. Maxed out at 64MB of RAM though, it quickly encountered problems with the emergence of online serivces and was eventually replaced by Microsoft ISA server (that and because it didn't handle port-forwarding for our Citrix Metaframe install).


Citrix Metaframe
Microsoft IIS 3-7, SMTP, and FTP services
Microsoft ISA Server
Microsoft Server NT 3.51, NT 4, 2000, 2003, 2008(R2), 2012(R2)
Microsoft Windows 95A/B/C, 98(SE), ME, 2000, XP, 7, 8, 8.1, 10

Microsoft Access 7-2003
WebSphere Application Server 6

(Q)BASIC
Delphi
Turbo Pascal
Assembly
PHP 3 and 4


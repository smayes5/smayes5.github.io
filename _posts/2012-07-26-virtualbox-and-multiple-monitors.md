---
title: 'VirtualBox and Multiple Monitors'
slug:  'virtualbox-and-multiple-monitors'
date:  2012-07-26 14:40:00 -0400
---

As a SharePoint consultant, I really enjoy having a **local** copy of a full SharePoint server on my laptop. As I am patiently waiting for Windows 8 to release with [Client Hyper-V](http://technet.microsoft.com/library/hh857623.aspx), I'm continuing to use Oracle's VirtualBox software to host my SharePoint development environments.

<!--more-->

With my current engagement, I find myself in my client's offices Monday through Thursday and at my office on Friday. At my office, I have an external monitor that I use in addition to my laptop's screen. At my client's offices, I just have my laptop's screen to use. Whenever I'm able to have a secondary monitor connected, I like to use VirtualBox's multiple monitor support to show my VM on both of my screens. VirtualBox achieves this look by opening two windows - each window meant to be maximized on one of the two monitors.

On Fridays I get to use two monitors with two VirtualBox windows. However, come Monday, I typically forget to reset the virtual machine back to one monitor. Even though I'm only using one monitor, VirtualBox still opens two windows for me! With only one monitor, this makes the VM very tough to use. The only remedy is to shut down the VM, reconfigure the setting, and restart the machine; a process that can be quite lengthy on a server with SharePoint 2010 and SQL installed!

I turned to PowerShell to help ease my Monday morning woes. I developed the script below and put a link in my Startup folder so that, each morning when I boot my laptop, all of my VirtualBox VMs will be *automatically configured* to use the number of monitors that I currently have connected to my laptop.

Fair warning - this was a quick script just for my personal use. While it should work for you, it does not have any error handling to report if things go awry. See the comments in the script for details.

<script src="https://gist.github.com/smayes5/00df1d04a69f82bad17a.js"></script>

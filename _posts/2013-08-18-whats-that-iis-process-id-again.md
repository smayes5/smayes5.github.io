---
title: "What's that IIS Process ID again?"
slug:  'whats-that-iis-process-id-again'
date:  2013-08-18 14:01:00 -0400
---

This one came up during a recent sprint of coding in which I was debugging a SharePoint 2013 farm solution (I know - apps are the way to go now!) with Visual Studio 2012. Let me say, since first starting development in MOSS 2007, this is a totally different and much smoother experience. I switched back-and-forth between using Visual Studio's "Play" button - also known as "Start Debugging" - and a more traditional Build, Deploy, Attach to Process method.

When looking to attach to IIS, it's always a gamble as to which w3wp.exe process to attach. However, this can be avoid by using the `appcmd.exe` utility. There is a nifty set of parameters that will allow you to view the currently running w3wp processes, their process IDs, and the names of the associated application pools. It's just the right amount of information.

<script src="https://gist.github.com/smayes5/1419a602cc3d2cb0cfd8.js"></script>

Enjoy and happy debugging!

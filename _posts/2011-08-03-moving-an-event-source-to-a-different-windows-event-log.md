---
title: 'Moving an Event Source to a Different Windows Event Log'
slug:  'moving-an-event-source-to-a-different-windows-event-log'
date:  2011-08-03 11:36:00 -0400
---

It is typically best practice when developing .NET applications, including SharePoint customizations, to create an event source for Windows Event Logging while installing the application. Each event source on a Windows computer is tied to a specific log upon registration. I recently provided guidance on how to move an event source to use its own brand new event log. The following lines of PowerShell can do this quickly. Unless your .NET application has the event log hardcoded into itself, which it shouldn't because the event source should be registered to a log during installation, then the move shouldn't require any code changes.

<script src="https://gist.github.com/smayes5/7707e4e75f827bfb084a.js?file=Move-EventSource.ps1"></script>

I found that I had to reboot the machine after executing the above lines of PowerShell for this change to fully take effect.

**_Update_** - I also have had the need to update the event log properties. To do so, use the Limit-EventLog cmdlet. The following code limits the `MyNewOrExistingWindowsEventLog` event log to a size of 20 MB (20 x 1024 x 1024 or 20,971,520 bytes) and tells Windows to overwrite old entries with new entries as needed.

<script src="https://gist.github.com/smayes5/7707e4e75f827bfb084a.js?file=Limit-EventLog.ps1"></script>

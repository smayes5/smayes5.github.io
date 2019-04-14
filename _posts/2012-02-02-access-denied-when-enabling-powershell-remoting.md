---
title: 'Access Denied when Enabling PowerShell Remoting'
slug:  'access-denied-when-enabling-powershell-remoting'
date:  2012-02-02 10:17:00 -0500
---

Quick tip: Are you receiving Access Denied messages when you are attempting to run the `Enable-PSRemoting` cmdlet? I've received them on machines connected to a domain, even when I'm running in an elevated PowerShell window. An easy trick is to open a PowerShell window as the built-in administration account on the machine. Not sure why, but for whatever reason, it seems to work!

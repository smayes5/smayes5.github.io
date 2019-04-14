---
title: 'Adding a PowerShell Snap-in'
slug:  'adding-a-powershell-snap-in'
date:  2012-05-29 23:31:00 -0400
---

A typical scenario when developing PowerShell scripts for SharePoint is to need the use of the SharePoint PowerShell snap-in. This snap-in provides all of the great SharePoint cmdlets that have quickly become critical to any SharePoint developer or administrator's toolkit.

Some scripts may assume that the script will be executed within the SharePoint 2010 Management Shell - which is simply a standard PowerShell window with some extras: including the SharePoint PowerShell snap-in. However, what if an unsuspecting or unexperienced administrator executes the SharePoint script in a standard PowerShell instance?

To cover this circumstance, most script developers will choose to add the snap-in at the beginning of the script using the following code.

<script src="https://gist.github.com/smayes5/6359618931e9cbc3b2c0.js?file=Add-PSSnapin-Basic.ps1"></script>

While this seems sufficient, there is an issue if the snap-in has already been added. This would easily occur if the script was run within the SharePoint 2010 Management Shell. PowerShell reports the following error pertaining to the fact that the snap-in has already been added:

> Add-PSSnapin : Cannot add Windows PowerShell snap-in Microsoft.SharePoint.PowerShell because it is already added. Verify the name of the snap-in and try again.  
> At line:1 char:13  
> \+ Add-PSSnapin < <<<  Microsoft.SharePoint.PowerShell -PassThru  
>     \+ CategoryInfo          : InvalidArgument: (Microsoft.SharePoint.PowerShell:String) [Add-PSSnapin], PSArgumentException  
>     \+ FullyQualifiedErrorId : AddPSSnapInRead,Microsoft.PowerShell.Commands.AddPSSnapinCommand

A typical solution to this issue I've seen is to instruct PowerShell to ignore the error using the following code.

<script src="https://gist.github.com/smayes5/6359618931e9cbc3b2c0.js?file=Add-PSSnapin-SuppressErrors.ps1"></script>

That bothers me. I should be looking to suppress only the error where the snap-in is already added. What if I get another unrelated error? Therefore, instead of instructing PowerShell to silently continue, I would prefer the following code below. This code first checks for the snap-in before adding it and also allows for unrelated errors to still be reported.

<script src="https://gist.github.com/smayes5/6359618931e9cbc3b2c0.js?file=Add-PSSnapinIfNotYetAdded-SharePoint.ps1"></script>

It should be noted that this code could be used with any snap-in; not just SharePoint's PowerShell snap-in. Here is a function one could use for any snap-in.

<script src="https://gist.github.com/smayes5/6359618931e9cbc3b2c0.js?file=Add-PSSnapinIfNotYetAdded.ps1"></script>

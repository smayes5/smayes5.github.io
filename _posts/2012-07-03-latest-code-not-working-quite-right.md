---
title: 'Latest Code Not Working Quite Right?'
slug:  'latest-code-not-working-quite-right'
date:  2012-07-03 09:29:00 -0400
---

I'm going to start this post with an example. See if you have ever found yourself in this situation when working on developing updates for previously deployed code; most often in a test and/or development environment:

1. I start out with an existing feature receiver that uses a web-scoped feature. The feature is already deployed to my development environment. To make this example very simple to illustrate the issue - this feature receiver will set the web's title to an arbitrary value. The title will be reset back to its original state when the feature is deactivated.  
<script src="https://gist.github.com/smayes5/468c8a683bb5c0d39212.js?file=ExampleFeatureEventReceiver.cs"></script>

2. Activating the feature on my test site reveals the following site title change.  
**Pre-activation**  
![Site title showing My Example Team Site]({{ "/assets/images/2012-07-03-2-pre.jpg" | relative_url }})  
**Post-activation**  
![Site title showing EXISTING WEB TITLE]({{ "/assets/images/2012-07-03-2-post.jpg" | relative_url }})

3. Now I'm going to illustrate a change to my previously deployed code. Let's say for example that I wanted the title to be changed to `*** NEW WEB TITLE ***` instead of `EXISTING WEB TITLE`. I'll make the change on Line 16 of the code above and create a new WSP for deployment.

4. I then open a new PowerShell window and execute the following six PowerShell commands using the *same* window. These commands will deploy the latest code and reactivate the feature on my test site so I can see the latest change.  
<script src="https://gist.github.com/smayes5/468c8a683bb5c0d39212.js?file=Install-LatestCode.ps1"></script>

5. I will then visit the site to see the change...  
![Site title showing EXISTING WEB TITLE]({{ "/assets/images/2012-07-03-2-post.jpg" | relative_url }})  
What happened? The title didn't change?! What is wrong?

The problem occurred during Step 4. When the first command deactivated the feature, that cmdlet called the SharePoint DLLs which in turn called the SMayes.SharePoint assembly. This loaded the assembly in the PowerShell.exe process. Once the assembly is loaded, it will not be unloaded.

That's the issue here: Once a DLL is loaded in a .NET process, it will not and **_cannot_** be unloaded!

So what? This means that when I executed the Enable-SPFeature cmdlet, PowerShell was still referring to my **old** code that was loaded earlier and therefore was not using my updated code! Opening a new PowerShell instance and executing the activation in that instance will cause the latest code to be used since the new process will be getting the updated DLL from the GAC.

In summary: Whenever you are deploying code updates, the proper way to execute the redeployment would include closing the current PowerShell process and opening a new PowerShell process as soon as new assemblies are deployed. This will ensure that further commands, such as feature activations, will not use outdated code.

Note that while the issue I described above pertains specifically to a feature receiver, it would also equally pertain to an event receiver, timer job, or other piece of code within a deployed assembly called by the SharePoint API. This issue is also a general issue across the board whenever a process (PowerShell, W3WP, OWSTimer, etc.) is using a custom-built assembly. This is why, when deploying new assemblies, W3WP is typically recycled. Also ensure that OWSTimer is recycled if there are any timer jobs being deployed.

Happy coding!

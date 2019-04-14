---
title: 'Expanding Warm Up Script Triggers'
slug:  'expanding-warm-up-script-triggers'
date:  2011-09-07 13:46:00 -0400
---

This post is a follow up to a post I wrote a few months ago around triggering a warm up script for SharePoint to execute only when a specific application pool is recycled. You may want to read that post before this one: ["Application Pool"-Specific Warm Up Scripts]({% post_url 2011-06-29-application-pool-specific-warm-up-scripts %})

-----

After using what I recommended in the previous post, I realized there were still issues with this approach. To step back and explain the larger picture, I had a scenario where I needed to execute a warm up script whenever the application pool was freshly started. The script is responsible for filling application-level cache. Therefore, I want to execute the script any time the application pool will be freshly started. This means not just when it is recycled, but also in instances like server reboots. I have previously described how to do this using the Windows Task Scheduler to trigger the script to execute based on certain events being logged.

<div class="box"><b>Preface:</b> I did brainstorm ideas to trigger execution when the application pool started, instead of triggering when the application pool recycled, etc. Regrettably, I was unable to find any Windows events that are logged when the application pool starts. Therefore, that paradigm could not be leveraged. However, one could feasibly create a custom extension to the OOTB SharePoint <a href="http://msdn.microsoft.com/en-us/library/system.web.httpapplication.aspx">HttpApplication</a> class (<a href="http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.applicationruntime.sphttpapplication.aspx">SPHttpApplication</a>) and add logic to one of the class' methods or events to trigger the warm up script.

This method would be a bit expensive in terms of the need for extra custom development, impacts to the entire web application due to the need to customize the IIS web application's HttpApplication class, extra deployment steps, interoperability issues with other global.asax customizations, etc. Evaluate this carefully before proceeding with the suggestion above.</div>

As it would be too difficult to trigger when the application pool is started (see the Preface above), I needed to continue triggering the event based on what would occur immediately prior to the application pool being started. I found three things that need to be used:
1. Server Start/Reboot
2. IIS Reset
3. Application Pool Recycle

**Server Start/Reboot**  
Whenever the server is started, one may wish to trigger the warm up script. If the server will take requests or the script will perform a function desired soon after the server is started, then this trigger can be used. Fortunately, the Windows Task Scheduler allows one to add a trigger that will begin the task **At startup**. Therefore, one can easily add this trigger!

**IIS Reset**  
Whenever IIS is reset, then all of the application pools will be shut down. If requests will come shortly after resetting IIS, it would make sense to use this as a trigger for the warm up script. After analyzing all of the events that the IISreset utility could log, the only one that applies to the situation of needing to track the application pool's pending start would be when IIS is freshly started: ID 3201. All of the other events described above pertain to states where the warm up script would not need to be executed.

To add event 3201 as a trigger to the task scheduler, you can use the following settings:
- Begin the task: On an event
- Log: System
- Source: IIS-IISReset
- Event ID: 3201

Note that, per the TechNet links below, Microsoft no longer supports using the IISreset utility in IIS 7.0 & 7.5.

IISreset Event Reference for Windows Server 2008 / IIS 7.0  
[http://technet.microsoft.com/en-us/library/cc735265(WS.10).aspx](http://technet.microsoft.com/en-us/library/cc735265(WS.10).aspx)

IISreset Event Reference for Windows Server 2008 R2 / IIS 7.5  
[http://technet.microsoft.com/en-us/library/dd364067(WS.10).aspx](http://technet.microsoft.com/en-us/library/dd364067(WS.10).aspx)

**Application Pool Recycle**  
This is the key event that need to be tracked. The application pool's recycle settings in IIS are quite extensive! Each reason for recycling produces a different event. This is the major area I realized that I was lacking from the previous post.

IIS logs an event when the application pool recycles
- (5074) automatically on a regular time interval,
- (5075) automatically when a defined number of requests have been fulfilled,
- (5076) automatically at specific times,
- (5077) automatically when a defined amount of virtual memory is used or exceeded by the worker process,
- (5078) automatically when an ISAPI extension reports an unhealthy condition,
- (5079) manually by an administrator,
- (5080) automatically upon making configuration changes,
- (5081) automatically due to detected problems with the IIS configuration store,
- (5117) automatically when a defined amount of private memory is used or exceeded by the worker process, or,
- (5186) automatically when inactive.

Parameters for automatic recycles can be configured in the IIS Manager. After considering each event, if the warm up script needs to perform any function that should *always* be in place while the application pool is running, then each of these events should have a trigger set against it.

The good news is that we can use the notion from the previous post to configure the trigger. (["Application Pool"-Specific Warm Up Scripts]({% post_url 2011-06-29-application-pool-specific-warm-up-scripts %})) One simply needs to include all of the extra IDs in the event filter.

Example Event Filter for an example application pool **SharePoint - 32767**  
*Don't forget to replace **SharePoint - 32767** with your application pool's name!*
<script src="https://gist.github.com/smayes5/187fc374d73220619f8e7aba03c21708.js"></script>

**Important Note: There are also settings in IIS to control if IIS generates recycle event log entries for each of the events listed above! For any warm up script triggers to function properly, ensure that all of the scnearios are set to generate events!**

Application Pool Recycling Event Reference for Windows Server 2008 / IIS 7.0  
[http://technet.microsoft.com/en-us/library/cc735206(WS.10).aspx](http://technet.microsoft.com/en-us/library/cc735206(WS.10).aspx)

Application Pool Recycling Event Reference for Windows Server 2008 R2 / IIS 7.5  
[http://technet.microsoft.com/en-us/library/dd349270(WS.10).aspx](http://technet.microsoft.com/en-us/library/dd349270(WS.10).aspx)

**Summary**  
In closing, don't forget to include any necessary triggers to your own particular warm up script's scheduled task in Windows so that it executes whenever desired! My suggestions are to include the server start/reboot, the IIS Reset event (3201), and the application pool recycle events (5074-5081, 5117, & 5186).

**Thanks to [Greg Rosati](https://www.linkedin.com/in/greg-rosati-33429311/) for providing the links to the event information on TechNet that led to the creation of this follow up post!**

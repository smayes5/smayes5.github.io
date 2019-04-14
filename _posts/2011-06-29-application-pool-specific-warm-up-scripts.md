---
title: '"Application Pool"-Specific Warm Up Scripts'
slug:  'application-pool-specific-warm-up-scripts'
date:  2011-06-29 15:18:00 -0400
---

**Thanks to Thomas Vuylsteke and his ADdict blog for providing the foundation of this post!**  
[FIM 2010: Warm Up Your Portal (IIS)](http://setspn.blogspot.com/2011/06/fim-2010-warm-up-your-portal-iis.html)  
Be sure to read this post first to get the foundation of how one can configure a scheduled task to execute a warm up script whenever IIS recycles an application pool or whenever IIS is reset.

-----

I had a scenario recently where one of my clients needed a rather long running warm up script to execute upon the recycling of a particular application pool. Being a larger farm, there were several application pools and the client only desired the warm up script to run when one specific application pool was recycled. The Windows Task Scheduler allows tasks to be triggered for execution when specific events are logged. When an application pool is recycled (automatically or manually) or when IIS is reset, events are logged in the System event log. Those events can be used to trigger tasks to run a warm up script in the Windows Task Scheduler!

<div class="styled-table">
<table>
<tr><th>IIS Action</th><th>Source</th><th>Event ID</th></tr>
<tr><td>Application Pool Recycle (automatic)</td><td>WAS</td><td>5076</td></tr>
<tr><td>Application Pool Recycle (manual)</td><td>WAS</td><td>5079</td></tr>
<tr><td>IIS Reset</td><td>IIS-IISReset</td><td>3201</td></tr>
</table>
</div>

One can implement triggers for a warm up script task on each one of these event IDs. However, the 5076 & 5079 events are logged for *all* application pools. Therefore, if either event is used for a trigger, it means that the task will execute once for every application pool recycle on that instance of IIS. This may or may not be desired; for my client, it was not desired.

I needed to inspect the 5076 & 5079 events to see if there was enough data to further filter the trigger so it would only match 5076 & 5079 events for a specific web application. In my example, I'm attempting to filter on a web application named "SharePoint - 32767" (the name of your application pool can be looked up in IIS Manager).

The following is an excerpt of the relevant sections of the 5079 event's XML representation (the 5076 event logs similar data):
<script src="https://gist.github.com/smayes5/f5406b9634dde2205a8374b78595c286.js?file=Event5079Excerpt.xml"></script>

Fortunately, the application pool's name is logged! One can use that data to filter the trigger even more. To do this within the trigger, select **Custom** on the event trigger window. Then click on **New Event Filter...**

![Trigger on Custom Event Filter]({{ "/assets/images/2011-06-29-trigger-on-custom-event-filter.jpg" | relative_url }})

Once in **New Event Filter** window, select the **XML** tab and check the box at the bottom to **Edit query manually**. Copy and paste the following - which will cover *both* the 5076 and 5079 events. Don't forget to replace the **SharePoint - 32767** name in the example below with your application pool's name!
<script src="https://gist.github.com/smayes5/f5406b9634dde2205a8374b78595c286.js?file=QueryForEvents5076And5079.xml"></script>

This will cause the warm up script task to trigger on the 5076 & 5079 events, but only for a specific application pool!

**Thanks again to Thomas Vuylsteke and his ADdict blog for providing the foundation of this post!**

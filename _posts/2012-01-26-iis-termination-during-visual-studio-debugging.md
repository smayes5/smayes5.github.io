---
title: 'IIS Termination during Visual Studio Debugging'
slug:  'iis-termination-during-visual-studio-debugging'
date:  2012-01-26 10:32:00 -0500
---

What a familiar situation: You are debugging a SharePoint solution (or any ASP.NET code) and have Visual Studio attached to IIS. As you are stepping through your code, checking variables and getting a lot done, all of the sudden you are presented with the following message:

> The web server process that was being debugged has been terminated by Internet Information Services (IIS). This can be avoided by configuring Application Pool ping settings in IIS. See help for further details.

<!--more-->

![IIS Terminated Process Error Message from Visual Studio]({{ "/assets/images/2012-01-26-TerminatedProcess.jpg" | relative_url }})

It typically occurs within two minutes of Visual Studio pausing on a breakpoint. It is possible to extend this timeframe and the error message gives the answer. The reason Visual Studio presents this message is because IIS is forcefully terminating the worker process being debugged. Why? Because IIS (by default) performs health monitoring pings against each of its worker processes to ensure they are still responding. If IIS does not receive a response from the worker process to one of these pings within a given timeframe, IIS forcefully terminates the worker process. When debugging in Visual Studio, the worker process is stopped while Visual Studio is paused on a breakpoint. Therefore, the worker process has no way to respond to a health monitoring ping. Therefore, the worker process gets terminated.

How does one stop this vicious cycle and allow debugging to continue unimpeded? As the error message states, modify the IIS settings for health monitoring pings! To do this on a particular application pool:
<ol>
	<li>Open IIS Manager.</li>
	<li>
		<p>On the left in the Connections pane, click <b>Application Pools</b>. The list of all of the application pools in IIS will display in the middle section of the window.</p>
		<p><img src="/assets/images/2012-01-26-ApplicationPools.jpg" alt="IIS Manager - Application Pools in the Connections Pane" /></p>
	</li>
	<li>
		<p>Select the particular application pool used for debugging and click <b>Advanced Settings</b> under Edit Application Settings on the right in the Actions pane.</p>
		<p><img src="/assets/images/2012-01-26-AdvancedSettings.jpg" alt="IIS Manager - Advanced Settings in the Actions Pane" /></p>
	</li>
	<li>
		<p>Modify the two Ping settings: <b>Ping Maximum Response Time (seconds)</b> and <b>Ping Period (seconds)</b>. The Ping Period is the interval used by IIS to ping the worker process. The Ping Maximum Response Time is the amount of time IIS will wait for a ping response. If IIS doesn't get a response after the maximum response time has elapsed, that is when the termination occurs. By default, the ping period is 30 seconds and the maximum response time in 90 seconds. These are great in normal situations, but only give you about two minutes for debugging! Therefore, I would suggest throwing a few extra zeros in there - I personally set my ping period to 3000 seconds (50 minutes) and my maximum response time to 9000 seconds (150 minutes or 2.5 hours). Those values give me plenty of time to debug without needing to worry about termination. Once you have modified these values, click <b>OK</b>.</p>
		<p><img src="/assets/images/2012-01-26-PingSettings.jpg" alt="IIS Manager - Application Pool Advanced Settings Dialog" /></p>
	</li>
</ol>

While I would recommend developers to modify the health monitoring ping settings on their local development servers, **these modifications should never be made in a testing or production environment.**

Happy debugging!

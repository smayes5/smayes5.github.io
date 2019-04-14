---
title: 'Finding Sites with a Particular Feature Activated'
slug:  'finding-sites-with-a-particular-feature-activated'
date:  2011-02-12 22:05:00 -0500
---

I continually have a question arise that seems easy to answer, yet from what I can tell, is not yet available with SharePoint out-of-the-box in its user interfaces. That question is:

> What are all of the sites with feature *xyz* enabled?

A few years ago with a MOSS 2007 client environment where I had to answer this question, I was left with writing a quick C# console application that would crawl a web application for me and discover all of the site collections with a particular feature enabled. Not quite the best way of doing things, but it's what was the best at the time.

Fast forward to SharePoint 2010. Now, PowerShell is in the mix and *required* for all SharePoint installations. **Thanks Microsoft!** So now, I have the vast capabilities of PowerShell available on any SharePoint farm I may encounter on any client environment with which I may be working. Plus, there's an added bonus that, for some reason, the word "script" seems to scare clients and IT departments less than "console applications". Even though PowerShell scripting can often wield the same powers as a C# console application, it seems to be accepted with more ease which makes my life easier.

Now that PowerShell is widely available, I rewrote that "quick C# console application" I referenced above in PowerShell for use recently on a SharePoint 2010 environment. The script below has a slightly more specific task than solving the question I posed above:

> Output all of the site collection URLs within a particular web application that have a particular site-scoped feature enabled.

Here's my script to do just such a task. The code below is meant for a single ps1 file.

Please note that it should work with **both** the 2007 & 2010 SharePoint product lines: SharePoint 2010 (Foundation & Server), WSS 3.0, and MOSS 2007. I have only tested this code on a SharePoint Server 2010 environment, but have no reason to believe it wouldn't work in the other environments.

<script src="https://gist.github.com/smayes5/130e694c2c5e4e30c8dd.js"></script>

Feel free to use this script or modify it to fit your needs. There are several extra features that could be added to this script to make it even more versatile:

- Reporting on the status of multiple features, not just a single feature
- Reporting based on feature name versus feature GUID
- Reporting on features scoped at different levels besides Site
  - Farm
  - Web Application
  - Web
- Crawling other scopes besides a single web application
  - The entire farm,
  - Multiple web applications,
  - A subset of site collections,
  - A subset of sites,
  - Based on another block of script and/or function call to determine if a site should be scanned by the script,
  - etc.

I think the above script is a great starting point. Hopefully either myself or someone will get around to adding the above suggestions. Please share in the comments if you do end up extending this script!

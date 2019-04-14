---
title: 'Hiding the SharePoint 2010 Ribbon for Readers - A Proof of Concept'
slug:  'hiding-the-sharepoint-2010-ribbon-for-readers-a-proof-of-concept'
date:  2011-02-05 13:06:00 -0500
---

In SharePoint 2010 publishing sites, the ribbon is the new way of life for authors. However for readers, the ribbon provides very little, if any, functionality. A few days ago, I was asked about hiding this empty space by a client. Their current 2010 master page had the ribbon moved to a custom position on their publishing sites. They had also suppressed the breadcrumb/folder navigation in the ribbon; thus for a typical reader view, there was no ribbon contents that would be displayed. However, the space on the page where the ribbon would live for authors still remained as empty space!

We very well could have developed some server-side logic to determine the permissions of the user and hide that area completely based on that. Given infinite time and resources, I probably would have opted for that solution.

However, several of us were tasked with coming up with an easier and simpler solution to implement. One of the other consultants in the room at the time suggested a solution using JavaScript to hide the area if no contents was found (i.e. if the ribbon hadn't rendered anything for the reader). I thought it was a great idea and immediately opened up an out-of-the-box publishing site in SharePoint Designer to tinker and develop a bit of JavaScript.

The goal of my JavaScript POC was to hide the *entire* `div` tag containing the SharePoint out-of-the-box ribbon control. I investigated and discovered that, in the `nightandday.master` file, the ribbon is contained in a `div` with the ID `s4-ribbonrow`. With that ID, I would be able to hide that `div` based on its rendered HTML contents. I figured, for this example, that the existence of the text "Browse" would be enough to determine whether or not the ribbon had rendered something or not.

To do this, I added JavaScript code to `nightandday.master` and it worked like a charm. Below is the relevant excerpt of `nightandday.master`:

<script src="https://gist.github.com/smayes5/59b2fc7ebed33d3e077e1bfdf0e897d6.js"></script>

I should note that I only was able to spend about 30 minutes on this entire topic. Please treat the above code as a proof-of-concept and make sure to evaluate the impact or effectiveness of this code in your own environment before using it.

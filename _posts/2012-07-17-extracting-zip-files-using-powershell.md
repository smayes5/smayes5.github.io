---
title: 'Extracting Zip Files using PowerShell'
slug:  'extracting-zip-files-using-powershell'
date:  2012-07-17 10:11:00 -0400
---

To help troubleshoot some SharePoint issues, I had a need to analyze some log files that were contained in multiple zip files; one log file per zip file. Since there were several hundred zip files to extract, I figured PowerShell could help! There are several posts I found with example scripts for how to perform this operation. I took pieces from many posts, added some COM object clean-up code, and wrapped this in a function. I hope it helps you in your scripting activities!

<!--more-->

The code below is the function I'm using to extract a single zip file. It leverages the Windows Shell COM object (i.e. Windows Explorer) to extract the files by exploiting the fact that Windows Explorer treats zip files as folders. Therefore, the operation can leverage existing file copy methods between two folders.

<script src="https://gist.github.com/smayes5/48926beaa065198ffd24.js"></script>

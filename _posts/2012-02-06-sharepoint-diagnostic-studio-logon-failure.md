---
title: 'SharePoint Diagnostic Studio Logon Failure'
slug:  'sharepoint-diagnostic-studio-logon-failure'
date:  2012-02-06 05:08:00 -0500
---

I was introduced to the [SharePoint Diagnostic Studio 2010](http://technet.microsoft.com/en-us/library/hh144782.aspx) at the Microsoft SharePoint Conference 2011 in Anaheim, CA. I was very excited about its capabilities and have just gotten around to testing it in the last couple of days.

After configuring PowerShell remoting between my laptop and my development server, I set out to create a new project in the SharePoint Diagnostic Studio and see what it could do. Upon creating a new project, I entered my credentials. After a few moments, I received the following message.

> It appears the new project installation was not successful. Click OK to see the error message

After clicking OK, the error log is opened. If it does not open for you, my log was at `%HOMEPATH%\Documents\SharePoint Diagnostic Studio\Server Extensions\Error.log` In the error log, I saw the following message.

> Logon failure: unknown user name or bad password.

After getting quite paranoid about being up too early in the morning and perhaps not remembering any of my passwords, I started to investigate things on the server to see if I could figure out what was going on. What I found has to do with my configuration of CredSSP authentication.

With CredSSP authentication, there are two preferred/secure ways of submitting credentials: Kerberos authentication and Certificates. These required extra configuration of PowerShell remoting that I haven't quite gotten to just yet. Therefore, my remoting capability was relying on the third and less secure way of submitting credentials: NTLM.

It turns out, after investigating my server's security log, the SharePoint Diagnostic Studio (or perhaps the underlying PowerShell commands) are attempting to first authenticate using Kerberos. That fails and I would expect that since I didn't do all of the necessary configuration for Kerberos to work. However, it then falls back to NTLM. To my surprise it does not use the credentials I had specified. Rather, it was using my local laptop credentials!

To trick any application in to sending other credentials when it wants to send the credentials of the currently logged in user, you can use the [Runas command](http://technet.microsoft.com/en-us/library/bb490994.aspx) which allows one to execute any program under another user. For this instance, SharePoint Diagnostic Studio shouldn't run under the remote account. It should run under the local account, but use the remote account for any remote access. Thankfully, Runas has just such a provision - its /netonly switch! The following command will open SharePoint Diagnostic Studio under the local account while using the remote account to access the remote server.

`runas /netonly /user:DOMAIN\USERNAME "C:\Program Files\Microsoft\SharePoint 2010 Administration Toolkit\SPDIAG.EXE"`  
(The Runas command will ask for a password before launching SharePoint Diagnostic Studio.)

Have fun!

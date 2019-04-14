---
title: 'VirtualBox Unidentified Network'
slug:  'virtualbox-unidentified-network'
date:  2012-02-02 10:08:00 -0500
---

**Thanks to Oisin Grehan and his Nivot Ink blog for providing the foundation of this post!**  
[VMWare VMNET Adapters Triggering Public Profile for Windows Firewall](http://www.nivot.org/post/2008/09/05/VMWareVMNETAdaptersTriggeringPublicProfileForWindowsFirewall.aspx)

-----

I use Oracle's VirtualBox to run x64 SharePoint virtual machines from my laptop. I've also noticed an Unidentified Network in my Windows 7 list of networks. That is caused by VirtualBox's Host-Only Network Adapter. It wasn't harming anything at the time so I left it alone.

However, I later attempt to enable PowerShell remoting on my host laptop for work with SharePoint scripting. Upon doing so, I was greeted with the following error message while attempting the Enable-PSRemoting cmdlet:

> Set-WSManQuickConfig : WinRM firewall exception will not work since one of the network connection types on this machine is set to Public. Change the network connection type to either Domain or Private and try again.

Another helpful error message! That seems easy enough; Windows makes it very easy to modify the settings for each individual network adapter to Private, Work, or Public depending on your personal preference. However, this is not the case with an unidentified network. With an unidentified network, Windows sticks to its Public settings and will not change it.

So can I now not enable PowerShell remoting since I can't remove the Public designation of VirtualBox's unidentified network? No! VirtualBox's Host-Only network isn't really a true network connection at all. It is an endpoint adapter. Kudos to Oisin Grehan for developing a nice PowerShell script that will solve the issue by telling Windows, via the registry, that the network adapter is an endpoint device and not a true external network connection. This will cause Windows to stop treating the VirtualBox Host-Only adapter as a network and thus remove the unidentified network (and its public designation) from my list of networks. Problem solved! I've modified Oisin's script to account for VirtualBox's Host-Only instead of VMware adapters.

**Note:** This script will **need to be executed every time VirtualBox is updated** because the update will replace the existing adapter and cause the settings in the registry to be lost.

<script src="https://gist.github.com/smayes5/7da1a3d001aedd9fe953.js"></script>

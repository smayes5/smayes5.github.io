---
title: 'Managing Customizations to ASP.NET & SharePoint Browser Definitions'
slug:  'managing-customizations-to-asp-net-and-sharepoint-browser-definitions'
date:  2011-11-02 14:17:00 -0500
---

*This article's purpose is to discuss the best practices around managing customizations to ASP.NET's Browser Definitions. For more details around ASP.NET's browser definition platform, please see the [Browser Definition File Schema (browsers element)](http://msdn.microsoft.com/en-us/library/ms228122.aspx) article on MSDN. A quick overview of ASP.NET Browser Definitions can be found in the box below.*

<div class="box">
	<p><b>ASP.NET Browser Definition Overview</b><br />
	In any IIS site, including SharePoint sites, Microsoft provides a highly configurable platform for defining the various capabilities and mobile adapters of a browser. To facilitate this, Microsoft uses what is called a Browser Definition File which is an XML-based file that defines browsers, what makes that browsers different from the rest (the Identification), and browser properties - capabilities and mobile adapters. Microsoft provides several definition files out-of-the-box with ASP.NET that define various browsers. Then, individual web applications can provide their own Browser Definition Files that will supplement or override the out-of-the-box files. ASP.NET uses this information to tailor page rendering based on the browser making the page request.</p>
	<p>
		ASP.NET allows for multiple files that all have the same schema. There are two sets of files that ASP.NET parses:
		<ol>
			<li><b>Predefined Browser Definition Files</b> - This set of files is specific to the version of the .NET Framework being used in the web application and contains the out-of-the-box ASP.NET browser definition files. <code>%SystemRoot%\Microsoft.NET\Framework\<i>version</i>\CONFIG\Browsers</code></li>
			<li><b>Application-Level Browser Definition Files</b> - This set of files is specific to each web application and contains the web application specific browser definition files. <code>App_Browsers</code> directory within the web application's web root folder (e.g. <code>inetpub</code> or <code>inetpub\wss\VirtualDirectories\80</code>)</li>
		</ol>
	</p>
	<p>Each individual browser is defined using a <code>&lt;browser&gt;</code> tag. Within this tag, there are several other allowed tags that define how a browser is uniquely identified and what unique attributes should be defined for that browser. Browser definitions follow an inheritance hierarchy. New definitions must define a parent definition or existing definitions can be modified using the definition's ID.</p>
	<p>ASP.NET loads each browser definition file by concatenating them - each browser file contains a series of browser tags. The predefined files are processed first based on the ASP.NET version being used followed by the application-level files. Within each file location, ASP.NET seemingly processes each file in alphabetical order by file name. This means that if there are multiple definitions for the same browser, it seems that the definition in the last file will take precedence.</p>
</div>

SharePoint chose to implement two browser definition files that are deployed locally to each SharePoint web application's `App_Browsers` directory. This is important as SharePoint deploys its custom browser definitions in the same way that other applications using SharePoint or ASP.NET should deploy them - as separate .browser files deployed to the application-level `App_Browsers` directory. These two files, which may look familiar, are:
- `compat.browser`, and
- `compat.moss.browser` (If using SharePoint Server editions)

As the Browser Definition File Schema article in MSDN stresses, **the predefined files that ship with the .NET Framework should never be modified** - ever! This is because they could be overwritten by updates, patches, or service packs; causing any customizations to be lost.

As an extension to this rule, **never modify the application-level browser definition files that ship with SharePoint**. Why? The same reason as with the predefined files. Future SharePoint updates, patches, or service packs could overwrite these files; causing any customizations to be lost.

So what is left if customizations are required? Thanks to the architecture of these browser definition files, Microsoft allows an application to define its own browser 
definition files. This is relatively straightforward, but it does require knowledge of the browser definition files you will be overriding. I will cover three scenarios:

1. Adding an entirely new browser definition
2. Appending information to an existing browser definition
3. Modifying an existing browser definition

As far as I know, there is no way to remove a browser definition. However, I'm not sure there would be a need or desire to do so.

**Adding an entirely new browser definition**  
To add a new browser definition, add the definition's browser element to a new browser definition file. Deploy this file locally into each web application's `App_Browsers` folder. This new file's definition(s) will be combined with the other predefined and application-level definitions.

**Appending information to an existing browser definition**  
To append capabilities, mobile adapters, or other information to an existing browser definition, add a new browser tag to a new browser definition file that uses refID to reference the existing browser's ID. Add the new information within this browser tag. This definition will be combined with the existing browser's definition, which could be in another file, and append the new information.

**Modifying an existing browser definition**  
To modify a capability, mobile adapter, or other information already defined as part of an existing browser definition, there are a few options. The first would be to use a similar process to appending information. It is possible to define browser definitions in a new file, using refID to reference the existing browser's ID, and define the same information with different values. The second would be to implement a new browser, with no additional identification, with the parent browser set to the browser that should be modified.

For example, if the IE browser should be modified to be treated as a mobile device, then the following browser definitions could be used:

**Option 1:**  
<code>&lt;browser refID="ie"&gt;<br />
&nbsp;&nbsp;&nbsp;&nbsp;&lt;capabilities&gt;<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;capability name="isMobileDevice" value="true" /&gt;<br />
&nbsp;&nbsp;&nbsp;&nbsp;&lt;/capabilities&gt;<br />
&lt;/browser&gt;</code>

**Option 2:**  
<code>&lt;browser id="ieMobile" parentID="ie"&gt;<br />
&nbsp;&nbsp;&nbsp;&nbsp;&lt;capabilities&gt;<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;capability name="isMobileDevice" value="true" /&gt;<br />
&nbsp;&nbsp;&nbsp;&nbsp;&lt;/capabilities&gt;<br />
&lt;/browser&gt;</code>

However, note that if multiple browser definitions exist that reference the same browser ID and each has a different definition of the same information, then the last definition in the last file alphabetically in the application-level will be used.

For example, assume Option 1 was implemented in both `MyApp1.browser` & `MyApp2.browser` and that each of those browser files was deployed to the application-level `App_Browsers` folder. MyApp1 defines IsMobileDevice=True and MyApp2 defines IsMobileDevice=False. Since MyApp2 comes last alphabetically, it will take precedence and IsMobileDevice will be False. This underscores the need for applications to consider the environment's existing customizations when developing custom browser definitions. In this example, the MyApp2 team, presumably deploying after MyApp1, should have seen MyApp1's existing customization and implemented a new child browser (Option 2) to avoid a conflict or change MyApp1's customization. The MyApp2 team would need to be aware that either way that is chosen for implementation will affect the entire web application, so they should consult the MyApp1 team!

**Summary**  
To properly manage customizations to the browser definitions that ship with ASP.NET and SharePoint, applications being deployed to a SharePoint environment should make use of their own browser definition files that are deployed to the application-level `App_Browsers` folder. However, if the environment is planning on host multiple sets of customizations or applications that will require changes to the browser definitions, I would encourage a single file for each web application to ensure that applications changes are developed properly and do not conflict with each other.

**Never** modify any browser definition file not directly managed by the application being deployed. To implement the changes to existing browsers within the application browser definition files, it is best to use the refID attribute of the browser tag.

When deploying, changes to the `App_Browsers` folder should automatically be picked up by IIS and *not* require any sort of application pool recycle or IISReset.

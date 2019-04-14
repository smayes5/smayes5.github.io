---
title: 'Passing Parameters to Delegate Controls'
slug:  'passing-parameters-to-delegate-controls'
date:  2012-03-13 16:41:00 -0400
---

I have a requirement for a [DelegateControl]("http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.webcontrols.delegatecontrol.aspx") where I need the child candidate control to render a link that opens in a new window in certain instances of the DelegateControl and open the link in the same window otherwise. The design that made the most sense to me was to use the same code for the child control to compute and render the link in both circumstances and pass a boolean parameter, `OpenLinkInNewWindow`, to control the target of the link.

The reader might be wondering why I need a control just to render a link. For the purposes of this article, just assume that the control has some built-in intelligence to render a link that cannot otherwise be easily generated!

But how would I pass a parameter to the child control that the DelegateControl uses? Unfortunately, the DelegateControl itself does not have the ability to accept parameters. However, the [Control element](http://msdn.microsoft.com/en-us/library/ms469179.aspx) that defines child controls for a DelegateControl, within the elements.xml file for a feature, *does* have the ability to accept parameters. Perfect!

To define parameters for a control, one can setup property name/value pairs using the [Property element](http://msdn.microsoft.com/en-us/library/ms455054.aspx), which is a child of the Control element. It's pretty straightforward!

**Example**  
Say I have an application page, master page, etc. that needs to put the my fancy link-generating control in two separate places: one opens the link in a new window and one opens the link in the same window. In my page's ASP.NET code, I have my two separate DelegateControls. I give each DelegateControl its own unique ControlId so that I can differentiate between the two.

<script src="https://gist.github.com/smayes5/7df3ebf886ed1b997001.js?file=DelegateControlExample.aspx"></script>

With the above DelegateControls, I can use the first one wherever I need the link to open in a new window and the second wherever I need the link to open in the same window.

I would then define the following in the elements.xml file within the feature that adds the children to these DelegateControls.

<script src="https://gist.github.com/smayes5/7df3ebf886ed1b997001.js?file=elements.xml"></script>

Notice that I'm using the **same control** for both of the DelegateControls. I'm only changing the value of the `OpenLinkInNewWindow` parameter. This will use the same code in both DelegateControls to render the desired link. This allows me to use the LinkNewWindow DelegateControl wherever I want the link opened in a new window and the LinkSameWindow DelegateControl wherever I want the link opened in the same window.

**_Update:_ Adding parameters to custom controls**  
After sharing this article, I had a few colleagues suggest that I add a quick update around how to allow custom controls to accept parameters in the method described above. Parameters for a control can be created by using public properties defined on the control.

<script src="https://gist.github.com/smayes5/7df3ebf886ed1b997001.js?file=LinkGenerator.cs"></script>

As long as the property is public, one can use the method outlined in this article to pass parameters when the control is used with a DelegateControl. If used in a more traditional context on an ASP.NET page, the public property could be used directly in the source code as follows.

<script src="https://gist.github.com/smayes5/7df3ebf886ed1b997001.js?file=LinkGeneratorExample.aspx"></script>

There are several attributes that can decorate the public property to enhance its functionality as a property of the control. See the **Attributes Applied to Public Properties** section of the [Metadata Attributes for Custom Server Controls](http://msdn.microsoft.com/en-us/library/ms178658.aspx) article on MSDN for details.

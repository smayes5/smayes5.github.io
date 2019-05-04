---
title: 'Discard Check Outs by the System Account'
slug:  'discard-check-outs-by-the-system-account'
date:  2011-08-03 12:58:00 -0400
---

SharePoint has a wonderful check-in/check-out system for any library with minor versioning enabled. Users with the **Override Check Out** permission on a particular library have the powerful ability to discard or check in a document, page, etc. that is checked out to another user. This can typically be easily achieved via any of the library's views using the out-of-the-box user interface.

<!--more-->

![Discard Check Out highlighted in an item's edit control block menu]({{ "/assets/images/2011-08-03-DiscardCheckOut1.jpg" | relative_url }})  
Any user with the proper permissions can check in or discard the check out using the item's edit menu.

If the System Account is the account that has the item checked out, users are no longer available to check in the item or discard the check out from the library's views, even with the **Override Check Out** permission!

![Edit Control Block menu under the System Account]({{ "/assets/images/2011-08-03-DiscardCheckOut2.jpg" | relative_url }})  
The options to check in or discard the check out are unavailable in the item's edit menu.

However, there is an area where this can be done: **Site Content and Structure**. One can navigate there by selecting **Manage Content and Structure** under the **Site Actions** menu. If you find the same item in that area, you will be able to check in the item or discard the check out.

![Item's context menu from within Site Content and Structure]({{ "/assets/images/2011-08-03-DiscardCheckOut3.jpg" | relative_url }})  
The missing options are available in the Site Content and Structure views.

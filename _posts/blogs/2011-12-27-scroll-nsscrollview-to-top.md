---
layout:     post
title:      Scroll NSScrollView(Mac) to Top
category:   blog
description: Scrolling NSScrollView to top is more difficult than what it looks like.
---

AppKit is old and is not as convenient as UIKit. But we have to use it on Mac app development.

"Scroll a scrollview to top" sounds easy, but actually it isn't. If you assign a documentView to a NSScrollView, you will see it scrolls to the bottom. (Very stupid!) The doc did not mention how to scroll to top. I found a way to control the vertical scroller. I can set the scroller to top, but the scrollView is still at bottom!

This post (http://stackoverflow.com/questions/4506391/nsscrollview-jumping-to-bottom-on-scroll) inspired me. I got the solution (of cause, I post my answer on stackoverflow too):

```
    // Scroll the vertical scroller to top
    
    if ([_scrollView hasVerticalScroller]) {
    
        _scrollView.verticalScroller.floatValue = 0;
    
    }
    
    // Scroll the contentView to top
    
    [_scrollView.contentView scrollToPoint:NSMakePoint(0, ((NSView*)_scrollView.documentView).frame.size.height - _scrollView.contentSize.height)];
```


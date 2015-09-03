---
layout:     post
title:      Implementing Fade-In Fade-Out animation in NSView on Mac OS X
category:   blog
description: A tutorial of Implementing Fade-In Fade-Out animation in NSView on Mac OS X
---

It is easy to implement Fade-In animation in UIView on iOS. We just need to change alpha value in the animation.

```
theView.alpha = 0.0f;
[UIView beginAnimations:@"fadeIn" context:nil];
[UIView setAnimationDuration:1.0];
theView.alpha = 1.0f;
[UIView commitAnimations];
```

But the following code does not work in NSView on Mac Snow leopard.

```
[theView setAlphaValue:0.0f];
[[theView animator] setAlphaValue:0.8f];
```

I found `addSubView` and `removeFromSuperview` can have fade-in fade-out effects.

```
[[theSuperview animator] addSubview:theView];
```

There is a problem. We cannot change the duration time in the following code:

```
CABasicAnimation *animation = [CABasicAnimation animation];
animation.duration = 3.0f;
[theSuperview setAnimations:[NSDictionary dictionaryWithObjectsAndKeys:animation, @"FadeIn", nil]];
[[theSuperview animator] addSubview:theView];
```

Now I have the solution.

Fade-in animation:

```
[NSAnimationContext beginGrouping];
[[NSAnimationContext currentContext] setDuration:2.0];
[[theSuperview animator] addSubview:theView];
[NSAnimationContext endGrouping];
```

Fade-out animation:

```
[NSAnimationContext beginGrouping];
[[NSAnimationContext currentContext] setDuration:2.0];
[[theView animator] removeFromSuperview];
[NSAnimationContext endGrouping];
```


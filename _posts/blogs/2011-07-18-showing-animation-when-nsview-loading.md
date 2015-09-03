---
layout:     post
title:      Showing an animation when NSView loading
category:   blog
description: An UIView of one of my iOS app shows a fade-in animation. I implemented this animation in viewDidLoad. When I was porting this app onto Mac OS X, I found there is no viewDidLoad.
---

An UIView of one of my iOS app shows a fade-in animation. I implemented this animation in viewDidLoad. When I was porting this app onto Mac OS X, I found there is no viewDidLoad.

I tried adding the fade-in animation in awakeFromNib, but no animation appeared.

My solution: 

Put the animation code in a method and call it after a short delay.

```
[self performSelector:@selector(animationShowButton) withObject:nil afterDelay:0.2];
```

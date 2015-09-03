---
layout:     post
title:      The way to stop UIView animations
category:   blog
description: The way to stop UIView animations
---

```
#import <QuartzCore/QuartzCore.h>

[self.view.layer removeAllAnimations];
```

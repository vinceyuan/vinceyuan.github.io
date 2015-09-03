---
layout:     post
title:      Programming with Core Animation on Mac
category:   blog
description: Core Animation for Mac OS X and the iPhone
---


Learning notes of Core Animation for Mac OS X and the iPhone

1\. The Simplest animation (CABasicAnimation)

Without animation: `[theView setFrame:newFrame];`

With animation: `[[theView animator] setFrame:newFrame];`

`[theView animator]` is the Animator Proxy which is simply finding an animation and then invoking it. The default animation is `CABasicAnimation`.

2\. CAKeyframeAnimation

With `CAfeyFrameAnimation`, you can define a series of key frames. CoreAnimation system will create animation based on these key frames. For example, we want to create an animation of moving an image to point A, and then B. We only need to create the key frames of A and B. `CAKeyframeAnimation` could be understood as a series of `CABasicAnimation`.


```
// Create the path
NSRect frame = [theView frame];
CGMutablePathRef thePath = CGPathCreateMutable();
CGPathMoveToPoint(thePath, NULL, NSMinX(frame), NSMinY(frame)); //the origin
CGPathAddLineToPoint(thePath, NULL, pointA.x, pointA.y); //Point A
CGPathAddLineToPoint(thePath, NULL, pointB.x, pointB.y); //Point B

// Create an animation
CAKeyframeAnimation *originAnimation = [CAKeyframeAnimation animation];
originAnimation.path = thePath;
originAnimation.duration = 2.0f;
originAnimation.calculationMode = kCAAnimationPaced;

// Add this animation
[theView setAnimations:[NSDictionary dictionaryWithObjectsAndKeys: originAnimation, @”frameOrigin”, nil]];

// Activate this animation
[[theView animator] setFrameOrigin:pointB]; //set the point of the last frame
```

3\. Animation Group

You can add a group of animations to create a complex animation. For example, we can move a picture and rotate it at the same time, and then move it back to original place.

```
// Create keyframe animation
CAKeyframeAnimation *frameAnimation = [CAKeyframeAnimation animationWithKeyPath:@”frame”];
NSRect start = theFrame;
NSRect end = NSInsetRect(theFrame, -NSWidth(start) * 0.5, -NSHeight(start) * 0.5);
frameAnimation.values = [NSArray arrayWithObjects:[NSValue valueWithRect:start], [NSValue valueWithRect:end], nil];

// Create rotation animation
CABasicAnimation *rotation = [CABasicAnimation animationWithKeyPath:@”frameRotation”];
rotation.fromValue = [NSNumber numberWithFloat:0.0f];
rotation.toValue = [NSNumber numberWithFloat:45.0f];

// Create animation group
CAAnimationGroup *group = [CAAnimationGroup animation];
group.animations = [NSArray arrayWithObjects:frameAnimation, rotationAnimation, nil];
group.duration = 1.0f;
group.autoreverses = YES;

// Add this animation
NSDictionary *animations = [NSDictionary dictionaryWithObjectsAndKeys:group, @”frameRotation”, nil];
[theView setAnimations:animations];

// Activate this animation
[[theView animator] setFrameRotation:rotation];
```

4\. Transition animation

If we need to let a view support transition, we need to enable ‘Core Animation Layer’ for this view in IB.

Cross Fade (Default transition animation)

Hide imageViewA and show imageViewB with fade animation

```
[[self animator] replaceSubview:imageViewA with:imageViewB];
```

Customized Transition

```
// Create the animation of Move-in transition
CATransition *trans = [CATransition animation];
trans.type = kCATransitionMoveIn;
trans.subtype = kCATransitionFromTop;

// Add this animation
self.animations = [NSDictionary dictionaryWithObject:trans forKey:@”subviews”];

// Activate this animation
[[self animator] replaceSubview:imageViewA with:imageViewB];
```


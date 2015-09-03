---
layout:     post
title:      Change the parameter order in Objective-C formatting string
category:   blog
description: When we do localizations, the problem is the words order of a language differs to other languages. For example, this sentence "Ted wants a scooter." may be something like "A Scooter is what Ted wants" in another language.
---

(I got this idea from cocoa programming for mac os x 3rd)

When we do localizations, the problem is the words order of a language differs to other languages. For example, this sentence "Ted wants a scooter." may be something like "A Scooter is what Ted wants" in another language.

You can localize strings this way:

```
NSString * theFormat = NSLocalizedString(@"WANTS", @"%@ wants a %@"); x = [NSString stringWithFormat:theFormat, @"Ted", @"Scooter"];
```

It works well in a language:

```
"WANTS" = "%@ wants a %@";
```

But we need to change the token order this way in another language:

```
"WANTS" = "A %2$@ is what %1$@ wants".
```

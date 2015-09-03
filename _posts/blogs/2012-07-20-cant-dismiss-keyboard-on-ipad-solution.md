---
layout:     post
title:      Can't dismiss keyboard on iPad (Solution inside)
category:   blog
description: Sometimes, you see the following code of dismissing keyboard not working on iPad but working on iPhone [textField resignFirstResponder];
---

Sometimes, you see the following code of dismissing keyboard not working on iPad but working on iPhone:

`[_textField resignFirstResponder];`

That's because a modal view in UIModalPresentationFormSheet mode on iPad ignores it by default.

To let it work, we need to add this method into your view controller:

```
- (BOOL)disablesAutomaticKeyboardDismissal {
    return NO;
}
```

If your view controller is in a navigation controller, add this method into your customised navigation controller.

```
- (BOOL)disablesAutomaticKeyboardDismissal {
    return [self.topViewController disablesAutomaticKeyboardDismissal];
}
```


---
layout:     post
title:      Keyboard notification bug on iOS 5
category:   blog
description: There is an annoying bug (or behavior) about non-English language keyboard on iOS 5.
---

When the soft keyboard appears, we need to re-arrange the UI controls. I move the control up a little bit in keyboardWillShow handler:

```
frame.origin.y -= CONSTANT;

control.frame = frame;
```

But there is an annoying bug (or behavior) about non-English language keyboard on iOS 5. If you are using English keyboard, you will receive keyboardWillShow notification once. It is good. But if you are using some language keyboard like Chinese, you will receive the same notification TWICE! That means the above code will move the control up twice.

Some non-English language keyboard has an additional line at the top, used to choose words. Sounds like that additional line causes this problem.

The solution is simple. Just use hard-coded positions. And the additional line on some non-English language keyboards may cover parts of your UI on iOS 5. You have to leave enough space for keyboards. If your textField requires English characters only, you can force the keyboard to show English:

```
textField.keyboardType = UIKeyboardTypeASCIICapable;
```


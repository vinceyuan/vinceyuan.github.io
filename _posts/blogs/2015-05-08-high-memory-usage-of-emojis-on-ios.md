---
layout:     post
title:      High memory usage of Emojis on iOS
category:   blog
description: Emoji is very popular. I am making a keyboard which contains many Emojis. However, I found the memory usage of Emoji is too high. When the keyboard's memory usage is high enough, iOS will kill the keyboard immediately.  (Looks like the limit is 40mb on iOS 8.3)
---

Emoji is very popular. I am making a keyboard which contains many Emojis. However, I found the memory usage of Emoji is too high. When the keyboard's memory usage is high enough, iOS will kill the keyboard immediately.  (Looks like the limit is 40mb on iOS 8.3)

The emojis are just unicode characters. Apple renders them with lovely icons, which causes the high memory usage. It's OK, because we all know icons use much memory. But the problem is after you have destroyed the view which uses Emoji, the memory of Emoji will not be released. Looks like iOS keeps the Emoji cache in the app or app extension. It's acceptable in an app, but not acceptable in an app extension.

What I have to do is to delete many emojis from my keyboard.

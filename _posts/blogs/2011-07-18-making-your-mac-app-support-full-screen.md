---
layout:     post
title:      Making Your Mac App Support Full Screen in 1 Minute
category: blog
description: Several steps to let a Mac App support full screen mode on OS X.
---

Full screen mode is a cool feature on Mac OS X Lion. I'd like to let my app support full screen. I googled, but did not find any tutorial. I thought there would be an option like'Support full screen' when a new project is created with the new Xcode 4.1 on Lion. Surprisingly, there is no that option. Today I watched a video of WWDC2011 and made this article. I am sure you can follow the steps to make your app support full screen in 1 minute.

I created a new Mac OS X Cocoa Application with Xcode 4.1 on Lion and named it MyFullScreen.

1\. Set Base SDK Mac OS X 10.7

2\. Choose MainMenu.xib in Project navigator

3\. Choose 'Window - MyFullScreen' in Objects

4\. Choose 'Primary Window' for Full Screen in Attributes inspector. You will see a new icon on the top-right corner of your main window. Run this app. It already supports full screen!

[![Make Full Screen](/images/201107/201107181942393108.jpg)](/images/201107/201107181942393108.jpg)  

5\. You need to add a menu item. Choose 'Menu - View', and then drag 'Full Screen Menu Item' in Object Library into 'Menu - View'.

[![Full Screen Menu Item](/images/201107/201107181943078708.jpg)](/images/201107/201107181943078708.jpg)  

We are done! It is very easy.

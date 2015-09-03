---
layout:     post
title:      Xcode 4 and Git
category: blog
description: Using Git with Xcode 4
---

I am new to Git. I used Perforce (P4) before, so I thought it is easy to use Git. Actually I was wrong. Git is not complex, but it has different concept.

In P4, we just check out and check in files. In Git, checking in has a new name Commit. When I committed my first change, I was surprised to see it does not appear in the server. Actually, Git did not really submit my code to the server. It was stored locally. I have to run this command in the command line:

`git push origin master`

So you must Push the local Commits to the server.

The problem is Xcode 4 can Pull, Commit, Compare, but does not provide Push functionality. How stupid the designer is. You can modify but cannot submit! Actually Pull, Commit and Compare are the only functionalities Xcode 4 provides. You cannot even revert!

Xcode 4 is free, but Apple should not make it suck.

Update on Nov 30, 2011:

SourceTree on Mac App Store is an excellent Git client. It is much better than GitX. And it is *FREE*!


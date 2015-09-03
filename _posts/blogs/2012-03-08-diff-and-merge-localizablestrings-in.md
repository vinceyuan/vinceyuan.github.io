---
layout:     post
title:      Diff and Merge Localizable.strings in git
category:   blog
description: The default code set of Localizable.strings is UTF-16. Actually I don't care what code set it is. But git/gitX/SourceTree does not recognize UTF-16 (Stupid!). They treat UTF-16 Localizable.strings as a binary file and reject to diff or merge. This is a very big problem if you work in a team.
---

The default code set of Localizable.strings is UTF-16. Actually I don't care what code set it is. But git/gitX/SourceTree does not recognize UTF-16 (Stupid!). They treat UTF-16 Localizable.strings as a binary file and reject to diff or merge. This is a very big problem if you work in a team.

Google says we can make git support UTF-16. I tried, but failed. I have to convert Localizable.strings into UTF-8. (Actually before converting, I have manually merged by myself.) Surprisingly, git/SourceTree still says it is a binary file and cannot diff. It's OK. Just commit. Git says it just because the history files are still UTF-16. If you make any new changes, git can diff and merge it. Great!

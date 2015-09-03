---
layout:     post
title:      Time Machine error (caused by Mac OS X Lion)
category:   blog
description: Fixing a weird Time Machine error.
---

My Time Machine did not work today. I saw the following message:

![Time Machine Error](http://farm6.static.flickr.com/5298/5518430255_13983fe085.jpg)

I tried rebooting my mac and reformatting my external disk. They did not help. My external disk looks very healthy. There must be something wrong in Time Machine software.

In order to see what happened, I ran this command in the terminal window:

```
sudo grep backupd /var/log/system.log
```

I got many errors like this:

```
Mar 12 09:21:50 localhost com.apple.backupd[432]: Error: (-36) SrcErr:YES Copying /.DocumentRevisions-V100/PerUID/501/1/com.apple.documentVersions/074F6D28-7E46-4BBD-A877-7896143B9D1A.rtf to (null)
```

`/.DocumentRevisions-V100` was created by AutoSave feature of Mac OS X Lion. I recalled I installed Lion on another partition and saved a file on Snow Leopard partition (to test AutoSave). Lion created that hidden folder on Snow Leopard partition and put the auto saved files there. However, the Time Machine software on Snow Leopard is not updated. It cannot recognize that system folder.

It is easy to fix this Time Machine error. Just exclude that hidden folder in Time Machine Preferences. (You need to enable 'Show invisible items'.) I actually excluded the following 3 hidden folders:`/.DocumentRevisions-V100` `/.MobileBackups` `/.Spotlight-V100`

![Exclude folders in Time Machine](http://farm6.static.flickr.com/5219/5518432145_87b6f74c60.jpg) 

My Time Machine is working again! This command `sudo grep backupd /var/log/system.log` is very helpful.

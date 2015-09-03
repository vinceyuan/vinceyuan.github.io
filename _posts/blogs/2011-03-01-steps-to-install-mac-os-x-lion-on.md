---
layout:     post
title:      Steps to install Mac OS X Lion on external hard drive
category:   blog
description: Apple just released Mac OS X 10.7 Lion developer preview. There are some new features inspired by iPad. The Launchpad looks like iOS springboard. The apps can run in the full-screen mode. You macbook with Lion will look like an iPad. However, many people including me only have one machine with Snow Leopard installed. We don't want our valuable data destroyed by the beta OS. It is not safe to upgrade the existing OS to Lion in the existing partition. Can we just install Mac OS X Lion on the external hard drive? Yes!
---

Apple just released Mac OS X 10.7 Lion developer preview. There are some new features inspired by iPad. The Launchpad looks like iOS springboard. The apps can run in the full-screen mode. You macbook with Lion will look like an iPad.

However, many people including me only have one machine with Snow Leopard installed. We don't want our valuable data destroyed by the beta OS. It is not safe to upgrade the existing OS to Lion in the existing partition. Can we just install Mac OS X Lion on the external hard drive? Yes!

Steps to install Mac OS X Lion on external hard drive:

1\. Plug in your external hard drive to your Mac.

2\. Open Utility-\>Disk Utility. You should see your external hard drive in the left view.

3\. Choose your external hard drive (not the partitions) in the left view.

4\. Choose Partition in the right view and create an GUID partition with Mac OS Extended (Journaled) format.

If your partitions were created in Windows, they are not GUID partitions. You have to delete all of them and create partitions with Mac OS Extended (Journaled) format. Don't forget to click Options button and choose GUID Partition Table! Otherwise, when you install Lion on this partition, you will receive an error message: this partition is not GUID partition.

![GUID](http://farm6.static.flickr.com/5254/5488334960_1f6355cee4.jpg)

5\. After you have created legal partitions, you can start installing Lion. Just in your snow leopard, double click mac os x lion 10.7 .dmg, In the pop-up window, double click "Install Mac OS X", and then click "Continue". Accept the agreement. You will see this window.

![Install Mac OS X Lion](http://farm6.static.flickr.com/5093/5488361568_fbed4ea3bb.jpg)

6\. Click "Show All Disks..." and choose your external hard drive.

7\. Follow the instructions on the screen to complete the installation. You will get Lion installed on your external hard drive. If you want to reboot to the snow leopard, just press Option key on the machine startup.

If you installed Lion on another partition and saved a file on snow leopard partition with AutoSave enabled, you may have error on Time Machine in snow leopard. Refer to this post.

---
layout:     post
title:      Can't change UITableView background colour on iPad (Solution Inside)
category:   blog
description: If your app is an universal app and you changed the background colour of an UITableView, you will see it works on iPhone but does not work on iPad.
---

If your app is an universal app and you changed the background colour of an UITableView, you will see it works on iPhone but does not work on iPad.

The reason is we can't modify the background colour of the backgroundView of the tableview on iPad. (I think it is a bug. But Apple does not fix it for years.)

Solution: create a new backgroundView.

```
_tableView.backgroundView = nil;
_tableView.backgroundView = [[[UIView alloc] init] autorelease];
_tableView.backgroundColor = [UIColor clearColor];
```


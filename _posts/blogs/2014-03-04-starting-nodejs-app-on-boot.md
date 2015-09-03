---
layout:     post
title:      Starting Node.js App Automatically on Boot
category:   blog
description: I deployed a node.js app on a server running on CentOS linux. I am using 'forever' to keep my node.js app alive. But when the server restarts, we need to let the app run automatically.
---

I deployed a node.js app on a server running on CentOS linux. I am using `forever` to keep my node.js app alive. But when the server restarts, we need to let the app run automatically.

We can modify `/etc/rc.local` to execute commands on boot. The commands should not run under root. And we should set the environment variable `NODE_ENV`.

```
sudo vi /etc/rc.local
```

Add this line at the end of the file. Change `USER_NAME` and `PATH_TO_PROJECT` to your own. `NODE_ENV=production` means the app runs in production mode. You can add more lines if you need to run more than one node.js app.

```
su - USER_NAME -c "NODE_ENV=production /usr/local/bin/forever start /PATH_TO_PROJECT/app.js"
```

Don't set `NODE_ENV` in a separate line, your app will still run in development mode, because forever does not get `NODE_ENV`.

```
# WRONG!
su - USER_NAME -c "export NODE_ENV=production"
```

Save and quit vi (press ESC : w q return). You can try rebooting your server. After your server reboots, your node.js app should run automatically, even if you don't log into any account remotely via ssh.

You'd better set `NODE_ENV` environment in your shell. `NODE_ENV` will be set automatically when your account `USER_NAME` logs in.

```
echo export NODE_ENV=production >> ~/.bash_profile
```

So you can run commands like `forever stop/start /PATH_TO_PROJECT/app.js` via ssh without setting `NODE_ENV` again.

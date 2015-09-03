---
layout:     post
title:      Let Several Apps Use the same Facebook App ID
category:   blog
description: If you want to let your user post something from your app onto Facebook, you need to register a Facebook app and call Facebook SDK to post messages. But if you have a free version and a paid version, you would like to share the same Facebook App ID.
---

If you want to let your user post something from your app onto Facebook, you need to register a Facebook app and call Facebook SDK to post messages. But if you have a free version and a paid version, you would like to share the same Facebook App ID.

Just follow Facebook tutorial to register Facebook App and config your apps. https://developers.facebook.com/docs/mobile/ios/build/

The only one thing I need to mention is you must leave 'iOS Bundle ID' empty.

From Facebook doc page:

`iOS Bundle ID` \- **(Optional)** You may set this to match the bundle ID for your iOS app. If you add this setting then the bundle ID of your app will be checked before authorizing the user. The benefit of this setting for SSO is if the user has already authorized your app they will not be asked to authorize it when they access your app from a new device.

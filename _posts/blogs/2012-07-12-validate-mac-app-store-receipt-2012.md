---
layout:     post
title:      Validate Mac App Store Receipt 2012
category:   blog
description: As many Mac developer did to validate Mac app store receipt, I use roddi's code (https://github.com/roddi/ValidateStoreReceipt Thanks!). But recently I found it does not work! The code fails to validate the sample receipt.
---

As many Mac developer did to validate Mac app store receipt, I use roddi's code (https://github.com/roddi/ValidateStoreReceipt Thanks!). But recently I found it does not work! The code fails to validate the sample receipt.

Because I just restored my machine from TimeMachine. I thought maybe there was something wrong in my dev environment. I spent a lot of hours to make sure my development provisioning and certificates are correct. But it still fails to validate the sample receipt.

I read Apple's doc carefully (https://developer.apple.com/library/mac/#releasenotes/General/ValidateAppStoreReceipt/\_index.html), and find the solution.

roddi's code is still working. You need not change it. (Just need to get the latest version)

Follow these steps (internet required):

1. Log out from Mac App Store app.
2. Remove `USE_SAMPLE_RECEIPT` flag from your project settings -> Preprocessor Macros.
3. Compile your project
4. Find this app in Finder
5. Double click it in Finder to run. Do not run it in Xcode.
6. The OS will ask you to log in with your Apple ID. Do not log in with your real iTunes account. You need to log in with the test account. Find it or create it in the iTunesconnect website.
7. The OS will say something like "Your app is broken. Download it in App Store". Ignore this message. If you "Show Package Contents" of this app in Finder, you will see there is a file `_MASReceipt/receipt`. The OS installed a development receipt. We will not need the old sample receipt any more. That's why we remove `USE_SAMPLE_RECEIPT` debugging flag.

Done. You can debug your app now.


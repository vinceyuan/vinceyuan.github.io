---
layout:     post
title:      A Weird Reason Why iOS App is Rejected
category:   blog
description: I made a new iPhone app and submitted to App Store. However, it is rejected because of 'Invalid Binary'.
---

I made a new iPhone app and submitted to App Store. However, it is rejected because of 'Invalid Binary'. And I received an email telling me this:

*iPhone 5 Optimization Requirement - Your binary is not optimized for iPhone 5. New iPhone apps and app updates submitted targeting iOS 6 and above must support the 4-inch display on iPhone 5 and must include a launch image with the -568h size modifier immediately following the <basename> portion of the launch image's filename. Launch images must be PNG files and located at the top-level of your bundle, or provided within each .lproj folder if you localize your launch images. Learn more about iPhone 5 support and app launch images by reviewing the iOS Human Interface Guidelines and iOS App Programming Guide.*

I have submitted several apps and all support iPhone 5. I know we should add Default.png, Default@2x.png, Default-568h@2x.png to an app. I noticed this sentence _"Launch images must be PNG files and located at the top-level of your bundle, or provided within each .lproj folder if you localize your launch images."_ The key is EACH .lproj folder.

I localized launch images for English, Simplified Chinese, Traditional Chinese. However, I used appirater which contains many localizations. My app was rejected because there are no launch images in these .lproj folders. So we can solve this problem by removing unnecessary localizations from appirater. (Another solution is localizing launch images for these localizations. But I don't want to duplicate too many images.)

Steps:

1. Open your project with Xcode.
2. Delete AppiraterLocalizable.strings, choose 'Remove Reference'.
3. Add files to 'Your Project'...
4. Choose appirater/en.lproj/AppiraterLocalizable.strings (Do not choose appirater/en.lproj folder)
5. Repeat Step 4 for other languages you need.
6. Clean your project and rebuild.

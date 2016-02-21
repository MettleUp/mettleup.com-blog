---
title: "How to: Install an iOS app for Ad Hoc testing"
date: 2016-02-21
author: andy
description: "Apple has provided a number of ways to distribute test builds for iOS devices. Here's the simplest."
tags: [ "howto", "ios", "iphone" ]
image: "/images/howto-install-ios-app-beta-ad-hoc/bg.jpg"
---

Apple has provided a solid system for building and deploying iOS apps for testing, but a lot of the documentation feels highly technical. In the first post in our "How to" series, we will show you how to install beta builds iOS apps on your iPhone, iPad or iPod touch.

## First things first

This post assumes that your iOS developer properly built the app with your device's UDID as part of the provisioning profile. If you use this method to try to install an iOS app that wasn't specifically built for your device, you're going to have some problems.

## Get the app into iTunes

Once you have the app file (usually named something like _MyNewApp.app_), you will need to add it to your local copy of iTunes. Just launch iTunes and then navigate to **File&#65515;Open**, find the .app file, and click the  **Open** button. This will copy the app file into your local library so you can put it on one or more of your devices.

![](/images/howto-install-ios-app-beta-ad-hoc/add-to-itunes.png)

## Add the app to your device

To actually put the app on your iPhone, iPad or iPod touch, start by clicking the devices icon on the top left of the window. It's the one that looks like an iPhone. Then find your app by selecting **Apps** from the Settings menu on the left. You will be shown a list of all apps that were either downloaded or backed up from a device on this computer. You will also find the beta build you installed in the previous step. Find it and click the **install** button next to it, followed by the **Sync** button in the lower right.

![](/images/howto-install-ios-app-beta-ad-hoc/select-for-installation.png)

## And that's it!

After clicking the sync button, your device will be loaded with the beta copy and it will be ready for testing. It's a simple process, but a little confusing if it's your first time doing it. Check back soon for the next in our [How To](/tags/howto) series on how to submit crash reports.
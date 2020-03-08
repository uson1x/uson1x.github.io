---
layout: post
title: "How to make watchOS-only application (no iOS counterpart)"
description: "Guide on how to make an app which is available only on the App Store for Apple Watch"
date:   2020-02-14 09:00:00 +0300
categories: watchkit
---

In this guide, we follow the process of creating a **watchOS-only** application, which doesn't have an iOS counterpart and available only on the App Store for Apple Watch.

## Create a new project in Xcode

Click File -> New -> Project (Shift+cmd+N), select **watchOS** tab and choose **Watch App**.

![Creating new watchOS project]({{site.baseurl}}/assets/2020-02-14-watchos-app-xcode-project.jpg)

Enter a name, hit Next and select a folder where you want to save your project.

## Create a new App ID in Developer Portal 

Create a new App ID in Apple Developer Portal here: [Apple Developer Portal New App ID page](https://developer.apple.com/account/resources/identifiers/bundleId/add/)

![Creating new App ID]({{site.baseurl}}/assets/2020-02-14-watchos-app-developer-portal-app-id.png)

## App Store Connect

In App Store Connect, go to My Apps, click **+**, then **New App** and select, counterintuitively, **iOS** platform.

![Creating new App in App Store Connect]({{site.baseurl}}/assets/2020-02-14-watchos-app-app-store-connect-new-app.png)

The last unusual thing you need to skip the usual **App Previews and Screenshots** section.

![Skip iOS Screenshots section]({{site.baseurl}}/assets/2020-02-14-watchos-app-app-store-connect-skip-ios-screenshots.png)

Scroll further and expand the **Apple Watch** section. That's where you are going to upload your screenshots.

![Upload watchOS screenshots in Apple Watch section]({{site.baseurl}}/assets/2020-02-14-watchos-app-app-store-connect-watchos-screenshots.png)

If you need a quick way to generate icon sizes for different Apple Watch, you can use service like App Icon Generator, but pay attention to compression artifacts which can appear on some icons: [App Icon Generator](https://appicon.co)

## Metronome for Watch

As a quick exercise I made this small haptic Metronome watchOS app which is distributed on the App Store for Apple Watch: [Metronome for Watch app](https://apps.apple.com/us/app/metronome-for-watch/id1498649562?ls=1)

![Skip iOS Screenshots section]({{site.baseurl}}/assets/2020-02-14-watchos-app-metronome-for-watch.jpg)

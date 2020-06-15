---
layout: post
title: "Rately: Theme System Journal app"
description: "iOS app for tracking your important activites, daily"
date:   2020-06-15 08:00:00 +0300
categories: my apps
---

## Background

In February 2019 I picked up a new habit: every evening I open my Google Sheet called "Everyday questions", go through the list of questions and mark them as 0, 1, or 2.

"Zero" means I did _nothing_ about it, "one" means I did _something_ and "two" means that I did _enough_.

Since then I've logged 3348 data points. 

This evening reflection gives me closure for the day, lets me see what I've accomplished, and even made me change my working schedule to a four-day working week. Before that, I rarely looked back at my day in detail, but now I'm doing it daily.

## Rately

You don't need much to start tracking your days: a diary, a spreadsheet or a database.

But what is the best way to solve a problem like that?

Of course, it's an app!

Please, welcome, [**Rately**](https://rately.cc).

![Rately app icon]({{site.baseurl}}/assets/2020-05-11-rately-splash-screen.png){: .half-page-image} ![Rately app icon]({{site.baseurl}}/assets/2020-05-11-rately-screenshot.png){: .half-page-image} 

Rately lets you track your most important activities daily.

Just create a new activity and start tracking.

## Neumorphic design

In case you miss skeuomorphic design from the pre-iOS 7 era, we have a treat for you.

In February, when I was struggling with design, [Mikhail Zakharov](https://www.producthunt.com/@mike_z3) approached me with this beautiful new **Neumorphic** style.

The trick is to use off-white background and black/pure white semi-transparent shadows.

![Rately Neumorphic design]({{site.baseurl}}/assets/2020-05-11-rately-neumorphism.png)

## SwiftUI

The app is built with the new SwiftUI technology that Apple released last year.

SwiftUI lets you build and iterate over the interface quickly. However, in my experience, it has a lot of rough edges.

My first UI iteration failed due to the lack of fine-control over gestures in SwiftUI. When one pan gesture started conflicting with another, I was not able to resolve this conflict and had to ditch the design completely.

Another annoying point that I encountered was the way you build animations.
There are lots of tutorials around the internet on how you can build different animations easily with SwiftUI.

However, the way you get to a sleek piece of SwiftUI code, in my experience, is often non-obvious.

## Subscriptions via RevenueCat

The app has a subscription in it, mostly to let users support the creators. 

And this time we implemented subscriptions via [RevenueCat](https://www.revenuecat.com). Let me tell you what a treat it is to use RevenueCat!

If you ever implemented server-side receipt verification for In-App Purchase subscriptions, you should know that it's quite a hassle to set everything up. Trials, renewals, verification, receipt storage, and on top of that, you still need to implement StoreKit integration locally. RevenueCat takes all that pain away.

There is some new RevenueCat terminology you need to learn, like entitlements and offerings, but it's simple and they have great documentation available.

## Get it now

Please, go to the App Store, try the app and let me know what you think!

[![Download on the App Store]({{site.baseurl}}/assets/Download_on_the_App_Store_Badge.svg)](https://apps.apple.com/app/rately-theme-system-journal/id1510322095)

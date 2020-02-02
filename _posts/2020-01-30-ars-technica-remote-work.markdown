---
layout: post
title: "How to make Apple Watch Haptic Engine continue playing in background"
description: "Guide on how to make Apple Watch Haptic Engine playing haptics when your app goes into background"
date:   2020-02-02 21:35:23 +0300
categories: apple watch, watchkit
---

If you are building an app where you want the haptics to continue playing when your Apple Watch app is sent to the background, you need to use the so-called "Extended session" (WKExtendedRuntimeSession).

For that: 
1. Head to your *WatchKit Extension* target settings
2. Click Signing & Capabilities
3. Click + Capability to add new Capability
4. Find and select "Background Mode" capability, double click it

In "Background Modes" capability select the "Session Type" that suits your app functionality. In this example, I'll use "Self Care"

![Configuring Background mode for WatchKit Extension]({{site.baseurl}}/assets/2020-02-02-haptic-in-background.jpg)

Then, when you start playing the haptics, you need to start the "Extended session":

```
func startSession() {
    session = WKExtendedRuntimeSession()
    session.start()
}
```

When it's time to end playing the haptics, you need to invalidate your extended session:
```
func stopSession() {
    session.invalidate()
}
```

There is one caveat though. You can't continue playing haptics if the user exits your app (by clicking on the Digital Crown).
In that case, watchOS stops your extended session and you need to reactivate it when the user comes back.

For that, we will use one of the `WKExtensionDelegate` methods `applicationDidBecomeActive`.
Let's imagine we are using a shared `HapticEngine` component to control our haptics. 

Then, in WKExtensionDelegate we could add something like that:

```
class ExtensionDelegate: NSObject, WKExtensionDelegate {
    func applicationDidBecomeActive() {
        // Restart our WKExtendedRuntimeSession
        HapticEngine.shared.startSessionIfNeeded()
    }
    
    // ... other methods
}
```

Here is a final HapticsEngine drop-in class that allows you to play haptics every second, even if the Apple Watch screen dims:
```
class HapticsEngine: NSObject, ObservableObject {
    static let shared = HapticEngine()

    private var timer: Timer?
    private var session = WKExtendedRuntimeSession()

    private var isPlaying: Bool { timer != nil }

    private func startSessionIfNeeded() {
        guard !isPlaying, session.state != .running else { return }

        session = WKExtendedRuntimeSession()
        session.start()
    }

    private func stopSession() {
        session.invalidate()
    }

    private func tick() {
        WKInterfaceDevice.current().play(.start)

        timer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: false, block: { [weak self] (_) in
            self?.tick()
        })
    }

    func startPlayinTicks() {
        timer?.invalidate()
        timer = nil

        startSessionIfNeeded()

        tick()
    }

    func stopPlayingTicks() {
        timer?.invalidate()
        timer = nil

        stopSession()
    }
}
```
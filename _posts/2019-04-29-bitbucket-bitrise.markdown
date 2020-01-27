---
layout: post
title: "Bitbucket + Bitrise: Configuring Continuous Integration for an iOS app"
description: "How to configure Continuous Integration setup using Bitbucket and Bitrise"
date:   2019-04-29 21:00:00 +0300
categories: continuous integration
canonical_url: https://bitbucket.org/blog/bitbucket-bitrise-configuring-continuous-integration-for-an-ios-app

---

When iOS applications start growing, at some point it becomes essential to have a quick develop-release-test feedback loop. You can create this loop by doing everything manually, but it can be much quicker and more advanced if you use Continuous Integration (CI) tools.

With a CI tool, you can build up a history of releases and quickly see which build contained what. You can run tests for every build automatically and catch some inevitable bugs. You can have consistency in your release notes. And you can streamline your release cycles, which automates your checklists.
Sound interesting? Let’s try to build this feedback loop using Bitbucket Webhooks, Bitrise and fastlane.

For a couple of months, I’ve been tracking my time during the weekdays. I tried to track as much as I could, from the moment I wake up to the moment I go to bed.

Deployment flow
===
The flow we are going to use for our Continuous Integration is going to look like this:

1. Create & merge a Pull Request in Bitbucket
2. [Bitbucket](https://bitbucket.org/product) performs a "Webhook" HTTP request to Bitrise
3. [Bitrise](https://www.bitrise.io/) starts building the process and launches fastlane
4. [fastlane](https://fastlane.tools/) builds the app and sends it to App Store Connect
5. App Store Connect processes the build, and it becomes available in TestFlight

Bitbucket Webhooks and git branching model
===
Each deployment starts from us creating a Pull Request.

Let's say your team is using master git branch for code in the releasable state. It also makes new releases by merging this master branch to the release branch.

The following section describes how to create a Webhook manually. However, if you use Bitrise, it can create a Webhook for you automatically, so, feel free to skip to the Bitrise section.


Manual Webhook configuration
===
Next, let's configure Bitbucket Webhooks so that whenever someone pushes to release branch or merges Pull Request to release branch, the Webhook is triggered.

To do that go to your Bitbucket repository and click "Settings" in the side menu. Then click "Webhooks" in the "Workflows" section and then click "Add webhook."

Fill out Title, URL (see below), set Status to Active, and select "Choose from a full list of triggers" for Triggers. The triggers we are going to use are:

1. Repository: Push
2. Pull Request: Created, Updated

To get the URL for our Webhook:

1. Head over to Bitrise, create a new app
2. Open the Dashboard -> Your app -> Code tab
3. Scroll to Incoming Webhooks section and click Setup Manually.
4. Select "Bitbucket Webhooks" and copy the Webhook URL

![Configuring webhook in Bitbucket]({{site.baseurl}}/assets/2019-04-29-bitbucket-webhooks.png)

Bitrise and automatic Webhook configuration
===
Bitrise is a platform for Continuous Integration. You can configure different deployment "workflows" in it and have the Bitrise servers build and publish your application. Here are the steps to create a new deployment workflow for our CI setup.

{% include youtubePlayer.html id="UeIekFewrKM" %}

Bitrise is a platform for Continuous Integration. You can configure different deployment “workflows” in it and have the Bitrise servers build and publish your application. Here are the steps to create a new deployment workflow for our CI setup.
1. First sign up in Bitrise, go to Dashboard, click “Add New App”
2. Select “Private” if you want your config and logs to stay private
3. Select Bitbucket and connect it to your account
4. Click “Auto-add SSH key” or configure SSH access manually
5. Enter `release` as branch name in the “Choose branch” step
6. In Project build configuration select “fastlane” , check that Fastlane lane is set to `ios release`.
7. Select the stack that you normally use to build your app or just the latest available Xcode/macOS and click Confirm.
8. In the last step “Webhook setup” click “Register Webhook for me”

The last step creates a Webhook in Bitbucket, so you don’t have to do anything manually. You can head over to Bitbucket repository and check the Webhook configuration in Settings -> Webhooks.
In our setup, we are going to use fastlane to build and publish the app to App Store Connect.

Fastlane configuration
===
Fastlane is a set of tools for automating development and release process.

Follow this guide to install fastlane: [Setup — fastlane docs](https://docs.fastlane.tools/getting-started/ios/setup)

In short, you need to install Xcode development tools:

```
xcode-select --install
```

and then install fastlane via RubyGems
```
sudo gem install fastlane -NV
```
or via brew:
```
brew cask install fastlane
```
Then open the working directory of your app in the Terminal and initialize fastlane.
```
cd /path/to/your/app fastlane init
```
Select “3. 🚀 Automate App Store distribution”

Then follow the configuration requests. Fastlane can create and configure the new App Id for you and create a sample deployment “lane.” Lane is just a collection of steps, required to complete some scenario.

Once the configuration is finished, let’s open the Fastfile which has been created and configure our first deployment script. Fastlane has large set of tools for automating various processes like code signing, uploading of screenshots, running tests and so on. However, we are going to start with a simple setup:

```
default_platform(:ios)
platform :ios do 
  desc "Push a new release build to the App Store" 
  lane :release do 
    build_app(scheme: "CITest") 
    upload_to_app_store(force: true, skip_metadata: true, skip_screenshots: true) 
  end 
end
```
The “release” lane will
1. Perform a build of your project build_app(scheme: “CITest”)
2. Upload the resulting ipa file to App Store Connect upload_to_app_store. In this guide, we are skipping Fastlane metadata upload.

You can test your setup by opening your project directory in Terminal and running fastlane release:
```
cd /path/to/your/app/directory fastlane release
```

Code-signing
===
If you see problems with code-signing start here: Troubleshooting — fastlane docs. You can use fastlane match to manage code signing, but be careful: if you already have generated Certificates and Provisioning Profiles, match can break things. However, if it’s a completely new setup or you don’t care much about the existing profiles, match is going to speed things up considerably.

We’ll use fastlane match in our example:
```
cd /path/to/your/app/directory 
fastlane match development
fastlane match adhoc
fastlane match appstore
```

Then open the Xcode, turn off Automatic code signing and select provisioning profiles that match has generated.

After that, we can add match to our Fastfile:
```
default_platform(:ios) platform :ios do
  desc "Push a new release build to the App Store" 
  lane :release do 
    match(type: "appstore", readonly: true)
    build_app(scheme: "CITest")
    upload_to_app_store(force: true, skip_metadata: true, skip_screenshots: true) 
  end 
end
```

|

Finishing up the Bitrise setup
===
![Setting secrets in Bitrise]({{site.baseurl}}/assets/2019-04-29-bitrise-config.png)

Bitrise is building the project on its servers which don’t have any of your passwords and credentials required to code-sign and upload your app to App Store Connect. Therefore we’ll have to share some of those, precisely these two:
1. Login/password for App Store Connect user
2. Password to decrypt your match repository (if you use match)

The App Store Connect user doesn’t have to be the one you use to control your apps. You can create a new user in App Store Connect, which only has access to the app you automate and has at least a Developer role.

Once you set your new App Store Connect user, head over to Bitrise and open Workflow Editor tab and then Secrets. Add two new secrets:
ITUNES_CONNECT_USER and ITUNES_CONNECT_PASSWORD with App Store credentials for this new user. Plus put the same password into FASTLANE_PASSWORD secret.

If you use match, then add one more secret called MATCH_PASSWORD with the password you used to encrypt match repository.

Conclusion
===
That should be it. Try to create a new Pull Request and merge it and see if Bitrise triggers the new build. If everything goes well, you will see the new build in TestFlight and will be able to select it for your new iOS app version.

![Successful build in Bitrise]({{site.baseurl}}/assets/2019-04-29-bitrise-green-build.png)

There is much more that you can do with automated deployments, such as:
1. Testing using a fastlane scan
2. Automated build number incrementation
3. dSYM uploads to Crashlytics_Raygun_etc

However, start with simple things first. I hope this guide helps you!
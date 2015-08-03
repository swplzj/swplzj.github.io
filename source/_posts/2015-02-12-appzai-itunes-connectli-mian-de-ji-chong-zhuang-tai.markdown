---
layout: post
title: "App在iTunes Connect里面的几种状态"
date: 2014-05-04 20:20:41 +0800
comments: true
categories: 
---


#### Waiting for Upload (Yellow) 
Appears when you’ve completed entering your metadata, however, you have not finished uploading your binary or have chosen to upload your binary at a later time. Your app must be in the Waiting For Upload state before you can deliver your binary through Application Loader.

<!--more-->

#### Prepare for Upload (Yellow) 
Appears when you have created a new version, but you have not yet clicked the Ready to Submit Binary button. This state also indicates that you can now deliver your binary through Application Loader.

#### Upload Received (Yellow) 
Appears when your binary has been received through Application Loader, but is still being processed by the iTunes Connect system.

#### Invalid Binary (Red) 
Appears when a binary is received through Application Loader, has been processed, but your binary is invalid.  Examples of an invalid binary are: your binary icon does not meet our requirements, you have placed the payload directory at the wrong level in the .app wrapper, you attempted to use a non-increasing CFBundleVersion, etc.

#### Missing Screenshot (Red) 
Appears when your app is missing a required screenshot for iPhone and iPod touch or iPad for your default language app or for your added localizations. At least one screenshot is required for both iPhone and iPod touch and for iPad if you are submitting a universal app.

#### Waiting for Review (Yellow) 
Appears after you submit a new application or update and prior to the application being reviewed by Apple. This status means that your app has been added to the app review queue, but has not yet started the review process.

#### Waiting For Export Compliance (Yellow) 
Appears when your CCATS is in review with Export Compliance.

#### In Review (Yellow) 
Appears when we are reviewing your app prior to the application being approved or rejected. It takes time to review binaries so we appreciate your patience and ask that you allow sufficient time for the processing of your application.  When the status of your application is in review, you have the option to reject the binary you have submitted by clicking Reject Binary. This will remove your binary from the review queue and will allow for another update to be submitted. If you reject your binary, the status of your app will change to Developer Rejected and when your binary is re-submitted, the review process will start over from the beginning.

#### Pending Contract (Yellow) 
Appears when your application has been reviewed and is Ready for Sale but your contracts are not yet in effect. You may check the progress of your contracts in iTunes Connect by clicking on the Contracts, Tax & Banking information module.

#### Pending Developer Release (Yellow) 
Appears when the version of your app has been approved by Apple and you have turned on the Version Release Control, but have not yet clicked Send Version Live. You should also see a pending action symbol on the version. Your version will remain in this state, and thus will not be live on the App Store until you click Send Version Live.

#### Processing for App Store (Yellow)
Appears when the version is being processed to go live on the App Store. Once the processing is complete, the version state will change to “Ready for Sale.” This is a temporary state (approx. 1 – 2 hours).

#### Ready for Sale (Green) 
Appears once your application been approved and posted to the App Store. When your application is in this status, you have the option to remove it from the store by going to the Rights and Pricing page and removing all App Store territories.

#### Rejected (Red) 
Appears when the binary has been rejected.

#### Removed from Sale (Red) 
Appears when the binary has been removed from the App Store.

#### Developer Rejected (Red) 
Appears when youʼve rejected the binary from the review process. Existing versions of your application on the App Store will not be affected by self-rejecting binaries in review.

Important: When you self-reject your binary, you lose your place in the review queue. Your binary will be placed at the end of the queue when you resubmit.

#### Developer Removed from Sale (Red) 
Appears when you’ve removed your application from the App Store.

 
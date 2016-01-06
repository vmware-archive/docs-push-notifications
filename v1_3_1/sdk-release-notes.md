---
title: Push Notification Service Android and iOS Client SDKs v1.3.1 Release Notes
---

The Push Notification services allows application developers to publish push notifications to devices on various platforms. Integration is done through provided SDKs which implement the device registration flow.

##SDK Documentation and Setup Guides

 - [iOS](../ios/index.html)
 - [Android](../android/index.html)

##Dependencies

 - iOS 7.0+ with Xcode 6.2+
 - Android 4.0+ with Android Studio 1.2+
 - PCF Push Notification Service 1.3.0+ (if you are using geofences)
 - PCF Push Notification Service 1.2.0+ (if you are not using geofences)

##Known issues

### iOS

 - The iOS SDK only supports 20 geofences monitored at one time.
 - The iOS SDK is not compatible with any other library or app trying to monitor geofences in the same app at the same time. If there are other geofences being monitored by an app then the Push Client SDK will delete them.
 - The iOS SDK will not always trigger a geofence if the device is currently inside of it at the same time that the geofence is registered (or when the geofence's tag is subscribed to).
 - Tags can't be created on the dashboard until some device has subscribed to the tag.

### Android

 - The Android SDK only supports 100 geofences monitored at one time.
 - The Android SDK is not compatible with any other library or app trying to monitor geofences in the same app at the same time. If there are other geofences being monitored by an app then the Push Client SDK will delete them.
 - Geofences are not monitored after the user turns Location Services off and on again.
 - Tags can't be created on the dashboard until some device has subscribed to the tag.

## List of Changes

 - SSL Certificate pinning
 - Any geofences with tags will be monitored only if the user is subscribed to that tag

## Upgrading from version 1.3.0

 - There are no breaking changes from 1.3.0 to 1.3.1.
 - The only new parameter is `pinnedSslCertificateNames`.  This parameter is optional and does not need to be added to your configuration file unless you need to use it.  For instructions on how to use this parameter, please see the [iOS](../ios/index.html) and [Android](../android/index.html) SDK documentation.

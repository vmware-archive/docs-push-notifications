---
title: Push Notification Service Release Notes
---

## 1.4.7
**Release Date: May 2016**
 - Security release requiring stemcell 3232.2

## 1.4.5
**Release Date: May 2016**
 - PCF 1.7 compatibility.
 - Please update to this version of push _before_ updating to PCF 1.7.0

## 1.4.3
**Release Date: March 2016**
 - Security release requiring stemcell 3146.10.

## 1.4.2
**Release Date: February 2016**
 - Security release requiring stemcell 3146.8.

## [1.4.0](v1_4_0/release-notes.html)
**Release Date: November 2015**

 - The Push Notifications Service now supports multiple tenants.
   - Push Notifications is now a service that can be provisioned from the CF Marketplace.
   - The dashboard now requires a Tenant Id.
 - The dashboard now displays logs related to push activities.
 - The analytics system now configures a second Redis to behave as a cache for storing logs.
 - Update to the Push SDK supports iOS 9 and includes a Swift sample app.
 - The Push SDK for Android now supports Android 6.0 Marshmallow, including the new permissions system.
   - See the Push Sample app for an example of Android 6.0 Marshmallow permissions.

## [1.3.5](v1_3_5/release-notes.html)
**Release Date: October 2015**

 - Support for PCF 1.6 and Diego.
 - SOCKS proxy bug fix.

## [1.3.4](v1_3_4/release-notes.html)
**Release Date: October 2015**

 - Bugfixes for smoke tests.

## [1.3.3](v1_3_3/release-notes.html)
**Release Date: September 2015**

 - Bugfixes for certain scenarios regarding expiry time.

## [1.3.3 iOS and Android Client SDK](v1_3_3/sdk-release-notes.html)

- Push app analytics.
- Custom HTTP request headers.
- Custom SSL authentication.

## [1.3.2](v1_3_2/release-notes.html)
**Release Date: August 2015**

 - Deprecated lucid64 stack in favour of the new Trusty/cflinuxfs2 stack
 - Proxy Support for iOS push notifications.  Supports SOCKS proxies.
 - Proxy Support for Android push notifications.  Supports HTTP and SOCKS proxies.

## [1.3.2 iOS and Android Client SDK](v1_3_2/sdk-release-notes.html)

- Enable and disable geofences at runtime.
- Added a method to read the device UUID at runtime.

## [1.3.1](v1_3_1/release-notes.html)
**Release Date: August 2015**

  - Support for RabbitMQ Service versions 1.4.0 and higher
  - Tag management added to dashboard
  - Ability to regenerate push api keys
  - Minor improvements to installation
  - Allow certificate checks to be disabled in cf environments that use self signed certificates

## [1.3.1 iOS and Android Client SDK](v1_3_1/sdk-release-notes.html)

- SSL Certificate pinning.
- Any geofences with tags will be monitored only if the user is subscribed to that tag.

## [1.3.0](v1_3/release-notes.html)
**Release Date: June 2015**

- Location based notifications
- Android and iOS support (SDKs)
- Dashboard support
	- Maps
	- Saved locations and groups of locations
	- Active geofences view

[Upgrading from version 1.2.x to 1.3.0](v1_3/release-notes.html)

## 1.2.1 ##
**Release Date: April 2015**

- Offline installation support

## [1.2.0](v1_2/release-notes.html) ##
**Release Date: March 2015**

- Scheduled push notifications
- Notifications with expiry time
- Updated UI/UX for dashboard (sending scheduled push with expiry time)

## [1.1.0 - January 2015](v1_1/release-notes.html) ##

## [1.0.1 - November 2014](v1_0_1/release-notes.html) ##

## [1.0.0 - July 2014](v1_0/release-notes.html) ##

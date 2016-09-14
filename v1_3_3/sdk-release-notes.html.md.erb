---
title: Push Notification Service Android and iOS Client SDKs v1.3.3 Release Notes
---

The Push Notification services allows application developers to publish push notifications to devices on various platforms. Integration is done through provided SDKs which implement the device registration flow.

## SDK Documentation and Setup Guides

 - [iOS](../ios/index.html)
 - [Android](../android/index.html)

## Dependencies

 - iOS 7.0+ with Xcode 6.2+
 - Android 4.0+ with Android Studio 1.2+
 - PCF Push Notification Service 1.3.2+ (if you are collecting analytics data)
 - PCF Push Notification Service 1.3.0+ (if you are using geofences)
 - PCF Push Notification Service 1.2.0+ (if you are not using geofences)

## Known issues

### iOS

 - The iOS SDK only supports 20 geofences monitored at one time.
 - The iOS SDK is not compatible with any other library or app trying to monitor geofences in the same app at the same time. If there are other geofences being monitored by an app then the Push Client SDK will delete them.
 - The iOS SDK will not always trigger a geofence if the device is currently inside of it at the same time that the geofence is registered (or when the geofence's tag is subscribed to).
 - iOS 7.0 does not support being used as a framework.  If you target iOS 7 then you must build from source (e.g.: via Cocoapods).
 - If a remote notification does not have the `"content-available":1` field in its payload and if the user does not touch the notification then there will be no analytics event logged for receiving the notification when the application is in the background (since iOS does not call the application for the remote notifications in the background without `"content-available":1`).

### Android

 - The Android SDK only supports 100 geofences monitored at one time.
 - The Android SDK is not compatible with any other library or app trying to monitor geofences in the same app at the same time. If there are other geofences being monitored by an app then the Push Client SDK will delete them.
 - Geofences are not monitored after the user turns Location Services off and on again.
 - The Android SDK does not support the new Google Play Services APIs for registering for push notifications.
 - The Android SDK is not yet compatible with Android M permissions (i.e.: using the `InstanceID` service and the `google-service.json` file).

## List of Changes

 - Added simple push analytics - reporting of the following kinds of events at runtime:
   - Receiving push notifications.
   - Opening push notifications.
   - Triggering geofences.
 - Custom HTTP request headers.
 - Custom SSL authentication.

## iOS and Android Upgrade Notes

- There is one **breaking change** in this release.  We have removed the `pivotal.push.trustAllSslCertificates` parameter from the `Pivotal.plist` (iOS) and `pivotal.properties` (Android) files.  We have replaced it with the `pivotal.push.sslCertValidationMode` property.

If you were using the `pivotal.push.trustAllSslCertificates` parameter prior to Push SDK 1.3.3 then you must set `pivotal.push.sslCertValidationMode` to `trustall` to have the same functionality.

The `pivotal.push.sslCertValidationMode` property can be one of four values:

_default_  : Use the system-default SSL validation mode.  This mode generally only works with valid signed certificates on the server.

_trustall_ : Implicitly trust all certificates on servers. This mode may be useful in development when using self-signed certs but should not be used in production apps.

_pinned_   : "Pin" some certificates in the client and ensure that the server certificate matches one of them.

  On iOS you need to add each certificate in DER format to your project and add them in the `pivotal.push.pinnedSslCertificatesNames`
  parameter as an array property.  Each certificate filename should be listed as one string value in the array property.

  On Android you need to add each certificate in DER format to your project's "assets" directory and list them
  in the "pinnedSslCertificateNames" property as a space-separated list.

_callback_ : The "callback" mode is useful if you are using a special authorization scheme that the above options do not support.

  On iOS you provide a callback for custom SSL authentication. The callback must be a block that
  receives the arguments `(NSURLConnection *connection, NSURLAuthenticationChallenge *challenge)` and will be called when
  attempting to make an HTTPS network request.

  In order for this method to take effect you will need to call it both *before* `[PCFPush registerForPCFPushNotificationsWithDeviceToken:...]`
  and also *before* `[PCFPush didReceiveRemoteNotification:...]`.

  example:

```objective-c
    @implementation AppDelegate

        ...

        - (PCFPushAuthenticationCallback) getAuthenticationCallback
        {
            return ^(NSURLConnection *connection, NSURLAuthenticationChallenge *challenge) {
                // Handle the SSL challenge here!
            };
        }

        - (void) application:(UIApplication *)app didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
            [PCFPush setAuthenticationCallback:[self getAuthenticationCallback]];
            ...
            [PCFPush registerForPCFPushNotificationsWithDeviceToken:deviceToken ... ];
        }

        - (void)application:(UIApplication *)app didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            [PCFPush setAuthenticationCallback:[self getAuthenticationCallback]];
            ...
            [PCFPush didReceiveRemoteNotification:userInfo completionHandler: ... ];
        }

        ...

    @end
```

  Please see Apple's documentation for the [NSURLConnectionDelegate connection:willSendRequestForAuthenticationChallenge]
  (https://developer.apple.com/library/mac/documentation/Foundation/Reference/NSURLConnectionDelegate_Protocol/index.html#//apple_ref/occ/intfm/NSURLConnectionDelegate/connection:willSendRequestForAuthenticationChallenge:)
  method for more information on how to handle the callback.

  On Android you will need to create your own implementation of a class extending the
  `CustomSslProvider` interface and declare it in your manifest file in a `<meta-data>` element in
  your `<application>` element.  The name of the meta-data is
  "io.pivotal.android.push.CustomSslProvider" and the value of the meta-data should be the name of
  your custom SSL provider class (with its full package name).  This class must have a default
  (empty) constructor and will be instantiated at runtime when network requests are made to HTTPS
  service endpoints.

  example CustomSslProvider implementation:

```java
    public class MyCustomSslProvider implements CustomSslProvider {

        public MyCustomSslProvider() { /* Default constructor is required */ }

        @Override
        public SSLSocketFactory getSSLSocketFactory() throws NoSuchAlgorithmException, KeyManagementException {

            TrustManager[] trustAllCerts = new TrustManager[] { FILL ME IN };

            SSLContext context = SSLContext.getInstance("TLS"); // or "SSL" - please look at the Java documentation
            context.init(null, trustAllCerts, null);

            return context.getSocketFactory();
        }

        @Override
        public HostnameVerifier getHostnameVerifier() { FILL ME IN }
    }
```

  example AndroidManifest.xml:

```xml
    <application>

        ...

        <meta-data
            android:name="io.pivotal.android.push.CustomSslProvider"
            android:value="YOUR PACKAGE NAME.MyCustomSslProvider"/>

        ...

    </application>
```

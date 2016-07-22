---
title: Push
---

## <a id="push_create"></a> POST /v1/push ##

Push a message out to a list of devices or devices targeted by platform.

Authentication: HTTP Basic application\_uuid:api\_key

Query Parameters: None

### Request Body ###

There are many possible options for the request body.  All of the
options are listed in the JSON text example below.  Note that most of
the individual JSON fields are optional.  The options you need to use
are described below.  Several examples are illustrated below.

The __message__ &rarr; __body__ field in the JSON request body is the
_default_ message that is supplied in notifications to remote devices.  It
will be overridden by any platform-specific __custom__ message body data.

In particular, iOS devices will receive the __message__ &rarr; __body__
field as their alert message unless the __custom__ &rarr; __ios__ &rarr;
__alert__ &rarr; __body__ field is populated.  Android devices will
receive the __message__ &rarr; __body__ field in their payload
__message__ field unless the __custom__ &rarr; __android__ &rarr;
__message__ field is populated.

#### All options in request body ####

```JSON
{
  "message": {
    "body": "Message body",                  # The text of the push message
    "custom": {
      "ios": {
        "alert": {
          "body": "iOS only message body",   # The body of the push message
          "action-loc-key": "actionKey",     # (overrides body defined above)
          "loc-key": "localizedStringKey",
          "loc-args": [ "arg1", "arg2", ... ],
          "title": "Title",
          "title-loc-key": "titleKey",
          "title-loc-args": [ "arg1", "arg2", ... ],
          "launch-image": "Default.png"
        },
        "category": "SAMPLE_CATEGORY",
        "badge": 1,
        "sound": "default",
        "content-available": true,           # Note: the Push API expects this field to be a boolean. (see below)
        "extra": {}
      },
      "android": {
        "collapse_key": "collapseKey"
      },
      "windows8": {
        "template_name": "TileSquareBlock",
        "template_fields": {
          "textField1":"text1",
          "textField2":"text2"
        },
        "options": {},
        "type": "tile"
      },
      "windowsPhone": {
        "template_fields": {
          "subtitle":"text",
          "parameter":"text"
        },
        "template": "toast"
      },
      "bb10": "BB10 only message body"
    }
  },
  "target": {
    "tags": [ "tag1", "tag2", ... ],
    "platforms": [ "platform1", "platform2", ... ],
    "devices": [ "device_uuid1", "device_uuid2", ... ],
    "interactive-only": false,     # Either true or false
    "platform": "all"              # One of the following options
  },                               # (ios, android, windows8, windowsPhone, bb10)

  "scheduleAt": 1345852800000,     # Epoch timestamp in milliseconds.
  "scheduleIn": 0,                 # Integer (time delta in seconds)
  "expiryTime": null               # Epoch timestamp in milliseconds.
}
```

### Response Data, status: 200 (OK) ###

The fields returned by the /v1/push POST API depend on the type of push
notification that was requested: _scheduled_ or _immediate_.

If the push is given __scheduleAt__ or __scheduleIn__ fields then the push is
_scheduled_ to be delivered in the future.  These pushes will return a
__schedule_id__ field in the response data.  These __schedule_id__ values can
be used in the [/v1/schedule](../schedules/index.html) APIs to update or cancel
the scheduled push before it is delivered.


Otherwise the push will be queued to be sent _immediately_.  In this case,
the response will also contain a __receipt_id__ field that can be used
to follow the push notification delivery status in the audit logs.

#### Response Data ####
#
```JSON
{
  "schedule_id": "",  # only returned if the push notification is a scheduled push
  "receipt_id": ""    # only returned if the push is being delivered immediately.
}
```
_Scheduled_ pushes are described further [below](#scheduled_pushes).

## <a id="targeting_selection"></a> Targeting / Audience Selection ##

You can target your push notifications in many ways:

 - By platform(s). e.g.: "ios", "android".

 - By device UUID(s):
   * Devices UUIDs are determined by:
     + the device registration process in the PCF Push Client SDKs. See the documentation for the [iOS](../../../ios/#device_uuid) or [Android](../../../android/#deviceUuid) SDKs.
     + or by the [/v1/registration POST/PUT APIs](../registration/index.html) if you are registering your devices without using the Client SDKs.

 - By tag(s):
   * Tags are created:
     + implicitly when devices subscribed to them. See the [/v1/registration POST/PUT APIs](../registration/index.html).
     + or when the [/v1/tags POST API](../tags/#tag_create) is used.


Key              | Description
---              | ---
devices          | A list of up to 255 device UUIDs to target. These device UUIDs are the same ones that are returned by the PCF Push Client SDKs after registration or by the __/v1/registration__ HTTP POST call if you are registering your devices without using the Client SDKs.
tags             | A list of up to 255 tags (or topics) to which devices may be subscribed. Only devices subscribed to one of more of the listed tags will be targeted. Devices select which tags to subscribe to by calling the appropriate __subscribeToTags__ methods in the client SDKs or by calling the /v1/registration HTTP POST or PUT.
platforms        | A list of platforms to be targeted. Available platforms are 'ios', 'android', 'windows8', 'windowsPhone', 'bb10' (if enabled).
platform         | DEPRECATED. Possible values are 'all', 'ios', 'android', 'windows8', 'windowsPhone', 'bb10' (if enabled). If 'platforms' is also populated the platform(s) selected here will be added to list of platforms.
interactive-only | If set to __true__ then only those devices that can accept interactive pushes are targetted. At this time only iOS 8+ or Android 4.1+ devices are considered to support interactive pushes.

### LIMITS

Pushing to multiple targets is bounded by the following limits per request:

- Devices: 255
- Tags: 255

### NOTES

* At least one of __devices__, __tags__, __platforms__, or __platform__ is required.

* __devices__ will override any other targeting type. Any __tags__, __platforms__, or __platform__ targetting key will be ignored if there is a __devices__ key.

* __tags__ and __platforms__ can be used in a complementary way to push a message to just a subset of users (See example below).

* Devices only need to be subscribed to at least one of the tags in the targetting data in order to receive the message (See example below). There is no way using the Push API to send a message to a device that is subscribed to _all_ of the tags in a list.

### Target Examples

* Sending push messages to three specific devices (specified by their device UUIDs):

```JSON
  ...
  "target": {
    "devices": [ "device_uuid1", "device_uuid2", "device_uuid3" ]
  }
  ...
```

* Sending pushes to all devices (regardless of platform) subscribed to one of two specific tags (a device only needs to be subscribed to one of the tags in the list of tags in order to receive the message).

```JSON
  ...
  "target": {
    "tags": [ "exciting_topic", "pedantic_topic" ]
  }
  ...
```

* Sending pushes to all iOS devices:

```JSON
  ...
  "target": {
    "platforms": [ "ios" ]
  }
  ...
```

* Sending pushes to all "Android" devices subscribed to one specific tag:

```JSON
  ...
  "target": {
    "platforms": [ "android" ],
    "tags": [ "best_tag_ever" ]
  }
  ...
```

* Sending pushes to interactive only devices:

```JSON
  ...
  "target": {
    "platforms": [ "android", "ios" ],
    "interactive-only": true
  }
  ...
```

## <a id="setting_expiration"></a> Setting Expiration Time on Pushes ##

The "__expiryTime__" field can be used to specify a time after which a push should not be displayed. It should be an __Epoch__ timestamp integer in milliseconds (i.e.: the number of milliseconds since midnight January 1, 1970). If __expiryTime__ is not set the behavior will be the platform default. For iOS, Android and BB10 pushes will be queued for delivery if the target device is unreachable at the time of the push and delivered as soon as it is reachable. If __expiryTime__ is set and the the device becomes reachable AFTER the expiry time, the push will not be delivered.

Windows 8 behavior is similar for tile and badge notifications but only one tile and one badge notification will be queued. See the [Push notification service request and response headers](http://msdn.microsoft.com/en-us/library/windows/apps/hh465435.aspx#pncodes_x_wns_cache) documentation for more information.

__IMPORTANT NOTE__:

* If omitted, the default expiry time used for Apple devices is `Integer.MAX_VALUE` seconds (i.e.: sometime in the year 2038).
* If omitted, the default expiry time used on GCM is 4 weeks (2,419,200 seconds).  The maximum time-to-live for messages delivered on GCM is also 4 weeks.
* Windows 8 toast notifications are not queued and cannot have an expiry time. They will only be delivered at the time the push is sent if the target device is connected.
* Windows Phone 7 has no expiry option. Instead pushes fall into one of three categories: immediate, up to 450 seconds, or up to 900 seconds. Depending on the time set in the "__expiryTime__" field, a push to Windows Phone 7 will be slotted into one of those categories. See the [Sending push notifications for Windows Phone](http://msdn.microsoft.com/library/windows/apps/hh202945.aspx) documentation.

## <a id="scheduled_pushes"></a> Scheduled Pushes ##

Pushes can be scheduled to be sent at a later time. Use the __scheduleAt__ field to specify the time when the push should be sent. As with __expiryTime__ this should be an __Epoch__ timestamp integer in milliseconds. Alternatively you can use the __scheduleIn__ field to specify the scheduled time as the number of seconds from the time the server receives the push request. NOTE: You cannot set both the __scheduleAt__ and __scheduleIn__ fields at the same time as doing this would result in an error message from the server.

If the scheduled time is less than a preconfigured time in the future, the push will not be scheduled and will be sent immediately. By default this amount is 60 seconds.

### Scheduled Pushes Examples

* Scheduling a push message to be delivered for February 2, 2016 at 8 AM (UTC):

```JSON
  ...
  "scheduleAt": 1454313600000
  ...
```

* Scheduling a push message to be delivered two hours from now:

```JSON
  ...
  "scheduleIn": 7200000
  ...
```

## <a id="custom_ios_fields"></a>Custom Fields for iOS Pushes ##

The fields available in the custom block for iOS are described here:

```JSON
 "ios": {
        "alert": {
          "body": "iOS only message body",
          "action-loc-key": "actionKey",
          "loc-key": "localizedStringKey",
          "loc-args": [
            ""
          ],
          "title": "Title",
          "title-loc-key": "titleKey",
          "title-loc-args": [
          	"arg1",
            "arg2"
          ],
          "launch-image": "Default.png"
        },
        "category": "SAMPLE_CATEGORY",
        "badge": 1,
        "sound": "default",
        "content-available": true,           # Note: the Push API expects this field to be a boolean. (see below)
        "extra": { "freeform custom data" : "freeform custom data", ... }
      },
```
* __extra__<br/> type: dictionary or null<br/> This property can be used to pass free-form arbitrary payload data to the receiving iOS device.  This data will be passed in the `userInfo` dictionary in the `application:didReceiveRemoteNotification` callback in the application's app delegate class.  It is up to the application to use this data as it needs.

* __alert__<br/> type: string or dictionary<br/> If this property is included, the system displays a standard alert. You may specify a string as the value of alert or a dictionary as its value. If you specify a string, it becomes the message text of an alert with two buttons: Close and View. If the user taps View, the app is launched. Alternatively, you can specify a dictionary as the value of alert. See Table 3-2 at the [Apple Documentation](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW2) for descriptions of the keys of this dictionary.

* __badge__<br/> type: number<br/> The number to display as the badge of the app icon. If this property is absent the badge is not changed. To remove the badge, set the value of this property to 0.

* __sound__<br/> type: string<br/> The name of a sound file in the app bundle. The sound in this file is played as an alert. If the sound file doesn’t exist or default is specified as the value, the default alert sound is played. The audio must be in one of the audio data formats that are compatible with system sounds; see [Preparing Custom Alert Sounds](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html#//apple_ref/doc/uid/TP40008194-CH103-SW6) for details.

* __content-available__<br/> type: boolean<br/> Provide this key with a value of `true` to indicate that new content is available. Including this key and value means that when your app is launched in the background or resumed, `application:didReceiveRemoteNotification:fetchCompletionHandler:` is called. (Newsstand apps are guaranteed to be able to receive at least one push with this key per 24-hour window). The Push API will translate the value of this field to `1` or `0` before sending it to APNS.

* __title__<br/> type: string<br/> A short string describing the purpose of the notification. Apple Watch displays this string as part of the notification interface. This string is displayed only briefly and should be crafted so that it can be understood quickly. This key was added in iOS 8.2.

* __body__<br/> type: string<br/> The text of the alert message.

* __title-loc-key__<br/> type: string or null<br/> The key to a title string in the "Localizable.strings" file for the current localization. The key string can be formatted with `%@` and `%n$@` specifiers to take the variables specified in the __title-loc-args__ array. See [Localized Formatted Strings](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW21) for more information. This key was added in iOS 8.2.

* __title-loc-args__<br/> type: array of strings or null<br/> Variable string values to appear in place of the format specifiers in title-loc-key. See [Localized Formatted Strings](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW21) for more information. This key was added in iOS 8.2.

* __action-loc-key__<br/> type: string or null<br/> If a string is specified, the system displays an alert that includes the Close and View buttons. The string is used as a key to get a localized string in the current localization to use for the right button’s title instead of “View”. See [Localized Formatted Strings](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW21) for more information.

* __loc-key__<br/> type: string<br/> A key to an alert-message string in a "Localizable.strings" file for the current localization (which is set by the user’s language preference). The key string can be formatted with `%@` and `%n$@` specifiers to take the variables specified in the loc-args array. See [Localized Formatted Strings](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW21) for more information.

* __loc-args__<br/> type: array of strings<br/> Variable string values to appear in place of the format specifiers in __loc-key__. See [Localized Formatted Strings](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW21) for more information.

* __launch-image__<br/> type: string<br/> The filename of an image file in the app bundle; it may include the extension or omit it. The image is used as the launch image when users tap the action button or move the action slider. If this property is not specified then the system either uses the previous snapshot or uses the image identified by the __UILaunchImageFile__ key in the app’s "Info.plist" file, or falls back to "Default.png". This property was added in iOS 4.0.

Please check the [Apple documentation](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW2) for more detailed information.

## <a id="custom_android_fields"></a>Custom Fields for Android Pushes ##

The custom fields for android are a dictionary that can contain any fields required by your application. You can also specify a __collapse\_key__ in the custom fields for Android. A message with a __collapse\_key__ that has not yet been delivered may be replaced by a newer message with the same collapse key. Please see the [Google documentation on collapsable messages](https://developers.google.com/cloud-messaging/downstream#collapsible_and_non-collapsible_messages).

Otherwise, you can specify any arbitrary freeform payload data to deliver to the receiving Android device.  All of the fields in this in the __android__ element in the push request will be supplied to the receiving Android device in the `Bundle` provided to `onReceiveMessage` method in the Android application's subclass of `GcmService`.  In general the push message data would be provided in a `message` JSON field but it is up to your application to use the message payload as it needs.

Note that the "message" &rarr; "body" field in the Push request body, if present, will be delivered in the "message" field of the GCM push notification payload.

## <a id="custom_other_platforms_fields"></a>Custom Fields for Other Platforms ##

### BB10 ###

The __bb10__ custom field consists of a string which will override the message body on BlackBerry 10.  Note that PCF Push Notification Service support for Windows 8 is basic.  Pivotal does not provide any up-to-date client SDKs for BlackBerry 10 at this time.

__NOTE__: The PCF Push Notification Service does not ship with BB10 support enabled due to restrictions on redistribution of BlackBerry's push libraries. If you need to enable BB10 pushes please contact [Pivotal Support](https://support.pivotal.io/hc/en-us).

###  Windows 8 (WNS) ###

See [this reference](windows8Fields/index.html) for a description of Windows 8 push options.  Note that PCF Push Notification Service support for Windows 8 is basic.  Pivotal does not provide any up-to-date client SDKs for Windows 8 at this time.

###  Windows Phone (MPNS) ###

See [this reference](windowsPhoneFields/index.html) for a description of Windows Phone push options.  Note that PCF Push Notification Service support for Windows 8 is basic.  Pivotal does not provide any up-to-date client SDKs for Windows Phone at this time.

## <a id="examples"></a> Complete Examples ##

Unlike the above examples, these examples will show the complete Push request body.

* Send a message to all users subscribed to the "local_seminars" tags to alert them to an important community meeting.  This message expires on the morning of Friday April 1, 2016.

```JSON
{
  "message": {
    "body": "Town Hall This Thursday: Forging, Cheese, And You"
  },
  "target": {
    "tags": [ "local_seminars" ]
  },
  "expiryTime": 1459468800000
}
```

* Send a push to all iOS and Android devices that are subscribed to one (or more) of the "breaking_news", "local", or "dairy" tags. Provide some custom fields that apps can use to deep link to article data.  This message is scheduled to be delivered in two hours.

```JSON
{
  "message": {
    "custom": {
      "ios": {
        "alert": {
          "body": "Breaking News: World's Biggest Cheese Forged At Local Bakery"
        },
        "content-available": true,
        "extra": { "story_url": "https://my_server/article/123456789" }
      },
      "android": {
        "message": "Breaking News: World's Biggest Cheese Forged At Local Bakery",
        "story_url": "https://my_server/article/123456789"
      }
	}
  },
  "target": {
    "tags": [ "breaking_news", "local", "dairy" ],
    "platforms": [ "ios", "android" ]
  },
  "scheduleIn": 7200
}
```

* Send a push to one particular device informating the user that they have one new email notification.  The badge on the app icon will be set to "1" and a sound will be played. Some of the email metadata is provided in the message extras so that the application can show a preview of the message. The message is given the "new_email" category so that iOS 8.0+ devices can provide appropriate action buttons for the user.

```JSON
{
  "message": {
    "custom": {
      "ios": {
        "alert": {
          "body": "You've got mail!"
        },
        "category": "new_email",
        "badge": 1,
        "sound": "new_email",
        "content-available": true,
        "extra": {
          "from": "Your Local Bakery",
          "to": "You",
          "subject": "Special Deal on Cheese",
          "message_body": "Please come to your local bakery before Friday to sample a piece of the world's biggest cheese."
        }
      }
    }
  },
  "target": {
    "devices": [ "111-222-333444" ]
  }
}
```

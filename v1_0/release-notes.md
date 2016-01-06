---
title: Push Notification Service v1.0.0 Release Notes
---


## Changes
- RESTful APIs to send push notifications
- Support for iOS, Android, Windows 8, Windows Phone
- Adminstrative dashboard to manage applications and environments (e.g. development, staging, production)
- Ability to send test messages to an environment, or a particular device
- Audit logging allows tracing of a pushed message from the initial API call, up to and including the transmission to the platform's push endpoint

## Known issues
- Multi-tenant data protection is not available. Push configuration data in the dashboard is accessible to all PCF UAA users. However, an App user only sees messages pushed to that user.
- High-availability configuration requires a high-availability configuration of MySQL.
- There is no BB10 support
- There are no statistics or status checks on the messages in the queue.
- All apps and variants use the same RabbitMQ queues.

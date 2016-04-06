---
title: DevOps
---

## Uninstalling

**IMPORTANT**

It is advised that you do **NOT UNINSTALL** the Push tile in order to
solve problems with binding or communicating with other services. The
Push team will provide instructions on how to manually restore these
connections.

Deleting the tile will cause all of the user data stored in the MySQL,
Redis, and RabbitMQ services to be **DELETED** as well.

If you need to delete the Push tile or delete any of its connections to
the above services then you will need to **BACKUP** and **RESTORE** all
of the user data in these services.

Instructions for backing up and restore the user data is provided below.

## Troubleshooting common problems

For solutions to common problems, please see our [troubleshooting guide](troubleshooting.html).

## Configurable Environment Variables

### Push Api

    push_security_trustAllCerts (Boolean, default: inherited from cf runtime)

  When the push\_security\_trustAllCerts environment variable is set to `true` the Push API will skip SSL validation on calls to RabbitMQ and the Push Scheduler. This variable is necessary in environments that use self-signed certificates. The default value is `false` unless the CF Runtime is configured to trust self-signed certificates.

_Certificates generated in Elastic Runtime are signed by the Operations Manager Certificate Authority. They are not technically self-signed, but they are referred to as 'Self-Signed Certificates' in the Ops Manager GUI and throughout this documentation._

    push_scheduler_sendImmediatelyWithin (Integer, default: 60)

  The push\_scheduler\_sendImmediatelyWithin environment variable pertains to scheduled push notifications. It is a threshold (in seconds) within which the push server will skip scheduling a push and simply send it right away. The default value is 60 seconds. If a push is scheduled within 60 seconds of the current time it will not be scheduled but simply be sent right away. You can modify that threshold by modifying this environment variable.

    push_apns_sendReceipt (Boolean, default: true)

  The push\_apns\_sendReceipt environment variable is a flag that enables passing a receipt to the device as part of the push payload. The receipt is a unique id for each message that can be used for analytics.  This enables sending receipts for iOS/APNS.

    push_apns_logDeviceTokens (Boolean, default: true)

  The push\_apns\_logDeviceTokens environment variable controls the log verbosity of the apns push handler. When set to 'true' the device token for every recipient of a push will be logged as the push is sent. Note that this extra logging will reduce push throughput.

    push_gcm_sendReceipt (Boolean, default: true)

  The push\_gcm\_sendReceipt environment variable is a flag that enables passing a receipt to the device as part of the push payload. The receipt is a unique id for each message that can be used for analytics.  This enables sending receipts for Android/GCM.

    push_gcm_logDeviceTokens (Boolean, default: true)

  The push\_gcm\_logDeviceTokens environment variable controls the log verbosity of the android push handler. When set to 'true' the device token for every recipient of a push will be logged as the push is sent. Note that this extra logging will reduce push throughput.

#### Installing the push server behind a proxy (available in v1.3.2+)

  Starting in version 1.3.2 you can route communication with push providers (apns, google cloud messaging) through a proxy server. GCM pushes can use either a http or socks proxy. APNS pushes can only use a socks proxy. Use the following environment variables to specify proxies.

    push_gcm_httpProxyHost (String, default: [empty])
    push_gcm_httpProxyPort (Integer, default: [empty])

  The push\_gcm\_httpProxyHost and push\_gcm\_httpProxyPort environment variables allow you to specify an http proxy server through which to route google api requests (for android pushes).

    push_gcm_socksProxyHost (String, default: [empty])
    push_gcm_socksProxyPort (String, default: [empty])

  The push\_gcm\_socksProxyHost and push\_gcm\_socksProxyPort environment variables allow you to specify a SOCKS proxy through which to route google api requests.

  Note: If both HTTP and SOCKS proxies are defined for GCM, SOCKS will be used.

    push_apns_socksProxyHost (String, default: [empty])
    push_apns_socksProxyPort (String, default: [empty])

  The push\_apns\_socksProxyHost and push\_apns\_socksProxyPort environment variables allow you to specify a SOCKS proxy through which to route apns push requests.


## Backup And Restore

###Backup Mysql data

  - In the developer console in the "system" org go to the "push-notifications" space and the "push-api" app
  - Go to services
  - click show credentials for mysql
  - get username, password and database name
  - ssh into the proxy for your pivotal cf environment
  - From the proxy run

  		mysqldump -h hostname -p -u username database_name > push_db.sql

###Backup encryption key

   - In the developer console go to the push-api app and go to the "Env Variables" tab
   - Get and record the value for 'crypto_applicationKey'. You will need this during the installation.
      - The crypto\_applicationKey environment variable contains the key which will be used to encrypt sensitive information used by the push server (ex. iOS push certificates, google api keys). This value is set at install time and **_should not be modified_**. You will however need to record this value in order save and restore the push notification service database.



###Restore Mysql data

- From the developer console in the "push notifications" space through the "system" org, stop the "push" and "push-api" applications
- Go to services
- Click show credentials for mysql
- Get username, password and database name
- ssh into the proxy for your pivotal cf environment
- Delete data from push installation (this should just be empty data)
	- From the proxy run

    		mysql -h hostname -p -u username name -e "drop database database_name; create database database_name;"

 - Import data from old install
  	- From the proxy run

  			mysql -h hostname -p -u username database_name < push_db.sql

- Enable migrations
	- In the developer console, find the "push-api" application and go to the "Env Variables" tab
	- Edit 'liquibase_runMitrations' and set it to 'true'

- Start the "push-api" and "push" applications

- Disable migrations
	- In the developer console, find the "push-api" application and go to the "Env Variables" tab
	- Edit 'liquibase_runMitrations' and set it to 'false'
	- Restart the "push-api" application



###Backup Redis Data

  - See [redis backup instructions ](redis-backup.html)

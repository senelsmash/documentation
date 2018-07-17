# Documentation of SIMS-APP-API #

## Description ##

SIMS-APP-API is JAVA project that use as a API to sims-notify-service.

## End points ##

| Path |
| --- |
| `/login` |
| `/register` |
| `/link` |
| `/unlink` |
| `/unregister` |
| `/uploadcdr2` |
| `/matrix/get/ + {UID}` |
| `/uploadmessage/send` |
| `/uploadcdr2` |

### 01 login ###

SIMS user login to system this function required device telephony details, GSM details and UID

### 02 register ###

SIMS user registration for new user or unreg to system this function required device telephony details, GSM details and UID

### 03 link ###

User link the GSM sim card. One user can link mutiple sim cards at once. Query/ TopUps are run through the default sim card. This function required GSM details and the UID.

### 04 unlink ###

User unlink the GSM sim card. One user can link mutiple sim cards at once. Query/ TopUps are run through the default sim card. This function required GSM details and the UID.

### 05 unregister ###

User unregister function. This required UID.

### 06 uploadcdr2 ###

Upload every CDR files from this function. CDR uploading as binaries.

### 07 matrix/get/ + {UID} ###

Get recent matrix details. This required current user UID.

### 08 uploadmessage/send ###

Send response messages from GSM operator USSD and SMS.

### 09 uploadmessage/send ###

Send response messages from GSM operator USSD and SMS.

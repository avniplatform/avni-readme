---
title: Authentication and session management
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Login

* Users log in to the field app with their User ID and password (see [How-to Guide: Installing the Avni field app](https://avni.readme.io/docs/how-to-guide-installing-avni-field-app-and-basic-set-up-on-your-mobile-phone)).
* **Login requires an internet connection.** Identity is verified over OpenID Connect against the identity server (Amazon Cognito on the Avni hosted service), which issues signed JWT tokens to the device.

## Tokens and validity

| Token             | Purpose                                                               | Validity          |
| :---------------- | :-------------------------------------------------------------------- | :---------------- |
| ID / Access token | Sent with every API call to authenticate the user                      | 1 hour            |
| Refresh token     | Renews the ID/access token without the user re-entering their password | 30 days (default) |

* Token renewal is automatic and needs no user action — whenever the app talks to the server (login, manual sync, auto sync), an expired access token is renewed in the background using the refresh token.
* The app never asks for the password again while the refresh token is valid.

## Offline sessions

* After login, the app works completely offline except for sync. There is no inactivity timeout or automatic logout while offline.
* If a user stays offline (no successful sync) beyond the refresh-token validity, the session can no longer be renewed. The app returns to the login screen — at the next sync attempt or app restart — and the user must **go online and log in again** before continuing data entry.
* No data is lost in this situation. Data captured offline stays on the device, and sync resumes incrementally after re-login (the database is intact, so [fast sync](https://avni.readme.io/docs/fast-sync) is not triggered again).
* Recommended field practice: sync whenever connectivity is available — this keeps the session fresh well within the token validity.

## Logout

* Logging out requires an internet connection — the logout action itself communicates with the server, and the next login does too. The app warns at logout: "If you log out, you will need to login again with the internet to continue using the app."
* Logout does **not** delete local data — unsynced records are preserved on the device.
* Field users should stay logged in between field days; there is no security or operational need to log out.

## One user per device

* The app holds one user's data at a time. If a different User ID attempts to log in on the same device, the app warns that proceeding will delete the previous user's unsynced data, and continues only on explicit confirmation. The new user then starts with a fresh database for their own catchment (via [fast sync](https://avni.readme.io/docs/fast-sync) where available).

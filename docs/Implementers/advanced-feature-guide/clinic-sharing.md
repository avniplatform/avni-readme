---
title: Clinic sharing
excerpt: 'Share subjects, visits and photos between the phones in a mobile clinic without internet'
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
Clinic sharing lets the staff of a mobile clinic work on their own phones as one team, without internet. The receptionist registers a patient. A minute later the patient is on the nurse's phone. The nurse records the vitals, and the doctor sees them on a third phone.

One phone becomes the hub. It starts its own Wi-Fi network, and the other phones join it. Each joined phone shares with the hub every 30 seconds by itself. Nobody needs to press a button.

Sync with the server does not change. When a phone has internet, its user syncs as usual, and each phone uploads its own work.

## What travels between the phones

* Subjects, enrolments, visits and cancelled visits.
* Checklists, relationships, group members, approvals, comments, program eligibility and attendance.
* Photos and other media attached to those records.

Each phone receives only the records its user is allowed to see, as with sync. Unfinished drafts stay on the phone that made them.

## Before you start

* **Users must be in the SQLite Migration user group.** Clinic sharing works only on phones that use the new on-device database.
* **The organisation must not use on-device data encryption.** The Clinic sharing menu is hidden when encryption is on.
* **All phones must have the same version of the Avni app.** A phone on a different version cannot sync with the hub.

## Choosing the hub phone

* The hub phone needs Android 10 or later.
* The hub must be logged in as a user with all privileges. Other users do not see the option to start a hub.
* The hub is also a working phone. Its user can register patients and record visits as usual.
* Keep the hub charged and in the middle of the clinic. Phones out of its Wi-Fi range stop sharing until they come back.
* Keep at least 500 MB free on the hub. Below that, the hub stops taking new photos from other phones.
* Do not turn on the hub phone's personal hotspot. Android runs one hotspot at a time, so the hub stops.

## Switching clinic sharing on

1. In the web app, open the organisation settings page.
2. Turn on **Enable clinic sharing**.

[Image 1: web app, organisation settings page, the Enable clinic sharing switch turned on]

3. Ask each user to sync their phone. After the sync, **Clinic sharing** appears in the app's **More** menu.

[Image 2: phone, More menu with Clinic sharing in the list]

## Starting the hub

1. On the hub phone, open **More** and tap **Clinic sharing**.
2. Under **Make this phone the hub**, tap **Start hub**.
3. Allow the permissions Android asks for.

[Image 3: hub phone, Clinic sharing screen before starting, with Make this phone the hub and Start hub]

The hub shows a code to scan, with the name and password of its Wi-Fi network. It also shows how many phones synced with it in the last 5 minutes.

[Image 4: hub phone, running hub with the code, Network, Password, Phones synced in the last 5 minutes and Stop hub]

To stop sharing, tap **Stop hub**.

## Joining the hub

1. On each other phone, open **More** and tap **Clinic sharing**.
2. Tap **Scan to join**.
3. Point the camera at the code on the hub phone.

[Image 5: joining phone, the camera scanning the hub's code]

4. If Android asks to connect to the hub's network, tap **Connect**.

[Image 6: joining phone, Android's dialog asking to connect to the hub's network]

The screen then shows **Joined to** the hub's network, and when the phone last synced with the hub. A joined phone syncs with the hub every 30 seconds while Avni is open. To sync at once, tap **Sync with hub**. To stop sharing, tap **Leave hub**.

[Image 7: joined phone, Joined to the hub, Last synced with the hub, Sync with hub and Leave hub]

A joined phone can still sync with the server over mobile data. The hub's Wi-Fi has no internet, and Avni does not try to reach the server through it.

## Joining by hand on older phones

Phones on Android 8 or 9 cannot join the hub's network from inside Avni.

1. Open the phone's Wi-Fi settings.
2. Join the network shown on the hub screen, with the password shown there.
3. In Avni, open **Clinic sharing** and tap **Scan to join**.

If the phone is on another network, Avni says so and asks the user to join the hub's network in Wi-Fi settings first.

[Image 8: older phone, the message asking to join the hub's network in Wi-Fi settings]

## When a phone loses the hub

A phone that walks out of range loses the hub's network. The screen says the phone lost the hub's Wi-Fi, and offers **Rejoin** with the hub's name. Tap it when the phone is back in range.

[Image 9: joined phone after leaving range, the lost Wi-Fi message with Rejoin and Scan a new code]

The same happens if Android closes Avni in the background. For example, it can close Avni while the phone's camera app is open for a photo. Open **Clinic sharing** and tap **Rejoin**.

## When the hub restarts

Stopping and starting the hub creates a new network with a new name and password. Every phone must scan the hub's new code.

**Rejoin** cannot find the old network. The screen says the phone could not join, and suggests scanning the new code. Tap **Scan a new code** and scan the code on the hub. Records shared before the restart stay on the phones.

[Image 10: phone after the hub restarted, the could not join message suggesting a new scan]

## When two people edit the same record

* Through the hub, the version that reaches the hub last is kept.
* On the server, the last upload wins, as with sync.

Agree in the clinic who completes each record, so that two people do not fill the same visit.

## Why some uploads wait

A record from another phone belongs to that phone. That phone uploads it to the server. Until it does, this phone holds back its own work that depends on it. For example, a visit recorded on a patient registered on another phone waits until the patient reaches the server.

* The **Clinic sharing** screen shows how many records are waiting.

[Image 11: phone, Clinic sharing screen with the count of records waiting to upload]

* When this phone next syncs with the server and finds the patient there, it uploads the waiting records in the same sync.
* If a record waits more than 3 days, the screen names the record it is waiting on. Ask the user of the phone that made that record to sync.

## Photos

* Photos travel to the hub with their records.
* A joined phone fetches a photo from the hub when someone opens it.

[Image 12: joined phone, a visit opened with a photo taken on another phone]

* Each phone uploads its own photos to the server, as usual.

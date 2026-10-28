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

Starting a hub, or joining one, puts a phone in a camp. A phone in a camp does not sync with the server. After a phone leaves the camp, it syncs with the server as usual, and uploads its own work.

## What travels between the phones

* Subjects, enrolments, visits and cancelled visits.
* Checklists, relationships, group members, approvals, comments, program eligibility and attendance.
* Photos and other media attached to those records.

Each phone shows only the records its user is allowed to see, as with sync. Unfinished drafts stay on the phone that made them.

## Before you start

* **Users must be in the SQLite Migration user group.** Clinic sharing works only on phones that use the new on-device database.
* **The organisation must not use on-device data encryption.** The Clinic sharing menu is hidden when encryption is on.
* **Everyone in a camp must be a user of the same catchment.** Change a user's catchment only after the camp ends.
* **Sync every phone with the server just before the camp,** so that its forms and other organisation setup match the hub's. Phones may run different versions of the Avni app.
* A phone whose setup differs from the hub's leaves the camp, and its **Clinic sharing** screen says so. Sync it with the server, then scan the hub's code again.

## Choosing the hub phone

* The hub phone needs Android 10 or later.
* The hub must be logged in as a user with all privileges. Other users do not see the option to start a hub.
* The hub is also a working phone. Its user can register patients and record visits as usual.
* Keep the hub on charge and in the middle of the clinic. Phones out of its Wi-Fi range stop sharing. When they are back in range, open **Clinic sharing** and tap **Rejoin**.
* Keep Avni open on the hub's screen. Avni keeps the screen on while the hub runs. If someone locks the hub with the power button, the phone can pause Avni, and the other phones cannot sync until Avni is open again.
* On the hub phone, set Avni's battery use to **Unrestricted**. On Samsung phones, keep Avni out of the sleeping apps, and on Xiaomi phones allow Autostart.
* Keep at least 500 MB free on the hub. Below that, the hub stops taking new photos from other phones.
* Do not turn on the hub's personal hotspot, and do not turn on its Wi-Fi while the hub runs. On some phones either one stops the hub.

## Switching clinic sharing on

1. In the web app, open the organisation settings page.
2. Turn on **Enable clinic sharing**.

![Organisation settings in the web app, with Enable clinic sharing turned on](https://github.com/user-attachments/assets/bfd22bbb-ba63-42db-966e-2eb0b11a6676)

3. Ask each user to sync their phone. After the sync, **Clinic sharing** appears in the app's **More** menu.

<Image align="center" width="216px" alt="More menu with Clinic sharing" src="https://github.com/user-attachments/assets/b0d80acd-26f2-4f0d-aa67-d8ae3b7bbd87" />

## Starting the hub

1. On the hub phone, open **More** and tap **Clinic sharing**.
2. Under **Make this phone the hub**, tap **Start hub**.
3. Allow the permissions Android asks for.

<Image align="center" width="216px" alt="Clinic sharing screen with Make this phone the hub and Start hub" src="https://github.com/user-attachments/assets/b91f1be8-fa23-4315-96f9-755a31df730b" />

The hub shows a code to scan, with the name and password of its Wi-Fi network. It also shows how many phones synced with it in the last 5 minutes. Below that it lists every phone by its user and phone model, with when it last synced.

<Image align="center" width="216px" alt="A running hub showing its code, network name and password, phones synced in the last 5 minutes, and End camp" src="https://github.com/user-attachments/assets/f7767d47-3cc7-4a1d-b0b8-24ce5e13fe77" />

## Joining the hub

1. On each other phone, open **More** and tap **Clinic sharing**.
2. Tap **Scan to join**.
3. Point the camera at the code on the hub phone.

<Image align="center" width="216px" alt="Scanning the hub's code" src="https://github.com/user-attachments/assets/eeb43c42-49d8-4b72-a720-f07332fd82af" />

4. If Android asks to connect to the hub's network, tap **Connect**.

After its first sync with the hub, the phone goes back to the Home screen. The **Clinic sharing** screen then shows **Joined to** the hub's network, when the phone last synced with the hub, and that the phone is in a camp. A joined phone syncs with the hub every 30 seconds while Avni is open. To sync at once, tap **Sync with hub**.

<Image align="center" width="216px" alt="A joined phone in a camp, with Sync with hub and Leave camp" src="https://github.com/user-attachments/assets/1f335482-2c17-44b4-a2d3-7d85b0cf3755" />

## Joining by hand on older phones

Phones older than Android 10 cannot join the hub's network from inside Avni.

1. Open the phone's Wi-Fi settings.
2. Join the network shown on the hub screen, with the password shown there.
3. In Avni, open **Clinic sharing** and tap **Scan to join**.

These phones also join by hand again after the hub restarts, and when **Rejoin** does not connect. Join the network as in steps 1 and 2, then tap **Scan a new code**.

## During a camp

* No phone in the camp syncs with the server, even where there is signal.
* On a joined phone, the **Sync** button syncs with the hub.
* On the hub, the **Sync** button explains that the phones sync with this hub, and that it syncs with the server after the camp.
* On a phone that has lost the hub, the **Sync** button says to rejoin from **Clinic sharing**, or leave the camp.
* Logging out is blocked until the phone leaves the camp. The hub's user can log out once the hub is closed.

## When a phone loses the hub

A phone that walks out of range loses the hub's network. The screen says the phone lost the hub's Wi-Fi, and offers **Rejoin** with the hub's name. Tap it when the phone is back in range.

<Image align="center" width="216px" alt="Rejoin and Scan a new code" src="https://github.com/user-attachments/assets/e3f0c23a-3bd0-42df-85cd-535cfd1e643f" />

If Android closes Avni in the background, the phone also stops sharing. For example, it can close Avni while the phone's camera app is open for a photo. Open **Clinic sharing** and tap **Rejoin**.

## When the hub restarts

If the hub phone restarts, or Android closes Avni on it, the hub starts again by itself when Avni is opened. It stays in the same camp, with everything it held. It makes a new network with a new name and password, so every phone must scan the hub's new code.

**Rejoin** cannot find the old network. Android may say that no devices were found. Tap **Cancel**. The screen then says the phone could not join, and suggests scanning the new code. Tap **Scan a new code** and scan the code on the hub. Records shared before the restart stay on the phones.

<Image align="center" width="216px" alt="Could not join the old network, with the suggestion to scan the new code" src="https://github.com/user-attachments/assets/30aad166-c7ce-4ece-94cf-96eb3ace0c96" />

If the hub stops and does not start again, its screen says the camp is still on. Tap **Start hub** to carry on. Each phone then scans the new code.

## Ending a camp

1. When the clinic is over, tap **End camp** on the hub.
2. The hub asks first. Tap **End camp** again to confirm. This cannot be undone.

<Image align="center" width="216px" alt="The hub asking to confirm End camp" src="https://github.com/user-attachments/assets/3da947ba-d93d-4f6d-9008-aa359c75c302" />

3. Leave the hub running, and keep Avni open on every phone. At its next sync, each phone does one last sync with the hub, then leaves the camp. Its screen says the hub ended the camp, and that it can sync with the server again.
4. The hub ticks off each phone as it leaves. If a phone is switched off, gone, or already left with **Leave camp**, tap **Skip** under its name. A skipped phone is still in the camp until its user opens **Clinic sharing** and taps **Leave camp**.
5. When every phone has left or been skipped, tap **Close hub**. The hub deletes the records and photos it kept for the other phones.

If the hub is not running, it offers **Close the camp** instead. Phones still in the camp are not told, so each of them must tap **Leave camp**.

## Leaving a camp early

Any phone can leave on its own, for example when someone goes home early or the hub is lost. Open **Clinic sharing** and tap **Leave camp**. If the phone is still joined to the hub, it does a last sync first, then leaves the camp. It can then sync with the server. To come back, scan the hub's code again.

## When two people edit the same record

Each person should edit only the records they created. If two people do edit the same record:

* Through the hub, the version that reaches the hub last is kept.
* On the server, the last upload wins, as with sync.

## Why some uploads wait

A record from another phone belongs to that phone. That phone uploads it to the server. Until it does, this phone holds back its own work that depends on it. For example, a visit recorded on a patient registered on another phone waits until the patient reaches the server. Nobody has to sync first: each phone uploads its waiting work at its first sync after its teammate's.

* The **Clinic sharing** screen shows how many records are waiting.

<Image align="center" width="216px" alt="Records waiting to upload until records from other phones reach the server" src="https://github.com/user-attachments/assets/d97c9f86-6fc9-4f35-abe5-a9817a3f6315" />

* When this phone next syncs with the server and finds the patient there, it uploads the waiting records in the same sync.
* If a record waits more than 3 days, the screen names the record it is waiting on. Ask the user of the phone that made that record to sync with the server, leaving the camp first if that phone is still in one.

## Photos

* Photos travel to the hub with their records.
* A joined phone fetches a photo from the hub when someone opens it.

<Image align="center" width="216px" alt="A visit from another phone, opened with its photos" src="https://github.com/user-attachments/assets/45ee518a-f258-4c81-a066-a46e4b34420f" />

* Each phone uploads its own photos to the server, as usual.

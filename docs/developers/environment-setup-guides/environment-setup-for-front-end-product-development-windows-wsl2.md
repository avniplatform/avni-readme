---
title: Environment setup for front end product development - Windows (WSL2)
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
This page documents how to set up a **Windows** machine to contribute to Avni front-end code — primarily the mobile app. Avni's build tooling is Make + bash, so it does not run on native Windows; the supported route is **WSL2 with Ubuntu**. Everything (Node, Java, Android SDK, the build) runs inside WSL, and your phone or emulator connects to it through `adb`.

This assumes you have access to some test Avni server, with one or more implementations deployed, that you can connect to. The steps mirror the [Ubuntu front-end setup](doc:environment-setup-for-front-end-product-development-ubuntu) — only the WSL2 and device-connection parts are Windows-specific.

# Install WSL2 + Ubuntu

In PowerShell **as Administrator**:

```powershell
wsl --install -d Ubuntu-24.04
```

Reboot if asked, launch *Ubuntu* from the Start menu, and create your Linux username/password. Everything below runs **inside the Ubuntu shell** unless marked *(Windows)*.

> 🚧 Keep your repositories inside the WSL filesystem (e.g. `~/projects/...`), **not** under `/mnt/c/...`. Builds on `/mnt/c` are many times slower.

# Base tooling (inside WSL)

```bash
sudo apt update
sudo apt install -y git curl unzip zip make build-essential openjdk-17-jdk-headless
```

Install nvm (Node itself is installed later from the repo's `.nvmrc`):

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
# then close and reopen the Ubuntu terminal
```

# Android SDK

Only the command line tools are needed inside WSL — not Android Studio. Download the *"Command line tools only"* zip for **Linux** from [developer.android.com/studio](https://developer.android.com/studio#command-line-tools-only), then:

```bash
mkdir -p ~/Android/Sdk/cmdline-tools
cd ~/Android/Sdk/cmdline-tools
unzip ~/commandlinetools-linux-*.zip   # adjust path to where you downloaded it
mv cmdline-tools latest
```

Add to `~/.bashrc` (then `source ~/.bashrc`):

```bash
export ANDROID_HOME="$HOME/Android/Sdk"
export PATH="$PATH:$ANDROID_HOME/platform-tools:$ANDROID_HOME/cmdline-tools/latest/bin"
```

Install the SDK packages avni-client builds with:

```bash
sdkmanager --install "platform-tools" "platforms;android-35" "build-tools;35.0.0" "ndk;27.1.12297006"
sdkmanager --licenses   # accept all
```

# Connect a device

### Option A — physical Android phone over USB (recommended)

On the phone, enable *Developer options* and *USB debugging*, then plug it into the PC.

*(Windows, PowerShell as Administrator)* install [usbipd-win](https://github.com/dorssel/usbipd-win) and hand the phone to WSL:

```powershell
winget install usbipd
usbipd list                              # find your phone's BUSID, e.g. 2-3
usbipd bind --busid <BUSID>              # once per device
usbipd attach --wsl --busid <BUSID>      # re-run after replugging (or add --auto-attach)
```

Back in Ubuntu, `adb devices` should list the phone — accept the *"Allow USB debugging?"* prompt on the phone.

### Option B — Android emulator on the Windows side

WSL2 cannot run the Android emulator well, so run it on Windows (install Android Studio on Windows, create a virtual device, start it) and let the WSL build talk to the **Windows** adb server:

1. *(Windows)* create/edit `C:\Users\<you>\.wslconfig`:

   ```ini
   [wsl2]
   networkingMode=mirrored
   ```

   then run `wsl --shutdown` and reopen Ubuntu. (Mirrored networking needs Windows 11 22H2+; on older Windows point `ADB_SERVER_SOCKET` at the Windows host IP instead.)

2. In Ubuntu:

   ```bash
   export ADB_SERVER_SOCKET=tcp:127.0.0.1:5037   # add to ~/.bashrc to persist
   adb devices                                   # should list emulator-5554
   ```

Keep the adb versions on the Windows and WSL sides reasonably in sync (`adb --version`) — mismatched versions kill each other's server.

# Avni Android Client

```bash
git clone https://github.com/avniproject/avni-client.git ~/projects/avni-client
cd ~/projects/avni-client

nvm install     # reads .nvmrc to install the correct Node version
make deps       # install node dependencies (takes a while the first time)
make test       # verify unit tests run fine
```

Point the app at your Avni server:

```bash
cp packages/openchs-android/config/env/dev.json.template packages/openchs-android/config/env/dev.json
# edit dev.json and set SERVER_URL to your Avni server
```

Run it — two terminals:

```bash
make run_packager   # terminal 1 — Metro bundler, leave it running
make run_app        # terminal 2 — build, install and launch on the connected device
```

Press the sync button in the app to download the configuration and master data for your implementation.

# Troubleshooting

| Symptom | Fix |
| :------ | :-- |
| `adb devices` empty after plugging the phone | Re-run `usbipd attach --wsl --busid <BUSID>` *(Windows)* — attachment doesn't survive replug unless you used `--auto-attach` |
| Red screen: *unable to load script / could not connect to development server* | Ensure `make run_packager` is running; `adb reverse tcp:8081 tcp:8081`, then reload |
| `more than one device/emulator` | Disambiguate adb commands with `-s <serial>` (`adb devices` shows serials) |
| `[CXX1101] NDK ... did not have a source.properties file` | A partial NDK auto-download left an empty folder — delete it and `sdkmanager --install "ndk;27.1.12297006"` |

## **SETUP COMPLETE !!!**

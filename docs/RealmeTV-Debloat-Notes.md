# Realme TV — Debloat & Setup Notes

Done on **25 Oct 2026**. Everything below is reversible. Nothing was uninstalled from the system; packages were only *disabled*.

---

## 1. The TV

| Item | Value |
|---|---|
| Brand sold as | Realme TV |
| Actual maker | Changhong (`ro.product.manufacturer`) |
| Model | `AI PONT` |
| Device codename | `ikebukuro` |
| Android | Android TV 11 |
| CPU architecture | **32-bit only** — `armeabi-v7a, armeabi` |
| Home screen | **Glass TV v2.3-solat8** (original + prayer card, two home views, hide apps) — `com.gathin.tvhome` |
| Stock launcher | `com.google.android.tvlauncher` (disabled, can be re-enabled) |
| Live TV / inputs | `com.mediatek.wwtv.tvcenter`, `com.mediatek.tvinput`, `com.mediatek.tv.service` |
| IP used | `192.168.0.13` (Wi-Fi) |

Closest reference in the repo: `examples/chiq-ai-pont` (same Changhong "AI PONT" platform, Android TV 11).

---

## 2. Tools & folders

- Folder: `C:\RealmeTV\glass-tv-home` (clone of https://github.com/aqmangge/glass-tv-home)
- Python venv: `tools\venv` (installed from `tools\requirements.txt`)
- `adb` (Android platform-tools) on the laptop
- Disable / restore scripts: `tools\disable.ps1`, `tools\restore.ps1`

Files to keep:

| File | What it is |
|---|---|
| `my-batch1.txt` | Batch 1 package list (needed to undo) |
| `my-batch2.txt` | Batch 2 package list (needed to undo) |
| `backup\system-packages.txt` | System apps **before** debloat |
| `backup\user-packages.txt` | User-installed apps **before** debloat |
| `backup\already-disabled.txt` | What was already disabled from factory |
| `backup\home-apps.txt` | Apps that claimed the Home screen before changes |

---

## 3. Connecting to the TV (ADB)

1. On the TV: **Settings → Device Preferences → Developer options → USB debugging = ON**.
   (If Developer options is missing: **About → Build**, press OK 7 times.)
2. Check the TV's IP: **Settings → Device Preferences → About → Status**.
3. On the laptop (PowerShell):

```powershell
cd C:\RealmeTV\glass-tv-home
adb connect 192.168.0.13:5555
adb devices        # should show: 192.168.0.13:5555   device
```

4. Accept **"Allow USB debugging?"** on the TV (tick *Always allow*).

What we learned:
- This TV has **no "Wireless debugging"** menu. Turning on **USB debugging alone** opens network ADB on port 5555.
- `failed to authenticate` = the prompt on the TV is waiting. Accept it, then connect again.
- `actively refused` = ADB is off, or the IP is wrong. Re-check the IP in About → Status.
- Windows PowerShell 5.1 does **not** support `&&`. Run commands on separate lines.

---

## 4. What was disabled

### Batch 1 — ads, tracking, vendor clutter (`my-batch1.txt`)

| Package | What it is |
|---|---|
| `tv.anoki.acr.controller` | Content recognition (tracks what's on screen) |
| `com.homwee.aipont` | AI PONT brand portal |
| `com.google.android.tvrecommendations` | Promo / recommendation rows |
| `android.autoinstalls.config.homwee` | Silent partner-app installer |
| `com.google.android.feedback` | Feedback reporter |
| `com.homwee.store` | Vendor app store |
| `com.homwee.help` | Vendor e-manual |
| `com.homwee.howtocast` | "How to cast" tutorial |

### Batch 2 — features & apps not used (`my-batch2.txt`)

| Package | What it is |
|---|---|
| `com.google.android.katniss` | Google Assistant (**mic button stops working**) |
| `com.google.android.apps.mediashell` | Chromecast built-in (**casting from phone stops**) |
| `com.homwee.multiscreenshare` | Screen mirroring |
| `com.homwee.mmp.screenshare` | Screen mirroring |
| `com.google.android.youtube.tvmusic` | YouTube Music |
| `com.google.android.videos` | Google TV (Play Movies) |
| `com.google.android.play.games` | Play Games |
| `com.amazon.amazonvideo.livingroom` | Prime Video |
| `com.google.android.backdrop` | Ambient screensaver |
| `com.google.android.syncadapters.calendar` | Calendar sync |

Already disabled from the factory: `com.cltv.fast` (FAST channels), `com.google.android.marvin.talkback`.

Result after reboot: Home, Input/HDMI switching, YouTube and Netflix confirmed working.

---

## 5. DO NOT disable (important)

| Package | Why |
|---|---|
| `com.google.android.leanbacklauncher.partnercustomizer` | Looks like bloat, but on this platform disabling it makes the **Input key and remote hotkeys stop working after the next reboot** |
| `com.mediatek.*` (all) | Live TV, HDMI inputs, remote pairing |
| `com.homwee.synckey` | Remote key service (system app) |
| `com.homwee.factory` | Factory menu |
| `com.homwee.dmt.upgrade` | Firmware updates |
| `com.homwee.overlay.tvservice` | TV service overlay |
| `com.homwee.mmp.fileexplorer`, `com.homwee.mmp.imageplayer` | USB drive photos & files (kept on purpose) |
| `com.android.vending`, `com.google.android.gms`, `com.google.android.gsf` | Play Store / Google services |
| `com.google.android.tvlauncher` | Stock Home screen (only disable if another launcher is working) |
| `com.google.android.tungsten.setupwraith` | Setup wizard; not needed to touch |

General rule: **small batches, then reboot and test the Input key.** On this platform some breakage only appears after a reboot.

---

## 6. Apps

- Kept: YouTube, Netflix, Disney+ Hotstar, plus all user-installed apps (Spotify, iQIYI, Downloader, etc.).
- **Nuvio installed**: `app-full-armeabi-v7a-release.apk`.
  - The `arm64-v8a` build fails with `INSTALL_FAILED_NO_MATCHING_ABIS` — this TV is 32-bit.
  - **For any future APK: always choose `armeabi-v7a` (or universal).**

```powershell
adb install -r "C:\RealmeTV\app-full-armeabi-v7a-release.apk"
```

---

## 7. Ad-blocking DNS

Set to AdGuard DNS (blocks ads/trackers in most apps; does **not** block YouTube ads).

```powershell
# Turn on
adb shell settings put global private_dns_mode hostname
adb shell settings put global private_dns_specifier dns.adguard-dns.com

# Check
adb shell settings get global private_dns_mode
adb shell settings get global private_dns_specifier

# Turn off (if an app stops loading)
adb shell settings put global private_dns_mode opportunistic
```

---

## 8. Home screen — Glass TV (original + custom changes)

**Current setup: Glass TV v2.3-solat8 is the Home screen.** It is the original `cocombee/glass-tv` launcher with changes built for this TV. Tested working: remote left/right/OK, Home key, Inputs menu → HDMI, YouTube / Netflix / Nuvio, prayer times, hiding apps, both home views, and still Home after a restart.

| Item | Value |
|---|---|
| APK (keep this) | `C:\RealmeTV\realmetv-debloat\releases\glass-tv-original-v2.3-solat8.apk` (also on the repo's Releases page, v1.1) |
| SHA-256 | `57F3124DA4FDBAAB034A0BB3CA582581320A83A9936AAB27C7FE7FB24B9DFE05` |
| Based on | `cocombee/glass-tv`, commit `ce164b6` (24 Oct 2026) |
| Source changes | `launcher/glass-tv-solat.patch` in this repo |
| Package / version | `com.gathin.tvhome`, 2.3-solat8 (versionCode 12), pure Java, no native libraries |
| Stock launcher | `com.google.android.tvlauncher` — disabled |
| Previous release | v1.0 = 2.3-solat5 (first version with prayer times and hide apps) |

Designs were agreed as mock-ups before each build.

### The two home views

**Resting view** (what Home shows normally)
- Top left: 12-hour clock with a small AM/PM at the top of the digits, a thin divider, then three lines: `MONDAY` / `26 OCT 2026` / `15 JAMADILAWAL 1448`.
- Top right: three frosted icon circles (Inputs, Wi-Fi, Settings), tvOS style. The selected one turns solid white with a dark icon; the Wi-Fi name appears under the Wi-Fi circle while it is selected.
- Prayer card (left half, under the clock).
- Place caption (name, country, up to two lines of description) right-aligned on the right half, above the dock.
- The frosted dock at the bottom, with app names under every tile. Nothing below it.

**All-apps view** (Down from the dock)
- Prayer card and place caption hidden; the dock moves to the top with every other app below it. Easiest view for arranging apps.
- **Back**, **Up** from the dock, or coming back from an app returns to the resting view.
- Carrying an app down out of the dock in Move mode opens this view too.

### What was added on top of the original

**Prayer times (JAKIM), display only**
- A glass card on the left half: header `PRAYER · SGR01` with the countdown in amber on the right (`Isyak in 40min`, `Subuh in 7h 6min`).
- Five columns: large 12-hour time (`5:49` with a small `am`) over the name (SUBUH, ZOHOR, ASAR, MAGHRIB, ISYAK). The next prayer is amber.
- After Isyak the card shows tomorrow's times with SUBUH next.
- Zone **SGR01** (Gombak, Petaling, Sepang, Hulu Langat, Hulu Selangor, Shah Alam).
- Source: `api.waktusolat.app` (community service republishing JAKIM e-Solat; checked against JAKIM to the minute), with JAKIM's own `e-solat.gov.my` as backup. Current and next month cached on the TV; a failed download is retried every 30 minutes.
- Nothing in it can be selected with the remote, so navigation is unchanged.

**Selection and names**
- Selected app: solid white ring with a soft glow and a slight lift; the other tiles are veiled about 30% darker.
- App names under every tile, the dock included: dim at rest, bold white on the selected one.

**Wallpaper**
- Bing's daily homepage pictures (renewed weekly by the TV, pictures with people skipped).
- Changes **every hour at :00**. Coming back from an app after an hour passed, it changes about 6 seconds later; after standby it catches up within a minute.

**Hide / show apps (hold OK on any app)**
- **Hide from Home** — tile disappears (app is not disabled or uninstalled).
- **Hidden apps (N)** — list of hidden apps (up to 8 at a time, newest first). OK on one brings it back at the end of the grid.
- Saved on the TV; the last visible app cannot be hidden.

**Other (from the original)**
- Every TV app appears automatically; new installs join at the end.
- Hold OK → **Move** to rearrange, including into the dock.
- Top right: Inputs (HDMI), Wi-Fi, Settings (gear opens Android Settings).

### Optional settings (theme `config.json`)

```powershell
cd C:\RealmeTV\glass-tv-home
mkdir themes\my-tv -Force
'{ "prayer_zone": "SGR01", "wallpaper_minutes": 60 }' | Set-Content -Encoding ascii themes\my-tv\config.json
powershell -ExecutionPolicy Bypass -File tools\theme.ps1 -TvHost 192.168.0.13 themes\my-tv
```

| Key | Meaning |
|---|---|
| `prayer_zone` | JAKIM zone code, e.g. `WLY01` (KL/Putrajaya), `SGR03` (Klang). Default `SGR01` |
| `prayer_times` | `false` hides the prayer card and Hijri date |
| `wallpaper_minutes` | Minutes between pictures (default 60). Values that divide a day (30, 60, 120…) change on the clock |
| `hidden_apps`, `dock_apps` | Package lists, see the original repo's `themes/README.md` |

Undo a theme: `powershell -ExecutionPolicy Bypass -File tools\theme.ps1 -TvHost 192.168.0.13 -Reset`

### What did NOT work: the fork
- `aqmangge/glass-tv-home` v2.6.0: the **remote's left/right did not move the highlight** on this TV (up/down and OK worked; simulated ADB keys worked; the remote sends standard `KEY_LEFT` / `KEY_RIGHT`, checked with `getevent`).
- The original v2.3 works on the same TV, so the cause is the fork's changes to focus/navigation.
- Do not install the fork's APK over this one (same package name).

### Reinstall / update / roll back

```powershell
cd C:\RealmeTV\realmetv-debloat\releases
adb connect 192.168.0.13:5555

# Reinstall or update (keeps app order, hidden apps and cached prayer times)
adb install -r glass-tv-original-v2.3-solat8.apk
adb shell am force-stop com.gathin.tvhome

# After a factory reset: install, then make it Home
adb install -r glass-tv-original-v2.3-solat8.apk
adb shell pm grant com.gathin.tvhome android.permission.ACCESS_FINE_LOCATION
adb shell am start -n com.gathin.tvhome/.MainActivity        # test with the remote FIRST
adb shell pm disable-user --user 0 com.google.android.tvlauncher
adb shell cmd package set-home-activity --user 0 com.gathin.tvhome/.MainActivity

# Roll back to the plain original (-d allows a lower version)
adb install -r -d glass-tv-original-v2.3.apk
```

Reset everything in Glass TV (app order, hidden apps, wallpapers): **Settings → Apps → Glass TV → Clear data**.

### Rebuilding from source later
The APK was built with JDK 17 and Android SDK platform 34 from `cocombee/glass-tv` commit `ce164b6` plus `glass-tv-solat.patch`:

```bash
git clone https://github.com/cocombee/glass-tv.git && cd glass-tv
git checkout ce164b6
git apply ../glass-tv-solat.patch
cd launcher && ./gradlew assembleRelease
```

Note: it is signed with a debug key from the machine that built it. A rebuild on another machine has a different signature, so it cannot install over the current one with `-r` — uninstall first (`adb uninstall com.gathin.tvhome`), which resets app order and hidden apps.

### Go back to the stock home screen

```powershell
adb shell pm enable com.google.android.tvlauncher
adb shell cmd package set-home-activity --user 0 com.google.android.tvlauncher/com.google.android.tvlauncher.MainActivity
adb uninstall com.gathin.tvhome      # optional, removes Glass TV
```

---

## 9. Undo / restore

With ADB connected (Section 3):

```powershell
cd C:\RealmeTV\glass-tv-home

# Undo Batch 2
powershell -ExecutionPolicy Bypass -File tools\restore.ps1 -TvHost 192.168.0.13 my-batch2.txt

# Undo Batch 1
powershell -ExecutionPolicy Bypass -File tools\restore.ps1 -TvHost 192.168.0.13 my-batch1.txt

# Re-enable a single app
adb shell pm enable <package.name>
```

Without a computer: **Settings → Apps → See all apps → Show system apps → pick the app → Enable**.

Note: `restore.ps1` also re-enables the stock launcher and makes it Home again.

Factory reset also brings everything back, but erases all apps and sign-ins (last resort).

---

## 10. After finishing

- **Turn off USB debugging** in Developer options. Port 5555 is unencrypted; while it is on, any device on the home network can control the TV.
- Check it is closed:

```powershell
adb disconnect
adb connect 192.168.0.13:5555     # should now be refused
```

- Optional: in the router, give the TV a fixed IP (DHCP reservation) so `192.168.0.13` doesn't change.
- Keep a copy of the `C:\RealmeTV` folder somewhere safe (OneDrive / USB drive).

---

## 11. References

- Launcher in use: https://github.com/cocombee/glass-tv (original Glass TV, based on CleanYourTV)
- Debloat tooling & example lists: https://github.com/aqmangge/glass-tv-home (fork of the above)
- Original playbook idea: https://github.com/amirulmzlan/android-tv-debloat
- Nuvio: official NuvioTV releases on GitHub (`NuvioMedia/NuvioTV`)

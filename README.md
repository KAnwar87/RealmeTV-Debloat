# RealmeTV Debloat

Notes, package lists and a small launcher patch from cleaning up one **Realme TV** (Android TV 11) —
no root, nothing uninstalled, everything reversible.

This is a record of what worked on one set, shared in case it helps other owners of the same
Changhong-made "AI PONT" platform. It is not a general tool, and it has only been tested on this TV.

## The TV

| | |
|---|---|
| Sold as | Realme TV |
| Made by | Changhong (`ro.product.manufacturer`) |
| Model / codename | `AI PONT` / `ikebukuro` |
| System | Android TV 11, **32-bit only** (`armeabi-v7a`) |
| Same platform as | CHiQ, RCA and BenQ "AI PONT" sets |

## Result

- **18 packages disabled** in two small batches: content-recognition tracking, auto-installers,
  vendor store, promo rows, Assistant, Chromecast, mirroring and unused preloaded apps.
- **Still working:** HDMI / Input key, remote, Live TV, YouTube, Netflix, Disney+ Hotstar,
  USB photos and files, firmware updates.
- **Ad-blocking DNS** (AdGuard) via Android's Private DNS.
- **Glass TV launcher** as the Home screen, with a patch adding JAKIM prayer times, the Hijri date,
  hourly wallpapers and hide/show apps.

## What's in this repo

| Path | What it is |
|---|---|
| [`docs/RealmeTV-Debloat-Notes.md`](docs/RealmeTV-Debloat-Notes.md) | Full write-up: connecting over ADB, every step, undo, troubleshooting |
| [`debloat/my-batch1.txt`](debloat/my-batch1.txt) | Batch 1 — ads, tracking, vendor clutter |
| [`debloat/my-batch2.txt`](debloat/my-batch2.txt) | Batch 2 — features and apps not used (edit to taste) |
| [`debloat/DO-NOT-DISABLE.md`](debloat/DO-NOT-DISABLE.md) | Packages that look like bloat but break things |
| [`backup/`](backup/) | Package snapshots taken before and after |
| [`launcher/`](launcher/) | `glass-tv-solat.patch`, build steps and licence |

APK files are attached to the **Releases** page, not committed.

## Quick start

1. On the TV: **Settings → Device Preferences → About → Build** (press OK 7 times), then
   **Developer options → USB debugging = ON**. On this TV that alone opens network ADB on port 5555.
2. On a computer on the same network, get [`adb`](https://developer.android.com/tools/releases/platform-tools)
   and clone [aqmangge/glass-tv-home](https://github.com/aqmangge/glass-tv-home) for its
   `tools\disable.ps1` / `restore.ps1` scripts.
3. Read [`DO-NOT-DISABLE.md`](debloat/DO-NOT-DISABLE.md), adjust the batch lists, then apply **one batch
   at a time**, rebooting and testing the Input key after each.
4. **Turn USB debugging off** when finished — port 5555 is unencrypted.

Details, exact commands and undo steps are in the [notes](docs/RealmeTV-Debloat-Notes.md).

## Things learned the hard way

- `leanbacklauncher.partnercustomizer` looks like bloat, but on this platform disabling it breaks the
  Input key **after the next reboot**.
- This TV is 32-bit: sideloaded apps need the `armeabi-v7a` (or universal) build, otherwise
  `INSTALL_FAILED_NO_MATCHING_ABIS`.
- The `aqmangge/glass-tv-home` 2.6.0 launcher did not respond to the remote's left/right on this TV,
  although simulated keys worked. The original `cocombee/glass-tv` 2.3 works with the same remote,
  so the launcher here is built from the original.
- Windows PowerShell 5.1 has no `&&`; run commands on separate lines.

## Credits

- [cocombee/glass-tv](https://github.com/cocombee/glass-tv) — the Glass TV launcher this patch is for
- [Gathin23/cleanyourtv](https://github.com/Gathin23/cleanyourtv) — the debloat approach and launcher Glass TV is built on (MIT)
- [aqmangge/glass-tv-home](https://github.com/aqmangge/glass-tv-home) — Windows scripts and the Changhong example lists used as a starting point
- [amirulmzlan/android-tv-debloat](https://github.com/amirulmzlan/android-tv-debloat) — the original AI-agent playbook idea
- [api.waktusolat.app](https://api.waktusolat.app/docs) and JAKIM [e-Solat](https://www.e-solat.gov.my) — prayer time data
- Bing — daily wallpaper images, fetched by the TV itself

## Disclaimer

You change your own device at your own risk. Not affiliated with Realme, Changhong, Google or any
of the projects above. Everything here disables rather than uninstalls, and the notes include how
to undo each step.

## Licence

MIT — see [`LICENSE`](LICENSE). The launcher patch modifies MIT-licensed code by Gathin; see
[`launcher/LICENSE-glass-tv.txt`](launcher/LICENSE-glass-tv.txt).

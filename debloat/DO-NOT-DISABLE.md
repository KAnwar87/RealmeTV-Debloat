# Do not disable on this TV

| Package | Why |
|---|---|
| `com.google.android.leanbacklauncher.partnercustomizer` | Looks like bloat, but disabling it breaks the **Input key and remote hotkeys after the next reboot** |
| `com.mediatek.*` | Live TV, HDMI inputs, remote pairing |
| `com.homwee.synckey` | Remote key service (system app) |
| `com.homwee.factory` | Factory menu |
| `com.homwee.dmt.upgrade` | Firmware updates |
| `com.homwee.overlay.tvservice` | TV service overlay |
| `com.homwee.mmp.fileexplorer`, `com.homwee.mmp.imageplayer` | USB drive files and photos |
| `com.android.vending`, `com.google.android.gms`, `com.google.android.gsf` | Play Store / Google services |
| `com.google.android.tvlauncher` | Stock Home - only disable once another launcher works |

Rule of thumb: small batches, then **reboot and test the Input key**. On this platform some breakage only shows after a reboot.

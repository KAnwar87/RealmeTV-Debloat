# YouTube without ads (patched YouTube for Android TV)

**Status:** installed October 2026, still being tested. No ads seen so far, but that is not yet a long-term result.

This uses [pearlxcore/YouTube-TV-NoAds](https://github.com/pearlxcore/YouTube-TV-NoAds) v0.1.2 (commit `dc495ce`). All the patching work is theirs. This page only records how it was built for this TV and the one change that was needed.

No patched APK is shared here. It is Google's app, so build your own from your own files.

## How it works, briefly

The patcher takes the official YouTube for Android TV **5.30.320 (armeabi-v7a)** files, adds an ad-filter hook to the app's native libraries, renames it to "YouTube TV NoAds" and signs it with a key generated on your own PC.

It installs as a **separate app** (`local.x90h.youtube.tv.experimental`). The stock YouTube is not touched, so undoing it is just an uninstall.

## The one change: base.apk hash

The patcher accepts only the exact files the author used, checked by SHA-256.

| File | Author's hash | APKMirror 5.30.320 armeabi-v7a bundle |
|---|---|---|
| `split_config.armeabi_v7a.apk` | `5ca175e0…26cc4da` | same ✅ |
| `base.apk` | `418826127ad7…07f3f` | `ba1ce4398a9e…1df616` ❌ |

The split, which holds the native code the ad hook patches, matched. The base did not, and no other APKMirror upload matched either.

Before using it, I checked that the APKMirror `base.apk` is signed by the same Google certificate as the YouTube already on the TV:

```powershell
$bt = "C:\Android\build-tools\36.0.0"
& "$bt\apksigner.bat" verify --print-certs <stock>\base.apk | findstr SHA-256
& "$bt\apksigner.bat" verify --print-certs <apkmirror>\base.apk | findstr SHA-256
```

Both gave signer digest `21199d112cece428c82cb0df221da24ff8674c7a982a83a17a970eec43305b4a`.

After that, `BASE_HASH` in `patcher.py` was changed to the APKMirror hash. That is the only edit; `ARM_HASH` was left as it is. See [`youtube/base-hash.patch`](../youtube/base-hash.patch). All the patch steps applied and the build checks passed with this base.

If your base.apk hash is different again, check the certificate the same way before trusting it.

## Build (Windows)

Tools: Python 3.12, Java 21 (Temurin), Git, WSL Ubuntu-24.04 with `clang lld`, Apktool 3.0.3 and Android Build Tools 36.0.0.

```powershell
git clone https://github.com/pearlxcore/YouTube-TV-NoAds.git
cd YouTube-TV-NoAds
git apply <path-to>\base-hash.patch     # only if your base.apk matches ba1ce439…
python -m pip install -r requirements.txt
python patcher.py `
  --base <apks>\base.apk `
  --arm-split <apks>\split_config.armeabi_v7a.apk `
  --apktool C:\Tools\apktool_3.0.3.jar `
  --build-tools C:\Android\build-tools\36.0.0 `
  --output <fresh-folder>
```

Back up `<fresh-folder>\.signing\` right away and keep it private. Future updates of the app must be signed with the same key.

## Install and undo

```powershell
adb install <fresh-folder>\youtube-tv-noads-0.1.2.apk
adb uninstall local.x90h.youtube.tv.experimental    # to undo
```

## Things to know

- **No automatic updates.** If YouTube changes something and the app breaks, use the stock app until there is a new patch.
- **Keep the stock YouTube enabled.** It is the fallback, and it updates by itself.
- **Terms of service.** Modifying the app and skipping ads is against YouTube's terms. This is your own decision and your own risk.
- **Filenames with brackets.** APKMirror names contain `(armeabi-v7a)`, which PowerShell reads as code. Use `-LiteralPath '...'` with single quotes.
- **WSL drops you into Ubuntu.** After `wsl --install`, the prompt is inside Ubuntu, so run `sudo apt install -y clang lld` there directly, without the `wsl` prefix.

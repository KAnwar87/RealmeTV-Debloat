# Glass TV - the build used on this TV

The Home screen is **Glass TV** from [cocombee/glass-tv](https://github.com/cocombee/glass-tv)
(built on [CleanYourTV](https://github.com/Gathin23/cleanyourtv), MIT licence - see `LICENSE-glass-tv.txt`),
with `glass-tv-solat.patch` applied:

- JAKIM prayer card: five prayers in 12-hour time, the next one in amber with a countdown (display only)
- Clock with day / date / Hijri date beside it
- Resting view with the dock alone at the bottom; Down opens all apps with the dock on top
- Stronger selection (white ring, glow, others veiled) and app names under every tile
- tvOS-style icon circles at the top right; place caption on the right half
- Wallpaper changes every hour at :00
- Hide / show apps from the hold-OK menu

## Build

```bash
git clone https://github.com/cocombee/glass-tv.git && cd glass-tv
git checkout ce164b6
git apply /path/to/glass-tv-solat.patch
cd launcher && ./gradlew assembleRelease      # JDK 17, Android SDK platform 34
# output: app/build/outputs/apk/release/app-release.apk
```

The ready APK (`glass-tv-original-v2.3-solat8.apk`, SHA-256
`57F3124DA4FDBAAB034A0BB3CA582581320A83A9936AAB27C7FE7FB24B9DFE05`) is attached to this repo's
GitHub Release (v1.1) rather than committed to git. v1.0 holds the earlier 2.3-solat5 build.

Tested on one TV only. Use at your own risk.

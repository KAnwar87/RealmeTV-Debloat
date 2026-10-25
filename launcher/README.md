# Glass TV - the build used on this TV

The Home screen is **Glass TV** from [cocombee/glass-tv](https://github.com/cocombee/glass-tv)
(built on [CleanYourTV](https://github.com/Gathin23/cleanyourtv), MIT licence - see `LICENSE-glass-tv.txt`),
with `glass-tv-solat.patch` applied:

- JAKIM prayer times: five prayers in 12-hour time, the next one in capitals with a countdown (display only)
- Hijri date beside the date
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

The ready APK (`glass-tv-original-v2.3-solat5.apk`, SHA-256
`B897F4DAA5E5885E564FE39DEA7C3C8A63746B8161A5B3BBA3940C90DC20CAA3`) is attached to this repo's
GitHub Release rather than committed to git.

Tested on one TV only. Use at your own risk.

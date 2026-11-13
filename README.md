[**中文版**](READMECN.md) | [**日本語**](READMEJP.md) *(the translations are the upstream Miku UI text)*

# Miku-Picked UI (Android 16)

![MikuUI](img/MikuUI.webp)

**Miku-Picked UI** = **Miku** UI + cherry-**picked** features.

This is the complete **Miku UI A16 (Blooming V2)**, with every Miku repository, app and feature kept,
plus a curated set of features from **Infinity X**, **AxionOS**, **uwuAOSP**, **Evolution X** and **Lunaris AOSP**,
and the **OneUisland** dynamic island.
It also builds **unmodified LineageOS 23.2 device trees**.

All credits are listed on the device in **Settings → About phone → Thanks to**, and below.

> **Full step-by-step guide (requirements, sync, build, flashing the Galaxy S21, troubleshooting): [BUILDING.md](BUILDING.md)**

Sync source code
----------------

```shell
repo init -u https://github.com/Miku-Picked-UI/manifesto -b picked-16 --git-lfs
repo sync -c --force-sync --no-clone-bundle --no-tags
```

Build
-----

```shell
. build/envsetup.sh

# Miku device trees
lunch miku_<codename>-bp4a-userdebug

# ...or an unmodified LineageOS 23.2 device tree
lunch lineage_<codename>-bp4a-userdebug     # or: breakfast <codename>

# Make a diva! ("m bacon" and "brunch <codename>" work too)
m diva
```

`lineage.dependencies` files are resolved automatically (LineageOS org, `lineage-23.2`).
Android TV and Automotive Lineage products are not supported.

Ported devices
--------------

| Device | Codename | Device manifest | Lunch |
|---|---|---|---|
| Samsung Galaxy S21 Exynos (SM-G991B) | `o1s` | [`local_manifests/o1s.xml`](local_manifests/o1s.xml) | `lineage_o1s-bp4a-userdebug` |

```shell
mkdir -p .repo/local_manifests
cp .repo/manifests/local_manifests/o1s.xml .repo/local_manifests/
repo sync -c --force-sync --no-clone-bundle --no-tags
. build/envsetup.sh
lunch lineage_o1s-bp4a-userdebug
m bacon            # MIKU_GAPPS=true m bacon for a build with Google apps
```

The o1s trees and kernel are [Macktherealonereally](https://github.com/Macktherealonereally)'s
`lineage-23.2-20261031` release, based on the [exy2100](https://github.com/exy2100) trees, with the
`hardware/samsung` patches of that release. Vendor blobs, `hardware/samsung`,
`device/samsung_slsi/sepolicy` and `hardware/samsung_slsi-linaro/exynos` come from exy2100, the other
`hardware/samsung_slsi-linaro` repos from LineageOS. Everything is forked to this org (`picked-16`).
Not included (optional in the upstream builds): OpenEUICC (eSIM), the krazey IMS stack (VoLTE/VoWiFi),
Aurora Store. Without lineage-sdk there is no settings UI for LiveDisplay, charging control and
wireless fast charging.

Galaxy S21 specific:
- **SIM 2 / eSIM:** SIM tray 2 and the eSIM share the second SIM slot. Pick which one it uses in
  Settings → Network & internet → SIM 2 slot (Macktherealonereally's RIL slot switch).
- **USB tethering** uses NCM. The kernel's NCM driver is fixed so tethering no longer stalls under
  load (e.g. games on the tethered PC); RNDIS does not work with Linux hosts and is not used.

GSI
---

```shell
. build/envsetup.sh
lunch miku_gsi_phone_arm64-bp4a-userdebug
m systemimage
```

## Picked features

| Feature | From | Where to find it |
|---|---|---|
| Sleep mode (manual, scheduled, sunset/sunrise; QS tile) | Evolution X | Settings → Miku no Secret Stuff → Picked features |
| QS tile toggle animations (12 styles) | Evolution X | Picked features |
| Custom carrier label | Evolution X | Picked features |
| Status bar logo | Infinity X | Picked features |
| Battery bar | Infinity X | Picked features |
| Advanced restart: recovery, bootloader, SystemUI | AxionOS (LineageOS) | Picked features |
| Status/nav bar burn-in protection | AxionOS | automatic on devices with `config_enableBurnInProtection` |
| Flashlight fade in/out | AxionOS | automatic on devices with adjustable torch strength |
| 5G QS tile | AxionOS | Quick Settings edit mode |
| Auto brightness QS tile | uwuAOSP | Quick Settings edit mode |
| Full screen apps for display cutouts | uwuAOSP | Picked features |
| Deep sleep time in uptime | uwuAOSP | Settings → About phone → Status |
| Dynamic island: music, calls, timers, screen recording, flashlight, charging, notifications with reply, alerts; frosted or liquid glass cards | [OneUisland](https://github.com/csabaikende-ctrl/OneUisland) (built into SystemUI) | Picked features → Show dynamic island / Island settings |
| Separate notifications and quick settings (left half / right half pull-down) | Lunaris AOSP | Picked features |
| Switch between notifications and quick settings like One UI: pull down from the top edge on the other side, or swipe sideways; animated like One UI / ColorOS | Miku-Picked UI | automatic with separate notifications and quick settings |
| Landscape split shade switch | Lunaris AOSP | Picked features |
| Screenshot flash switch, hide status bar in screenshots | Lunaris AOSP | Picked features |
| Volume slider haptics switch | Evolution X | Picked features |
| Custom lockscreen clocks | Evolution X | Picked features → Lockscreen clock |
| Fingerprint icon for in-display sensors | Evolution X | Picked features → Fingerprint icon |
| Emoji styles: Android, Blobmoji, OpenMoji, Twemoji, or your own emoji font (.ttf/.otf) | Evolution X / AxionOS | Picked features → Emoji style (restart to apply) |
| System font picker (Miku's, now linked) | Miku UI | Picked features → Fonts |
| AxionFx audio effects, with ViPER4Android's Clarity (Natural, OZone+, X-HiFi), Dynamic System (bass) and Speaker optimization | AxionOS, [ViPERFX_RE](https://github.com/AndroidAudioMods/ViPERFX_RE) | AxionFx app, QS tile, or a music app's equalizer button |
| Resizable home screen folders: long-press a folder and drag the corner handle to any size that fits (2x1, 2x2, 4x2…); bigger folders show more apps, and tapping one opens it directly | AxionOS | Long-press a folder on the home screen |
| Skip tracks with the volume keys while the screen is off (hold up = next, hold down = previous) | Miku-Picked UI (like LineageOS) | Picked features → Skip tracks with volume keys |
| Now Bar: music (tap for a large player), flashlight, timers, calls and other live activities on the lock screen; liquid glass, frosted glass or black; placed around the fingerprint sensor | Miku-Picked UI (in the style of One UI) | Picked features → Now Bar |
| System-wide Liquid Glass: SurfaceFlinger draws blurs as glass; glass Quick Settings tiles, squishy PIN keys and notifications; Liquid Glass glyphs and ColorOS tile animations | PenguinOS (backported from Android 17, with permission) | Picked features → System-wide Liquid Glass, Glass style (Liquid Glass / ColorOS) |
| Liquid Glass brightness slider, and a media volume slider in Quick Settings | PenguinOS | Picked features → Volume slider in Quick Settings |
| Liquid Glass tab bars in apps (floating capsule with liquid motion) | PenguinOS | Picked features → Liquid Glass tab bars in apps, Float tab bars over the page |
| Volume panel styles: One UI (floating card, centres with every slider when expanded), MyUI (frosted card), ColorOS (glass column that slims to the edge), Expandable (a slider per stream) | PenguinOS | Picked features → Volume panel style |
| Liquid Glass back gesture arrow | Miku-Picked UI (PenguinOS' glass) | automatic with System-wide Liquid Glass |
| Lockscreen charging info: live current, power, voltage and battery temperature under the charging text | Evolution X / AxionOS | Picked features → Charging info on the lockscreen |
| Liquid Glass buttons in apps (iOS 26 style glass capsules) | Miku-Picked UI | Picked features → Liquid Glass buttons in apps |
| Adjustable shade blur strength | Miku-Picked UI | Picked features → Shade blur |
| ColorOS app open/close animations (apps grow out of and close into their icon on springs) | PenguinOS | Picked features → ColorOS app animations |
| The lockscreen clock flies onto a home screen clock widget on unlock | PenguinOS | Picked features → Clock flies home on unlock |
| LTE only while tethering: keeps the modem off 5G while hotspot/USB/Bluetooth tethering is on (cooler, less battery), 5G comes back afterwards | Miku-Picked UI | Picked features → LTE only while tethering |
| Screenshots: allow screenshots in secure apps (FLAG_SECURE off), hide screenshots from apps (no screenshot detection), and a screenshot detection tester | Miku-Picked UI (like BlassGO's FlagSecure Disabler, built into the framework) | Picked features → Screenshots |
| Google spoof for wearables: apps see a Google phone, so Pixel Watch and other watch apps can pair (attestation untouched) | GoogleSpoofForWear by 73sydney, built in | Picked features → Connected devices (restart to apply) |

Plus **LineageOS 23.2 device tree support** (`vendor/lineage`, `lineage.dependencies`, LineageOS AIDL HAL sepolicy,
LineageOS HAL compatibility matrix, LineageOS `wpa_supplicant_8` with Broadcom WPA3 support).

## Updating (dirty flash)

Builds can be flashed over each other without a wipe, as long as they are signed with the same keys
and you don't switch between a build with and without Google apps. Sideload the new zip in recovery.

Local builds keep the same build fingerprint, so Android can reuse stale cached app data and apps like
Settings crash after the update. After the first boot, clear the package cache once:

```shell
adb root
adb shell rm -rf /data/system/package_cache/*
adb reboot
```

## Be careful

> If you build with an "ify-only" device configuration (one adapted from another ROM), do not release the build. It's for your own use only.
> Respect the original authors of device trees.

## Thanks to

- [Miku UI](https://github.com/Miku-UI): the base of everything
- [Android Open Source Project](https://source.android.com)
- [LineageOS](https://github.com/LineageOS): HALs, device-tree conventions, the `vendor/lineage` compatibility
- [Infinity X](https://github.com/ProjectInfinity-X): status bar logo, battery bar
- [AxionOS](https://github.com/AxionAOSP): AxionFx audio effects, resizable home screen folders (Localhorst04, Saikrishna1504, rmp22), burn-in protection, advanced restart with Restart SystemUI, flashlight fade, 5G tile, emoji style switch
- [uwuAOSP](https://github.com/uwuAOSP): deep sleep info, auto brightness tile, full screen cutout apps
- [Evolution X](https://github.com/Evolution-X): sleep mode, QS tile animations, custom carrier label, volume slider haptics switch, custom lockscreen clocks, fingerprint icons, emoji style switch
- [Lunaris AOSP](https://github.com/Lunaris-AOSP): separate notifications/QS shade, landscape split shade switch, screenshot options
- [OneUisland](https://github.com/csabaikende-ctrl/OneUisland) by Spottq, with additions by csabaikende-ctrl: the dynamic island (synced from that repository, not hosted in this org)
- [PenguinOS](https://github.com/Project-PenguinOS) by redducc and Saikiran: Liquid Glass in SurfaceFlinger and SystemUI, glass tab bars in apps, Liquid Glass sliders, ColorOS app animations and tile animations, the lockscreen clock flight home, volume panel styles; backported to Android 16, used with permission
- [ViPERFX_RE](https://github.com/AndroidAudioMods/ViPERFX_RE) by Iscle / AndroidAudioMods (via [WSTxda's fork](https://github.com/WSTxda/ViPERFX_RE)): ViPER4Android's Clarity, Dynamic System and speaker correction in AxionFx, used with permission
- 73sydney: GoogleSpoofForWear (Magisk module), the idea behind the Google spoof for wearables toggle
- Blobmoji (C1710, Apache 2.0), OpenMoji (HfG Schwäbisch Gmünd, CC BY-SA 4.0), Twemoji (Twitter and contributors, CC BY 4.0): the bundled emoji styles
- [exy2100](https://github.com/exy2100) (ata-kaner, mst8981, Flopster101): Galaxy S21 Exynos device trees, kernel, vendor and Samsung/SLSI hardware forks
- [Macktherealonereally](https://github.com/Macktherealonereally): Galaxy S21 Exynos fixes (VoLTE, eSIM and the SIM 2 / eSIM slot switch, NFC, WPA3 hotspot, 60 fps video, charge limit) and the trees/kernel used here
- ExtremeXT and everyone on Exynos AOSP
- crDroid, AICP, OmniROM, PixelExperience and every project those features first came from
- Every original author of a picked commit (authorship is kept in git history)
- Claude (Anthropic, Claude Opus 5.5): porting and integration

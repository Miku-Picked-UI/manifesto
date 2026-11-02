[**中文版**](READMECN.md) | [**日本語**](READMEJP.md) *(the translations are the upstream Miku UI text)*

# Miku-Picked UI (Android 16)

![MikuUI](img/MikuUI.webp)

**Miku-Picked UI** = **Miku** UI + cherry-**picked** features.

This is the complete **Miku UI A16 (Blooming V2)**, with every Miku repository, app and feature kept,
plus a curated set of features from **Infinity X**, **AxionOS**, **uwuAOSP** and **Evolution X**.
It also builds **unmodified LineageOS 23.2 device trees**.

All credits are listed on the device in **Settings → About phone → Thanks to**, and below.

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

Plus **LineageOS 23.2 device tree support** (`vendor/lineage`, `lineage.dependencies`, LineageOS AIDL HAL sepolicy).

## Be careful

> If you build with an "ify-only" device configuration (one adapted from another ROM), do not release the build. It's for your own use only.
> Respect the original authors of device trees.

## Thanks to

- [Miku UI](https://github.com/Miku-UI): the base of everything
- [Android Open Source Project](https://source.android.com)
- [LineageOS](https://github.com/LineageOS): HALs, device-tree conventions, the `vendor/lineage` compatibility
- [Infinity X](https://github.com/ProjectInfinity-X): status bar logo, battery bar
- [AxionOS](https://github.com/AxionAOSP): burn-in protection, advanced restart with Restart SystemUI, flashlight fade, 5G tile
- [uwuAOSP](https://github.com/uwuAOSP): deep sleep info, auto brightness tile, full screen cutout apps
- [Evolution X](https://github.com/Evolution-X): sleep mode, QS tile animations, custom carrier label
- crDroid, AICP, OmniROM, PixelExperience and every project those features first came from
- Every original author of a picked commit (authorship is kept in git history)
- Claude (Anthropic, Claude Opus 5.5): porting and integration

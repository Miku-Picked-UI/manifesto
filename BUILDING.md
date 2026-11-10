# Building Miku-Picked UI

This guide builds Miku-Picked UI (Android 16) from source: first in general, then step by step
for the **Samsung Galaxy S21 Exynos (SM-G991B, `o1s`)**.

> **Heads-up:** this tree has not been compiled yet. The first build is the real test, so expect
> to possibly hit a build error. If you do, see [When the build fails](#when-the-build-fails).

## 1. What you need

| | Minimum | Recommended |
|---|---|---|
| OS | 64-bit Linux (Ubuntu 22.04/24.04, Debian 12, Arch, Fedora) | Ubuntu 24.04 |
| Disk | 400 GB free (source ~200 GB + `out/` ~150 GB) | 500 GB+ on an SSD |
| RAM | 16 GB + 32 GB swap | 32 GB or more |
| CPU | 4 cores | 8+ cores (a full build takes ~1.5 h on 16 cores, many hours on 4) |
| Internet | ~150 GB download for the first sync | |

Windows/macOS: use a Linux machine or a Linux VM/WSL2 with enough disk and RAM; macOS is not supported.

### Install the build packages

**Ubuntu / Debian:**

```shell
sudo apt update
sudo apt install bc bison build-essential ccache curl flex g++-multilib gcc-multilib git git-lfs \
  gnupg gperf imagemagick lib32readline-dev lib32z1-dev libdw-dev libelf-dev libssl-dev \
  libsdl1.2-dev libxml2 libxml2-utils lz4 lzop pngcrush protobuf-compiler python3-protobuf \
  python-is-python3 rsync schedtool squashfs-tools xsltproc zip unzip zlib1g-dev fontconfig
```

**Arch Linux:**

```shell
sudo pacman -S --needed base-devel bc ccache cpio git git-lfs gnupg gperf imagemagick lz4 lzop \
  libxml2 libxslt openssl pngcrush protobuf python python-protobuf rsync squashfs-tools zip unzip \
  zlib fontconfig ncurses
```

(For 32-bit libraries on Arch also enable `multilib` and install `lib32-gcc-libs lib32-zlib lib32-ncurses`.)

### Install `repo` and set up git

```shell
mkdir -p ~/bin
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

git lfs install
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### Optional: ccache (much faster rebuilds)

```shell
echo 'export USE_CCACHE=1' >> ~/.bashrc
echo 'export CCACHE_EXEC=/usr/bin/ccache' >> ~/.bashrc
source ~/.bashrc
ccache -M 50G
```

## 2. Download the source

```shell
mkdir -p ~/miku-picked && cd ~/miku-picked
repo init -u https://github.com/Miku-Picked-UI/manifesto -b picked-16 --git-lfs
```

**For the Galaxy S21 Exynos**, add its device manifest *before* syncing (this brings the device,
kernel, vendor and Samsung/SLSI hardware repos):

```shell
mkdir -p .repo/local_manifests
cp .repo/manifests/local_manifests/o1s.xml .repo/local_manifests/
```

Then sync (the first sync takes a long time):

```shell
repo sync -c -j$(nproc --all) --force-sync --no-clone-bundle --no-tags
```

If the sync stops with network errors, just run the same command again; it continues where it
stopped. Use a lower `-j` (e.g. `-j4`) on slow connections.

## 3. Build for the Galaxy S21 Exynos (o1s)

```shell
cd ~/miku-picked
source build/envsetup.sh
lunch lineage_o1s-bp4a-userdebug
m bacon -j$(nproc --all)
```

- `m bacon` and `m diva` are the same thing. `breakfast o1s` = the `lunch` line above,
  `brunch o1s` = `breakfast` + `m diva`.
- **With Google apps** (MindTheGapps): `MIKU_GAPPS=true m bacon -j$(nproc --all)`
- **User build** (no root over adb, like an official release): `lunch lineage_o1s-bp4a-user`

When it finishes you get, in `out/target/product/o1s/`:

| File | What it is |
|---|---|
| `MikuPickedUI-Blooming-o1s-<date>-UNOFFICIAL.zip` (`GAPPS-UNOFFICIAL` with Google apps) | the ROM, flash it from recovery |
| `….zip.sha256sum` | checksum |
| `recovery.img` | the recovery, flash it with Odin |

Building again after changes: run the same three commands; only changed parts are rebuilt.
To start over completely: `m clean` (or `rm -rf out`).

### Other devices

Any **LineageOS 23.2** device tree builds unchanged: add it (and its kernel/vendor repos) in a
local manifest or let `breakfast <codename>` fetch it from LineageOS, then
`lunch lineage_<codename>-bp4a-userdebug && m bacon`. Native Miku trees use
`lunch miku_<codename>-bp4a-userdebug && m diva`.

## 4. Install on the Galaxy S21 Exynos (SM-G991B)

> Installing wipes all data and permanently trips Knox (Samsung Pay/Wallet and Secure Folder stop
> working for good). Make a backup first.

Requirements:

- **Bootloader unlocked:** Settings → Developer options → OEM unlocking, then in download mode
  long press Volume Up to unlock.
- **Stock firmware G991BXXSJHZC2 (Android 15)** flashed first (all of BL/AP/CP/CSC with Odin, or
  `odin4` on Linux). Older firmware is known to hang at the boot logo. Firmware can be downloaded
  with e.g. [samloader](https://github.com/topjohnwu/samloader-rs).
- On the computer: Odin (Windows) or `odin4`/Heimdall (Linux), and `adb` (Android platform-tools).

Steps:

1. Pack the recovery so Odin accepts it:

   ```shell
   cd out/target/product/o1s
   tar --format=ustar -cf recovery.tar recovery.img
   ```

2. Power the phone off. Hold **Volume Up + Volume Down** and plug in the USB cable → download mode.
3. In Odin, put `recovery.tar` in the **AP** slot, **untick Auto Reboot**, press Start.
4. Leave download mode with **Volume Down + Power**; the moment the screen turns off, switch to
   **Volume Up + Power** (cable still connected) to boot straight into recovery. If stock Android
   boots instead, it puts back the stock recovery: repeat steps 2–4.
5. In recovery: *Factory reset* → *Format data/factory reset*.
6. *Apply update* → *Apply from ADB*, then on the computer:

   ```shell
   adb sideload MikuPickedUI-Blooming-o1s-*.zip
   ```

   (The package also writes `dtbo`, `vbmeta` and `vendor_boot`.) If it stops at 47% and says
   "Total xfer: 1.00x", that is normal.
7. *Reboot system now*. The first boot takes several minutes.

**Updates:** build again, then in recovery *Apply update* → *Apply from ADB* and sideload the new
zip. No wipe is needed unless you switch between a build with and without Google apps.
After the first boot of the update, clear the package cache once (local builds keep the same build
fingerprint, so stale cached app data can make Settings and other apps crash):

```shell
adb root
adb shell rm -rf /data/system/package_cache/*
adb reboot
```

## 5. What works and what doesn't on o1s

From the upstream trees (exy2100 + Macktherealonereally `lineage-23.2-20261031`), plus Miku-Picked UI:

- Included: device trees, kernel, vendor, `hardware/samsung` with the release patches (60 fps video,
  charge limit HAL, screen-off fingerprint, enroll fix), WPA3 including WPA3 hotspot.
- Not included in this manifest (optional extras of the upstream builds):
  - **VoLTE/VoWiFi** (krazey open-source IMS stack) and **eSIM** (OpenEUICC). The tree enables them
    automatically if you add those repos; see
    [galaxy-s21-exynos-lineageos-fixes](https://github.com/Macktherealonereally/galaxy-s21-exynos-lineageos-fixes).
  - Aurora Store.
- No settings screens for LiveDisplay, charging limit, wireless fast charging and PowerShare
  (these need LineageOS' lineage-sdk, which Miku UI does not use).

## 6. When the build fails

1. Scroll up to the **first** line containing `FAILED:` or `error:`; everything after it is follow-up noise.
2. Save the full log, it makes reporting much easier:

   ```shell
   m bacon -j$(nproc --all) 2>&1 | tee build.log
   ```

3. Common problems:

| Message | Fix |
|---|---|
| `Killed` / `ninja: build stopped` with no error, or `java.lang.OutOfMemoryError` | not enough RAM: add swap (32 GB), build with fewer jobs (`m bacon -j4`) |
| `No space left on device` | free disk space (see section 1); `m clean` removes `out/` |
| `duplicate path` / `duplicate project` during `repo sync` | the same project is in two manifests: remove it from your own file in `.repo/local_manifests/` |
| `fatal: couldn't find remote ref` during sync | run `repo sync` again; if it stays, the branch is missing on GitHub, report it |
| `error: ... is not a valid product` after `lunch` | the device manifest was not copied, or `repo sync` was not run after copying it |
| `module "X" ... depends on undefined module "Y"` | a missing repo: report the exact message |
| an error in `vendor/miku/oneuisland`, `OneUislandHost` or `dev.flexy.dynamicisland` | the dynamic island integration; report the error |
| `vendor/miku/oneuisland/src` missing / `OneUisland` module has no sources | the OneUisland repo wasn't synced: run `repo sync vendor/miku/oneuisland/src` |

4. Report the first error (and `build.log` if possible) by opening an issue on
   [Miku-Picked-UI/manifesto](https://github.com/Miku-Picked-UI/manifesto/issues), with the device
   and the `lunch` line you used.

## Source layout

- Manifest: [`default.xml`](default.xml) + [`snippets/`](snippets) (Miku UI, LineageOS, CAF projects)
- Device manifests: [`local_manifests/`](local_manifests)
- All modified repositories are in [github.com/Miku-Picked-UI](https://github.com/Miku-Picked-UI), branch `picked-16`.

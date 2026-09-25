# Project Infinity-X 4.0 for Realme GT Neo 5 SE (senna_jr / RMX3700)

![Android](https://img.shields.io/badge/Android-17%20(CP2A)-blue?style=flat-square)
![Status](https://img.shields.io/badge/Build-UNOFFICIAL-orange?style=flat-square)
![SELinux](https://img.shields.io/badge/SELinux-Enforcing-success?style=flat-square)
![GMS](https://img.shields.io/badge/GMS-Included-brightgreen?style=flat-square)

Unofficial community port of **Project Infinity-X 4.0 (Android 17)** for the **Realme GT Neo 5 SE** (`senna_jr` / `RMX3700` / `RMX3701`) with built-in Google Apps (GMS).  
*Personal community build, not affiliated with Project Infinity-X or LineageOS.*

* **Build:** `Project_Infinity-X-4.0-BETA-senna_jr-25.09.2026-GAPPS-UNOFFICIAL`
* **Type:** `userdebug`, `BUILD_ID=CP2A.260605.016`
* **Security Patch:** `2026-09-01`

> **Important Notices:**
> * **Hobby Build:** Signed with the public AOSP testkey. Any APK signed with that key gets platform permissions.
> * **Integrity:** AVB is off (`vbmeta` and `vbmeta_system` flashed with verity/verification disabled). Play Integrity fails; Google Pay and strict banking apps will not work out of the box.
> * **Security:** adb is secure (`ro.adb.secure=1`, `ro.debuggable=0`) and SELinux is enforcing. No adb root in system.
> * **Updates:** No recovery sideload due to dual-slot metadata quirks. All installations and updates are manual fastboot flashes or via Fastboot Firmware Flasher.
> * **Partitioning:** The build is single-slot (`_a`). Stock `my_*` service partitions are deleted to make space for the Android 17 system and GAPPS.
> * **Kernel:** Stock prebuilt `Image` (5.10.236-android12-9) and vendor modules are used verbatim. Sources: https://github.com/realme-kernel-opensource

---

## Highlights & Features

* **Android 17 base** (Project Infinity-X 4.0 with pixel UI features and customizations)
* **Built-in Google Apps (GMS)** pre-installed
* **Stock Display HAL (Prebuilt)**: Full hardware composition (no GPU composition lag), smooth 144 Hz refresh rate
* **In-display Fingerprint (UDFPS)**: Fully operational at all brightness levels (via SystemUI dim-layer cutout)
* **Voice calls & VoLTE**: Functional earpiece and microphone
* **Hotspot & Tethering**: Wi-Fi hotspot and USB tethering work out of the box (kernel 5.10 compatibility fix applied to `netd`)

---

## Tested Base (Read Before Flashing)

Exactly one firmware base was tested: **Global RMX3701**:
```text
ro.build.display.id      = RMX3701_16.0.5.1010(EX01)
ro.build.version.ota     = RMX3701_11.H.43_3430_202607021803
ro.boot.prjname          = 22623
ro.product.vendor.device = RE58D1L1
```

Check your parameters before flashing:
```bash
adb shell getprop ro.boot.prjname
adb shell getprop ro.product.vendor.device
```
> If `prjname` is not `22623` or vendor device is not `RE58D1L1`, do not flash. Chinese ColorOS bases are untested.

---

## Hardware Status

### Working
* Boot and system stability (daily driver ready)
* Display at 144 Hz with hardware composition (real, measured)
* Touch response and gestures
* Speaker, earpiece audio, microphones, voice calls (VoLTE)
* Mobile data & Wi-Fi
* Wi-Fi hotspot and USB tethering
* In-display optical fingerprint scanner (at all brightness levels)
* All 4 cameras, photo & video recording
* NFC (card emulation & tags)
* Bluetooth audio and headsets
* GPS / satellite navigation
* DRM video playback (Widevine L1)
* Hardware sensors (accelerometer, light, proximity) and vibration
* SELinux Enforcing

### Known Issues
* Stock RealmeUI service partitions (`my_*`) are wiped from super during installation to fit the Android 17 GAPPS image into the dynamic group. Returning to stock requires a full stock firmware package.

---

## Installation Guide

### Prerequisites
* Unlocked bootloader.
* Latest Android platform-tools: https://developer.android.com/studio/releases/platform-tools
* Firmware archive unpacked to `C:\senna-flash\release-20260925-infinitix` (or short path without spaces/Cyrillic).
* **Full backup:** Clean flash is required. All user data will be wiped.

### Option 1: Fastboot Firmware Flasher (Recommended)

1. Download Fastboot Firmware Flasher (FFF).
2. Copy folder `InfinityX_4.0_20260925_senna_jr` into FFF's `FIRMWARE` folder.
3. Open FFF → "Прошивальщик" → select `InfinityX_4.0_20260925_senna_jr`.
4. Enable "Отключить проверку VBMeta" (Disable VBMeta verification).
5. Connect phone via USB in ADB or Fastboot mode.
6. Click "В FastbootD" → "Старт".

### Option 2: flash.bat

1. Extract archive to a short path (e.g. `C:\senna-flash\release-20260925-infinitix`).
2. Power off phone → hold `Volume Down + Power` to enter bootloader.
3. Connect cable to PC.
4. Run `flash.bat` as Administrator and follow prompts.
5. Wait for the process to complete and device to reboot.

### Option 3: Manual commands

**Bootloader stage:**
```cmd
set PATH=C:\platform-tools;%PATH%
cd /d C:\senna-flash\release-20260925-infinitix

fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
fastboot --disable-verity --disable-verification flash vbmeta_system vbmeta_system.img
fastboot flash vbmeta_vendor vbmeta_vendor.img

fastboot flash boot_a boot.img
fastboot flash dtbo_a dtbo.img
fastboot flash vendor_boot_a vendor_boot.img
fastboot flash recovery_a recovery.img

fastboot reboot fastboot
```

**fastbootd stage:**
```cmd
fastboot delete-logical-partition my_carrier_a
fastboot delete-logical-partition my_region_a
fastboot delete-logical-partition my_company_a
fastboot delete-logical-partition my_engineering_a
fastboot delete-logical-partition my_heytap_a
fastboot delete-logical-partition my_manifest_a
fastboot delete-logical-partition my_preload_a
fastboot delete-logical-partition my_product_a
fastboot delete-logical-partition my_stock_a
fastboot delete-logical-partition my_bigball_a

fastboot flash system_a system.img
fastboot flash system_ext_a system_ext.img
fastboot flash product_a product.img
fastboot flash vendor_a vendor.img
fastboot flash vendor_dlkm_a vendor_dlkm.img
fastboot flash odm_a odm.img
fastboot flash odm_dlkm_a odm_dlkm.img

fastboot erase metadata
fastboot erase userdata
fastboot reboot
```

*First boot takes 2–5 minutes due to initial setup and GMS dex optimization.*

---

## How to Revert to Stock

1. Download the full stock firmware package (`RMX3701_16.0.5.1010(EX01)`).
2. Flash all stock images (including `my_*` logical partitions) in fastbootd.
3. Flash stock `boot`, `dtbo`, `vendor_boot`, `recovery`, `vbmeta`, `vbmeta_system`, `vbmeta_vendor` **without** disable flags in bootloader.
4. `fastboot erase metadata && fastboot erase userdata && fastboot reboot`

---

## Download
* **Google Drive (Firmware Package):** https://drive.google.com/file/d/1VMSTe4oNScRyuoOr6tTpZPsqBzl1UzIa/

---

## Credits & Sources

* **LolD:O:** https://4pda.to/forum/index.php?showuser=9090479 — original device tree (`senna_jr`) and OnePlus `sm8450-common` base.
* **Project Infinity-X Team:** https://github.com/ProjectInfinity-X — ROM base and UI.
* **The LineageOS Team:** https://github.com/LineageOS — upstream platform and hardware dependencies.
* **Realme / Oplus:** https://github.com/realme-kernel-opensource — official kernel releases.

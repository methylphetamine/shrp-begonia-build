# SHRP build for Redmi Note 8 Pro (`begonia`)

GitHub Actions recipe that compiles **SHRP** (SkyHawk Recovery Project)
`recovery.img` for the Xiaomi Redmi Note 8 Pro, codename `begonia`.

The device tree is a SHRP-flavoured copy of the PBRP 12.1 tree: it keeps the
working kernel, DTB, DTBO, keymaster/gatekeeper blobs and fstab, and adds the
`twrp_begonia` product plus the SHRP board variables.

## What this builds

| | |
|---|---|
| SHRP manifest | `SHRP/manifest` @ `shrp-12.1` (branch tip, not pinned) |
| SHRP build system | `SHRP/shrp_build` @ `android-12.1` |
| SHRP recovery source | `SHRP/bootable_recovery` @ `android-12.1` |
| SHRP vendor config | `SHRP/android_vendor_shrp` @ `v3_12` |
| TWRP vendor config | `SHRP/android_vendor_twrp` @ `android-12.1` |
| Device tree | `methylphetamine/device_xiaomi_begonia-shrp` @ `shrp-12.1` |
| Product / lunch | `twrp_begonia` / `twrp_begonia-eng` |
| Make target | `recoveryimage` |
| Output | `out/target/product/begonia/recovery.img` |

SHRP's `shrp-12.1` manifest is labelled WIP upstream and was last updated in
2022. It is used here because it matches the Android 12.1 device tree; the
more recent `v3_11.0` branch is Android 11 and would need a separate tree.

## Usage

1. **Actions → SHRP build (begonia) → Run workflow**
2. Options:
   - `makefile` — default `twrp_begonia-eng`
   - `build_target` — default `recoveryimage`
   - `manifest_branch` — default `shrp-12.1`
   - `jobs` — default `4` (the runner has 4 vCPUs)
   - `ccache` — default `true`
   - `publish_release` — attach the image to a GitHub release
3. When the job goes green, download `shrp-begonia-recovery` from the run's
   **Artifacts** section.

Every run does a fresh `repo init` / `repo sync`, and the **Record SHRP source
revisions** step prints the exact commits used.

## Publishing

After a successful compile the workflow:

1. uploads `recovery.img` as the `shrp-begonia-recovery` Actions artifact;
2. uploads it anonymously to Gofile and prints the download page;
3. sends it to Telegram as a document when it is under Telegram's 50 MB bot
   limit, otherwise sends the Gofile link and SHA-256.

Telegram publishing uses two repository secrets: `TELEGRAM_BOT_TOKEN` and
`TELEGRAM_CHAT_ID`. Gofile needs no credentials. Both uploads are best-effort
and never fail the build.

## Device tree notes

The tree carries its own kernel/DTB/DTBO and decryption blobs, so no kernel
build or proprietary vendor tree is needed. `BoardConfig.mk` sets the usual
TWRP flags plus:

```make
SHRP_PATH := device/xiaomi/begonia
SHRP_MAINTAINER := methylphetamine
SHRP_DEVICE_CODE := begonia
SHRP_REC_TYPE := Treble
SHRP_DEVICE_TYPE := A_Only
SHRP_REC := /dev/block/bootdevice/by-name/recovery
```

## Repository layout

```
.github/workflows/build.yml   the CI recipe
local_manifests/begonia.xml   device tree registration
```

# MPC-HC DLSS5

**Community launcher for running DLSS 5 Neural Rendering / Feature 18 with MPC-HC on Windows.**

The goal is simple: keep the mature playback, audio, subtitle and seeking behavior of MPC-HC while inserting the DLSS5 Neural Rendering path into the rendering chain.

> **Status:** experimental/community project. Not affiliated with or endorsed by NVIDIA, MPC-HC, ReShade, RenoDX, DLSS5-Feeder or LumeniteFX.

## What it does

The current pipeline is:

```text
MPC-HC
  -> ReShade
  -> LumeniteFX Kernel
  -> DLSS5-Feeder
  -> NVIDIA NGX / DLSS
  -> RenoDX DLSS5
  -> Neural Rendering Feature 18
```

The launcher provides:

- monitor and final output resolution selection;
- temporary Windows display-resolution switching;
- automatic resolution restoration after MPC-HC closes;
- DLSS work resolution from **50% to 100%**;
- stable and experimental temporal profiles;
- fullscreen launch and optional direct video selection;
- support for custom `nvngx_dlss.dll`, `nvngx_dlssnr.dll`, `renodx-dlss5.addon64` and `dlss5-feed.addon64` builds.

## Installation

### 1. Extract the package

Extract the complete release package to a normal writable folder. Do not run it directly from inside the ZIP.

### 2. Run the installer

Run:

```text
00_INSTALAR.cmd
```

The installer prepares a **portable** MPC-HC runtime inside the extracted package folder.

During installation it:

1. verifies the bundled installer payload;
2. downloads the supported MPC-HC portable build;
3. downloads and installs ReShade beside that portable MPC-HC copy;
4. patches the portable `mpc-hc64.exe` import table so local `dxgi.dll` / ReShade loads early;
5. requests the Windows **High performance GPU** preference for this MPC-HC executable;
6. installs/copies the DLSS, RenoDX and DLSS5-Feeder runtime files;
7. installs the LumeniteFX Kernel and required ReShade shader files;
8. writes the ReShade, Feeder and launcher configuration;
9. compiles `MPC-HC DLSS5 Launcher.exe` as an x64 Windows GUI application.

The daily launcher uses normal user permissions (`asInvoker`) and does not require administrator elevation for normal use.

### 3. Open the launcher

After installation, run:

```text
MPC-HC DLSS5 Launcher.exe
```

`01_ABRIR_LAUNCHER.cmd` is also provided as a convenience shortcut.

## Where the runtime files are installed

The installer creates this runtime directory **inside the extracted package folder**:

```text
Runtime\MPC-HC-DLSS5-FINAL\
```

The main layout is:

```text
<package folder>\
│
├─ MPC-HC DLSS5 Launcher.exe
├─ RUNTIME_PATH.txt
│
└─ Runtime\
   └─ MPC-HC-DLSS5-FINAL\
      ├─ mpc-hc64.exe
      ├─ dxgi.dll
      │
      ├─ nvngx_dlss.dll
      ├─ nvngx_dlssnr.dll
      ├─ renodx-dlss5.addon64
      ├─ dlss5-feed.addon64
      │
      ├─ ReShade.ini
      ├─ ReShadePreset.ini
      ├─ dlss5-feed.cfg
      │
      └─ reshade-shaders\
         ├─ Shaders\
         │  ├─ DLSS5_Feed.fx
         │  ├─ lumenite_Kernel.fx
         │  ├─ ReShade.fxh
         │  └─ include\...
         └─ Textures\...
```

`RUNTIME_PATH.txt` tells the launcher which portable runtime directory it should use.

### Exact location of custom DLL/add-on files

These four files all belong directly beside `mpc-hc64.exe`:

```text
Runtime\MPC-HC-DLSS5-FINAL\nvngx_dlss.dll
Runtime\MPC-HC-DLSS5-FINAL\nvngx_dlssnr.dll
Runtime\MPC-HC-DLSS5-FINAL\renodx-dlss5.addon64
Runtime\MPC-HC-DLSS5-FINAL\dlss5-feed.addon64
```

They do **not** go inside the shader folder.

## Using custom DLSS / RenoDX / Feeder binaries

The launcher intentionally does **not** enforce a fixed SHA-256 or version after installation.

To test a custom build:

1. close MPC-HC and the launcher;
2. open:

   ```text
   Runtime\MPC-HC-DLSS5-FINAL\
   ```

3. back up the currently working file if desired;
4. replace the compatible file while keeping the expected filename exactly the same;
5. reopen `MPC-HC DLSS5 Launcher.exe`.

Supported replaceable runtime files are:

```text
nvngx_dlss.dll
nvngx_dlssnr.dll
renodx-dlss5.addon64
dlss5-feed.addon64
```

The launcher checks that required files exist, but it does not block modified/custom versions.

> Re-running the full installer can recreate the runtime and overwrite custom files, so keep a backup of any custom build you want to preserve.

Custom binary combinations can crash, fail to initialize or produce different output depending on GPU, driver and component version.

## Why this repository is public

This repository exists so the launcher and installer behavior can be inspected instead of asking users to trust an opaque executable.

The launcher source is in [`src/Launcher.cs`](src/Launcher.cs), and the installation/patch scripts are in [`scripts/`](scripts/).

The daily launcher uses:

```text
requestedExecutionLevel="asInvoker"
```

so **normal launcher use does not request administrator privileges**.

No telemetry, password collection, account-token collection or machine-identification system is intentionally implemented in the launcher.

See [SECURITY.md](SECURITY.md) for the exact changes the installer makes.

## Why Windows/antivirus may warn

This project does several things that are unusual for a normal media player:

- uses a locally built, unsigned launcher;
- loads ReShade through a local `dxgi.dll`;
- patches the **portable MPC-HC executable's import table** so DXGI/ReShade loads early;
- works with third-party DLSS/RenoDX add-ons.

Those characteristics can trigger reputation-based warnings even when the files are expected.

A warning is **not proof that a file is safe or malicious**. The point of this repository is that users can inspect the source, build the launcher themselves, and compare hashes.

## Verified Feature 18 indicators

A working session has produced log messages including:

```text
SuperSampling.Available=1
feature ready
frame N delivered
feature 18 created via the signed snippet
inline feature 18 evaluation succeeded
```

These log lines are stronger evidence than a visible image change alone.

## Verification

Run:

```text
02_VERIFICAR_DLSS5.cmd
```

The verifier checks the latest Feeder/RenoDX logs for the expected initialization and Feature 18 evaluation messages.

## Source build

The repository is intended to make the build path inspectable:

```text
public source -> Windows compiler -> launcher executable
```

The current installer compiles the launcher locally as an optimized x64 Windows GUI executable and removes the temporary source copy used during that installation build.

## Full runtime package

The source repository intentionally does **not** commit large/proprietary NVIDIA runtime DLLs or other third-party binary payloads.

The public runtime package used during current testing is:

```text
MPC_HC_DLSS5_v2.3.8_PUBLIC.zip
SHA-256:
86E988DA2C9F30781FBDBF2A873A56FA3360791A7290BA319548060AADA1CD63
```

The full package and any future release assets should be distributed through a release/download channel appropriate for the third-party component licenses and file sizes.

## Support

The application is free. The support dialog in the launcher is optional and can be disabled by the user.

| Buy Me a Coffee | PayPal | Mercado Pago |
|---|---|---|
| [buymeacoffee.com/leoavilagg](https://buymeacoffee.com/leoavilagg) | [paypal.me/leoavilagg](https://paypal.me/leoavilagg) | Alias: `leovilagg.mp` |

The same support destinations remain embedded in the launcher UI.

## Project files

- [`src/Launcher.cs`](src/Launcher.cs) — graphical launcher source.
- [`scripts/install.ps1`](scripts/install.ps1) — runtime installation logic.
- [`scripts/patch_import.ps1`](scripts/patch_import.ps1) — portable MPC-HC PE import patch.
- [`scripts/verify.ps1`](scripts/verify.ps1) — log verification.
- [`SECURITY.md`](SECURITY.md) — security/transparency notes.
- [`THIRD_PARTY.md`](THIRD_PARTY.md) — third-party components.
- [`CHANGELOG.md`](CHANGELOG.md) — public release notes.

## Requirements

- Windows x64.
- NVIDIA GPU/driver compatible with the selected DLSS/NGX runtime.
- Internet connection during installation of downloaded third-party components.
- .NET Framework 4.x compiler/runtime for the current launcher build path.

## Disclaimer

DLSS 5 / Neural Rendering Feature 18 usage here is experimental/community work and may change as NVIDIA drivers and third-party projects evolve.

Use custom binaries at your own risk and keep backups of working configurations.

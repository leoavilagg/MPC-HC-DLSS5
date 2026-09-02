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

## Source build

A GitHub Actions workflow is included at:

[`/.github/workflows/build-launcher.yml`](.github/workflows/build-launcher.yml)

It builds the launcher from the public `Launcher.cs` source on a Windows runner and publishes it as a workflow artifact.

That makes the build path visible:

```text
public source -> GitHub Actions -> launcher artifact
```

## Full runtime package

The source repository intentionally does **not** commit large/proprietary NVIDIA runtime DLLs or other third-party binary payloads.

The public runtime package used during current testing is:

```text
MPC_HC_DLSS5_v2.3.8_PUBLIC.zip
SHA-256:
86E988DA2C9F30781FBDBF2A873A56FA3360791A7290BA319548060AADA1CD63
```

The full package and any future release assets should be distributed through a release/download channel appropriate for the third-party component licenses and file sizes.

## Custom binaries

The launcher does **not** enforce a fixed runtime SHA after installation.

Users may replace compatible versions of:

```text
nvngx_dlss.dll
nvngx_dlssnr.dll
renodx-dlss5.addon64
dlss5-feed.addon64
```

This is intentional so different GPUs, drivers and experimental versions can be tested.

Custom binary combinations can crash or fail to initialize; compatibility is not guaranteed.

## Support

The application is free. The support dialog in the launcher is optional and can be disabled by the user.

| Buy Me a Coffee | PayPal | Mercado Pago |
|---|---|---|
| [buymeacoffee.com/leoavilagg](https://buymeacoffee.com/leoavilagg) | [paypal.me/leoavilagg](https://paypal.me/leoavilagg) | Alias: `leovilagg.mp` |

The same support destinations are embedded in the launcher UI.

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

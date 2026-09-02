# Security & Transparency

This document describes what **MPC-HC DLSS5** changes and what it does **not** intentionally do.

## Daily launcher privileges

The graphical launcher is built with:

```xml
<requestedExecutionLevel level="asInvoker" uiAccess="false"/>
```

Normal launcher use does not intentionally request UAC elevation.

## Files changed by the installer

The project operates on its own portable MPC-HC runtime directory.

It installs/copies components beside that portable MPC-HC executable, including ReShade/DLSS-related files.

The installer patches the portable `mpc-hc64.exe` import table so it imports:

```text
dxgi.dll!CreateDXGIFactory
```

This is used to make the local ReShade/DXGI path load early enough.

A backup of the original portable MPC-HC executable is retained by the installation scripts.

The project does **not** intentionally patch the copy of MPC-HC installed elsewhere on the system.

## Windows settings changed

A per-user Windows Graphics preference is written under:

```text
HKCU\Software\Microsoft\DirectX\UserGpuPreferences
```

for the portable MPC-HC executable, using:

```text
GpuPreference=2;
```

This asks Windows to use the high-performance GPU for that executable.

## Local application data

Launcher preferences and display-resolution recovery state are stored below the current user's LocalAppData:

```text
MPC-HC-DLSS5
```

This is used for launcher settings and restoring a display mode after an interrupted session.

## Network behavior

During installation, scripts download third-party components from their upstream locations.

The launcher itself does not intentionally implement telemetry, analytics, password collection, browser-cookie collection, account-token collection or machine fingerprinting.

## Antivirus / SmartScreen

The launcher is currently unsigned, and the portable MPC-HC executable is modified after download.

Unsigned binaries, DLL injection/hooking frameworks such as ReShade, and modified PE import tables can receive warnings from SmartScreen or antivirus products.

Do not treat a warning alone as proof either way. Verify the repository, source, release hash and build path.

## Reproducible review

The public launcher source is located at:

```text
src/Launcher.cs
```

A GitHub Actions workflow compiles that source on `windows-latest`.

This lets reviewers inspect the code and build a launcher without relying on a precompiled copy supplied by the author.

## Custom binaries

Runtime hash enforcement is intentionally disabled in the launcher.

Users can test alternative compatible DLSS, RenoDX and Feeder binaries. The launcher checks required-file presence, not a fixed SHA/version.

This flexibility also means a modified binary can introduce instability or malicious behavior. Only use custom binaries from sources you trust.

## Reporting a security issue

If you find behavior that contradicts this document, open a GitHub issue with:

- affected version;
- file/hash involved;
- steps to reproduce;
- relevant log excerpt.

Do not include passwords, tokens or other secrets in public issues.

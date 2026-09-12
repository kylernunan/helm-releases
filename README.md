# Ebbwater Helm releases

Installers for [Ebbwater Helm](https://ventures.nunan.com), the command view for
fleets of coding agents. Download the latest release for your platform:

- Windows: `ebbwater-desktop-win-x64.exe`
- macOS (Apple silicon): `ebbwater-desktop-mac-arm64.dmg`
- Linux: `ebbwater-desktop-linux-x86_64.AppImage`, `.deb` or `.rpm`

The macOS build is not yet notarized: right-click the app and choose Open on
first launch. Windows installers are unsigned for the beta; accept the
SmartScreen prompt.

This repository holds only the release workflow and the published artifacts.
The source lives in a private repository and is built here so the installers
can be produced on GitHub's runners and updated in-app.

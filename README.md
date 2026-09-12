<div align="center">
  <h1>Ebbwater Helm</h1>
  <p><strong>Command every coding-agent session from one place.</strong></p>
  <p>
    <a href="https://github.com/kylernunan/helm-releases/releases/latest">Download the latest release</a>
    ·
    <a href="#install">Install</a>
    ·
    <a href="#first-launch">First launch</a>
    ·
    <a href="#reach-helm-from-your-phone">Phone access</a>
    ·
    <a href="#troubleshooting">Troubleshooting</a>
  </p>
</div>

Welcome. Helm is a desktop and web app for people who run more than one coding
agent at a time. It puts every session in one fleet view (the Armada), shows
the live thread of what each agent is doing, lets you approve or stop actions
from any device, and tracks what it all costs. It runs a private server on
your own machine; nothing leaves it except the calls you make to model
providers.

This repository holds the installers and the release workflow. The source is
developed privately and built here so the installers can be produced on
GitHub's runners and updated in-app.

## Which download

| You use | Download | Notes |
|---|---|---|
| Windows 10 or 11, 64-bit | `ebbwater-desktop-win-x64.exe` | One-click installer, per-user, no admin needed |
| Mac with Apple silicon (M1 or later) | `ebbwater-desktop-mac-arm64.dmg` | Not yet notarized; see the macOS steps |
| Mac with Intel | not built yet | Use `npx ebbwater-helm` in a browser meanwhile |
| Linux, any distribution | `ebbwater-desktop-linux-x86_64.AppImage` | Portable, no install |
| Debian or Ubuntu | `ebbwater-desktop-linux-amd64.deb` | |
| Fedora, RHEL, openSUSE | `ebbwater-desktop-linux-x86_64.rpm` | |
| Anything with Node.js 22.5+ | `npx ebbwater-helm` | Server and web UI, no desktop shell |

Get them from the [latest release](https://github.com/kylernunan/helm-releases/releases/latest).
The `.blockmap` and `latest*.yml` files are for the in-app updater; you do not
need to download them.

## Install

### Windows

1. Download `ebbwater-desktop-win-x64.exe` and run it.
2. Windows SmartScreen will say the publisher is unknown, because the beta is
   not yet code-signed. Choose **More info**, then **Run anyway**.
3. The installer finishes in a few seconds and starts Helm. It installs to
   `%LOCALAPPDATA%\Programs` for your user only, so no administrator prompt.

Uninstall from Settings → Apps, or run the uninstaller in the install folder.

### macOS (Apple silicon)

1. Download `ebbwater-desktop-mac-arm64.dmg`, open it, and drag **Helm Beta**
   into Applications.
2. The beta is not notarized yet, so the first launch is blocked with "cannot
   be opened because the developer cannot be verified". Either:
   - right-click the app in Applications and choose **Open**, then **Open**
     again in the dialog, or
   - in Terminal: `xattr -dr com.apple.quarantine "/Applications/Helm Beta.app"`
3. After that first approval it opens normally.

### Linux

**AppImage** (works everywhere):

```bash
chmod +x ebbwater-desktop-linux-x86_64.AppImage
./ebbwater-desktop-linux-x86_64.AppImage
```

If your distribution lacks FUSE 2, run with `--appimage-extract-and-run` or
install `libfuse2`.

**Debian or Ubuntu:**

```bash
sudo apt install ./ebbwater-desktop-linux-amd64.deb
```

**Fedora, RHEL, openSUSE:**

```bash
sudo dnf install ./ebbwater-desktop-linux-x86_64.rpm
```

The package installs a launcher named **Helm Beta**.

### npm (server and web UI only)

```bash
npx ebbwater-helm
```

Opens the same Helm at http://127.0.0.1:4096 in your browser, without the
desktop shell. Needs Node.js 22.5 or newer. `npx ebbwater-helm desktop`
downloads the desktop installer for your platform instead.

## First launch

Helm starts a private server on your machine and opens a short setup:

1. **Your name.** What Helm should call you. It greets you by name on the Home
   screen.
2. **Your local server.** Confirms the server is running, where it keeps its
   data, and offers to create a *Default Project* folder in Documents for
   sessions that have no repository yet.
3. **Providers.** Connect the models you want to use. If you already use the
   opencode CLI, its keys are imported automatically and show as connected.
   Otherwise paste an API key for OpenCode Zen or Go, Anthropic, OpenAI or
   Google here; two hundred more providers are available later under
   **Providers**.

Then you land on Home. Start a session from the composer at the bottom, or
open the **Armada** for the fleet view.

### Where your data lives

Sessions, approvals, audit history and provider keys are stored in the shared
opencode database under your home directory:

- Windows: `%USERPROFILE%\.local\share\opencode\`
- macOS and Linux: `~/.local/share/opencode/`

The desktop app, `npx ebbwater-helm` and the opencode CLI all read the same
database, so a session started in one shows up in the others.

## Reach Helm from your phone

Helm's web UI fits a phone screen. To reach it over a
[Tailscale](https://tailscale.com) tailnet with no port in the address:

```bash
npx ebbwater-helm serve --password choose-a-long-secret --tailnet
```

Then open `https://<this-computer>.<your-tailnet>.ts.net` on the phone and
sign in as `opencode` with that password. Turn on the bell in the top bar to
get a notification when an agent needs your approval.

Requirements: MagicDNS and HTTPS certificates enabled in the Tailscale admin
console. Never use `tailscale funnel` for this; Helm runs shell commands and
belongs inside your tailnet only.

## Updates

The desktop app checks this repository for new releases when it starts and
every ten minutes, and offers to download and install them. You can also
download any release from the list above and install it over the current one;
your data is kept.

## Troubleshooting

**"Server unreachable" in the header.** The app's local server did not start
or the app is pointed at a server that is not running. Open **Server** in the
left rail and pick the local entry, or quit and relaunch. On Windows the
per-launch log lives under
`%APPDATA%\OC.Ebbwater.Desktop.Beta\logs\<timestamp>\main.log`.

**A provider says "Not authorized" or 401/403.** The key is missing or wrong.
Open **Providers**, select the provider, and connect a key. If you set the key
as an environment variable, restart Helm so the server sees it.

**An agent looks stuck.** The status strip above the composer shows what it is
doing and for how long. A running tool can legitimately take minutes; if the
model itself has gone quiet the strip says so and offers **Stop**.

**macOS still refuses to open the app.** Use the `xattr` command above, or
check System Settings → Privacy & Security for an **Open Anyway** button after
the first failed attempt.

**Windows Defender quarantined the installer.** The beta is unsigned. Restore
it from Protection history, or download again and choose Run anyway.

## About

Ebbwater Helm is made by Ebbwater LLC. It contains code derived from the
opencode project (MIT licence; see THIRD-PARTY-NOTICES in the installed app).
OpenCode Zen, Go and Black are third-party subscriptions Helm can connect to;
they are not Ebbwater products.

Found a problem? [Open an issue](https://github.com/kylernunan/helm-releases/issues/new).

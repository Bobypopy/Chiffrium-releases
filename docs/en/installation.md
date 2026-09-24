# Installation

*[Version française](../installation.md)*

## Windows

1. Download `CryptoAnalyzer-Setup-<version>.exe` from the
   [latest version](https://github.com/Bobypopy/CryptoAnalyzer-releases/releases/latest).
2. Double-click it. Windows shows a blue screen, "Windows protected your PC": that is SmartScreen, which
   distrusts any executable that is little downloaded and not signed with a commercial certificate. Click
   *More info*, then *Run anyway*.
3. The installer offers to install for the current user (no administrator rights) or for everyone on the
   machine.
4. Installation takes several minutes: the application carries a Java runtime and the dependency inventory
   tool, about 900 MB and a great many small files. An antivirus that inspects each file makes it longer
   still.

A shortcut is added to the Start menu. The application runs with nothing else to install.

### Check what you downloaded

Next to the installer, every release publishes a `latest.yml` file holding the SHA-512 fingerprint of the
`.exe` (base64-encoded) - the very one the application uses to validate its own updates. To compare it:

```powershell
[Convert]::ToBase64String((Get-FileHash .\CryptoAnalyzer-Setup-<version>.exe -Algorithm SHA512).Hash `
  -split '(..)' -ne '' -replace '^', '0x' | ForEach-Object { [byte]$_ })
```

More simply, with Python:

```bash
python -c "import base64,hashlib,sys; print(base64.b64encode(hashlib.sha512(open(sys.argv[1],'rb').read()).digest()).decode())" CryptoAnalyzer-Setup-<version>.exe
```

The value must match the `sha512` field of `latest.yml` (`latest-linux.yml` for the Linux packages). If it
differs, do not run the file.

### Silent installation (managed fleet)

The installer is an NSIS one; it accepts the usual options:

```powershell
CryptoAnalyzer-Setup-<version>.exe /S /D=C:\Program Files\CryptoAnalyzer
```

`/S` installs without a user interface, `/D` chooses the folder (last argument, without quotes).

## Linux

### Debian, Ubuntu and derivatives

```bash
sudo apt install ./cryptoanalyzer-desktop_<version>_amd64.deb
```

The package installs the application under `/opt`, with its menu entry and its icon. The usual graphical
dependencies (GTK, NSS) are pulled in by `apt` if they are missing.

### AppImage (any distribution)

```bash
chmod +x CryptoAnalyzer-<version>.AppImage
./CryptoAnalyzer-<version>.AppImage
```

Nothing is installed: the file holds the whole application. On a system without `libfuse2`, run it with
`--appimage-extract-and-run`.

## Where the data lives

| System | Folder |
|---|---|
| Windows | `%APPDATA%\CryptoAnalyzer\` |
| Linux | `~/.config/CryptoAnalyzer/` |

There you will find the encrypted database, the reports, the application log (`desktop.log`) and the master
key protected by the system keyring (DPAPI on Windows, libsecret or kwallet on Linux).

## Uninstalling

Windows: *Settings > Apps > CryptoAnalyzer > Uninstall*.
Debian/Ubuntu: `sudo apt remove cryptoanalyzer-desktop`.
AppImage: delete the file.

**Uninstalling keeps your data** (analyses, accounts, settings), so that you can reinstall without losing
anything. To wipe everything, delete the folder listed above.

## Updating

The application checks for new versions at start-up and then every six hours, and offers an *Update* button
once the download is ready. You can also install over the top: your data is kept.

The check contacts only the publication repository of the application (github.com), nothing else. On a
machine without internet access it fails silently: the application works as usual.

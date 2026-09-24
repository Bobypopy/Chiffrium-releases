# CryptoAnalyzer - cryptographic inventory and post-quantum migration

*[Version française](README.md)*

A large enough quantum computer will break RSA and the elliptic curves. Before migrating, you need to know
what you are using: where, in which file, with which key size. That inventory is the first step asked for by
NIST, ANSSI and the BSI, and it is exactly what this tool does.

CryptoAnalyzer reads the code of a project - a Git repository or a folder on your disk - and produces the
inventory of its cryptography: algorithms, keys, certificates, protocols. Every item is checked against a
policy (recommended, acceptable, deprecated, disallowed) and placed on the post-quantum map. The tool also
reports the classic mistakes: a hardcoded secret, disabled TLS validation, predictable randomness, key
derivation that is too weak.

Everything runs on your machine. **Your code is never sent anywhere.**

The interface is available in French and English; pick the language in the top bar.

## Download

**-> [Latest version](https://github.com/Bobypopy/CryptoAnalyzer-releases/releases/latest)**

| System | File | What to do with it |
|---|---|---|
| Windows 10/11 (64-bit) | `CryptoAnalyzer-Setup-<version>.exe` | Double-click, then follow the installer |
| Linux (Debian, Ubuntu...) | `cryptoanalyzer-desktop_<version>_amd64.deb` | `sudo apt install ./cryptoanalyzer-desktop_<version>_amd64.deb` |
| Linux (any distribution) | `CryptoAnalyzer-<version>.AppImage` | `chmod +x`, then run it; nothing is installed |

Expect about 270 MB to download and 900 MB installed: the application carries everything it needs (analysis
engine, Java runtime, dependency inventory). Nothing else has to be installed, and it works without an
internet connection - except to clone a remote repository or to look up known vulnerabilities.

The Windows installation takes several minutes: it unpacks a great many files. That is normal.

> **Windows says "Windows protected your PC"**
> The installer is not signed with a commercial certificate (several hundred euros a year for a free
> project). Click *More info*, then *Run anyway*. You can check the fingerprint of the file before opening
> it: see [docs/en/installation.md](docs/en/installation.md#check-what-you-downloaded).

## Three minutes in

1. Start the application and choose **On this machine**.
2. Create your administrator account - the application hands you the token it asks for.
3. **New analysis**: paste the URL of a public Git repository, or pick a folder on your disk.
4. Wait. One to five minutes for an ordinary project; the screen follows every step.
5. Read the report: the score and *what weighs most*, then the assets, the security findings, the graph.

You can take away a CycloneDX 1.6 CBOM, a SARIF file for code review, a complete archive, or a printable
sheet (PDF) for a committee.

## What the tool can and cannot do

It **finds** the cryptography written in the code and in the configuration files, for about fifteen languages
(Java, Kotlin, Python, JavaScript/TypeScript, Go, C#, C/C++, PHP, Ruby, Rust, Swift...), the keys and
certificates present in the repository, the declared dependencies and their known vulnerabilities, and it
tells you where the post-quantum migration stands.

It **does not find** what exists only at runtime: an algorithm chosen in a configuration file absent from the
repository, a dynamically loaded library, a dependency computed by a build script. It never runs Maven,
Gradle, npm or any script of the analysed project - a deliberate security choice: analysing an unknown
repository must not execute its code.

A report is therefore a starting point for review, not a verdict.

## Your data

The analysed code, the reports and the keys stay on your machine, encrypted in your user profile. Only two
exchanges leave the machine, and both can be switched off:

- cloning the Git repository you asked for, to the server you named;
- the **names and versions** of dependencies, sent to the public OSV database to find out whether they are
  known to be vulnerable. Never a line of your code.

No telemetry, no account to create anywhere. Details:
[docs/en/security-and-data.md](docs/en/security-and-data.md).

## As a team

A shared server lets several people run analyses, group them by team and apply a common cryptographic
policy, from a browser or from the desktop application. It installs with Docker or Kubernetes:
[docs/en/team-server.md](docs/en/team-server.md).

## Updates

The application checks for new versions at start-up and then every six hours, and downloads them in the
background. An *Update* button appears when one is ready. Your analyses and settings are kept.

## Documentation

English documentation lives in [docs/en/](docs/en/); the French originals are in [docs/](docs/).

| Document | For whom |
|---|---|
| [Installation](docs/en/installation.md) | Installing, checking the fingerprint, uninstalling, enterprise rollout |
| [Getting started](docs/en/getting-started.md) | Running a first analysis and reading a report |
| [Security and data](docs/en/security-and-data.md) | What leaves the machine, where data is stored, GDPR |
| [Team server](docs/en/team-server.md) | Installing the shared server (Docker, Kubernetes, SSO) |
| [FAQ](docs/en/faq.md) | Windows warning, antivirus, proxy, empty report, slowness |

## Questions, problems

Open an issue in the *Issues* tab of this repository: say what you did, what you expected and what you got.
If the application showed an error, the log helps a lot - its location is in the [FAQ](docs/en/faq.md#where-is-the-log).

## Licence

Free of charge, including in a company, to analyse your own software (or software you are allowed to
analyse). This is not open-source software: redistribution and resale, as such or as a service, need written
agreement. The software comes with no warranty: its results support a decision, they are not a certificate of
compliance.

Full text: [LICENSE](LICENSE), also shown by the installer. Third-party components keep their own licences
([THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md)).

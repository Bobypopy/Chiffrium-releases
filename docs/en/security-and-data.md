# Security and data

*[Version française](../securite-et-donnees.md)*

## What leaves your machine

Nothing, except what you ask for:

| Exchange | When | What goes out |
|---|---|---|
| Git clone | You analyse a remote repository | Your request to the Git server you named |
| OSV | The "vulnerabilities" option is ticked | Dependency names and versions, never any code |
| CISA KEV, EPSS, NVD | Prioritising vulnerabilities | CVE identifiers |
| Updates | At start-up, then every six hours | One request to the publication repository (github.com) |

No telemetry, no online account, no usage collection. Every external source can be switched off in the
settings, and the *About* page lists exactly which ones are active.

The analysed code never leaves the machine. It is cloned into a working folder, read, and the folder is then
deleted.

## What is stored, and how

Reports, CBOMs and artefacts are encrypted at rest with AES-256-GCM. The local database is encrypted. The
master key is held by the system keyring (DPAPI on Windows, libsecret or kwallet on Linux) and, failing that,
in a file with restricted permissions.

Passwords are never stored: only a PBKDF2-HMAC-SHA256 hash with 600,000 iterations and a salt. A session is a
random 256-bit token of which only the hash is kept; it expires after inactivity and after a maximum
lifetime.

Secrets found in the code (keys, tokens) are **redacted** in reports: the tool says it found one and where,
without copying it. It obviously remains in the original repository: it must be revoked.

## Accounts and roles

Three roles: **viewer** (reads their analyses and those of their groups, downloads, proposes catalog
changes), **analyst** (starts, stops and deletes their analyses) and **administrator** (everything, plus
accounts, settings and the audit log).

There is no default account: the first administrator is created with a random token, which is then destroyed.
An account is locked for fifteen minutes after five failed sign-ins, and an IP address is slowed down after
twenty failures in five minutes.

## Audit log

Every sensitive action (sign-in, analysis, settings change, catalog change, account management) is written
into a chained log: each entry carries the hash of the previous one. Changing or deleting an entry breaks the
chain, and the tool notices. The log can be archived automatically to a backed-up folder or to a SIEM
collector, with an HMAC-SHA256 signature.

## Personal data (GDPR)

The organisation deploying the tool is the data controller. The personal data processed is the username, the
display name, the optional email address, the role, the groups, sign-in dates and the IP address in the audit
log - plus the analysed code, which may contain names or addresses.

Legal basis: legitimate interest (information system security) and the security obligation of Article 32.
Default retention: 90 days for analyses (configurable), at least six months for the audit log (CNIL
recommendation), purged only once archived.

Every user can download their data from *My space*. An administrator can correct or disable an account. The
audit log cannot be edited: its security purpose justifies that (Article 17.3 of the GDPR).

Under *Administration > Settings > Legal information*, the organisation fills in its name, the contact of its
data protection officer and the privacy notice shown on the sign-in page.

## Before you analyse

Only analyse repositories you own or are allowed to analyse. Accessing code or a system without permission
may be an offence, and some licences forbid analysis.

## Reporting a flaw

If you think you have found a vulnerability in the tool itself, open an issue **without exploitable detail**,
asking for a contact, rather than publishing the method.

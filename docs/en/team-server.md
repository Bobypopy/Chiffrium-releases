# Team server

*[Version française](../serveur-equipe.md)*

The desktop application is enough for one person. As soon as several people have to share analyses, a common
cryptographic policy and an audit log, you install a server once: everyone then connects with a browser, or
with the desktop application in "With my team" mode.

```text
 machine A (desktop app) --+
 machine B (desktop app) --+-- https --> TLS proxy --> interface --> engine --> PostgreSQL
 machine C (browser)     --+                                            |
                                                     encrypted volume (reports, CBOM)
```

## What the server brings

- **Accounts and roles**: viewer, analyst, administrator - or sign-in through the company SSO (OIDC: Entra
  ID, Keycloak, Okta...), with the role derived from directory groups.
- **Groups**: an analysis attached to a group is visible to its members, and to them only. The Groups page
  gives the team summary (average score, disallowed assets left, most exposed repositories).
- **Common policy**: the algorithm catalog of the server applies to every analysis. A member can propose a
  change, an administrator approves or rejects it with a reason. The policy is published (name + version
  number + fingerprint) and imported on other installations.
- **Audit log**: every action is recorded in a tamper-evident hash chain, which can be archived to a folder
  or to a signed SIEM collector.
- **Notifications**: the bell in the interface, a team channel (Slack, Mattermost, Teams, Discord) and email.

## Installing with Docker Compose

The publication repository holds the installers only; the deployment files ship with the product. The
procedure is a few commands:

```bash
# Secrets belong in a vault, never in a repository
export MASTER_KEY="$(openssl rand -base64 32)"
export POSTGRES_PASSWORD="$(openssl rand -base64 24)"
export DATABASE_URL="jdbc:postgresql://postgres:5432/cryptoanalyzer"

docker compose --profile team up -d

# First-installation token, to create the first administrator
docker compose logs backend | grep "Première installation"
```

Put a TLS proxy in front of it (Caddy, nginx, Traefik): the server expects to be reached over https. The
client machines then have a single address to know.

Kubernetes: Kustomize manifests are provided, with a `Deployment` for the engine, a PostgreSQL `StatefulSet`
and S3-compatible object storage for the reports when several instances run.

## Moving from a machine to the server

A local installation can move to the server without retyping anything: the `migrate-db` command copies the
database (accounts, groups, analyses, catalog, audit log) into PostgreSQL.

```bash
java -jar cryptoanalyzer-backend.jar migrate-db \
  --to jdbc:postgresql://server:5432/cryptoanalyzer --user cryptoanalyzer --password '...'
```

The source database is not modified; the target must be empty. Remember to copy the data folder (reports,
artefacts) and the master key as well: without it, the encrypted settings can no longer be read.

## Sizing

Two cores and 4 GB of memory are enough for a team running a few analyses a day. The number of simultaneous
analyses is configurable; the others wait in a shared queue. A large repository mostly eats disk while it is
being cloned: leave room in the working volume.

## Backing up

Three things to back up together:

1. the PostgreSQL database (`pg_dump`);
2. the `/data` volume (reports, CBOMs, encrypted artefacts);
3. the master key (`MASTER_KEY`), without which the first two cannot be read.

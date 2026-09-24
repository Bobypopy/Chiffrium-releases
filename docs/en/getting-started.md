# Getting started

*[Version française](../premiers-pas.md)*

## The first launch

The application first asks how you want to work.

**On this machine**: everything runs on your computer. The analysis engine listens on `127.0.0.1` only, so it
cannot be reached from the local network or from the internet. Your data is encrypted in your user profile.

**With my team**: you give the address of a [shared server](team-server.md); the application then shows its
accounts, its groups and its analyses, and nothing runs on your machine.

You can come back to that choice with *File > Change mode*, without losing anything.

There is no default account: you create the first administrator, protected by a random token that the
application hands you in local mode. The password is at least twelve characters long; length matters more
than odd characters.

The interface language (French or English) is chosen in the top bar, and remembered.

## Running an analysis

*New analysis*, then a target:

- the https address of a Git repository (`https://github.com/organisation/project.git`) - with an access
  token if the repository is private, and the option to follow submodules;
- or a folder on your disk, picked from the file explorer.

Two options deserve a word:

- **Dependency inventory**: reads the dependency files of the project (`pom.xml`, `build.gradle`,
  `package-lock.json`, `requirements.txt`, `go.mod`, `packages.lock.json`...) to list the libraries. No
  build tool is ever run.
- **Known vulnerabilities**: sends the names and versions of those libraries to the public OSV database. Not
  a line of code leaves the machine. Untick it if the machine is isolated or if internal policy forbids it.

The screen then follows every step (fetch, inventory, detection, evaluation). Expect one to five minutes for
an ordinary project; the application shows the usual duration observed on your previous analyses. You can
stop a running analysis.

## Reading a report

**Dashboard.** A score out of 100 - the weighted average of the statuses of the algorithms found - and above
all *What weighs most*: the families that cost the most points, with the number of points lost. That is where
to start. Each line leads straight to the assets concerned.

**Assets.** Every algorithm, key or certificate found, with its status, where it appears in the code and the
reason for the verdict. The filters (status, primitive, language, origin) are kept from one visit to the
next. The origin tells your own code apart from a dependency copied into the repository or a Git submodule:
the fix is not the same.

**Security findings.** Hardcoded secrets, disabled TLS validation, predictable randomness, fixed IV, a
password derived with too few iterations... each with its severity, its location and the expected fix.
Secrets are redacted in the report. A false positive can be ignored by adding the comment
`cryptoanalyzer:ignore` on the line concerned.

**Graph.** Which functions use which algorithms. Drive it with the mouse, or with the keyboard: Tab to enter,
left and right arrows to walk the nodes, up and down to follow the links, Enter to open an asset. The node
you reach is described below the graph. Searching for a node highlights it inside the whole graph instead of
hiding everything else.

**Dependencies.** The libraries, their licences and their known vulnerabilities, most urgent first
(exploited according to the CISA KEV catalog, then EPSS probability of exploitation).

## Taking something away

| Button | What you get | For whom |
|---|---|---|
| CBOM (CycloneDX 1.6) | The inventory in the standard format | A component management tool, an audit |
| SARIF | The findings located in the files | A code review, GitHub, GitLab |
| Full report (JSON) | Everything, including what the interface summarises | Automated processing |
| Download everything (ZIP) | The whole set, encrypted server-side | Archiving |
| Print / PDF | A laid-out sheet: score, priorities, findings | A security review, a committee |

## Comparing two analyses

From a report, *Compare with...* lists the other analyses of the same repository. The comparison shows what
appeared, what disappeared and what changed status: that is the view to use to show progress.

## Working as a team

An analysis can be attached to a group, and its members then see it. The *Groups* page shows a team summary -
average score, disallowed assets left, most exposed repositories, and who is improving or falling back since
the previous analysis.

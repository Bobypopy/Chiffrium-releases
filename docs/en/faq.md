# Frequently asked questions

*[Version française](../faq.md)*

## Windows says "Windows protected your PC". Is it dangerous?

That is SmartScreen. It shows this screen for any program that is not signed with a commercial certificate
and has not yet been downloaded thousands of times - which is the case of a recent, free tool. It is not an
antivirus detection: Windows is saying it does not know the publisher, not that it found something. Click
*More info*, then *Run anyway*.

If you want to check the file before opening it, compare its fingerprint with the published one
([Installation](installation.md#check-what-you-downloaded)), or run it through VirusTotal.

## My antivirus quarantines the installation

Some antivirus products distrust unsigned executables and archives holding thousands of files. If that
happens, allow the file after checking its fingerprint, or install the Linux version in a virtual machine.
Do tell us: a false positive is also worth reporting to the antivirus vendor.

## The installation takes ten minutes, is that normal?

Yes, on Windows. The application carries its Java runtime and the dependency inventory tool: nearly 900 MB
and a great many small files, each inspected by the antivirus as it is written. Starting it, on the other
hand, is fast.

## The report lists no dependency

Three possible causes:

1. the *Dependency inventory* option was unticked when the analysis was started;
2. the project does not declare its dependencies in a file the tool can read (they are computed by a build
   script, for instance);
3. the inventory tool cannot be found - the *New analysis* screen says so clearly. On the desktop
   application, reinstalling fixes it; on a server, check the `CDXGEN_COMMAND` variable.

In every case the detection of algorithms in the code still works: that is the rule engine, which is always
present.

## An analysis fails with "repository unreachable"

Check the URL (it must be `https`), network access, and the token if the repository is private. Behind a
corporate proxy, the Java virtual machine reads the `https.proxyHost`, `https.proxyPort` and
`http.nonProxyHosts` properties: on a server, add them to the launch command
(`java -Dhttps.proxyHost=... -jar ...`).

On a server, the allowed Git hosts are limited by configuration (`ALLOWED_GIT_HOSTS`): a repository hosted
elsewhere will be refused.

## The tool flags an algorithm we use on purpose

Two options. If the finding is a false positive (a test vector, say), add the comment
`chiffrium:ignore` on the line concerned. If it is a deliberate choice for your organisation, change the
policy: in the catalog, an administrator changes the status of the algorithm and every later analysis follows
suit. A non-administrator can propose the change.

## Can I analyse a local folder rather than a repository?

Yes on the desktop application: your user folders can be analysed. On a server, only the explicitly allowed
folders can (`ALLOWED_LOCAL_ROOTS`), so that a user cannot make the server read `/etc`.

## The analysis is slow on a large repository

Cloning usually dominates. You can untick the dependency inventory and the vulnerability lookup for a quick
first pass, then run a full analysis. The screen shows the usual duration of your previous analyses, and
warns when the current one runs longer than usual.

## Where is the log?

| System | File |
|---|---|
| Windows | `%APPDATA%\Chiffrium\desktop.log` |
| Linux | `~/.config/Chiffrium/desktop.log` |

It is rotated to `desktop.old.log` beyond 5 MB. Attach it to an issue: it holds the errors of the engine, not
your code.

## Can I use the tool in a company?

Yes, free of charge, to analyse the software of your organisation or software you are allowed to analyse.
What the licence forbids without written agreement is redistributing or reselling it, including as a service.

## Are the results exhaustive?

No, and no static analysis tool is. The tool sees what is written in the code and in the configuration files
of the repository; it does not see what is decided at runtime. A report is a starting point for review, not a
certificate of compliance.

## How do I uninstall completely?

Uninstall the application, then delete the data folder
([Installation](installation.md#where-the-data-lives)). Uninstalling alone keeps it on purpose, so that you
can reinstall without losing anything.

## Can I use the interface in English?

Yes: the language selector sits in the top bar, next to the theme button. French and English are available,
and the choice is remembered on the machine. The technical log of an analysis stays in the language written
by the engine.

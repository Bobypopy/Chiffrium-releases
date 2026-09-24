# Composants et données tiers

CryptoAnalyzer intègre les composants suivants, chacun sous sa propre licence. La liste affichée dans
l'application (page À propos) provient de `backend/src/main/resources/legal/third-party.json`.

## Composants logiciels

| Composant | Licence | Rôle |
|---|---|---|
| Javalin, Eclipse Jetty | Apache-2.0 ; EPL-2.0 ou Apache-2.0 | serveur HTTP |
| Jackson | Apache-2.0 | JSON |
| Eclipse JGit | BSD-3-Clause (EDL-1.0) | clonage Git |
| H2 Database | MPL-2.0 ou EPL-1.0 | base locale chiffrée |
| PostgreSQL JDBC, HikariCP | BSD-2-Clause, Apache-2.0 | base du serveur d'équipe |
| Nimbus JOSE + JWT | Apache-2.0 | SSO OpenID Connect |
| SLF4J, Logback | MIT ; EPL-1.0 ou LGPL-2.1 | journalisation |
| OpenJDK (JRE embarqué, jlink) | GPL-2.0 avec Classpath Exception | exécution Java (l'exception autorise la distribution avec un logiciel propriétaire) |
| CycloneDX cdxgen | Apache-2.0 | SBOM et analyse de flux |
| AppThreat atom | MIT | analyse de flux Java et Python utilisée par cdxgen |
| vis-network | Apache-2.0 ou MIT | graphe |
| Electron (Chromium, Node.js), electron-updater | MIT (Chromium : BSD-3-Clause et autres) | application de bureau |

Les notices de licence de ces composants sont conservées dans les paquets distribués
(jar, `node_modules`, JRE). Les composants sous MPL/EPL/LGPL ne sont pas modifiés (aucune obligation de
publication du code de CryptoAnalyzer), et aucun composant sous GPL/AGPL n'est lié au code de l'application.

## Données

| Source | Conditions |
|---|---|
| Publications NIST (SP 800-131A, IR 8547, FIPS 203/204/205) | domaine public (gouvernement américain) |
| OSV | licences propres à chaque base source (majoritairement CC-BY-4.0) |
| CISA KEV | domaine public (CC0) |
| FIRST EPSS | utilisation libre avec attribution à FIRST.org |
| NVD | *This product uses the NVD API but is not endorsed or certified by the NVD.* |

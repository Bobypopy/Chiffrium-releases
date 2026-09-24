# CryptoAnalyzer - inventaire cryptographique et migration post-quantique

Un ordinateur quantique suffisamment grand cassera RSA et les courbes elliptiques. Avant de migrer, il faut
savoir ce que l'on utilise : où, dans quel fichier, avec quelle taille de clé. C'est la première étape que
demandent le NIST, l'ANSSI et le BSI, et c'est précisément ce que fait cet outil.

CryptoAnalyzer lit le code d'un projet - un dépôt Git ou un dossier de votre disque - et produit l'inventaire
de sa cryptographie : algorithmes, clés, certificats, protocoles. Chaque élément est confronté à une
politique (recommandé, acceptable, déprécié, interdit) et situé par rapport au post-quantique. L'outil relève
aussi les erreurs classiques : secret écrit en dur, validation TLS désactivée, aléa prévisible, dérivation de
clé trop faible.

Tout tourne sur votre machine. **Votre code n'est envoyé nulle part.**

## Télécharger

**-> [Dernière version](https://github.com/Bobypopy/CryptoAnalyzer-releases/releases/latest)**

| Système | Fichier | Quoi en faire |
|---|---|---|
| Windows 10/11 (64 bits) | `CryptoAnalyzer-Setup-<version>.exe` | Double-cliquer, puis suivre l'installateur |
| Linux (Debian, Ubuntu...) | `cryptoanalyzer-desktop_<version>_amd64.deb` | `sudo apt install ./cryptoanalyzer-desktop_<version>_amd64.deb` |
| Linux (toute distribution) | `CryptoAnalyzer-<version>.AppImage` | `chmod +x` puis lancer, rien à installer |

Comptez environ 270 Mo à télécharger et 900 Mo installés : l'application contient tout ce dont elle a besoin
(moteur d'analyse, environnement Java, inventaire des dépendances). Rien d'autre n'est à installer, et elle
fonctionne sans accès à Internet - sauf pour cloner un dépôt distant ou vérifier les vulnérabilités connues.

L'installation Windows prend plusieurs minutes : elle décompresse un grand nombre de fichiers. C'est normal.

> **Windows affiche "Windows a protégé votre ordinateur"**
> L'installateur n'est pas signé par un certificat commercial (plusieurs centaines d'euros par an pour un
> projet gratuit). Cliquez sur *Informations complémentaires*, puis *Exécuter quand même*. Vous pouvez
> vérifier l'empreinte du fichier avant de l'ouvrir : voir
> [docs/installation.md](docs/installation.md#vérifier-ce-que-vous-avez-téléchargé).

## En trois minutes

1. Lancez l'application et choisissez **Sur ce poste**.
2. Créez votre compte administrateur - l'application vous donne d'elle-même le jeton demandé.
3. **Nouvelle analyse** : collez l'URL d'un dépôt Git public ou choisissez un dossier de votre disque.
4. Attendez. Une à cinq minutes pour un projet ordinaire ; l'écran suit chaque étape.
5. Lisez le rapport : le score et *ce qui pèse le plus*, puis les actifs, les constats de sécurité, le graphe.

Vous pouvez en ressortir un CBOM CycloneDX 1.6, un fichier SARIF pour une revue de code, une archive complète,
ou une feuille imprimable (PDF) pour un comité.

## Ce que l'outil sait faire, et ce qu'il ne sait pas faire

Il **trouve** les usages écrits dans le code et dans les fichiers de configuration, pour une quinzaine de
langages (Java, Kotlin, Python, JavaScript/TypeScript, Go, C#, C/C++, PHP, Ruby, Rust, Swift...), les clés et
certificats présents dans le dépôt, les dépendances déclarées et leurs vulnérabilités connues, et il dit où en
est la migration post-quantique.

Il **ne trouve pas** ce qui n'existe qu'à l'exécution : un algorithme choisi dans un fichier de configuration
absent du dépôt, une bibliothèque chargée dynamiquement, une dépendance calculée par un script de build. Il ne
lance ni Maven, ni Gradle, ni npm, ni aucun script du projet analysé - c'est un choix de sécurité : analyser un
dépôt inconnu ne doit pas exécuter son code.

Un rapport se lit donc comme un point de départ de revue, pas comme un verdict.

## Vos données

Le code analysé, les rapports et les clés restent sur votre machine, chiffrés dans votre profil utilisateur.
Deux échanges seulement sortent de la machine, tous deux désactivables :

- le clonage du dépôt Git que vous demandez, vers le serveur que vous indiquez ;
- les **noms et versions** des dépendances, envoyés à la base publique OSV pour savoir si elles sont connues
  comme vulnérables. Jamais une ligne de votre code.

Aucune télémétrie, aucun compte à créer chez qui que ce soit. Détails :
[docs/securite-et-donnees.md](docs/securite-et-donnees.md).

## En équipe

Un serveur partagé permet à plusieurs personnes de lancer des analyses, de les regrouper par équipe et
d'appliquer une politique cryptographique commune, depuis un navigateur ou depuis l'application de bureau.
Il s'installe avec Docker ou Kubernetes : [docs/serveur-equipe.md](docs/serveur-equipe.md).

## Mises à jour

L'application vérifie les nouvelles versions au démarrage puis toutes les six heures, et les télécharge en
arrière-plan. Un bouton *Mettre à jour* apparaît quand elle est prête. Vos analyses et vos réglages sont
conservés.

## Documentation

| Document | Pour qui |
|---|---|
| [Installation](docs/installation.md) | Installer, vérifier l'empreinte, désinstaller, déployer en entreprise |
| [Premiers pas](docs/premiers-pas.md) | Lancer sa première analyse et lire un rapport |
| [Sécurité et données](docs/securite-et-donnees.md) | Ce qui sort de la machine, où sont stockées les données, RGPD |
| [Serveur d'équipe](docs/serveur-equipe.md) | Installer le serveur partagé (Docker, Kubernetes, SSO) |
| [Questions fréquentes](docs/faq.md) | Avertissement Windows, antivirus, proxy, rapport vide, lenteurs |

## Questions, anomalies

Ouvrez un ticket dans l'onglet *Issues* de ce dépôt : décrivez ce que vous avez fait, ce que vous attendiez et
ce que vous avez obtenu. Si l'application a affiché une erreur, le journal aide beaucoup - son emplacement est
indiqué dans la [FAQ](docs/faq.md#où-est-le-journal).

## Licence

Gratuit, y compris en entreprise, pour analyser vos propres logiciels (ou ceux que vous êtes autorisé à
analyser). Ce n'est pas un logiciel libre : la redistribution et la revente, telles quelles ou sous forme de
service, demandent un accord écrit. Le logiciel est fourni sans garantie : ses résultats aident à décider, ils
ne valent pas certificat de conformité.

Texte complet : [LICENSE](LICENSE), également affiché par l'installateur. Les composants tiers gardent leurs
propres licences ([THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md)).

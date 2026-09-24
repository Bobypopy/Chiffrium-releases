# Questions fréquentes

*[English version](en/faq.md)*

## Windows dit "Windows a protégé votre ordinateur". C'est dangereux ?

C'est SmartScreen. Il affiche cet écran pour tout programme qui n'est pas signé par un certificat commercial
et qui n'a pas encore été téléchargé des milliers de fois - ce qui est le cas d'un outil récent et gratuit. Ce
n'est pas une détection de virus : Windows dit qu'il ne connaît pas l'éditeur, pas qu'il a trouvé quelque
chose. Cliquez sur *Informations complémentaires*, puis *Exécuter quand même*.

Si vous voulez vérifier le fichier avant de l'ouvrir, comparez son empreinte avec celle publiée
([Installation](installation.md#vérifier-ce-que-vous-avez-téléchargé)), ou passez-le sur VirusTotal.

## Mon antivirus met l'installation en quarantaine

Certains antivirus se méfient des exécutables non signés et des archives contenant des milliers de fichiers.
Si c'est le cas, autorisez le fichier après avoir vérifié son empreinte, ou installez la version Linux dans
une machine virtuelle. Signalez-nous le cas : un faux positif se remonte aussi à l'éditeur de l'antivirus.

## L'installation dure dix minutes, c'est normal ?

Oui, sur Windows. L'application embarque son moteur Java et l'outil d'inventaire des dépendances : près de
900 Mo et un grand nombre de petits fichiers, chacun inspecté par l'antivirus au moment de l'écriture. Le
lancement, lui, est rapide.

## Le rapport ne liste aucune dépendance

Trois causes possibles :

1. l'option *Inventaire des dépendances* était décochée au lancement de l'analyse ;
2. le projet ne déclare pas ses dépendances dans un fichier que l'outil sait lire (elles sont calculées par un
   script de build, par exemple) ;
3. l'outil d'inventaire est introuvable - l'écran *Nouvelle analyse* l'annonce alors clairement. Sur
   l'application de bureau, une réinstallation le rétablit ; sur un serveur, vérifiez la variable
   `CDXGEN_COMMAND`.

Dans tous les cas, la détection des algorithmes dans le code, elle, fonctionne : c'est le moteur de règles,
qui est toujours présent.

## Une analyse échoue avec "dépôt injoignable"

Vérifiez l'URL (elle doit être en `https`), l'accès réseau, et le jeton si le dépôt est privé. Derrière un
proxy d'entreprise, la machine virtuelle Java lit les propriétés `https.proxyHost`, `https.proxyPort` et
`http.nonProxyHosts` : sur un serveur, ajoutez-les à la commande de lancement
(`java -Dhttps.proxyHost=... -jar ...`).

Sur un serveur, les hôtes Git autorisés sont limités par configuration (`ALLOWED_GIT_HOSTS`) : un dépôt hébergé
ailleurs sera refusé.

## L'outil signale un algorithme que nous utilisons volontairement

Deux possibilités. Si le constat est un faux positif (un vecteur de test, par exemple), ajoutez le commentaire
`cryptoanalyzer:ignore` sur la ligne concernée. Si c'est un choix assumé pour votre organisation, changez la
politique : dans le catalogue, un administrateur modifie le statut de l'algorithme, et toutes les analyses
suivantes en tiennent compte. Un utilisateur non administrateur peut le proposer.

## Peut-on analyser un dossier local plutôt qu'un dépôt ?

Oui sur l'application de bureau : vos dossiers utilisateur sont analysables. Sur un serveur, seuls les
dossiers explicitement autorisés le sont (`ALLOWED_LOCAL_ROOTS`), pour éviter qu'un utilisateur ne fasse lire
`/etc` au serveur.

## L'analyse est lente sur un gros dépôt

Le clonage domine souvent. Vous pouvez décocher l'inventaire des dépendances et la recherche de vulnérabilités
pour un premier passage rapide, puis relancer une analyse complète. L'écran indique la durée habituelle de vos
analyses précédentes, et prévient quand celle en cours dépasse l'ordinaire.

## Où est le journal ?

| Système | Fichier |
|---|---|
| Windows | `%APPDATA%\CryptoAnalyzer\desktop.log` |
| Linux | `~/.config/CryptoAnalyzer/desktop.log` |

Il est archivé en `desktop.old.log` au-delà de 5 Mo. Joignez-le à un ticket : il contient les erreurs du
moteur, pas votre code.

## Puis-je utiliser l'outil en entreprise ?

Oui, gratuitement, pour analyser les logiciels de votre organisation ou ceux que vous êtes autorisé à
analyser. Ce que la licence interdit sans accord écrit, c'est de le redistribuer ou de le revendre, y compris
sous forme de service.

## Les résultats sont-ils exhaustifs ?

Non, et aucun outil d'analyse statique ne l'est. L'outil voit ce qui est écrit dans le code et les fichiers de
configuration du dépôt ; il ne voit pas ce qui se décide à l'exécution. Un rapport est un point de départ de
revue, pas un certificat de conformité.

## Comment désinstaller complètement ?

Désinstallez l'application, puis supprimez le dossier de données
([Installation](installation.md#où-sont-les-données)). La désinstallation seule les conserve volontairement,
pour pouvoir réinstaller sans rien perdre.

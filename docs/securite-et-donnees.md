# Sécurité et données

## Ce qui sort de votre machine

Rien, sauf ce que vous demandez explicitement :

| Échange | Quand | Ce qui part |
|---|---|---|
| Clonage Git | Vous analysez un dépôt distant | Votre requête vers le serveur Git que vous avez indiqué |
| OSV | Option "vulnérabilités" cochée | Noms et versions des dépendances, jamais de code |
| CISA KEV, EPSS, NVD | Priorisation des vulnérabilités | Numéros de CVE |
| Mises à jour | Démarrage, puis toutes les six heures | Une requête au dépôt de publication (github.com) |

Pas de télémétrie, pas de compte en ligne, pas de collecte d'usage. Chaque source externe est désactivable
dans les paramètres, et la page *À propos* affiche la liste exacte de celles qui sont actives.

Le code analysé ne quitte jamais la machine. Il est cloné dans un dossier de travail, lu, puis le dossier est
supprimé.

## Ce qui est stocké, et comment

Les rapports, les CBOM et les artefacts sont chiffrés au repos en AES-256-GCM. La base locale est chiffrée.
La clé maîtresse est gardée par le trousseau du système (DPAPI sous Windows, libsecret ou kwallet sous Linux)
et, à défaut, dans un fichier aux droits restreints.

Les mots de passe ne sont jamais stockés : seule une empreinte PBKDF2-HMAC-SHA256 à 600 000 itérations avec
sel l'est. Une session est un jeton aléatoire de 256 bits dont seule l'empreinte est conservée ; elle expire
après inactivité et au bout d'une durée maximale.

Les secrets trouvés dans le code (clés, jetons) sont **masqués** dans les rapports : l'outil dit qu'il en a
trouvé un et où, sans le recopier. Il reste évidemment présent dans le dépôt d'origine : il faut le révoquer.

## Comptes et rôles

Trois rôles : **lecteur** (consulte ses analyses et celles de ses groupes, télécharge, propose des
modifications du catalogue), **analyste** (lance, arrête et supprime ses analyses) et **administrateur**
(tout, plus les comptes, les paramètres et le journal d'audit).

Il n'existe aucun compte par défaut : le premier administrateur se crée avec un jeton aléatoire, détruit
ensuite. Un compte est bloqué quinze minutes après cinq échecs de connexion, et une adresse IP est freinée
après vingt échecs en cinq minutes.

## Journal d'audit

Chaque action sensible (connexion, analyse, changement de paramètre, modification du catalogue, gestion des
comptes) est écrite dans un journal chaîné : chaque entrée contient l'empreinte de la précédente. Modifier ou
supprimer une entrée casse la chaîne, et l'outil le détecte. Le journal peut être archivé automatiquement vers
un dossier sauvegardé ou vers un collecteur SIEM, avec une signature HMAC-SHA256.

## Données personnelles (RGPD)

L'organisation qui déploie l'outil est responsable du traitement. Les données personnelles traitées sont
l'identifiant, le nom affiché, l'e-mail facultatif, le rôle, les groupes, les dates de connexion et l'adresse
IP dans le journal d'audit - auxquels s'ajoute le code analysé, qui peut contenir des noms ou des adresses.

Base légale : intérêt légitime (sécurité des systèmes d'information) et obligation de sécurité de l'article 32.
Durées par défaut : 90 jours pour les analyses (réglable), six mois minimum pour le journal d'audit
(recommandation CNIL), purgé seulement après archivage.

Chaque utilisateur peut télécharger ses données depuis *Mon espace*. Un administrateur peut corriger ou
désactiver un compte. Le journal d'audit n'est pas modifiable : sa finalité de sécurité le justifie
(article 17.3 du RGPD).

Dans *Administration > Paramètres > Informations légales*, l'organisation renseigne son nom, le contact de son
délégué à la protection des données et la mention d'information affichée à la connexion.

## Avant d'analyser

N'analysez que des dépôts que vous possédez ou que vous êtes autorisé à analyser. Accéder sans autorisation à
du code ou à un système peut constituer une infraction, et certaines licences interdisent l'analyse.

## Signaler une faille

Si vous pensez avoir trouvé une vulnérabilité dans l'outil lui-même, ouvrez un ticket **sans détail
exploitable** en demandant un contact, plutôt qu'en publiant la méthode.

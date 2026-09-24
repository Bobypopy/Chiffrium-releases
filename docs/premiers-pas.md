# Premiers pas

*[English version](en/getting-started.md)*

## Le premier lancement

L'application demande d'abord comment vous voulez travailler.

**Sur ce poste** : tout tourne chez vous. Le moteur d'analyse écoute uniquement sur `127.0.0.1`, il n'est
joignable ni depuis le réseau local ni depuis Internet. Vos données sont chiffrées dans votre profil
utilisateur.

**Avec mon équipe** : vous indiquez l'adresse d'un [serveur partagé](serveur-equipe.md) ; l'application
affiche alors ses comptes, ses groupes et ses analyses, et rien ne tourne sur votre poste.

On revient à ce choix par *Fichier > Changer de mode*, sans rien perdre.

Il n'existe aucun compte par défaut : vous créez le premier administrateur, protégé par un jeton aléatoire que
l'application vous donne d'elle-même en mode local. Le mot de passe fait au moins douze caractères ; la
longueur compte davantage que les caractères bizarres.

La langue de l'interface (français ou anglais) se choisit dans la barre du haut, et elle est retenue.

## Lancer une analyse

*Nouvelle analyse*, puis une cible :

- l'URL https d'un dépôt Git (`https://github.com/organisation/projet.git`) - avec un jeton d'accès si le
  dépôt est privé, et l'option pour suivre les sous-modules ;
- ou un dossier de votre disque, choisi dans l'explorateur.

Deux options méritent un mot :

- **Inventaire des dépendances** : lit les fichiers de dépendances du projet (`pom.xml`, `build.gradle`,
  `package-lock.json`, `requirements.txt`, `go.mod`, `packages.lock.json`...) pour dresser la liste des
  bibliothèques. Aucun outil de build n'est lancé.
- **Vulnérabilités connues** : envoie les noms et versions de ces bibliothèques à la base publique OSV. Aucune
  ligne de code ne sort. Décochez si le poste est isolé ou si la politique interne l'interdit.

L'écran suit ensuite chaque étape (récupération, inventaire, détection, évaluation). Comptez une à cinq
minutes pour un projet ordinaire ; l'application affiche la durée habituelle observée sur vos analyses
précédentes. Vous pouvez arrêter une analyse en cours.

## Lire un rapport

**Tableau de bord.** Un score sur 100 - moyenne pondérée des statuts des algorithmes trouvés - et surtout
*Ce qui pèse le plus* : les familles qui coûtent le plus de points, avec le nombre de points perdus. C'est par
là qu'on commence. Chaque ligne mène directement aux actifs concernés.

**Actifs.** Chaque algorithme, clé ou certificat trouvé, avec son statut, ses emplacements dans le code et le
motif du verdict. Les filtres (statut, primitive, langage, origine) sont conservés d'une visite à l'autre.
L'origine distingue votre propre code d'une dépendance recopiée dans le dépôt ou d'un sous-module Git : la
correction n'est pas la même.

**Constats de sécurité.** Secrets écrits en dur, validation TLS désactivée, aléa prévisible, IV fixe, mot de
passe dérivé avec trop peu d'itérations... chacun avec sa gravité, son emplacement et la correction attendue.
Les secrets sont masqués dans le rapport. Un faux positif s'ignore en ajoutant le commentaire
`chiffrium:ignore` sur la ligne concernée.

**Graphe.** Quelles fonctions utilisent quels algorithmes. Il se manipule à la souris, ou au clavier : Tab
pour y entrer, flèches gauche et droite pour parcourir les nœuds, haut et bas pour suivre les liens, Entrée
pour ouvrir un actif. Le nœud atteint est décrit sous le graphe.

**Dépendances.** Les bibliothèques, leurs licences et leurs vulnérabilités connues, les plus urgentes en
premier (exploitées d'après le catalogue CISA KEV, puis probabilité d'exploitation EPSS).

## En ressortir quelque chose

| Bouton | Ce que vous obtenez | Pour qui |
|---|---|---|
| CBOM (CycloneDX 1.6) | L'inventaire au format standard | Un outil de gestion des composants, un audit |
| SARIF | Les constats localisés dans les fichiers | Une revue de code, GitHub, GitLab |
| Rapport complet (JSON) | Tout, y compris ce que l'interface résume | Un traitement automatisé |
| Tout télécharger (ZIP) | L'ensemble, chiffré côté serveur | Archivage |
| Imprimer / PDF | Une feuille mise en page : score, priorités, constats | Une revue de sécurité, un comité |

## Comparer deux analyses

Depuis un rapport, *Comparer avec...* liste les autres analyses du même dépôt. La comparaison montre ce qui est
apparu, ce qui a disparu et ce qui a changé de statut : c'est la vue qui sert à démontrer une progression.

## Travailler à plusieurs

Une analyse peut être rattachée à un groupe : ses membres la voient. La page *Groupes* affiche alors une
synthèse d'équipe - score moyen, actifs interdits restants, dépôts les plus exposés, et qui progresse ou
recule depuis l'analyse précédente.

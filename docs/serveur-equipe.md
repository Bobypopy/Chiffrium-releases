# Serveur d'équipe

*[English version](en/team-server.md)*

L'application de bureau suffit pour une personne. Dès que plusieurs personnes doivent partager des analyses,
une politique cryptographique commune et un journal d'audit, on installe une fois un serveur : chacun s'y
connecte ensuite avec son navigateur, ou avec l'application de bureau en mode "Avec mon équipe".

```text
 poste A (appli bureau) --+
 poste B (appli bureau) --+-- https --> proxy TLS --> interface --> moteur --> PostgreSQL
 poste C (navigateur)   --+                                                |
                                                        volume chiffré (rapports, CBOM)
```

## Ce que le serveur apporte

- **Comptes et rôles** : lecteur, analyste, administrateur - ou connexion par le SSO de l'entreprise (OIDC :
  Entra ID, Keycloak, Okta...), avec le rôle déduit des groupes de l'annuaire.
- **Groupes** : une analyse rattachée à un groupe est visible de ses membres, et d'eux seuls. La page Groupes
  donne la synthèse de l'équipe (score moyen, actifs interdits restants, dépôts les plus exposés).
- **Politique commune** : le catalogue d'algorithmes du serveur s'applique à toutes les analyses. Un membre
  peut proposer un changement, un administrateur l'accepte ou le refuse avec un motif. La politique se publie
  (nom + numéro de version + empreinte) et s'importe sur d'autres installations.
- **Journal d'audit** : chaque action est tracée dans une chaîne d'empreintes infalsifiable, archivable vers un
  dossier ou un collecteur SIEM signé.
- **Notifications** : cloche dans l'interface, canal d'équipe (Slack, Mattermost, Teams, Discord) et courriel.

## Installer avec Docker Compose

Le dépôt de publication ne contient que les installateurs ; les fichiers de déploiement sont fournis avec le
produit. La procédure tient en quelques commandes :

```bash
# Secrets à garder dans un coffre, jamais dans un dépôt
export MASTER_KEY="$(openssl rand -base64 32)"
export POSTGRES_PASSWORD="$(openssl rand -base64 24)"
export DATABASE_URL="jdbc:postgresql://postgres:5432/chiffrium"

docker compose --profile team up -d

# Jeton de première installation, pour créer le premier administrateur
docker compose logs backend | grep "Première installation"
```

Mettez un proxy TLS devant (Caddy, nginx, Traefik) : le serveur attend d'être joint en https. Les postes
clients n'ont alors qu'une adresse à connaître.

Kubernetes : des manifestes Kustomize sont fournis, avec un `Deployment` pour le moteur, un `StatefulSet`
PostgreSQL et un stockage objet compatible S3 pour les rapports quand plusieurs instances tournent.

## Passer d'un poste au serveur

Une installation locale peut déménager vers le serveur sans ressaisie : la commande `migrate-db` recopie la
base (comptes, groupes, analyses, catalogue, journal d'audit) vers PostgreSQL.

```bash
java -jar chiffrium-backend.jar migrate-db \
  --to jdbc:postgresql://serveur:5432/chiffrium --user chiffrium --password '...'
```

La base d'origine n'est pas modifiée ; la cible doit être vide. Pensez à recopier aussi le dossier de données
(rapports, artefacts) et la clé maîtresse : sans elle, les réglages chiffrés ne sont plus lisibles.

## Dimensionner

Deux cœurs et 4 Go de mémoire suffisent pour une équipe qui lance quelques analyses par jour. Le nombre
d'analyses simultanées est réglable ; les autres attendent dans une file partagée. Un gros dépôt monopolise
surtout du disque pendant son clonage : prévoyez de la place dans le volume de travail.

## Sauvegarder

Trois choses à sauvegarder ensemble :

1. la base PostgreSQL (`pg_dump`) ;
2. le volume `/data` (rapports, CBOM, artefacts chiffrés) ;
3. la clé maîtresse (`MASTER_KEY`), sans laquelle les deux premières sont illisibles.

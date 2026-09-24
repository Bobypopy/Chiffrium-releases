# Installation

*[English version](en/installation.md)*

## Windows

1. Téléchargez `Chiffrium-Setup-<version>.exe` depuis la
   [dernière version](https://github.com/Bobypopy/Chiffrium-releases/releases/latest).
2. Double-cliquez. Windows affiche un écran bleu "Windows a protégé votre ordinateur" : c'est SmartScreen,
   qui se méfie de tout exécutable peu téléchargé et non signé par un certificat commercial. Cliquez sur
   *Informations complémentaires*, puis *Exécuter quand même*.
3. L'installateur propose une installation pour l'utilisateur courant (aucun droit administrateur) ou pour
   tous les utilisateurs du poste.
4. L'installation dure plusieurs minutes : l'application contient un moteur Java et l'outil d'inventaire des
   dépendances, soit environ 900 Mo et beaucoup de petits fichiers. Un antivirus qui inspecte chaque fichier
   allonge encore l'opération.

Un raccourci est créé dans le menu Démarrer. L'application se lance sans rien d'autre à installer.

### Vérifier ce que vous avez téléchargé

Chaque version publie, à côté de l'installateur, un fichier `latest.yml` qui contient l'empreinte SHA-512 du
`.exe` (encodée en base64) - c'est celle que l'application utilise pour valider ses propres mises à jour.
Pour la comparer :

```powershell
[Convert]::ToBase64String((Get-FileHash .\Chiffrium-Setup-<version>.exe -Algorithm SHA512).Hash `
  -split '(..)' -ne '' -replace '^', '0x' | ForEach-Object { [byte]$_ })
```

Plus simplement, avec Python :

```bash
python -c "import base64,hashlib,sys; print(base64.b64encode(hashlib.sha512(open(sys.argv[1],'rb').read()).digest()).decode())" Chiffrium-Setup-<version>.exe
```

La valeur doit être identique au champ `sha512` de `latest.yml` (`latest-linux.yml` pour les paquets Linux).
Si elle diffère, ne lancez pas le fichier.

### Installation silencieuse (parc d'entreprise)

L'installateur est un NSIS ; il accepte les options habituelles :

```powershell
Chiffrium-Setup-<version>.exe /S /D=C:\Program Files\Chiffrium
```

`/S` installe sans interface, `/D` choisit le dossier (en dernier argument, sans guillemets).

## Linux

### Debian, Ubuntu et dérivées

```bash
sudo apt install ./chiffrium-desktop_<version>_amd64.deb
```

Le paquet installe l'application dans `/opt`, avec son entrée de menu et son icône. Les dépendances
graphiques habituelles (GTK, NSS) sont tirées par `apt` si elles manquent.

### AppImage (toute distribution)

```bash
chmod +x Chiffrium-<version>.AppImage
./Chiffrium-<version>.AppImage
```

Rien n'est installé : le fichier contient l'application entière. Sur un système sans `libfuse2`, lancez-le
avec `--appimage-extract-and-run`.

## Où sont les données

| Système | Dossier |
|---|---|
| Windows | `%APPDATA%\Chiffrium\` |
| Linux | `~/.config/Chiffrium/` |

On y trouve la base chiffrée, les rapports, le journal de l'application (`desktop.log`) et la clé maîtresse
protégée par le trousseau du système (DPAPI sous Windows, libsecret ou kwallet sous Linux).

## Désinstaller

Windows : *Paramètres > Applications > Chiffrium > Désinstaller*.
Debian/Ubuntu : `sudo apt remove chiffrium-desktop`.
AppImage : supprimez le fichier.

**La désinstallation conserve vos données** (analyses, comptes, réglages), pour pouvoir réinstaller sans rien
perdre. Si vous voulez tout effacer, supprimez le dossier indiqué ci-dessus.

## Mettre à jour

L'application vérifie les nouvelles versions au démarrage puis toutes les six heures, et propose un bouton
*Mettre à jour* quand le téléchargement est prêt. Vous pouvez aussi réinstaller par-dessus : les données sont
conservées.

La vérification ne contacte que le dépôt de publication de l'application (github.com), et rien d'autre. Sur
un poste sans accès à Internet, elle échoue en silence : l'application fonctionne normalement.

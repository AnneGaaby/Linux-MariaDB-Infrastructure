# Accès distant au serveur de test avec ZeroTier et DBeaver

Ce document explique comment nous avons relié le PC Windows au serveur CentOS du laboratoire pour tester le serveur privé Talisman Online, puis comment nous avons utilisé DBeaver pour accéder aux bases de données et examiner les comptes.

L'objectif était de créer un accès privé entre nos machines, sans publier le serveur de base de données sur Internet.

## 1. Comprendre le rôle de chaque outil

Les deux outils ont des fonctions différentes et complémentaires :

| Outil | Son rôle dans le laboratoire |
|---|---|
| **ZeroTier** | Créer un réseau privé virtuel entre le PC Windows et la machine CentOS. Il fournit une adresse IP privée permettant aux appareils autorisés de communiquer. |
| **DBeaver** | Se connecter à MariaDB/MySQL avec cette adresse IP et un compte SQL, afficher les bases et les tables, consulter les données et exécuter des requêtes. |

**ZeroTier s'occupe donc de la connexion réseau et de l'adresse IP. DBeaver utilise cette connexion pour accéder à la base de données.** DBeaver ne crée pas l'adresse IP et ZeroTier ne gère pas les comptes du jeu.

## 2. Architecture de test

Dans notre configuration, le serveur Linux fonctionnait dans une machine virtuelle CentOS 7.9. Le poste Windows était utilisé pour administrer le serveur et consulter la base.

~~~mermaid
flowchart LR
    WIN[PC Windows
DBeaver + client de jeu]
    ZT((Réseau privé ZeroTier))
    VM[Machine virtuelle CentOS 7.9]
    DB[(MariaDB / MySQL)]
    GAME[Services Talisman
DB / Login / Game]

    WIN <-->|Connexion privée| ZT
    ZT <-->|Adresse IP ZeroTier| VM
    VM --> DB
    VM --> GAME
    DB <--> GAME
    WIN -->|DBeaver : SQL| DB
~~~ 

Le schéma est une représentation simplifiée. Les services du jeu et MariaDB n'utilisent pas nécessairement tous le même port ni le même protocole.

## 3. Relier Windows et CentOS avec ZeroTier

### Étape 1 — Installer ZeroTier sur les deux machines

Installer le client ZeroTier sur :
- le PC Windows ;
- la machine CentOS qui héberge le serveur.

Les deux appareils doivent rejoindre le **même réseau ZeroTier**. Dans le tableau de bord ZeroTier, le propriétaire du réseau doit autoriser les appareils lorsqu'une validation est nécessaire.

> Le Network ID est une information propre au réseau. Il ne faut pas le confondre avec l'adresse IP privée attribuée à chaque appareil.

### Étape 2 — Vérifier l'adresse IP privée

Une fois les deux appareils connectés et autorisés, ZeroTier attribue une adresse IP privée à chaque appareil sur ce réseau.

Dans notre cas :
- le serveur CentOS possédait son adresse IP ZeroTier ;
- le PC Windows possuía sa propre adresse IP ZeroTier ;
- c'est l'adresse IP ZeroTier de CentOS que nous avons utilisée comme adresse de destination pour joindre le serveur depuis Windows.

Cette adresse est différente de l'adresse IP locale du réseau domestique et de l'adresse publique du routeur. Elle permet aux membres autorisés du réseau virtuel de communiquer même s'ils ne sont pas sur le même réseau physique.

### Étape 3 — Vérifier la connexion

Depuis Windows, nous avons testé la communication avec le serveur CentOS à l'aide de `ping`, en utilisant l'adresse IP ZeroTier du serveur :

~~~powershell
ping <IP-ZEROTIER-DU-SERVEUR>
~~~

Un résultat avec des réponses et sans perte de paquets confirme que les deux machines peuvent communiquer au niveau IP. Lors de notre test, nous avons obtenu 4 réponses sur 4, sans perte.

Sur CentOS, nous avons également vérifié l'état du réseau ZeroTier :

~~~bash
sudo zerotier-cli listnetworks
~~~

Si la commande n'affiche pas le réseau attendu comme connecté, il faut vérifier l'installation, l'autorisation de l'appareil et l'état du service ZeroTier.

**Important :** un ping réussi prouve la connectivité IP, mais ne garantit pas qu'un service particulier accepte les connexions. Le pare-feu, le port et la configuration du service doivent aussi être corrects.

## 4. Connecter DBeaver à MariaDB depuis Windows

Une fois le réseau ZeroTier fonctionnel, nous avons utilisé DBeaver sur Windows pour accéder à MariaDB hébergé sur CentOS.

### Étape 1 — Créer une connexion MariaDB

Dans DBeaver :

1. Ouvrir DBeaver.
2. Choisir **Nouvelle connexion**.
3. Sélectionner le pilote **MariaDB** (ou MySQL si le pilote utilisé correspond à l'installation).
4. Renseigner les paramètres de connexion.

Les paramètres utilisés comme point de départ dans notre laboratoire étaient :

| Champ | Valeur / explication |
|---|---|
| Hôte (Host) | Adresse IP ZeroTier de la machine CentOS |
| Port | `3306` — port MariaDB/MySQL observé dans le laboratoire |
| Base de données | Peut être laissé vide au premier test, selon le pilote et les droits |
| Utilisateur | Compte SQL autorisé sur MariaDB |
| Mot de passe | Mot de passe de ce compte SQL — ne jamais le publier |

Dans notre première configuration DBeaver, nous avons utilisé le compte SQL `root` pour tester la connexion. Pour un environnement durable, il est préférable de créer un compte SQL dédié à l'administration nécessaire, avec uniquement les privilèges requis, plutôt que d'utiliser root à distance.

### Étape 2 — Tester la connexion

Cliquer sur **Tester la connexion**. Si DBeaver propose de télécharger le pilote, accepter son installation.

Si la connexion échoue, vérifier dans cet ordre :
- le PC Windows et CentOS sont bien connectés au même réseau ZeroTier ;
- l'adresse IP saisie est celle de CentOS dans ZeroTier ;
- MariaDB est démarré ;
- MariaDB écoute sur une interface accessible depuis le réseau privé ;
- le port TCP 3306 est autorisé par le pare-feu ;
- le compte SQL possède le droit de se connecter depuis l'adresse source ;
- le nom d'utilisateur et le mot de passe sont corrects.

Une connexion ZeroTier fonctionnelle ne suffit pas si MariaDB écoute uniquement sur `localhost`. Il faut vérifier la configuration réseau de MariaDB avant d'autoriser un accès distant. Ne pas ouvrir le port 3306 à tout Internet.

## 5. Utiliser DBeaver pour examiner les bases et les comptes du jeu

Après la connexion, DBeaver affiche les bases de données auxquelles le compte SQL a accès. Dans notre environnement, les bases importées comprenaient :

- `db_account`
- `db_game`
- `db_gmtool`
- `db_log`

Le nom `db_account indique la base associée aux données de comptes. Les autres bases correspondent à des domaines distincts du serveur. Le contenu précis dépend du schéma et de la version des fichiers SQL installés.

DBeaver permet notamment de :
- parcourir les bases et leurs tables ;
- consulter les colonnes et les types de données ;
- rechercher un compte dans les tables appropriées ;
- exécuter des requêtes SQL de lecture ;
- examiner les résultats et vérifier si les données attendues existent.

### Exemple de consultation SQL

Les requêtes suivantes sont des exemples génériques pour explorer une base. Les noms exacts des tables et colonnes doivent être vérifiés dans le schéma réellement importé :

~~~sql
SHOW DATABASES;

USE db_account;

SHOW TABLES;
~~~

Pour examiner la structure d'une table identifiée dans DBeaver :

~~~sql
DESCRIBE nom_de_la_table;
~~~

On peut ensuite construire une requête de lecture en utilisant les noms de colonnes réellement présents. Il ne faut pas supposer que toutes les versions du serveur utilisent les mêmes noms de tables ou le même format de mot de passe.

### DBeaver et les comptes : une distinction importante

DBeaver est un **client d'administration SQL**, pas le système d'inscription du jeu. Il permet de voir et de modifier les enregistrements stockés dans la base, selon les permissions du compte SQL.

Les comptes du jeu sont stockés dans la base et sont lus par les services Talisman. Pour créer ou modifier un compte, il faut respecter exactement le schéma attendu par le serveur : colonnes obligatoires, format du mot de passe, encodage, identifiants et éventuelles règles supplémentaires.

Il ne faut donc pas inventer une commande `INSERT` ou modifier directement un mot de passe sans avoir identifié le fonctionnement de cette version du serveur. Une modification incorrecte peut empêcher l'authentification ou endommager les données. Pour une première exploration, privilégier les requêtes `SELECT` et les opérations de lecture.

## 6. Vérifications côté serveur

Pendant les tests, nous avons utilisé des commandes Linux pour vérifier que MariaDB et les processus du serveur étaient présents :

~~~bash
systemctl is-active mysqld

ps aux | grep -E 'db_server|login_server|game_server' | grep -v grep

ss -lntp | grep -E '3306|9006|8888|8885|9002'
~~~

Dans notre laboratoire, le port 3306 correspondait à MariaDB/MySQL. Le port 9006 était associé au service DB Talisman et était observé en écoute locale. Les ports 8888, 8885 et 9002 étaient également observés parmi les ports des services Talisman. Leur attribution exacte peut dépendre de la configuration et n'est pas détaillée ici.

Ces contrôles permettent de distinguer plusieurs situations :
- le processus n'est pas démarré ;
- le processus tourne mais n'écoute pas sur le port attendu ;
- le port écoute, mais le réseau ou le pare-feu bloque l'accès ;
- le réseau fonctionne, mais l'authentification SQL échoue.

## 7. Sécurité : pourquoi utiliser un réseau privé ?

ZeroTier nous a permis de tester l'infrastructure sans rendre directement les services accessibles à tout Internet. Seuls les appareils autorisés sur le réseau privé pouvaient rejoindre cette partie de notre environnement, sous réserve des règles de pare-feu et de service.

Quelques règles à retenir :
- ne pas publier le Network ID, les adresses privées ou les mots de passe dans un dépôt public ;
- ne pas exposer MariaDB sur Internet ;
- éviter l'utilisation de `root` pour les connexions distantes permanentes ;
- créer des utilisateurs SQL avec des privilèges limités ;
- limiter les ports autorisés aux appareils et services nécessaires ;
- sauvegarder les bases avant toute modification ;
- ne jamais publier de données réelles de comptes ou de joueurs.

## 8. Ce que nous avons validé dans le laboratoire

Les éléments suivants ont été testés dans notre environnement :
- communication entre Windows et CentOS au travers de ZeroTier ;
- réponses au test ping entre les deux machines ;
- état actif de MariaDB/MySQL ;
- présence des processus serveur Talisman pendant les tests ;
- connexion depuis Windows à la base MariaDB avec DBeaver ;
- bases de données Talisman importées, dont `db_account`, `db_game`, `db_gmtool` et `db_log`.

Cela documente une méthode de laboratoire reproductible dans son principe. Les paramètres sensibles et les fichiers privés du serveur ne sont volontairement pas fournis.

## 9. Résumé du flux

1. CentOS héberge MariaDB et les services du serveur Talisman.
2. ZeroTier relie le PC Windows et CentOS dans un réseau privé.
3. Windows utilise l'adresse IP ZeroTier de CentOS pour joindre la machine.
4. DBeaver se connecte à MariaDB via cette adresse IP et le port SQL.
5. Depuis DBeaver, on explore les bases et les tables, notamment celles liées aux comptes du jeu.
6. Les services Talisman utilisent les données stockées dans MariaDB pour leur fonctionnement.

**En résumé : ZeroTier nous donne le chemin réseau privé jusqu'au serveur ; DBeaver nous donne l'interface pour travailler avec les données SQL du serveur.**

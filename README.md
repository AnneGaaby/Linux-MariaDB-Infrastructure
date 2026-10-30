# Infrastructure Linux & MariaDB — Serveur privé Talisman Online

> Projet personnel d'administration système : environnement Linux, base de données MariaDB et services nécessaires au fonctionnement d'un serveur privé de jeu.

**Autrice :** Anne Gabrielly Alves  
**Environnement :** CentOS 7 · MariaDB / MySQL · systemd · VMware / VirtualBox  
**Statut :** laboratoire personnel — documentation publique, fichiers opérationnels privés non publiés.

## Pourquoi ai-je créé ce projet ?

J'ai créé cette infrastructure pour mettre en place la partie serveur de mon projet de serveur privé **Talisman Online**.

Un jeu en ligne ne fonctionne pas uniquement avec le client installé sur l'ordinateur du joueur. Il a aussi besoin de services côté serveur pour gérer les connexions, exécuter la logique du jeu et conserver les données nécessaires à son fonctionnement.

Mon objectif était donc de construire un environnement Linux capable d'héberger les différents services du serveur Talisman et de leur fournir une base de données centrale. J'ai utilisé CentOS 7, installé MariaDB, puis travaillé sur les services serveur et leur gestion avec systemd.

Ce projet m'a permis de pratiquer concrètement l'administration Linux, les bases de données relationnelles, la gestion des services et le diagnostic d'une infrastructure composée de plusieurs processus.

## Pourquoi MariaDB ?

MariaDB est un système de gestion de base de données relationnelle (SGBDR). Il permet de stocker les informations de manière structurée, de les consulter et de les modifier à l'aide du langage SQL.

Dans mon projet, MariaDB sert de couche de stockage pour l'infrastructure du serveur Talisman Online. Les services applicatifs ont besoin de données persistantes. Une base de données leur permet de retrouver ces informations, même après le redémarrage d'un processus ou de la machine.

Dans un jeu en ligne, une base de données peut notamment contenir des comptes, personnages, progression, objets, inventaires et paramètres du jeu. Cette liste décrit des usages habituels ; elle ne prétend pas décrire le schéma exact de la base Talisman utilisée dans mon environnement.

## Pourquoi Linux et CentOS 7 ?

Linux fournit un environnement pour exécuter et administrer des services en arrière-plan. Il permet de gérer les utilisateurs, permissions, processus, journaux, réseau et démarrage automatique des applications.

Dans ce laboratoire, CentOS 7 a servi de système hôte pour MariaDB et les services du serveur Talisman. La machine virtuelle permettait de séparer cet environnement de mon Windows principal.

CentOS 7 est une version ancienne. Pour une nouvelle infrastructure, il faut choisir une distribution encore maintenue et vérifier la compatibilité des logiciels nécessaires.

## À quoi peut servir Linux + MariaDB ?

Cette combinaison n'est pas réservée aux jeux vidéo. Linux héberge les applications et MariaDB conserve les données structurées dont elles ont besoin.

| Domaine | Exemples |
|---|---|
| Jeux en ligne | Comptes, personnages, progression et données persistantes |
| Sites web | Utilisateurs, articles, commentaires et contenus |
| Applications web | Données métier et comptes utilisateurs |
| API / backend | Stockage partagé entre plusieurs clients et services |
| E-commerce | Catalogue, commandes, clients et stocks |
| Gestion d'entreprise | Facturation, inventaire et gestion interne |
| Applications internes | Données de services et outils d'équipe |
| Projets personnels | Hébergement d'un backend et apprentissage de l'administration |

MariaDB peut être utilisé par des applications développées en PHP, Python, Java, JavaScript/Node.js et d'autres langages via un connecteur adapté.

## Architecture générale

Le schéma représente les rôles logiques des composants. Il ne décrit pas tous les détails du protocole propriétaire de Talisman.

~~~mermaid
flowchart TD
    CLIENT[Client Talisman Online] <--> LOGIN[Service de connexion]
    CLIENT <--> GAME[Service de jeu]
    LOGIN <--> DB[(MariaDB)]
    GAME <--> DB
    subgraph VM[Machine virtuelle Linux - CentOS 7]
      LOGIN
      GAME
      DB
      SYSTEMD[systemd - gestion des services]
    end
    SYSTEMD -. gère .-> LOGIN
    SYSTEMD -. gère .-> GAME
    SYSTEMD -. gère .-> DB
    ADMIN[Administration Linux] --> VM
~~~

### Rôle des composants

- **CentOS 7 :** système Linux du laboratoire.
- **MariaDB :** stockage relationnel des données persistantes.
- **talisman-db :** unité de service associée à la base de données.
- **talisman-login :** service associé au processus de connexion.
- **talisman-game :** service associé au fonctionnement du jeu.
- **systemd :** gestion du démarrage, de l'arrêt, de l'état et des journaux des services.
- **VMware / VirtualBox :** virtualisation de l'environnement de test.

## Services Linux

Les unités rencontrées dans le projet comprennent :

- talisman-db
- talisman-login
- talisman-game

Exemples de commandes systemd :

~~~bash
systemctl status talisman-db
sudo systemctl start talisman-db
sudo systemctl stop talisman-db
sudo systemctl restart talisman-db
sudo systemctl enable talisman-db
sudo journalctl -u talisman-db -n 100
~~~

Ces commandes sont des exemples d'administration Linux. Elles supposent que les unités correspondantes existent sur la machine.

## Environnement et outils

- Linux / CentOS 7
- MariaDB et MySQL
- systemd
- VMware Workstation / VirtualBox
- terminal Linux et commandes d'administration
- services serveur Talisman Online
- réseau privé de laboratoire
- dossier partagé VMware, notamment /mnt/hgfs

Les adresses, identifiants, mots de passe, ports internes et fichiers de configuration propres à l'environnement ne sont pas publiés.

## Ce que ce projet m'a appris

- installation et administration d'un environnement Linux ;
- gestion d'une base de données relationnelle ;
- compréhension du rôle de SQL et MariaDB ;
- gestion des services avec systemd ;
- lecture de journaux et diagnostic de démarrage ;
- séparation d'une infrastructure en plusieurs services ;
- utilisation de machines virtuelles ;
- compréhension des échanges entre application, serveur et base de données.

## Sécurité

Une base de données ne doit pas être exposée inutilement à Internet. Une infrastructure réelle doit utiliser des comptes dédiés avec des droits limités, restreindre l'accès réseau, protéger les mots de passe, effectuer des sauvegardes et tester leur restauration.

Voir [SECURITY.md](SECURITY.md).

## Documentation

- [Architecture technique](docs/ARCHITECTURE.md)
- [Linux et systemd](docs/LINUX-SYSTEMD.md)
- [MariaDB et SQL](docs/MARIADB-SQL.md)
- [Guide de laboratoire](docs/LAB-GUIDE.md)
- [Dépannage](docs/TROUBLESHOOTING.md)
- [Feuille de route](docs/ROADMAP.md)

## À propos

Je suis Anne Gabrielly, basée dans le canton de Vaud, en Suisse, et actuellement en formation en développement Web et Web Mobile chez Skill&You Suisse.

Ce projet fait partie de mes travaux pratiques autour de Linux, des bases de données, de l'administration système et des infrastructures backend.

- **Portfolio :** https://annegaaby.github.io/Anne-Gabrielly/
- **GitHub :** https://github.com/AnneGaaby

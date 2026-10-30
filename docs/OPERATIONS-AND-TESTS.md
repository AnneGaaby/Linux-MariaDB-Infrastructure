# Exploitation et vérifications du laboratoire

Ce document décrit les vérifications réellement effectuées pendant le fonctionnement du serveur privé Talisman Online.

## Services utilisés

Les unités systemd identifiées dans le laboratoire sont :
- talisman-mysql-socket.service
- talisman-db.service
- talisman-login.service
- talisman-game.service

Les programmes serveur correspondants étaient séparés en trois composants : db_server, login_server et game_server.

## Ordre de démarrage observé

La séquence utilisée pendant les essais était :
1. arrêter les services applicatifs Talisman ;
2. démarrer le service talisman-mysql-socket ;
3. démarrer talisman-db ;
4. démarrer talisman-login ;
5. démarrer talisman-game.

Cette séquence correspond au fonctionnement observé dans le laboratoire. Il faut toujours vérifier l'état réel des unités et leurs dépendances avant de l'appliquer à un autre environnement.

## Vérifications réellement effectuées

Les tests ont confirmé à un moment donné :
- MariaDB/MySQL actif ;
- les trois processus db_server, login_server et game_server présents ;
- les ports applicatifs attendus en écoute ;
- connectivité entre Windows et CentOS via ZeroTier ;
- connexion à la base depuis Windows avec DBeaver.

Ces résultats montrent que les composants ont pu démarrer et communiquer pendant les essais. Ils ne garantissent pas que toutes les fonctions du jeu sont validées.

## Réseau privé

ZeroTier a été utilisé pour relier la machine Windows et la VM CentOS sur un réseau privé. Un test ping depuis Windows a réussi avec quatre paquets reçus sur quatre. Les adresses et identifiants privés sont omis de cette documentation.

## DBeaver

DBeaver a été utilisé depuis Windows pour accéder à MariaDB. L'outil permet de parcourir les schémas, examiner les tables, lancer des requêtes SQL et vérifier la connexion à une base.

## Ports observés

Les tests ont montré MariaDB/MySQL sur le port 3306, le service DB Talisman sur le port local 9006, et des services Talisman écoutant sur l'interface privée sur les ports 8888, 8885 et 9002. Le rôle précis de chaque port applicatif doit être confirmé à partir des configurations du serveur. Les ports ne sont pas nécessairement identiques dans d'autres versions ou installations.

## Limites et sécurité

Aucune adresse IP, aucun identifiant ZeroTier, mot de passe, compte SQL ni fichier de configuration privé n'est inclus. Les exécutables du serveur et les données de la base ne sont pas distribués.

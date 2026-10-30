# Guide de laboratoire — Linux et MariaDB

Ce guide présente une démarche pour construire un laboratoire comparable. Il ne permet pas de reproduire exactement le serveur privé Talisman : les fichiers binaires, configurations et données ne sont pas inclus dans ce dépôt.

## 1. Définir le but

Identifier les applications à héberger, les données persistantes, les services nécessaires, les ressources CPU/RAM/disque et les besoins réseau.

Dans ce projet, le but était d'héberger les composants serveur d'un environnement privé Talisman Online.

## 2. Préparer une machine virtuelle

Utiliser VMware ou VirtualBox pour créer une machine Linux isolée. Définir la distribution, les ressources, le réseau, les accès administrateur et une stratégie de sauvegarde.

CentOS 7 a été utilisé dans ce projet historique. Pour un nouveau laboratoire, choisir une distribution encore maintenue et vérifier les exigences de compatibilité.

## 3. Installer et vérifier MariaDB

Suivre la documentation officielle correspondant à la distribution et à sa version. Après l'installation, vérifier le service, créer un compte applicatif limité et préparer une sauvegarde testée.

Les commandes d'installation précises ne sont pas données ici, car elles dépendent de la version du système et du dépôt logiciel choisi.

## 4. Séparer les services

Dans le laboratoire Talisman, les unités de service identifiées sont :
- talisman-db ;
- talisman-login ;
- talisman-game.

Cette séparation facilite le démarrage, l'arrêt et le diagnostic de chaque composant.

## 5. Vérifier le fonctionnement

Pour chaque service, vérifier sa configuration, le démarrer, consulter son état systemd, lire ses journaux et tester le flux applicatif attendu.

Un processus présent ne signifie pas nécessairement que le service répond correctement.

## 6. Réseau et accès

N'exposer que les ports nécessaires, limiter l'accès à MariaDB aux services autorisés, préférer un réseau privé de laboratoire et ne pas publier les identifiants.

## 7. Sauvegarde

Prévoir une sauvegarde des données et configurations nécessaires, stockée séparément, et tester la procédure de restauration.

## 8. Ce qui manque pour une reproduction exacte

Il faudrait disposer des versions confirmées, fichiers de configuration expurgés, dépendances, schéma de base vérifié et procédure de restauration testée. Ces éléments ne sont pas publiés dans cette vitrine.

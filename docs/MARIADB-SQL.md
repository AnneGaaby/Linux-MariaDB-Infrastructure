# MariaDB et SQL — rôle et cas d'utilisation

## Qu'est-ce que MariaDB ?

MariaDB est un système de gestion de base de données relationnelle (SGBDR). Il stocke les informations dans des tables composées de lignes et de colonnes. SQL est le langage utilisé pour créer des structures, consulter des données et effectuer des modifications.

MariaDB est un logiciel open source issu de l'écosystème MySQL et compatible avec de nombreux outils et connecteurs MySQL. Cette compatibilité ne signifie pas que toutes les fonctionnalités ou versions sont interchangeables : il faut vérifier les exigences de chaque application.

## Pourquoi l'utiliser dans le laboratoire Talisman ?

Le serveur privé Talisman a besoin d'une couche de stockage persistante. MariaDB a été installé pour fournir cette base aux composants serveur.

Une base relationnelle permet à une application de retrouver des données après le redémarrage d'un processus ou de la machine. Plusieurs services autorisés peuvent consulter ou mettre à jour ces données selon les règles de l'application.

Dans un jeu en ligne, les données persistantes peuvent être des comptes, personnages, objets, inventaires ou progression. Cette liste décrit des cas d'usage généraux ; le schéma exact de la base du projet n'est pas publié ici.

## Où peut-on utiliser MariaDB ?

- sites web et systèmes de gestion de contenu ;
- applications web et API backend ;
- boutiques en ligne et gestion de commandes ;
- outils de gestion d'entreprise ;
- plateformes communautaires ;
- serveurs de jeux ;
- applications internes et projets personnels.

Des applications en PHP, Python, Java, JavaScript/Node.js et d'autres langages peuvent communiquer avec MariaDB via un pilote adapté.

## Notions relationnelles

- **Table :** ensemble de données d'un même type.
- **Ligne :** un enregistrement.
- **Colonne :** un attribut.
- **Clé primaire :** identifiant unique d'une ligne.
- **Clé étrangère :** référence vers une autre table.
- **Requête SQL :** instruction de lecture ou de modification.
- **Transaction :** groupe d'opérations qui doit respecter les règles d'intégrité.

## Exemples SQL pédagogiques

Ces exemples utilisent une table fictive et ne représentent pas le schéma privé de Talisman.

~~~sql
CREATE DATABASE demo_app;
USE demo_app;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(80) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO users (username) VALUES ('player_demo');

SELECT id, username, created_at FROM users;
~~~

Ne jamais exécuter des modifications sur une base de production sans sauvegarde et sans comprendre leur effet.

## Sauvegarde

Une stratégie sérieuse doit prévoir une fréquence de sauvegarde, un stockage séparé, la protection des fichiers et des tests réguliers de restauration. Une sauvegarde jamais restaurée lors d'un test ne garantit pas la récupération.

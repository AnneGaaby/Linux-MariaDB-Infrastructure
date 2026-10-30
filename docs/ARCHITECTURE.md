# Architecture technique — Linux & MariaDB

## Contexte

Cette infrastructure a été créée pour héberger les composants serveur d'un projet personnel de serveur privé Talisman Online. L'environnement de laboratoire repose sur CentOS 7, MariaDB et plusieurs services gérés par systemd.

## Vue logique

~~~mermaid
flowchart TD
    C[Client du jeu] <--> L[Service talisman-login]
    C <--> G[Service talisman-game]
    L <--> D[(MariaDB)]
    G <--> D
    subgraph Linux[Machine virtuelle CentOS 7]
      L
      G
      D
      S[systemd]
    end
    S -. gère .-> L
    S -. gère .-> G
    S -. gère .-> D
~~~

Ce schéma est une représentation fonctionnelle simplifiée. Il ne documente pas les détails du protocole réseau propriétaire du jeu ni le schéma exact des tables.

## Composants

| Composant | Rôle |
|---|---|
| CentOS 7 | Système Linux de l'environnement |
| MariaDB | Stockage relationnel des données persistantes |
| talisman-db | Unité associée à la base de données |
| talisman-login | Service associé à la connexion |
| talisman-game | Service associé au jeu |
| systemd | Gestion du démarrage et de l'état des services |
| VMware / VirtualBox | Virtualisation du laboratoire |

## Pourquoi une base centrale ?

Les services applicatifs ont besoin de données persistantes. Une base relationnelle permet de stocker ces données, de les interroger avec SQL et de les partager entre plusieurs composants autorisés.

Pour un jeu en ligne, les données peuvent inclure comptes, personnages, progression et inventaires. La présence exacte de ces éléments dans le schéma de ce laboratoire n'est pas affirmée sans inspection de la base réelle.

## Limites

Les ports, adresses, identifiants, secrets, tables et paramètres exacts ne sont pas publiés. Ils devront être documentés à partir de fichiers expurgés et de schémas vérifiés.

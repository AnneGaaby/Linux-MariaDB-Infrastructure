# Dépannage — Linux, MariaDB et services

Cette page propose une méthode générale de diagnostic pour le laboratoire.

## Un service systemd ne démarre pas

~~~bash
systemctl status nom-du-service
journalctl -u nom-du-service -n 100
~~~

Vérifier le chemin du programme, les permissions, l'utilisateur du service, les dépendances et les fichiers de configuration.

## MariaDB ne démarre pas

Consulter l'état du service et ses journaux. Examiner les erreurs de configuration, les permissions sur le répertoire de données, l'espace disque, les fichiers de journal et un éventuel conflit de port.

Ne jamais supprimer des fichiers de données pour tenter de corriger un démarrage sans sauvegarde et sans comprendre la cause.

## Un service ne se connecte pas à MariaDB

Vérifier que MariaDB fonctionne, que l'adresse et le port configurés sont corrects, que le compte applicatif existe, que ses permissions sont suffisantes et que le réseau autorise la connexion.

Ne pas publier les mots de passe ni les chaînes de connexion.

## Espace disque insuffisant

~~~bash
df -h
du -sh /var/log
~~~

Identifier les fichiers volumineux avant toute suppression. Ne pas supprimer arbitrairement les journaux ou les fichiers de base de données.

## Un service fonctionne seul, mais pas avec les autres

Vérifier les dépendances, l'ordre de démarrage et la disponibilité réelle de chaque service. Un démarrage réussi ne garantit pas qu'une base est déjà prête à accepter des connexions.

## Méthode générale

1. Reproduire le problème.
2. Identifier le service concerné.
3. Consulter les journaux.
4. Vérifier les dépendances et permissions.
5. Modifier un seul paramètre à la fois.
6. Refaire le même test.
7. Documenter la cause et la solution confirmée.

Masquer les informations sensibles avant de partager une sortie de terminal.

# Linux et systemd — administration des services

## Rôle de Linux

Linux fournit l'environnement dans lequel les applications serveur s'exécutent. Il permet de gérer les processus, utilisateurs, permissions, réseau, stockage, journaux et services.

Dans ce laboratoire, CentOS 7 a été utilisé pour héberger les composants du serveur privé Talisman Online.

## systemd

systemd permet de démarrer, arrêter, superviser et consulter les journaux des services Linux.

~~~bash
# État d'un service
systemctl status talisman-db

# Démarrer, arrêter ou redémarrer
sudo systemctl start talisman-db
sudo systemctl stop talisman-db
sudo systemctl restart talisman-db

# Activer le démarrage automatique
sudo systemctl enable talisman-db

# Consulter les journaux
sudo journalctl -u talisman-db -n 100
~~~

Les unités rencontrées dans le projet comprennent talisman-db, talisman-login et talisman-game. Adapter les commandes aux unités réellement présentes.

## Diagnostic système

~~~bash
# Services actifs
systemctl --type=service --state=running

# Processus
ps aux

# Espace disque
df -h

# Mémoire
free -h

# Ports en écoute
ss -lntup
~~~

Un service peut dépendre d'un autre. Un serveur de jeu peut, par exemple, avoir besoin que la base de données soit disponible avant son démarrage. Vérifier les dépendances systemd et les journaux réels.

Avant de partager une sortie de terminal, supprimer les IP privées, noms d'utilisateur, chemins personnels et secrets.

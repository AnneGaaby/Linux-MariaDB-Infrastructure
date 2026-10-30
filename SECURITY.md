# Politique de sécurité

Ce dépôt documente un laboratoire personnel Linux + MariaDB lié à un projet de serveur privé Talisman Online. Il ne contient pas les fichiers opérationnels privés de l'infrastructure.

## Informations à ne pas publier

- mots de passe, clés privées, tokens et identifiants ;
- sauvegardes de bases de données réelles ;
- fichiers de configuration contenant des secrets ;
- adresses IP privées et détails d'accès réseau ;
- fichiers binaires du serveur ou éléments dont la redistribution n'est pas autorisée ;
- données personnelles ou données de joueurs.

## Bonnes pratiques

- créer des comptes de base de données avec le minimum de privilèges ;
- limiter l'accès réseau à MariaDB ;
- ne pas exposer directement la base à Internet ;
- protéger et tester les sauvegardes ;
- séparer les environnements de test et de production ;
- mettre à jour les systèmes et dépendances ;
- vérifier les permissions des services systemd ;
- retirer les secrets des journaux avant tout partage.

## Système ancien

CentOS 7 est un système ancien. Il ne doit pas être considéré comme un choix adapté à un nouveau serveur exposé sans plan de migration et évaluation de sécurité. Les besoins de compatibilité d'un logiciel historique doivent être isolés et compensés par des mesures adaptées.

## Signalement

Ne publiez jamais de secret ou de donnée sensible dans une issue publique. Pour une question liée à ce dépôt, utilisez le profil GitHub de l'autrice : https://github.com/AnneGaaby

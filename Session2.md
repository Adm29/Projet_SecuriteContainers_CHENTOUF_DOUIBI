Compte Rendu Session 2:

Objectifs:
  1-Appliquer les meilleures pratiques de sécurité pour les containers.
  2-Comprendre la gestion des droits et permissions.
  3-Apprendre à sécuriser les communications entre containers.
  4-Gérer de manière sécurisée les secrets et variables d’environnement.

  1/ Principales Bonnes Pratiques
  a) Utilisation d’images de confiance
    Télécharger uniquement des images officielles ou vérifiées.
    Scanner les images avant de les utiliser.
  b) Restriction des privilèges
    Ne jamais exécuter des containers avec --privileged.
    Éviter de monter /var/run/docker.sock dans les containers.
  c) Utilisation des profils AppArmor et Seccomp
    Restreindre l’accès aux appels système sensibles.
    Exécuter Docker avec un profil de sécurité par défaut.
  d) Mise en place de scans de sécurité automatisés
    Intégrer des outils comme Trivy dans les pipelines CI/CD.
  
Mise en pratique et Exercies:
  1:
  

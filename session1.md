## Compte rendu  | Session 1 : Introduction aux Containers et à la Sécurité

# Objectifs

Comprendre le fonctionnement des containers et leurs différences avec les machines virtuelles.
Identifier les enjeux de sécurité liés aux containers.

# Activités Pratiques

Après l'installation de Docker, on a executé un container de test  

![Capture d'écran 2025-03-19 150754](https://github.com/user-attachments/assets/6d3b8cbb-6333-4904-bf1b-60f98be4a3fd)

On voit que le client Docker contacte le daemon, que ce dernier va chercher l'image "hello-world" du Docker Hub et crée un nouveau container et enfin le transmet au client qui va l'envoyer à notre terminal.

Après, on veut explorer un container en interactif.

On lance un container interactif basé sur alpine et on teste quelque commandes (ls, pxc, whoami)



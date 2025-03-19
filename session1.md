## Compte rendu  | Session 1 : Introduction aux Containers et à la Sécurité

# Objectifs

Comprendre le fonctionnement des containers et leurs différences avec les machines virtuelles.
Identifier les enjeux de sécurité liés aux containers.

# Activités Pratiques

1) Container de test  :

  docker run --rm hello-world


![Capture d'écran 2025-03-19 150754](https://github.com/user-attachments/assets/6d3b8cbb-6333-4904-bf1b-60f98be4a3fd)

On voit que le client Docker contacte le daemon, que ce dernier va chercher l'image "hello-world" du Docker Hub et crée un nouveau container et enfin le transmet au client qui va l'envoyer à notre terminal.

2) Explorer un container en interactif : 

On lance un container interactif basé sur alpine et on teste quelque commandes (ls, pwc, whoami) :

docker run -it --rm alpine sh

3) Analyser les ressources système d’un container

Lancer un container et surveiller ses ressources :

docker run -d --name test-container nginx
docker stats test-container

On observe la consommation de CPU et mémoire : 

![Capture d'écran 2025-03-19 151608](https://github.com/user-attachments/assets/16907984-2963-41c8-8692-3a0438e022fc)

On voit que le container test utilise 0% du CPU et qu'il utilise que 11.23 MiB sur 7.599 GiB, soit 0.14%, de la  mémoire.

4) Lister les capacités d’un container

On a vu la doc sur les capabilities Linux

On vérifie les permissions :

docker run --rm --cap-add=SYS_ADMIN alpine sh -c 'cat /proc/self/status'

![Capture d'écran 2025-03-19 151835](https://github.com/user-attachments/assets/d8ec183b-021c-4e85-8a2f-4e987eaca20a)


![Capture d'écran 2025-03-19 151849](https://github.com/user-attachments/assets/9fb3c95a-cc40-43f2-8776-08cff01182d3)

# Vulnérabilités et Menaces

# Objectifs

Identifier les vulnérabilités courantes dans un environnement conteneurisé.
Comprendre comment les attaquants exploitent ces failles.

# Activités Pratiques

1) Tester un Container avec des Permissions Élevées

On lance un container en mode privilégié et on exécute une commande système :

docker run --rm --privileged alpine sh -c 'echo hello from privileged mode'

Cette pratique est dangereuse car ...

2) Simuler une Évasion de Container

On lance un container et on monte le système de fichiers de l’hôte :

docker run --rm -v /:/mnt alpine sh -c 'ls /mnt'

Les implications de cette faille de configuration sont ...
Créer une Image Sécurisée

3) Écrire un Dockerfile minimaliste :

FROM alpine
RUN adduser -D appuser
USER appuser
CMD ["echo", "Container sécurisé!"]
Construire et exécuter l’image.
Afficher l'id et l'uid de cet utilisateur

4) Restreindre l’accès réseau d’un container

5) Bloquer la connexion internet dans un container :

docker network disconnect bridge mon-container

6) Tester l’accès internet avec par exemple ping google.com.

7) Télécharger et Scanner une Image

Télécharger une image vulnérable et l’analyser avec Trivy :

docker pull vulnerables/web-dvwa
trivy image vulnerables/web-dvwa
Faites un scan et sauvegarder le résultat en json
Faites un rapide résumé des vulnérabilités de cette image

8) Scanner une Image pour Détecter les Vulnérabilités

Analyser une image avec Grype :

grype alpine:latest
Comparer votre image build avec l'image alpine:latest
Quelles sont les différences entre grype et trivy ?


# Étude de Cas : Attaque par Élévation de Privilège

Un attaquant exploite une faille dans un container mal configuré.
Il parvient à exécuter du code sur l’hôte et récupérer la sauvegarde d'une base de données qui contient des données bancaires.

Quelles mesures auraient pu empêcher cette attaque ?








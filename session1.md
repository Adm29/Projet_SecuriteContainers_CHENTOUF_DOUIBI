# Compte rendu  | Session 1 : Introduction aux Containers et à la Sécurité

## Objectifs

Dans cette session, on cherche à comprendre le fonctionnement des containers et leurs différences avec les machines virtuelles et à identifier les enjeux de sécurité liés aux containers.

## Activités Pratiques

1) On commence par un container de test  :

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

## Vulnérabilités et Menaces

## Objectifs

Dans cette partie, on cherche donc à identifier les vulnérabilités courantes dans un environnement conteneurisé et comprendre comment les attaquants exploitent ces failles.

## Activités Pratiques

1) Tester un Container avec des Permissions Élevées

On lance un container en mode privilégié et on exécute une commande système :

docker run --rm --privileged alpine sh -c 'echo hello from privileged mode'

Cette pratique est dangereuse, car elle accorde au conteneur un accès total aux ressources de l’hôte. 

2) Simuler une Évasion de Container

On lance un container et on monte le système de fichiers de l’hôte :

docker run --rm -v /:/mnt alpine sh -c 'ls /mnt'

![image](https://github.com/user-attachments/assets/cc4afc3d-962e-4d27-9d99-fd66cd784754)

Comme le montre notre test, le conteneur a pu lister l’ensemble du système de fichiers de l’hôte via /mnt, ce qui pourrait lui permettre de modifier des fichiers sensibles ou d’interagir avec des périphériques critiques.

Les implications de cette faille de configuration sont une exposition totale du système hôte, permettant à un attaquant d’accéder, de modifier ou de supprimer des fichiers critiques, voire de prendre le contrôle complet de la machine.

3) Créer une Image Sécurisée

On crée un dossier secure-container dans lequel on crée un fichier Dockerfile minimaliste :

FROM alpine
RUN adduser -D appuser
USER appuser
CMD ["echo", "Container sécurisé!"]

On construit et on exécute l’image puis on affiche l'id et l'uid de cet utilisateur


![image](https://github.com/user-attachments/assets/32f10e90-fb73-4aa4-89a8-d11a069c89b6)



4) Restreindre l’accès réseau d’un container

5) Bloquer la connexion internet dans un container :

docker network disconnect bridge mon-container

6) Tester l’accès internet avec par exemple ping google.com.

![image](https://github.com/user-attachments/assets/b303b9cb-2d1a-4cad-afb4-b8b422482792)

On veut bloquer la connexion internet au secure-container créé précédemment.

Le secure container était dans les images sur Docker Desktop et n'était pas reconu dans la commande pour le déconnecter.

Il a fallu qu'on le fasse tourner avec la commande suivante : 

docker run -d secure-container sleep inf 

Après on a retesté la commande suivante : 

docker network disconnect bridge secure-container 

Cependant ca ne marchait pas donc on a mis son ID : 

docker network disconnect bridge 711f62910213

On a vu qu'il était bien déconnecté puis on a créé un shell à l'intérieur duquel on a testé le ping car en dehors du shell nous n'obtenions pas le résultat attendu : 

docker exec -it 711f62910213 sh

ping google.com



7) Télécharger et Scanner une Image

On télécharge une image vulnérable et on l’analyse avec Trivy :

docker pull vulnerables/web-dvwa

trivy image vulnerables/web-dvwa

On fait un scan et on sauvegarde le résultat en json

![image](https://github.com/user-attachments/assets/12ed5bf7-da11-4fba-89e3-0f61c894122e)

Rapide résumé des vulnérabilités de cette image : 

Trivy détecte des failles critiques dans les dépendances et le système.

L’image web-dvwa contient des packages obsolètes avec des vulnérabilités d’élévation de privilège et d’exécution de code à distance.

Certaines failles sont exploitables via le navigateur ou des services exposés.

8) Scanner une Image pour Détecter les Vulnérabilités

On analyse une image avec Grype :

grype alpine:latest

Et on compare l'image build avec l'image alpine:latest

![image](https://github.com/user-attachments/assets/77868665-c1e0-408c-8a16-d03b5983c0fa)

Nous avons effectué une analyse de vulnérabilités sur deux images Docker : Alpine:latest et notre image secure-container, en utilisant l’outil Grype.

Les résultats indiquent 0 vulnérabilité détectée pour les deux images, confirmant ainsi qu’elles ne contiennent aucun paquet présentant des failles connues à la date de l’analyse.

Les différences entre grype et trivy : 

Grype et Trivy sont deux scanners de vulnérabilités pour les images Docker, mais ils diffèrent sur plusieurs points. 

Grype est plus précis sur les vulnérabilités OS, alors que Trivy fournit une couverture plus large incluant les applications.


## Étude de Cas : Attaque par Élévation de Privilège

Un attaquant exploite une faille dans un container mal configuré.
Il parvient à exécuter du code sur l’hôte et récupérer la sauvegarde d'une base de données qui contient des données bancaires.

## Quelles mesures auraient pu empêcher cette attaque ?


Cette attaque aurait pu être évitée en appliquant des mesures de sécurité de base lors de la configuration du conteneur. Par exemple, il ne faut pas utiliser --privileged, car cela donne trop de droits au conteneur sur l’hôte. De plus, exécuter le conteneur avec un utilisateur non-root permet de limiter les dégâts en cas d’exploitation. Il aurait aussi été important de restreindre l’accès aux fichiers sensibles en évitant de monter des volumes critiques de l’hôte. Enfin, en analysant l’image avec Trivy ou Grype, il aurait été possible de détecter des vulnérabilités connues avant son exécution.








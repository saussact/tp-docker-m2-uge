# TP Docker M2 UGE  2021 - Module DevOps

# Table of Contents
0. [Introduction](#0-les-conteneurs-cest-quoi-)
1. [Installation](#1-installation)
2. [First step](#2-first-step)
3. [Découverte](#3-découverte)
4. [Dockerfile](#4-dockerfile)
5. [Docker Compose](#5-docker-compose)

## Rendu :

Repondre à l'ensemble des questions posés. Ce TP servira de cours, c'est à vous d'aller chercher les notions. Pour comprendre les thématiques abordés.
Rendre le compte rendu qui vous servira de (cours / fiche de révision) du TP avant mardi 16 mars 23h59.

Vous pouvez créer un branche via master exemple : feature/<NomPrenom>

## 0. Les conteneurs c'est quoi ?

Faire un schéma rapide pour comprendre et expliquer la différence entre conteneur et VM ? (3' à 5'). https://www.youtube.com/watch?v=caXHwYC3tq8

C'est quoi un micro-service ? https://youtu.be/ucHwp1jUS2w

Quel est l'avantage d'utiliser les conteneurs dans une approche Microservice?

(PS: C'est quoi de regarder des videos sur youtube en cours?)

## 1. Installation

Au vu de l'evolution TRES rapide de docker, l'ideal est de ce rendre sur la doc officiel de Docker :
https://docs.docker.com/engine/

Pour facilité le TP on ajoutera l'utilisateur tpdocker
`$ sudo usermod -aG docker tpdocker`

Penser à se déloguer reloguer pour recharger les groupes de l'utilisateur tpdocker

Pour vérifier que l'installation est bonne :
`$ docker run hello-world`

## 2. First step

### a. Découverte

`$ docker ps -a`

tu devrais voir le docker avec l'image 'hello-world'

`$ docker run -it --rm ubuntu:xenial /bin/bash`

Puis depuis un autre terminal on lance (ctrl+t sur un terminal):

`$ docker ps`

Quelle est la différence avec ps -a ?

Regarder les process qui tournent dans le container :

`# ps -edf`

Que remarque-t-on ?

Pour quitter le conteneur :
`# exit`

Puis à nouveau 

`$ docker ps -a`

Qu'est ce que tu remarques ?

### b. Gestion des images docker

`$ docker images`

Que fais cette commande ?

`$ docker search mysql`

`$ docker images`

`$ docker pull mysql`

`$ docker images`

`$ docker ps -a`

Quelle différence etre un conteneur et une image Docker ?

## 3. Déployer un Wordpress / Jouer avec les images


Pour déployer wordpress il faut (cas simple) ?

    1) 2 volumes
        1 pour wordpress
        1 pour mysql
    2) 2 conteneurs
        1 conteneur wordpress
        1 conteneur mysql

![Alt text](images/schemaDocker.png?raw=true)


On va donc créer tout ca : 

`$ docker network create web`

`$ docker volume create wordpress`

`$ docker volume create mysql`

`$ docker volume list`

Récupération des images Docker WP et MySQL

`$ docker pull wordpress`

`$ docker pull mysql`

Pour regarder les détails des images :

`$ docker inspect --type image mysql:latest`

`$ docker inspect --type image wordpress:latest`

Quelles sont les informations importantes ici ? Est-ce suffisant ? 

https://hub.docker.com/_/mysql/
https://hub.docker.com/_/wordpress/

Dans quel ordre devont nous démarrer nos services ?

`$  docker run -e MYSQL_ROOT_PASSWORD=12345 -e MYSQL_DATABASE=wordpress --name wordpressdb -v database:/var/lib/mysql -d mariadb:latest`


Vérifier que le conteneur MySQL est bien lancé :)

`$ docker run -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=12345 --name wordpress --link wordpressdb:mysql -p 8090:80 -v html:/var/www/html -d wordpress`

Vérifier que le conteneur WP est bien lancé :)


Se rendre sur `http://${IP_SERVEUR}:8090/` ${IP_SERVEUR} = localhost et créer un article de test.

Arrêter les deux dockers :

`$ docker stop wordpress mysql`
ou
`$ docker stop ${CONTAINER ID WP} ${CONTAINER ID MySQL}`


Lancez `$ docker ps -a` puis `$ docker volume list` 

Quelles sont les différences? c'est normal?

J'espère que vous etes capable de relancer WP tout seul :)

## 4. Dockerfile

Objectif : 
Créer une image docker qui fait tourner une petite app node. 


Allez dans le repertoire ./dockerfile/

Ouvrir le fichier dockerfile. Que veulent dire les lignes ? (NB : google is your friend) 

Lancer le build du docker file : 

`docker build -t tsaussac/node-app .`

Vous venez normalement de créer une image docker ! Good Job 

Vérifier quelle existe. (docker images)

Maintenant vous pouvez lancer le docker : 

`docker run -p 8088:8080 -d tsaussac/node-app`

vérifier que le conteneur est bien lancé.

`docker ps`


Ouvrez un navigateur : localhost:8088

Qu'est ce qui est écrit?

Arretez vos dockers.

**NB** : Pour toute modification du dockerfile, pensez à bien relancer 'docker build'

## 5. Docker Compose

Objectif : On revient au serveur wordpress, ou a fait pleins de chose manuel (cf 3.), Ici on va automatiser tout ca dans un docker compose.
--> On aussi lancer le dockerfile qu'on a crée juste avant.

![Alt text](images/schemaDocker.png?raw=true)
![Alt text](images/serveurNode.png?raw=true)

Aller dans le repertoire dockercompose

Expliquer ce que vous comprennez des lignes et faire le liens avec ce qui a été fait au au chap 3.

Lancer `docker-compose` pour regarder les différentes commandes.

Dans le répertoire dockercompose, lancer `docker-compose up`.

Aller sur http://localhost:8090/, qu'avez vous?
Aller sur http://localhost:8088/, qu'avez vous?

Dans le terminal vous voyez des logs ? vous les comprennez?
Dans un nouveau terminal lancer `docker ps` qu'avez vous ? ca vous semble cohérent?


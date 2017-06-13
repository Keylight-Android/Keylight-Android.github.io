## Objectif

## Cahier des charges

## Présentation

## Architecture

### Architecture du système

![Alt text](https://raw.githubusercontent.com/Keylight-Android/Keylight-Android.github.io/master/system_architecture.png "Architecture du système")


A. Application Android (Java).
B. API (Ruby On Rails) hébergée dans le service Elastic Beanstalk de AWS (Amazon Web Services).
C. Base de données PostgreSQL hébergée dans le service RDS (Relational Database Service) de AWS.
D. Base de données locale Realm permettant de sauvegarder les données utilisateur même si l'application est quittée.


1. L'application fait une requête à l'API afin de récupérer les nouvelles données.
2. L'API fait une requête sur les tables correspondantes de la BDD.
3. La BDD retourne les lignes des tables correspondant à la requête.
4. L'API renvoie un JSON contenant les objets.
5. L'application demande à la BDD locale de formatter les données brutes en objets.
6. La BDD locale sauvegarde et renvoie les objets Java.

## Evaluations utilisateurs

## Limites et améliorations possibles
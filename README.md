## Objectif

## Cahier des charges

## Présentation

## Architecture

### Architecture du système

![Architecture du système](https://raw.githubusercontent.com/Keylight-Android/Keylight-Android.github.io/master/system_architecture.png "Architecture du système")


A. **Application Android** (*Java*).

B. **API** (*Ruby On Rails*) hébergée dans le service *Elastic Beanstalk* de *AWS* (*Amazon Web Services*).

C. **Base de données PostgreSQL** hébergée dans le service *RDS* (*Relational Database Service*) de *AWS*.

D. **Base de données locale Realm** permettant de sauvegarder les données utilisateur même si l'application est quittée. Ce choix a été fait afin de gérer plus facilement les objets, et de former dynamiquement les objets Java depuis des objets JSON.


1. L'application fait une requête à l'API afin de récupérer les nouvelles données.
2. L'API fait une requête sur les tables correspondantes de la BDD.
3. La BDD retourne les lignes des tables correspondant à la requête.
4. L'API renvoie un *JSON* contenant les objets.
5. L'application demande à la BDD locale de formatter les données brutes en objets.
6. La BDD locale sauvegarde et renvoie les objets Java.

### Architecture de l'application

L'architecture choisie pour cette application est l'architecture **MVC**.

![Architecture MVC](https://raw.githubusercontent.com/Keylight-Android/Keylight-Android.github.io/master/MVC_structure.png "Architecture MVC")

1. L'utilisateur change d'onglet ou refraîchit une liste. La *View* demande au *Controller* les dernières données associées à la requête. Cette requête se fait via un **RecyclerView**, qui sait les requêtes nécessaires pour chaque *View*. Par exemple, le *LocksListFragment* possède un *LocksRecyclerView* qui sait que la requête de rafraichissement consiste à aller chercher les dernières *DoorLocks* et *SharedKeys* du *User*.
2. Si l'application est connectée à Internet, le *Controller* lance une requête à l'API afin de récupérer les dernières *DoorLocks* et *SharedKeys*. Sinon, il récupère les données sauvegardées dans la BDD Realm locale.
3. Une fois la requête terminée, la BDD locale Realm stocke les nouvelles données et envoie un signal au *Controller* lui disant que les données sont prêtes. Ce signal est pris en charge par **ReactiveX**. Chaque *Controller* possède des *listener* à certains évènements *ReactiveX* (comme "Realm a fini de sauvegarder les DoorLocks" ou "Realm a fini de supprimer les SharedKeys demandées"), dépendant des données qu'il gère.

```java
public void doOwnSubscriptions() {
        final ReactiveXService reactiveXService = ReactiveXService.getInstance(getActivity().getApplicationContext());
        reactiveXService.subscribeToSaveUserPublishSubject(saveUserObserver);
        reactiveXService.subscribeToSaveDoorLocksPublishSubject(saveDoorLocksObserver);
        reactiveXService.subscribeToDeleteDoorLockPublishSubject(deleteDoorLockObserver);
        reactiveXService.subscribeToSaveSharedKeysPublishSubject(saveSharedKeysObserver);
        reactiveXService.subscribeToDeleteSharedKeyPublishSubject(deleteSharedKeyObserver);
    }
```

4. Lorsque le *Controller* reçoit le signal de *ReactiveX*, il fait une requête sur la BDD Realm locale afin de récupérer les objets demandés. Il envoie alors ces objets à la vue qui les affiche en passant par un Adpater. Chaque vue possède un Adapter permettant de représenter les objets reçus au sein de la *View*.

### Structure des données

![Architecture des données](https://raw.githubusercontent.com/Keylight-Android/Keylight-Android.github.io/master/data_structure.png "Architecture des données")

* *User* : Cette classe représente un utilisateur. Il possède des objets *Contact*, des *DoorLock* et des *SharedKey*.
* *Contact* : Cette classe représente la relation entre deux *Users*. Lorsque d'un *User* ajoute un autre *User* via son username ou son email, une demande lui ai envoyée.
* *DoorLock* : Cette classe représente une serrure. Elle est caractérisée par son numéro de série, qui sert à l'activer lors de son installation. Elle possède également une adresse pour permettre de proposer des services et features de localisations à l'utilisateur.
* *SharedKey* : Cette classe représente un accès spécifique à une *DoorLock* pour un *User* donné. Chaque *SharedKey* possède une date de début, de fin et un booléen qui indique si le *User* est admin est peut alors partager et gérer la *DoorLock* à son tour.


## Evaluations utilisateurs

## Limites et améliorations possibles
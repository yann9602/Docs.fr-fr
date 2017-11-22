## <a name="overview"></a>Vue d'ensemble

Ce didacticiel crée l’API suivante :

|API | Description | Corps de la requête | Corps de réponse |
|--- | ---- | ---- | ---- |
|GET /api/todo | Obtenir toutes les tâches | Aucune | Tableau de tâches|
|GET /api/todo/{id} | Obtenir un élément par ID | Aucune | Tâche|
|POST /api/todo | Ajouter un nouvel élément | Tâche | Tâche |
|PUT /api/todo/{id} | Mettre à jour un élément existant &nbsp; | Tâche | Aucune |
|DELETE /api/todo/{id} &nbsp; &nbsp; | Supprimer un élément &nbsp; &nbsp; | Aucune | Aucune|

<br>

Le diagramme suivant illustre la conception de base de l’application.

![Le client, représenté par une zone située à gauche, soumet une requête et reçoit une réponse de l’application, représentée par une zone dessinée sur la droite. Dans la zone de l’application, trois zones représentent le contrôleur, le modèle et la couche d’accès aux données. La requête provient du contrôleur de l’application, et les opérations de lecture/écriture se produisent entre le contrôleur et la couche d’accès aux données. Le modèle est sérialisé et retourné au client dans la réponse.](../../tutorials/first-web-api/_static/architecture.png)

* Le client correspond à tout ce qui consomme l’API web (application mobile, navigateur, etc.). Ce didacticiel ne crée pas de client. [Postman](https://www.getpostman.com/) ou [curl](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/curl.1.html) est utilisé comme client pour tester l’application.

* Un *modèle* est un objet qui représente les données de l’application. Dans le cas présent, le seul modèle est une tâche. Les modèles sont représentés sous forme de classes C#, également appelées objets POCO (**P**lain **O**ld **C**# **O**bject).

* Un *contrôleur* est un objet qui gère les requêtes HTTP et crée la réponse HTTP. Cette application comporte un seul contrôleur.

* Pour que le didacticiel reste simple, l’application n’utilise pas une base de données persistante. L’exemple d’application stocke des tâches dans une base de données en mémoire.

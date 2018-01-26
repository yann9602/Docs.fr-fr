---
title: "Créer une API web avec ASP.NET Core et Visual Studio pour Mac"
description: "Créer une API web avec ASP.NET Core MVC et Visual Studio pour Mac"
author: rick-anderson
ms.author: riande
ms.date: 09/15/2017
ms.topic: get-started-article
ms.prod: asp.net-core
uid: tutorials/first-web-api-mac
helpviewer_heywords: ASP.NET Core, WebAPI, Web API, REST, mac, macOS, HTTP, Service, HTTP Service
ms.technology: aspnet
manager: wpickett
ms.openlocfilehash: 4f2643a91e1523008b68df670a9734e3d4dea5a8
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="create-a-web-api-with-aspnet-core-mvc-and-visual-studio-for-mac"></a>Créer une API web avec ASP.NET Core MVC et Visual Studio pour Mac

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Mike Wasson](https://github.com/mikewasson)

Dans ce didacticiel, vous allez générer une API web pour la gestion d’une liste de tâches. Vous ne générerez pas d’interface utilisateur.

Il existe trois versions de ce didacticiel :

* macOS : API web avec Visual Studio pour Mac (ce didacticiel)
* Windows : [API web avec Visual Studio pour Windows](xref:tutorials/first-web-api)
* macOS, Linux, Windows : [API web avec Visual Studio Code](xref:tutorials/web-api-vsc)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[template files](../includes/webApi/intro.md)]

* Pour obtenir un exemple qui utilise une base de données persistante, consultez [Introduction à ASP.NET Core MVC sur Mac ou Linux](xref:tutorials/first-mvc-app-xplat/index).

## <a name="prerequisites"></a>Prérequis

Installez les éléments suivants :

- [SDK .NET Core 2.0.0 ](https://www.microsoft.com/net/core) ou version ultérieure
- [Visual Studio pour Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-the-project"></a>Créer le projet

Dans Visual Studio, sélectionnez **Fichier > Nouvelle solution**.

![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

Sélectionnez **Application .NET Core > API web ASP.NET Core > Suivant**.

![macOS - Boîte de dialogue Nouveau projet](first-web-api-mac/_static/1.png)

Entrez **TodoApi** comme **Nom du projet**, puis sélectionnez Créer.

![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

### <a name="launch-the-app"></a>Lancer l’application

Dans Visual Studio, sélectionnez **Exécuter > Démarrer avec débogage** pour lancer l’application. Visual Studio lance un navigateur et accède à `http://localhost:5000`. Vous obtenez une erreur HTTP 404 (introuvable).  Remplacez l’URL par `http://localhost:port/api/values`. Les données `ValuesController` s’affichent :

```
["value1","value2"]
```

### <a name="add-support-for-entity-framework-core"></a>Ajouter la prise en charge d’Entity Framework Core

Installez le fournisseur de base de données [Entity Framework Core InMemory](https://docs.microsoft.com/ef/core/providers/in-memory/). Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec une base de données en mémoire.

* Dans le menu **Projet**, choisissez **Ajouter des packages NuGet**. 

  *  Vous pouvez aussi cliquer sur **Dépendances**, puis sélectionner **Ajouter des packages**.

* Entrez `EntityFrameworkCore.InMemory` dans la zone de recherche.
* Sélectionnez `Microsoft.EntityFrameworkCore.InMemory`, puis **Ajouter un package**.

### <a name="add-a-model-class"></a>Ajouter une classe de modèle

Un modèle est un objet qui représente les données dans votre application. Dans le cas présent, le seul modèle est une tâche.

Ajoutez un dossier nommé *Models*. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet. Sélectionnez **Ajouter** > **Nouveau dossier**. Nommez le dossier *Models*.

![nouveau dossier](first-web-api-mac/_static/folder.png)

Remarque : Vous pouvez placer des classes de modèle n’importe où dans votre projet, mais le dossier *Models* est utilisé par convention.

Ajoutez une classe `TodoItem`. Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter > Nouveau fichier > Général > Classe vide**. Nommez la classe `TodoItem`, puis sélectionnez **Nouveau**.

Remplacez le code généré par ce qui suit :

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

La base de données génère la valeur `Id` quand un `TodoItem` est créé.

### <a name="create-the-database-context"></a>Créer le contexte de base de données

Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données spécifié. Vous créez cette classe en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.

Ajoutez une classe `TodoContext` au dossier *Models*.

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

## <a name="add-a-controller"></a>Ajouter un contrôleur

Dans l’Explorateur de solutions, dans le dossier *Contrôleurs*, ajoutez la classe `TodoController`.

Remplacez le code généré par ce qui suit (et ajoutez des accolades fermantes) :

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a>Lancer l’application

Dans Visual Studio, sélectionnez **Exécuter > Démarrer avec débogage** pour lancer l’application. Visual Studio lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi au hasard. Vous obtenez une erreur HTTP 404 (introuvable).  Remplacez l’URL par `http://localhost:port/api/values`. Les données `ValuesController` s’affichent :

```
["value1","value2"]
```

Accédez au contrôleur `Todo` à l’adresse `http://localhost:port/api/todo` :

```
[{"key":1,"name":"Item1","isComplete":false}]
```

## <a name="implement-the-other-crud-operations"></a>Implémenter les autres opérations CRUD

Nous allons ajouter les méthodes `Create`, `Update` et `Delete` au contrôleur. Comme il s’agit de variations sur un même thème, je vais simplement montrer le code et mettre en évidence les principales différences. Générez le projet après avoir ajouté ou modifié du code.

### <a name="create"></a>Créer

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Il s’agit d’une méthode HTTP POST, indiquée par l’attribut [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute). L’attribut [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) indique à MVC qu’il faut obtenir la valeur de l’élément d’action à partir du corps de la requête HTTP.

La méthode `CreatedAtRoute` retourne une réponse 201, qui constitue la réponse standard pour une méthode HTTP POST qui crée une ressource sur le serveur. `CreatedAtRoute` ajoute également un en-tête Location à la réponse. L’en-tête Location spécifie l’URI de l’élément d’action qui vient d’être créé. Consultez [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

### <a name="use-postman-to-send-a-create-request"></a>Utiliser Postman pour envoyer une requête Create

* Démarrez l’application (**Exécuter > Démarrer avec débogage**).
* Démarrez Postman.

![Console Postman](first-web-api/_static/pmc.png)

* Affectez la valeur `POST` à la méthode HTTP.
* Sélectionnez la case d’option **Body**.
* Sélectionnez la case d’option **raw**.
* Sélectionnez le type JSON.
* Dans l’éditeur de clé-valeur, entrez un élément d’action comme

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* Sélectionnez **Send**.

* Sélectionnez l’onglet Headers dans le volet inférieur et copiez l’en-tête **Location** :

![Onglet Headers de la console Postman](first-web-api/_static/pmget.png)

Vous pouvez utiliser l’URI d’en-tête Location pour accéder à la ressource que vous venez de créer. Rappelez la méthode `GetById` qui a créé la route nommée `"GetTodo"` :

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(string id)
```

### <a name="update"></a>Mise à jour

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`Update` est similaire à `Create`, mais utilise HTTP PUT. La réponse est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les différences. Pour prendre en charge les mises à jour partielles, utilisez HTTP PATCH.

```json
{
  "key": 1,
  "name": "walk dog",
  "isComplete": true
}
```

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmcput.png)

### <a name="delete"></a>Supprimer

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

La réponse est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmd.png)

## <a name="next-steps"></a>Étapes suivantes

* [Routage vers les actions du contrôleur](xref:mvc/controllers/routing)
* Pour plus d’informations sur le déploiement de votre API, consultez [Héberger et déployer](xref:host-and-deploy/index).
* [Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))
* [Postman](https://www.getpostman.com/)
* [Fiddler](https://www.telerik.com/download/fiddler)

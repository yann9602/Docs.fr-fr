---
title: "Créer une API web avec ASP.NET Core et Visual Studio pour Windows"
author: rick-anderson
description: "Générer une API web avec ASP.NET Core MVC et Visual Studio pour Windows"
ms.author: riande
manager: wpickett
ms.date: 08/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-web-api
ms.openlocfilehash: 234dbf73f9751ad4f995d6e7471d94f060fb15bf
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
#<a name="create-a-web-api-with-aspnet-core-and-visual-studio-for-windows"></a>Créer une API web avec ASP.NET Core et Visual Studio pour Windows

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Mike Wasson](https://github.com/mikewasson)

Ce didacticiel génère une API web de gestion d’une liste de tâches. Aucune interface utilisateur (IU) n’est créée.

Il existe trois versions de ce didacticiel :

* Windows : API web avec Visual Studio pour Windows (ce didacticiel)
* macOS : [API web avec Visual Studio pour Mac](xref:tutorials/first-web-api-mac)
* macOS, Linux, Windows : [API web avec Visual Studio Code](xref:tutorials/web-api-vsc)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE[install 2.0](../includes/install2.0.md)]

Consultez [ce fichier PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) pour la version ASP.NET Core 1.1.

## <a name="create-the-project"></a>Créer le projet

Dans Visual Studio, sélectionnez **Fichier** Menu > **Nouveau** > **Projet**.

Sélectionnez le modèle de projet **Application web ASP.NET Core (.NET Core)**. Nommez le projet `TodoApi` et sélectionnez **OK**.

![Boîte de dialogue Nouveau projet](first-web-api/_static/new-project.png)

Dans la boîte de dialogue **Nouvelle application web ASP.NET Core - TodoApi**, sélectionnez le modèle **API web**. Sélectionnez **OK**. Ne sélectionnez **pas** **Activer la prise en charge de Docker**.

![Boîte de dialogue Nouvelle application web ASP.NET avec modèle de projet API web sélectionné parmi les modèles ASP.NET Core](first-web-api/_static/web-api-project.png)

### <a name="launch-the-app"></a>Lancer l’application

Dans Visual Studio, appuyez sur Ctrl+F5 pour lancer l’application. Visual Studio lance un navigateur et accède à `http://localhost:port/api/values`, où *port* est un numéro de port choisi au hasard. Chrome, Microsoft Edge et Firefox affichent la sortie suivante :

```
["value1","value2"]
```

### <a name="add-a-model-class"></a>Ajouter une classe de modèle

Un modèle est un objet qui représente les données de l’application. Dans le cas présent, le seul modèle est une tâche.

Ajoutez un dossier nommé « Models ». Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet. Sélectionnez **Ajouter** > **Nouveau dossier**. Nommez le dossier *Models*.

Remarque : Les classes de modèles vont n’importe où dans le projet. Le dossier *Modèles* est utilisé par convention pour les classes de modèles.

Ajoutez une classe `TodoItem`. Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**. Nommez la classe `TodoItem` et sélectionnez **Ajouter**.

Ajoutez le code suivant à la classe `TodoItem` :

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

La base de données génère la valeur `Id` quand un `TodoItem` est créé.

### <a name="create-the-database-context"></a>Créer le contexte de base de données

Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données spécifié. Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.

Ajoutez une classe `TodoContext`. Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**. Nommez la classe `TodoContext` et sélectionnez **Ajouter**.

Remplacez la classe par le code suivant :

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a>Ajouter un contrôleur

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier *Contrôleurs*. Sélectionnez **Ajouter** > **Nouvel élément**. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez le modèle **Classe de contrôleur des API web**. Nommez la classe `TodoController`.

![Boîte de dialogue Ajouter un nouvel élément avec contrôleur dans la zone de recherche et le contrôleur des API web sélectionné](first-web-api/_static/new_controller.png)

Remplacez la classe par le code suivant :

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a>Lancer l’application

Dans Visual Studio, appuyez sur Ctrl+F5 pour lancer l’application. Visual Studio lance un navigateur et accède à `http://localhost:port/api/values`, où *port* est un numéro de port choisi au hasard. Accédez au contrôleur `Todo` à l’adresse `http://localhost:port/api/todo`.

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]


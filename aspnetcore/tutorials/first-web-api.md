---
title: "Créer une API web avec ASP.NET Core et Visual Studio pour Windows"
author: rick-anderson
description: "Générer une API web avec ASP.NET Core MVC et Visual Studio pour Windows"
keywords: ASP.NET Core, APIweb, API web, REST, HTTP, Service, Service HTTP
ms.author: riande
manager: wpickett
ms.date: 08/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-web-api
ms.openlocfilehash: 617b11cd7652e393c06446c62138802e4a4e90df
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2017
---
#<a name="create-a-web-api-with-aspnet-core-and-visual-studio-for-windows"></a>Créer une API web avec ASP.NET Core et Visual Studio pour Windows

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Mike Wasson](https://github.com/mikewasson)

Dans ce didacticiel, vous allez générer une API web pour la gestion d’une liste de tâches. Vous ne générerez pas d’interface utilisateur.

Il existe trois versions de ce didacticiel :

* Windows : API web avec Visual Studio pour Windows (ce didacticiel)
* macOS : [API web avec Visual Studio pour Mac](xref:tutorials/first-web-api-mac)
* macOS, Linux, Windows : [API web avec Visual Studio Code](xref:tutorials/web-api-vsc)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE[install 2.0](../includes/install2.0.md)]

Consultez [ce fichier PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) pour la version ASP.NET Core 1.1.

## <a name="create-the-project"></a>Créer le projet

Dans Visual Studio, sélectionnez **Fichier** Menu > **Nouveau** > **Projet**.

Sélectionnez le modèle de projet **Application web ASP.NET Core (.NET Core)**. Nommez le projet `TodoApi` et sélectionnez **OK**.

![Boîte de dialogue Nouveau projet](first-web-api/_static/new-project.png)

Dans la boîte de dialogue **Nouvelle application web ASP.NET Core - TodoApi**, sélectionnez le modèle **API web**. Sélectionnez **OK**. Ne sélectionnez **pas** **Activer la prise en charge de Docker**.

![Boîte de dialogue Nouvelle application web ASP.NET avec modèle de projet API web sélectionné parmi les modèles ASP.NET Core](first-web-api/_static/web-api-project.png)

### <a name="launch-the-app"></a>Lancer l’application

Dans Visual Studio, appuyez sur Ctrl+F5 pour lancer l’application. Visual Studio lance un navigateur et accède à `http://localhost:port/api/values`, où *port* est un numéro de port choisi au hasard. Chrome, Edge et Firefox affichent ce qui suit :

```
["value1","value2"]
``` 

### <a name="add-a-model-class"></a>Ajouter une classe de modèle

Un modèle est un objet qui représente les données dans votre application. Ici, le seul modèle est une tâche à effectuer.

Ajoutez un dossier nommé « Models ». Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet. Sélectionnez **Ajouter** > **Nouveau dossier**. Nommez le dossier *Models*.

Remarque : Vous pouvez placer des classes de modèle n’importe où dans votre projet, mais le dossier *Models* est utilisé par convention.

Ajoutez une classe `TodoItem`. Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**. Nommez la classe `TodoItem` et sélectionnez **Ajouter**.

Remplacez le code généré par ce qui suit :

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

La base de données génère le `Id` quand un `TodoItem` est créé.

### <a name="create-the-database-context"></a>Créer le contexte de base de données

Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données spécifié. Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.

Ajoutez une classe `TodoContext`. Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**. Nommez la classe `TodoContext` et sélectionnez **Ajouter**.

Remplacez le code généré par ce qui suit :

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a>Ajouter un contrôleur

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier *Contrôleurs*. Sélectionnez **Ajouter** > **Nouvel élément**. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez le modèle **Classe de contrôleur des API web**. Nommez la classe `TodoController`.

![Boîte de dialogue Ajouter un nouvel élément avec contrôleur dans la zone de recherche et le contrôleur des API web sélectionné](first-web-api/_static/new_controller.png)

Remplacez le code généré par ce qui suit :

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]
  
### <a name="launch-the-app"></a>Lancer l’application

Dans Visual Studio, appuyez sur Ctrl+F5 pour lancer l’application. Visual Studio lance un navigateur et accède à `http://localhost:port/api/values`, où *port* est un numéro de port choisi au hasard. Si vous utilisez Chrome, Edge ou Firefox, les données sont affichées. Si vous utilisez Internet Explorer, ce dernier vous invite à ouvrir ou à enregistrer le fichier *values.json*. Accédez au contrôleur `Todo` que nous venons de créer `http://localhost:port/api/todo`.

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]


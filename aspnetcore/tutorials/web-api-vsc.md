---
title: "Créer une API web avec ASP.NET Core et VS Code"
description: "Générer une API web sur macOS, Linux ou Windows avec ASP.NET Core MVC et Visual Studio Code"
author: rick-anderson
ms.author: riande
ms.date: 09/22/2017
ms.topic: get-started-article
ms.prod: asp.net-core
ms.technology: aspnet
manager: wpickett
uid: tutorials/web-api-vsc
ms.openlocfilehash: 9fec8904cf05fc486160c0641731c6336fe2766a
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="create-a-web-api-with-aspnet-core-mvc-and-visual-studio-code-on-linux-macos-and-windows"></a>Créer une API web avec ASP.NET Core MVC et Visual Studio Code sur Linux, macOS et Windows

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Mike Wasson](https://github.com/mikewasson)

Dans ce didacticiel, vous allez générer une API web pour la gestion d’une liste de tâches. Vous ne générerez pas d’interface utilisateur.

Il existe trois versions de ce didacticiel :

* macOS, Linux, Windows : API web avec Visual Studio Code (le présent didacticiel)
* macOS : [API web avec Visual Studio pour Mac](xref:tutorials/first-web-api-mac)
* Windows : [API web avec Visual Studio pour Windows](xref:tutorials/first-web-api)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[template files](../includes/webApi/intro.md)]

## <a name="set-up-your-development-environment"></a>Configurer votre environnement de développement

Téléchargez et installez :
- [SDK .NET Core 2.0.0](https://www.microsoft.com/net/core) ou version ultérieure
- [Visual Studio Code](https://code.visualstudio.com)
- [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) Visual Studio Code

## <a name="create-the-project"></a>Créer le projet

À partir d’une console, exécutez les commandes suivantes :

```console
mkdir TodoApi
cd TodoApi
dotnet new webapi
```

Ouvrez le dossier *TodoApi* dans Visual Studio Code (VS Code), puis sélectionnez le fichier *Startup.cs*.

- Sélectionnez **Oui** quand une boîte de dialogue **Avertissement** s’affiche en indiquant le message suivant : « Les composants nécessaires à la build et au débogage sont manquants dans 'TodoApi'. Faut-il les ajouter ? »
- Sélectionnez **Restaurer** quand une boîte de dialogue **Informations** s’affiche en indiquant qu’il existe des dépendances non résolues.

<!-- uid: tutorials/first-mvc-app-xplat/start-mvc uses the pic below. If you change it, make sure it's consistent -->

![VS Code avec le message d’avertissement indiquant « Les composants nécessaires à la build et au débogage sont manquants dans 'TodoApi'. Faut-il les ajouter ?  » Ne plus demander, Pas maintenant, Oui](web-api-vsc/_static/vsc_restore.png)

Appuyez sur **Déboguer** (F5) pour générer et exécuter le programme. Dans un navigateur, accédez à http://localhost:5000/api/values. Le résultat suivant s’affiche :

`["value1","value2"]`

Pour obtenir des conseils sur l’utilisation de VS Code, consultez [Aide de Visual Studio Code](#visual-studio-code-help).

## <a name="add-support-for-entity-framework-core"></a>Ajouter la prise en charge d’Entity Framework Core

La création d’un projet dans .NET Core 2.0 ajoute le fournisseur 'Microsoft.AspNetCore.All' au fichier *TodoApi.csproj*. Il est inutile d’installer le fournisseur de base de données [Entity Framework Core InMemory](https://docs.microsoft.com/ef/core/providers/in-memory/) séparément. Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec une base de données en mémoire.

[!code-xml[Main](web-api-vsc/sample/TodoApi/TodoApi.csproj?highlight=12)]

## <a name="add-a-model-class"></a>Ajouter une classe de modèle

Un modèle est un objet qui représente les données dans votre application. Dans le cas présent, le seul modèle est une tâche.

Ajoutez un dossier nommé *Models*. Vous pouvez placer des classes de modèle n’importe où dans votre projet, mais le dossier *Models* est utilisé par convention.

Ajouter une classe `TodoItem` avec le code suivant :

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

La base de données génère la valeur `Id` quand un `TodoItem` est créé.

## <a name="create-the-database-context"></a>Créer le contexte de base de données

Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données spécifié. Vous créez cette classe en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.

Ajoutez une classe `TodoContext` dans le dossier *Models* :

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

## <a name="add-a-controller"></a>Ajouter un contrôleur

Dans le dossier *Contrôleurs*, créez une classe nommée `TodoController`. Ajoutez le code suivant :

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a>Lancer l’application

Dans VS Code, appuyez sur F5 pour lancer l’application. Accédez à http://localhost:5000/api/todo (le contrôleur `Todo` que nous venons de créer).

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

## <a name="visual-studio-code-help"></a>Aide de Visual Studio Code

- [Prise en main](https://code.visualstudio.com/docs)
- [Débogage](https://code.visualstudio.com/docs/editor/debugging)
- [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal)
- [Raccourcis clavier](https://code.visualstudio.com/docs/getstarted/keybindings#_keyboard-shortcuts-reference)

  - [Raccourcis clavier Mac](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)
  - [Raccourcis clavier Linux](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-linux.pdf)
  - [Raccourcis clavier Windows](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)

[!INCLUDE[next steps](../includes/webApi/next.md)]



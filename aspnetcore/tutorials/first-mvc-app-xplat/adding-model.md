---
title: "Ajout d’un modèle dans une application ASP.NET Core MVC."
author: rick-anderson
description: "Ajoutez un modèle à une application ASP.NET Core simple."
ms.author: riande
ms.date: 09/18/2017
ms.topic: get-started-article
ms.technology: aspnet
keywords: ASP.NET Core, APIweb, API web, REST, Mac, Linux, HTTP, Service, Service HTTP, VS Code
ms.prod: asp.net-core
manager: wpickett
ms.assetid: 8dc28498-eeee-4666-b903-b593059e9f39
uid: tutorials/first-mvc-app-xplat/adding-model
ms.openlocfilehash: 70aa344ca4ceafacf53907c925fd595e47104d7e
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
[!INCLUDE[adding-model1](../../includes/mvc-intro/adding-model1.md)]

* Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.
* Ajoutez le code suivant au fichier *Models/Movie.cs* :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]

Le champ `ID` est nécessaire à la base de données pour la clé primaire. 

Générez l’application pour vérifier que vous n’avez aucune erreur, et que vous avez enfin ajouté un **M**odèle à votre application **M**VC.

## <a name="prepare-the-project-for-scaffolding"></a>Préparer le projet pour la génération de modèles automatique

- Ajoutez les packages NuGet en surbrillance suivants au fichier *MvcMovie.csproj* :
             
   [!code-csharp[Main](start-mvc/sample/MvcMovie/MvcMovie.csproj?highlight=7,10)]

- Enregistrez le fichier et sélectionnez **Restaurer** quand une boîte de dialogue **Informations** s’affiche en indiquant qu’il existe des dépendances non résolues.
- Créez un fichier *Models/MvcMovieContext.cs*, puis ajoutez la classe `MvcMovieContext` suivante :

   [!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]
   
- Ouvrez le fichier *Startup.cs*, puis ajoutez deux instructions using :

   [!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]

- Ajoutez le contexte de base de données au fichier *Startup.cs* :

   [!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-7)]

  Cela permet d’indiquer à Entity Framework quelles sont les classes de modèles incluses dans le modèle de données. Vous définissez un *jeu d’entités* d’objets Movies, lesquels sont représentés dans la base de données sous forme de table Movie.

- Générez le projet pour vérifier qu’il n’existe aucune erreur.

## <a name="scaffold-the-moviecontroller"></a>Effectuer une génération de modèles automatique pour MovieController

Ouvrez une fenêtre de terminal dans le dossier de projet, puis exécutez les commandes suivantes :

```
dotnet restore
dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries 
```

> [!NOTE]
> Si vous obtenez une erreur au moment de l’exécution de la commande de génération de modèles automatique, consultez les informations relatives au [problème 444 dans le dépôt de génération de modèles automatique](https://github.com/aspnet/scaffolding/issues/444) pour trouver une solution de contournement.

Le moteur de génération de modèles automatique crée les éléments suivants :

* contrôleur de films (*Controllers/MoviesController.cs*) ;
* fichiers de vues Razor pour les pages Create, Delete, Details, Edit et Index (*Views/Movies/\*.cshtml*).

La création automatique de méthodes d’action et de vues [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (créer, lire, mettre à jour et supprimer) s’appelle la *génération de modèles automatique*. Vous aurez bientôt une application web entièrement opérationnelle qui vous permettra de gérer une base de données de films.

[!INCLUDE[adding-model 2x](../../includes/mvc-intro/adding-model2xp.md)]

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model3.md)]

Vous disposez maintenant d’une base de données et de pages pour afficher, modifier, mettre à jour et supprimer les données. Dans le prochain didacticiel, nous allons utiliser la base de données.

### <a name="additional-resources"></a>Ressources supplémentaires

* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
* [Globalisation et localisation](xref:fundamentals/localization)

>[!div class="step-by-step"]
[Précédent - Ajouter une vue](adding-view.md)
[Suivant - Utilisation de SQLite](working-with-sql.md)

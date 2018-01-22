---
title: "Ajouter un modèle dans une application ASP.NET Core MVC"
author: rick-anderson
description: "Ajoutez un modèle à une application ASP.NET Core simple."
ms.author: riande
manager: wpickett
ms.devlang: csharp
ms.date: 09/22/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: .net-core
uid: tutorials/first-mvc-app-mac/adding-model
ms.openlocfilehash: 3a7db5e435fe72150feb1e0ec905b6f6adc16f2c
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model1.md)]

* Cliquez avec le bouton droit sur le dossier *Models*, puis sélectionnez **Ajouter** > **Nouveau fichier**. 
* Dans la boîte de dialogue **Nouveau fichier** :

  * Dans le volet gauche, sélectionnez **Général**.
  * Dans le volet central, sélectionnez **Classe vide**.
  * Nommez la classe **Movie**, puis sélectionnez **Nouveau**.

Ajoutez les propriétés suivantes à la classe `Movie` :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]

Le champ `ID` est nécessaire à la base de données pour la clé primaire.

Générez le projet pour vérifier qu’il ne comporte aucune erreur. Vous avez désormais un **M**odèle dans votre application **M**VC.

## <a name="prepare-the-project-for-scaffolding"></a>Préparer le projet pour la génération de modèles automatique

- Cliquez avec le bouton droit sur le fichier projet, puis sélectionnez **Outils > Modifier le fichier**.

  ![affichage de l’étape ci-dessus](adding-model/_static/1.png)

- Ajoutez les packages NuGet en surbrillance suivants au fichier *MvcMovie.csproj* :
             
  [!code-csharp[Main](../first-mvc-app-xplat/start-mvc/sample/MvcMovie/MvcMovie.csproj?highlight=7,10)]

- Enregistrez le fichier.

- Créez un fichier *Models/MvcMovieContext.cs*, puis ajoutez la classe `MvcMovieContext` suivante : [!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]
   
- Ouvrez le fichier *Startup.cs*, puis ajoutez deux instructions using : [!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]

- Ajoutez le contexte de base de données au fichier *Startup.cs* :

   [!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-7)]

  Cela permet d’indiquer à Entity Framework quelles sont les classes de modèles incluses dans le modèle de données. Vous définissez un *jeu d’entités* d’objets Movies, lesquels sont représentés dans la base de données sous forme de table Movie.

- Générez le projet pour vérifier qu’il n’existe aucune erreur.

## <a name="scaffold-the-moviecontroller"></a>Effectuer une génération de modèles automatique pour MovieController

Ouvrez une fenêtre de terminal dans le dossier de projet, puis exécutez les commandes suivantes :

```
dotnet restore
dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries 
```
Si vous obtenez l’erreur `No executable found matching command "dotnet-aspnet-codegenerator", verify` :

 * Vous êtes dans le répertoire du projet. Le répertoire du projet contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*.
 * Votre version de dotnet est la 1.1 ou une version ultérieure. Exécutez `dotnet` pour obtenir la version souhaitée.
 * Vous avez ajouté l’élément `<DotNetCliToolReference>` au [fichier MvcMovie.csproj](#prepare-the-project-for-scaffolding).
 
<!--
> [!NOTE]
> If you get an error when the scaffolding command runs, see [issue 444 in the scaffolding repository](https://github.com/aspnet/scaffolding/issues/444) for a workaround.
-->

Le moteur de génération de modèles automatique crée les éléments suivants :

* contrôleur de films (*Controllers/MoviesController.cs*) ;
* fichiers de vues Razor pour les pages Create, Delete, Details, Edit et Index (*Views/Movies/\*.cshtml*).

La création automatique de méthodes d’action et de vues [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (créer, lire, mettre à jour et supprimer) s’appelle la *génération de modèles automatique*. Vous aurez bientôt une application web entièrement opérationnelle qui vous permettra de gérer une base de données de films.

### <a name="add-the-files-to-visual-studio"></a>Ajouter les fichiers à Visual Studio

* Ajoutez le fichier *MovieController.cs* au projet Visual Studio :

  * Cliquez avec le bouton droit sur le dossier *Controllers*, puis sélectionnez **Ajouter > Ajouter des fichiers**.
  * Sélectionnez le fichier *MovieController.cs*.

* Ajoutez le dossier *Movies* et les vues :

  * Cliquez avec le bouton droit sur le dossier *Views*, puis sélectionnez **Ajouter > Ajouter un dossier existant**.
  * Accédez au dossier *Views*, sélectionnez *Views\Movies*, puis **Ouvrir**.
  * Dans la boîte de dialogue **Sélectionnez les fichiers à ajouter à partir de Movies**, sélectionnez **Inclure tout**, puis **OK**.

[!INCLUDE[adding-model 2x](../../includes/mvc-intro/adding-model2xp.md)]

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model3.md)]

Vous disposez maintenant d’une base de données et de pages pour afficher, modifier, mettre à jour et supprimer les données. Dans le prochain didacticiel, nous allons utiliser la base de données.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
* [Globalisation et localisation](xref:fundamentals/localization)

>[!div class="step-by-step"]
[Précédent : Ajout d’une vue](adding-view.md)
[Suivant : Utilisation de SQL](working-with-sql.md)  

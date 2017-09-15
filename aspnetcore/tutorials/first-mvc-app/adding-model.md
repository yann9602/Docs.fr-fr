---
title: "Ajout d’un modèle à une application ASP.NET Core MVC"
author: rick-anderson
description: "Ajoutez un modèle à une application ASP.NET Core simple."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 03/30/2017
ms.topic: get-started-article
ms.assetid: 8dc28498-00ee-4d66-b903-b593059e9f39
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 8d0bebc22e1cfdc6d9b213d0c3159a7dab988020
ms.sourcegitcommit: 0bd3f6ec577c648dd777877e97572ec2da1b36c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2017
---
[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model1.md)]

Remarque : Les modèles ASP.NET Core 2.0 contiennent le dossier *Models*.

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **MvcMovie** > **Ajouter** > **Nouveau dossier**. Nommez le dossier *Models*.

Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**. Nommez la classe **Movie**, puis ajoutez les propriétés suivantes :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]

Le champ `ID` est nécessaire à la base de données pour la clé primaire. 

Générez le projet pour vérifier qu’il ne comporte aucune erreur. Vous avez désormais un **M**odèle dans votre application **M**VC.

## <a name="scaffolding-a-controller"></a>Génération de modèles automatique pour un contrôleur

Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs* **> Ajouter > Contrôleur**.

![affichage de l’étape ci-dessus](adding-model/_static/add_controller.png)

Dans la boîte de dialogue **Ajouter des dépendances MVC**, sélectionnez **Dépendances minimales**, puis **Ajouter**.

![affichage de l’étape ci-dessus](adding-model/_static/add_depend.png)

Visual Studio ajoute les dépendances nécessaires pour générer automatiquement un modèle pour un contrôleur, mais le contrôleur proprement dit n’est pas créé. L’appel suivant de **> Ajouter > Contrôleur** crée le contrôleur. 

Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs* **> Ajouter > Contrôleur**.

![affichage de l’étape ci-dessus](adding-model/_static/add_controller.png)

Dans la boîte de dialogue **Ajouter un modèle automatique**, appuyez sur **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold2.png)

Renseignez la boîte de dialogue **Ajouter un contrôleur** :

* **Classe de modèle :** *Movie (MvcMovie.Models)*
* **Classe de contexte de données :** sélectionnez l’icône **+** et ajoutez le **MvcMovie.Models.MvcMovieContext** par défaut.

![Ajouter un contexte de données](adding-model/_static/dc.png)

* **Affichages :** conservez la valeur par défaut de chaque option activée.
* **Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.
* Appuyez sur **Ajouter**.

![Boîte de dialogue Ajouter un contrôleur](adding-model/_static/add_controller2.png)

Visual Studio crée :

* Une [classe de contexte de base de données](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext.cs*)
* Un contrôleur de films (*Controllers/MoviesController.cs*)
* Des fichiers de vues Razor pour les pages Create, Delete, Details, Edit et Index (*Views/Movies/&ast;.cshtml*)

La création automatique du contexte de base de données et de méthodes d’action et de vues [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) (créer, lire, mettre à jour et supprimer) porte le nom de *génération de modèles automatique*. Vous aurez bientôt une application web entièrement opérationnelle qui vous permettra de gérer une base de données de films.

Si vous exécutez l’application et que vous cliquez sur le lien **Mvc Movie**, vous recevez une erreur semblable à la suivante :

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString 
```

Vous devez créer la base de données, et vous utiliserez pour cela la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core. Les migrations permettent de créer une base de données qui correspond à votre modèle de données, et de mettre à jour le schéma de base de données quand votre modèle de données change.

## <a name="add-ef-tooling-and-perform-initial-migration"></a>Ajouter les outils EF et effectuer la migration initiale

Dans cette section, vous allez utiliser la console du Gestionnaire de package pour :

* Ajouter le package d’outils Entity Framework Core. Ce package est nécessaire pour ajouter des migrations et mettre à jour la base de données
* Ajouter une migration initiale
* Mettre à jour la base de données avec la migration initiale

Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.

<!-- following image shared with uid: tutorials/razor-pages/model -->
  ![Menu de la console du gestionnaire de package](adding-model/_static/pmc.png)

Dans la console du Gestionnaire de package, entrez les commandes suivantes :

``` PMC
Install-Package Microsoft.EntityFrameworkCore.Tools
Add-Migration Initial
Update-Database
```

Remarque : Consultez [l’approche CLI](#cli) si vous rencontrez des problèmes avec la console du Gestionnaire de package.

La commande `Add-Migration` crée le code nécessaire à la création du schéma de base de données initial. Le schéma est basé sur le modèle spécifié dans le fichier `DbContext` (dans *Data/MvcMovieContext.cs*). L’argument `Initial` est utilisé pour nommer les migrations. Vous pouvez utiliser n’importe quel nom, mais par convention, choisissez un nom qui décrit la migration. Pour plus d’informations, consultez [Présentation des migrations](xref:data/ef-mvc/migrations#introduction-to-migrations).

La commande `Update-Database` exécute la méthode `Up` dans le fichier *Migrations/\<horodatage>_InitialCreate.cs*, ce qui entraîne la création de la base de données.

<a name="cli"></a> Vous pouvez effectuer les étapes qui précèdent à l’aide de l’interface de ligne de commande (CLI) plutôt que la console du Gestionnaire de package :

* Ajoutez les [outils EF Core](xref:data/ef-mvc/migrations#entity-framework-core-nuget-packages-for-migrations) au fichier *.csproj*.
* Exécutez les commandes suivantes à partir de la console (dans le répertoire du projet) :

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```     
  

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model3.md)]

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model4.md)]

![Menu contextuel d’IntelliSense sur un élément de modèle qui répertorie les propriétés disponibles pour ID, Price, Release Date et Title.](adding-model/_static/ints.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
* [Globalisation et localisation](xref:fundamentals/localization)

>[!div class="step-by-step"]
[Précédent : Ajout d’une vue](adding-view.md)
[Suivant : Utilisation de SQL](working-with-sql.md)  

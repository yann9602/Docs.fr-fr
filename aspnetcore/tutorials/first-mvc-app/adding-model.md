---
title: "Ajout d’un modèle à une application ASP.NET Core MVC"
author: rick-anderson
description: "Ajoutez un modèle à une application ASP.NET Core simple."
manager: wpickett
ms.author: riande
ms.date: 12/8/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 1819aff0e6ae68ad3c609466e52fcb6510fe1dcd
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model1.md)]

Remarque : Les modèles ASP.NET Core 2.0 contiennent le dossier *Models*.

Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**. Nommez la classe **Movie**, puis ajoutez les propriétés suivantes :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]

Le champ `ID` est nécessaire à la base de données pour la clé primaire. 

Générez le projet pour vérifier qu’il ne comporte aucune erreur. Vous avez désormais un **M**odèle dans votre application **M**VC.

## <a name="scaffolding-a-controller"></a>Génération de modèles automatique pour un contrôleur

Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs* **> Ajouter > Contrôleur**.

![affichage de l’étape ci-dessus](adding-model/_static/add_controller.png)

Si la boîte de dialogue **Ajouter des dépendances MVC** apparaît :

* [Effectuez la mise à jour de Visual Studio vers la dernière version](https://www.visualstudio.com/downloads/). Les versions de Visual Studio antérieures à 15.5 affichent cette boîte de dialogue.
* Si vous ne pouvez pas effectuer la mise à jour, sélectionnez **ADD**, puis suivez à nouveau les étapes pour ajouter un contrôleur.

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

La création automatique du contexte de base de données et de méthodes d’action et de vues [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (créer, lire, mettre à jour et supprimer) porte le nom de *génération de modèles automatique*. Vous aurez bientôt une application web entièrement opérationnelle qui vous permettra de gérer une base de données de films.

Si vous exécutez l’application et que vous cliquez sur le lien **Mvc Movie**, vous recevez une erreur semblable à la suivante :

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
* Mettez à jour la base de données avec la migration initiale.

Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.

<!-- following image shared with uid: tutorials/razor-pages/model -->
  ![Menu Console du Gestionnaire de package](adding-model/_static/pmc.png)

Dans la console du Gestionnaire de package, entrez les commandes suivantes :

``` PMC
Install-Package Microsoft.EntityFrameworkCore.Tools
Add-Migration Initial
Update-Database
```

**Remarque :** Si vous recevez une erreur avec la commande `Install-Package`, ouvrez le Gestionnaire de Package NuGet et recherchez le package `Microsoft.EntityFrameworkCore.Tools`. Ceci vous permet d’installer le package ou de vérifier s’il est déjà installé. Vous pouvez aussi appliquer [l’approche CLI](#cli) si vous rencontrez des problèmes avec la console du Gestionnaire de package.

La commande `Add-Migration` crée le code nécessaire à la création du schéma de base de données initial. Le schéma est basé sur le modèle spécifié dans le fichier `DbContext` (dans *Data/MvcMovieContext.cs*). L’argument `Initial` est utilisé pour nommer les migrations. Vous pouvez utiliser n’importe quel nom, mais par convention, choisissez un nom qui décrit la migration. Pour plus d’informations, consultez [Présentation des migrations](xref:data/ef-mvc/migrations#introduction-to-migrations).

La commande `Update-Database` exécute la méthode `Up` dans le fichier *Migrations/\<date et heure>_Initial.cs*, ce qui entraîne la création de la base de données.

<a name="cli"></a> Vous pouvez effectuer les étapes qui précèdent à l’aide de l’interface de ligne de commande (CLI) plutôt que la console du Gestionnaire de package :

* Ajoutez les [outils EF Core](xref:data/ef-mvc/migrations#entity-framework-core-nuget-packages-for-migrations) au fichier *.csproj*.
* Exécutez les commandes suivantes à partir de la console (dans le répertoire du projet) :

  ```console
  dotnet ef migrations add Initial
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

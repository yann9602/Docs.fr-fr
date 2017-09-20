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
ms.openlocfilehash: 7469546494ec54bfe36bc5bd2f5f9702889ddf4a
ms.sourcegitcommit: 2e61e287e220eddd5f3f4cd9147aa6417cfd9236
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model1.md)]

<span data-ttu-id="bb7d2-104">Remarque : Les modèles ASP.NET Core 2.0 contiennent le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-104">Note: The ASP.NET Core 2.0 templates contain the *Models* folder.</span></span>

<span data-ttu-id="bb7d2-105">Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **MvcMovie** > **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-105">In Solution Explorer, right click the **MvcMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="bb7d2-106">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-106">Name the folder *Models*.</span></span>

<span data-ttu-id="bb7d2-107">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-107">Right click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="bb7d2-108">Nommez la classe **Movie**, puis ajoutez les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="bb7d2-108">Name the class **Movie** and add the following properties:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]

<span data-ttu-id="bb7d2-109">Le champ `ID` est nécessaire à la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-109">The `ID` field is required by the database for the primary key.</span></span> 

<span data-ttu-id="bb7d2-110">Générez le projet pour vérifier qu’il ne comporte aucune erreur.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-110">Build the project to verify you don't have any errors.</span></span> <span data-ttu-id="bb7d2-111">Vous avez désormais un **M**odèle dans votre application **M**VC.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-111">You now have a **M**odel in your **M**VC app.</span></span>

## <a name="scaffolding-a-controller"></a><span data-ttu-id="bb7d2-112">Génération de modèles automatique pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="bb7d2-112">Scaffolding a controller</span></span>

<span data-ttu-id="bb7d2-113">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs* **> Ajouter > Contrôleur**.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-113">In **Solution Explorer**, right-click the *Controllers* folder **> Add > Controller**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller.png)

<span data-ttu-id="bb7d2-115">Dans la boîte de dialogue **Ajouter des dépendances MVC**, sélectionnez **Dépendances minimales**, puis **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-115">In the **Add MVC Dependencies** dialog, select **Minimal Dependencies**, and select **Add**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_depend.png)

<span data-ttu-id="bb7d2-117">Visual Studio ajoute les dépendances nécessaires pour générer automatiquement un modèle pour un contrôleur, mais le contrôleur proprement dit n’est pas créé.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-117">Visual Studio adds the dependencies needed to scaffold a controller, but the controller itself is not created.</span></span> <span data-ttu-id="bb7d2-118">L’appel suivant de **> Ajouter > Contrôleur** crée le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-118">The next invoke of **> Add > Controller** creates the controller.</span></span> 

<span data-ttu-id="bb7d2-119">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs* **> Ajouter > Contrôleur**.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-119">In **Solution Explorer**, right-click the *Controllers* folder **> Add > Controller**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller.png)

<span data-ttu-id="bb7d2-121">Dans la boîte de dialogue **Ajouter un modèle automatique**, appuyez sur **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-121">In the **Add Scaffold** dialog, tap **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold2.png)

<span data-ttu-id="bb7d2-123">Renseignez la boîte de dialogue **Ajouter un contrôleur** :</span><span class="sxs-lookup"><span data-stu-id="bb7d2-123">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="bb7d2-124">**Classe de modèle :** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="bb7d2-124">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="bb7d2-125">**Classe de contexte de données :** sélectionnez l’icône **+** et ajoutez le **MvcMovie.Models.MvcMovieContext** par défaut.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-125">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Ajouter un contexte de données](adding-model/_static/dc.png)

* <span data-ttu-id="bb7d2-127">**Affichages :** conservez la valeur par défaut de chaque option activée.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-127">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="bb7d2-128">**Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-128">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="bb7d2-129">Appuyez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-129">Tap **Add**</span></span>

![Boîte de dialogue Ajouter un contrôleur](adding-model/_static/add_controller2.png)

<span data-ttu-id="bb7d2-131">Visual Studio crée :</span><span class="sxs-lookup"><span data-stu-id="bb7d2-131">Visual Studio creates:</span></span>

* <span data-ttu-id="bb7d2-132">Une [classe de contexte de base de données](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="bb7d2-132">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="bb7d2-133">Un contrôleur de films (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="bb7d2-133">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="bb7d2-134">Des fichiers de vues Razor pour les pages Create, Delete, Details, Edit et Index (*Views/Movies/&ast;.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="bb7d2-134">Razor view files for Create, Delete, Details, Edit and Index pages (*Views/Movies/&ast;.cshtml*)</span></span>

<span data-ttu-id="bb7d2-135">La création automatique du contexte de base de données et de méthodes d’action et de vues [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (créer, lire, mettre à jour et supprimer) porte le nom de *génération de modèles automatique*.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-135">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span> <span data-ttu-id="bb7d2-136">Vous aurez bientôt une application web entièrement opérationnelle qui vous permettra de gérer une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-136">You'll soon have a fully functional web application that lets you manage a movie database.</span></span>

<span data-ttu-id="bb7d2-137">Si vous exécutez l’application et que vous cliquez sur le lien **Mvc Movie**, vous recevez une erreur semblable à la suivante :</span><span class="sxs-lookup"><span data-stu-id="bb7d2-137">If you run the app and click on the **Mvc Movie** link, you'll get an error similar to the following:</span></span>

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString 
```

<span data-ttu-id="bb7d2-138">Vous devez créer la base de données, et vous utiliserez pour cela la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-138">You need to create the database, and you'll use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="bb7d2-139">Les migrations permettent de créer une base de données qui correspond à votre modèle de données, et de mettre à jour le schéma de base de données quand votre modèle de données change.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-139">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

## <a name="add-ef-tooling-and-perform-initial-migration"></a><span data-ttu-id="bb7d2-140">Ajouter les outils EF et effectuer la migration initiale</span><span class="sxs-lookup"><span data-stu-id="bb7d2-140">Add EF tooling and perform initial migration</span></span>

<span data-ttu-id="bb7d2-141">Dans cette section, vous allez utiliser la console du Gestionnaire de package pour :</span><span class="sxs-lookup"><span data-stu-id="bb7d2-141">In this section you'll use the Package Manager Console (PMC) to:</span></span>

* <span data-ttu-id="bb7d2-142">Ajouter le package d’outils Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-142">Add the Entity Framework Core Tools package.</span></span> <span data-ttu-id="bb7d2-143">Ce package est nécessaire pour ajouter des migrations et mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="bb7d2-143">This package is required to add migrations and update the database.</span></span>
* <span data-ttu-id="bb7d2-144">Ajouter une migration initiale</span><span class="sxs-lookup"><span data-stu-id="bb7d2-144">Add an initial migration.</span></span>
* <span data-ttu-id="bb7d2-145">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-145">Update the database with the initial migration.</span></span>

<span data-ttu-id="bb7d2-146">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-146">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

<!-- following image shared with uid: tutorials/razor-pages/model -->
  ![Menu Console du Gestionnaire de package](adding-model/_static/pmc.png)

<span data-ttu-id="bb7d2-148">Dans la console du Gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="bb7d2-148">In the PMC, enter the following commands:</span></span>

``` PMC
Install-Package Microsoft.EntityFrameworkCore.Tools
Add-Migration Initial
Update-Database
```

<span data-ttu-id="bb7d2-149">**Remarque :** Si vous recevez une erreur avec la commande `Install-Package`, ouvrez le Gestionnaire de Package NuGet et recherchez le package `Microsoft.EntityFrameworkCore.Tools`.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-149">**Note:** If you receive an error with the `Install-Package` command, open NuGet Package Manager and search for the `Microsoft.EntityFrameworkCore.Tools` package.</span></span> <span data-ttu-id="bb7d2-150">Ceci vous permet d’installer le package ou de vérifier s’il est déjà installé.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-150">This allows you to install the package or check if it is already installed.</span></span> <span data-ttu-id="bb7d2-151">Vous pouvez aussi appliquer [l’approche CLI](#cli) si vous rencontrez des problèmes avec la console du Gestionnaire de package.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-151">Alternatively, see the [CLI approach](#cli) if you have problems with the PMC.</span></span>

<span data-ttu-id="bb7d2-152">La commande `Add-Migration` crée le code nécessaire à la création du schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-152">The `Add-Migration` command creates code to create the initial database schema.</span></span> <span data-ttu-id="bb7d2-153">Le schéma est basé sur le modèle spécifié dans le fichier `DbContext` (dans *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="bb7d2-153">The schema is based on the model specified in the `DbContext`(In the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="bb7d2-154">L’argument `Initial` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-154">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="bb7d2-155">Vous pouvez utiliser n’importe quel nom, mais par convention, choisissez un nom qui décrit la migration.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-155">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="bb7d2-156">Pour plus d’informations, consultez [Présentation des migrations](xref:data/ef-mvc/migrations#introduction-to-migrations).</span><span class="sxs-lookup"><span data-stu-id="bb7d2-156">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="bb7d2-157">La commande `Update-Database` exécute la méthode `Up` dans le fichier *Migrations/\<horodatage>_InitialCreate.cs*, ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-157">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

<span data-ttu-id="bb7d2-158"><a name="cli"></a> Vous pouvez effectuer les étapes qui précèdent à l’aide de l’interface de ligne de commande (CLI) plutôt que la console du Gestionnaire de package :</span><span class="sxs-lookup"><span data-stu-id="bb7d2-158"><a name="cli"></a> You can perform the preceeding steps using the command-line interface (CLI) rather than the PMC:</span></span>

* <span data-ttu-id="bb7d2-159">Ajoutez les [outils EF Core](xref:data/ef-mvc/migrations#entity-framework-core-nuget-packages-for-migrations) au fichier *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="bb7d2-159">Add [EF Core tooling](xref:data/ef-mvc/migrations#entity-framework-core-nuget-packages-for-migrations) to the *.csproj* file.</span></span>
* <span data-ttu-id="bb7d2-160">Exécutez les commandes suivantes à partir de la console (dans le répertoire du projet) :</span><span class="sxs-lookup"><span data-stu-id="bb7d2-160">Run the following commands from the console (in the project directory):</span></span>

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```     
  

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model3.md)]

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model4.md)]

![Menu contextuel d’IntelliSense sur un élément de modèle qui répertorie les propriétés disponibles pour ID, Price, Release Date et Title.](adding-model/_static/ints.png)

## <a name="additional-resources"></a><span data-ttu-id="bb7d2-162">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="bb7d2-162">Additional resources</span></span>

* [<span data-ttu-id="bb7d2-163">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="bb7d2-163">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="bb7d2-164">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="bb7d2-164">Globalization and localization</span></span>](xref:fundamentals/localization)

>[!div class="step-by-step"]
<span data-ttu-id="bb7d2-165">[Précédent : Ajout d’une vue](adding-view.md)
[Suivant : Utilisation de SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="bb7d2-165">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>  

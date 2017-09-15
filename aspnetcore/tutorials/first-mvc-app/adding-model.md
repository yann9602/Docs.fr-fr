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

<span data-ttu-id="a5c3b-104">Remarque : Les modèles ASP.NET Core 2.0 contiennent le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-104">Note: The ASP.NET Core 2.0 templates contain the *Models* folder.</span></span>

<span data-ttu-id="a5c3b-105">Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **MvcMovie** > **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-105">In Solution Explorer, right click the **MvcMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="a5c3b-106">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-106">Name the folder *Models*.</span></span>

<span data-ttu-id="a5c3b-107">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-107">Right click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="a5c3b-108">Nommez la classe **Movie**, puis ajoutez les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="a5c3b-108">Name the class **Movie** and add the following properties:</span></span>

<span data-ttu-id="a5c3b-109">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="a5c3b-109">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]</span></span>

<span data-ttu-id="a5c3b-110">Le champ `ID` est nécessaire à la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-110">The `ID` field is required by the database for the primary key.</span></span> 

<span data-ttu-id="a5c3b-111">Générez le projet pour vérifier qu’il ne comporte aucune erreur.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-111">Build the project to verify you don't have any errors.</span></span> <span data-ttu-id="a5c3b-112">Vous avez désormais un **M**odèle dans votre application **M**VC.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-112">You now have a **M**odel in your **M**VC app.</span></span>

## <a name="scaffolding-a-controller"></a><span data-ttu-id="a5c3b-113">Génération de modèles automatique pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="a5c3b-113">Scaffolding a controller</span></span>

<span data-ttu-id="a5c3b-114">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs* **> Ajouter > Contrôleur**.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-114">In **Solution Explorer**, right-click the *Controllers* folder **> Add > Controller**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller.png)

<span data-ttu-id="a5c3b-116">Dans la boîte de dialogue **Ajouter des dépendances MVC**, sélectionnez **Dépendances minimales**, puis **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-116">In the **Add MVC Dependencies** dialog, select **Minimal Dependencies**, and select **Add**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_depend.png)

<span data-ttu-id="a5c3b-118">Visual Studio ajoute les dépendances nécessaires pour générer automatiquement un modèle pour un contrôleur, mais le contrôleur proprement dit n’est pas créé.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-118">Visual Studio adds the dependencies needed to scaffold a controller, but the controller itself is not created.</span></span> <span data-ttu-id="a5c3b-119">L’appel suivant de **> Ajouter > Contrôleur** crée le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-119">The next invoke of **> Add > Controller** creates the controller.</span></span> 

<span data-ttu-id="a5c3b-120">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs* **> Ajouter > Contrôleur**.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-120">In **Solution Explorer**, right-click the *Controllers* folder **> Add > Controller**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller.png)

<span data-ttu-id="a5c3b-122">Dans la boîte de dialogue **Ajouter un modèle automatique**, appuyez sur **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-122">In the **Add Scaffold** dialog, tap **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold2.png)

<span data-ttu-id="a5c3b-124">Renseignez la boîte de dialogue **Ajouter un contrôleur** :</span><span class="sxs-lookup"><span data-stu-id="a5c3b-124">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="a5c3b-125">**Classe de modèle :** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="a5c3b-125">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="a5c3b-126">**Classe de contexte de données :** sélectionnez l’icône **+** et ajoutez le **MvcMovie.Models.MvcMovieContext** par défaut.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-126">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Ajouter un contexte de données](adding-model/_static/dc.png)

* <span data-ttu-id="a5c3b-128">**Affichages :** conservez la valeur par défaut de chaque option activée.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-128">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="a5c3b-129">**Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-129">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="a5c3b-130">Appuyez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-130">Tap **Add**</span></span>

![Boîte de dialogue Ajouter un contrôleur](adding-model/_static/add_controller2.png)

<span data-ttu-id="a5c3b-132">Visual Studio crée :</span><span class="sxs-lookup"><span data-stu-id="a5c3b-132">Visual Studio creates:</span></span>

* <span data-ttu-id="a5c3b-133">Une [classe de contexte de base de données](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="a5c3b-133">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="a5c3b-134">Un contrôleur de films (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="a5c3b-134">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="a5c3b-135">Des fichiers de vues Razor pour les pages Create, Delete, Details, Edit et Index (*Views/Movies/&ast;.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="a5c3b-135">Razor view files for Create, Delete, Details, Edit and Index pages (*Views/Movies/&ast;.cshtml*)</span></span>

<span data-ttu-id="a5c3b-136">La création automatique du contexte de base de données et de méthodes d’action et de vues [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) (créer, lire, mettre à jour et supprimer) porte le nom de *génération de modèles automatique*.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-136">The automatic creation of the database context and [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span> <span data-ttu-id="a5c3b-137">Vous aurez bientôt une application web entièrement opérationnelle qui vous permettra de gérer une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-137">You'll soon have a fully functional web application that lets you manage a movie database.</span></span>

<span data-ttu-id="a5c3b-138">Si vous exécutez l’application et que vous cliquez sur le lien **Mvc Movie**, vous recevez une erreur semblable à la suivante :</span><span class="sxs-lookup"><span data-stu-id="a5c3b-138">If you run the app and click on the **Mvc Movie** link, you'll get an error similar to the following:</span></span>

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString 
```

<span data-ttu-id="a5c3b-139">Vous devez créer la base de données, et vous utiliserez pour cela la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-139">You need to create the database, and you'll use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="a5c3b-140">Les migrations permettent de créer une base de données qui correspond à votre modèle de données, et de mettre à jour le schéma de base de données quand votre modèle de données change.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-140">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

## <a name="add-ef-tooling-and-perform-initial-migration"></a><span data-ttu-id="a5c3b-141">Ajouter les outils EF et effectuer la migration initiale</span><span class="sxs-lookup"><span data-stu-id="a5c3b-141">Add EF tooling and perform initial migration</span></span>

<span data-ttu-id="a5c3b-142">Dans cette section, vous allez utiliser la console du Gestionnaire de package pour :</span><span class="sxs-lookup"><span data-stu-id="a5c3b-142">In this section you'll use the Package Manager Console (PMC) to:</span></span>

* <span data-ttu-id="a5c3b-143">Ajouter le package d’outils Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-143">Add the Entity Framework Core Tools package.</span></span> <span data-ttu-id="a5c3b-144">Ce package est nécessaire pour ajouter des migrations et mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="a5c3b-144">This package is required to add migrations and update the database.</span></span>
* <span data-ttu-id="a5c3b-145">Ajouter une migration initiale</span><span class="sxs-lookup"><span data-stu-id="a5c3b-145">Add an initial migration.</span></span>
* <span data-ttu-id="a5c3b-146">Mettre à jour la base de données avec la migration initiale</span><span class="sxs-lookup"><span data-stu-id="a5c3b-146">Update the database with the initial migration.</span></span>

<span data-ttu-id="a5c3b-147">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-147">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

<!-- following image shared with uid: tutorials/razor-pages/model -->
  ![Menu de la console du gestionnaire de package](adding-model/_static/pmc.png)

<span data-ttu-id="a5c3b-149">Dans la console du Gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a5c3b-149">In the PMC, enter the following commands:</span></span>

``` PMC
Install-Package Microsoft.EntityFrameworkCore.Tools
Add-Migration Initial
Update-Database
```

<span data-ttu-id="a5c3b-150">Remarque : Consultez [l’approche CLI](#cli) si vous rencontrez des problèmes avec la console du Gestionnaire de package.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-150">Note: See the [CLI approach](#cli) if you have problems with the PMC.</span></span>

<span data-ttu-id="a5c3b-151">La commande `Add-Migration` crée le code nécessaire à la création du schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-151">The `Add-Migration` command creates code to create the initial database schema.</span></span> <span data-ttu-id="a5c3b-152">Le schéma est basé sur le modèle spécifié dans le fichier `DbContext` (dans *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="a5c3b-152">The schema is based on the model specified in the `DbContext`(In the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="a5c3b-153">L’argument `Initial` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-153">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="a5c3b-154">Vous pouvez utiliser n’importe quel nom, mais par convention, choisissez un nom qui décrit la migration.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-154">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="a5c3b-155">Pour plus d’informations, consultez [Présentation des migrations](xref:data/ef-mvc/migrations#introduction-to-migrations).</span><span class="sxs-lookup"><span data-stu-id="a5c3b-155">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="a5c3b-156">La commande `Update-Database` exécute la méthode `Up` dans le fichier *Migrations/\<horodatage>_InitialCreate.cs*, ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-156">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

<span data-ttu-id="a5c3b-157"><a name="cli"></a> Vous pouvez effectuer les étapes qui précèdent à l’aide de l’interface de ligne de commande (CLI) plutôt que la console du Gestionnaire de package :</span><span class="sxs-lookup"><span data-stu-id="a5c3b-157"><a name="cli"></a> You can perform the preceeding steps using the command-line interface (CLI) rather than the PMC:</span></span>

* <span data-ttu-id="a5c3b-158">Ajoutez les [outils EF Core](xref:data/ef-mvc/migrations#entity-framework-core-nuget-packages-for-migrations) au fichier *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="a5c3b-158">Add [EF Core tooling](xref:data/ef-mvc/migrations#entity-framework-core-nuget-packages-for-migrations) to the *.csproj* file.</span></span>
* <span data-ttu-id="a5c3b-159">Exécutez les commandes suivantes à partir de la console (dans le répertoire du projet) :</span><span class="sxs-lookup"><span data-stu-id="a5c3b-159">Run the following commands from the console (in the project directory):</span></span>

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```     
  

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model3.md)]

<span data-ttu-id="a5c3b-160">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]</span><span class="sxs-lookup"><span data-stu-id="a5c3b-160">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]</span></span>

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model4.md)]

![Menu contextuel d’IntelliSense sur un élément de modèle qui répertorie les propriétés disponibles pour ID, Price, Release Date et Title.](adding-model/_static/ints.png)

## <a name="additional-resources"></a><span data-ttu-id="a5c3b-162">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a5c3b-162">Additional resources</span></span>

* [<span data-ttu-id="a5c3b-163">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="a5c3b-163">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="a5c3b-164">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="a5c3b-164">Globalization and localization</span></span>](xref:fundamentals/localization)

>[!div class="step-by-step"]
<span data-ttu-id="a5c3b-165">[Précédent : Ajout d’une vue](adding-view.md)
[Suivant : Utilisation de SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="a5c3b-165">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>  

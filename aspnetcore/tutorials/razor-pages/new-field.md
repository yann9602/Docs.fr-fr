---
title: "Ajout d’un nouveau champ à une page Razor"
author: rick-anderson
description: "Montre comment ajouter un nouveau champ à une page Razor avec Entity Framework Core"
keywords: ASP.NET Core, Entity Framework Core, migrations
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: b45642e39eb46631bb8034f4747222fb1bc0a83b
ms.sourcegitcommit: 8005eb4051e568d88ee58d48424f39916052e6e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2017
---
# <a name="adding-a-new-field-to-a-razor-page"></a><span data-ttu-id="8b189-104">Ajout d’un nouveau champ à une page Razor</span><span class="sxs-lookup"><span data-stu-id="8b189-104">Adding a new field to a Razor Page</span></span>

<span data-ttu-id="8b189-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8b189-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8b189-106">Dans cette section, vous allez utiliser la fonctionnalité Migrations Code First [d’Entity Framework](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) pour ajouter un nouveau champ au modèle et migrer ce changement dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8b189-106">In this section you'll use [Entity Framework](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) Code First Migrations to add a new field to the model and migrate that change to the database.</span></span>

<span data-ttu-id="8b189-107">Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First ajoute une table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.</span><span class="sxs-lookup"><span data-stu-id="8b189-107">When you use EF Code First to automatically create a database, Code First adds a table to the database to help track whether the schema of the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="8b189-108">S’ils ne sont pas synchronisés, EF lève une exception.</span><span class="sxs-lookup"><span data-stu-id="8b189-108">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="8b189-109">Cela facilite la recherche de problèmes d’incohérence de code/de bases de données.</span><span class="sxs-lookup"><span data-stu-id="8b189-109">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="8b189-110">Ajout d’une propriété Rating au modèle Movie</span><span class="sxs-lookup"><span data-stu-id="8b189-110">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="8b189-111">Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="8b189-111">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

<span data-ttu-id="8b189-112">Générez l’application (Ctrl+Maj+B).</span><span class="sxs-lookup"><span data-stu-id="8b189-112">Build the app (Ctrl+Shift+B).</span></span>

<span data-ttu-id="8b189-113">Modifiez *Pages/Movies/Index.cshtml*et ajoutez un champ `Rating` :</span><span class="sxs-lookup"><span data-stu-id="8b189-113">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="8b189-114">Ajoutez le champ `Rating` aux pages Delete et Details.</span><span class="sxs-lookup"><span data-stu-id="8b189-114">Add the `Rating` field to the Delete and Details pages.</span></span>

<span data-ttu-id="8b189-115">Mettez à jour *Create.cshtml* avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="8b189-115">Update *Create.cshtml* with a `Rating` field.</span></span> <span data-ttu-id="8b189-116">Vous pouvez copier/coller l’élément `<div>` précédent et laisser IntelliSense vous aider à mettre à jour les champs.</span><span class="sxs-lookup"><span data-stu-id="8b189-116">You can copy/paste the previous `<div>` element and let intelliSense help you update the fields.</span></span> <span data-ttu-id="8b189-117">IntelliSense fonctionne avec des [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="8b189-117">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Le développeur a tapé la lettre R comme valeur d’attribut asp-for dans le deuxième élément étiquette de la vue.](new-field/_static/cr.png)

<span data-ttu-id="8b189-121">Le code suivant montre *Create.cshtml* avec un champ `Rating` :</span><span class="sxs-lookup"><span data-stu-id="8b189-121">The following code shows *Create.cshtml* with a `Rating` field:</span></span>

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?highlight=36-40)]

<span data-ttu-id="8b189-122">Ajoutez le champ `Rating` à la Page Edit.</span><span class="sxs-lookup"><span data-stu-id="8b189-122">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="8b189-123">L’application ne fonctionne pas tant que la base de données n’est pas mise à jour pour inclure le nouveau champ.</span><span class="sxs-lookup"><span data-stu-id="8b189-123">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="8b189-124">Si vous l’exécutez à présent, l’application lève une `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="8b189-124">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="8b189-125">Cette erreur est due au fait que la classe du modèle Movie mise à jour est différente du schéma de la table Movie de la base de données.</span><span class="sxs-lookup"><span data-stu-id="8b189-125">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="8b189-126">(Il n’existe pas de colonne `Rating` dans la table de base de données.)</span><span class="sxs-lookup"><span data-stu-id="8b189-126">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="8b189-127">Plusieurs approches sont possibles pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="8b189-127">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="8b189-128">Laisser Entity Framework supprimer et recréer automatiquement la base de données à l’aide du nouveau schéma de classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="8b189-128">Have the Entity Framework automatically drop and re-create the database using  the new model class schema.</span></span> <span data-ttu-id="8b189-129">Cette approche est très utile au début du cycle de développement. Elle permet de faire évoluer rapidement le modèle et le schéma de base de données ensemble.</span><span class="sxs-lookup"><span data-stu-id="8b189-129">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="8b189-130">L’inconvénient est que vous perdez les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8b189-130">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="8b189-131">Il ne faut pas adopter cette approche sur une base de données de production.</span><span class="sxs-lookup"><span data-stu-id="8b189-131">You don't want to use this approach on a production database!</span></span> <span data-ttu-id="8b189-132">La suppression de la base de données lors des changements de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen efficace pour développer une application.</span><span class="sxs-lookup"><span data-stu-id="8b189-132">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="8b189-133">Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="8b189-133">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="8b189-134">L’avantage de cette approche est que vous conservez vos données.</span><span class="sxs-lookup"><span data-stu-id="8b189-134">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="8b189-135">Vous pouvez apporter cette modification manuellement ou en créant un script de modification de la base de données.</span><span class="sxs-lookup"><span data-stu-id="8b189-135">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="8b189-136">Utilisez les migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="8b189-136">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="8b189-137">Pour ce didacticiel, nous allons utiliser les migrations Code First.</span><span class="sxs-lookup"><span data-stu-id="8b189-137">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="8b189-138">Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="8b189-138">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="8b189-139">Vous pouvez voir un exemple de modification ci-dessous, mais elle doit être appliquée à chaque bloc `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="8b189-139">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="8b189-140">Consultez le [fichier SeedData.cs complet](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="8b189-140">See the [completed SeedData.cs file](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="8b189-141">Générez la solution.</span><span class="sxs-lookup"><span data-stu-id="8b189-141">Build the solution.</span></span>

<a name="pmc"></a>

<span data-ttu-id="8b189-142">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="8b189-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="8b189-143">Dans la console du Gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="8b189-143">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Rating
Update-Database
```

<span data-ttu-id="8b189-144">La commande `Add-Migration` indique au framework qu’il doit :</span><span class="sxs-lookup"><span data-stu-id="8b189-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="8b189-145">Comparer le modèle `Movie` au schéma de base de données `Movie`</span><span class="sxs-lookup"><span data-stu-id="8b189-145">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="8b189-146">Créer du code pour migrer le schéma de base de données vers le nouveau modèle</span><span class="sxs-lookup"><span data-stu-id="8b189-146">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="8b189-147">Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="8b189-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="8b189-148">Il est utile d’utiliser un nom explicite pour le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="8b189-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="8b189-149"><a name="ssox"></a> Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="8b189-149"><a name="ssox"></a> If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="8b189-150">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).</span><span class="sxs-lookup"><span data-stu-id="8b189-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span> <span data-ttu-id="8b189-151">Pour supprimer la base de données à partir de SSOX</span><span class="sxs-lookup"><span data-stu-id="8b189-151">To delete the database from SSOX:</span></span>

* <span data-ttu-id="8b189-152">Sélectionnez la base de données dans SSOX.</span><span class="sxs-lookup"><span data-stu-id="8b189-152">Select the database in SSOX.</span></span>
* <span data-ttu-id="8b189-153">Cliquez avec le bouton droit sur la base de données, puis sélectionnez *Supprimer*.</span><span class="sxs-lookup"><span data-stu-id="8b189-153">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="8b189-154">Cochez **Fermer les connexions existantes*.</span><span class="sxs-lookup"><span data-stu-id="8b189-154">Check **Close existing connections*</span></span>
* <span data-ttu-id="8b189-155">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="8b189-155">Select **OK**</span></span>
* <span data-ttu-id="8b189-156">Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="8b189-156">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database</span></span> 

    ```PMC
    Update-Database
    ```

<span data-ttu-id="8b189-157">Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="8b189-157">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="8b189-158">Si la base de données n’est pas amorcée, arrêtez IIS Express puis exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="8b189-158">If the database is not seeded, stop IIS Express, and then run the app.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="8b189-159">[Précédent : Ajout de la recherche](xref:tutorials/razor-pages/search)
[Suivant : Ajout de la validation](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="8b189-159">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

---
title: "Ajout d’un nouveau champ"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: get-started-article
ms.assetid: 16efbacf-fe7b-4b41-84b0-06a1574b95c2
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 52fe9ee7bf006553c7dcb17c00ff659d3797b204
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="adding-a-new-field"></a><span data-ttu-id="6379d-103">Ajout d’un nouveau champ</span><span class="sxs-lookup"><span data-stu-id="6379d-103">Adding a New Field</span></span>

<span data-ttu-id="6379d-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6379d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6379d-105">Dans cette section, vous allez utiliser Migrations [Entity Framework](http://docs.efproject.net/en/latest/platforms/aspnetcore/new-db.html) Code First pour ajouter un nouveau champ au modèle et migrer ce changement dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="6379d-105">In this section you'll use [Entity Framework](http://docs.efproject.net/en/latest/platforms/aspnetcore/new-db.html) Code First Migrations to add a new field to the model and migrate that change to the database.</span></span>

<span data-ttu-id="6379d-106">Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First ajoute une table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.</span><span class="sxs-lookup"><span data-stu-id="6379d-106">When you use EF Code First to automatically create a database, Code First adds a table to the database to help track whether the schema of the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="6379d-107">S’ils ne sont pas synchronisés, EF lève une exception.</span><span class="sxs-lookup"><span data-stu-id="6379d-107">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="6379d-108">Cela facilite la recherche de problèmes d’incohérence de code/de bases de données.</span><span class="sxs-lookup"><span data-stu-id="6379d-108">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="6379d-109">Ajout d’une propriété Rating au modèle Movie</span><span class="sxs-lookup"><span data-stu-id="6379d-109">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="6379d-110">Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="6379d-110">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

<span data-ttu-id="6379d-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]</span><span class="sxs-lookup"><span data-stu-id="6379d-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]</span></span>

<span data-ttu-id="6379d-112">Générez l’application (Ctrl+Maj+B).</span><span class="sxs-lookup"><span data-stu-id="6379d-112">Build the app (Ctrl+Shift+B).</span></span>

<span data-ttu-id="6379d-113">Comme vous avez ajouté un nouveau champ à la classe `Movie`, vous devez également mettre à jour la liste verte des liaisons pour y inclure cette nouvelle propriété.</span><span class="sxs-lookup"><span data-stu-id="6379d-113">Because you've added a new field to the `Movie` class, you also need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="6379d-114">Dans *MoviesController.cs*, mettez à jour l’attribut `[Bind]` des méthodes d’action `Create` et `Edit` pour y inclure la propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="6379d-114">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="6379d-115">Vous devez aussi mettre à jour les modèles de vue pour afficher, créer et modifier la nouvelle propriété `Rating` dans la vue du navigateur.</span><span class="sxs-lookup"><span data-stu-id="6379d-115">You also need to update the view templates in order to display, create and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="6379d-116">Ouvrez le fichier */Views/Movies/Index.cshtml* et ajoutez un champ `Rating` :</span><span class="sxs-lookup"><span data-stu-id="6379d-116">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

<span data-ttu-id="6379d-117">[!code-HTML[Main](start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]</span><span class="sxs-lookup"><span data-stu-id="6379d-117">[!code-HTML[Main](start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]</span></span>

<span data-ttu-id="6379d-118">Mettez à jour le fichier */Views/Movies/Create.cshtml* avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="6379d-118">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span> <span data-ttu-id="6379d-119">Vous pouvez copier/coller le « groupe de formulaire » précédent et laisser IntelliSense vous aider à mettre à jour les champs.</span><span class="sxs-lookup"><span data-stu-id="6379d-119">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="6379d-120">IntelliSense fonctionne avec des [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="6379d-120">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="6379d-121">Remarque : Dans la version RTM de Visual Studio 2017, vous devez installer les [Services de langage Razor](https://marketplace.visualstudio.com/items?itemName=ms-madsk.RazorLanguageServices) pour Razor IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="6379d-121">Note: In the RTM verison of Visual Studio 2017 you need to install the [Razor Language Services](https://marketplace.visualstudio.com/items?itemName=ms-madsk.RazorLanguageServices) for Razor intelliSense.</span></span> <span data-ttu-id="6379d-122">Ce problème sera résolu dans la prochaine version.</span><span class="sxs-lookup"><span data-stu-id="6379d-122">This will be fixed in the next release.</span></span>

![Le développeur a tapé la lettre R comme valeur d’attribut asp-for dans le deuxième élément étiquette de la vue.](new-field/_static/cr.png)

<span data-ttu-id="6379d-126">L’application ne fonctionne pas tant que la base de données n’est pas mise à jour pour inclure le nouveau champ.</span><span class="sxs-lookup"><span data-stu-id="6379d-126">The app won't work until we update the DB to include the new field.</span></span> <span data-ttu-id="6379d-127">Si vous l’exécutez maintenant, vous recevez l’exception `SqlException` suivante :</span><span class="sxs-lookup"><span data-stu-id="6379d-127">If you run it now, you'll get the following `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="6379d-128">Cette erreur s’affiche car la classe du modèle Movie mise à jour est différente du schéma de la table Movie de la base de données existante.</span><span class="sxs-lookup"><span data-stu-id="6379d-128">You're seeing this error because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="6379d-129">(Il n’existe pas de colonne Rating dans la table de base de données.)</span><span class="sxs-lookup"><span data-stu-id="6379d-129">(There's no Rating column in the database table.)</span></span>

<span data-ttu-id="6379d-130">Plusieurs approches sont possibles pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="6379d-130">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="6379d-131">Laissez Entity Framework supprimer et recréer automatiquement la base de données sur la base du nouveau schéma de classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="6379d-131">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="6379d-132">Cette approche est très utile au début du cycle de développement quand vous effectuez un développement actif sur une base de données de test. Elle permet de faire évoluer rapidement le schéma de modèle et de base de données ensemble.</span><span class="sxs-lookup"><span data-stu-id="6379d-132">This approach is very convenient early in the development cycle when you are doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="6379d-133">L’inconvénient, cependant, est que vous perdez les données existantes dans la base de données. Par conséquent, n’utilisez pas cette approche sur une base de données de production.</span><span class="sxs-lookup"><span data-stu-id="6379d-133">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="6379d-134">L’utilisation d’un initialiseur pour amorcer automatiquement une base de données avec des données de test est souvent un moyen efficace pour développer une application.</span><span class="sxs-lookup"><span data-stu-id="6379d-134">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span>

2. <span data-ttu-id="6379d-135">Modifiez explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="6379d-135">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="6379d-136">L’avantage de cette approche est que vous conservez vos données.</span><span class="sxs-lookup"><span data-stu-id="6379d-136">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="6379d-137">Vous pouvez apporter cette modification manuellement ou en créant un script de modification de la base de données.</span><span class="sxs-lookup"><span data-stu-id="6379d-137">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="6379d-138">Utilisez Migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="6379d-138">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="6379d-139">Pour ce didacticiel, nous allons utiliser les migrations Code First.</span><span class="sxs-lookup"><span data-stu-id="6379d-139">For this tutorial, we'll use Code First Migrations.</span></span>

<span data-ttu-id="6379d-140">Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="6379d-140">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="6379d-141">Vous pouvez voir un exemple de modification ci-dessous, mais elle doit être appliquée à chaque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="6379d-141">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

<span data-ttu-id="6379d-142">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]</span><span class="sxs-lookup"><span data-stu-id="6379d-142">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]</span></span>

<span data-ttu-id="6379d-143">Générez la solution.</span><span class="sxs-lookup"><span data-stu-id="6379d-143">Build the solution.</span></span>

<span data-ttu-id="6379d-144">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="6379d-144">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![Menu Console du Gestionnaire de package](adding-model/_static/pmc.png)

<span data-ttu-id="6379d-146">Dans la console du Gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="6379d-146">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Rating
Update-Database
```

<span data-ttu-id="6379d-147">La commande `Add-Migration` indique au framework de migration d’examiner le modèle `Movie` actuel avec le schéma de la base de données `Movie` actuel et de créer le code nécessaire pour migrer la base de données vers le nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="6379d-147">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span> <span data-ttu-id="6379d-148">Le nom « Rating » est arbitraire et est utilisé pour nommer le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="6379d-148">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="6379d-149">Il est utile d’utiliser un nom explicite pour le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="6379d-149">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="6379d-150">Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="6379d-150">If you delete all the records in the DB, the initialize will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="6379d-151">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de SSOX.</span><span class="sxs-lookup"><span data-stu-id="6379d-151">You can do this with the delete links in the browser or from SSOX.</span></span>

<span data-ttu-id="6379d-152">Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="6379d-152">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="6379d-153">Vous devez également ajouter le champ `Rating` aux modèles de vue `Edit`, `Details` et `Delete`.</span><span class="sxs-lookup"><span data-stu-id="6379d-153">You should also add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="6379d-154">[Précédent](search.md)
[Suivant](validation.md)</span><span class="sxs-lookup"><span data-stu-id="6379d-154">[Previous](search.md)
[Next](validation.md)</span></span>  

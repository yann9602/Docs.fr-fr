---
title: "Mise à jour des pages générées"
author: rick-anderson
description: "Mise à jour des pages générées avec un meilleur affichage."
keywords: ASP.NET Core, Pages Razor
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 39b65f8af8304fabc6cf8d9a27992043f1e381a0
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="updating-the-generated-pages"></a><span data-ttu-id="d1886-104">Mise à jour des pages générées</span><span class="sxs-lookup"><span data-stu-id="d1886-104">Updating the generated pages</span></span>

<span data-ttu-id="d1886-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d1886-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d1886-106">Nous avons un bon début pour l’application gestion de films, mais sa présentation n’est pas idéale.</span><span class="sxs-lookup"><span data-stu-id="d1886-106">We have a good start to the movie app, but the presentation is not ideal.</span></span> <span data-ttu-id="d1886-107">Nous ne voulons pas voir l’heure (« 12:00:00 AM » dans l’image ci-dessous) et **ReleaseDate** doit être **Release Date** (en deux mots).</span><span class="sxs-lookup"><span data-stu-id="d1886-107">We don't want to see the time (12:00:00 AM in the image below) and **ReleaseDate** should be **Release Date** (two words).</span></span>

![Application Movie ouverte dans Chrome, affichant les données relatives aux films](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="d1886-109">Mettre à jour le code généré</span><span class="sxs-lookup"><span data-stu-id="d1886-109">Update the generated code</span></span>

<span data-ttu-id="d1886-110">Ouvrez le fichier *Models/Movie.cs*, puis ajoutez les lignes affichées en surbrillance dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="d1886-110">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

<span data-ttu-id="d1886-111">[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDate.cs?name=snippet_1&highlight=10-11)]</span><span class="sxs-lookup"><span data-stu-id="d1886-111">[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDate.cs?name=snippet_1&highlight=10-11)]</span></span>

<span data-ttu-id="d1886-112">Cliquez avec le bouton droit sur une ligne ondulée rouge > ** Actions rapides et refactorisations**.</span><span class="sxs-lookup"><span data-stu-id="d1886-112">Right click on a red squiggly line > ** Quick Actions and Refactorings**.</span></span>

  ![Le menu contextuel affiche **> Actions rapides et refactorisations**.](da1/qa.png)


<span data-ttu-id="d1886-114">Sélectionnez `using System.ComponentModel.DataAnnotations;`.</span><span class="sxs-lookup"><span data-stu-id="d1886-114">Select `using System.ComponentModel.DataAnnotations;`</span></span>

  ![using System.ComponentModel.DataAnnotations en haut de la liste](da1/da.png)

  <span data-ttu-id="d1886-116">Visual studio ajoute `using System.ComponentModel.DataAnnotations;`.</span><span class="sxs-lookup"><span data-stu-id="d1886-116">Visual studio adds `using System.ComponentModel.DataAnnotations;`.</span></span>

<span data-ttu-id="d1886-117">Nous aborderons [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) dans le prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="d1886-117">We'll cover [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) in the next tutorial.</span></span> <span data-ttu-id="d1886-118">L’attribut [Display](https://docs.microsoft.com//aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) spécifie les éléments à afficher pour le nom d’un champ (dans le cas présent, « Release Date » au lieu de « ReleaseDate »).</span><span class="sxs-lookup"><span data-stu-id="d1886-118">The [Display](https://docs.microsoft.com//aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="d1886-119">L’attribut [DataType](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) spécifie le type des données (Date). Les informations d’heures stockées dans le champ ne s’affichent donc pas.</span><span class="sxs-lookup"><span data-stu-id="d1886-119">The [DataType](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field is not displayed.</span></span>

<span data-ttu-id="d1886-120">Accédez à Pages/Movies, puis placez le curseur sur un lien **Modifier** pour afficher l’URL cible.</span><span class="sxs-lookup"><span data-stu-id="d1886-120">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Fenêtre de navigateur avec la souris sur le lien Edit et une URL de lien http://localhost:1234/Movies/Edit/5 affichée](da1/edit7.png)

<span data-ttu-id="d1886-122">Les liens **Edit**, **Details**, et **Delete** sont générés par le [Tag Helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper) dans le fichier *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d1886-122">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper) in the *Pages/Movies/Index.cshtml* file.</span></span>

<span data-ttu-id="d1886-123">[!code-cshtml[Main](razor-pages-start\snapshot_sample\RazorPagesMovie\Pages\Movie\Index.cshtml?highlight=16-18&range=32-)]</span><span class="sxs-lookup"><span data-stu-id="d1886-123">[!code-cshtml[Main](razor-pages-start\snapshot_sample\RazorPagesMovie\Pages\Movie\Index.cshtml?highlight=16-18&range=32-)]</span></span>

<span data-ttu-id="d1886-124">Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les fichiers Razor.</span><span class="sxs-lookup"><span data-stu-id="d1886-124">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="d1886-125">Dans le code précédent, `AnchorTagHelper` génère dynamiquement la valeur d’attribut HTML `href` dans la page Razor (l’itinéraire est relatif), `asp-page` et l’ID de l’itinéraire (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="d1886-125">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="d1886-126">Pour plus d’informations, consultez [Génération d’URL pour les pages](xref:mvc/razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="d1886-126">See [URL generation for Pages](xref:mvc/razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="d1886-127">Utilisez **Afficher la Source** dans votre navigateur favori pour examiner le balisage généré.</span><span class="sxs-lookup"><span data-stu-id="d1886-127">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="d1886-128">Une partie du code HTML généré est affichée ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="d1886-128">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>

```

<span data-ttu-id="d1886-129">Les liens générés dynamiquement passent l’ID de film avec une chaîne de requête (par exemple, `http://localhost:5000/Movies/Details?id=2`).</span><span class="sxs-lookup"><span data-stu-id="d1886-129">The dynamically-generated links pass the movie ID with a query string (for example, `http://localhost:5000/Movies/Details?id=2` ).</span></span> 

<span data-ttu-id="d1886-130">Mettez à jour les pages Razor Edit, Details et Delete pour utiliser le modèle d’itinéraire « {id:int} ».</span><span class="sxs-lookup"><span data-stu-id="d1886-130">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="d1886-131">Remplacez la directive de page de chacune de ces pages par `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="d1886-131">Change the page directive for each of these pages to `@page "{id:int}"`.</span></span> <span data-ttu-id="d1886-132">Exécuter l’application, puis affichez le code source.</span><span class="sxs-lookup"><span data-stu-id="d1886-132">Run the app and then view source.</span></span> <span data-ttu-id="d1886-133">Le code HTML généré ajoute l’ID à la partie de chemin de l’URL :</span><span class="sxs-lookup"><span data-stu-id="d1886-133">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="d1886-134">Une requête à la page avec le modèle d’itinéraire « {id:int} » qui n’inclut **pas** l’entier retourne une erreur HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="d1886-134">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="d1886-135">Par exemple, `http://localhost:5000/Movies/Details` retourne une erreur 404.</span><span class="sxs-lookup"><span data-stu-id="d1886-135">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="d1886-136">Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :</span><span class="sxs-lookup"><span data-stu-id="d1886-136">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

### <a name="update-concurrency-exception-handling"></a><span data-ttu-id="d1886-137">Mettre à jour la gestion des exceptions d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="d1886-137">Update concurrency exception handling</span></span>

<span data-ttu-id="d1886-138">Mettez à jour la méthode `OnPostAsync` dans le fichier *Pages/Movies/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="d1886-138">Update the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file.</span></span> <span data-ttu-id="d1886-139">Le code en surbrillance suivant illustre les changements :</span><span class="sxs-lookup"><span data-stu-id="d1886-139">The following highlighted code shows the changes:</span></span>

<span data-ttu-id="d1886-140">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet1&highlight=17-24)]</span><span class="sxs-lookup"><span data-stu-id="d1886-140">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet1&highlight=17-24)]</span></span>

<span data-ttu-id="d1886-141">Le code précédent détecte uniquement les exceptions d’accès concurrentiel quand le premier client simultané supprime le film et que le deuxième client simultané poste les modifications apportées au film.</span><span class="sxs-lookup"><span data-stu-id="d1886-141">The previous code only detects concurrency exceptions when the first concurrent client deletes the movie, and the second concurrent client posts changes to the movie.</span></span>

<span data-ttu-id="d1886-142">Pour tester le bloc `catch` :</span><span class="sxs-lookup"><span data-stu-id="d1886-142">To test the `catch` block:</span></span>

* <span data-ttu-id="d1886-143">Définissez un point d'arrêt sur `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="d1886-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="d1886-144">Modifiez un film.</span><span class="sxs-lookup"><span data-stu-id="d1886-144">Edit a movie.</span></span>
* <span data-ttu-id="d1886-145">Dans une autre fenêtre de navigateur, sélectionnez le lien **Delete** du même film, puis supprimez le film.</span><span class="sxs-lookup"><span data-stu-id="d1886-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="d1886-146">Dans la fenêtre de navigateur précédente, postez les modifications apportées au film.</span><span class="sxs-lookup"><span data-stu-id="d1886-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="d1886-147">Le code de production détecte généralement les conflits d’accès concurrentiel quand deux clients, ou plus, mettent simultanément à jour un enregistrement.</span><span class="sxs-lookup"><span data-stu-id="d1886-147">Production code would generally detect concurrency conflicts when two or more clients concurrently updated a record.</span></span> <span data-ttu-id="d1886-148">Pour plus d’informations, consultez [Gestion de conflits d’accès concurrentiel](xref:data/ef-mvc/concurrency).</span><span class="sxs-lookup"><span data-stu-id="d1886-148">See [Handling concurrency conflicts](xref:data/ef-mvc/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="d1886-149">Validation de la publication et de la liaison</span><span class="sxs-lookup"><span data-stu-id="d1886-149">Posting and binding review</span></span>

<span data-ttu-id="d1886-150">Examinez le fichier *Pages/Movies/Edit.cshtml.cs* : [!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="d1886-150">Examine the *Pages/Movies/Edit.cshtml.cs* file: [!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet2)]</span></span>

<span data-ttu-id="d1886-151">Quand une requête HTTP GET est effectuée sur la page Movies/Edit (par exemple, `http://localhost:5000/Movies/Edit/2`) :</span><span class="sxs-lookup"><span data-stu-id="d1886-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="d1886-152">La méthode `OnGetAsync` extrait le film de la base de données et retourne la méthode `Page`.</span><span class="sxs-lookup"><span data-stu-id="d1886-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="d1886-153">La méthode `Page` restitue la page Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d1886-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="d1886-154">Le fichier *Pages/Movies/Edit.cshtml* contient la directive de modèle (`@model RazorPagesMovie.Pages.Movies.EditModel`), ce qui rend le modèle Movie disponible sur la page.</span><span class="sxs-lookup"><span data-stu-id="d1886-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the the movie model available on the page.</span></span>
* <span data-ttu-id="d1886-155">Le formulaire d’édition s’affiche avec les valeurs relatives au film.</span><span class="sxs-lookup"><span data-stu-id="d1886-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="d1886-156">Quand la page Movies/Edit est postée :</span><span class="sxs-lookup"><span data-stu-id="d1886-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="d1886-157">Les valeurs de formulaire affichées dans la page sont liées à la propriété `Movie`.</span><span class="sxs-lookup"><span data-stu-id="d1886-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="d1886-158">L’attribut `[BindProperty]` active la [liaison de données](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="d1886-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

```csharp
[BindProperty]
public Movie Movie { get; set; }
```

* <span data-ttu-id="d1886-159">S’il existe des erreurs dans l’état du modèle (par exemple, si `ReleaseDate` ne peut pas être converti en date), le formulaire est à nouveau posté avec les valeurs soumises.</span><span class="sxs-lookup"><span data-stu-id="d1886-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is posted again with the submitted values.</span></span>
* <span data-ttu-id="d1886-160">S’il n’y a aucune erreur de modèle, le film est enregistré.</span><span class="sxs-lookup"><span data-stu-id="d1886-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="d1886-161">Les méthodes HTTP GET dans les pages Razor Index, Create et Delete suivent un modèle similaire.</span><span class="sxs-lookup"><span data-stu-id="d1886-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="d1886-162">La méthode HTTP POST `OnPostAsync` dans la page Razor Create suit un modèle semblable à la méthode `OnPostAsync` dans la page Razor Edit.</span><span class="sxs-lookup"><span data-stu-id="d1886-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="d1886-163">La fonction de recherche est ajoutée dans le prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="d1886-163">Search is added in the next tutorial.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="d1886-164">[Précédent : Utilisation de SQL Server LocalDB](xref:tutorials/razor-pages/sql)
[Ajout d’une fonction de recherche](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="d1886-164">[Previous: Working with SQL Server LocalDB](xref:tutorials/razor-pages/sql)
[Adding Search](xref:tutorials/razor-pages/search)</span></span>

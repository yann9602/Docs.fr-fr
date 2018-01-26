---
title: "Mise à jour des pages générées"
author: rick-anderson
description: "Mise à jour des pages générées avec un meilleur affichage."
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 201e8d9c77d8e022bc56ffcf46456fada6fcfe25
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="updating-the-generated-pages"></a>Mise à jour des pages générées

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Nous avons un bon début pour l’application gestion de films, mais sa présentation n’est pas idéale. Nous ne voulons pas voir l’heure (« 12:00:00 AM » dans l’image ci-dessous) et **ReleaseDate** doit être **Release Date** (en deux mots).

![Application Movie ouverte dans Chrome, affichant les données relatives aux films](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Mettre à jour le code généré

Ouvrez le fichier *Models/Movie.cs*, puis ajoutez les lignes affichées en surbrillance dans le code suivant :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDate.cs?name=snippet_1&highlight=10-11)]

Cliquez avec le bouton droit sur une ligne ondulée rouge > ** Actions rapides et refactorisations**.

  ![Le menu contextuel affiche **> Actions rapides et refactorisations**.](da1/qa.png)

Sélectionnez `using System.ComponentModel.DataAnnotations;`.

  ![using System.ComponentModel.DataAnnotations en haut de la liste](da1/da.png)

  Visual studio ajoute `using System.ComponentModel.DataAnnotations;`.

Nous aborderons [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) dans le prochain didacticiel. L’attribut [Display](https://docs.microsoft.com//aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) spécifie les éléments à afficher pour le nom d’un champ (dans le cas présent, « Release Date » au lieu de « ReleaseDate »). L’attribut [DataType](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) spécifie le type des données (Date). Les informations d’heures stockées dans le champ ne s’affichent donc pas.

Accédez à Pages/Movies, puis placez le curseur sur un lien **Modifier** pour afficher l’URL cible.

![Fenêtre de navigateur avec la souris sur le lien Edit et une URL de lien http://localhost:1234/Movies/Edit/5 affichée](da1/edit7.png)

Les liens **Edit**, **Details**, et **Delete** sont générés par le [Tag Helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dans le fichier *Pages/Movies/Index.cshtml*.

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les fichiers Razor. Dans le code précédent, `AnchorTagHelper` génère dynamiquement la valeur d’attribut HTML `href` dans la page Razor (l’itinéraire est relatif), `asp-page` et l’ID de l’itinéraire (`asp-route-id`). Pour plus d’informations, consultez [Génération d’URL pour les pages](xref:mvc/razor-pages/index#url-generation-for-pages).

Utilisez **Afficher la Source** dans votre navigateur favori pour examiner le balisage généré. Une partie du code HTML généré est affichée ci-dessous :

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Les liens générés dynamiquement passent l’ID de film avec une chaîne de requête (par exemple, `http://localhost:5000/Movies/Details?id=2`). 

Mettez à jour les pages Razor Edit, Details et Delete pour utiliser le modèle d’itinéraire « {id:int} ». Remplacez la directive de chacune de ces pages (`@page "{id:int}"`) par `@page`. Exécuter l’application, puis affichez le code source. Le code HTML généré ajoute l’ID à la partie de chemin de l’URL :

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Une requête à la page avec le modèle d’itinéraire « {id:int} » qui n’inclut **pas** l’entier retourne une erreur HTTP 404 (introuvable). Par exemple, `http://localhost:5000/Movies/Details` retourne une erreur 404. Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :

 ```cshtml
@page "{id:int?}"
```

### <a name="update-concurrency-exception-handling"></a>Mettre à jour la gestion des exceptions d’accès concurrentiel

Mettez à jour la méthode `OnPostAsync` dans le fichier *Pages/Movies/Edit.cshtml.cs*. Le code en surbrillance suivant illustre les changements :

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit.cshtml.cs?name=snippet1&highlight=16-23)]

Le code précédent détecte uniquement les exceptions d’accès concurrentiel quand le premier client simultané supprime le film et que le deuxième client simultané poste les modifications apportées au film.

Pour tester le bloc `catch` :

* Définissez un point d'arrêt sur `catch (DbUpdateConcurrencyException)`
* Modifiez un film.
* Dans une autre fenêtre de navigateur, sélectionnez le lien **Delete** du même film, puis supprimez le film.
* Dans la fenêtre de navigateur précédente, postez les modifications apportées au film.

Le code de production détecte généralement les conflits d’accès concurrentiel quand deux clients, ou plus, mettent simultanément à jour un enregistrement. Pour plus d’informations, consultez [Gestion de conflits d’accès concurrentiel](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Validation de la publication et de la liaison

Examinez le fichier *Pages/Movies/Edit.cshtml.cs* : [!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit.cshtml.cs?name=snippet2)]

Quand une requête HTTP GET est effectuée sur la page Movies/Edit (par exemple, `http://localhost:5000/Movies/Edit/2`) :

* La méthode `OnGetAsync` extrait le film de la base de données et retourne la méthode `Page`. 
* La méthode `Page` restitue la page Razor *Pages/Movies/Edit.cshtml*. Le fichier *Pages/Movies/Edit.cshtml* contient la directive de modèle (`@model RazorPagesMovie.Pages.Movies.EditModel`), ce qui rend le modèle Movie disponible sur la page.
* Le formulaire d’édition s’affiche avec les valeurs relatives au film.

Quand la page Movies/Edit est postée :

* Les valeurs de formulaire affichées dans la page sont liées à la propriété `Movie`. L’attribut `[BindProperty]` active la [liaison de données](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* S’il existe des erreurs dans l’état du modèle (par exemple, si `ReleaseDate` ne peut pas être converti en date), le formulaire est à nouveau posté avec les valeurs soumises.
* S’il n’y a aucune erreur de modèle, le film est enregistré.

Les méthodes HTTP GET dans les pages Razor Index, Create et Delete suivent un modèle similaire. La méthode HTTP POST `OnPostAsync` dans la page Razor Create suit un modèle semblable à la méthode `OnPostAsync` dans la page Razor Edit.

La fonction de recherche est ajoutée dans le prochain didacticiel.

>[!div class="step-by-step"]
[Précédent : Utilisation de SQL Server LocalDB](xref:tutorials/razor-pages/sql)
[Ajout d’une fonction de recherche](xref:tutorials/razor-pages/search)

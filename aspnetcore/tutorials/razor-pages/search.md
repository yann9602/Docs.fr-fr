---
title: "Ajout d’une fonction de recherche à des pages Razor dans ASP.NET Core MVC"
author: rick-anderson
description: "Montre comment ajouter une fonction de recherche à des pages Razor dans ASP.NET Core MVC"
keywords: ASP.NET Core, recherches, Pages Razor
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/razor-pages/search
ms.openlocfilehash: f70f1e9b0e085f5aa90fcca499526588662c3cfd
ms.sourcegitcommit: ffac7e195bd7f99364f3aab45e491eeaf8f173b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2017
---
# <a name="adding-search-to-an-aspnet-core-mvc-app"></a>Ajout d’une fonction de recherche à une application ASP.NET Core MVC

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Dans ce document, la fonction de recherche est ajoutée à la page d’index qui permet la recherche de films par *genre* ou par *nom*.

Mettez à jour la méthode `OnGetAsync` de la page d’index avec le code suivant :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

La première ligne de la méthode `OnGetAsync` crée une requête [LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/) pour sélectionner les films :

```csharp
 var movies = from m in _context.Movie
              select m;
```

La requête est *seulement* définie à ce stade ; elle n’a **pas** été exécutée sur la base de données.

Si le paramètre `searchString` contient une chaîne, la requête de films est modifiée de façon à filtrer sur la chaîne de recherche :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Le code `s => s.Title.Contains()` est une [expression lambda](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Les expressions lambda sont utilisées dans les requêtes [LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/) basées sur une méthode comme arguments pour les méthodes d’opérateur de requête standard, telles que la méthode [Where](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` (utilisée dans le code précédent). Les requêtes LINQ ne sont pas exécutées quand elles sont définies ou quand elles sont modifiées en appelant une méthode comme `Where`, `Contains` ou `OrderBy`. Au lieu de cela, l’exécution de la requête est différée. Cela signifie que l’évaluation d’une expression est retardée jusqu’à ce que sa valeur réalisée fasse l’objet d’une itération réelle ou que la méthode `ToListAsync` soit appelée. Pour plus d’informations, consultez [Exécution de requête](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/ef/language-reference/query-execution).

**Remarque :** La méthode [Contains](http://msdn.microsoft.com/library/bb155125.aspx) est exécutée sur la base de données, et non pas dans le code C#. Le respect de la casse pour la requête dépend de la base de données et du classement. Sur SQL Server, `Contains` est mappée à [SQL LIKE](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/like-transact-sql), qui ne respecte pas la casse. Dans SQLite, avec le classement par défaut, elle respecte la casse.

Accédez à la page Movies, puis ajoutez une chaîne de requête telle que `?searchString=Ghost` à l’URL (par exemple, `http://localhost:5000/Movies?searchString=Ghost`). Les films filtrés sont affichés.

![Vue Index](search/_static/ghost.png)

Si le modèle d’itinéraire suivant est ajouté à la page d’index, la chaîne de recherche peut être passée comme un segment d’URL (par exemple, `http://localhost:5000/Movies/ghost`).

```cshtml
@page "{searchString?}"
```

La contrainte d’itinéraire précédente permet de rechercher le titre comme données d’itinéraire (un segment d’URL) et non comme valeur de chaîne de requête.  Le `?` dans `"{searchString?}"` signifie qu’il s’agit d’un paramètre d’itinéraire facultatif.

![Vue Index avec le mot « ghost » ajouté à l’URL et une liste de films retournée contenant deux films, Ghostbusters et Ghostbusters 2](search/_static/g2.png)

Cependant, vous ne pouvez pas attendre des utilisateurs qu’ils modifient l’URL pour rechercher un film. Dans cette étape, une interface utilisateur est ajoutée pour filtrer les films. Si vous avez ajouté la contrainte d’itinéraire `"{searchString?}"`, supprimez-la.

Ouvrez le fichier *Pages/Movies/Index.cshtml*, puis ajoutez la balise `<form>` mise en surbrillance dans le code suivant :

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

La balise HTML `<form>` utilise le [Tag Helper de formulaire](xref:mvc/views/working-with-forms#the-form-tag-helper). Quand le formulaire est envoyé, la chaîne de filtrage est envoyée à la page *Pages/Movies/Index*. Enregistrez les modifications apportées, puis testez le filtre.

![Vue Index avec le mot « ghost » tapé dans la zone de texte de filtre des titres](search/_static/filter.png)

## <a name="search-by-genre"></a>Rechercher par genre

Ajoutez les propriétés en surbrillance suivantes à *Pages/Movies/Index.cshtml.cs* :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-)]

La propriété `SelectList Genres` contient la liste des genres. Cela permet à l’utilisateur de sélectionner un genre dans la liste.

La propriété `MovieGenre` contient le genre spécifique sélectionné par l’utilisateur (par exemple, « Western »).

Mettez à jour la méthode `OnGetAsync` avec le code suivant :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Le code suivant est une requête LINQ qui récupère tous les genres dans la base de données.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

La liste `SelectList` de genres est créée en projetant des différents genres.

<!-- BUG in OPS
Tag snippet_selectlist's start line '75' should be less than end line '29' when resolving "[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]"

There is no start line.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]
-->

```csharp
Genres = new SelectList(await genreQuery.Distinct().ToListAsync());
```

### <a name="adding-search-by-genre"></a>Ajout d’une fonction de recherche par genre

Mettez à jour *Index.cshtml* comme suit :

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Testez l’application en effectuant une recherche par genre, par titre de film et selon ces deux critères.

>[!div class="step-by-step"]
[Précédent : Mise à jour des pages](xref:tutorials/razor-pages/da1)
[Suivant : Ajout d’un nouveau champ](xref:tutorials/razor-pages/new-field)
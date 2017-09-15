<!--
[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

![Index view](../../tutorials/first-mvc-app/search/_static/ghost.png)


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

--> 

Maintenant, quand vous soumettez une recherche, l’URL contient la chaîne de requête de la recherche. La recherche accède également à la méthode d’action `HttpGet Index`, même si vous avez une méthode `HttpPost Index`.

![Fenêtre de navigateur montrant la chaîne de recherche « ghost » dans l’URL et les films retournés, Ghostbusters et Ghostbusters 2, qui contiennent le mot « ghost »](../../tutorials/first-mvc-app/search/_static/search_get.png)

La mise en forme suivante montre la modification apportée à la balise `form` :

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="adding-search-by-genre"></a>Ajout de la recherche par genre

Ajoutez la classe `MovieGenreViewModel` suivante au dossier *Models* :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Le modèle de vue movie-genre contiendra :

   * Une liste de films.
   * Une `SelectList` contenant la liste des genres. L’utilisateur pourra ainsi sélectionner un genre à partir de la liste.
   * `movieGenre`, qui contient le genre sélectionné.

Remplacez la méthode `Index` dans `MoviesController.cs` par le code suivant :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Le code suivant est une requête `LINQ` qui récupère tous les genres de la base de données.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_LINQ)]

La `SelectList` de genres est créée en projetant les genres distincts (nous ne voulons pas que notre liste de sélection ait des genres en doublon).

```csharp
movieGenreVM.genres = new SelectList(await genreQuery.Distinct().ToListAsync())
   ```

## <a name="adding-search-by-genre-to-the-index-view"></a>Ajout de la recherche par genre à la vue Index

Mettez à jour `Index.cshtml` comme suit :

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,28,31,34,37,43)]

Examinez l’expression lambda utilisée dans le Helper HTML suivant :

`@Html.DisplayNameFor(model => model.movies[0].Title)`
 
Dans le code précédent, le Helper HTML `DisplayNameFor` inspecte la propriété `Title` référencée dans l’expression lambda pour déterminer le nom d’affichage. Comme l’expression lambda est inspectée et non pas évaluée, vous ne recevez pas de violation d’accès quand `model`, `model.movies` ou `model.movies[0]` sont `null` ou vides. Quand l’expression lambda est évaluée (par exemple `@Html.DisplayFor(modelItem => item.Title)`), les valeurs des propriétés du modèle sont évaluées.

Testez l’application en effectuant une recherche par genre, par titre de film et selon ces deux critères.

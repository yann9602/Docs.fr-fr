Le code mis en surbrillance ci-dessus montre l’ajout du contexte de base de données des films au conteneur [d’injection de dépendances](xref:fundamentals/dependency-injection) (dans le fichier *Startup.cs*). `services.AddDbContext<MvcMovieContext>(options =>` spécifie la base de données à utiliser et la chaîne de connexion. `=>` est un [opérateur lambda](https://docs.microsoft.com/dotnet/articles/csharp/language-reference/operators/lambda-operator).

Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_1)] 

Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext `) dans le contrôleur. Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.

<a name="strongly-typed-models-keyword-label"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modèles fortement typés et mot clé @model

Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`. Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.

Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue. Cette approche fortement typée permet une meilleure vérification de votre code au moment de la compilation. Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et les vues quand il a créé les méthodes et les vues.

Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]

Le paramètre `id` est généralement passé en tant que données de routage. Par exemple, `http://localhost:5000/movies/details/1` définit :

* Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).
* L’action sur `details` (le deuxième segment de l’URL).
* L’ID sur 1 (le dernier segment de l’URL).

Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :

`http://localhost:1234/movies/details?id=1`

Le paramètre `id` est défini comme [type nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) au cas où la valeur d’ID n’est pas fournie.

Une [expression lambda](https://docs.microsoft.com/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `SingleOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.

```csharp
var movie = await _context.Movie
    .SingleOrDefaultAsync(m => m.ID == id);
```

Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :

```csharp
return View(movie);
   ```

Examinez le contenu du fichier *Views/Movies/Details.cshtml*:

[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/DetailsOriginal.cshtml)]

En incluant une instruction `@model` en haut du fichier de la vue, vous pouvez spécifier le type d’objet attendu par la vue. Quand vous avez créé le contrôleur pour les films, Visual Studio a inclus automatiquement l’instruction `@model` suivante en haut du fichier *Details.cshtml*:

```HTML
@model MvcMovie.Models.Movie
   ```

Cette directive `@model` vous permet d’accéder au film que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé. Par exemple, dans la vue *Details.cshtml*, le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé. Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.

Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies. Notez comment le code crée un objet `List` quand il appelle la méthode `View`. Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_index)]

Quand vous avez créé le contrôleur pour les films, la génération de modèles automatique a inclus automatiquement l’instruction `@model` suivante en haut du fichier *Index.cshtml* :

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?range=1)]

La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé. Par exemple, dans la vue *Index.cshtml*, le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :

[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`. Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :

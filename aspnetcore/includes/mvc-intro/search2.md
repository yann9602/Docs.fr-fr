<!--
[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

![Index view](../../tutorials/first-mvc-app/search/_static/ghost.png)


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

--> 

La méthode `Index` précédente :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_1stSearch)]

La méthode `Index` mise à jour avec le paramètre `id` :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_SearchID)]

Vous pouvez maintenant passer le titre de la recherche en tant que données de routage (un segment de l’URL) et non pas en tant que valeur de chaîne de requête.

![Vue Index avec le mot « ghost » ajouté à l’URL, et une liste des films retournés avec deux films, Ghostbusters et Ghostbusters 2](../../tutorials/first-mvc-app/search/_static/g2.png)

Cependant, vous ne pouvez pas attendre des utilisateurs qu’ils modifient l’URL à chaque fois qu’ils veulent rechercher un film. Vous allez donc maintenant ajouter une interface utilisateur pour les aider à filtrer les films. Si vous avez changé la signature de la méthode `Index` pour tester comment passer le paramètre `ID` lié à une route, rétablissez-la de façon à ce qu’elle prenne un paramètre nommé `searchString` :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_1stSearch)]

Ouvrez le fichier *Views/Movies/Index.cshtml* et ajoutez le balisage `<form>` mis en surbrillance ci-dessous :

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

La balise HTML `<form>` utilise le [Tag Helper de formulaire](../../mvc/views/working-with-forms.md), de façon que quand vous envoyez le formulaire, la chaîne de filtrage soit envoyée à l’action `Index` du contrôleur de films. Enregistrez vos modifications puis testez le filtre.

![Vue Index avec le mot « ghost » tapé dans la zone de texte du filtre Title](../../tutorials/first-mvc-app/search/_static/filter.png)

Contrairement à ce que vous pourriez penser, une surcharge de `[HttpPost]` dans la méthode `Index` n’est pas nécessaire. Vous n’en avez pas besoin, car la méthode ne change pas l’état de l’application, elle filtre seulement les données.

Vous pourriez ajouter la méthode `[HttpPost] Index` suivante.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

Le paramètre `notUsed` est utilisé pour créer une surcharge pour la méthode `Index`. Nous parlons de ceci plus loin dans le didacticiel.

Si vous ajoutez cette méthode, le demandeur de l’action correspondrait à la méthode `[HttpPost] Index` et la méthode `[HttpPost] Index` s’exécuterait comme indiqué dans l’image ci-dessous.

![Fenêtre de navigateur avec la réponse de l’application de « From HttpPost Index: » avec filtrage sur « ghost »](../../tutorials/first-mvc-app/search/_static/fo.png)

Cependant, même si vous ajoutez cette version `[HttpPost]` de la méthode `Index`, il existe une limitation dans la façon dont tout ceci a été implémenté. Imaginez que vous voulez insérer un signet pour une recherche spécifique, ou que vous voulez envoyer un lien à vos amis sur lequel ils peuvent cliquer pour afficher la même liste filtrée de films. Notez que l’URL de la requête HTTP POST est identique à l’URL de la requête GET (localhost:xxxxx/Movies/Index) : il n’existe aucune information de recherche dans l’URL. Les informations de la chaîne de recherche sont envoyées au serveur en tant que [valeur d’un champ de formulaire](https://developer.mozilla.org/docs/Web/Guide/HTML/Forms/Sending_and_retrieving_form_data). Vous pouvez vérifier ceci avec les outils de développement du navigateur ou avec l’excellent [outil Fiddler](http://www.telerik.com/fiddler). L’illustration ci-dessous montre les outils de développement du navigateur Chrome :

![Onglet Réseau des outils de développement dans Microsoft Edge montrant un corps de demande avec une valeur « ghost » pour searchString](../../tutorials/first-mvc-app/search/_static/f12_rb.png)

Vous pouvez voir le paramètre de recherche et le jeton [XSRF](../../security/anti-request-forgery.md) dans le corps de la demande. Notez que, comme indiqué dans le didacticiel précédent, le [Tag Helper de formulaire](../../mvc/views/working-with-forms.md) génère un jeton [XSRF](../../security/anti-request-forgery.md) anti-contrefaçon. Nous ne modifions pas les données : nous n’avons donc pas besoin de valider le jeton dans la méthode du contrôleur.

Comme le paramètre de recherche se trouve dans le corps de la demande et pas dans l’URL, vous ne pouvez pas capturer ces informations de recherche pour les insérer dans un signet ou les partager avec d’autres personnes. Nous résolvons ce problème en spécifiant que la requête doit être `HTTP GET`.
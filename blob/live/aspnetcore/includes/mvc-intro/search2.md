<!--
[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

![Index view](../../tutorials/first-mvc-app/search/_static/ghost.png)


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

--> 

<span data-ttu-id="a6987-101">La méthode `Index` précédente :</span><span class="sxs-lookup"><span data-stu-id="a6987-101">The previous `Index` method:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_1stSearch)]

<span data-ttu-id="a6987-102">La méthode `Index` mise à jour avec le paramètre `id` :</span><span class="sxs-lookup"><span data-stu-id="a6987-102">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_SearchID)]

<span data-ttu-id="a6987-103">Vous pouvez maintenant passer le titre de la recherche en tant que données de routage (un segment de l’URL) et non pas en tant que valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="a6987-103">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Vue Index avec le mot « ghost » ajouté à l’URL, et une liste des films retournés avec deux films, Ghostbusters et Ghostbusters 2](../../tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="a6987-105">Cependant, vous ne pouvez pas attendre des utilisateurs qu’ils modifient l’URL à chaque fois qu’ils veulent rechercher un film.</span><span class="sxs-lookup"><span data-stu-id="a6987-105">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="a6987-106">Vous allez donc maintenant ajouter une interface utilisateur pour les aider à filtrer les films.</span><span class="sxs-lookup"><span data-stu-id="a6987-106">So now you'll add UI to help them filter movies.</span></span> <span data-ttu-id="a6987-107">Si vous avez changé la signature de la méthode `Index` pour tester comment passer le paramètre `ID` lié à une route, rétablissez-la de façon à ce qu’elle prenne un paramètre nommé `searchString` :</span><span class="sxs-lookup"><span data-stu-id="a6987-107">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_1stSearch)]

<span data-ttu-id="a6987-108">Ouvrez le fichier *Views/Movies/Index.cshtml* et ajoutez le balisage `<form>` mis en surbrillance ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="a6987-108">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="a6987-109">La balise HTML `<form>` utilise le [Tag Helper de formulaire](../../mvc/views/working-with-forms.md), de façon que quand vous envoyez le formulaire, la chaîne de filtrage soit envoyée à l’action `Index` du contrôleur de films.</span><span class="sxs-lookup"><span data-stu-id="a6987-109">The HTML `<form>` tag uses the [Form Tag Helper](../../mvc/views/working-with-forms.md), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="a6987-110">Enregistrez vos modifications puis testez le filtre.</span><span class="sxs-lookup"><span data-stu-id="a6987-110">Save your changes and then test the filter.</span></span>

![Vue Index avec le mot « ghost » tapé dans la zone de texte du filtre Title](../../tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="a6987-112">Contrairement à ce que vous pourriez penser, une surcharge de `[HttpPost]` dans la méthode `Index` n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="a6987-112">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="a6987-113">Vous n’en avez pas besoin, car la méthode ne change pas l’état de l’application, elle filtre seulement les données.</span><span class="sxs-lookup"><span data-stu-id="a6987-113">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="a6987-114">Vous pourriez ajouter la méthode `[HttpPost] Index` suivante.</span><span class="sxs-lookup"><span data-stu-id="a6987-114">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="a6987-115">Le paramètre `notUsed` est utilisé pour créer une surcharge pour la méthode `Index`.</span><span class="sxs-lookup"><span data-stu-id="a6987-115">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="a6987-116">Nous parlons de ceci plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="a6987-116">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="a6987-117">Si vous ajoutez cette méthode, le demandeur de l’action correspondrait à la méthode `[HttpPost] Index` et la méthode `[HttpPost] Index` s’exécuterait comme indiqué dans l’image ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a6987-117">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Fenêtre de navigateur avec la réponse de l’application de « From HttpPost Index: » avec filtrage sur « ghost »](../../tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="a6987-119">Cependant, même si vous ajoutez cette version `[HttpPost]` de la méthode `Index`, il existe une limitation dans la façon dont tout ceci a été implémenté.</span><span class="sxs-lookup"><span data-stu-id="a6987-119">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="a6987-120">Imaginez que vous voulez insérer un signet pour une recherche spécifique, ou que vous voulez envoyer un lien à vos amis sur lequel ils peuvent cliquer pour afficher la même liste filtrée de films.</span><span class="sxs-lookup"><span data-stu-id="a6987-120">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="a6987-121">Notez que l’URL de la requête HTTP POST est identique à l’URL de la requête GET (localhost:xxxxx/Movies/Index) : il n’existe aucune information de recherche dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="a6987-121">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:xxxxx/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="a6987-122">Les informations de la chaîne de recherche sont envoyées au serveur en tant que [valeur d’un champ de formulaire](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span><span class="sxs-lookup"><span data-stu-id="a6987-122">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="a6987-123">Vous pouvez vérifier ceci avec les outils de développement du navigateur ou avec l’excellent [outil Fiddler](http://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="a6987-123">You can verify that with the browser Developer tools or the excellent [Fiddler tool](http://www.telerik.com/fiddler).</span></span> <span data-ttu-id="a6987-124">L’illustration ci-dessous montre les outils de développement du navigateur Chrome :</span><span class="sxs-lookup"><span data-stu-id="a6987-124">The image below shows the Chrome browser Developer tools:</span></span>

![Onglet Réseau des outils de développement dans Microsoft Edge montrant un corps de demande avec une valeur « ghost » pour searchString](../../tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="a6987-126">Vous pouvez voir le paramètre de recherche et le jeton [XSRF](../../security/anti-request-forgery.md) dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="a6987-126">You can see the search parameter and [XSRF](../../security/anti-request-forgery.md) token in the request body.</span></span> <span data-ttu-id="a6987-127">Notez que, comme indiqué dans le didacticiel précédent, le [Tag Helper de formulaire](../../mvc/views/working-with-forms.md) génère un jeton [XSRF](../../security/anti-request-forgery.md) anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="a6987-127">Note, as mentioned in the previous tutorial, the [Form Tag Helper](../../mvc/views/working-with-forms.md) generates an [XSRF](../../security/anti-request-forgery.md) anti-forgery token.</span></span> <span data-ttu-id="a6987-128">Nous ne modifions pas les données : nous n’avons donc pas besoin de valider le jeton dans la méthode du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a6987-128">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="a6987-129">Comme le paramètre de recherche se trouve dans le corps de la demande et pas dans l’URL, vous ne pouvez pas capturer ces informations de recherche pour les insérer dans un signet ou les partager avec d’autres personnes.</span><span class="sxs-lookup"><span data-stu-id="a6987-129">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="a6987-130">Nous résolvons ce problème en spécifiant que la requête doit être `HTTP GET`.</span><span class="sxs-lookup"><span data-stu-id="a6987-130">We'll fix this by specifying the request should be `HTTP GET`.</span></span>

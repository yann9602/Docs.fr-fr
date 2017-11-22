[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="a6796-101">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="a6796-101">The preceding code:</span></span>

* <span data-ttu-id="a6796-102">Définit une classe de contrôleur vide.</span><span class="sxs-lookup"><span data-stu-id="a6796-102">Defines an empty controller class.</span></span> <span data-ttu-id="a6796-103">Dans les sections suivantes, des méthodes sont ajoutées pour implémenter l’API.</span><span class="sxs-lookup"><span data-stu-id="a6796-103">In the next sections, methods are added to implement the API.</span></span>
* <span data-ttu-id="a6796-104">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`TodoContext `) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a6796-104">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`TodoContext `) into the controller.</span></span> <span data-ttu-id="a6796-105">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a6796-105">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="a6796-106">Le constructeur ajoute un élément à la base de données en mémoire s’il n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="a6796-106">The constructor adds an item to the in-memory database if one doesn't exist.</span></span>

## <a name="getting-to-do-items"></a><span data-ttu-id="a6796-107">Obtention des éléments d’action</span><span class="sxs-lookup"><span data-stu-id="a6796-107">Getting to-do items</span></span>

<span data-ttu-id="a6796-108">Pour obtenir les éléments d’action, ajoutez les méthodes suivantes à la classe `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="a6796-108">To get to-do items, add the following methods to the `TodoController` class.</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="a6796-109">Ces méthodes implémentent les deux méthodes GET :</span><span class="sxs-lookup"><span data-stu-id="a6796-109">These methods implement the two GET methods:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="a6796-110">Voici un exemple de réponse HTTP pour la méthode `GetAll` :</span><span class="sxs-lookup"><span data-stu-id="a6796-110">Here is an example HTTP response for the `GetAll` method:</span></span>

```
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
   ```

<span data-ttu-id="a6796-111">Dans la suite de ce didacticiel, je vous montrerai comment afficher la réponse HTTP avec [Postman](https://www.getpostman.com/) ou [curl](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/curl.1.html).</span><span class="sxs-lookup"><span data-stu-id="a6796-111">Later in the tutorial I'll show how the HTTP response can be viewed with [Postman](https://www.getpostman.com/) or [curl](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/curl.1.html).</span></span>

### <a name="routing-and-url-paths"></a><span data-ttu-id="a6796-112">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="a6796-112">Routing and URL paths</span></span>

<span data-ttu-id="a6796-113">L’attribut `[HttpGet]` spécifie une méthode GET HTTP.</span><span class="sxs-lookup"><span data-stu-id="a6796-113">The `[HttpGet]` attribute specifies an HTTP GET method.</span></span> <span data-ttu-id="a6796-114">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="a6796-114">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="a6796-115">Prenez le modèle de chaîne dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="a6796-115">Take the template string in the controller’s `Route` attribute:</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="a6796-116">Remplacez « [Controller] » par le nom du contrôleur, qui est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="a6796-116">Replace "[Controller]" with the name of the controller, which is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="a6796-117">Pour cet exemple, le nom de classe du contrôleur est **Todo**Controller et le nom racine est « todo ».</span><span class="sxs-lookup"><span data-stu-id="a6796-117">For this sample, the controller class name is **Todo**Controller and the root name is "todo".</span></span> <span data-ttu-id="a6796-118">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="a6796-118">ASP.NET Core [routing](xref:mvc/controllers/routing) is not case sensitive.</span></span>
* <span data-ttu-id="a6796-119">Si l’attribut `[HttpGet]` a un modèle de route (comme `[HttpGet("/products")]`, ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="a6796-119">If the `[HttpGet]` attribute has a route template (such as `[HttpGet("/products")]`, append that to the path.</span></span> <span data-ttu-id="a6796-120">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="a6796-120">This sample doesn't use a template.</span></span> <span data-ttu-id="a6796-121">Pour plus d’informations, consultez [Routage d’attribut avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="a6796-121">See [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes) for more information.</span></span>

<span data-ttu-id="a6796-122">Dans la méthode `GetById` :</span><span class="sxs-lookup"><span data-stu-id="a6796-122">In the `GetById` method:</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

<span data-ttu-id="a6796-123">`"{id}"` est une variable d’espace réservé pour l’ID de l’élément `todo`.</span><span class="sxs-lookup"><span data-stu-id="a6796-123">`"{id}"` is a placeholder variable for the ID of the `todo` item.</span></span> <span data-ttu-id="a6796-124">Quand `GetById` est appelée, elle affecte la valeur de « {id} » dans l’URL au paramètre `id` de la méthode.</span><span class="sxs-lookup"><span data-stu-id="a6796-124">When `GetById` is invoked, it assigns the value of "{id}" in the URL to the method's `id` parameter.</span></span>

<span data-ttu-id="a6796-125">`Name = "GetTodo"` crée un itinéraire nommé.</span><span class="sxs-lookup"><span data-stu-id="a6796-125">`Name = "GetTodo"` creates a named route.</span></span> <span data-ttu-id="a6796-126">Les itinéraires nommés :</span><span class="sxs-lookup"><span data-stu-id="a6796-126">Named routes:</span></span>

* <span data-ttu-id="a6796-127">permettent à l’application créer une liaison HTTP à l’aide du nom de l’itinéraire ;</span><span class="sxs-lookup"><span data-stu-id="a6796-127">Enable the app to create an HTTP link using the route name.</span></span>
* <span data-ttu-id="a6796-128">sont expliqués dans la suite du didacticiel.</span><span class="sxs-lookup"><span data-stu-id="a6796-128">Are explained later in the tutorial.</span></span>

### <a name="return-values"></a><span data-ttu-id="a6796-129">Valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="a6796-129">Return values</span></span>

<span data-ttu-id="a6796-130">La méthode `GetAll` retourne un `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="a6796-130">The `GetAll` method returns an `IEnumerable`.</span></span> <span data-ttu-id="a6796-131">MVC sérialise automatiquement l’objet en [JSON](http://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="a6796-131">MVC automatically serializes the object to [JSON](http://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="a6796-132">Le code de réponse pour cette méthode est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="a6796-132">The response code for this method is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="a6796-133">(Les exceptions non gérées sont converties en erreurs 5xx.)</span><span class="sxs-lookup"><span data-stu-id="a6796-133">(Unhandled exceptions are translated into 5xx errors.)</span></span>

<span data-ttu-id="a6796-134">En revanche, la méthode `GetById` retourne le type plus général `IActionResult`, qui représente une grande variété de types de retour.</span><span class="sxs-lookup"><span data-stu-id="a6796-134">In contrast, the `GetById` method returns the more general `IActionResult` type, which represents a wide range of return types.</span></span> <span data-ttu-id="a6796-135">`GetById` a deux types de retour différents :</span><span class="sxs-lookup"><span data-stu-id="a6796-135">`GetById` has two different return types:</span></span>

* <span data-ttu-id="a6796-136">Si aucun élément ne correspond à l’ID demandé, la méthode retourne une erreur 404.</span><span class="sxs-lookup"><span data-stu-id="a6796-136">If no item matches the requested ID, the method returns a 404 error.</span></span> <span data-ttu-id="a6796-137">Si l’on renvoie `NotFound`, une réponse HTTP 404 est renvoyée.</span><span class="sxs-lookup"><span data-stu-id="a6796-137">Returning `NotFound` returns an HTTP 404 response.</span></span>

* <span data-ttu-id="a6796-138">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="a6796-138">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="a6796-139">Si l’on renvoie `ObjectResult`, une réponse HTTP 200 est renvoyée.</span><span class="sxs-lookup"><span data-stu-id="a6796-139">Returning `ObjectResult` returns an HTTP 200 response.</span></span>

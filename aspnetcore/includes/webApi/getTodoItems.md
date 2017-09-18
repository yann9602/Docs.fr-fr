<span data-ttu-id="f64e4-101">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]</span><span class="sxs-lookup"><span data-stu-id="f64e4-101">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]</span></span>

<span data-ttu-id="f64e4-102">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="f64e4-102">The preceding code:</span></span>

* <span data-ttu-id="f64e4-103">Définit une classe de contrôleur vide.</span><span class="sxs-lookup"><span data-stu-id="f64e4-103">Defines an empty controller class.</span></span> <span data-ttu-id="f64e4-104">Dans les sections suivantes, nous ajoutons des méthodes pour implémenter l’API.</span><span class="sxs-lookup"><span data-stu-id="f64e4-104">In the next sections, we'll add methods to implement the API.</span></span>
* <span data-ttu-id="f64e4-105">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`TodoContext `) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f64e4-105">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`TodoContext `) into the controller.</span></span> <span data-ttu-id="f64e4-106">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f64e4-106">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="f64e4-107">Le constructeur ajoute un élément à la base de données en mémoire s’il n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="f64e4-107">The constructor adds an item to the in-memory database if one doesn't exist.</span></span>

## <a name="getting-to-do-items"></a><span data-ttu-id="f64e4-108">Obtention des éléments d’action</span><span class="sxs-lookup"><span data-stu-id="f64e4-108">Getting to-do items</span></span>

<span data-ttu-id="f64e4-109">Pour obtenir les éléments d’action, ajoutez les méthodes suivantes à la classe `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="f64e4-109">To get to-do items, add the following methods to the `TodoController` class.</span></span>

<span data-ttu-id="f64e4-110">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]</span><span class="sxs-lookup"><span data-stu-id="f64e4-110">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]</span></span>

<span data-ttu-id="f64e4-111">Ces méthodes implémentent les deux méthodes GET :</span><span class="sxs-lookup"><span data-stu-id="f64e4-111">These methods implement the two GET methods:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="f64e4-112">Voici un exemple de réponse HTTP pour la méthode `GetAll` :</span><span class="sxs-lookup"><span data-stu-id="f64e4-112">Here is an example HTTP response for the `GetAll` method:</span></span>

```
HTTP/1.1 200 OK
   Content-Type: application/json; charset=utf-8
   Server: Microsoft-IIS/10.0
   Date: Thu, 18 Jun 2015 20:51:10 GMT
   Content-Length: 82

   [{"Key":"1", "Name":"Item1","IsComplete":false}]
   ```

<span data-ttu-id="f64e4-113">Plus loin dans ce didacticiel, je vous montre comment vous pouvez afficher la réponse HTTP en utilisant [Postman](https://www.getpostman.com/) ou [curl](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/curl.1.html).</span><span class="sxs-lookup"><span data-stu-id="f64e4-113">Later in the tutorial I'll show how you can view the HTTP response using [Postman](https://www.getpostman.com/) or [curl](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/curl.1.html).</span></span>

### <a name="routing-and-url-paths"></a><span data-ttu-id="f64e4-114">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="f64e4-114">Routing and URL paths</span></span>

<span data-ttu-id="f64e4-115">L’attribut `[HttpGet]` spécifie une méthode GET HTTP.</span><span class="sxs-lookup"><span data-stu-id="f64e4-115">The `[HttpGet]` attribute specifies an HTTP GET method.</span></span> <span data-ttu-id="f64e4-116">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="f64e4-116">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f64e4-117">Prenez le modèle de chaîne dans l’attribut route du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="f64e4-117">Take the template string in the controller’s route attribute:</span></span>

<span data-ttu-id="f64e4-118">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="f64e4-118">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]</span></span>

* <span data-ttu-id="f64e4-119">Remplacez « [Controller] » par le nom du contrôleur, qui est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="f64e4-119">Replace "[Controller]" with the name of the controller, which is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f64e4-120">Pour cet exemple, le nom de classe du contrôleur est **Todo**Controller et le nom racine est « todo ».</span><span class="sxs-lookup"><span data-stu-id="f64e4-120">For this sample, the controller class name is **Todo**Controller and the root name is "todo".</span></span> <span data-ttu-id="f64e4-121">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="f64e4-121">ASP.NET Core [routing](xref:mvc/controllers/routing) is not case sensitive.</span></span>
* <span data-ttu-id="f64e4-122">Si l’attribut `[HttpGet]` a un modèle de route (comme `[HttpGet("/products")]`, ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="f64e4-122">If the `[HttpGet]` attribute has a route template (such as `[HttpGet("/products")]`, append that to the path.</span></span> <span data-ttu-id="f64e4-123">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="f64e4-123">This sample doesn't use a template.</span></span> <span data-ttu-id="f64e4-124">Pour plus d’informations, consultez [Routage d’attribut avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f64e4-124">See [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes) for more information.</span></span>

<span data-ttu-id="f64e4-125">Dans la méthode `GetById` :</span><span class="sxs-lookup"><span data-stu-id="f64e4-125">In the `GetById` method:</span></span>

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(long id)
```

<span data-ttu-id="f64e4-126">`"{id}"` est une variable d’espace réservé pour l’ID de l’élément `todo`.</span><span class="sxs-lookup"><span data-stu-id="f64e4-126">`"{id}"` is a placeholder variable for the ID of the `todo` item.</span></span> <span data-ttu-id="f64e4-127">Quand `GetById` est appelée, elle affecte la valeur de « {id} » dans l’URL au paramètre `id` de la méthode.</span><span class="sxs-lookup"><span data-stu-id="f64e4-127">When `GetById` is invoked, it assigns the value of "{id}" in the URL to the method's `id` parameter.</span></span>

<span data-ttu-id="f64e4-128">`Name = "GetTodo"` crée une route nommée et vous permet de lier à cette route dans une réponse HTTP.</span><span class="sxs-lookup"><span data-stu-id="f64e4-128">`Name = "GetTodo"` creates a named route and allows you to link to this route in an HTTP Response.</span></span> <span data-ttu-id="f64e4-129">J’expliquerai ceci plus loin avec un exemple.</span><span class="sxs-lookup"><span data-stu-id="f64e4-129">I'll explain it with an example later.</span></span> <span data-ttu-id="f64e4-130">Pour plus d’informations, consultez [Routage vers les actions du contrôleur](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="f64e4-130">See [Routing to Controller Actions](xref:mvc/controllers/routing) for detailed information.</span></span>

### <a name="return-values"></a><span data-ttu-id="f64e4-131">Valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="f64e4-131">Return values</span></span>

<span data-ttu-id="f64e4-132">La méthode `GetAll` retourne un `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="f64e4-132">The `GetAll` method returns an `IEnumerable`.</span></span> <span data-ttu-id="f64e4-133">MVC sérialise automatiquement l’objet en [JSON](http://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="f64e4-133">MVC automatically serializes the object to [JSON](http://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f64e4-134">Le code de réponse pour cette méthode est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="f64e4-134">The response code for this method is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f64e4-135">(Les exceptions non gérées sont converties en erreurs 5xx.)</span><span class="sxs-lookup"><span data-stu-id="f64e4-135">(Unhandled exceptions are translated into 5xx errors.)</span></span>

<span data-ttu-id="f64e4-136">En revanche, la méthode `GetById` retourne le type plus général `IActionResult`, qui représente une grande variété de types de retour.</span><span class="sxs-lookup"><span data-stu-id="f64e4-136">In contrast, the `GetById` method returns the more general `IActionResult` type, which represents a wide range of return types.</span></span> <span data-ttu-id="f64e4-137">`GetById` a deux types de retour différents :</span><span class="sxs-lookup"><span data-stu-id="f64e4-137">`GetById` has two different return types:</span></span>

* <span data-ttu-id="f64e4-138">Si aucun élément ne correspond à l’ID demandé, la méthode retourne une erreur 404.</span><span class="sxs-lookup"><span data-stu-id="f64e4-138">If no item matches the requested ID, the method returns a 404 error.</span></span>  <span data-ttu-id="f64e4-139">Ceci se fait en retournant `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="f64e4-139">This is done by returning `NotFound`.</span></span>

* <span data-ttu-id="f64e4-140">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="f64e4-140">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f64e4-141">Ceci se fait en retournant `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="f64e4-141">This is done by returning an `ObjectResult`</span></span>

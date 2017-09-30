## <a name="implement-the-other-crud-operations"></a><span data-ttu-id="0a45c-101">Implémenter les autres opérations CRUD</span><span class="sxs-lookup"><span data-stu-id="0a45c-101">Implement the other CRUD operations</span></span>

<span data-ttu-id="0a45c-102">Nous allons ajouter les méthodes `Create`, `Update` et `Delete` au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="0a45c-102">We'll add `Create`, `Update`, and `Delete` methods to the controller.</span></span> <span data-ttu-id="0a45c-103">Comme il s’agit de variations sur un même thème, je vais simplement montrer le code et mettre en évidence les principales différences.</span><span class="sxs-lookup"><span data-stu-id="0a45c-103">These are variations on a theme, so I'll just show the code and highlight the main differences.</span></span> <span data-ttu-id="0a45c-104">Générez le projet après avoir ajouté ou modifié du code.</span><span class="sxs-lookup"><span data-stu-id="0a45c-104">Build the project after adding or changing code.</span></span>

### <a name="create"></a><span data-ttu-id="0a45c-105">Créer</span><span class="sxs-lookup"><span data-stu-id="0a45c-105">Create</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="0a45c-106">Il s’agit d’une méthode HTTP POST, indiquée par l’attribut [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="0a45c-106">This is an HTTP POST method, indicated by the [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="0a45c-107">L’attribut [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) indique à MVC qu’il faut obtenir la valeur de l’élément d’action à partir du corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0a45c-107">The [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) attribute tells MVC to get the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0a45c-108">La méthode `CreatedAtRoute` retourne une réponse 201, qui constitue la réponse standard pour une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0a45c-108">The `CreatedAtRoute` method returns a 201 response, which is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="0a45c-109">`CreatedAtRoute` ajoute également un en-tête Location à la réponse.</span><span class="sxs-lookup"><span data-stu-id="0a45c-109">`CreatedAtRoute` also adds a Location header to the response.</span></span> <span data-ttu-id="0a45c-110">L’en-tête Location spécifie l’URI de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="0a45c-110">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="0a45c-111">Consultez [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0a45c-111">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

### <a name="use-postman-to-send-a-create-request"></a><span data-ttu-id="0a45c-112">Utiliser Postman pour envoyer une requête Create</span><span class="sxs-lookup"><span data-stu-id="0a45c-112">Use Postman to send a Create request</span></span>

![Console Postman](../../tutorials/first-web-api/_static/pmc.png)

* <span data-ttu-id="0a45c-114">Affectez la valeur `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="0a45c-114">Set the HTTP method to `POST`</span></span>
* <span data-ttu-id="0a45c-115">Sélectionnez la case d’option **Body**.</span><span class="sxs-lookup"><span data-stu-id="0a45c-115">Select the **Body** radio button</span></span>
* <span data-ttu-id="0a45c-116">Sélectionnez la case d’option **raw**.</span><span class="sxs-lookup"><span data-stu-id="0a45c-116">Select the **raw** radio button</span></span>
* <span data-ttu-id="0a45c-117">Sélectionnez le type JSON.</span><span class="sxs-lookup"><span data-stu-id="0a45c-117">Set the type to JSON</span></span>
* <span data-ttu-id="0a45c-118">Dans l’éditeur de clé-valeur, entrez un élément d’action comme</span><span class="sxs-lookup"><span data-stu-id="0a45c-118">In the key-value editor, enter a Todo item such as</span></span> 

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* <span data-ttu-id="0a45c-119">Sélectionnez **Send**.</span><span class="sxs-lookup"><span data-stu-id="0a45c-119">Select **Send**</span></span>

* <span data-ttu-id="0a45c-120">Sélectionnez l’onglet Headers dans le volet inférieur et copiez l’en-tête **Location** :</span><span class="sxs-lookup"><span data-stu-id="0a45c-120">Select the Headers tab in the lower pane and copy the **Location** header:</span></span>

![Onglet Headers de la console Postman](../../tutorials/first-web-api/_static/pmget.png)

<span data-ttu-id="0a45c-122">Vous pouvez utiliser l’URI d’en-tête Location pour accéder à la ressource que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="0a45c-122">You can use the Location header URI to access the resource you just created.</span></span> <span data-ttu-id="0a45c-123">Rappelez la méthode `GetById` qui a créé la route nommée `"GetTodo"` :</span><span class="sxs-lookup"><span data-stu-id="0a45c-123">Recall the `GetById` method created the `"GetTodo"` named route:</span></span>

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(long id)
```

### <a name="update"></a><span data-ttu-id="0a45c-124">Mise à jour</span><span class="sxs-lookup"><span data-stu-id="0a45c-124">Update</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="0a45c-125">`Update` est similaire à `Create`, mais utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="0a45c-125">`Update` is similar to `Create`, but uses HTTP PUT.</span></span> <span data-ttu-id="0a45c-126">La réponse est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0a45c-126">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0a45c-127">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les différences.</span><span class="sxs-lookup"><span data-stu-id="0a45c-127">According to the HTTP spec, a PUT request requires the client to send the entire updated entity, not just the deltas.</span></span> <span data-ttu-id="0a45c-128">Pour prendre en charge les mises à jour partielles, utilisez HTTP PATCH.</span><span class="sxs-lookup"><span data-stu-id="0a45c-128">To support partial updates, use HTTP PATCH.</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](../../tutorials/first-web-api/_static/pmcput.png)

### <a name="delete"></a><span data-ttu-id="0a45c-130">Supprimer</span><span class="sxs-lookup"><span data-stu-id="0a45c-130">Delete</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="0a45c-131">La réponse est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0a45c-131">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](../../tutorials/first-web-api/_static/pmd.png)

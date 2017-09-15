## <a name="implement-the-other-crud-operations"></a><span data-ttu-id="42940-101">Implémenter les autres opérations CRUD</span><span class="sxs-lookup"><span data-stu-id="42940-101">Implement the other CRUD operations</span></span>

<span data-ttu-id="42940-102">Nous allons ajouter les méthodes `Create`, `Update` et `Delete` au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="42940-102">We'll add `Create`, `Update`, and `Delete` methods to the controller.</span></span> <span data-ttu-id="42940-103">Comme il s’agit de variations sur un même thème, je vais simplement montrer le code et mettre en évidence les principales différences.</span><span class="sxs-lookup"><span data-stu-id="42940-103">These are variations on a theme, so I'll just show the code and highlight the main differences.</span></span> <span data-ttu-id="42940-104">Générez le projet après avoir ajouté ou modifié du code.</span><span class="sxs-lookup"><span data-stu-id="42940-104">Build the project after adding or changing code.</span></span>

### <a name="create"></a><span data-ttu-id="42940-105">Créer</span><span class="sxs-lookup"><span data-stu-id="42940-105">Create</span></span>

<span data-ttu-id="42940-106">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]</span><span class="sxs-lookup"><span data-stu-id="42940-106">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]</span></span>

<span data-ttu-id="42940-107">Il s’agit d’une méthode HTTP POST, indiquée par l’attribut [`[HttpPost]`](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/HttpPostAttribute/index.html).</span><span class="sxs-lookup"><span data-stu-id="42940-107">This is an HTTP POST method, indicated by the [`[HttpPost]`](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/HttpPostAttribute/index.html) attribute.</span></span> <span data-ttu-id="42940-108">L’attribut [`[FromBody]`](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/FromBodyAttribute/index.html) indique à MVC qu’il faut obtenir la valeur de l’élément d’action à partir du corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="42940-108">The [`[FromBody]`](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/FromBodyAttribute/index.html) attribute tells MVC to get the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="42940-109">La méthode `CreatedAtRoute` retourne une réponse 201, qui constitue la réponse standard pour une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="42940-109">The `CreatedAtRoute` method returns a 201 response, which is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="42940-110">`CreatedAtRoute` ajoute également un en-tête Location à la réponse.</span><span class="sxs-lookup"><span data-stu-id="42940-110">`CreatedAtRoute` also adds a Location header to the response.</span></span> <span data-ttu-id="42940-111">L’en-tête Location spécifie l’URI de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="42940-111">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="42940-112">Consultez [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="42940-112">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

### <a name="use-postman-to-send-a-create-request"></a><span data-ttu-id="42940-113">Utiliser Postman pour envoyer une requête Create</span><span class="sxs-lookup"><span data-stu-id="42940-113">Use Postman to send a Create request</span></span>

![Console Postman](../../tutorials/first-web-api/_static/pmc.png)

* <span data-ttu-id="42940-115">Affectez la valeur `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="42940-115">Set the HTTP method to `POST`</span></span>
* <span data-ttu-id="42940-116">Sélectionnez la case d’option **Body**.</span><span class="sxs-lookup"><span data-stu-id="42940-116">Select the **Body** radio button</span></span>
* <span data-ttu-id="42940-117">Sélectionnez la case d’option **raw**.</span><span class="sxs-lookup"><span data-stu-id="42940-117">Select the **raw** radio button</span></span>
* <span data-ttu-id="42940-118">Sélectionnez le type JSON.</span><span class="sxs-lookup"><span data-stu-id="42940-118">Set the type to JSON</span></span>
* <span data-ttu-id="42940-119">Dans l’éditeur de clé-valeur, entrez un élément d’action comme</span><span class="sxs-lookup"><span data-stu-id="42940-119">In the key-value editor, enter a Todo item such as</span></span> 

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* <span data-ttu-id="42940-120">Sélectionnez **Send**.</span><span class="sxs-lookup"><span data-stu-id="42940-120">Select **Send**</span></span>

* <span data-ttu-id="42940-121">Sélectionnez l’onglet Headers dans le volet inférieur et copiez l’en-tête **Location** :</span><span class="sxs-lookup"><span data-stu-id="42940-121">Select the Headers tab in the lower pane and copy the **Location** header:</span></span>

![Onglet Headers de la console Postman](../../tutorials/first-web-api/_static/pmget.png)

<span data-ttu-id="42940-123">Vous pouvez utiliser l’URI d’en-tête Location pour accéder à la ressource que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="42940-123">You can use the Location header URI to access the resource you just created.</span></span> <span data-ttu-id="42940-124">Rappelez la méthode `GetById` qui a créé la route nommée `"GetTodo"` :</span><span class="sxs-lookup"><span data-stu-id="42940-124">Recall the `GetById` method created the `"GetTodo"` named route:</span></span>

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(long id)
```

### <a name="update"></a><span data-ttu-id="42940-125">Mise à jour</span><span class="sxs-lookup"><span data-stu-id="42940-125">Update</span></span>

<span data-ttu-id="42940-126">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]</span><span class="sxs-lookup"><span data-stu-id="42940-126">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]</span></span>

<span data-ttu-id="42940-127">`Update` est similaire à `Create`, mais utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="42940-127">`Update` is similar to `Create`, but uses HTTP PUT.</span></span> <span data-ttu-id="42940-128">La réponse est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="42940-128">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="42940-129">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les différences.</span><span class="sxs-lookup"><span data-stu-id="42940-129">According to the HTTP spec, a PUT request requires the client to send the entire updated entity, not just the deltas.</span></span> <span data-ttu-id="42940-130">Pour prendre en charge les mises à jour partielles, utilisez HTTP PATCH.</span><span class="sxs-lookup"><span data-stu-id="42940-130">To support partial updates, use HTTP PATCH.</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](../../tutorials/first-web-api/_static/pmcput.png)

### <a name="delete"></a><span data-ttu-id="42940-132">Supprimer</span><span class="sxs-lookup"><span data-stu-id="42940-132">Delete</span></span>

<span data-ttu-id="42940-133">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]</span><span class="sxs-lookup"><span data-stu-id="42940-133">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]</span></span>

<span data-ttu-id="42940-134">La réponse est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="42940-134">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](../../tutorials/first-web-api/_static/pmd.png)

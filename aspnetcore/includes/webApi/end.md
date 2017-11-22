## <a name="implement-the-other-crud-operations"></a><span data-ttu-id="c986e-101">Implémenter les autres opérations CRUD</span><span class="sxs-lookup"><span data-stu-id="c986e-101">Implement the other CRUD operations</span></span>

<span data-ttu-id="c986e-102">Dans les sections suivantes, les méthodes `Create`, `Update` et `Delete` sont ajoutées au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c986e-102">In the following sections, `Create`, `Update`, and `Delete` methods are added to the controller.</span></span>

### <a name="create"></a><span data-ttu-id="c986e-103">Créer</span><span class="sxs-lookup"><span data-stu-id="c986e-103">Create</span></span>

<span data-ttu-id="c986e-104">Ajoutez la méthode `Create` suivante.</span><span class="sxs-lookup"><span data-stu-id="c986e-104">Add the following `Create` method.</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="c986e-105">Le code précédent est une méthode HTTP POST, indiquée par l’attribut [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="c986e-105">The preceding code is an HTTP POST method, indicated by the [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="c986e-106">L’attribut [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) indique à MVC qu’il faut obtenir la valeur de l’élément d’action à partir du corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="c986e-106">The [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) attribute tells MVC to get the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c986e-107">La méthode `CreatedAtRoute` :</span><span class="sxs-lookup"><span data-stu-id="c986e-107">The `CreatedAtRoute` method:</span></span>

* <span data-ttu-id="c986e-108">Retourne une réponse 201.</span><span class="sxs-lookup"><span data-stu-id="c986e-108">Returns a 201 response.</span></span> <span data-ttu-id="c986e-109">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="c986e-109">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c986e-110">Ajoute un en-tête Location à la réponse.</span><span class="sxs-lookup"><span data-stu-id="c986e-110">Adds a Location header to the response.</span></span> <span data-ttu-id="c986e-111">L’en-tête Location spécifie l’URI de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="c986e-111">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="c986e-112">Consultez [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c986e-112">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c986e-113">Utilise l’itinéraire nommé « GetTodo » pour créer l’URL.</span><span class="sxs-lookup"><span data-stu-id="c986e-113">Uses the "GetTodo" named route to create the URL.</span></span> <span data-ttu-id="c986e-114">L’itinéraire nommé « GetTodo » est défini dans `GetById` :</span><span class="sxs-lookup"><span data-stu-id="c986e-114">The "GetTodo" named route is defined in `GetById`:</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="use-postman-to-send-a-create-request"></a><span data-ttu-id="c986e-115">Utiliser Postman pour envoyer une requête Create</span><span class="sxs-lookup"><span data-stu-id="c986e-115">Use Postman to send a Create request</span></span>

![Console Postman](../../tutorials/first-web-api/_static/pmc.png)

* <span data-ttu-id="c986e-117">Affectez la valeur `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="c986e-117">Set the HTTP method to `POST`</span></span>
* <span data-ttu-id="c986e-118">Sélectionnez la case d’option **Body**.</span><span class="sxs-lookup"><span data-stu-id="c986e-118">Select the **Body** radio button</span></span>
* <span data-ttu-id="c986e-119">Sélectionnez la case d’option **raw**.</span><span class="sxs-lookup"><span data-stu-id="c986e-119">Select the **raw** radio button</span></span>
* <span data-ttu-id="c986e-120">Sélectionnez le type JSON.</span><span class="sxs-lookup"><span data-stu-id="c986e-120">Set the type to JSON</span></span>
* <span data-ttu-id="c986e-121">Dans l’éditeur de clé-valeur, entrez un élément d’action comme</span><span class="sxs-lookup"><span data-stu-id="c986e-121">In the key-value editor, enter a Todo item such as</span></span>

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* <span data-ttu-id="c986e-122">Sélectionnez **Send**.</span><span class="sxs-lookup"><span data-stu-id="c986e-122">Select **Send**</span></span>
* <span data-ttu-id="c986e-123">Sélectionnez l’onglet Headers dans le volet inférieur et copiez l’en-tête **Location** :</span><span class="sxs-lookup"><span data-stu-id="c986e-123">Select the Headers tab in the lower pane and copy the **Location** header:</span></span>

![Onglet Headers de la console Postman](../../tutorials/first-web-api/_static/pmget.png)

<span data-ttu-id="c986e-125">L’URI d’en-tête d’emplacement permet d’accéder au nouvel élément.</span><span class="sxs-lookup"><span data-stu-id="c986e-125">The Location header URI can be used to access the new item.</span></span>

### <a name="update"></a><span data-ttu-id="c986e-126">Mise à jour</span><span class="sxs-lookup"><span data-stu-id="c986e-126">Update</span></span>

<span data-ttu-id="c986e-127">Ajoutez la méthode `Update` suivante :</span><span class="sxs-lookup"><span data-stu-id="c986e-127">Add the following `Update` method:</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="c986e-128">`Update` est similaire à `Create`, mais utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="c986e-128">`Update` is similar to `Create`, but uses HTTP PUT.</span></span> <span data-ttu-id="c986e-129">La réponse est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c986e-129">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c986e-130">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les différences.</span><span class="sxs-lookup"><span data-stu-id="c986e-130">According to the HTTP spec, a PUT request requires the client to send the entire updated entity, not just the deltas.</span></span> <span data-ttu-id="c986e-131">Pour prendre en charge les mises à jour partielles, utilisez HTTP PATCH.</span><span class="sxs-lookup"><span data-stu-id="c986e-131">To support partial updates, use HTTP PATCH.</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](../../tutorials/first-web-api/_static/pmcput.png)

### <a name="delete"></a><span data-ttu-id="c986e-133">Supprimer</span><span class="sxs-lookup"><span data-stu-id="c986e-133">Delete</span></span>

<span data-ttu-id="c986e-134">Ajoutez la méthode `Delete` suivante :</span><span class="sxs-lookup"><span data-stu-id="c986e-134">Add the following `Delete` method:</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="c986e-135">La réponse `Delete` est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c986e-135">The `Delete` response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

<span data-ttu-id="c986e-136">Testez `Delete` :</span><span class="sxs-lookup"><span data-stu-id="c986e-136">Test `Delete`:</span></span> 

![Console Postman montrant la réponse 204 (Pas de contenu)](../../tutorials/first-web-api/_static/pmd.png)

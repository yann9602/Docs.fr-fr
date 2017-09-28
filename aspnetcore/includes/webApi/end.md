## <a name="implement-the-other-crud-operations"></a>Implémenter les autres opérations CRUD

Nous allons ajouter les méthodes `Create`, `Update` et `Delete` au contrôleur. Comme il s’agit de variations sur un même thème, je vais simplement montrer le code et mettre en évidence les principales différences. Générez le projet après avoir ajouté ou modifié du code.

### <a name="create"></a>Créer

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Il s’agit d’une méthode HTTP POST, indiquée par l’attribut [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute). L’attribut [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) indique à MVC qu’il faut obtenir la valeur de l’élément d’action à partir du corps de la requête HTTP.

La méthode `CreatedAtRoute` retourne une réponse 201, qui constitue la réponse standard pour une méthode HTTP POST qui crée une ressource sur le serveur. `CreatedAtRoute` ajoute également un en-tête Location à la réponse. L’en-tête Location spécifie l’URI de l’élément d’action qui vient d’être créé. Consultez [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

### <a name="use-postman-to-send-a-create-request"></a>Utiliser Postman pour envoyer une requête Create

![Console Postman](../../tutorials/first-web-api/_static/pmc.png)

* Affectez la valeur `POST` à la méthode HTTP.
* Sélectionnez la case d’option **Body**.
* Sélectionnez la case d’option **raw**.
* Sélectionnez le type JSON.
* Dans l’éditeur de clé-valeur, entrez un élément d’action comme 

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* Sélectionnez **Send**.

* Sélectionnez l’onglet Headers dans le volet inférieur et copiez l’en-tête **Location** :

![Onglet Headers de la console Postman](../../tutorials/first-web-api/_static/pmget.png)

Vous pouvez utiliser l’URI d’en-tête Location pour accéder à la ressource que vous venez de créer. Rappelez la méthode `GetById` qui a créé la route nommée `"GetTodo"` :

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(long id)
```

### <a name="update"></a>Mise à jour

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`Update` est similaire à `Create`, mais utilise HTTP PUT. La réponse est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les différences. Pour prendre en charge les mises à jour partielles, utilisez HTTP PATCH.

![Console Postman montrant la réponse 204 (Pas de contenu)](../../tutorials/first-web-api/_static/pmcput.png)

### <a name="delete"></a>Supprimer

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

La réponse est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

![Console Postman montrant la réponse 204 (Pas de contenu)](../../tutorials/first-web-api/_static/pmd.png)

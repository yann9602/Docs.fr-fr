[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Le code précédent :

* Définit une classe de contrôleur vide. Dans les sections suivantes, nous ajoutons des méthodes pour implémenter l’API.
* Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`TodoContext `) dans le contrôleur. Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.
* Le constructeur ajoute un élément à la base de données en mémoire s’il n’existe pas.

## <a name="getting-to-do-items"></a>Obtention des éléments d’action

Pour obtenir les éléments d’action, ajoutez les méthodes suivantes à la classe `TodoController`.

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Ces méthodes implémentent les deux méthodes GET :

* `GET /api/todo`
* `GET /api/todo/{id}`

Voici un exemple de réponse HTTP pour la méthode `GetAll` :

```
HTTP/1.1 200 OK
   Content-Type: application/json; charset=utf-8
   Server: Microsoft-IIS/10.0
   Date: Thu, 18 Jun 2015 20:51:10 GMT
   Content-Length: 82

   [{"Key":"1", "Name":"Item1","IsComplete":false}]
   ```

Plus loin dans ce didacticiel, je vous montre comment vous pouvez afficher la réponse HTTP en utilisant [Postman](https://www.getpostman.com/) ou [curl](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/curl.1.html).

### <a name="routing-and-url-paths"></a>Routage et chemins d’URL

L’attribut `[HttpGet]` spécifie une méthode GET HTTP. Le chemin d’URL pour chaque méthode est construit comme suit :

* Prenez le modèle de chaîne dans l’attribut route du contrôleur :

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Remplacez « [Controller] » par le nom du contrôleur, qui est le nom de la classe du contrôleur sans le suffixe « Controller ». Pour cet exemple, le nom de classe du contrôleur est **Todo**Controller et le nom racine est « todo ». Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.
* Si l’attribut `[HttpGet]` a un modèle de route (comme `[HttpGet("/products")]`, ajoutez-le au chemin. Cet exemple n’utilise pas de modèle. Pour plus d’informations, consultez [Routage d’attribut avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

Dans la méthode `GetById` :

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(long id)
```

`"{id}"` est une variable d’espace réservé pour l’ID de l’élément `todo`. Quand `GetById` est appelée, elle affecte la valeur de « {id} » dans l’URL au paramètre `id` de la méthode.

`Name = "GetTodo"` crée une route nommée et vous permet de lier à cette route dans une réponse HTTP. J’expliquerai ceci plus loin avec un exemple. Pour plus d’informations, consultez [Routage vers les actions du contrôleur](xref:mvc/controllers/routing).

### <a name="return-values"></a>Valeurs de retour

La méthode `GetAll` retourne un `IEnumerable`. MVC sérialise automatiquement l’objet en [JSON](http://www.json.org/) et écrit le JSON dans le corps du message de réponse. Le code de réponse pour cette méthode est 200, en supposant qu’il n’existe pas d’exception non gérée. (Les exceptions non gérées sont converties en erreurs 5xx.)

En revanche, la méthode `GetById` retourne le type plus général `IActionResult`, qui représente une grande variété de types de retour. `GetById` a deux types de retour différents :

* Si aucun élément ne correspond à l’ID demandé, la méthode retourne une erreur 404.  Ceci se fait en retournant `NotFound`.

* Sinon, la méthode retourne 200 avec un corps de réponse JSON. Ceci se fait en retournant `ObjectResult`.

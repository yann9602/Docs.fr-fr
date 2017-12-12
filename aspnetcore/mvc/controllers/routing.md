---
title: "Routage vers les Actions de contrôleur"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 26250a4d-bf62-4d45-8549-26801cf956e9
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/routing
ms.openlocfilehash: d6e230351eb2f4c8549b54d75fd8e345718e6109
ms.sourcegitcommit: 9ecd4e9fb0c40c3693dab079eab1ff94b461c922
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="routing-to-controller-actions"></a>Routage vers les Actions de contrôleur

Par [Ryan Nowak](https://github.com/rynowak) et [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core MVC utilise le routage [intergiciel (middleware)](../../fundamentals/middleware.md) pour faire correspondre les URL des demandes entrantes et de les mapper à des actions. Itinéraires sont définis dans le code de démarrage ou d’attributs. Itinéraires décrivent comment les chemins d’accès de l’URL doivent correspondre aux actions. Itinéraires sont également utilisés pour générer des URL (pour les liens) envoyés dans les réponses. 

Actions sont soit traditionnellement routées ou attribut routés. En plaçant un itinéraire sur le contrôleur ou de l’action, il attribut routé. Consultez [mixte routage](#routing-mixed-ref-label) pour plus d’informations.

Ce document explique les interactions entre MVC et de routage et de la création d’applications MVC utilise des fonctionnalités de routage. Consultez [routage](xref:fundamentals/routing) pour plus d’informations sur le routage avancé.

## <a name="setting-up-routing-middleware"></a>Configuration de routage intergiciel (middleware)

Dans votre *configurer* (méthode), vous pouvez voir code semblable à :

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

À l’intérieur de l’appel à `UseMvc`, `MapRoute` est utilisé pour créer un seul itinéraire, nous allons faire référence à la `default` itinéraire. La plupart des applications MVC utilisera un itinéraire avec un modèle semblable à la `default` itinéraire.

Le modèle d’itinéraire `"{controller=Home}/{action=Index}/{id?}"` peut correspondre à un chemin d’accès de l’URL comme `/Products/Details/5` et extrait les valeurs d’itinéraire `{ controller = Products, action = Details, id = 5 }` par le chemin d’accès de création de jetons. MVC tentent de trouver un contrôleur nommé `ProductsController` et exécuter l’action `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Notez que dans cet exemple, la liaison de modèle serait utiliser la valeur de `id = 5` pour définir le `id` paramètre `5` lors de l’appel de cette action. Consultez le [liaison de modèle](../models/model-binding.md) pour plus d’informations.

À l’aide de la `default` itinéraire :

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Le modèle d’itinéraire :

* `{controller=Home}`définit `Home` comme la valeur par défaut`controller`

* `{action=Index}`définit `Index` comme la valeur par défaut`action`

* `{id?}`définit `id` comme facultatifs

Valeur par défaut et les paramètres de routage facultatif n’avez pas besoin d’être présents dans le chemin d’accès d’URL pour une correspondance. Consultez [référence de modèle d’itinéraire](../../fundamentals/routing.md#route-template-reference) pour une description détaillée de la syntaxe de modèle d’itinéraire.

`"{controller=Home}/{action=Index}/{id?}"`peut faire correspondre le chemin d’accès URL `/` et produit les valeurs d’itinéraire `{ controller = Home, action = Index }`. Les valeurs de `controller` et `action` rendre utilisent des valeurs par défaut, `id` ne produisent pas de valeur, car il n’existe aucun segment correspondant dans le chemin d’accès d’URL. MVC Utilisez ces valeurs d’itinéraire pour sélectionner le `HomeController` et `Index` action :

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

À l’aide de cette définition du contrôleur et le modèle d’itinéraire, le `HomeController.Index` action devraient être exécutée pour un des chemins d’URL suivants :

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

La méthode pratique `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Peut être utilisé pour remplacer :

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc`et `UseMvcWithDefaultRoute` ajouter une instance de `RouterMiddleware` au pipeline intergiciel (middleware). MVC n’interagit pas directement avec un intergiciel (middleware) et utilise le routage pour gérer les demandes. MVC est connecté aux itinéraires via une instance de `MvcRouteHandler`. Le code à l’intérieur de `UseMvc` est semblable au suivant :

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`ne définit pas directement les itinéraires, il ajoute un espace réservé à la collection d’itinéraires pour le `attribute` itinéraire. La surcharge `UseMvc(Action<IRouteBuilder>)` vous permet d’ajouter vos propres itinéraires et prend également en charge le routage d’attributs.  `UseMvc`et toutes ses variations ajoute un espace réservé pour l’itinéraire d’attribut - attribut routage est toujours disponible, quelle que soit la façon dont vous configurez `UseMvc`. `UseMvcWithDefaultRoute`définit un itinéraire par défaut et prend en charge le routage d’attributs. Le [attribut routage](#attribute-routing-ref-label) comprend plus de détails sur le routage d’attributs.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Le routage classique

Le `default` itinéraire :

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

est un exemple d’un *le routage classique*. Nous appelons ce style *le routage classique* , car elle établit un *convention* des chemins d’accès de l’URL :

* le premier segment de chemin d’accès correspond au nom du contrôleur

* la seconde mappe le nom de l’action.

* le troisième segment est utilisé pour un élément facultatif `id` utilisée pour mapper à une entité de modèle

L’utilisation de cette `default` itinéraire, le chemin d’accès URL `/Products/List` mappe à la `ProductsController.List` action, et `/Blog/Article/17` mappe à `BlogController.Article`. Ce mappage est basé sur les noms de contrôleur et d’action **uniquement** et n’est pas basé sur les espaces de noms, les emplacements des fichiers sources ou les paramètres de méthode.

> [!TIP]
> À l’aide de routage classique avec l’itinéraire par défaut vous permet de créer rapidement de l’application sans avoir à élaborer un nouveau modèle d’URL pour chaque action que vous définissez. Pour une application avec des actions CRUD style, ayant la cohérence pour les URL entre vos contrôleurs peut aider à simplifier votre code et rendre votre interface utilisateur plus prévisible.

> [!WARNING]
> Le `id` est défini comme étant facultatifs par le modèle d’itinéraire, ce qui signifie que que vos actions peuvent s’exécuter sans l’ID fourni dans le cadre de l’URL. Généralement ce qui se produira si `id` est omis de l’URL est qu’elle est définie `0` par la liaison de modèle et par conséquent aucune entité ne se trouve dans la base de données de correspondance `id == 0`. Routage d’attributs vous donne un contrôle précis pour rendre le code requis pour certaines actions et pas pour d’autres. Par convention, la documentation inclut comme paramètres facultatifs `id` lorsqu’ils sont susceptibles d’apparaître dans une utilisation correcte.

## <a name="multiple-routes"></a>Plusieurs itinéraires

Vous pouvez ajouter plusieurs itinéraires à l’intérieur de `UseMvc` en ajoutant plusieurs appels de `MapRoute`. Cela vous permet de définir plusieurs conventions ou ajouter des itinéraires classiques qui sont dédiés à une action spécifique, tel que :

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Le `blog` itinéraire ici est un *dédié itinéraire conventionnelle*, ce qui signifie qu’il utilise le système de routage classique, mais est dédié à une action spécifique. Étant donné que `controller` et `action` n’apparaissent pas dans le modèle d’itinéraire en tant que paramètres, ils peuvent avoir uniquement les valeurs par défaut et par conséquent, cet itinéraire correspondra toujours à l’action `BlogController.Article`.

Itinéraires dans la collection d’itinéraires sont ordonnées et sont traités dans l’ordre qu’ils sont ajoutés. Ainsi, dans cet exemple, le `blog` itinéraire sera tenté avant le `default` itinéraire.

> [!NOTE]
> *Dédié itinéraires conventionnelles* utilisent souvent des paramètres d’itinéraire de fourre-tout comme `{*article}` pour capturer la partie restante du chemin d’accès d’URL. Cela peut rendre un itinéraire 'trop gourmande' ce qui signifie que qu’elle correspond à l’URL que vous souhaitez mettre en correspondance par d’autres itinéraires. Placez les itinéraires « gourmandes » plus loin dans la table d’itinéraires pour résoudre ce problème.

### <a name="fallback"></a>Secours

Dans le cadre du traitement de requête, MVC vérifie que les valeurs d’itinéraire peuvent être utilisés pour rechercher un contrôleur et une action dans votre application. Si les valeurs d’itinéraire ne correspond à une action alors l’itinéraire n’est pas considéré comme une correspondance, et l’itinéraire suivant sera tentée. Il s’agit *secours*, et il a conçu simplifier les cas où les itinéraires classiques se chevauchent.

### <a name="disambiguating-actions"></a>Actions désambiguïser

Lorsque deux actions correspondent par routage, MVC doit lever l’ambiguïté pour choisir le candidat « meilleur », sans quoi une exception. Exemple :

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Ce contrôleur définit deux actions qui correspondrait au chemin d’accès URL `/Products/Edit/17` et des données d’itinéraire `{ controller = Products, action = Edit, id = 17 }`. Il s’agit d’un modèle par défaut pour les contrôleurs MVC où `Edit(int)` présente un formulaire pour modifier un produit, et `Edit(int, Product)` traite le formulaire publié. Pour permettre cela MVC devra choisir `Edit(int, Product)` lorsque la demande est HTTP `POST` et `Edit(int)` lorsque le verbe HTTP est autre chose.

Le `HttpPostAttribute` ( `[HttpPost]` ) est une implémentation de `IActionConstraint` qui autorise uniquement l’action à être sélectionné lorsque le verbe HTTP est `POST`. La présence d’un `IActionConstraint` rend le `Edit(int, Product)` 'mieux' correspond à `Edit(int)`, de sorte que `Edit(int, Product)` sont essayés en premier.

Vous devrez écrire personnalisée `IActionConstraint` implémentations dans des scénarios spécifiques, mais il. il est important de comprendre le rôle d’attributs comme `HttpPostAttribute` -des attributs similaires sont définies pour les autres verbes HTTP. Dans le routage classique, il est courant pour les actions à utiliser le même nom d’action lorsqu’elles font partie d’un `show form -> submit form` flux de travail. L’avantage de ce modèle est deviennent plus évident après avoir examiné les [IActionConstraint de présentation](#understanding-iactionconstraint) section.

Si plusieurs itinéraires correspondent et MVC ne peut pas trouver un itinéraire « meilleur », elle lève une `AmbiguousActionException`.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Noms d’itinéraires

Les chaînes `"blog"` et `"default"` dans les exemples suivants sont des noms de l’itinéraire :


```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Les noms d’itinéraires nommez l’itinéraire logique afin que l’itinéraire nommé peut être utilisé pour la génération d’URL. Cela simplifie considérablement la création d’URL lorsque le classement des itinéraires peut rendre génération d’URL compliquée. Les noms d’itinéraires doivent être unique à l’échelle de l’application.

Les noms d’itinéraires n’ont aucun impact sur l’URL correspondant ou le traitement des requêtes ; ils sont utilisés uniquement pour la génération d’URL. [Routage](xref:fundamentals/routing) contient plus d’informations sur la génération d’URL, y compris la génération d’URL dans les programmes d’assistance MVC spécifiques.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Routage d’attributs

Routage d’attributs d’utilise un ensemble d’attributs pour mapper les actions directement aux modèles d’itinéraire. Dans l’exemple suivant, `app.UseMvc();` est utilisé dans le `Configure` (méthode) et aucun itinéraire n’est passée. Le `HomeController` correspond à un ensemble d’URL similaires à l’itinéraire par défaut `{controller=Home}/{action=Index}/{id?}` correspondrait à :

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

Le `HomeController.Index()` action sera exécutée pour un des chemins d’accès de l’URL `/`, `/Home`, ou `/Home/Index`.

> [!NOTE]
> Cet exemple met en surbrillance une programmation principale différence entre l’attribut et de routage classique. Routage d’attributs nécessite plus d’entrée pour spécifier un itinéraire ; l’itinéraire par défaut classiques gère plus succinctement les itinéraires. Toutefois, routage d’attributs permet (et requiert) d’un contrôle précis dont les modèles d’itinéraire s’appliquent à chaque action.

Avec l’attribut routage le nom du contrôleur et les noms d’actions lire **aucune** rôle dans lequel l’action est sélectionnée. Cet exemple correspond aux mêmes URL que l’exemple précédent.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Les modèles d’itinéraire ne définissent pas les paramètres d’itinéraire pour `action`, `area`, et `controller`. En fait, les paramètres d’itinéraire ne sont pas autorisés dans les itinéraires d’attribut. Étant donné que le modèle d’itinéraire est déjà associé à une action, il n’aurait aucun sens pour analyser le nom d’action de l’URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Attribut de routage avec des attributs Http [verbe]

Routage d’attributs peut s’utiliser de la `Http[Verb]` des attributs tels que `HttpPostAttribute`. Tous ces attributs peuvent accepter un modèle d’itinéraire. Cet exemple montre deux actions qui correspondent au modèle d’itinéraire même :

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Pour un chemin d’accès de l’URL comme `/products` le `ProductsApi.ListProducts` action sera exécutée lorsque le verbe HTTP est `GET` et `ProductsApi.CreateProduct` sera exécutée lorsque le verbe HTTP est `POST`. Tout d’abord le routage attribut correspond à l’URL par rapport au jeu de modèles d’itinéraire défini par les attributs d’itinéraire. Une fois qu’un modèle d’itinéraire correspond à, `IActionConstraint` les contraintes sont appliquées pour déterminer quelles actions peuvent être exécutées.

> [!TIP]
> Lors de la génération d’une API REST, il est rare que vous souhaitez utiliser `[Route(...)]` sur une méthode d’action. Il est préférable d’utiliser la plus spécifique `Http*Verb*Attributes` pour être précis sur ce que votre API prend en charge. Les clients d’API REST doivent connaître les chemins d’accès et les verbes HTTP mappent à des opérations logiques spécifiques.

Dans la mesure où un itinéraire d’attribut s’applique à une action spécifique, il est facile d’apporter des paramètres requis dans le cadre de la définition de modèle d’itinéraire. Dans cet exemple, `id` est requis dans le cadre du chemin d’accès d’URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Le `ProductsApi.GetProduct(int)` action sera exécutée pour un chemin d’accès de l’URL comme `/products/3` , mais pas pour un chemin d’accès de l’URL comme `/products`. Consultez [routage](../../fundamentals/routing.md) pour obtenir une description complète des modèles d’itinéraire et les options associées.

## <a name="route-name"></a>Nom de l’itinéraire

Le code suivant définit un *nom d’itinéraire* de `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Les noms d’itinéraires peuvent être utilisés pour générer une URL basée sur un itinéraire spécifique. Les noms d’itinéraires n’ont aucun impact sur l’URL de comportement de routage de correspondance et sont utilisés uniquement pour la génération d’URL. Les noms d’itinéraires doivent être unique à l’échelle de l’application.

> [!NOTE]
> Ceci contraste avec conventionnels *l’itinéraire par défaut*, qui définit le `id` paramètre comme facultatif (`{id?}`). Cette possibilité de spécifier précisément les API présente des avantages, par exemple pour autoriser `/products` et `/products/5` doit être distribué à des actions différentes.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Itinéraires de combinaison

Pour rendre attribut routage moins répétitives, attributs d’itinéraire sur le contrôleur sont combinées avec des attributs d’itinéraire sur les actions individuelles. Les modèles d’itinéraire définis sur le contrôleur sont ajoutés à des modèles d’itinéraire sur les actions. En plaçant un attribut d’itinéraire sur le contrôleur, **tous les** actions dans le contrôleur utilisent le routage d’attributs.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

Dans cet exemple, le chemin d’accès URL `/products` peut correspondre à `ProductsApi.ListProducts`et le chemin d’accès URL `/products/5` peut correspondre à `ProductsApi.GetProduct(int)`. Ces deux actions uniquement en correspondance HTTP `GET` , car elles sont décorées avec le `HttpGetAttribute`.

Acheminer les modèles appliqués à une action qui commencent par un `/` ne pas combiner avec les modèles d’itinéraire appliqués au contrôleur. Cet exemple correspond à un ensemble de chemins d’accès de URL semblables à la *l’itinéraire par défaut*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Does not combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Classement des itinéraires d’attribut

Contrairement aux itinéraires classiques qui s’exécutent dans un ordre défini, le routage d’attributs génère une arborescence et correspond à tous les itinéraires simultanément. Il se comporte comme-si les entrées d’itinéraire ont été placées dans un classement idéale ; les itinéraires plus spécifiques ont une chance de s’exécuter avant les itinéraires plus générales.

Par exemple, un itinéraire comme `blog/search/{topic}` est plus spécifique à un itinéraire comme `blog/{*article}`. Logiquement parlant le `blog/search/{topic}` itinéraire 'exécute' en premier lieu, par défaut, car il s’agit de l’ordre uniquement pratique. À l’aide de routage classique, le développeur est chargé pour la sélection élective des itinéraires dans l’ordre souhaité.

Itinéraires d’attribut peuvent configurer une commande, à l’aide de la `Order` propriété de tous les attributs d’itinéraire prévues. Les itinéraires sont traités selon un ordre croissant de le `Order` propriété. L’ordre par défaut est `0`. Définition d’un itinéraire à l’aide `Order = -1` s’exécutera avant les itinéraires qui ne définissent pas une commande. Définition d’un itinéraire à l’aide `Order = 1` s’exécutera après le classement d’itinéraire par défaut.

> [!TIP]
> Éviter de dépendre `Order`. Si votre espace URL requiert des valeurs d’ordre explicites pour acheminer correctement, il est probablement à confusion pour les clients. En général le routage d’attributs sélectionnera l’itinéraire correct avec l’URL correspondant. Si l’ordre par défaut utilisé pour la génération d’URL ne fonctionne pas, à l’aide de nom d’itinéraire comme une substitution est généralement plus simple que l’application le `Order` propriété.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Dans les modèles d’itinéraire du remplacement de jeton ([controller], [action], [domaine])

Pour plus de commodité, prend en charge les itinéraires d’attribut *du remplacement de jeton* en mettant un jeton entre accolades du carré (`[`, `]`). Les jetons `[action]`, `[area]`, et `[controller]` sera remplacé par les valeurs du nom d’action, nom de la zone et du nom de contrôleur à partir de l’action dans lequel l’itinéraire est défini. Dans cet exemple montre comment les actions peuvent correspondre à des chemins d’URL comme décrit dans les commentaires :

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Remplacement des jetons s’effectue lors de la dernière étape de la création d’itinéraires d’attribut. L’exemple ci-dessus sera se comportent comme le code suivant :

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Itinéraires d’attribut peuvent aussi être combinés avec héritage. Cela est particulièrement puissante combiné avec le remplacement des jetons.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPost("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

Remplacement des jetons s’applique également aux noms d’itinéraires définis par les itinéraires d’attribut. `[Route("[controller]/[action]", Name="[controller]_[action]")]`génère un nom d’itinéraire unique pour chaque action.

Pour faire correspondre le délimiteur de littéral remplacement des jetons `[` ou `]`, échapper en répétant le caractère (`[[` ou `]]`).

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Plusieurs itinéraires

Attribut de routage prend en charge la définition de plusieurs itinéraires pour atteindre la même action. L’utilisation la plus courante de ce consiste à reproduire le comportement de la *l’itinéraire par défaut classiques* comme indiqué dans l’exemple suivant :

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Placement de plusieurs attributs d’itinéraire sur le contrôleur signifie que chacun d’eux permet de combiner avec chacun des attributs d’itinéraire sur les méthodes d’action.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Lorsque plusieurs attributs d’itinéraire (qui implémentent `IActionConstraint`) sont placés sur une action, puis chaque contrainte action associe le modèle d’itinéraire à partir de l’attribut qui l’a défini.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> À l’aide de plusieurs itinéraires sur les actions permettre sembler puissant, il est préférable de conserver l’espace d’URL de votre application simple et bien définie. Utilisez plusieurs itinéraires sur les actions lorsque cela est nécessaire, par exemple, pour prendre en charge les clients existants.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Spécification des paramètres d’itinéraire d’attribut facultatif, les valeurs par défaut et les contraintes

Itinéraires d’attribut prend en charge la même syntaxe inline en tant qu’itinéraires classiques pour spécifier des paramètres facultatifs, les valeurs par défaut et les contraintes.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Consultez [référence de modèle d’itinéraire](../../fundamentals/routing.md#route-template-reference) pour une description détaillée de la syntaxe de modèle d’itinéraire.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Attributs d’itinéraire personnalisées à l’aide`IRouteTemplateProvider`

Tous les attributs d’itinéraire fournis dans le framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implémentent le `IRouteTemplateProvider` interface. MVC recherche les attributs sur les classes de contrôleur et les méthodes d’action lors de l’application démarre et utilise ceux qui implémentent `IRouteTemplateProvider` pour générer l’ensemble initial des itinéraires.

Vous pouvez implémenter `IRouteTemplateProvider` pour définir vos propres attributs d’itinéraire. Chaque `IRouteTemplateProvider` vous permet de définir une gamme unique avec un modèle d’itinéraire personnalisées, classer et nommer :

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

L’attribut de l’exemple ci-dessus définit automatiquement le `Template` à `"api/[controller]"` lorsque `[MyApiController]` est appliqué.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>À l’aide du modèle d’Application pour personnaliser les itinéraires d’attribut

Le *modèle d’application* est un modèle d’objet créé au démarrage avec toutes les métadonnées utilisée par MVC pour router et exécuter vos actions. Le *modèle d’application* inclut toutes les données collectées à partir des attributs d’itinéraire (via `IRouteTemplateProvider`). Vous pouvez écrire *conventions* pour modifier le modèle d’application au moment du démarrage pour personnaliser le comporte de routage. Cette section montre un exemple simple de personnalisation de routage à l’aide du modèle d’application.

[!code-csharp[Main](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Mixte de routage : routage classique de routage et d’attribut

Les applications MVC peuvent combiner l’utilisation de routage classique et le routage de l’attribut. Il est courant pour utiliser les itinéraires classiques pour les contrôleurs de servir des pages HTML pour les navigateurs et l’attribut de routage pour les contrôleurs servant d’API REST.

Actions sont soit traditionnellement routées ou attribut routés. En plaçant un itinéraire sur le contrôleur ou de l’action, il attribut routé. Les actions qui définissent des itinéraires d’attribut n’est pas accessible via les itinéraires classiques et vice versa. **N’importe quel** attribut d’itinéraire sur le contrôleur rend toutes les actions dans l’attribut de contrôleur routé.

> [!NOTE]
> Toute la différence entre les deux types de systèmes de routage consiste à appliquée après une URL correspond à un modèle d’itinéraire. Dans le routage classique, les valeurs d’itinéraire à partir de la correspondance sont utilisés pour choisir l’action et le contrôleur à partir d’une table de recherche de toutes les actions de routage classiques. Dans le routage de l’attribut, chaque modèle est déjà associé avec une action, et aucune recherche supplémentaire n’est nécessaire.

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Génération d’URL

Applications de MVC peuvent utiliser les fonctionnalités de génération de routage URL pour générer des URL des liens vers des actions. Génération des URL élimine les URL de coder en dur, rendre votre code plus robuste et plus facile à gérer. Cette section se concentre sur les fonctionnalités de génération d’URL fournies par MVC et couvre uniquement les principes de base du fonctionne de la génération d’URL. Consultez [routage](../../fundamentals/routing.md) pour obtenir une description détaillée de la génération d’URL.

Le `IUrlHelper` interface est l’élément sous-jacent de l’infrastructure entre MVC et le routage pour la génération d’URL. Vous trouverez une instance de `IUrlHelper` disponible via le `Url` propriété dans l’affichage des composants, des vues et des contrôleurs.

Dans cet exemple, le `IUrlHelper` interface est utilisée par le biais du `Controller.Url` propriété pour générer une URL vers une autre action.

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Si l’application est à l’aide de la valeur par défaut classique Router, la valeur de la `url` variable sera la chaîne de chemin d’accès d’URL `/UrlGeneration/Destination`. Ce chemin d’accès de l’URL est créé par le routage en combinant les valeurs d’itinéraire à partir de la demande en cours (valeurs ambiantes), avec les valeurs passées à `Url.Action` et en remplaçant les valeurs dans le modèle d’itinéraire :

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Chaque paramètre d’itinéraire dans le modèle d’itinéraire a sa valeur remplacée par la correspondance des noms avec les valeurs et ambiante. Un paramètre d’itinéraire qui n’a pas de valeur peut utiliser une valeur par défaut si elle existe, ou être ignorée si elle est facultative (comme dans le cas de `id` dans cet exemple). Génération d’URL échoue si n’importe quel paramètre d’itinéraire requis n’a pas une valeur correspondante. En cas de génération d’URL d’un itinéraire, l’itinéraire suivant est tentée jusqu'à ce que tous les itinéraires ont été essayées ou une correspondance est trouvée.

L’exemple de `Url.Action` ci-dessus suppose que le routage classique, mais fonctionne de génération d’URL de la même façon avec le routage de l’attribut, bien que les concepts sont différents. Avec le routage classique, les valeurs d’itinéraire sont utilisés pour développer un modèle et les valeurs d’itinéraire pour `controller` et `action` apparaissent généralement dans ce modèle - cela fonctionne parce que les URL mises en correspondance par le routage adhèrent à un *convention*. Dans le routage de l’attribut, les valeurs de l’itinéraire pour `controller` et `action` ne sont pas autorisés à afficher dans le modèle - ils sont utilisés à la place pour rechercher le modèle à utiliser.

Cet exemple utilise le routage d’attributs :

[!code-csharp[Main](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC génère une table de recherche de toutes les actions de l’attribut routé et correspond à la `controller` et `action` valeurs pour sélectionner le modèle d’itinéraire à utiliser pour la génération d’URL. Dans l’exemple ci-dessus, `custom/url/to/destination` est généré.

### <a name="generating-urls-by-action-name"></a>Génération des URL par le nom de l’action

`Url.Action` (`IUrlHelper` . `Action`) et toutes les surcharges tous sont basés sur cette idée que vous souhaitez spécifier ce que vous créez le lien en spécifiant un nom de contrôleur et le nom de l’action.

> [!NOTE]
> Lorsque vous utilisez `Url.Action`, les valeurs de l’itinéraire actuel pour `controller` et `action` sont spécifiées pour vous, la valeur de `controller` et `action` font partie des deux *valeurs ambiantes* **et** *valeurs*. La méthode `Url.Action`, utilise toujours les valeurs actuelles des `action` et `controller` et génère un chemin d’accès d’URL qui achemine vers l’action en cours.

Tente d’utiliser les valeurs dans les valeurs ambiantes pour renseigner les informations que vous n’avez pas fourni lors de la génération d’une URL de routage. À l’aide d’un itinéraire comme `{a}/{b}/{c}/{d}` et valeurs ambiantes `{ a = Alice, b = Bob, c = Carol, d = David }`, routage possède suffisamment d’informations pour générer une URL sans aucune valeur supplémentaire - étant donné que tous les router paramètres ont une valeur. Si vous avez ajouté la valeur `{ d = Donovan }`, la valeur `{ d = David }` est ignoré, et le chemin d’accès URL généré serait `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> Chemins d’accès d’URL sont hiérarchiques. Dans l’exemple ci-dessus, si vous avez ajouté la valeur `{ c = Cheryl }`, les deux valeurs `{ c = Carol, d = David }` est ignoré. Dans ce cas nous n’avons plus une valeur pour `d` et fera échouer la génération d’URL. Vous devez spécifier la valeur souhaitée de `c` et `d`.  Vous pouvez vous attendre à ce problème avec l’itinéraire par défaut (`{controller}/{action}/{id?}`)- mais vous rencontrerez rarement ce comportement en pratique, en tant que `Url.Action` seront toujours spécifier explicitement un `controller` et `action` valeur.

Les surcharges plus de `Url.Action` prennent également un autre *valeurs d’itinéraire* objet pour fournir des valeurs autres que pour les paramètres d’itinéraire `controller` et `action`. Vous verrez plus couramment utilisée avec `id` comme `Url.Action("Buy", "Products", new { id = 17 })`. Par convention le *valeurs d’itinéraire* objet est généralement un objet de type anonyme, mais il peut également être un `IDictionary<>` ou un *brut ancien objet .NET*. Toutes les valeurs d’itinéraire supplémentaires qui ne correspondent pas aux paramètres d’itinéraire sont placées dans la chaîne de requête.

[!code-csharp[Main](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> Pour créer une URL absolue, utilisez une surcharge qui accepte un `protocol`:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Génération des URL par itinéraire

Le code ci-dessus démontré la génération d’une URL en passant le nom d’action et de contrôleur. `IUrlHelper`fournit également le `Url.RouteUrl` famille de méthodes. Ces méthodes sont similaires aux `Url.Action`, mais ils ne copient pas les valeurs actuelles des `action` et `controller` pour les valeurs d’itinéraire. L’utilisation la plus courante consiste à spécifier un nom d’itinéraire à utiliser un itinéraire spécifique pour générer l’URL, généralement *sans* spécifiant un nom de contrôleur ou d’action.

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Génération des URL au format HTML

`IHtmlHelper`Fournit la `HtmlHelper` méthodes `Html.BeginForm` et `Html.ActionLink` pour générer `<form>` et `<a>` éléments respectivement. Ces méthodes utilisent le `Url.Action` méthode pour générer une URL et ils acceptent les arguments similaires. Le `Url.RouteUrl` être consultées conjointement pour `HtmlHelper` sont `Html.BeginRouteForm` et `Html.RouteLink` qui ont des fonctionnalités similaires.

TagHelpers générer des URL via le `form` TagHelper et `<a>` TagHelper. Ces deux utilisent `IUrlHelper` pour leur implémentation. Consultez [utilisation des formulaires](../views/working-with-forms.md) pour plus d’informations.

Dans les vues, les `IUrlHelper` est disponible via le `Url` propriété pour n’importe quel ad hoc de génération d’URL non couverte par la commande ci-dessus.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Génération des URL dans les résultats d’Action

Les exemples précédents ont montré à l’aide de `IUrlHelper` dans un contrôleur, tandis que l’utilisation la plus courante dans un contrôleur consiste à générer une URL en tant que partie d’un résultat d’action.

Le `ControllerBase` et `Controller` classes de base fournissent des méthodes pratiques pour les résultats d’action qui font référence à une autre action. Une utilisation typique consiste à rediriger après acceptation de l’entrée d’utilisateur.

```csharp
public Task<IActionResult> Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
}
```

Les méthodes de fabrique de résultats action suivent un modèle similaire aux méthodes `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Cas spécial pour les itinéraires conventionnelles dédiés

Le routage classique peut utiliser un type spécial de définition d’itinéraire appelée un *dédié itinéraire conventionnelle*. Dans l’exemple ci-dessous, l’itinéraire nommé `blog` est un itinéraire conventionnel dédié.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

À l’aide de ces définitions de route, `Url.Action("Index", "Home")` génère le chemin d’accès URL `/` avec la `default` itinéraire, mais pourquoi ? Vous pouvez deviner les valeurs d’itinéraire `{ controller = Home, action = Index }` serait suffisant pour générer une URL avec `blog`, et le résultat serait `/blog?action=Index&controller=Home`.

Itinéraires conventionnelles dédiés s’appuient sur un comportement spécial de valeurs par défaut qui n’ont pas de paramètre d’itinéraire correspondant qui empêche l’itinéraire « trop gourmande » avec la génération d’URL. Dans ce cas les valeurs par défaut sont `{ controller = Blog, action = Article }`et ni `controller` ni `action` apparaît sous la forme d’un paramètre d’itinéraire. Lorsque le routage effectue une génération d’URL, les valeurs fournies doivent correspondre aux valeurs de la valeur par défaut. Génération d’URL à l’aide `blog` échoue, car les valeurs `{ controller = Home, action = Index }` ne correspondent pas `{ controller = Blog, action = Article }`. Routage puis revient pour essayer `default`, ce qui aboutit.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Zones

[Zones](areas.md) sont une fonctionnalité MVC utilisée pour organiser des fonctionnalités connexes dans un groupe comme une structure de dossier (pour les vues) et le routage-espace de noms distinct (pour les actions de contrôleur). L’utilisation de zones permet à une application de posséder plusieurs contrôleurs portant le même nom - tant qu’ils disposent différents *zones*. L’utilisation de zones crée une hiérarchie à des fins de routage en ajoutant un autre paramètre d’itinéraire, `area` à `controller` et `action`. Cette section explique comment le routage interagit avec les zones - consultez [zones](areas.md) pour plus d’informations sur l’utilisation des zones avec des vues.

L’exemple suivant configure MVC pour utiliser l’itinéraire conventionnelle par défaut et un *les itinéraires de zone* pour une zone nommée `Blog`:

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

Pour mettre en correspondance un chemin d’accès de l’URL comme `/Manage/Users/AddUser`, le premier itinéraire génère les valeurs d’itinéraire `{ area = Blog, controller = Users, action = AddUser }`. Le `area` itinéraire valeur produite par une valeur par défaut `area`, en fait l’itinéraire créé par `MapAreaRoute` est équivalente à la suivante :

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`Crée un itinéraire à l’aide d’une valeur par défaut et la contrainte pour `area` en utilisant le nom de la zone fournie, dans ce cas `Blog`. La valeur par défaut garantit que l’itinéraire génère toujours `{ area = Blog, ... }`, la contrainte requiert la valeur `{ area = Blog, ... }` pour la génération d’URL.

> [!TIP]
> Le routage classique est dépendant de l’ordre. En général, les itinéraires avec des zones doivent être placés plus haut dans la table d’itinéraires lorsqu’ils sont plus spécifiques que les itinéraires sans une zone.

À l’aide de l’exemple ci-dessus, les valeurs d’itinéraire correspondrait à l’action suivante :

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

Le `AreaAttribute` est ce qui indique un contrôleur dans le cadre d’une zone, nous dire que ce contrôleur est dans le `Blog` zone. Contrôleurs sans un `[Area]` attribut ne sont pas membres de n’importe quelle zone et seront **pas** correspondent lorsque la `area` valeur de l’itinéraire est fournie par le routage. Dans l’exemple suivant, le premier contrôleur répertorié peut correspondre aux valeurs d’itinéraire `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> L’espace de noms de chaque contrôleur est illustrée ici par souci d’exhaustivité, sinon les contrôleurs aurait d’une affectation de noms en conflit et génèrent une erreur du compilateur. Espaces de noms de classe ont aucun effet sur le routage de MVC.

Tout d’abord deux contrôleurs sont membres de domaines et uniquement en correspondance lorsque leur nom de la zone correspondante est fournie par le `area` acheminer de valeur. Le contrôleur tiers n’est pas un membre de zone et peut seule correspondance lorsqu’aucune valeur de `area` est fourni par le routage.

> [!NOTE]
> En termes de mise en correspondance *aucune valeur*, l’absence de la `area` la valeur est identique comme si la valeur de `area` est null ou une chaîne vide.

Lors de l’exécution d’une action à l’intérieur d’une zone, valeur de l’itinéraire pour `area` peuvent être utilisés comme un *valeur ambiante* pour le routage à utiliser pour la génération d’URL. Cela signifie que par défaut zones agissent *rémanentes* pour la génération d’URL comme illustré dans l’exemple suivant.

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Présentation des IActionConstraint

> [!NOTE]
> Cette section est une présentation détaillée sur les mécanismes internes de framework et comment MVC choisit une action à exécuter. Une application classique n’aurez pas personnalisé`IActionConstraint`

Vous avez utilisé probablement déjà `IActionConstraint` même si vous n’êtes pas familiarisé avec l’interface. Le `[HttpGet]` attribut et similaires `[Http-VERB]` attributs implémentent `IActionConstraint` afin de limiter l’exécution d’une méthode d’action.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

En supposant que l’itinéraire par défaut classique, le chemin d’accès URL `/Products/Edit` génère les valeurs `{ controller = Products, action = Edit }`, qui correspondrait à **les deux** des actions ci-après. Dans `IActionConstraint` terminologie indiquer que ces deux actions sont considérés comme candidats - que les deux correspondent aux données d’itinéraire.

Lorsque le `HttpGetAttribute` s’exécute, il indiquera que *Edit()* correspond aux *obtenir* et n’est pas une correspondance pour tous les autres verbes HTTP. Le `Edit(...)` action ne dispose pas des contraintes définies et donc correspond à n’importe quel verbe HTTP. Par conséquent, en supposant un `POST` - uniquement `Edit(...)` correspond à. Mais, pour un `GET` les deux actions peuvent correspondre encore aux - Toutefois, une action avec un `IActionConstraint` est toujours considéré comme *mieux* à une action sans. Par conséquent, étant donné que `Edit()` a `[HttpGet]` il est considéré comme plus spécifique et est sélectionnée si les deux actions peuvent correspondre.

Point de vue conceptuel, `IActionConstraint` est une forme de *surcharge*, mais au lieu de la surcharge de méthodes portant le même nom, il est la surcharge entre les actions qui correspondent à la même URL. Routage d’attributs utilise également `IActionConstraint` et peut générer des actions à partir de différents contrôleurs à la fois être considéré comme candidats.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implémentation de IActionConstraint

La façon la plus simple d’implémenter un `IActionConstraint` consiste à créer une classe dérivée de `System.Attribute` et placez-le sur vos actions et les contrôleurs. MVC détecte automatiquement les `IActionConstraint` qui sont appliquées en tant qu’attributs. Vous pouvez utiliser le modèle d’application pour appliquer des contraintes, et c’est probablement l’approche la plus souple car elle vous permet d’accéder à metaprogram comment ils sont appliqués.

Dans l’exemple suivant, une contrainte choisit une action basée sur un *code pays* à partir des données d’itinéraire. Le [exemple complet sur GitHub](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Vous êtes chargé d’implémenter la `Accept` (méthode) et en choisissant un 'Order' pour la contrainte à exécuter. Dans ce cas, le `Accept` méthode retourne `true` pour indiquer l’action est une correspondance lorsque le `country` Router correspond à la valeur. Cela est différent d’un `RouteValueAttribute` car elle permet de revenir à une action sans attributs. L’exemple montre que si vous définissez un `en-US` action puis un code de pays comme `fr-FR` fasse appel à un contrôleur plus générique qui n’a pas `[CountrySpecific(...)]` appliqué.

Le `Order` propriété décide *étape* la contrainte fait partie. Contraintes d’action s’exécutent dans des groupes en fonction de la `Order`. Par exemple, tous du framework fourni les attributs de méthode HTTP utilisent le même `Order` valeur afin qu’ils s’exécutent dans la même étape. Vous pouvez avoir autant d’étapes que vous devez implémenter vos stratégies de sécurité.

> [!TIP]
> Choisir une valeur pour `Order` réfléchissons ou non une contrainte doit être appliquée avant les méthodes HTTP. Les nombres inférieurs exécuté en premier.

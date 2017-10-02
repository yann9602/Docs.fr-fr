---
title: Le routage ASP.NET Core
author: ardalis
description: "Découvrez comment les fonctionnalités de routage ASP.NET Core sont responsable de mappage d’une demande entrante à un gestionnaire d’itinéraire."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bbbcf9e4-3c4c-4f50-b91e-175fe9cae4e2
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/routing
ms.openlocfilehash: 9d24c2956c24a7995b3eeffc19e8c0a827349493
ms.sourcegitcommit: ed401027aac45c5938c917c7f518a33ceffe9f95
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2017
---
# <a name="routing-in-aspnet-core"></a>Le routage ASP.NET Core

Par [Ryan Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/), et [Rick Anderson](https://twitter.com/RickAndMSFT)

Fonctionnalité de routage est chargée pour le mappage d’une demande entrante vers un gestionnaire d’itinéraire. Les itinéraires sont définis dans l’application ASP.NET et configurés au démarrage de l’application. Un itinéraire peut éventuellement extraire les valeurs à partir de l’URL contenue dans la demande, et ces valeurs peuvent ensuite être utilisées pour traiter une demande. À l’aide des informations d’itinéraire à partir de l’application ASP.NET, la fonctionnalité de routage peut également générer des URL qui mappent aux gestionnaires d’itinéraire. Par conséquent, le routage peut trouver un gestionnaire d’itinéraires basé sur une URL ou l’URL correspondant à un gestionnaire d’itinéraire donné en fonction des informations de gestionnaire d’itinéraire.

>[!IMPORTANT]
> Ce document couvre le routage du bas niveau ASP.NET Core. Pour le routage ASP.NET MVC de base, consultez [routage vers les Actions de contrôleur](../mvc/controllers/routing.md)

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/sample) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))

## <a name="routing-basics"></a>Principes de base de routage

Routage utilise *itinéraires* (implémentations de [IRouter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.routing.irouter)) pour :

* mapper les demandes entrantes vers *acheminer des gestionnaires*

* générer des URL utilisées dans les réponses

En règle générale, une application possède une seule collection d’itinéraires. Lorsqu’une demande arrive, la collection d’itinéraires est traitée dans l’ordre. Recherche de la demande entrante d’un itinéraire qui correspond à l’URL de demande en appelant le `RouteAsync` méthode sur chaque itinéraire disponible dans la collection d’itinéraires. En revanche, une réponse peut utiliser le routage pour générer des URL (par exemple, pour la redirection ou liaisons) en fonction des informations d’itinéraire et ainsi éviter d’avoir à coder en dur des URL, qui vous aide à la facilité de maintenance.

Le routage est connecté à la [intergiciel (middleware)](middleware.md) de pipeline par la `RouterMiddleware` classe. [ASP.NET MVC](../mvc/overview.md) ajoute le routage vers le pipeline de l’intergiciel (middleware) dans le cadre de sa configuration. Pour en savoir plus sur l’utilisation de routage comme un composant autonome, consultez [middleware routage à l’aide de](#using-routing-middleware).

<a name=url-matching-ref></a>

### <a name="url-matching"></a>URL correspondant

URL correspondant est le processus par le routage distribue un entrant demander à un *gestionnaire*. Ce processus est généralement basé sur des données dans le chemin d’accès URL, mais peut être étendu pour prendre en compte toutes les données de la demande. La possibilité de distribuer des demandes pour séparer les gestionnaires est la clé à l’échelle de la taille et la complexité d’une application.

Les demandes entrantes entrent le `RouterMiddleware`, qui appelle la `RouteAsync` méthode sur chaque itinéraire dans la séquence. Le `IRouter` instance choisit s’il faut *gérer* la demande en définissant le `RouteContext.Handler` à une valeur non null `RequestDelegate`. Si un itinéraire définit un gestionnaire pour la demande, itinéraire, le traitement s’arrête et le gestionnaire sera appelé pour traiter la demande. Si tous les itinéraires sont essayés et qu’aucun gestionnaire ne trouvé pour la demande, l’intergiciel (middleware) appelle *suivant* et de l’intergiciel (middleware) suivant dans le pipeline de requête est appelée.

L’entrée principale de `RouteAsync` est le `RouteContext.HttpContext` associé à la demande en cours. Le `RouteContext.Handler` et `RouteContext.RouteData` sont sorties qui seront définis une fois que l’itinéraire correspond à.

Une correspondance pendant `RouteAsync` sera également définir les propriétés de la `RouteContext.RouteData` avec les valeurs appropriées selon le traitement de la demande effectué jusqu'à présent. Si l’itinéraire correspond à une demande, le `RouteContext.RouteData` contiennent des informations d’état importantes sur la *résultat*.

`RouteData.Values`est un dictionnaire de *valeurs d’itinéraire* produits à partir de l’itinéraire. Ces valeurs sont généralement déterminées par l’URL de création de jetons et peuvent être utilisés pour accepter l’entrée d’utilisateur, ou prendre des décisions davantage de distribution à l’intérieur de l’application.

`RouteData.DataTokens`est un jeu de propriétés des données supplémentaires associées à l’itinéraire correspondant. `DataTokens`sont fournis pour prendre en charge d’association état mis en correspondance des données avec chaque itinéraire afin de l’application de prendre des décisions ultérieurement en fonction de l’itinéraire. Ces valeurs sont définis par le développeur et **pas** affectent le comportement de routage en aucune façon. En outre, les valeurs dissimulés dans des jetons de données peuvent être de n’importe quel type, contrairement aux valeurs d’itinéraire, qui doit être facilement convertibles vers et à partir de chaînes.

`RouteData.Routers`est une liste des itinéraires que participé correctement correspondant à la demande. Itinéraires peuvent être imbriquées dans une autre et le `Routers` propriété reflète le chemin d’accès dans l’arborescence logique d’itinéraires qui a entraîné une correspondance. En règle générale le premier élément de `Routers` est la collection d’itinéraires et doit être utilisé pour la génération d’URL. Le dernier élément `Routers` est le Gestionnaire d’itinéraire correspondant.

### <a name="url-generation"></a>Génération d’URL

Génération d’URL est le processus par lequel le routage peut créer un chemin d’accès d’URL basée sur un ensemble de valeurs d’itinéraire. Cela permet une séparation logique entre les gestionnaires et les URL qui y accèdent.

Génération d’URL suit un processus itératif similaire, mais démarre avec le code utilisateur ou framework appellent la `GetVirtualPath` méthode de la collection d’itinéraires. Chaque *itinéraire* aura son `GetVirtualPath` méthode appelée dans la séquence jusqu'à une valeur non null `VirtualPathData` est retourné.

Le réplica principal est une entrée `GetVirtualPath` sont :

* `VirtualPathContext.HttpContext`

* `VirtualPathContext.Values`

* `VirtualPathContext.AmbientValues`

Itinéraires utilisent principalement les valeurs d’itinéraire fournies par le `Values` et `AmbientValues` pour décider où il est possible de générer une URL et les valeurs à inclure. Le `AmbientValues` sont le jeu de valeurs d’itinéraire qui ont été générés à partir de la mise en correspondance la demande en cours avec le système de routage. En revanche, `Values` sont les valeurs d’itinéraire qui spécifient comment générer l’URL de votre choix pour l’opération actuelle. Le `HttpContext` est fournie au cas où un itinéraire a besoin d’obtenir des services ou des données supplémentaires associées au contexte actuel.

Conseil : Considérer `Values` comme étant un ensemble de remplacements pour le `AmbientValues`. Génération d’URL tente de réutiliser des valeurs d’itinéraire à partir de la requête actuelle pour faciliter la génération d’URL pour les liens à l’aide de la même itinéraire ou les valeurs d’itinéraire.

La sortie de `GetVirtualPath` est un `VirtualPathData`. `VirtualPathData`est une représentation parallèle de `RouteData`; il contient le `VirtualPath` pour l’URL de sortie, ainsi que des propriétés supplémentaires qui doivent être définies par l’itinéraire.

Le `VirtualPathData.VirtualPath` propriété contient le *chemin d’accès virtuel* produits par l’itinéraire. Selon vos besoins, vous devrez peut-être traiter davantage le chemin d’accès. Par exemple, si vous souhaitez afficher l’URL générée au format HTML, vous devez ajouter le chemin d’accès de base de l’application.

Le `VirtualPathData.Router` est une référence à l’itinéraire qui a généré avec succès l’URL.

Le `VirtualPathData.DataTokens` propriétés est un dictionnaire de données supplémentaires relatives à l’itinéraire qui a généré l’URL. Il s’agit du parallèle de `RouteData.DataTokens`.

### <a name="creating-routes"></a>Création des itinéraires

Le routage fournit le `Route` classe en tant que l’implémentation standard de `IRouter`. `Route`utilise le *modèle d’itinéraire* syntaxe pour définir des modèles correspondant sur le chemin d’accès de l’URL lorsque `RouteAsync` est appelée. `Route`utilisera le même modèle d’itinéraire pour générer une URL lorsque `GetVirtualPath` est appelée.

La plupart des applications de créer des itinéraires en appelant `MapRoute` ou l’une des méthodes d’extension semblable définis sur `IRouteBuilder`. Toutes ces méthodes créera une instance de `Route` et l’ajouter à la collection d’itinéraires.

Remarque : `MapRoute` n’accepte pas un paramètre de gestionnaire d’itinéraire - il ajoute uniquement les itinéraires qui seront gérés par le `DefaultHandler`. Étant donné que le gestionnaire par défaut est un `IRouter`, il peut décider de ne pas traiter la demande. Par exemple, ASP.NET MVC est généralement configuré comme un gestionnaire par défaut qui gère uniquement les requêtes qui correspondent à une action et contrôleur disponible. Pour en savoir plus sur le routage vers MVC, consultez [le routage pour les Actions de contrôleur](../mvc/controllers/routing.md).

Il s’agit d’un exemple d’un `MapRoute` appel utilisée par une définition d’itinéraire ASP.NET MVC par défaut :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Ce modèle correspond à un chemin d’accès de l’URL comme `/Products/Details/17` et extraire les valeurs d’itinéraire `{ controller = Products, action = Details, id = 17 }`. Les valeurs d’itinéraire sont déterminées par le fractionnement le chemin d’accès de l’URL en segments et correspondant à chaque segment avec le *paramètre d’itinéraire* nom dans le modèle d’itinéraire. Paramètres d’itinéraire sont nommés. Ils sont définis en incluant le nom du paramètre entouré d’accolades `{ }`.

Le modèle ci-dessus peut également correspondre le chemin d’accès URL `/` et génère les valeurs `{ controller = Home, action = Index }`. Cela se produit car le `{controller}` et `{action}` paramètres d’itinéraire ont des valeurs par défaut et le `id` paramètre d’itinéraire est facultatif. Égal `=` signe suivi d’une valeur une fois que le nom de paramètre d’itinéraire définit une valeur par défaut pour le paramètre. Un point d’interrogation `?` après le nom de paramètre d’itinéraire définit le paramètre comme facultatif. Paramètres avec une valeur par défaut d’itinéraire *toujours* produisent une valeur de routage lors de l’itinéraire correspond à - paramètres optionnels ne produisent pas une valeur de l’itinéraire s’il y a aucun segment de chemin d’accès d’URL correspondante.

Consultez [référence du modèle d’itinéraire](#route-template-reference) pour une description complète des fonctionnalités de modèle d’itinéraire et la syntaxe.

Cet exemple inclut un *Router contrainte*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Ce modèle correspond à un chemin d’accès de l’URL comme `/Products/Details/17`, mais pas `/Products/Details/Apples`. La définition de paramètre d’itinéraire `{id:int}` définit un *Router contrainte* pour la `id` paramètre d’itinéraire. Implémentent des contraintes d’itinéraire `IRouteConstraint` et inspecter pour vérifier les valeurs d’itinéraire. Dans cet exemple, la valeur de l’itinéraire `id` doit être convertible en entier. Consultez [référence de contrainte d’itinéraire](#route-constraint-reference) pour une explication plus détaillée des contraintes d’itinéraire qui sont fournies par l’infrastructure.

Les surcharges supplémentaires de `MapRoute` accepte les valeurs pour `constraints`, `dataTokens`, et `defaults`. Ces paramètres supplémentaires de `MapRoute` sont définies en tant que type `object`. L’utilisation typique de ces paramètres consiste à passer un objet typé anonymement, où les noms des propriétés de la correspondance de type anonyme diriger les noms de paramètre.

Les deux exemples suivants créent équivalentes :

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Conseil : La syntaxe inline pour la définition des contraintes et des valeurs par défaut peut être plus pratique pour les itinéraires simples. Toutefois, il existe des fonctionnalités telles que les jetons de données qui ne sont pas pris en charge par la syntaxe inline.

Cet exemple illustre quelques fonctionnalités supplémentaires :

```csharp
routes.MapRoute(
  name: "blog",
  template: "Blog/{*article}",
  defaults: new { controller = "Blog", action = "ReadArticle" });
```

Ce modèle correspond à un chemin d’accès de l’URL comme `/Blog/All-About-Routing/Introduction` et extrait les valeurs `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Les valeurs de l’itinéraire par défaut pour `controller` et `action` sont produites par l’itinéraire, même s’il n’existe aucun paramètre d’itinéraire correspondant dans le modèle. Valeurs par défaut peuvent être spécifiées dans le modèle d’itinéraire. Le `article` paramètre d’itinéraire est défini comme un *fourre-tout* par l’apparence d’un astérisque `*` avant le nom de paramètre d’itinéraire. Paramètres d’itinéraire de fourre-tout capturer le reste du chemin d’URL et peuvent correspondre également à une chaîne vide.

Cet exemple ajoute des jetons contraintes et les données d’itinéraire :

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Ce modèle correspond à un chemin d’accès de l’URL comme `/Products/5` et extrait les valeurs `{ controller = Products, action = Details, id = 5 }` et des jetons de données `{ locale = en-US }`.

![Jetons Windows variables locales](routing/_static/tokens.png)

<a name=id1></a>

### <a name="url-generation"></a>Génération d’URL

La `Route` classe peut également effectuer de génération d’URL en combinant un ensemble de valeurs d’itinéraire et de son modèle d’itinéraire. Il est logiquement le processus inverse de mise en correspondance le chemin d’accès d’URL.

Conseil : Pour mieux comprendre la génération de l’URL, imaginez le URL que vous souhaitez générer et puis pensez à la façon dont un modèle d’itinéraire correspondrait à cette URL. Quelles sont les valeurs serait produit ? Ceci est l’équivalent du fonctionne de la génération d’URL dans la `Route` classe.

Cet exemple utilise un itinéraire de style base ASP.NET MVC :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Avec les valeurs d’itinéraire `{ controller = Products, action = List }`, cet itinéraire génère l’URL `/Products/List`. Les valeurs d’itinéraire sont substitués pour les paramètres d’itinéraire correspondant former le chemin d’accès d’URL. Étant donné que `id` est d’un paramètre d’itinéraire, elle ne pose aucun problème qu’il n’a pas une valeur.

Avec les valeurs d’itinéraire `{ controller = Home, action = Index }`, cet itinéraire génère l’URL `/`. Les valeurs d’itinéraire qui ont été fournies correspondent aux valeurs par défaut pour les segments correspondant à ces valeurs peuvent être omis en toute sécurité. Notez que les deux URL générées serait aller-retour avec cette définition de l’itinéraire et produire les mêmes valeurs d’itinéraire qui ont été utilisés pour générer l’URL.

Conseil : Une application à l’aide d’ASP.NET MVC doit utiliser `UrlHelper` pour générer des URL au lieu d’appeler directement le routage.

Pour plus d’informations sur le processus de génération d’URL, consultez [url de référence de génération](#url-generation-reference).

## <a name="using-routing-middleware"></a>À l’aide de routage intergiciel (middleware)

Ajoutez le package NuGet « Microsoft.AspNetCore.Routing ».

Ajouter le routage vers le conteneur de service dans *Startup.cs*:

[!code-csharp[Main](../fundamentals/routing/sample/RoutingSample/Startup.cs?highlight=3&start=11&end=14)]

Les itinéraires doivent être configurés dans le `Configure` méthode dans la `Startup` classe. L’exemple ci-dessous utilise ces API :

* `RouteBuilder`
* `Build`
* `MapGet`Correspond à uniquement les requêtes HTTP GET
* `UseRouter`

```csharp
public void Configure(IApplicationBuilder app, ILoggerFactory loggerFactory)
{
    var trackPackageRouteHandler = new RouteHandler(context =>
    {
        var routeValues = context.GetRouteData().Values;
        return context.Response.WriteAsync(
            $"Hello! Route values: {string.Join(", ", routeValues)}");
    });

    var routeBuilder = new RouteBuilder(app, trackPackageRouteHandler);

    routeBuilder.MapRoute(
        "Track Package Route",
        "package/{operation:regex(^(track|create|detonate)$)}/{id:int}");

    routeBuilder.MapGet("hello/{name}", context =>
    {
        var name = context.GetRouteValue("name");
        // This is the route handler when HTTP GET "hello/<anything>"  matches
        // To match HTTP GET "hello/<anything>/<anything>,
        // use routeBuilder.MapGet("hello/{*name}"
        return context.Response.WriteAsync($"Hi, {name}!");
    });

    var routes = routeBuilder.Build();
    app.UseRouter(routes);
}
```

Le tableau ci-dessous montre les réponses avec l’URI donné.

| URI | Réponse  |
| ------- | -------- |
| /package/Create/3  | Hello! Valeurs d’itinéraire : [opération, créer], [id, 3] |
| / package/suivi /-3  | Hello! Valeurs d’itinéraire : [opération, le suivi,] [id -3] |
| / package/suivi/3 / | Hello! Valeurs d’itinéraire : [opération, le suivi,] [id -3]  |
| /package/suivi | \<Passage à aucune correspondance > |
| OBTENIR /hello/Joe | Bonjour, Joe ! |
| POST /hello/Joe | \<Passer, correspond à HTTP GET uniquement > |
| OBTENIR /hello/Joe/Smith | \<Passage à aucune correspondance > |

Si vous configurez un seul itinéraire, appelez `app.UseRouter` en passant un `IRouter` instance. Vous ne devez pas appeler `RouteBuilder`.

Le framework fournit un ensemble de méthodes d’extension pour la création d’itinéraires telles que :

* `MapRoute`
* `MapGet`
* `MapPost`
* `MapPut`
* `MapDelete`
* `MapVerb`

Certaines de ces méthodes comme `MapGet` nécessitent un `RequestDelegate` doit être fourni. Le `RequestDelegate` sera utilisé comme le *Gestionnaire d’itinéraire* lorsque correspond à l’itinéraire. Autres méthodes dans cette famille de permettent la configuration d’un pipeline d’intergiciel (middleware) qui sera utilisé en tant que gestionnaire d’itinéraire. Si le *carte* méthode n’accepte pas un gestionnaire, tel que `MapRoute`, il utilisera le `DefaultHandler`.

Le `Map[Verb]` méthodes utilisent des contraintes pour limiter l’itinéraire pour le verbe HTTP dans le nom de la méthode. Par exemple, consultez [MapGet](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/RequestDelegateRouteBuilderExtensions.cs#L85-L88) et [MapVerb](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/RequestDelegateRouteBuilderExtensions.cs#L156-L180).

## <a name="route-template-reference"></a>Référence de modèle d’itinéraire

Jetons entre accolades (`{ }`) définir *paramètres d’itinéraire* sera lié si l’itinéraire est mis en correspondance. Vous pouvez définir plusieurs paramètres d’itinéraire dans un segment de l’itinéraire, mais ils doivent être séparés par une valeur littérale. Par exemple `{controller=Home}{action=Index}` ne serait pas un itinéraire valid, car il n’existe aucune valeur littérale entre `{controller}` et `{action}`. Ces paramètres d’itinéraire doivent avoir un nom, et peuvent avoir des attributs supplémentaires spécifiés.

Texte littéral autres que les paramètres d’itinéraire (par exemple, `{id}`) et le séparateur de chemin d’accès `/` doit correspondre au texte dans l’URL. La correspondance de texte est la casse et en fonction de la représentation sous forme décodée du chemin d’accès d’URL. Pour faire correspondre le délimiteur de paramètre d’itinéraire littéral `{` ou `}`, échapper en répétant le caractère (`{{` ou `}}`).

Modèles d’URL qui tentent de capturer un nom de fichier avec une extension de fichier facultatif ont des considérations supplémentaires. Par exemple, à l’aide du modèle `files/{filename}.{ext?}` - lorsque les deux `filename` et `ext` existe, les deux valeurs seront remplies. Si une seule `filename` existe dans l’URL, les correspondances de l’itinéraire, car la période de fin `.` est facultatif. Les URL suivantes correspondrait à cet itinéraire :

* `/files/myFile.txt`
* `/files/myFile.`
* `/files/myFile`

Vous pouvez utiliser la `*` caractère en tant que préfixe pour un paramètre d’itinéraire à lier au reste de l’URI - il s’agit un *fourre-tout* paramètre. Par exemple, `blog/{*slug}` correspondrait à n’importe quel URI qui commencent par `/blog` et toute valeur (qui sont assigné à la `slug` Router valeur). Les paramètres de collecte peuvent correspondre également à une chaîne vide.

Paramètres d’itinéraire peut-être *les valeurs par défaut*, désigné par la spécification de la valeur par défaut après le nom de paramètre, séparé par un `=`. Par exemple, `{controller=Home}` définirait `Home` en tant que la valeur par défaut `controller`. La valeur par défaut est utilisée si aucune valeur n’est présent dans l’URL pour le paramètre. En plus des valeurs par défaut, les paramètres d’itinéraire peuvent être facultatif (spécifié en ajoutant un `?` à la fin du nom du paramètre, comme dans `id?`). La différence entre facultatif et « a par défaut » n’est qu’un paramètre d’itinéraire avec une valeur par défaut génère toujours une valeur ; un paramètre optionnel a une valeur uniquement lorsqu’elle est fournie.

Paramètres d’itinéraire peuvent également avoir des contraintes, qui doivent correspondre à la valeur de l’itinéraire liée à partir de l’URL. Ajout d’un signe deux-points `:` et le nom de la contrainte après le nom de paramètre d’itinéraire spécifie un *contrainte inline* sur un paramètre d’itinéraire. Si la contrainte requiert des arguments que ceux fournis entre parenthèses `( )` après le nom de la contrainte. Plusieurs contraintes en ligne peuvent être spécifiés en ajoutant un autre signe deux-points `:` et le nom de la contrainte. Le nom de la contrainte est passé à la `IInlineConstraintResolver` pour créer une instance de service `IRouteConstraint` à utiliser dans le traitement de l’URL. Par exemple, le modèle d’itinéraire `blog/{article:minlength(10)}` Spécifie le `minlength` contrainte avec l’argument `10`. Pour plusieurs contraintes d’itinéraire description et la liste des contraintes fournies par le framework, consultez [référence de contrainte d’itinéraire](#route-constraint-reference).

Le tableau suivant illustre certains modèles d’itinéraire et leur comportement.

| Modèle d’itinéraire | Exemple d’URL mise en correspondance | Remarques |
| -------- | -------- | ------- |
| hello  | / Bonjour  | Correspond uniquement au chemin d’accès unique`/hello` |
| {Page = Home} | / | Correspond à et définit `Page` à`Home` |
| {Page = Home}  | / Contact  | Correspond à et définit `Page` à`Contact` |
| {controller} / {action} / {id} ? | / Produits/liste | Mappe à `Products` contrôleur et `List` action |
| {controller} / {action} / {id} ? | / Produits/détails/123  |  Mappe à `Products` contrôleur et `Details` action.  `id`la valeur 123 |
| {contrôleur = Home} / {action = Index} / {id} ? | /  |  Mappe à `Home` contrôleur et `Index` méthode ; `id` est ignoré. |

À l’aide d’un modèle est généralement l’approche la plus simple pour le routage. Contraintes et des valeurs par défaut peuvent également être spécifiées en dehors du modèle d’itinéraire.

Conseil : Activer [journalisation](logging.md) pour voir comment la générées dans les implémentations de routage, tels que `Route`, correspondent aux requêtes.

## <a name="route-constraint-reference"></a>Référence de contrainte d’itinéraire

Contraintes d’itinéraire exécutent quand un `Route` a mis en correspondance de la syntaxe de l’URL entrante et le chemin d’accès de l’URL sous forme de jetons dans les valeurs d’itinéraire. En règle générale, les contraintes d’itinéraire inspecter la valeur de l’itinéraire associée via le modèle d’itinéraire et créer une simple de décision Oui/non ou non la valeur est acceptable. Certaines contraintes d’itinéraire utilisent des données en dehors de la valeur de l’itinéraire pour envisager si la demande peut être routée. Par exemple, le `HttpMethodRouteConstraint` peut accepter ou refuser une demande basée sur son verbe HTTP.

>[!WARNING]
> Évitez d’utiliser des contraintes pour **validation d’entrée**, car vous aurez ainsi que les entrées non valides entraîne une erreur 404 (introuvable) au lieu d’un 400 avec un message d’erreur approprié. Contraintes d’itinéraire doivent être utilisés pour **lever l’ambiguïté** entre les itinéraires semblables, ne pas à valider les entrées d’un itinéraire particulier.

Le tableau suivant illustre certaines contraintes d’itinéraire et leur comportement attendu.

| contrainte | Exemple | Correspondances d’exemple | Remarques |
| --------   | ------- | ------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789`  | Correspond à n’importe quel entier |
| `bool`  | `{active:bool}` | `true`, `FALSE` | Correspondances `true` ou `false` (non-respect de la casse) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm`  | Correspond à un élément valide `DateTime` valeur (voir l’avertissement dans la culture dite indifférente -) |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Correspond à un élément valide `decimal` valeur (voir l’avertissement dans la culture dite indifférente -) |
| `double`  | `{weight:double}` | `1.234`, `-1,001.01e8` | Correspond à un élément valide `double` valeur (voir l’avertissement dans la culture dite indifférente -) |
| `float`  | `{weight:float}` | `1.234`, `-1,001.01e8` | Correspond à un élément valide `float` valeur (voir l’avertissement dans la culture dite indifférente -) |
| `guid`  | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Correspond à un élément valide `Guid` valeur |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Correspond à un élément valide `long` valeur |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Chaîne doit comporter au moins 4 caractères |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | Chaîne doit être pas plus de 8 caractères |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Chaîne doit être exactement de 12 caractères. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Chaîne doit être au moins 8 et pas plus de 16 caractères |
| `min(value)` | `{age:min(18)}` | `19` | Valeur entière doit être au moins 18 |
| `max(value)` | `{age:max(120)}` |  `91` | Valeur entière doit être pas plus de 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Valeur entière doit être au moins 18, mais pas plus de 120 |
| `alpha` | `{name:alpha}` | `Rick` | Chaîne doit se composer d’un ou plusieurs caractères alphabétiques (`a`-`z`, non-respect de la casse) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Chaîne doit correspondre à l’expression régulière (consultez les conseils sur la définition d’une expression régulière) |
| `required`  | `{name:required}` | `Rick` |  Utilisé pour garantir qu’une valeur de paramètre non est présente pendant la génération d’URL |

>[!WARNING]
> Contraintes d’itinéraire qui vérifient l’URL peuvent être convertis en un type CLR (tel que `int` ou `DateTime`) utilisez toujours la culture dite indifférente : ils supposent que l’URL n’est pas localisable. Les contraintes d’itinéraire fourni par le framework ne modifiez pas les valeurs stockées dans les valeurs d’itinéraire. Toutes les valeurs d’itinéraire analysés à partir de l’URL seront stockées en tant que chaînes. Par exemple, le [contrainte d’itinéraire Float](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/Constraints/FloatRouteConstraint.cs#L44-L60) tente de convertir la valeur de l’itinéraire à virgule flottante, mais la valeur convertie est utilisée uniquement pour vérifier qu’il peut être converti en une valeur float.

## <a name="regular-expressions"></a>Expressions régulières 

L’infrastructure ASP.NET Core ajoute `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` au constructeur d’expression régulière. Consultez [RegexOptions, énumération](https://docs.microsoft.com/dotnet/api/system.text.regularexpressions.regexoptions) pour obtenir une description de ces membres.

Expressions régulières utilisent les délimiteurs et les jetons semblables à celles utilisées par le routage et le langage c#. Jetons d’expression régulière doivent être échappés. Par exemple, pour utiliser l’expression régulière `^\d{3}-\d{2}-\d{4}$` de routage, il doit avoir le `\` caractères tapées comme `\\` dans le fichier source c# pour échapper le `\` caractère d’échappement de chaîne (sauf à l’aide de [textuel littéraux de chaîne](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/string). Le `{` , `}` , ' [' et ']' caractères doivent être doublée pour échapper les caractères de délimitation des paramètres de routage.  Le tableau ci-dessous montre une expression régulière et la version.

| Expression               | Remarque |
| ----------------- | ------------ | 
| `^\d{3}-\d{2}-\d{4}$` | Expression régulière |
| `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` | Séquence d’échappement  |
| `^[a-z]{2}$` | Expression régulière |
| `^[[a-z]]{{2}}$` | Séquence d’échappement  |

Souvent des expressions régulières utilisées dans le routage commence par la `^` caractères (correspondance la position de la chaîne de départ) et se terminent par le `$` caractère (se terminant par la position de la chaîne de correspondance). Le `^` et `$` caractères vous assurer que la valeur de paramètre d’itinéraire entier de la correspondance d’expression régulière. Sans le `^` et `$` caractères, l’expression régulière correspondra toute sous-chaîne dans la chaîne, ce qui est souvent pas ce que vous souhaitez. Le tableau ci-dessous présente des exemples et explique pourquoi ils correspondent ou ne correspondent pas.

| Expression               | Chaîne | Faire correspondre à | Commentaire |
| ----------------- | ------------ |  ------------ |  ------------ | 
| `[a-z]{2}` | hello | oui | correspondances de sous-chaînes |
| `[a-z]{2}` | 123abc456 | oui | correspondances de sous-chaînes |
| `[a-z]{2}` | Mz | oui | correspond à l’expression |
| `[a-z]{2}` | MZ | oui | non respect de la casse |
| `^[a-z]{2}$` |  hello | Non | consultez `^` et `$` ci-dessus |
| `^[a-z]{2}$` |  123abc456 | Non | consultez `^` et `$` ci-dessus |

Reportez-vous à [Expressions régulières .NET Framework](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) pour plus d’informations sur la syntaxe d’expression régulière.

Pour contraindre un paramètre à un jeu connu de valeurs possibles, utilisez une expression régulière. Par exemple `{action:regex(^(list|get|create)$)}` correspond uniquement à la `action` acheminer la valeur à `list`, `get`, ou `create`. Si passé dans le dictionnaire de contraintes, la chaîne « ^ (liste | get | créer) $» serait équivalent. Les contraintes qui sont passées dans le dictionnaire de contraintes (pas inline dans un modèle) et qui ne correspondent pas l’une des contraintes connus sont également traités comme des expressions régulières.

## <a name="url-generation-reference"></a>Référence de la génération d’URL

L’exemple ci-dessous montre comment générer un lien vers un itinéraire donné d’un dictionnaire de valeurs d’itinéraire et un `RouteCollection`.

[!code-csharp[Main](../fundamentals/routing/sample/RoutingSample/Startup.cs?range=45-59)]

Le `VirtualPath` généré à la fin de l’exemple ci-dessus est `/package/create/123`.

Le second paramètre pour le `VirtualPathContext` constructeur est une collection de *valeurs ambiantes*. Valeurs ambiantes fournissent plus de commodité en limitant le nombre de valeurs, qu'un développeur doit spécifier dans un certain contexte de demande. Les valeurs d’itinéraire actuel de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de lien. Par exemple, dans une application ASP.NET MVC si vous êtes dans le `About` action de la `HomeController`, vous n’avez pas besoin de spécifier la valeur d’itinéraire de contrôleur à lier à la `Index` action (la valeur ambiante de `Home` sera utilisé).

Valeurs ambiantes qui ne correspond pas à un paramètre sont ignorées et ambiante les valeurs sont également ignorées lorsqu’une valeur explicitement fourni substitue à elle, allant de gauche à droite dans l’URL.

Les valeurs qui sont fournis explicitement, mais qui ne correspond à rien sont ajoutées à la chaîne de requête. Le tableau suivant présente le résultat lorsque vous utilisez le modèle d’itinéraire `{controller}/{action}/{id?}`.

| Valeurs ambiantes | Valeurs explicites | Résultat |
| -------------   | -------------- | ------ |
| contrôleur = « Accueil » | action = « About » | `/Home/About` |
| contrôleur = « Accueil » | contrôleur = « Order », action = « About » | `/Order/About` |
| contrôleur = « Home », color = « Red » | action = « About » | `/Home/About` |
| contrôleur = « Accueil » | action = « About », de couleur = « Red » | `/Home/About?color=Red`

Si un itinéraire a la valeur par défaut qui ne correspond pas à un paramètre et cette valeur est explicitement fournie, il doit correspondre à la valeur par défaut. Exemple :

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
  defaults: new { controller = "Blog", action = "ReadPost" });
```

Génération de liens génère un lien pour cet itinéraire uniquement lorsque les valeurs correspondantes pour le contrôleur et d’action sont fournis.

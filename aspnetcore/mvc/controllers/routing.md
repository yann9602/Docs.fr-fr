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
ms.openlocfilehash: cc3277400aee956f47c53e5a4f3d4e84d3a3d1a3
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="routing-to-controller-actions"></a><span data-ttu-id="ba6d9-103">Routage vers les Actions de contrôleur</span><span class="sxs-lookup"><span data-stu-id="ba6d9-103">Routing to Controller Actions</span></span>

<span data-ttu-id="ba6d9-104">Par [Ryan Nowak](https://github.com/rynowak) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ba6d9-104">By [Ryan Nowak](https://github.com/rynowak) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ba6d9-105">ASP.NET Core MVC utilise le routage [intergiciel (middleware)](../../fundamentals/middleware.md) pour faire correspondre les URL des demandes entrantes et de les mapper à des actions.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-105">ASP.NET Core MVC uses the Routing [middleware](../../fundamentals/middleware.md) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="ba6d9-106">Itinéraires sont définis dans le code de démarrage ou d’attributs.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-106">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="ba6d9-107">Itinéraires décrivent comment les chemins d’accès de l’URL doivent correspondre aux actions.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-107">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="ba6d9-108">Itinéraires sont également utilisés pour générer des URL (pour les liens) envoyés dans les réponses.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-108">Routes are also used to generate URLs (for links) sent out in responses.</span></span> 

<span data-ttu-id="ba6d9-109">Actions sont soit traditionnellement routées ou attribut routés.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-109">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ba6d9-110">En plaçant un itinéraire sur le contrôleur ou de l’action, il attribut routé.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-110">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ba6d9-111">Consultez [mixte routage](#routing-mixed-ref-label) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-111">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="ba6d9-112">Ce document explique les interactions entre MVC et de routage et de la création d’applications MVC utilise des fonctionnalités de routage.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-112">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="ba6d9-113">Consultez [routage](xref:fundamentals/routing) pour plus d’informations sur le routage avancé.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-113">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="ba6d9-114">Configuration de routage intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="ba6d9-114">Setting up Routing Middleware</span></span>

<span data-ttu-id="ba6d9-115">Dans votre *configurer* (méthode), vous pouvez voir code semblable à :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-115">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ba6d9-116">À l’intérieur de l’appel à `UseMvc`, `MapRoute` est utilisé pour créer un seul itinéraire, nous allons faire référence à la `default` itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-116">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="ba6d9-117">La plupart des applications MVC utilisera un itinéraire avec un modèle semblable à la `default` itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-117">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="ba6d9-118">Le modèle d’itinéraire `"{controller=Home}/{action=Index}/{id?}"` peut correspondre à un chemin d’accès de l’URL comme `/Products/Details/5` et extrait les valeurs d’itinéraire `{ controller = Products, action = Details, id = 5 }` par le chemin d’accès de création de jetons.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-118">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="ba6d9-119">MVC tentent de trouver un contrôleur nommé `ProductsController` et exécuter l’action `Details`:</span><span class="sxs-lookup"><span data-stu-id="ba6d9-119">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="ba6d9-120">Notez que dans cet exemple, la liaison de modèle serait utiliser la valeur de `id = 5` pour définir le `id` paramètre `5` lors de l’appel de cette action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-120">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="ba6d9-121">Consultez le [liaison de modèle](../models/model-binding.md) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-121">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="ba6d9-122">À l’aide de la `default` itinéraire :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-122">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="ba6d9-123">Le modèle d’itinéraire :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-123">The route template:</span></span>

* <span data-ttu-id="ba6d9-124">`{controller=Home}`définit `Home` comme la valeur par défaut`controller`</span><span class="sxs-lookup"><span data-stu-id="ba6d9-124">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="ba6d9-125">`{action=Index}`définit `Index` comme la valeur par défaut`action`</span><span class="sxs-lookup"><span data-stu-id="ba6d9-125">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="ba6d9-126">`{id?}`définit `id` comme facultatifs</span><span class="sxs-lookup"><span data-stu-id="ba6d9-126">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="ba6d9-127">Valeur par défaut et les paramètres de routage facultatif n’avez pas besoin d’être présents dans le chemin d’accès d’URL pour une correspondance.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-127">Default and optional route parameters do not need to be present in the URL path for a match.</span></span> <span data-ttu-id="ba6d9-128">Consultez [référence de modèle d’itinéraire](../../fundamentals/routing.md#route-template-reference) pour une description détaillée de la syntaxe de modèle d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-128">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="ba6d9-129">`"{controller=Home}/{action=Index}/{id?}"`peut faire correspondre le chemin d’accès URL `/` et produit les valeurs d’itinéraire `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-129">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="ba6d9-130">Les valeurs de `controller` et `action` rendre utilisent des valeurs par défaut, `id` ne produisent pas de valeur, car il n’existe aucun segment correspondant dans le chemin d’accès d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-130">The values for `controller` and `action` make use of the default values, `id` does not produce a value since there is no corresponding segment in the URL path.</span></span> <span data-ttu-id="ba6d9-131">MVC Utilisez ces valeurs d’itinéraire pour sélectionner le `HomeController` et `Index` action :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-131">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="ba6d9-132">À l’aide de cette définition du contrôleur et le modèle d’itinéraire, le `HomeController.Index` action devraient être exécutée pour un des chemins d’URL suivants :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-132">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="ba6d9-133">La méthode pratique `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="ba6d9-133">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="ba6d9-134">Peut être utilisé pour remplacer :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-134">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ba6d9-135">`UseMvc`et `UseMvcWithDefaultRoute` ajouter une instance de `RouterMiddleware` au pipeline intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="ba6d9-135">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="ba6d9-136">MVC n’interagit pas directement avec un intergiciel (middleware) et utilise le routage pour gérer les demandes.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-136">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="ba6d9-137">MVC est connecté aux itinéraires via une instance de `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-137">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="ba6d9-138">Le code à l’intérieur de `UseMvc` est semblable au suivant :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-138">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="ba6d9-139">`UseMvc`ne définit pas directement les itinéraires, il ajoute un espace réservé à la collection d’itinéraires pour le `attribute` itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-139">`UseMvc` does not directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="ba6d9-140">La surcharge `UseMvc(Action<IRouteBuilder>)` vous permet d’ajouter vos propres itinéraires et prend également en charge le routage d’attributs.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-140">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="ba6d9-141">`UseMvc`et toutes ses variations ajoute un espace réservé pour l’itinéraire d’attribut - attribut routage est toujours disponible, quelle que soit la façon dont vous configurez `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-141">`UseMvc` and all of its variations adds a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="ba6d9-142">`UseMvcWithDefaultRoute`définit un itinéraire par défaut et prend en charge le routage d’attributs.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-142">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="ba6d9-143">Le [attribut routage](#attribute-routing-ref-label) comprend plus de détails sur le routage d’attributs.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-143">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="ba6d9-144">Le routage classique</span><span class="sxs-lookup"><span data-stu-id="ba6d9-144">Conventional routing</span></span>

<span data-ttu-id="ba6d9-145">Le `default` itinéraire :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-145">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="ba6d9-146">est un exemple d’un *le routage classique*.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-146">is an example of a *conventional routing*.</span></span> <span data-ttu-id="ba6d9-147">Nous appelons ce style *le routage classique* , car elle établit un *convention* des chemins d’accès de l’URL :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-147">We call this style *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="ba6d9-148">le premier segment de chemin d’accès correspond au nom du contrôleur</span><span class="sxs-lookup"><span data-stu-id="ba6d9-148">the first path segment maps to the controller name</span></span>

* <span data-ttu-id="ba6d9-149">la seconde mappe le nom de l’action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-149">the second maps to the action name.</span></span>

* <span data-ttu-id="ba6d9-150">le troisième segment est utilisé pour un élément facultatif `id` utilisée pour mapper à une entité de modèle</span><span class="sxs-lookup"><span data-stu-id="ba6d9-150">the third segment is used for an optional `id` used to map to a model entity</span></span>

<span data-ttu-id="ba6d9-151">L’utilisation de cette `default` itinéraire, le chemin d’accès URL `/Products/List` mappe à la `ProductsController.List` action, et `/Blog/Article/17` mappe à `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-151">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="ba6d9-152">Ce mappage est basé sur les noms de contrôleur et d’action **uniquement** et n’est pas basé sur les espaces de noms, les emplacements des fichiers sources ou les paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-152">This mapping is based on the controller and action names **only** and is not based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="ba6d9-153">À l’aide de routage classique avec l’itinéraire par défaut vous permet de créer rapidement de l’application sans avoir à élaborer un nouveau modèle d’URL pour chaque action que vous définissez.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-153">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="ba6d9-154">Pour une application avec des actions CRUD style, ayant la cohérence pour les URL entre vos contrôleurs peut aider à simplifier votre code et rendre votre interface utilisateur plus prévisible.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-154">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="ba6d9-155">Le `id` est défini comme étant facultatifs par le modèle d’itinéraire, ce qui signifie que que vos actions peuvent s’exécuter sans l’ID fourni dans le cadre de l’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-155">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="ba6d9-156">Généralement ce qui se produira si `id` est omis de l’URL est qu’elle est définie `0` par la liaison de modèle et par conséquent aucune entité ne se trouve dans la base de données de correspondance `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-156">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="ba6d9-157">Routage d’attributs vous donne un contrôle précis pour rendre le code requis pour certaines actions et pas pour d’autres.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-157">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="ba6d9-158">Par convention, la documentation inclut comme paramètres facultatifs `id` lorsqu’ils sont susceptibles d’apparaître dans une utilisation correcte.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-158">By convention the documentation will include optional parameters like `id` when they are likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="ba6d9-159">Plusieurs itinéraires</span><span class="sxs-lookup"><span data-stu-id="ba6d9-159">Multiple routes</span></span>

<span data-ttu-id="ba6d9-160">Vous pouvez ajouter plusieurs itinéraires à l’intérieur de `UseMvc` en ajoutant plusieurs appels de `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-160">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="ba6d9-161">Cela vous permet de définir plusieurs conventions ou ajouter des itinéraires classiques qui sont dédiés à une action spécifique, tel que :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-161">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ba6d9-162">Le `blog` itinéraire ici est un *dédié itinéraire conventionnelle*, ce qui signifie qu’il utilise le système de routage classique, mais est dédié à une action spécifique.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-162">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="ba6d9-163">Étant donné que `controller` et `action` n’apparaissent pas dans le modèle d’itinéraire en tant que paramètres, ils peuvent avoir uniquement les valeurs par défaut et par conséquent, cet itinéraire correspondra toujours à l’action `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-163">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="ba6d9-164">Itinéraires dans la collection d’itinéraires sont ordonnées et sont traités dans l’ordre qu’ils sont ajoutés.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-164">Routes in the route collection are ordered, and will be processed in the order they are added.</span></span> <span data-ttu-id="ba6d9-165">Ainsi, dans cet exemple, le `blog` itinéraire sera tenté avant le `default` itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-165">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="ba6d9-166">*Dédié itinéraires conventionnelles* utilisent souvent des paramètres d’itinéraire de fourre-tout comme `{*article}` pour capturer la partie restante du chemin d’accès d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-166">*Dedicated conventional routes* often use catch-all route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="ba6d9-167">Cela peut rendre un itinéraire 'trop gourmande' ce qui signifie que qu’elle correspond à l’URL que vous souhaitez mettre en correspondance par d’autres itinéraires.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-167">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="ba6d9-168">Placez les itinéraires « gourmandes » plus loin dans la table d’itinéraires pour résoudre ce problème.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-168">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="ba6d9-169">Secours</span><span class="sxs-lookup"><span data-stu-id="ba6d9-169">Fallback</span></span>

<span data-ttu-id="ba6d9-170">Dans le cadre du traitement de requête, MVC vérifie que les valeurs d’itinéraire peuvent être utilisés pour rechercher un contrôleur et une action dans votre application.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-170">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="ba6d9-171">Si les valeurs d’itinéraire ne correspond à une action alors l’itinéraire n’est pas considéré comme une correspondance, et l’itinéraire suivant sera tentée.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-171">If the route values don't match an action then the route is not considered a match, and the next route will be tried.</span></span> <span data-ttu-id="ba6d9-172">Il s’agit *secours*, et il a conçu simplifier les cas où les itinéraires classiques se chevauchent.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-172">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="ba6d9-173">Actions désambiguïser</span><span class="sxs-lookup"><span data-stu-id="ba6d9-173">Disambiguating actions</span></span>

<span data-ttu-id="ba6d9-174">Lorsque deux actions correspondent par routage, MVC doit lever l’ambiguïté pour choisir le candidat « meilleur », sans quoi une exception.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-174">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="ba6d9-175">Exemple :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-175">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="ba6d9-176">Ce contrôleur définit deux actions qui correspondrait au chemin d’accès URL `/Products/Edit/17` et des données d’itinéraire `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-176">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="ba6d9-177">Il s’agit d’un modèle par défaut pour les contrôleurs MVC où `Edit(int)` présente un formulaire pour modifier un produit, et `Edit(int, Product)` traite le formulaire publié.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-177">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="ba6d9-178">Pour permettre cela MVC devra choisir `Edit(int, Product)` lorsque la demande est HTTP `POST` et `Edit(int)` lorsque le verbe HTTP est autre chose.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-178">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="ba6d9-179">Le `HttpPostAttribute` ( `[HttpPost]` ) est une implémentation de `IActionConstraint` qui autorise uniquement l’action à être sélectionné lorsque le verbe HTTP est `POST`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-179">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="ba6d9-180">La présence d’un `IActionConstraint` rend le `Edit(int, Product)` 'mieux' correspond à `Edit(int)`, de sorte que `Edit(int, Product)` sont essayés en premier.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-180">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="ba6d9-181">Vous devrez écrire personnalisée `IActionConstraint` implémentations dans des scénarios spécifiques, mais il. il est important de comprendre le rôle d’attributs comme `HttpPostAttribute` -des attributs similaires sont définies pour les autres verbes HTTP.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-181">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="ba6d9-182">Dans le routage classique, il est courant pour les actions à utiliser le même nom d’action lorsqu’elles font partie d’un `show form -> submit form` flux de travail.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-182">In conventional routing it's common for actions to use the same action name when they are part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="ba6d9-183">L’avantage de ce modèle est deviennent plus évident après avoir examiné les [IActionConstraint de présentation](#understanding-iactionconstraint) section.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-183">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="ba6d9-184">Si plusieurs itinéraires correspondent et MVC ne peut pas trouver un itinéraire « meilleur », elle lève une `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-184">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="ba6d9-185">Noms d’itinéraires</span><span class="sxs-lookup"><span data-stu-id="ba6d9-185">Route names</span></span>

<span data-ttu-id="ba6d9-186">Les chaînes `"blog"` et `"default"` dans les exemples suivants sont des noms de l’itinéraire :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-186">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>


```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ba6d9-187">Les noms d’itinéraires nommez l’itinéraire logique afin que l’itinéraire nommé peut être utilisé pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-187">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="ba6d9-188">Cela simplifie considérablement la création d’URL lorsque le classement des itinéraires peut rendre génération d’URL compliquée.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-188">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="ba6d9-189">Les noms d’itinéraires doivent être unique à l’échelle de l’application.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-189">Routes names must be unique application-wide.</span></span>

<span data-ttu-id="ba6d9-190">Les noms d’itinéraires n’ont aucun impact sur l’URL correspondant ou le traitement des requêtes ; ils sont utilisés uniquement pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-190">Route names have no impact on URL matching or handling of requests; they are used only for URL generation.</span></span> <span data-ttu-id="ba6d9-191">[Routage](xref:fundamentals/routing) contient plus d’informations sur la génération d’URL, y compris la génération d’URL dans les programmes d’assistance MVC spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-191">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="ba6d9-192">Routage d’attributs</span><span class="sxs-lookup"><span data-stu-id="ba6d9-192">Attribute routing</span></span>

<span data-ttu-id="ba6d9-193">Routage d’attributs d’utilise un ensemble d’attributs pour mapper les actions directement aux modèles d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-193">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="ba6d9-194">Dans l’exemple suivant, `app.UseMvc();` est utilisé dans le `Configure` (méthode) et aucun itinéraire n’est passée.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-194">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="ba6d9-195">Le `HomeController` correspond à un ensemble d’URL similaires à l’itinéraire par défaut `{controller=Home}/{action=Index}/{id?}` correspondrait à :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-195">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="ba6d9-196">Le `HomeController.Index()` action sera exécutée pour un des chemins d’accès de l’URL `/`, `/Home`, ou `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-196">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="ba6d9-197">Cet exemple met en surbrillance une programmation principale différence entre l’attribut et de routage classique.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-197">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="ba6d9-198">Routage d’attributs nécessite plus d’entrée pour spécifier un itinéraire ; l’itinéraire par défaut classiques gère plus succinctement les itinéraires.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-198">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="ba6d9-199">Toutefois, routage d’attributs permet (et requiert) d’un contrôle précis dont les modèles d’itinéraire s’appliquent à chaque action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-199">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="ba6d9-200">Avec l’attribut routage le nom du contrôleur et les noms d’actions lire **aucune** rôle dans lequel l’action est sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-200">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="ba6d9-201">Cet exemple correspond aux mêmes URL que l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-201">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="ba6d9-202">Les modèles d’itinéraire ne définissent pas les paramètres d’itinéraire pour `action`, `area`, et `controller`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-202">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="ba6d9-203">En fait, les paramètres d’itinéraire ne sont pas autorisés dans les itinéraires d’attribut.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-203">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="ba6d9-204">Étant donné que le modèle d’itinéraire est déjà associé à une action, il n’aurait aucun sens pour analyser le nom d’action de l’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-204">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="ba6d9-205">Attribut de routage avec des attributs Http [verbe]</span><span class="sxs-lookup"><span data-stu-id="ba6d9-205">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="ba6d9-206">Routage d’attributs peut s’utiliser de la `Http[Verb]` des attributs tels que `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-206">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="ba6d9-207">Tous ces attributs peuvent accepter un modèle d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-207">All of these attributes can accept a route template.</span></span> <span data-ttu-id="ba6d9-208">Cet exemple montre deux actions qui correspondent au modèle d’itinéraire même :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-208">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="ba6d9-209">Pour un chemin d’accès de l’URL comme `/products` le `ProductsApi.ListProducts` action sera exécutée lorsque le verbe HTTP est `GET` et `ProductsApi.CreateProduct` sera exécutée lorsque le verbe HTTP est `POST`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-209">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="ba6d9-210">Tout d’abord le routage attribut correspond à l’URL par rapport au jeu de modèles d’itinéraire défini par les attributs d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-210">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="ba6d9-211">Une fois qu’un modèle d’itinéraire correspond à, `IActionConstraint` les contraintes sont appliquées pour déterminer quelles actions peuvent être exécutées.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-211">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="ba6d9-212">Lors de la génération d’une API REST, il est rare que vous souhaitez utiliser `[Route(...)]` sur une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-212">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method.</span></span> <span data-ttu-id="ba6d9-213">Il est préférable d’utiliser la plus spécifique `Http*Verb*Attributes` pour être précis sur ce que votre API prend en charge.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-213">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="ba6d9-214">Les clients d’API REST doivent connaître les chemins d’accès et les verbes HTTP mappent à des opérations logiques spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-214">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="ba6d9-215">Dans la mesure où un itinéraire d’attribut s’applique à une action spécifique, il est facile d’apporter des paramètres requis dans le cadre de la définition de modèle d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-215">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="ba6d9-216">Dans cet exemple, `id` est requis dans le cadre du chemin d’accès d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-216">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ba6d9-217">Le `ProductsApi.GetProduct(int)` action sera exécutée pour un chemin d’accès de l’URL comme `/products/3` , mais pas pour un chemin d’accès de l’URL comme `/products`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-217">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="ba6d9-218">Consultez [routage](../../fundamentals/routing.md) pour obtenir une description complète des modèles d’itinéraire et les options associées.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-218">See [Routing](../../fundamentals/routing.md) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="ba6d9-219">Nom de l’itinéraire</span><span class="sxs-lookup"><span data-stu-id="ba6d9-219">Route Name</span></span>

<span data-ttu-id="ba6d9-220">Le code suivant définit un *nom d’itinéraire* de `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="ba6d9-220">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ba6d9-221">Les noms d’itinéraires peuvent être utilisés pour générer une URL basée sur un itinéraire spécifique.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-221">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="ba6d9-222">Les noms d’itinéraires n’ont aucun impact sur l’URL de comportement de routage de correspondance et sont utilisés uniquement pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-222">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="ba6d9-223">Les noms d’itinéraires doivent être unique à l’échelle de l’application.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-223">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="ba6d9-224">Ceci contraste avec conventionnels *l’itinéraire par défaut*, qui définit le `id` paramètre comme facultatif (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="ba6d9-224">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="ba6d9-225">Cette possibilité de spécifier précisément les API présente des avantages, par exemple pour autoriser `/products` et `/products/5` doit être distribué à des actions différentes.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-225">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="ba6d9-226">Itinéraires de combinaison</span><span class="sxs-lookup"><span data-stu-id="ba6d9-226">Combining routes</span></span>

<span data-ttu-id="ba6d9-227">Pour rendre attribut routage moins répétitives, attributs d’itinéraire sur le contrôleur sont combinées avec des attributs d’itinéraire sur les actions individuelles.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-227">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="ba6d9-228">Les modèles d’itinéraire définis sur le contrôleur sont ajoutés à des modèles d’itinéraire sur les actions.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-228">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="ba6d9-229">En plaçant un attribut d’itinéraire sur le contrôleur, **tous les** actions dans le contrôleur utilisent le routage d’attributs.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-229">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="ba6d9-230">Dans cet exemple, le chemin d’accès URL `/products` peut correspondre à `ProductsApi.ListProducts`et le chemin d’accès URL `/products/5` peut correspondre à `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-230">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="ba6d9-231">Ces deux actions uniquement en correspondance HTTP `GET` , car elles sont décorées avec le `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-231">Both of these actions only match HTTP `GET` because they are decorated with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="ba6d9-232">Acheminer les modèles appliqués à une action qui commencent par un `/` ne pas combiner avec les modèles d’itinéraire appliqués au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-232">Route templates applied to an action that begin with a `/` do not get combined with route templates applied to the controller.</span></span> <span data-ttu-id="ba6d9-233">Cet exemple correspond à un ensemble de chemins d’accès de URL semblables à la *l’itinéraire par défaut*.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-233">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="ba6d9-234">Classement des itinéraires d’attribut</span><span class="sxs-lookup"><span data-stu-id="ba6d9-234">Ordering attribute routes</span></span>

<span data-ttu-id="ba6d9-235">Contrairement aux itinéraires classiques qui s’exécutent dans un ordre défini, le routage d’attributs génère une arborescence et correspond à tous les itinéraires simultanément.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-235">In contrast to conventional routes which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="ba6d9-236">Il se comporte comme-si les entrées d’itinéraire ont été placées dans un classement idéale ; les itinéraires plus spécifiques ont une chance de s’exécuter avant les itinéraires plus générales.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-236">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="ba6d9-237">Par exemple, un itinéraire comme `blog/search/{topic}` est plus spécifique à un itinéraire comme `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-237">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="ba6d9-238">Logiquement parlant le `blog/search/{topic}` itinéraire 'exécute' en premier lieu, par défaut, car il s’agit de l’ordre uniquement pratique.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-238">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="ba6d9-239">À l’aide de routage classique, le développeur est chargé pour la sélection élective des itinéraires dans l’ordre souhaité.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-239">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="ba6d9-240">Itinéraires d’attribut peuvent configurer une commande, à l’aide de la `Order` propriété de tous les attributs d’itinéraire prévues.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-240">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="ba6d9-241">Les itinéraires sont traités selon un ordre croissant de le `Order` propriété.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-241">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="ba6d9-242">L’ordre par défaut est `0`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-242">The default order is `0`.</span></span> <span data-ttu-id="ba6d9-243">Définition d’un itinéraire à l’aide `Order = -1` s’exécutera avant les itinéraires qui ne définissent pas une commande.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-243">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="ba6d9-244">Définition d’un itinéraire à l’aide `Order = 1` s’exécutera après le classement d’itinéraire par défaut.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-244">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="ba6d9-245">Éviter de dépendre `Order`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-245">Avoid depending on `Order`.</span></span> <span data-ttu-id="ba6d9-246">Si votre espace URL requiert des valeurs d’ordre explicites pour acheminer correctement, il est probablement à confusion pour les clients.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-246">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="ba6d9-247">En général le routage d’attributs sélectionnera l’itinéraire correct avec l’URL correspondant.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-247">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="ba6d9-248">Si l’ordre par défaut utilisé pour la génération d’URL ne fonctionne pas, à l’aide de nom d’itinéraire comme une substitution est généralement plus simple que l’application le `Order` propriété.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-248">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="ba6d9-249">Dans les modèles d’itinéraire du remplacement de jeton ([controller], [action], [domaine])</span><span class="sxs-lookup"><span data-stu-id="ba6d9-249">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="ba6d9-250">Pour plus de commodité, prend en charge les itinéraires d’attribut *du remplacement de jeton* en mettant un jeton entre accolades du carré (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="ba6d9-250">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="ba6d9-251">Les jetons `[action]`, `[area]`, et `[controller]` sera remplacé par les valeurs du nom d’action, nom de la zone et du nom de contrôleur à partir de l’action dans lequel l’itinéraire est défini.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-251">The tokens `[action]`, `[area]`, and `[controller]` will be replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="ba6d9-252">Dans cet exemple montre comment les actions peuvent correspondre à des chemins d’URL comme décrit dans les commentaires :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-252">In this example the actions can match URL paths as described in the comments:</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="ba6d9-253">Remplacement des jetons s’effectue lors de la dernière étape de la création d’itinéraires d’attribut.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-253">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="ba6d9-254">L’exemple ci-dessus sera se comportent comme le code suivant :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-254">The above example will behave the same as the following code:</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="ba6d9-255">Itinéraires d’attribut peuvent aussi être combinés avec héritage.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-255">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="ba6d9-256">Cela est particulièrement puissante combiné avec le remplacement des jetons.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-256">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="ba6d9-257">Remplacement des jetons s’applique également aux noms d’itinéraires définis par les itinéraires d’attribut.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-257">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="ba6d9-258">`[Route("[controller]/[action]", Name="[controller]_[action]")]`génère un nom d’itinéraire unique pour chaque action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-258">`[Route("[controller]/[action]", Name="[controller]_[action]")]` will generate a unique route name for each action.</span></span>

<span data-ttu-id="ba6d9-259">Pour faire correspondre le délimiteur de littéral remplacement des jetons `[` ou `]`, échapper en répétant le caractère (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="ba6d9-259">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="ba6d9-260">Plusieurs itinéraires</span><span class="sxs-lookup"><span data-stu-id="ba6d9-260">Multiple Routes</span></span>

<span data-ttu-id="ba6d9-261">Attribut de routage prend en charge la définition de plusieurs itinéraires pour atteindre la même action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-261">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="ba6d9-262">L’utilisation la plus courante de ce consiste à reproduire le comportement de la *l’itinéraire par défaut classiques* comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-262">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="ba6d9-263">Placement de plusieurs attributs d’itinéraire sur le contrôleur signifie que chacun d’eux permet de combiner avec chacun des attributs d’itinéraire sur les méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-263">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="ba6d9-264">Lorsque plusieurs attributs d’itinéraire (qui implémentent `IActionConstraint`) sont placés sur une action, puis chaque contrainte action associe le modèle d’itinéraire à partir de l’attribut qui l’a défini.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-264">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="ba6d9-265">À l’aide de plusieurs itinéraires sur les actions permettre sembler puissant, il est préférable de conserver l’espace d’URL de votre application simple et bien définie.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-265">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="ba6d9-266">Utilisez plusieurs itinéraires sur les actions lorsque cela est nécessaire, par exemple, pour prendre en charge les clients existants.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-266">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="ba6d9-267">Spécification des paramètres d’itinéraire d’attribut facultatif, les valeurs par défaut et les contraintes</span><span class="sxs-lookup"><span data-stu-id="ba6d9-267">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="ba6d9-268">Itinéraires d’attribut prend en charge la même syntaxe inline en tant qu’itinéraires classiques pour spécifier des paramètres facultatifs, les valeurs par défaut et les contraintes.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-268">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="ba6d9-269">Consultez [référence de modèle d’itinéraire](../../fundamentals/routing.md#route-template-reference) pour une description détaillée de la syntaxe de modèle d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-269">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="ba6d9-270">Attributs d’itinéraire personnalisées à l’aide`IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="ba6d9-270">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="ba6d9-271">Tous les attributs d’itinéraire fournis dans le framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implémentent le `IRouteTemplateProvider` interface.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-271">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="ba6d9-272">MVC recherche les attributs sur les classes de contrôleur et les méthodes d’action lors de l’application démarre et utilise ceux qui implémentent `IRouteTemplateProvider` pour générer l’ensemble initial des itinéraires.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-272">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="ba6d9-273">Vous pouvez implémenter `IRouteTemplateProvider` pour définir vos propres attributs d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-273">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="ba6d9-274">Chaque `IRouteTemplateProvider` vous permet de définir une gamme unique avec un modèle d’itinéraire personnalisées, classer et nommer :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-274">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="ba6d9-275">L’attribut de l’exemple ci-dessus définit automatiquement le `Template` à `"api/[controller]"` lorsque `[MyApiController]` est appliqué.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-275">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="ba6d9-276">À l’aide du modèle d’Application pour personnaliser les itinéraires d’attribut</span><span class="sxs-lookup"><span data-stu-id="ba6d9-276">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="ba6d9-277">Le *modèle d’application* est un modèle d’objet créé au démarrage avec toutes les métadonnées utilisée par MVC pour router et exécuter vos actions.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-277">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="ba6d9-278">Le *modèle d’application* inclut toutes les données collectées à partir des attributs d’itinéraire (via `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="ba6d9-278">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="ba6d9-279">Vous pouvez écrire *conventions* pour modifier le modèle d’application au moment du démarrage pour personnaliser le comporte de routage.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-279">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="ba6d9-280">Cette section montre un exemple simple de personnalisation de routage à l’aide du modèle d’application.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-280">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[Main](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="ba6d9-281">Mixte de routage : routage classique de routage et d’attribut</span><span class="sxs-lookup"><span data-stu-id="ba6d9-281">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="ba6d9-282">Les applications MVC peuvent combiner l’utilisation de routage classique et le routage de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-282">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="ba6d9-283">Il est courant pour utiliser les itinéraires classiques pour les contrôleurs de servir des pages HTML pour les navigateurs et l’attribut de routage pour les contrôleurs servant d’API REST.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-283">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="ba6d9-284">Actions sont soit traditionnellement routées ou attribut routés.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-284">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ba6d9-285">En plaçant un itinéraire sur le contrôleur ou de l’action, il attribut routé.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-285">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ba6d9-286">Les actions qui définissent des itinéraires d’attribut n’est pas accessible via les itinéraires classiques et vice versa.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-286">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="ba6d9-287">**N’importe quel** attribut d’itinéraire sur le contrôleur rend toutes les actions dans l’attribut de contrôleur routé.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-287">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="ba6d9-288">Toute la différence entre les deux types de systèmes de routage consiste à appliquée après une URL correspond à un modèle d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-288">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="ba6d9-289">Dans le routage classique, les valeurs d’itinéraire à partir de la correspondance sont utilisés pour choisir l’action et le contrôleur à partir d’une table de recherche de toutes les actions de routage classiques.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-289">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="ba6d9-290">Dans le routage de l’attribut, chaque modèle est déjà associé avec une action, et aucune recherche supplémentaire n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-290">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="ba6d9-291">Génération d’URL</span><span class="sxs-lookup"><span data-stu-id="ba6d9-291">URL Generation</span></span>

<span data-ttu-id="ba6d9-292">Applications de MVC peuvent utiliser les fonctionnalités de génération de routage URL pour générer des URL des liens vers des actions.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-292">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="ba6d9-293">Génération des URL élimine les URL de coder en dur, rendre votre code plus robuste et plus facile à gérer.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-293">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="ba6d9-294">Cette section se concentre sur les fonctionnalités de génération d’URL fournies par MVC et couvre uniquement les principes de base du fonctionne de la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-294">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="ba6d9-295">Consultez [routage](../../fundamentals/routing.md) pour obtenir une description détaillée de la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-295">See [Routing](../../fundamentals/routing.md) for a detailed description of URL generation.</span></span>

<span data-ttu-id="ba6d9-296">Le `IUrlHelper` interface est l’élément sous-jacent de l’infrastructure entre MVC et le routage pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-296">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="ba6d9-297">Vous trouverez une instance de `IUrlHelper` disponible via le `Url` propriété dans l’affichage des composants, des vues et des contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-297">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="ba6d9-298">Dans cet exemple, le `IUrlHelper` interface est utilisée par le biais du `Controller.Url` propriété pour générer une URL vers une autre action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-298">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="ba6d9-299">Si l’application est à l’aide de la valeur par défaut classique Router, la valeur de la `url` variable sera la chaîne de chemin d’accès d’URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-299">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="ba6d9-300">Ce chemin d’accès de l’URL est créé par le routage en combinant les valeurs d’itinéraire à partir de la demande en cours (valeurs ambiantes), avec les valeurs passées à `Url.Action` et en remplaçant les valeurs dans le modèle d’itinéraire :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-300">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="ba6d9-301">Chaque paramètre d’itinéraire dans le modèle d’itinéraire a sa valeur remplacée par la correspondance des noms avec les valeurs et ambiante.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-301">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="ba6d9-302">Un paramètre d’itinéraire qui n’a pas de valeur peut utiliser une valeur par défaut si elle existe, ou être ignorée si elle est facultative (comme dans le cas de `id` dans cet exemple).</span><span class="sxs-lookup"><span data-stu-id="ba6d9-302">A route parameter that does not have a value can use a default value if it has one, or be skipped if it is optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="ba6d9-303">Génération d’URL échoue si n’importe quel paramètre d’itinéraire requis n’a pas une valeur correspondante.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-303">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="ba6d9-304">En cas de génération d’URL d’un itinéraire, l’itinéraire suivant est tentée jusqu'à ce que tous les itinéraires ont été essayées ou une correspondance est trouvée.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-304">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="ba6d9-305">L’exemple de `Url.Action` ci-dessus suppose que le routage classique, mais fonctionne de génération d’URL de la même façon avec le routage de l’attribut, bien que les concepts sont différents.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-305">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="ba6d9-306">Avec le routage classique, les valeurs d’itinéraire sont utilisés pour développer un modèle et les valeurs d’itinéraire pour `controller` et `action` apparaissent généralement dans ce modèle - cela fonctionne parce que les URL mises en correspondance par le routage adhèrent à un *convention*.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-306">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="ba6d9-307">Dans le routage de l’attribut, les valeurs de l’itinéraire pour `controller` et `action` ne sont pas autorisés à afficher dans le modèle - ils sont utilisés à la place pour rechercher le modèle à utiliser.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-307">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they are instead used to look up which template to use.</span></span>

<span data-ttu-id="ba6d9-308">Cet exemple utilise le routage d’attributs :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-308">This example uses attribute routing:</span></span>

[!code-csharp[Main](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="ba6d9-309">MVC génère une table de recherche de toutes les actions de l’attribut routé et correspond à la `controller` et `action` valeurs pour sélectionner le modèle d’itinéraire à utiliser pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-309">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="ba6d9-310">Dans l’exemple ci-dessus, `custom/url/to/destination` est généré.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-310">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="ba6d9-311">Génération des URL par le nom de l’action</span><span class="sxs-lookup"><span data-stu-id="ba6d9-311">Generating URLs by action name</span></span>

<span data-ttu-id="ba6d9-312">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ba6d9-312">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="ba6d9-313">`Action`) et toutes les surcharges tous sont basés sur cette idée que vous souhaitez spécifier ce que vous créez le lien en spécifiant un nom de contrôleur et le nom de l’action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-313">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="ba6d9-314">Lorsque vous utilisez `Url.Action`, les valeurs de l’itinéraire actuel pour `controller` et `action` sont spécifiées pour vous, la valeur de `controller` et `action` font partie des deux *valeurs ambiantes* **et** *valeurs*.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-314">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="ba6d9-315">La méthode `Url.Action`, utilise toujours les valeurs actuelles des `action` et `controller` et génère un chemin d’accès d’URL qui achemine vers l’action en cours.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-315">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="ba6d9-316">Tente d’utiliser les valeurs dans les valeurs ambiantes pour renseigner les informations que vous n’avez pas fourni lors de la génération d’une URL de routage.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-316">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="ba6d9-317">À l’aide d’un itinéraire comme `{a}/{b}/{c}/{d}` et valeurs ambiantes `{ a = Alice, b = Bob, c = Carol, d = David }`, routage possède suffisamment d’informations pour générer une URL sans aucune valeur supplémentaire - étant donné que tous les router paramètres ont une valeur.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-317">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="ba6d9-318">Si vous avez ajouté la valeur `{ d = Donovan }`, la valeur `{ d = David }` est ignoré, et le chemin d’accès URL généré serait `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-318">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="ba6d9-319">Chemins d’accès d’URL sont hiérarchiques.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-319">URL paths are hierarchical.</span></span> <span data-ttu-id="ba6d9-320">Dans l’exemple ci-dessus, si vous avez ajouté la valeur `{ c = Cheryl }`, les deux valeurs `{ c = Carol, d = David }` est ignoré.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-320">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="ba6d9-321">Dans ce cas nous n’avons plus une valeur pour `d` et fera échouer la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-321">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="ba6d9-322">Vous devez spécifier la valeur souhaitée de `c` et `d`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-322">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="ba6d9-323">Vous pouvez vous attendre à ce problème avec l’itinéraire par défaut (`{controller}/{action}/{id?}`)- mais vous rencontrerez rarement ce comportement en pratique, en tant que `Url.Action` seront toujours spécifier explicitement un `controller` et `action` valeur.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-323">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="ba6d9-324">Les surcharges plus de `Url.Action` prennent également un autre *valeurs d’itinéraire* objet pour fournir des valeurs autres que pour les paramètres d’itinéraire `controller` et `action`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-324">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="ba6d9-325">Vous verrez plus couramment utilisée avec `id` comme `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-325">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="ba6d9-326">Par convention le *valeurs d’itinéraire* objet est généralement un objet de type anonyme, mais il peut également être un `IDictionary<>` ou un *brut ancien objet .NET*.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-326">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="ba6d9-327">Toutes les valeurs d’itinéraire supplémentaires qui ne correspondent pas aux paramètres d’itinéraire sont placées dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-327">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="ba6d9-328">Pour créer une URL absolue, utilisez une surcharge qui accepte un `protocol`:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="ba6d9-328">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="ba6d9-329">Génération des URL par itinéraire</span><span class="sxs-lookup"><span data-stu-id="ba6d9-329">Generating URLs by route</span></span>

<span data-ttu-id="ba6d9-330">Le code ci-dessus démontré la génération d’une URL en passant le nom d’action et de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-330">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="ba6d9-331">`IUrlHelper`fournit également le `Url.RouteUrl` famille de méthodes.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-331">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="ba6d9-332">Ces méthodes sont similaires aux `Url.Action`, mais ils ne copient pas les valeurs actuelles des `action` et `controller` pour les valeurs d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-332">These methods are similar to `Url.Action`, but they do not copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="ba6d9-333">L’utilisation la plus courante consiste à spécifier un nom d’itinéraire à utiliser un itinéraire spécifique pour générer l’URL, généralement *sans* spécifiant un nom de contrôleur ou d’action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-333">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="ba6d9-334">Génération des URL au format HTML</span><span class="sxs-lookup"><span data-stu-id="ba6d9-334">Generating URLs in HTML</span></span>

<span data-ttu-id="ba6d9-335">`IHtmlHelper`Fournit la `HtmlHelper` méthodes `Html.BeginForm` et `Html.ActionLink` pour générer `<form>` et `<a>` éléments respectivement.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-335">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="ba6d9-336">Ces méthodes utilisent le `Url.Action` méthode pour générer une URL et ils acceptent les arguments similaires.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-336">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="ba6d9-337">Le `Url.RouteUrl` être consultées conjointement pour `HtmlHelper` sont `Html.BeginRouteForm` et `Html.RouteLink` qui ont des fonctionnalités similaires.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-337">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="ba6d9-338">TagHelpers générer des URL via le `form` TagHelper et `<a>` TagHelper.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-338">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="ba6d9-339">Ces deux utilisent `IUrlHelper` pour leur implémentation.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-339">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="ba6d9-340">Consultez [utilisation des formulaires](../views/working-with-forms.md) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-340">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="ba6d9-341">Dans les vues, les `IUrlHelper` est disponible via le `Url` propriété pour n’importe quel ad hoc de génération d’URL non couverte par la commande ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-341">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="ba6d9-342">Génération des URL dans les résultats d’Action</span><span class="sxs-lookup"><span data-stu-id="ba6d9-342">Generating URLS in Action Results</span></span>

<span data-ttu-id="ba6d9-343">Les exemples précédents ont montré à l’aide de `IUrlHelper` dans un contrôleur, tandis que l’utilisation la plus courante dans un contrôleur consiste à générer une URL en tant que partie d’un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-343">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="ba6d9-344">Le `ControllerBase` et `Controller` classes de base fournissent des méthodes pratiques pour les résultats d’action qui font référence à une autre action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-344">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="ba6d9-345">Une utilisation typique consiste à rediriger après acceptation de l’entrée d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-345">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="ba6d9-346">Les méthodes de fabrique de résultats action suivent un modèle similaire aux méthodes `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-346">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="ba6d9-347">Cas spécial pour les itinéraires conventionnelles dédiés</span><span class="sxs-lookup"><span data-stu-id="ba6d9-347">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="ba6d9-348">Le routage classique peut utiliser un type spécial de définition d’itinéraire appelée un *dédié itinéraire conventionnelle*.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-348">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="ba6d9-349">Dans l’exemple ci-dessous, l’itinéraire nommé `blog` est un itinéraire conventionnel dédié.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-349">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ba6d9-350">À l’aide de ces définitions de route, `Url.Action("Index", "Home")` génère le chemin d’accès URL `/` avec la `default` itinéraire, mais pourquoi ?</span><span class="sxs-lookup"><span data-stu-id="ba6d9-350">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="ba6d9-351">Vous pouvez deviner les valeurs d’itinéraire `{ controller = Home, action = Index }` serait suffisant pour générer une URL avec `blog`, et le résultat serait `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-351">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="ba6d9-352">Itinéraires conventionnelles dédiés s’appuient sur un comportement spécial de valeurs par défaut qui n’ont pas de paramètre d’itinéraire correspondant qui empêche l’itinéraire « trop gourmande » avec la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-352">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="ba6d9-353">Dans ce cas les valeurs par défaut sont `{ controller = Blog, action = Article }`et ni `controller` ni `action` apparaît sous la forme d’un paramètre d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-353">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="ba6d9-354">Lorsque le routage effectue une génération d’URL, les valeurs fournies doivent correspondre aux valeurs de la valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-354">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="ba6d9-355">Génération d’URL à l’aide `blog` échoue, car les valeurs `{ controller = Home, action = Index }` ne correspondent pas `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-355">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="ba6d9-356">Routage puis revient pour essayer `default`, ce qui aboutit.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-356">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="ba6d9-357">Zones</span><span class="sxs-lookup"><span data-stu-id="ba6d9-357">Areas</span></span>

<span data-ttu-id="ba6d9-358">[Zones](areas.md) sont une fonctionnalité MVC utilisée pour organiser des fonctionnalités connexes dans un groupe comme une structure de dossier (pour les vues) et le routage-espace de noms distinct (pour les actions de contrôleur).</span><span class="sxs-lookup"><span data-stu-id="ba6d9-358">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="ba6d9-359">L’utilisation de zones permet à une application de posséder plusieurs contrôleurs portant le même nom - tant qu’ils disposent différents *zones*.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-359">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="ba6d9-360">L’utilisation de zones crée une hiérarchie à des fins de routage en ajoutant un autre paramètre d’itinéraire, `area` à `controller` et `action`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-360">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="ba6d9-361">Cette section explique comment le routage interagit avec les zones - consultez [zones](areas.md) pour plus d’informations sur l’utilisation des zones avec des vues.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-361">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="ba6d9-362">L’exemple suivant configure MVC pour utiliser l’itinéraire conventionnelle par défaut et un *les itinéraires de zone* pour une zone nommée `Blog`:</span><span class="sxs-lookup"><span data-stu-id="ba6d9-362">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="ba6d9-363">Pour mettre en correspondance un chemin d’accès de l’URL comme `/Manage/Users/AddUser`, le premier itinéraire génère les valeurs d’itinéraire `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-363">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="ba6d9-364">Le `area` itinéraire valeur produite par une valeur par défaut `area`, en fait l’itinéraire créé par `MapAreaRoute` est équivalente à la suivante :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-364">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="ba6d9-365">`MapAreaRoute`Crée un itinéraire à l’aide d’une valeur par défaut et la contrainte pour `area` en utilisant le nom de la zone fournie, dans ce cas `Blog`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-365">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="ba6d9-366">La valeur par défaut garantit que l’itinéraire génère toujours `{ area = Blog, ... }`, la contrainte requiert la valeur `{ area = Blog, ... }` pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-366">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="ba6d9-367">Le routage classique est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-367">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ba6d9-368">En général, les itinéraires avec des zones doivent être placés plus haut dans la table d’itinéraires lorsqu’ils sont plus spécifiques que les itinéraires sans une zone.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-368">In general, routes with areas should be placed earlier in the route table as they are more specific than routes without an area.</span></span>

<span data-ttu-id="ba6d9-369">À l’aide de l’exemple ci-dessus, les valeurs d’itinéraire correspondrait à l’action suivante :</span><span class="sxs-lookup"><span data-stu-id="ba6d9-369">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="ba6d9-370">Le `AreaAttribute` est ce qui indique un contrôleur dans le cadre d’une zone, nous dire que ce contrôleur est dans le `Blog` zone.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-370">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="ba6d9-371">Contrôleurs sans un `[Area]` attribut ne sont pas membres de n’importe quelle zone et seront **pas** correspondent lorsque la `area` valeur de l’itinéraire est fournie par le routage.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-371">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="ba6d9-372">Dans l’exemple suivant, le premier contrôleur répertorié peut correspondre aux valeurs d’itinéraire `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-372">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="ba6d9-373">L’espace de noms de chaque contrôleur est illustrée ici par souci d’exhaustivité, sinon les contrôleurs aurait d’une affectation de noms en conflit et génèrent une erreur du compilateur.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-373">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="ba6d9-374">Espaces de noms de classe ont aucun effet sur le routage de MVC.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-374">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="ba6d9-375">Tout d’abord deux contrôleurs sont membres de domaines et uniquement en correspondance lorsque leur nom de la zone correspondante est fournie par le `area` acheminer de valeur.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-375">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="ba6d9-376">Le contrôleur tiers n’est pas un membre de zone et peut seule correspondance lorsqu’aucune valeur de `area` est fourni par le routage.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-376">The third controller is not a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="ba6d9-377">En termes de mise en correspondance *aucune valeur*, l’absence de la `area` la valeur est identique comme si la valeur de `area` est null ou une chaîne vide.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-377">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="ba6d9-378">Lors de l’exécution d’une action à l’intérieur d’une zone, valeur de l’itinéraire pour `area` peuvent être utilisés comme un *valeur ambiante* pour le routage à utiliser pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-378">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="ba6d9-379">Cela signifie que par défaut zones agissent *rémanentes* pour la génération d’URL comme illustré dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-379">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="ba6d9-380">Présentation des IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="ba6d9-380">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="ba6d9-381">Cette section est une présentation détaillée sur les mécanismes internes de framework et comment MVC choisit une action à exécuter.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-381">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="ba6d9-382">Une application classique n’aurez pas personnalisé`IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="ba6d9-382">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="ba6d9-383">Vous avez utilisé probablement déjà `IActionConstraint` même si vous n’êtes pas familiarisé avec l’interface.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-383">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="ba6d9-384">Le `[HttpGet]` attribut et similaires `[Http-VERB]` attributs implémentent `IActionConstraint` afin de limiter l’exécution d’une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-384">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="ba6d9-385">En supposant que l’itinéraire par défaut classique, le chemin d’accès URL `/Products/Edit` génère les valeurs `{ controller = Products, action = Edit }`, qui correspondrait à **les deux** des actions ci-après.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-385">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="ba6d9-386">Dans `IActionConstraint` terminologie indiquer que ces deux actions sont considérés comme candidats - que les deux correspondent aux données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-386">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="ba6d9-387">Lorsque le `HttpGetAttribute` s’exécute, il indiquera que *Edit()* correspond aux *obtenir* et n’est pas une correspondance pour tous les autres verbes HTTP.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-387">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and is not a match for any other HTTP verb.</span></span> <span data-ttu-id="ba6d9-388">Le `Edit(...)` action ne dispose pas des contraintes définies et donc correspond à n’importe quel verbe HTTP.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-388">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="ba6d9-389">Par conséquent, en supposant un `POST` - uniquement `Edit(...)` correspond à.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-389">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="ba6d9-390">Mais, pour un `GET` les deux actions peuvent correspondre encore aux - Toutefois, une action avec un `IActionConstraint` est toujours considéré comme *mieux* à une action sans.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-390">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="ba6d9-391">Par conséquent, étant donné que `Edit()` a `[HttpGet]` il est considéré comme plus spécifique et est sélectionnée si les deux actions peuvent correspondre.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-391">So because `Edit()` has `[HttpGet]` it is considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="ba6d9-392">Point de vue conceptuel, `IActionConstraint` est une forme de *surcharge*, mais au lieu de la surcharge de méthodes portant le même nom, il est la surcharge entre les actions qui correspondent à la même URL.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-392">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it is overloading between actions that match the same URL.</span></span> <span data-ttu-id="ba6d9-393">Routage d’attributs utilise également `IActionConstraint` et peut générer des actions à partir de différents contrôleurs à la fois être considéré comme candidats.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-393">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="ba6d9-394">Implémentation de IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="ba6d9-394">Implementing IActionConstraint</span></span>

<span data-ttu-id="ba6d9-395">La façon la plus simple d’implémenter un `IActionConstraint` consiste à créer une classe dérivée de `System.Attribute` et placez-le sur vos actions et les contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-395">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="ba6d9-396">MVC détecte automatiquement les `IActionConstraint` qui sont appliquées en tant qu’attributs.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-396">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="ba6d9-397">Vous pouvez utiliser le modèle d’application pour appliquer des contraintes, et c’est probablement l’approche la plus souple car elle vous permet d’accéder à metaprogram comment ils sont appliqués.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-397">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they are applied.</span></span>

<span data-ttu-id="ba6d9-398">Dans l’exemple suivant, une contrainte choisit une action basée sur un *code pays* à partir des données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-398">In the following example a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="ba6d9-399">Le [exemple complet sur GitHub](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="ba6d9-399">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="ba6d9-400">Vous êtes chargé d’implémenter la `Accept` (méthode) et en choisissant un 'Order' pour la contrainte à exécuter.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-400">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="ba6d9-401">Dans ce cas, le `Accept` méthode retourne `true` pour indiquer l’action est une correspondance lorsque le `country` Router correspond à la valeur.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-401">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="ba6d9-402">Cela est différent d’un `RouteValueAttribute` car elle permet de revenir à une action sans attributs.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-402">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="ba6d9-403">L’exemple montre que si vous définissez un `en-US` action puis un code de pays comme `fr-FR` fasse appel à un contrôleur plus générique qui n’a pas `[CountrySpecific(...)]` appliqué.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-403">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that does not have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="ba6d9-404">Le `Order` propriété décide *étape* la contrainte fait partie.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-404">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="ba6d9-405">Contraintes d’action s’exécutent dans des groupes en fonction de la `Order`.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-405">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="ba6d9-406">Par exemple, tous du framework fourni les attributs de méthode HTTP utilisent le même `Order` valeur afin qu’ils s’exécutent dans la même étape.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-406">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="ba6d9-407">Vous pouvez avoir autant d’étapes que vous devez implémenter vos stratégies de sécurité.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-407">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="ba6d9-408">Choisir une valeur pour `Order` réfléchissons ou non une contrainte doit être appliquée avant les méthodes HTTP.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-408">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="ba6d9-409">Les nombres inférieurs exécuté en premier.</span><span class="sxs-lookup"><span data-stu-id="ba6d9-409">Lower numbers run first.</span></span>

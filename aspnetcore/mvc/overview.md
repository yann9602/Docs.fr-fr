---
title: "Vue d’ensemble des principaux d’ASP.NET MVC"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 89af38d1-52e0-4db7-b791-dbce909b0714
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/overview
ms.openlocfilehash: 67394b066c18a149a97b957d6478ba8301ea8147
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="10446-103">Vue d’ensemble des principaux d’ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="10446-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="10446-104">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="10446-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="10446-105">Le cœur de ASP.NET MVC est une infrastructure riche pour la création d’applications web et API à l’aide du modèle-Vue-contrôleur concevoir le modèle.</span><span class="sxs-lookup"><span data-stu-id="10446-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="10446-106">Quel est le modèle MVC ?</span><span class="sxs-lookup"><span data-stu-id="10446-106">What is the MVC pattern?</span></span>

<span data-ttu-id="10446-107">Le modèle d’architecture Model-View-Controller (MVC) sépare une application en trois groupes de composants principaux : les modèles, vues et contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="10446-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="10446-108">Ce modèle permet d’améliorer [séparation des préoccupations](http://deviq.com/separation-of-concerns/).</span><span class="sxs-lookup"><span data-stu-id="10446-108">This pattern helps to achieve [separation of concerns](http://deviq.com/separation-of-concerns/).</span></span> <span data-ttu-id="10446-109">Demandes de l’utilisateur à l’aide de ce modèle, sont acheminés vers un contrôleur qui est responsable de l’utilisation du modèle pour effectuer des actions de l’utilisateur et/ou de récupérer les résultats de requêtes.</span><span class="sxs-lookup"><span data-stu-id="10446-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="10446-110">Le contrôleur choisit la vue à afficher à l’utilisateur et lui fournit toutes les données de modèle qu’il requiert.</span><span class="sxs-lookup"><span data-stu-id="10446-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="10446-111">Le diagramme suivant montre les trois composants principaux et ceux qui font référence aux autres :</span><span class="sxs-lookup"><span data-stu-id="10446-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![Modèle MVC](overview/_static/mvc.png)

<span data-ttu-id="10446-113">Cette limite de responsabilités vous permet de faire évoluer l’application en termes de complexité, car il est plus facile de code, déboguer et tester un élément (modèle, vue ou contrôleur) qui a un seul travail (et suit le [principe de responsabilité unique ](http://deviq.com/single-responsibility-principle/)).</span><span class="sxs-lookup"><span data-stu-id="10446-113">This delineation of responsibilities helps you scale the application in terms of complexity because it’s easier to code, debug, and test something (model, view, or controller) that has a single job (and follows the [Single Responsibility Principle](http://deviq.com/single-responsibility-principle/)).</span></span> <span data-ttu-id="10446-114">Il est plus difficile de mise à jour, de test et le code de débogage qui a des dépendances réparties sur deux ou plusieurs de ces trois zones.</span><span class="sxs-lookup"><span data-stu-id="10446-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="10446-115">Par exemple, logique de l’interface utilisateur a tendance à changer plus fréquemment que la logique métier.</span><span class="sxs-lookup"><span data-stu-id="10446-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="10446-116">Si la présentation code et la logique métier est combinée en un seul objet, vous devez modifier l’objet contenant la logique métier chaque fois que vous modifiez l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="10446-116">If presentation code and business logic are combined in a single object, you have to modify an object containing business logic every time you change the user interface.</span></span> <span data-ttu-id="10446-117">Ceci est susceptible d’introduire des erreurs et nécessitent un nouveau test de toute la logique métier après modification de chaque interface utilisateur minimale.</span><span class="sxs-lookup"><span data-stu-id="10446-117">This is likely to introduce errors and require the retesting of all business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="10446-118">La vue et le contrôleur dépendent du modèle.</span><span class="sxs-lookup"><span data-stu-id="10446-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="10446-119">Toutefois, le modèle dépend de la vue, ni le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="10446-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="10446-120">Il s’agit d’un des principaux avantages de la séparation.</span><span class="sxs-lookup"><span data-stu-id="10446-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="10446-121">Cette séparation permet le modèle pour être générées et testées indépendamment de la présentation visuelle.</span><span class="sxs-lookup"><span data-stu-id="10446-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="10446-122">Responsabilités du modèle</span><span class="sxs-lookup"><span data-stu-id="10446-122">Model Responsibilities</span></span>

<span data-ttu-id="10446-123">Le modèle dans une application MVC représente l’état de l’application et de toute logique métier ou d’opérations qui doivent être effectuées par celui-ci.</span><span class="sxs-lookup"><span data-stu-id="10446-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="10446-124">Logique métier doit être encapsulée dans le modèle, ainsi que toute logique d’implémentation de la persistance de l’état de l’application.</span><span class="sxs-lookup"><span data-stu-id="10446-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="10446-125">Vues fortement typées utilisera généralement types ViewModel spécifiquement conçus pour contenir les données à afficher sur cette vue ; le contrôleur crée et remplit ces instances ViewModel à partir du modèle.</span><span class="sxs-lookup"><span data-stu-id="10446-125">Strongly-typed views will typically use ViewModel types specifically designed to contain the data to display on that view; the controller will create and populate these ViewModel instances from the model.</span></span>

> [!NOTE]
> <span data-ttu-id="10446-126">Il existe de nombreuses façons d’organiser le modèle dans une application qui utilise le modèle architectural MVC.</span><span class="sxs-lookup"><span data-stu-id="10446-126">There are many ways to organize the model in an app that uses the MVC architectural pattern.</span></span> <span data-ttu-id="10446-127">En savoir plus sur certaines [différents types de types de modèles](http://deviq.com/kinds-of-models/).</span><span class="sxs-lookup"><span data-stu-id="10446-127">Learn more about some [different kinds of model types](http://deviq.com/kinds-of-models/).</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="10446-128">Afficher les responsabilités</span><span class="sxs-lookup"><span data-stu-id="10446-128">View Responsibilities</span></span>

<span data-ttu-id="10446-129">Les vues sont chargés pour la présentation du contenu via l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="10446-129">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="10446-130">Ils utilisent le [moteur d’affichage Razor](#razor-view-engine) pour incorporer le code .NET dans le balisage HTML.</span><span class="sxs-lookup"><span data-stu-id="10446-130">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="10446-131">Il doit y avoir minimum de logique dans les vues, et toute logique dans les doit être liés à la présentation du contenu.</span><span class="sxs-lookup"><span data-stu-id="10446-131">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="10446-132">Si vous trouvez la nécessité d’effectuer une grande partie de la logique dans l’affichage des fichiers pour afficher des données à partir d’un modèle complexe, envisagez d’utiliser un [composant de vue](views/view-components.md), ViewModel, ou le modèle d’affichage pour simplifier l’affichage.</span><span class="sxs-lookup"><span data-stu-id="10446-132">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="10446-133">Responsabilités de contrôleur</span><span class="sxs-lookup"><span data-stu-id="10446-133">Controller Responsibilities</span></span>

<span data-ttu-id="10446-134">Les contrôleurs sont les composants qui gèrent l’intervention de l’utilisateur, travailler avec le modèle et finalement sélectionnent une vue à restituer.</span><span class="sxs-lookup"><span data-stu-id="10446-134">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="10446-135">Dans une application MVC, la vue affiche uniquement les informations ; le contrôleur gère et répond à l’entrée d’utilisateur et d’interaction.</span><span class="sxs-lookup"><span data-stu-id="10446-135">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="10446-136">Dans le modèle MVC, le contrôleur est le point d’entrée initial et est chargé de sélectionner quel modèle pour travailler avec les types et la vue à restituer (par conséquent, son nom - elle contrôle la manière dont l’application répond à une requête donnée).</span><span class="sxs-lookup"><span data-stu-id="10446-136">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="10446-137">Contrôleurs de ne doivent pas être trop complexe par trop de responsabilités.</span><span class="sxs-lookup"><span data-stu-id="10446-137">Controllers should not be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="10446-138">Pour conserver la logique du contrôleur de devenir trop complexe, utilisez la [principe de responsabilité unique](http://deviq.com/single-responsibility-principle/) par émission de données logique d’entreprise dans le modèle de domaine et le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="10446-138">To keep controller logic from becoming overly complex, use the [Single Responsibility Principle](http://deviq.com/single-responsibility-principle/) to push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="10446-139">Si vous trouvez que les actions de contrôleur effectuent fréquemment les mêmes types d’actions, vous pouvez suivre le [vous-même ne répétez principe](http://deviq.com/don-t-repeat-yourself/) en déplaçant ces actions courantes dans [filtres](#filters).</span><span class="sxs-lookup"><span data-stu-id="10446-139">If you find that your controller actions frequently perform the same kinds of actions, you can follow the [Don't Repeat Yourself principle](http://deviq.com/don-t-repeat-yourself/) by moving these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="10446-140">Nouveautés d’ASP.NET MVC de base</span><span class="sxs-lookup"><span data-stu-id="10446-140">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="10446-141">L’infrastructure ASP.NET MVC de base est une léger et open source, framework de présentation simple et facilement testable optimisé pour une utilisation avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="10446-141">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="10446-142">Cœur d’ASP.NET MVC offre un moyen de basée sur des modèles pour créer des sites Web dynamiques qui permet une séparation claire des préoccupations.</span><span class="sxs-lookup"><span data-stu-id="10446-142">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="10446-143">Il vous donne un contrôle total sur le balisage, prend en charge les développement TDD rapide et convivial et utilise les normes web les plus récentes.</span><span class="sxs-lookup"><span data-stu-id="10446-143">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="10446-144">Fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="10446-144">Features</span></span>

<span data-ttu-id="10446-145">Base d’ASP.NET MVC inclut les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="10446-145">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="10446-146">Routage</span><span class="sxs-lookup"><span data-stu-id="10446-146">Routing</span></span>](#routing)
* [<span data-ttu-id="10446-147">Liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="10446-147">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="10446-148">Validation du modèle</span><span class="sxs-lookup"><span data-stu-id="10446-148">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="10446-149">Injection de dépendance</span><span class="sxs-lookup"><span data-stu-id="10446-149">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="10446-150">Filtres</span><span class="sxs-lookup"><span data-stu-id="10446-150">Filters</span></span>](#filters)
* [<span data-ttu-id="10446-151">Zones</span><span class="sxs-lookup"><span data-stu-id="10446-151">Areas</span></span>](#areas)
* [<span data-ttu-id="10446-152">API Web</span><span class="sxs-lookup"><span data-stu-id="10446-152">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="10446-153">Testabilité</span><span class="sxs-lookup"><span data-stu-id="10446-153">Testability</span></span>](#testability)
* [<span data-ttu-id="10446-154">Moteur d’affichage Razor</span><span class="sxs-lookup"><span data-stu-id="10446-154">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="10446-155">Vues fortement typées</span><span class="sxs-lookup"><span data-stu-id="10446-155">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="10446-156">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="10446-156">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="10446-157">Affichage des composants</span><span class="sxs-lookup"><span data-stu-id="10446-157">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="10446-158">Routage</span><span class="sxs-lookup"><span data-stu-id="10446-158">Routing</span></span>

<span data-ttu-id="10446-159">ASP.NET MVC de base est construit sur [ASP.NET Core routage](../fundamentals/routing.md), un composant de mappage d’URL puissant qui vous permet de créer des applications ayant des URL compréhensibles et de recherches.</span><span class="sxs-lookup"><span data-stu-id="10446-159">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="10446-160">Cela vous permet de définir d’affectation de noms des modèles d’URL de votre application qui fonctionnent bien pour l’optimisation des moteurs de recherche (SEO) et pour la génération de lien, sans tenir compte de la façon dont les fichiers sur votre serveur web sont organisés.</span><span class="sxs-lookup"><span data-stu-id="10446-160">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="10446-161">Vous pouvez définir votre itinéraires à l’aide d’une syntaxe de modèle d’itinéraire pratique qui prend en charge les contraintes de valeur d’itinéraire, les valeurs par défaut et les valeurs facultatives.</span><span class="sxs-lookup"><span data-stu-id="10446-161">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="10446-162">*Le routage basé sur une convention* Active globalement définir l’URL qui met en forme votre application accepte et comment chacun de ces formats mappe à une méthode d’action spécifique sur donné contrôleur.</span><span class="sxs-lookup"><span data-stu-id="10446-162">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="10446-163">Lorsqu’une demande entrante est reçue, le moteur de routage analyse l’URL correspond à l’un des formats d’URL définies et appelle ensuite la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="10446-163">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="10446-164">*Attribut de routage* vous permet de spécifier des informations de routage en décorant vos contrôleurs et vos actions avec les attributs qui définissent des itinéraires de votre application.</span><span class="sxs-lookup"><span data-stu-id="10446-164">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="10446-165">Cela signifie que vos définitions de route sont placées en regard de l’action avec lequel ils sont associés et le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="10446-165">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [1, 4]}} -->

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
  [HttpGet("{id}")]
  public IActionResult GetProduct(int id)
  {
    ...
  }
}
```

### <a name="model-binding"></a><span data-ttu-id="10446-166">Liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="10446-166">Model binding</span></span>

<span data-ttu-id="10446-167">ASP.NET Core MVC [liaison de modèle](models/model-binding.md) convertit les données de demande client (valeurs de formulaire, données d’itinéraire, les paramètres de chaîne de requête, les en-têtes HTTP) en objets que le contrôleur peut traiter.</span><span class="sxs-lookup"><span data-stu-id="10446-167">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="10446-168">Par conséquent, votre logique de contrôleur ne doit pas nécessairement faire le travail d’identifier les données de demande entrant ; Il a simplement les données en tant que paramètres aux méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="10446-168">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a><span data-ttu-id="10446-169">validation du modèle</span><span class="sxs-lookup"><span data-stu-id="10446-169">Model validation</span></span>

<span data-ttu-id="10446-170">ASP.NET MVC de base prend en charge [validation](models/validation.md) en décorant votre objet de modèle avec les attributs de validation de données d’annotation.</span><span class="sxs-lookup"><span data-stu-id="10446-170">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="10446-171">Les attributs de validation sont vérifiées sur le côté client avant que les valeurs sont publiées sur le serveur, ainsi que sur le serveur avant l’action du contrôleur est appelée.</span><span class="sxs-lookup"><span data-stu-id="10446-171">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4, 5, 8, 9]}} -->

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

<span data-ttu-id="10446-172">Une action de contrôleur :</span><span class="sxs-lookup"><span data-stu-id="10446-172">A controller action:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [3]}} -->

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // If we got this far, something failed, redisplay form
    return View(model);
}
```

<span data-ttu-id="10446-173">Le framework gère la validation des données de demande à la fois sur le client et sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="10446-173">The framework will handle validating request data both on the client and on the server.</span></span> <span data-ttu-id="10446-174">La logique de validation spécifiée sur les types de modèle est ajoutée aux vues rendus sous forme d’annotations discrètes et est appliquée dans le navigateur avec [jQuery Validation](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="10446-174">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="10446-175">Injection de dépendance</span><span class="sxs-lookup"><span data-stu-id="10446-175">Dependency injection</span></span>

<span data-ttu-id="10446-176">ASP.NET Core a une prise en charge intégrée pour [injection de dépendance (DI)](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="10446-176">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="10446-177">Dans ASP.NET MVC de base, [contrôleurs](controllers/dependency-injection.md) pouvez demande nécessaires services via leurs constructeurs, ce qui leur permet de suivre les [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/).</span><span class="sxs-lookup"><span data-stu-id="10446-177">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](http://deviq.com/explicit-dependencies-principle/).</span></span>

<span data-ttu-id="10446-178">Votre application peut également utiliser [injection de dépendances des fichiers dans la vue](views/dependency-injection.md), à l’aide du `@inject` la directive :</span><span class="sxs-lookup"><span data-stu-id="10446-178">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1]}} -->

```html
@inject SomeService ServiceName
<!DOCTYPE html>
<html>
<head>
  <title>@ServiceName.GetTitle</title>
</head>
<body>
  <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a><span data-ttu-id="10446-179">Filtres</span><span class="sxs-lookup"><span data-stu-id="10446-179">Filters</span></span>

<span data-ttu-id="10446-180">[Filtres](controllers/filters.md) aider les développeurs d’encapsuler des problèmes transversaux, telles que la gestion des exceptions ou d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="10446-180">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="10446-181">Les filtres permettent une logique personnalisée en cours d’exécution et de post-traitement pour les méthodes d’action et peuvent être configurées pour s’exécuter à certains points dans le pipeline de l’exécution pour une demande donnée.</span><span class="sxs-lookup"><span data-stu-id="10446-181">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="10446-182">Les filtres peuvent être appliquées aux contrôleurs ou les actions en tant qu’attributs (ou peuvent être exécutés de manière globale).</span><span class="sxs-lookup"><span data-stu-id="10446-182">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="10446-183">Plusieurs filtres (tels que `Authorize`) sont inclus dans le framework.</span><span class="sxs-lookup"><span data-stu-id="10446-183">Several filters (such as `Authorize`) are included in the framework.</span></span>


```csharp
[Authorize]
   public class AccountController : Controller
   {

```

### <a name="areas"></a><span data-ttu-id="10446-184">Zones</span><span class="sxs-lookup"><span data-stu-id="10446-184">Areas</span></span>

<span data-ttu-id="10446-185">[Zones](controllers/areas.md) fournissent un moyen de partitionner une application Web ASP.NET Core MVC volumineuse en regroupements fonctionnels plus petits.</span><span class="sxs-lookup"><span data-stu-id="10446-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="10446-186">Une zone est en réalité une structure MVC à l’intérieur d’une application.</span><span class="sxs-lookup"><span data-stu-id="10446-186">An area is effectively an MVC structure inside an application.</span></span> <span data-ttu-id="10446-187">Dans un projet MVC, les composants logiques tels que le modèle, le contrôleur et vue sont conservées dans des dossiers différents et MVC utilise les conventions d’affectation de noms pour créer la relation entre ces composants.</span><span class="sxs-lookup"><span data-stu-id="10446-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="10446-188">Pour une application volumineuse, il peut être avantageux de partition de l’application en différents domaines de niveau élevés de fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="10446-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="10446-189">Par exemple, une application de commerce électronique avec plusieurs entités, telles que l’extraction, de facturation et de recherche, etc.. Chacune de ces unités ont leurs propres vues des composants logiques, les contrôleurs et les modèles.</span><span class="sxs-lookup"><span data-stu-id="10446-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="10446-190">API Web</span><span class="sxs-lookup"><span data-stu-id="10446-190">Web APIs</span></span>

<span data-ttu-id="10446-191">En plus de constituer une plate-forme idéale pour la création de sites web, ASP.NET MVC de base est très bien en charge API Web de génération.</span><span class="sxs-lookup"><span data-stu-id="10446-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="10446-192">Vous pouvez créer des services qui peuvent atteindre un large éventail de clients, y compris les navigateurs et périphériques mobiles.</span><span class="sxs-lookup"><span data-stu-id="10446-192">You can build services that can reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="10446-193">Le framework prend en charge pour la négociation de contenu HTTP avec prise en charge intégrée pour [mise en forme des données](models/formatting.md) en tant que JSON ou XML.</span><span class="sxs-lookup"><span data-stu-id="10446-193">The framework includes support for HTTP content-negotiation with built-in support for [formatting data](models/formatting.md) as JSON or XML.</span></span> <span data-ttu-id="10446-194">Écrire [formateurs personnalisés](advanced/custom-formatters.md) pour ajouter la prise en charge de vos propres formats.</span><span class="sxs-lookup"><span data-stu-id="10446-194">Write [custom formatters](advanced/custom-formatters.md) to add support for your own formats.</span></span>

<span data-ttu-id="10446-195">Utilisez la génération de la liaison pour prendre en charge hypermédia.</span><span class="sxs-lookup"><span data-stu-id="10446-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="10446-196">Facilement activer la prise en charge de [ressources cross-origin (CORS) de partage](http://www.w3.org/TR/cors/) afin que vos API Web peuvent être partagés entre plusieurs applications Web.</span><span class="sxs-lookup"><span data-stu-id="10446-196">Easily enable support for [cross-origin resource sharing (CORS)](http://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="10446-197">Testabilité</span><span class="sxs-lookup"><span data-stu-id="10446-197">Testability</span></span>

<span data-ttu-id="10446-198">Utilisation de l’infrastructure des interfaces et injection de dépendances conviennent parfaitement aux tests unitaires et l’infrastructure comprend des fonctionnalités (par exemple, un fournisseur TestHost et en mémoire pour Entity Framework) qui [test d’intégration](../testing/integration-testing.md) rapide simple et également.</span><span class="sxs-lookup"><span data-stu-id="10446-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration testing](../testing/integration-testing.md) quick and easy as well.</span></span> <span data-ttu-id="10446-199">En savoir plus sur [logique du contrôleur de test](controllers/testing.md).</span><span class="sxs-lookup"><span data-stu-id="10446-199">Learn more about [testing controller logic](controllers/testing.md).</span></span>

### <a name="razor-view-engine"></a><span data-ttu-id="10446-200">Moteur d’affichage Razor</span><span class="sxs-lookup"><span data-stu-id="10446-200">Razor view engine</span></span>

<span data-ttu-id="10446-201">[Les vues ASP.NET MVC de base](views/overview.md) utiliser le [moteur d’affichage Razor](views/razor.md) pour restituer des vues.</span><span class="sxs-lookup"><span data-stu-id="10446-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="10446-202">Razor est un langage de balisage de modèle compact, expressif et fluide pour définir des vues à l’aide de code c# incorporé.</span><span class="sxs-lookup"><span data-stu-id="10446-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="10446-203">Razor est utilisé pour générer dynamiquement le contenu web sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="10446-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="10446-204">Vous pouvez correctement mélangez du code de serveur avec le code et le contenu du côté client.</span><span class="sxs-lookup"><span data-stu-id="10446-204">You can cleanly mix server code with client side content and code.</span></span>

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

<span data-ttu-id="10446-205">Le moteur d’affichage Razor que vous pouvez définir à l’aide de [dispositions](views/layout.md), [vues partielles](views/partial.md) et sections remplaçables.</span><span class="sxs-lookup"><span data-stu-id="10446-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="10446-206">Vues fortement typées</span><span class="sxs-lookup"><span data-stu-id="10446-206">Strongly typed views</span></span>

<span data-ttu-id="10446-207">Vues Razor dans MVC peuvent être fortement typées en fonction de votre modèle.</span><span class="sxs-lookup"><span data-stu-id="10446-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="10446-208">Contrôleurs peuvent passer un modèle fortement typé à l’activation de vos vues pour que la vérification du type et IntelliSense prend en charge les vues.</span><span class="sxs-lookup"><span data-stu-id="10446-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="10446-209">Par exemple, la vue suivante définit un modèle de type `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="10446-209">For example, the following view defines a model of type `IEnumerable<Product>`:</span></span>

```html
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="10446-210">Programmes d’assistance de balise</span><span class="sxs-lookup"><span data-stu-id="10446-210">Tag Helpers</span></span>

<span data-ttu-id="10446-211">[Programmes d’assistance de balise](views/tag-helpers/intro.md) permettre au code côté serveur participer à la création et le rendu des éléments HTML dans les fichiers Razor.</span><span class="sxs-lookup"><span data-stu-id="10446-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="10446-212">Vous pouvez utiliser des programmes d’assistance de balise pour définir des balises personnalisées (par exemple, `<environment>`) ou pour modifier le comportement de balises existantes (par exemple, `<label>`).</span><span class="sxs-lookup"><span data-stu-id="10446-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="10446-213">Programmes d’assistance de balise lier à des éléments spécifiques, selon le nom de l’élément et ses attributs.</span><span class="sxs-lookup"><span data-stu-id="10446-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="10446-214">Ils fournissent les avantages de rendu côté serveur tout en conservant un expérience d’édition de HTML.</span><span class="sxs-lookup"><span data-stu-id="10446-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="10446-215">Il existe de nombreuses applications auxiliaires balise intégrés pour les tâches courantes - telles que la création de formulaires, des liens, des ressources de chargement et plus - et encore plus disponibles dans les référentiels GitHub publics et comme NuGet packages.</span><span class="sxs-lookup"><span data-stu-id="10446-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="10446-216">Programmes d’assistance de balise sont créés en c#, et ils conviennent à des éléments HTML en fonction de la balise parente, nom d’attribut ou nom de l’élément.</span><span class="sxs-lookup"><span data-stu-id="10446-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="10446-217">Par exemple, la fonction intégrée LinkTagHelper peut être utilisé pour créer un lien vers le `Login` action de la `AccountsController`:</span><span class="sxs-lookup"><span data-stu-id="10446-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3]}} -->

```html
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="10446-218">Le `EnvironmentTagHelper` peut être utilisé pour inclure des scripts différents dans vos affichages (par exemple, raw ou réduite) en fonction de l’environnement d’exécution, telles que le développement, intermédiaire ou de Production :</span><span class="sxs-lookup"><span data-stu-id="10446-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1, 3, 4, 9]}} -->

```html
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

<span data-ttu-id="10446-219">Programmes d’assistance de balise fournissent une expérience de développement HTML convivial et un environnement IntelliSense riche pour la création d’un balisage HTML et Razor.</span><span class="sxs-lookup"><span data-stu-id="10446-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="10446-220">La plupart des programmes d’assistance de balise intégrée cible des éléments HTML existants et fournit des attributs de côté serveur pour l’élément.</span><span class="sxs-lookup"><span data-stu-id="10446-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="10446-221">Affichage des composants</span><span class="sxs-lookup"><span data-stu-id="10446-221">View Components</span></span>

<span data-ttu-id="10446-222">[Afficher les composants](views/view-components.md) vous permettent d’empaqueter la logique de rendu et la réutiliser dans toute l’application.</span><span class="sxs-lookup"><span data-stu-id="10446-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="10446-223">Elles ressemblent à [vues partielles](views/partial.md), mais avec la logique associée.</span><span class="sxs-lookup"><span data-stu-id="10446-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

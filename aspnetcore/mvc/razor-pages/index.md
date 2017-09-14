---
title: "Présentation des pages Razor dans ASP.NET Core"
author: Rick-Anderson
description: "Ce document offre une vue d’ensemble de l’utilisation des pages Razor dans ASP.NET Core qui permet de faciliter le développement de scénarios orientés page."
keywords: ASP.NET Core, pages Razor
ms.author: riande
manager: wpickett
ms.date: 09/12/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/razor-pages/index
ms.openlocfilehash: e9678279db85ec03616e693b9772c6ee71c4fef8
ms.sourcegitcommit: d2f705f7a8ef2c1a940f590e4de188621fd48d2a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2017
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="b6384-104">Présentation des pages Razor dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6384-104">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="b6384-105">De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="b6384-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="b6384-106">Les pages Razor sont une nouvelle fonctionnalité d’ASP.NET Core MVC qui permet de développer des scénarios orientés page de façon plus simple et plus productive.</span><span class="sxs-lookup"><span data-stu-id="b6384-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="b6384-107">Si vous avez besoin d’un didacticiel qui utilise l’approche Model-View-Controller, consultez [Bien démarrer avec ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="b6384-107">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Getting started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<a name="prerequisites"></a>

## <a name="aspnet-core-20-prerequisites"></a><span data-ttu-id="b6384-108">Prérequis d’ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="b6384-108">ASP.NET Core 2.0 prerequisites</span></span>

<span data-ttu-id="b6384-109">Installez [.NET Core](https://www.microsoft.com/net/core) 2.0.0 ou ultérieur.</span><span class="sxs-lookup"><span data-stu-id="b6384-109">Install [.NET Core](https://www.microsoft.com/net/core) 2.0.0 or later.</span></span>

<span data-ttu-id="b6384-110">Si vous utilisez Visual Studio, installez [Visual Studio](https://www.visualstudio.com/vs/) 2017 version 15.3 ou ultérieure avec les charges de travail suivantes :</span><span class="sxs-lookup"><span data-stu-id="b6384-110">If you're using Visual Studio, install [Visual Studio](https://www.visualstudio.com/vs/) 2017 version 15.3 or later with the following workloads:</span></span>

* <span data-ttu-id="b6384-111">**Développement web et ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="b6384-111">**ASP.NET and web development**</span></span>
* <span data-ttu-id="b6384-112">**Développement multiplateforme .NET Core**</span><span class="sxs-lookup"><span data-stu-id="b6384-112">**.NET Core cross-platform development**</span></span>

<a name="rpvs17"></a>

## <a name="creating-a-razor-pages-project"></a><span data-ttu-id="b6384-113">Création d’un projet de pages Razor</span><span class="sxs-lookup"><span data-stu-id="b6384-113">Creating a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6384-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6384-114">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b6384-115">Pour obtenir des instructions détaillées sur la façon de créer un projet de pages Razor avec Visual Studio, consultez [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="b6384-115">See [Getting started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project using Visual Studio.</span></span>

#   <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b6384-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b6384-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b6384-117">Exécutez `dotnet new razor` à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="b6384-117">Run `dotnet new razor` from the command line.</span></span>

<span data-ttu-id="b6384-118">Ouvrez le fichier *.csproj* généré à partir de Visual Studio pour Mac.</span><span class="sxs-lookup"><span data-stu-id="b6384-118">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b6384-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6384-119">Visual Studio Code</span></span>](#tab/visual-studio-code) 

<span data-ttu-id="b6384-120">Exécutez `dotnet new razor` à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="b6384-120">Run `dotnet new razor` from the command line.</span></span>

#   <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b6384-121">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b6384-121">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="b6384-122">Exécutez `dotnet new razor` à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="b6384-122">Run `dotnet new razor` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="b6384-123">Pages Razor</span><span class="sxs-lookup"><span data-stu-id="b6384-123">Razor Pages</span></span>

<span data-ttu-id="b6384-124">La fonctionnalité Pages Razor est activée dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="b6384-124">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[main](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="b6384-125">Considérez une page de base : <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="b6384-125">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="b6384-126">Le code précédent ressemble beaucoup à un fichier vue Razor.</span><span class="sxs-lookup"><span data-stu-id="b6384-126">The preceding code looks a lot like a Razor view file.</span></span> <span data-ttu-id="b6384-127">Ce qui le rend différent est la directive `@page`.</span><span class="sxs-lookup"><span data-stu-id="b6384-127">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="b6384-128">`@page` fait du fichier une action MVC, ce qui signifie qu’il gère les demandes directement, sans passer par un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b6384-128">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="b6384-129">`@page` doit être la première directive Razor sur une page.</span><span class="sxs-lookup"><span data-stu-id="b6384-129">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="b6384-130">`@page` affecte le comportement d’autres constructions Razor.</span><span class="sxs-lookup"><span data-stu-id="b6384-130">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="b6384-131">Une page similaire, utilisant une classe `PageModel`, est illustrée dans les deux fichiers suivants.</span><span class="sxs-lookup"><span data-stu-id="b6384-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="b6384-132">Le fichier *Pages/Index2.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b6384-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="b6384-133">Le fichier « code-behind » *Pages/Index2.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="b6384-133">The *Pages/Index2.cshtml.cs* "code-behind" file:</span></span>

[!code-cs[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="b6384-134">Par convention, le fichier de classe `PageModel` a le même nom que le fichier Page Razor, avec *.cs* en plus.</span><span class="sxs-lookup"><span data-stu-id="b6384-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="b6384-135">Par exemple, la page Razor précédente est *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6384-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="b6384-136">Le fichier contenant la classe `PageModel` se nomme *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="b6384-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="b6384-137">Les associations des chemins d’URL aux pages sont déterminées par l’emplacement de la page dans le système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="b6384-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="b6384-138">Le tableau suivant montre un chemin Page Razor et l’URL correspondante :</span><span class="sxs-lookup"><span data-stu-id="b6384-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="b6384-139">Nom et chemin de fichier</span><span class="sxs-lookup"><span data-stu-id="b6384-139">File name and path</span></span>               | <span data-ttu-id="b6384-140">URL correspondante</span><span class="sxs-lookup"><span data-stu-id="b6384-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="b6384-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b6384-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="b6384-142">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="b6384-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="b6384-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b6384-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="b6384-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b6384-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="b6384-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b6384-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="b6384-146">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="b6384-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="b6384-147">Remarques :</span><span class="sxs-lookup"><span data-stu-id="b6384-147">Notes:</span></span>

* <span data-ttu-id="b6384-148">Le runtime recherche les fichiers Pages Razor dans le dossier *Pages* par défaut.</span><span class="sxs-lookup"><span data-stu-id="b6384-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="b6384-149">`Index` est la page par défaut quand une URL n’inclut pas de page.</span><span class="sxs-lookup"><span data-stu-id="b6384-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="writing-a-basic-form"></a><span data-ttu-id="b6384-150">Écriture d’un formulaire de base</span><span class="sxs-lookup"><span data-stu-id="b6384-150">Writing a basic form</span></span>

<span data-ttu-id="b6384-151">Les fonctionnalités des pages Razor sont conçues pour simplifier les modèles courants utilisés avec les navigateurs web.</span><span class="sxs-lookup"><span data-stu-id="b6384-151">Razor Pages features are designed to make common patterns used with web browsers easy.</span></span> <span data-ttu-id="b6384-152">La [liaison de modèle](xref:mvc/models/model-binding), les [Tag Helpers](xref:mvc/views/tag-helpers/intro)et les assistances HTML *fonctionnent tous* avec les propriétés définies dans une classe Page Razor.</span><span class="sxs-lookup"><span data-stu-id="b6384-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="b6384-153">Considérez une page qui implémente un formulaire « Nous contacter » de base pour le modèle `Contact` :</span><span class="sxs-lookup"><span data-stu-id="b6384-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="b6384-154">Pour les exemples de ce document, le `DbContext` est initialisé dans le fichier [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="b6384-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="b6384-155">Le modèle de données :</span><span class="sxs-lookup"><span data-stu-id="b6384-155">The data model:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="b6384-156">Le fichier vue *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b6384-156">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="b6384-157">Le fichier code-behind *Pages/Create.cshtml.cs* de la vue :</span><span class="sxs-lookup"><span data-stu-id="b6384-157">The *Pages/Create.cshtml.cs* code-behind file for the view:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="b6384-158">Par convention, la classe `PageModel` se nomme `<PageName>Model` et se trouve dans le même espace de noms que la page.</span><span class="sxs-lookup"><span data-stu-id="b6384-158">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="b6384-159">L’utilisation d’un fichier code-behind `PageModel` prend en charge les tests unitaires, mais vous oblige à écrire un constructeur et une classe explicites.</span><span class="sxs-lookup"><span data-stu-id="b6384-159">Using a `PageModel` code-behind file supports unit testing, but requires you to write an explicit constructor and class.</span></span> <span data-ttu-id="b6384-160">Les pages sans fichiers code-behind `PageModel` prennent en charge la compilation au moment de l’exécution, ce qui peut être un avantage lors du développement.</span><span class="sxs-lookup"><span data-stu-id="b6384-160">Pages without `PageModel` code-behind files support runtime compilation, which can be an advantage in development.</span></span>  <!-- review: advantage because you can make changes and refresh the browser without explicitly compiling the app -->

<span data-ttu-id="b6384-161">La page a une *méthode de gestionnaire* `OnPostAsync`, qui s’exécute sur les requêtes `POST` (quand un utilisateur poste le formulaire).</span><span class="sxs-lookup"><span data-stu-id="b6384-161">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="b6384-162">Vous pouvez ajouter des méthodes de gestionnaire pour n’importe quel verbe HTTP.</span><span class="sxs-lookup"><span data-stu-id="b6384-162">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="b6384-163">Les gestionnaires les plus courants sont :</span><span class="sxs-lookup"><span data-stu-id="b6384-163">The most common handlers are:</span></span>

* <span data-ttu-id="b6384-164">`OnGet` pour initialiser l’état nécessaire pour la page.</span><span class="sxs-lookup"><span data-stu-id="b6384-164">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="b6384-165">Exemple [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="b6384-165">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="b6384-166">`OnPost` pour gérer les envois de formulaire.</span><span class="sxs-lookup"><span data-stu-id="b6384-166">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="b6384-167">Le suffixe de nommage `Async` est facultatif, mais souvent utilisé par convention pour les fonctions asynchrones.</span><span class="sxs-lookup"><span data-stu-id="b6384-167">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="b6384-168">Le code `OnPostAsync` dans l’exemple précédent est similaire à celui que vous écririez normalement dans un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b6384-168">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="b6384-169">Le code précédent est typique des pages Razor.</span><span class="sxs-lookup"><span data-stu-id="b6384-169">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="b6384-170">La plupart des primitives MVC comme la [liaison de modèle](xref:mvc/models/model-binding), la [validation](xref:mvc/models/validation) et les résultats d’action sont partagées.</span><span class="sxs-lookup"><span data-stu-id="b6384-170">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="b6384-171">La méthode `OnPostAsync` précédente :</span><span class="sxs-lookup"><span data-stu-id="b6384-171">The previous `OnPostAsync` method:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="b6384-172">Le flux de base de `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="b6384-172">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="b6384-173">Recherchez les erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="b6384-173">Check for validation errors.</span></span>

*  <span data-ttu-id="b6384-174">S’il n’y a aucune erreur, enregistrez les données et redirigez.</span><span class="sxs-lookup"><span data-stu-id="b6384-174">If there are no errors, save the data and redirect.</span></span>
*  <span data-ttu-id="b6384-175">S’il y a des erreurs, réaffichez la page avec des messages de validation.</span><span class="sxs-lookup"><span data-stu-id="b6384-175">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="b6384-176">La validation côté client est identique à celle des applications ASP.NET Core MVC traditionnelles.</span><span class="sxs-lookup"><span data-stu-id="b6384-176">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="b6384-177">Dans de nombreux cas, les erreurs de validation sont détectées sur le client et jamais envoyées au serveur.</span><span class="sxs-lookup"><span data-stu-id="b6384-177">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="b6384-178">Quand les données sont entrées correctement, la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `RedirectToPage` pour retourner une instance de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="b6384-178">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="b6384-179">`RedirectToPage` est un nouveau résultat d’action, semblable à `RedirectToAction` ou `RedirectToRoute`, mais personnalisé pour les pages.</span><span class="sxs-lookup"><span data-stu-id="b6384-179">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="b6384-180">Dans l’exemple précédent, il redirige vers la page Index racine (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="b6384-180">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="b6384-181">`RedirectToPage` est détaillé dans la section [Génération d’URL pour les pages](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="b6384-181">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="b6384-182">Quand le formulaire envoyé comporte des erreurs de validation (qui sont passées au serveur), la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `Page`.</span><span class="sxs-lookup"><span data-stu-id="b6384-182">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="b6384-183">`Page` retourne une instance de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="b6384-183">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="b6384-184">Le retour de `Page` est similaire à la façon dont les actions dans les contrôleurs retournent `View`.</span><span class="sxs-lookup"><span data-stu-id="b6384-184">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="b6384-185">`PageResult` est le type de retour <!-- Review  --> par défaut pour une méthode de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="b6384-185">`PageResult` is the default <!-- Review  --> return type for a handler method.</span></span> <span data-ttu-id="b6384-186">Une méthode de gestionnaire qui retourne `void` restitue la page.</span><span class="sxs-lookup"><span data-stu-id="b6384-186">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="b6384-187">La propriété `Customer` utilise l’attribut `[BindProperty]` pour accepter la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="b6384-187">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="b6384-188">Par défaut, les pages Razor lient les propriétés uniquement avec les verbes non-GET.</span><span class="sxs-lookup"><span data-stu-id="b6384-188">Razor Pages, by default, bind properties only with non-GET verbs.</span></span> <span data-ttu-id="b6384-189">La liaison aux propriétés peut réduire la quantité de code à écrire.</span><span class="sxs-lookup"><span data-stu-id="b6384-189">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="b6384-190">Elle réduit la quantité de code en utilisant la même propriété pour afficher les champs de formulaire (`<input asp-for="Customer.Name" />`) et accepter l’entrée.</span><span class="sxs-lookup"><span data-stu-id="b6384-190">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name" />`) and accept the input.</span></span>

<span data-ttu-id="b6384-191">La page d’accueil (*Index.cshtml*) :</span><span class="sxs-lookup"><span data-stu-id="b6384-191">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="b6384-192">Le fichier code-behind *Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="b6384-192">The code behind *Index.cshtml.cs* file:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="b6384-193">Le fichier *Index.cshtml* contient le balisage suivant pour créer un lien d’édition pour chaque contact :</span><span class="sxs-lookup"><span data-stu-id="b6384-193">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

```cshtml
<a asp-page="./Edit" asp-route-id="@contact.Id">edit</a>
```

<span data-ttu-id="b6384-194">Le [tag helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper) a utilisé l’attribut [asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper#route) pour générer un lien vers la page Edit.</span><span class="sxs-lookup"><span data-stu-id="b6384-194">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper) used the [asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper#route) attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="b6384-195">Le lien contient des données d’itinéraire avec l’ID de contact.</span><span class="sxs-lookup"><span data-stu-id="b6384-195">The link contains route data with the contact ID.</span></span> <span data-ttu-id="b6384-196">Par exemple, `http://localhost:5000/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="b6384-196">For example, `http://localhost:5000/Edit/1`.</span></span>

<span data-ttu-id="b6384-197">Le fichier *Pages/Edit.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b6384-197">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="b6384-198">La première ligne contient la directive `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="b6384-198">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="b6384-199">La contrainte de routage `"{id:int}"` indique à la page qu’elle doit accepter les requêtes qui contiennent des données d’itinéraire `int`.</span><span class="sxs-lookup"><span data-stu-id="b6384-199">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="b6384-200">Si une requête à la page ne contient de données d’itinéraire qui peuvent être converties en `int`, le runtime retourne une erreur HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="b6384-200">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span>

<span data-ttu-id="b6384-201">Le fichier *Pages/Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="b6384-201">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="b6384-202">XSRF/CSRF et pages Razor</span><span class="sxs-lookup"><span data-stu-id="b6384-202">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="b6384-203">Vous n’avez aucun code à écrire pour la [validation anti-contrefaçon](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="b6384-203">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="b6384-204">La validation et la génération de jetons anti-contrefaçon sont automatiquement incluses dans les pages Razor.</span><span class="sxs-lookup"><span data-stu-id="b6384-204">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>
## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="b6384-205">Utilisation de dispositions, partiels, modèles et Tag Helpers avec les pages Razor</span><span class="sxs-lookup"><span data-stu-id="b6384-205">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="b6384-206">Les pages Razor fonctionnent avec toutes les fonctionnalités du moteur de vue Razor.</span><span class="sxs-lookup"><span data-stu-id="b6384-206">Pages work with all the features of the Razor view engine.</span></span> <span data-ttu-id="b6384-207">Les dispositions, partiels, modèles, Tag Helpers, *_ViewStart.cshtml* et *_ViewImports.cshtml* fonctionnent de la même façon que pour les vues Razor classiques.</span><span class="sxs-lookup"><span data-stu-id="b6384-207">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="b6384-208">Nous allons nettoyer un peu cette page en tirant parti de certaines de ces fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="b6384-208">Let's declutter this page by taking advantage of some of those features.</span></span>

<span data-ttu-id="b6384-209">Ajoutez une [page de disposition](xref:mvc/views/layout) à *Pages/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b6384-209">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="b6384-210">La [disposition](xref:mvc/views/layout) :</span><span class="sxs-lookup"><span data-stu-id="b6384-210">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="b6384-211">Contrôle la disposition de chaque page (à moins que la page ne refuse la disposition).</span><span class="sxs-lookup"><span data-stu-id="b6384-211">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="b6384-212">Importe des structures HTML telles que JavaScript et les feuilles de style.</span><span class="sxs-lookup"><span data-stu-id="b6384-212">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="b6384-213">Pour plus d’informations, consultez [Page de disposition](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="b6384-213">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="b6384-214">La propriété [Layout](xref:mvc/views/layout#specifying-a-layout) est définie dans *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b6384-214">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="b6384-215">**Remarque :** La disposition est dans le dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="b6384-215">**Note:** The layout is in the *Pages* folder.</span></span> <span data-ttu-id="b6384-216">Les pages recherchent d’autres vues (dispositions, modèles, partiels) hiérarchiquement, en commençant dans le même dossier que la page active.</span><span class="sxs-lookup"><span data-stu-id="b6384-216">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="b6384-217">Une disposition dans le dossier *Pages* peut être utilisée à partir de n’importe quelle page Razor sous le dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="b6384-217">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="b6384-218">Nous vous recommandons de **ne pas** placer le fichier de disposition dans le dossier *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="b6384-218">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="b6384-219">*Views/Shared* est un modèle de vues MVC.</span><span class="sxs-lookup"><span data-stu-id="b6384-219">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="b6384-220">Les pages Razor sont censées s’appuyer sur la hiérarchie des dossiers, pas sur les conventions de chemins.</span><span class="sxs-lookup"><span data-stu-id="b6384-220">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="b6384-221">La recherche de vue à partir d’une page Razor inclut le dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="b6384-221">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="b6384-222">Les dispositions, modèles et partiels que vous utilisez avec les contrôleurs MVC et les vues Razor conventionnelles *fonctionnent simplement*.</span><span class="sxs-lookup"><span data-stu-id="b6384-222">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="b6384-223">Ajoutez un fichier *Pages/_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b6384-223">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="b6384-224">`@namespace` est expliqué plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="b6384-224">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="b6384-225">La directive `@addTagHelper` permet de bénéficier des [Tag Helpers intégrés](xref:mvc/views/tag-helpers/builtin-th/Index) dans toutes les pages du dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="b6384-225">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="b6384-226">Quand la directive `@namespace` est utilisée explicitement sur une page :</span><span class="sxs-lookup"><span data-stu-id="b6384-226">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="b6384-227">La directive définit l’espace de noms pour la page.</span><span class="sxs-lookup"><span data-stu-id="b6384-227">The directive sets the namespace for the page.</span></span> <span data-ttu-id="b6384-228">La directive `@model` n’a pas besoin d’inclure l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="b6384-228">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="b6384-229">Quand la directive `@namespace` est contenue dans *_ViewImports.cshtml*, l’espace de noms spécifié fournit le préfixe de l’espace de noms généré dans la Page qui importe la directive `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="b6384-229">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="b6384-230">Le reste de l’espace de noms généré (la partie suffixe) est le chemin relatif séparé par un point entre le dossier contenant *_ViewImports.cshtml* et le dossier contenant la page.</span><span class="sxs-lookup"><span data-stu-id="b6384-230">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="b6384-231">Par exemple, le fichier code-behind *Pages/Customers/Edit.cshtml.cs* définit explicitement l’espace de noms :</span><span class="sxs-lookup"><span data-stu-id="b6384-231">For example, the code behind file *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="b6384-232">Le fichier *Pages/_ViewImports.cshtml* définit l’espace de noms suivant :</span><span class="sxs-lookup"><span data-stu-id="b6384-232">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="b6384-233">L’espace de noms généré pour la page Razor *Pages/Customers/Edit.cshtml* est identique au fichier code-behind.</span><span class="sxs-lookup"><span data-stu-id="b6384-233">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the code behind file.</span></span> <span data-ttu-id="b6384-234">La directive `@namespace` a été conçue pour que les classes C# ajoutées à un projet et au code généré par les pages *fonctionnent simplement* sans avoir à ajouter de directive `@using` pour le fichier code-behind.</span><span class="sxs-lookup"><span data-stu-id="b6384-234">The `@namespace` directive was designed so the C# classes added to a project and pages-generated code *just work* without having to add an `@using` directive for the code behind file.</span></span>

<span data-ttu-id="b6384-235">**Remarque :** `@namespace` fonctionne également avec les vues Razor classiques.</span><span class="sxs-lookup"><span data-stu-id="b6384-235">**Note:** `@namespace` also works with conventional Razor views.</span></span>

<span data-ttu-id="b6384-236">Le fichier de vue *Pages/Create.cshtml* d’origine :</span><span class="sxs-lookup"><span data-stu-id="b6384-236">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="b6384-237">Le fichier vue *Pages/Create.cshtml* mis à jour :</span><span class="sxs-lookup"><span data-stu-id="b6384-237">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="b6384-238">Le [projet de démarrage de pages Razor](#rpvs17) contient *Pages/_ValidationScriptsPartial.cshtml*, qui connecte la validation côté client.</span><span class="sxs-lookup"><span data-stu-id="b6384-238">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="b6384-239">Génération d’URL pour les pages</span><span class="sxs-lookup"><span data-stu-id="b6384-239">URL generation for Pages</span></span>

<span data-ttu-id="b6384-240">La page `Create`, illustrée précédemment, utilise `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="b6384-240">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="b6384-241">L’application a la structure de fichiers/dossiers suivante :</span><span class="sxs-lookup"><span data-stu-id="b6384-241">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="b6384-242">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="b6384-242">*/Pages*</span></span>

  * <span data-ttu-id="b6384-243">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b6384-243">*Index.cshtml*</span></span>
  * <span data-ttu-id="b6384-244">*/Customer*</span><span class="sxs-lookup"><span data-stu-id="b6384-244">*/Customer*</span></span>

    * <span data-ttu-id="b6384-245">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b6384-245">*Create.cshtml*</span></span>
    * <span data-ttu-id="b6384-246">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b6384-246">*Edit.cshtml*</span></span>
    * <span data-ttu-id="b6384-247">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b6384-247">*Index.cshtml*</span></span>

<span data-ttu-id="b6384-248">Une fois l’opération réussie, les pages *Pages/Customers/Create.cshtml* et *Pages/Customers/Edit.cshtml* redirigent vers *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6384-248">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="b6384-249">La chaîne `/Index` fait partie de l’URI pour accéder à la page précédente.</span><span class="sxs-lookup"><span data-stu-id="b6384-249">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="b6384-250">La chaîne `/Index` peut être utilisée pour générer l’URI de la page *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6384-250">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="b6384-251">Exemple :</span><span class="sxs-lookup"><span data-stu-id="b6384-251">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="b6384-252">Le nom de la page est le chemin de la page à partir du dossier racine */Pages* (y compris un `/` de début, par exemple `/Index`).</span><span class="sxs-lookup"><span data-stu-id="b6384-252">The page name is the path to the page from the root */Pages* folder (including a leading `/`, for example `/Index`).</span></span> <span data-ttu-id="b6384-253">Les exemples de génération d’URL précédents sont beaucoup plus riches en fonctionnalités que le simple codage en dur d’une URL.</span><span class="sxs-lookup"><span data-stu-id="b6384-253">The preceding URL generation samples are much more feature rich than just hardcoding a URL.</span></span> <span data-ttu-id="b6384-254">La génération d’URL utilise le [routage](xref:mvc/controllers/routing) et peut générer et encoder des paramètres en fonction de la façon dont l’itinéraire est défini dans le chemin de destination.</span><span class="sxs-lookup"><span data-stu-id="b6384-254">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="b6384-255">La génération d’URL pour les pages prend en charge les noms relatifs.</span><span class="sxs-lookup"><span data-stu-id="b6384-255">URL generation for pages supports relative names.</span></span> <span data-ttu-id="b6384-256">Le tableau suivant montre la page Index sélectionnée avec différents paramètres `RedirectToPage` à partir de *Pages/Customers/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b6384-256">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="b6384-257">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="b6384-257">RedirectToPage(x)</span></span>| <span data-ttu-id="b6384-258">Page</span><span class="sxs-lookup"><span data-stu-id="b6384-258">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="b6384-259">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="b6384-259">RedirectToPage("/Index")</span></span> | <span data-ttu-id="b6384-260">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="b6384-260">*Pages/Index*</span></span> |
| <span data-ttu-id="b6384-261">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="b6384-261">RedirectToPage("./Index");</span></span> | <span data-ttu-id="b6384-262">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="b6384-262">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="b6384-263">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="b6384-263">RedirectToPage("../Index")</span></span> | <span data-ttu-id="b6384-264">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="b6384-264">*Pages/Index*</span></span> |
| <span data-ttu-id="b6384-265">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="b6384-265">RedirectToPage("Index")</span></span>  | <span data-ttu-id="b6384-266">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="b6384-266">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="b6384-267">`RedirectToPage("Index")`, `RedirectToPage("./Index")` et `RedirectToPage("../Index")` sont des *noms relatifs*.</span><span class="sxs-lookup"><span data-stu-id="b6384-267">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="b6384-268">Le paramètre `RedirectToPage` est *combiné* avec le chemin de la page active pour calculer le nom de la page de destination.</span><span class="sxs-lookup"><span data-stu-id="b6384-268">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page. -- page name, not page path -->

<span data-ttu-id="b6384-269">La liaison de nom relatif est utile lors de la création de sites avec une structure complexe.</span><span class="sxs-lookup"><span data-stu-id="b6384-269">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="b6384-270">Si vous utilisez des noms relatifs pour établir une liaison entre les pages d’un dossier, vous pouvez renommer ce dossier.</span><span class="sxs-lookup"><span data-stu-id="b6384-270">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="b6384-271">Tous les liens fonctionneront encore (car ils n’incluent pas le nom du dossier).</span><span class="sxs-lookup"><span data-stu-id="b6384-271">All the links still work (because they didn't include the folder name).</span></span>

## <a name="tempdata"></a><span data-ttu-id="b6384-272">TempData</span><span class="sxs-lookup"><span data-stu-id="b6384-272">TempData</span></span>

<span data-ttu-id="b6384-273">ASP.NET Core expose la propriété [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) sur un [contrôleur](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="b6384-273">ASP.NET Core exposes the [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="b6384-274">Cette propriété stocke les données jusqu’à ce qu’elles soient lues.</span><span class="sxs-lookup"><span data-stu-id="b6384-274">This property stores data until it is read.</span></span> <span data-ttu-id="b6384-275">Vous pouvez utiliser les méthodes `Keep` et `Peek` pour examiner les données sans suppression.</span><span class="sxs-lookup"><span data-stu-id="b6384-275">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="b6384-276">`TempData` est utile pour la redirection, quand des données sont nécessaires pour plusieurs requêtes.</span><span class="sxs-lookup"><span data-stu-id="b6384-276">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="b6384-277">L’attribut `[TempData]` est une nouveauté dans ASP.NET Core 2.0. Il est pris en charge sur les contrôleurs et les pages.</span><span class="sxs-lookup"><span data-stu-id="b6384-277">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="b6384-278">Le code suivant définit la valeur de `Message` à l’aide de `TempData` :</span><span class="sxs-lookup"><span data-stu-id="b6384-278">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="b6384-279">Le balisage suivant dans le fichier *Pages/Customers/Index.cshtml* affiche la valeur de `Message` à l’aide de `TempData`.</span><span class="sxs-lookup"><span data-stu-id="b6384-279">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="b6384-280">Le fichier code-behind *Pages/Customers/Index.cshtml.cs* applique l’attribut `[TempData]` à la propriété `Message`.</span><span class="sxs-lookup"><span data-stu-id="b6384-280">The *Pages/Customers/Index.cshtml.cs* code-behind file applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="b6384-281">Pour plus d’informations, consultez [TempData](xref:fundamentals/app-state#temp).</span><span class="sxs-lookup"><span data-stu-id="b6384-281">See [TempData](xref:fundamentals/app-state#temp) for more information.</span></span>

<a name="mhpp"></a>
## <a name="multiple-handlers-per-page"></a><span data-ttu-id="b6384-282">Plusieurs gestionnaires par page</span><span class="sxs-lookup"><span data-stu-id="b6384-282">Multiple handlers per page</span></span>

<span data-ttu-id="b6384-283">La page suivante génère un balisage pour deux gestionnaires de page à l’aide du Tag Helper `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="b6384-283">The following page generates markup for two page handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there is no `asp-` attribute   -->

<span data-ttu-id="b6384-284">Le formulaire dans l’exemple précédent contient deux boutons d’envoi, chacun utilisant `FormActionTagHelper` pour envoyer à une URL différente.</span><span class="sxs-lookup"><span data-stu-id="b6384-284">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="b6384-285">L’attribut `asp-page-handler` est un complément de `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="b6384-285">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="b6384-286">`asp-page-handler` génère des URL qui envoient à chacune des méthodes de gestionnaire définies par une page.</span><span class="sxs-lookup"><span data-stu-id="b6384-286">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="b6384-287">`asp-page` n’est pas spécifié, car l’exemple établit une liaison à la page active.</span><span class="sxs-lookup"><span data-stu-id="b6384-287">`asp-page` is not specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="b6384-288">Le fichier code-behind :</span><span class="sxs-lookup"><span data-stu-id="b6384-288">The code-behind file:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="b6384-289">Le code précédent utilise des *méthodes de gestionnaire nommées*.</span><span class="sxs-lookup"><span data-stu-id="b6384-289">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="b6384-290">Pour créer des méthodes de gestionnaire nommées, il faut prendre le texte dans le nom après `On<HTTP Verb>` et avant `Async` (le cas échéant).</span><span class="sxs-lookup"><span data-stu-id="b6384-290">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="b6384-291">Dans l’exemple précédent, les méthodes de page sont OnPost**JoinList**Async et OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="b6384-291">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="b6384-292">Avec *OnPost* et *Async* supprimés, les noms des gestionnaires sont `JoinList` et `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="b6384-292">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="b6384-293">Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="b6384-293">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="b6384-294">Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="b6384-294">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="customizing-routing"></a><span data-ttu-id="b6384-295">Personnalisation du routage</span><span class="sxs-lookup"><span data-stu-id="b6384-295">Customizing Routing</span></span>

<span data-ttu-id="b6384-296">Si vous ne voulez pas avoir la chaîne de requête `?handler=JoinList` dans l’URL, vous pouvez changer l’itinéraire pour placer le nom du gestionnaire dans la partie chemin de l’URL.</span><span class="sxs-lookup"><span data-stu-id="b6384-296">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="b6384-297">Vous pouvez personnaliser l’itinéraire en ajoutant un modèle d’itinéraire placé entre des guillemets doubles après la directive `@page`.</span><span class="sxs-lookup"><span data-stu-id="b6384-297">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="b6384-298">L’itinéraire précédent place le nom du gestionnaire dans le chemin d’URL au lieu de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="b6384-298">The preceding route puts the handler name in the URL path instead of the query string.</span></span> <span data-ttu-id="b6384-299">Le `?` suivant `handler` signifie que le paramètre d’itinéraire est facultatif.</span><span class="sxs-lookup"><span data-stu-id="b6384-299">The `?` following `handler` means the route parameter is optional.</span></span>

<span data-ttu-id="b6384-300">Vous pouvez utiliser `@page` pour ajouter des segments et des paramètres supplémentaires à l’itinéraire d’une page.</span><span class="sxs-lookup"><span data-stu-id="b6384-300">You can use `@page` to add additional segments and parameters to a page's route.</span></span> <span data-ttu-id="b6384-301">Tout ce qui y figure est **ajouté** à l’itinéraire par défaut de la page.</span><span class="sxs-lookup"><span data-stu-id="b6384-301">Whatever's there is **appended** to the default route of the page.</span></span> <span data-ttu-id="b6384-302">L’utilisation d’un chemin absolu ou virtuel pour changer l’itinéraire de la page (comme `"~/Some/Other/Path"`) n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="b6384-302">Using an absolute or virtual path to change the page's route (like `"~/Some/Other/Path"`) is not supported.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="b6384-303">Configuration et paramètres</span><span class="sxs-lookup"><span data-stu-id="b6384-303">Configuration and settings</span></span>

<span data-ttu-id="b6384-304">Pour configurer les options avancées, utilisez la méthode d’extension `AddRazorPagesOptions` sur le générateur MVC :</span><span class="sxs-lookup"><span data-stu-id="b6384-304">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="b6384-305">Actuellement, vous pouvez utiliser `RazorPagesOptions` pour définir le répertoire racine pour les pages, ou ajouter des conventions de modèle d’application pour les pages.</span><span class="sxs-lookup"><span data-stu-id="b6384-305">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="b6384-306">Nous espérons à l’avenir favoriser une extensibilité supplémentaire de cette manière.</span><span class="sxs-lookup"><span data-stu-id="b6384-306">We hope to enable more extensibility this way in the future.</span></span>

<span data-ttu-id="b6384-307">Pour précompiler des vues, consultez [Compilation de vue Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="b6384-307">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="b6384-308">[Téléchargez ou affichez des exemples de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="b6384-308">[Download or view sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).</span></span>

<span data-ttu-id="b6384-309">Consultez [Bien démarrer avec les pages Razor dans ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start), qui s’appuie sur cette introduction.</span><span class="sxs-lookup"><span data-stu-id="b6384-309">See [Getting started with Razor Pages in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
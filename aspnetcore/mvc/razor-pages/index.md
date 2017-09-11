---
title: "Présentation des pages Razor dans ASP.NET Core"
author: Rick-Anderson
description: "Vue d’ensemble des pages Razor dans ASP.NET Core"
keywords: ASP.NET Core, pages Razor
ms.author: riande
manager: wpickett
ms.date: 08/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/razor-pages/index
ms.openlocfilehash: 9301b99aed8fcb3bef91abf0fb269c4052cdb7e2
ms.sourcegitcommit: 87900dffec8ad84a0f74357b23343e215f354dcb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2017
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="7c12b-104">Présentation des pages Razor dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c12b-104">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="7c12b-105">De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="7c12b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="7c12b-106">Les pages Razor sont une nouvelle fonctionnalité d’ASP.NET Core MVC qui permet de développer des scénarios orientés page de façon plus simple et plus productive.</span><span class="sxs-lookup"><span data-stu-id="7c12b-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="7c12b-107">Si vous avez besoin d’un didacticiel qui utilise l’approche Model-View-Controller, consultez [Bien démarrer avec ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="7c12b-107">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Getting started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<a name="prerequisites"></a>

## <a name="aspnet-core-20-prerequisites"></a><span data-ttu-id="7c12b-108">Prérequis d’ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="7c12b-108">ASP.NET Core 2.0 prerequisites</span></span>

<span data-ttu-id="7c12b-109">Installez [.NET Core](https://dot.net/core) 2.0.0 ou ultérieur.</span><span class="sxs-lookup"><span data-stu-id="7c12b-109">Install [.NET Core](https://dot.net/core) 2.0.0 or later.</span></span>

<span data-ttu-id="7c12b-110">Si vous utilisez Visual Studio, installez [Visual Studio](https://www.visualstudio.com/vs/) 15.3 ou ultérieur avec les charges de travail suivantes :</span><span class="sxs-lookup"><span data-stu-id="7c12b-110">If you're using Visual Studio, install [Visual Studio](https://www.visualstudio.com/vs/) 15.3 or later with the following workloads:</span></span>

* <span data-ttu-id="7c12b-111">**Développement web et ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="7c12b-111">**ASP.NET and web development**</span></span>
* <span data-ttu-id="7c12b-112">**Développement multiplateforme .NET Core**</span><span class="sxs-lookup"><span data-stu-id="7c12b-112">**.NET Core cross-platform development**</span></span>

<a name="rpvs17"></a>

## <a name="creating-a-razor-pages-project"></a><span data-ttu-id="7c12b-113">Création d’un projet de pages Razor</span><span class="sxs-lookup"><span data-stu-id="7c12b-113">Creating a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c12b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c12b-114">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="7c12b-115">Pour obtenir des instructions détaillées sur la façon de créer un projet de pages Razor avec Visual Studio, consultez [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="7c12b-115">See [Getting started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project using Visual Studio.</span></span>

#   <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c12b-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="7c12b-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7c12b-117">Exécutez `dotnet new razor` à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="7c12b-117">Run `dotnet new razor` from the command line.</span></span>

<span data-ttu-id="7c12b-118">Ouvrez le fichier *.csproj* généré à partir de Visual Studio pour Mac.</span><span class="sxs-lookup"><span data-stu-id="7c12b-118">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c12b-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c12b-119">Visual Studio Code</span></span>](#tab/visual-studio-code) 

<span data-ttu-id="7c12b-120">Exécutez `dotnet new razor` à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="7c12b-120">Run `dotnet new razor` from the command line.</span></span>

#   <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7c12b-121">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="7c12b-121">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="7c12b-122">Exécutez `dotnet new razor` à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="7c12b-122">Run `dotnet new razor` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="7c12b-123">Pages Razor</span><span class="sxs-lookup"><span data-stu-id="7c12b-123">Razor Pages</span></span>

<span data-ttu-id="7c12b-124">La fonctionnalité Pages Razor est activée dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-124">Razor Pages is enabled in *Startup.cs*:</span></span>

<span data-ttu-id="7c12b-125">[!code-cs[main](index/sample/RazorPagesIntro/Startup.cs?name=Startup)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-125">[!code-cs[main](index/sample/RazorPagesIntro/Startup.cs?name=Startup)]</span></span>

<span data-ttu-id="7c12b-126">Considérez une page de base : <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="7c12b-126">Consider a basic page: <a name="OnGet"></a></span></span>

<span data-ttu-id="7c12b-127">[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-127">[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index.cshtml)]</span></span>

<span data-ttu-id="7c12b-128">Le code précédent ressemble beaucoup à un fichier vue Razor.</span><span class="sxs-lookup"><span data-stu-id="7c12b-128">The preceding code looks a lot like a Razor view file.</span></span> <span data-ttu-id="7c12b-129">Ce qui le rend différent est la directive `@page`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-129">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="7c12b-130">`@page` fait du fichier une action MVC, ce qui signifie qu’il gère les demandes directement, sans passer par un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7c12b-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="7c12b-131">`@page` doit être la première directive Razor sur une page.</span><span class="sxs-lookup"><span data-stu-id="7c12b-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="7c12b-132">`@page` affecte le comportement d’autres constructions Razor.</span><span class="sxs-lookup"><span data-stu-id="7c12b-132">`@page` affects the behavior of other Razor constructs.</span></span> <span data-ttu-id="7c12b-133">La directive [@functions](xref:mvc/views/razor#functions) active le contenu au niveau de la fonction.</span><span class="sxs-lookup"><span data-stu-id="7c12b-133">The [@functions](xref:mvc/views/razor#functions) directive enables function-level content.</span></span>

<span data-ttu-id="7c12b-134">Une page similaire, avec le `PageModel` dans un fichier distinct, est illustré dans les deux fichiers suivants.</span><span class="sxs-lookup"><span data-stu-id="7c12b-134">A similar page, with the `PageModel` in a separate file, is shown in the following two files.</span></span> <span data-ttu-id="7c12b-135">Le fichier *Pages/Index2.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-135">The *Pages/Index2.cshtml* file:</span></span>

<span data-ttu-id="7c12b-136">[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-136">[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]</span></span>

<span data-ttu-id="7c12b-137">Le fichier « code-behind » *Pages/Index2.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-137">The *Pages/Index2.cshtml.cs* 'code-behind' file:</span></span>

<span data-ttu-id="7c12b-138">[!code-cs[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-138">[!code-cs[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]</span></span>

<span data-ttu-id="7c12b-139">Par convention, le fichier de classe `PageModel` a le même nom que le fichier Page Razor, avec *.cs* en plus.</span><span class="sxs-lookup"><span data-stu-id="7c12b-139">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="7c12b-140">Par exemple, la page Razor précédente est *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-140">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="7c12b-141">Le fichier contenant la classe `PageModel` se nomme *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-141">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="7c12b-142">Pour les pages simples, cela ne pose pas de problème de combiner la classe `PageModel` avec le balisage Razor.</span><span class="sxs-lookup"><span data-stu-id="7c12b-142">For simple pages, mixing the `PageModel` class with the Razor markup is fine.</span></span> <span data-ttu-id="7c12b-143">Si votre code est plus complexe, nous vous recommandons de séparer le code de modèle de page.</span><span class="sxs-lookup"><span data-stu-id="7c12b-143">For more complex code, it's a best practice to keep the page model code separate.</span></span>

<span data-ttu-id="7c12b-144">Les associations des chemins d’URL aux pages sont déterminées par l’emplacement de la page dans le système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="7c12b-144">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="7c12b-145">Le tableau suivant montre un chemin Page Razor et l’URL correspondante :</span><span class="sxs-lookup"><span data-stu-id="7c12b-145">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="7c12b-146">Nom et chemin de fichier</span><span class="sxs-lookup"><span data-stu-id="7c12b-146">File name and path</span></span>               | <span data-ttu-id="7c12b-147">URL correspondante</span><span class="sxs-lookup"><span data-stu-id="7c12b-147">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="7c12b-148">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7c12b-148">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="7c12b-149">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="7c12b-149">`/` or `/Index`</span></span> |
| <span data-ttu-id="7c12b-150">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7c12b-150">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="7c12b-151">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7c12b-151">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="7c12b-152">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7c12b-152">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="7c12b-153">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="7c12b-153">`/Store` or `/Store/Index`</span></span>  |

<span data-ttu-id="7c12b-154">Remarques :</span><span class="sxs-lookup"><span data-stu-id="7c12b-154">Notes:</span></span>

* <span data-ttu-id="7c12b-155">Le runtime recherche les fichiers Pages Razor dans le dossier *Pages* par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c12b-155">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="7c12b-156">`Index` est la page par défaut quand une URL n’inclut pas de page.</span><span class="sxs-lookup"><span data-stu-id="7c12b-156">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="writing-a-basic-form"></a><span data-ttu-id="7c12b-157">Écriture d’un formulaire de base</span><span class="sxs-lookup"><span data-stu-id="7c12b-157">Writing a basic form</span></span>

<span data-ttu-id="7c12b-158">Les fonctionnalités des pages Razor sont conçues pour simplifier les modèles courants utilisés avec les navigateurs web.</span><span class="sxs-lookup"><span data-stu-id="7c12b-158">Razor Pages features are designed to make common patterns used with web browsers easy.</span></span> <span data-ttu-id="7c12b-159">La [liaison de modèle](xref:mvc/models/model-binding), les [Tag Helpers](xref:mvc/views/tag-helpers/intro)et les assistances HTML *fonctionnent tous* avec les propriétés définies dans une classe Page Razor.</span><span class="sxs-lookup"><span data-stu-id="7c12b-159">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="7c12b-160">Considérez une page qui implémente un formulaire « Nous contacter » de base pour le modèle `Contact` :</span><span class="sxs-lookup"><span data-stu-id="7c12b-160">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="7c12b-161">Pour les exemples de ce document, le `DbContext` est initialisé dans le fichier [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="7c12b-161">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

<span data-ttu-id="7c12b-162">[!code-cs[main](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-162">[!code-cs[main](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]</span></span>

<span data-ttu-id="7c12b-163">Le modèle de données :</span><span class="sxs-lookup"><span data-stu-id="7c12b-163">The data model:</span></span>

<span data-ttu-id="7c12b-164">[!code-cs[main](index/sample/RazorPagesContacts/Data/Customer.cs)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-164">[!code-cs[main](index/sample/RazorPagesContacts/Data/Customer.cs)]</span></span>

<span data-ttu-id="7c12b-165">Le fichier vue *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-165">The *Pages/Create.cshtml* view file:</span></span>

<span data-ttu-id="7c12b-166">[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-166">[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml)]</span></span>

<span data-ttu-id="7c12b-167">Le fichier code-behind *Pages/Create.cshtml.cs* de la vue :</span><span class="sxs-lookup"><span data-stu-id="7c12b-167">The *Pages/Create.cshtml.cs* code-behind file for the view:</span></span>

<span data-ttu-id="7c12b-168">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=ALL)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-168">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=ALL)]</span></span>

<span data-ttu-id="7c12b-169">Par convention, la classe `PageModel` se nomme `<PageName>Model` et se trouve dans le même espace de noms que la page.</span><span class="sxs-lookup"><span data-stu-id="7c12b-169">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span> <span data-ttu-id="7c12b-170">Peu de modifications sont nécessaires pour effectuer une conversion à partir d’une page à l’aide de `@functions` afin de définir des gestionnaires et une page avec une classe `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-170">Not much change is needed to convert from a page using `@functions` to define handlers and a page using a `PageModel` class.</span></span>

<span data-ttu-id="7c12b-171">L’utilisation d’un fichier code-behind `PageModel` prend en charge les tests unitaires, mais vous oblige à écrire un constructeur et une classe explicites.</span><span class="sxs-lookup"><span data-stu-id="7c12b-171">Using a `PageModel` code-behind file supports unit testing, but requires you to write an explicit constructor and class.</span></span> <span data-ttu-id="7c12b-172">Les pages sans fichiers code-behind `PageModel` prennent en charge la compilation au moment de l’exécution, ce qui peut être un avantage lors du développement.</span><span class="sxs-lookup"><span data-stu-id="7c12b-172">Pages without `PageModel` code-behind files support runtime compilation, which can be an advantage in development.</span></span>  <!-- review: advantage because you can make changes and refresh the browser without explicitly compiling the app -->

<span data-ttu-id="7c12b-173">La page a une *méthode de gestionnaire* `OnPostAsync`, qui s’exécute sur les requêtes `POST` (quand un utilisateur poste le formulaire).</span><span class="sxs-lookup"><span data-stu-id="7c12b-173">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="7c12b-174">Vous pouvez ajouter des méthodes de gestionnaire pour n’importe quel verbe HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c12b-174">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="7c12b-175">Les gestionnaires les plus courants sont :</span><span class="sxs-lookup"><span data-stu-id="7c12b-175">The most common handlers are:</span></span>

* <span data-ttu-id="7c12b-176">`OnGet` pour initialiser l’état nécessaire pour la page.</span><span class="sxs-lookup"><span data-stu-id="7c12b-176">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="7c12b-177">Exemple [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="7c12b-177">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="7c12b-178">`OnPost` pour gérer les envois de formulaire.</span><span class="sxs-lookup"><span data-stu-id="7c12b-178">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="7c12b-179">Le suffixe de nommage `Async` est facultatif, mais souvent utilisé par convention pour les fonctions asynchrones.</span><span class="sxs-lookup"><span data-stu-id="7c12b-179">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="7c12b-180">Le code `OnPostAsync` dans l’exemple précédent est similaire à celui que vous écririez normalement dans un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7c12b-180">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="7c12b-181">Le code précédent est typique des pages Razor.</span><span class="sxs-lookup"><span data-stu-id="7c12b-181">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="7c12b-182">La plupart des primitives MVC comme la [liaison de modèle](xref:mvc/models/model-binding), la [validation](xref:mvc/models/validation) et les résultats d’action sont partagées.</span><span class="sxs-lookup"><span data-stu-id="7c12b-182">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="7c12b-183">La méthode `OnPostAsync` précédente :</span><span class="sxs-lookup"><span data-stu-id="7c12b-183">The previous `OnPostAsync` method:</span></span>

<span data-ttu-id="7c12b-184">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=OnPostAsync)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-184">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=OnPostAsync)]</span></span>

<span data-ttu-id="7c12b-185">Le flux de base de `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="7c12b-185">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="7c12b-186">Recherchez les erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="7c12b-186">Check for validation errors.</span></span>

*  <span data-ttu-id="7c12b-187">S’il n’y a aucune erreur, enregistrez les données et redirigez.</span><span class="sxs-lookup"><span data-stu-id="7c12b-187">If there are no errors, save the data and redirect.</span></span>
*  <span data-ttu-id="7c12b-188">S’il y a des erreurs, réaffichez la page avec des messages de validation.</span><span class="sxs-lookup"><span data-stu-id="7c12b-188">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="7c12b-189">La validation côté client est identique à celle des applications ASP.NET Core MVC traditionnelles.</span><span class="sxs-lookup"><span data-stu-id="7c12b-189">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="7c12b-190">Dans de nombreux cas, les erreurs de validation sont détectées sur le client et jamais envoyées au serveur.</span><span class="sxs-lookup"><span data-stu-id="7c12b-190">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="7c12b-191">Quand les données sont entrées correctement, la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `RedirectToPage` pour retourner une instance de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-191">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="7c12b-192">`RedirectToPage` est un nouveau résultat d’action, semblable à `RedirectToAction` ou `RedirectToRoute`, mais personnalisé pour les pages.</span><span class="sxs-lookup"><span data-stu-id="7c12b-192">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="7c12b-193">Dans l’exemple précédent, il redirige vers la page Index racine (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="7c12b-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="7c12b-194">`RedirectToPage` est détaillé dans la section [Génération d’URL pour les pages](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="7c12b-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="7c12b-195">Quand le formulaire envoyé comporte des erreurs de validation (qui sont passées au serveur), la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `Page`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-195">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="7c12b-196">`Page` retourne une instance de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-196">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="7c12b-197">Le retour de `Page` est similaire à la façon dont les actions dans les contrôleurs retournent `View`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="7c12b-198">`PageResult` est le type de retour <!-- Review  --> par défaut pour une méthode de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="7c12b-198">`PageResult` is the default <!-- Review  --> return type for a handler method.</span></span> <span data-ttu-id="7c12b-199">Une méthode de gestionnaire qui retourne `void` restitue la page.</span><span class="sxs-lookup"><span data-stu-id="7c12b-199">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="7c12b-200">La propriété `Customer` utilise l’attribut `[BindProperty]` pour accepter la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="7c12b-200">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

<span data-ttu-id="7c12b-201">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=PageModel&highlight=10-11)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-201">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=PageModel&highlight=10-11)]</span></span>

<span data-ttu-id="7c12b-202">Par défaut, les pages Razor lient les propriétés uniquement avec les verbes non-GET.</span><span class="sxs-lookup"><span data-stu-id="7c12b-202">Razor Pages, by default, bind properties only with non-GET verbs.</span></span> <span data-ttu-id="7c12b-203">La liaison aux propriétés peut réduire la quantité de code à écrire.</span><span class="sxs-lookup"><span data-stu-id="7c12b-203">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="7c12b-204">Elle réduit la quantité de code en utilisant la même propriété pour afficher les champs de formulaire (`<input asp-for="Customer.Name" />`) et accepter l’entrée.</span><span class="sxs-lookup"><span data-stu-id="7c12b-204">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name" />`) and accept the input.</span></span>

<span data-ttu-id="7c12b-205">Le code suivant montre la version combinée de la page de création :</span><span class="sxs-lookup"><span data-stu-id="7c12b-205">The following code shows the combined version of the create page:</span></span>

<span data-ttu-id="7c12b-206">[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/CreateCombined.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-206">[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/CreateCombined.cshtml)]</span></span>

<span data-ttu-id="7c12b-207">Au lieu d’utiliser `@model`, nous tirons parti d’une nouvelle fonctionnalité des pages.</span><span class="sxs-lookup"><span data-stu-id="7c12b-207">Rather than using `@model`, we're taking advantage of a new feature for Pages.</span></span> <span data-ttu-id="7c12b-208">Par défaut, la classe générée dérivée de `Page` *est* le modèle.</span><span class="sxs-lookup"><span data-stu-id="7c12b-208">By default, the generated `Page`-derived class *is* the model.</span></span> <span data-ttu-id="7c12b-209">L’utilisation d’un *modèle d’affichage* avec les vues Razor est une bonne pratique.</span><span class="sxs-lookup"><span data-stu-id="7c12b-209">Using a *view model* with Razor views is a best practice.</span></span> <span data-ttu-id="7c12b-210">Avec les pages Razor, vous obtenez un modèle d’affichage *automatiquement*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-210">With Pages, you get a view model *automatically*.</span></span>

<span data-ttu-id="7c12b-211">Le principal changement est le remplacement de l’injection de constructeur par des propriétés injectées (`@inject`).</span><span class="sxs-lookup"><span data-stu-id="7c12b-211">The main change is replacing constructor injection with injected (`@inject`) properties.</span></span> <span data-ttu-id="7c12b-212">Cette page utilise [@inject](xref:mvc/views/razor#inject) pour [l’injection de dépendance de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection).</span><span class="sxs-lookup"><span data-stu-id="7c12b-212">This page uses [@inject](xref:mvc/views/razor#inject) for [constructor dependency injection](xref:mvc/controllers/dependency-injection#constructor-injection).</span></span> <span data-ttu-id="7c12b-213">L’instruction `@inject` génère et initialise la propriété `Db` utilisée dans `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-213">The `@inject` statement generates and initializes the `Db` property that is used in `OnPostAsync`.</span></span> <span data-ttu-id="7c12b-214">Les propriétés injectées (`@inject`) sont définies avant l’exécution des méthodes de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="7c12b-214">Injected (`@inject`) properties are set before handler methods run.</span></span>


<span data-ttu-id="7c12b-215">La page d’accueil (*Index.cshtml*) :</span><span class="sxs-lookup"><span data-stu-id="7c12b-215">The home page (*Index.cshtml*):</span></span>

<span data-ttu-id="7c12b-216">[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-216">[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml)]</span></span>

<span data-ttu-id="7c12b-217">Le fichier code-behind *Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-217">The code behind *Index.cshtml.cs* file:</span></span>

<span data-ttu-id="7c12b-218">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-218">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]</span></span>

<span data-ttu-id="7c12b-219">Le fichier *Index.cshtml* contient le balisage suivant pour créer un lien d’édition pour chaque contact :</span><span class="sxs-lookup"><span data-stu-id="7c12b-219">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

```html
<a asp-page="./Edit" asp-route-id="@contact.Id">edit</a>
```

<span data-ttu-id="7c12b-220">Le [tag helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper) a utilisé l’attribut [asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper#route) pour générer un lien vers la page Edit.</span><span class="sxs-lookup"><span data-stu-id="7c12b-220">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper) used the [asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper#route) attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="7c12b-221">Le lien contient des données d’itinéraire avec l’ID de contact.</span><span class="sxs-lookup"><span data-stu-id="7c12b-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="7c12b-222">Par exemple, `http://localhost:5000/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-222">For example, `http://localhost:5000/Edit/1`.</span></span>

<span data-ttu-id="7c12b-223">Le fichier *Pages/Edit.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-223">The *Pages/Edit.cshtml* file:</span></span>

<span data-ttu-id="7c12b-224">[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-224">[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]</span></span>

<span data-ttu-id="7c12b-225">La première ligne contient la directive `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-225">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="7c12b-226">La contrainte de routage `"{id:int}"` indique à la page qu’elle doit accepter les requêtes qui contiennent des données d’itinéraire `int`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-226">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="7c12b-227">Si une requête à la page ne contient de données d’itinéraire qui peuvent être converties en `int`, le runtime retourne une erreur HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="7c12b-227">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span>

<span data-ttu-id="7c12b-228">Le fichier *Pages/Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-228">The *Pages/Edit.cshtml.cs* file:</span></span>

<span data-ttu-id="7c12b-229">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-229">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="7c12b-230">XSRF/CSRF et pages Razor</span><span class="sxs-lookup"><span data-stu-id="7c12b-230">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="7c12b-231">Vous n’avez aucun code à écrire pour la [validation anti-contrefaçon](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="7c12b-231">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="7c12b-232">La validation et la génération de jetons anti-contrefaçon sont automatiquement incluses dans les pages Razor.</span><span class="sxs-lookup"><span data-stu-id="7c12b-232">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>
## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="7c12b-233">Utilisation de dispositions, partiels, modèles et Tag Helpers avec les pages Razor</span><span class="sxs-lookup"><span data-stu-id="7c12b-233">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="7c12b-234">Les pages Razor fonctionnent avec toutes les fonctionnalités du moteur de vue Razor.</span><span class="sxs-lookup"><span data-stu-id="7c12b-234">Pages work with all the features of the Razor view engine.</span></span> <span data-ttu-id="7c12b-235">Les dispositions, partiels, modèles, Tag Helpers, *_ViewStart.cshtml* et *_ViewImports.cshtml* fonctionnent de la même façon que pour les vues Razor classiques.</span><span class="sxs-lookup"><span data-stu-id="7c12b-235">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="7c12b-236">Nous allons nettoyer un peu cette page en tirant parti de certaines de ces fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="7c12b-236">Let's declutter this page by taking advantage of some of those features.</span></span>

<span data-ttu-id="7c12b-237">Ajoutez une [page de disposition](xref:mvc/views/layout) à *Pages/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-237">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

<span data-ttu-id="7c12b-238">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-238">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]</span></span>

<span data-ttu-id="7c12b-239">La [disposition](xref:mvc/views/layout) :</span><span class="sxs-lookup"><span data-stu-id="7c12b-239">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="7c12b-240">Contrôle la disposition de chaque page (à moins que la page ne refuse la disposition).</span><span class="sxs-lookup"><span data-stu-id="7c12b-240">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="7c12b-241">Importe des structures HTML telles que JavaScript et les feuilles de style.</span><span class="sxs-lookup"><span data-stu-id="7c12b-241">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="7c12b-242">Pour plus d’informations, consultez [Page de disposition](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="7c12b-242">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="7c12b-243">La propriété [Layout](xref:mvc/views/layout#specifying-a-layout) est définie dans *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-243">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

<span data-ttu-id="7c12b-244">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-244">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]</span></span>

<span data-ttu-id="7c12b-245">Remarque : La disposition est dans le dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-245">Note: The layout is in the *Pages* folder.</span></span> <span data-ttu-id="7c12b-246">Les pages recherchent d’autres vues (dispositions, modèles, partiels) hiérarchiquement, en commençant dans le même dossier que la page active.</span><span class="sxs-lookup"><span data-stu-id="7c12b-246">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="7c12b-247">Une disposition dans le dossier *Pages* peut être utilisée à partir de n’importe quelle page Razor sous le dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-247">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="7c12b-248">Nous vous recommandons de **ne pas** placer le fichier de disposition dans le dossier *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-248">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="7c12b-249">*Views/Shared* est un modèle de vues MVC.</span><span class="sxs-lookup"><span data-stu-id="7c12b-249">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="7c12b-250">Les pages Razor sont censées s’appuyer sur la hiérarchie des dossiers, pas sur les conventions de chemins.</span><span class="sxs-lookup"><span data-stu-id="7c12b-250">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="7c12b-251">La recherche de vue à partir d’une page Razor inclut le dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-251">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="7c12b-252">Les dispositions, modèles et partiels que vous utilisez avec les contrôleurs MVC et les vues Razor conventionnelles *fonctionnent simplement*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-252">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="7c12b-253">Ajoutez un fichier *Pages/_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-253">Add a *Pages/_ViewImports.cshtml* file:</span></span>

<span data-ttu-id="7c12b-254">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-254">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]</span></span>

<span data-ttu-id="7c12b-255">`@namespace` est expliqué plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="7c12b-255">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="7c12b-256">La directive `@addTagHelper` permet de bénéficier des [Tag Helpers intégrés](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/built-in/) dans toutes les pages du dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-256">The `@addTagHelper` directive brings in the [built-in Tag Helpers](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/built-in/) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="7c12b-257">Quand la directive `@namespace` est utilisée explicitement sur une page :</span><span class="sxs-lookup"><span data-stu-id="7c12b-257">When the `@namespace` directive is used explicitly on a page:</span></span>

<span data-ttu-id="7c12b-258">[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-258">[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]</span></span>

<span data-ttu-id="7c12b-259">La directive définit l’espace de noms pour la page.</span><span class="sxs-lookup"><span data-stu-id="7c12b-259">The directive sets the namespace for the page.</span></span> <span data-ttu-id="7c12b-260">La directive `@model` n’a pas besoin d’inclure l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="7c12b-260">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="7c12b-261">Quand la directive `@namespace` est contenue dans *_ViewImports.cshtml*, l’espace de noms spécifié fournit le préfixe de l’espace de noms généré dans la Page qui importe la directive `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-261">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="7c12b-262">Le reste de l’espace de noms généré (la partie suffixe) est le chemin relatif séparé par un point entre le dossier contenant *_ViewImports.cshtml* et le dossier contenant la page.</span><span class="sxs-lookup"><span data-stu-id="7c12b-262">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="7c12b-263">Par exemple, le fichier code-behind *Pages/Customers/Edit.cshtml.cs* définit explicitement l’espace de noms :</span><span class="sxs-lookup"><span data-stu-id="7c12b-263">For example, the code behind file *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

<span data-ttu-id="7c12b-264">[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=namespace)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-264">[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=namespace)]</span></span>

<span data-ttu-id="7c12b-265">Le fichier *Pages/_ViewImports.cshtml* définit l’espace de noms suivant :</span><span class="sxs-lookup"><span data-stu-id="7c12b-265">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

<span data-ttu-id="7c12b-266">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-266">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]</span></span>

<span data-ttu-id="7c12b-267">L’espace de noms généré pour la page Razor *Pages/Customers/Edit.cshtml* est identique au fichier code-behind.</span><span class="sxs-lookup"><span data-stu-id="7c12b-267">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the code behind file.</span></span> <span data-ttu-id="7c12b-268">La directive `@namespace` a été conçue pour que les classes C# ajoutées à un projet et au code généré par les pages *fonctionnent simplement* sans avoir à ajouter de directive `@using` pour le fichier code-behind.</span><span class="sxs-lookup"><span data-stu-id="7c12b-268">The `@namespace` directive was designed so the C# classes added to a project and pages-generated code *just work* without having to add an `@using` directive for the code behind file.</span></span>

<span data-ttu-id="7c12b-269">Remarque : `@namespace` fonctionne également avec les vues Razor classiques.</span><span class="sxs-lookup"><span data-stu-id="7c12b-269">Note: `@namespace` also works with conventional Razor views.</span></span>

<span data-ttu-id="7c12b-270">Le fichier de vue *Pages/Create.cshtml* d’origine :</span><span class="sxs-lookup"><span data-stu-id="7c12b-270">The original *Pages/Create.cshtml* view file:</span></span>

<span data-ttu-id="7c12b-271">[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-271">[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]</span></span>

<span data-ttu-id="7c12b-272">La page mise à jour :</span><span class="sxs-lookup"><span data-stu-id="7c12b-272">The updated page:</span></span>

<span data-ttu-id="7c12b-273">Le fichier vue *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-273">The *Pages/Create.cshtml* view file:</span></span>

<span data-ttu-id="7c12b-274">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-274">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]</span></span>

<span data-ttu-id="7c12b-275">Le [projet de démarrage de pages Razor](#rpvs17) contient *Pages/_ValidationScriptsPartial.cshtml*, qui connecte la validation côté client.</span><span class="sxs-lookup"><span data-stu-id="7c12b-275">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="7c12b-276">Génération d’URL pour les pages</span><span class="sxs-lookup"><span data-stu-id="7c12b-276">URL generation for Pages</span></span>

<span data-ttu-id="7c12b-277">La page `Create`, illustrée précédemment, utilise `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="7c12b-277">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

<span data-ttu-id="7c12b-278">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=OnPostAsync&highlight=10)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-278">[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=OnPostAsync&highlight=10)]</span></span>

<span data-ttu-id="7c12b-279">L’application a la structure de fichiers/dossiers suivante :</span><span class="sxs-lookup"><span data-stu-id="7c12b-279">The app has the following file/folder structure</span></span>

* <span data-ttu-id="7c12b-280">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="7c12b-280">*/Pages*</span></span>

  * <span data-ttu-id="7c12b-281">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7c12b-281">*Index.cshtml*</span></span>
  * <span data-ttu-id="7c12b-282">*/Customer*</span><span class="sxs-lookup"><span data-stu-id="7c12b-282">*/Customer*</span></span>

    * <span data-ttu-id="7c12b-283">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7c12b-283">*Create.cshtml*</span></span>
    * <span data-ttu-id="7c12b-284">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7c12b-284">*Edit.cshtml*</span></span>
    * <span data-ttu-id="7c12b-285">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7c12b-285">*Index.cshtml*</span></span>

<span data-ttu-id="7c12b-286">Une fois l’opération réussie, les pages *Pages/Customers/Create.cshtml* et *Pages/Customers/Edit.cshtml* redirigent vers *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-286">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="7c12b-287">La chaîne `/Index` fait partie de l’URI pour accéder à la page précédente.</span><span class="sxs-lookup"><span data-stu-id="7c12b-287">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="7c12b-288">La chaîne `/Index` peut être utilisée pour générer l’URI de la page *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-288">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="7c12b-289">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7c12b-289">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="7c12b-290">Le nom de la page est le chemin de la page à partir du dossier racine */Pages* (y compris un `/` de début, par exemple `/Index`).</span><span class="sxs-lookup"><span data-stu-id="7c12b-290">The page name is the path to the page from the root */Pages* folder (including a leading `/`, for example `/Index`).</span></span> <span data-ttu-id="7c12b-291">Les exemples de génération d’URL précédents sont beaucoup plus riches en fonctionnalités que le simple codage en dur d’une URL.</span><span class="sxs-lookup"><span data-stu-id="7c12b-291">The preceding URL generation samples are much more feature rich than just hardcoding a URL.</span></span> <span data-ttu-id="7c12b-292">La génération d’URL utilise le [routage](xref:mvc/controllers/routing) et peut générer et encoder des paramètres en fonction de la façon dont l’itinéraire est défini dans le chemin de destination.</span><span class="sxs-lookup"><span data-stu-id="7c12b-292">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="7c12b-293">La génération d’URL pour les pages prend en charge les noms relatifs.</span><span class="sxs-lookup"><span data-stu-id="7c12b-293">URL generation for pages supports relative names.</span></span> <span data-ttu-id="7c12b-294">Le tableau suivant montre la page Index sélectionnée avec différents paramètres `RedirectToPage` à partir de *Pages/Customers/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="7c12b-294">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="7c12b-295">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="7c12b-295">RedirectToPage(x)</span></span>| <span data-ttu-id="7c12b-296">Page</span><span class="sxs-lookup"><span data-stu-id="7c12b-296">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="7c12b-297">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="7c12b-297">RedirectToPage("/Index")</span></span> | <span data-ttu-id="7c12b-298">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="7c12b-298">*Pages/Index*</span></span> |
| <span data-ttu-id="7c12b-299">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="7c12b-299">RedirectToPage("./Index");</span></span> | <span data-ttu-id="7c12b-300">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="7c12b-300">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="7c12b-301">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="7c12b-301">RedirectToPage("../Index")</span></span> | <span data-ttu-id="7c12b-302">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="7c12b-302">*Pages/Index*</span></span> |
| <span data-ttu-id="7c12b-303">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="7c12b-303">RedirectToPage("Index")</span></span>  | <span data-ttu-id="7c12b-304">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="7c12b-304">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="7c12b-305">`RedirectToPage("Index")`, `RedirectToPage("./Index")` et `RedirectToPage("../Index")` sont des *noms relatifs*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-305">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="7c12b-306">Le paramètre `RedirectToPage` est *combiné* avec le chemin de la page active pour calculer le nom de la page de destination.</span><span class="sxs-lookup"><span data-stu-id="7c12b-306">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page. -- page name, not page path -->

<span data-ttu-id="7c12b-307">La liaison de nom relatif est utile lors de la création de sites avec une structure complexe.</span><span class="sxs-lookup"><span data-stu-id="7c12b-307">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="7c12b-308">Si vous utilisez des noms relatifs pour établir une liaison entre les pages d’un dossier, vous pouvez renommer ce dossier.</span><span class="sxs-lookup"><span data-stu-id="7c12b-308">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="7c12b-309">Tous les liens fonctionneront encore (car ils n’incluent pas le nom du dossier).</span><span class="sxs-lookup"><span data-stu-id="7c12b-309">All the links still work (because they didn't include the folder name).</span></span>

## <a name="tempdata"></a><span data-ttu-id="7c12b-310">TempData</span><span class="sxs-lookup"><span data-stu-id="7c12b-310">TempData</span></span>

<span data-ttu-id="7c12b-311">ASP.NET Core expose la propriété [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) sur un [contrôleur](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="7c12b-311">ASP.NET Core exposes the [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="7c12b-312">Cette propriété stocke les données jusqu’à ce qu’elles soient lues.</span><span class="sxs-lookup"><span data-stu-id="7c12b-312">This property stores data until it is read.</span></span> <span data-ttu-id="7c12b-313">Vous pouvez utiliser les méthodes `Keep` et `Peek` pour examiner les données sans suppression.</span><span class="sxs-lookup"><span data-stu-id="7c12b-313">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="7c12b-314">`TempData` est utile pour la redirection, quand des données sont nécessaires pour plusieurs requêtes.</span><span class="sxs-lookup"><span data-stu-id="7c12b-314">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="7c12b-315">L’attribut `[TempData]` est une nouveauté dans ASP.NET Core 2.0. Il est pris en charge sur les contrôleurs et les pages.</span><span class="sxs-lookup"><span data-stu-id="7c12b-315">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="7c12b-316">Le code suivant définit la valeur de `Message` à l’aide de `TempData`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-316">The following code sets the value of `Message` using `TempData`.</span></span>
<span data-ttu-id="7c12b-317">[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,27-28&name=snippetTemp)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-317">[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,27-28&name=snippetTemp)]</span></span>

<span data-ttu-id="7c12b-318">Le balisage suivant dans le fichier *Pages/Customers/Index.cshtml* affiche la valeur de `Message` à l’aide de `TempData`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-318">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="7c12b-319">Le fichier code-behind *Pages/Customers/Index.cshtml.cs* applique l’attribut `[TempData]` à la propriété `Message`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-319">The *Pages/Customers/Index.cshtml.cs* code-behind file applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="7c12b-320">Pour plus d’informations, consultez [TempData](xref:fundamentals/app-state#temp).</span><span class="sxs-lookup"><span data-stu-id="7c12b-320">See [TempData](xref:fundamentals/app-state#temp) for more information.</span></span>

<a name="mhpp"></a>
## <a name="multiple-handlers-per-page"></a><span data-ttu-id="7c12b-321">Plusieurs gestionnaires par page</span><span class="sxs-lookup"><span data-stu-id="7c12b-321">Multiple handlers per page</span></span>

<span data-ttu-id="7c12b-322">La page suivante génère un balisage pour deux gestionnaires de page à l’aide du Tag Helper `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="7c12b-322">The following page generates markup for two page handlers using the `asp-page-handler` Tag Helper:</span></span>

<span data-ttu-id="7c12b-323">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-323">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]</span></span>

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there is no `asp-` attribute   -->

<span data-ttu-id="7c12b-324">Le formulaire dans l’exemple précédent contient deux boutons d’envoi, chacun utilisant `FormActionTagHelper` pour envoyer à une URL différente.</span><span class="sxs-lookup"><span data-stu-id="7c12b-324">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="7c12b-325">L’attribut `asp-page-handler` est un complément de `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-325">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="7c12b-326">`asp-page-handler` génère des URL qui envoient à chacune des méthodes de gestionnaire définies par une page.</span><span class="sxs-lookup"><span data-stu-id="7c12b-326">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="7c12b-327">`asp-page` n’est pas spécifié, car l’exemple établit une liaison à la page active.</span><span class="sxs-lookup"><span data-stu-id="7c12b-327">`asp-page` is not specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="7c12b-328">Le fichier code-behind :</span><span class="sxs-lookup"><span data-stu-id="7c12b-328">The code-behind file:</span></span>

<span data-ttu-id="7c12b-329">[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-329">[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]</span></span>

<span data-ttu-id="7c12b-330">Le code précédent utilise des *méthodes de gestionnaire nommées*.</span><span class="sxs-lookup"><span data-stu-id="7c12b-330">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="7c12b-331">Pour créer des méthodes de gestionnaire nommées, il faut prendre le texte dans le nom après `On<HTTP Verb>` et avant `Async` (le cas échéant).</span><span class="sxs-lookup"><span data-stu-id="7c12b-331">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="7c12b-332">Dans l’exemple précédent, les méthodes de page sont OnPost**JoinList**Async et OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="7c12b-332">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="7c12b-333">Avec *OnPost* et *Async* supprimés, les noms des gestionnaires sont `JoinList` et `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-333">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

<span data-ttu-id="7c12b-334">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-334">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]</span></span>

<span data-ttu-id="7c12b-335">Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-335">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="7c12b-336">Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-336">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="customizing-routing"></a><span data-ttu-id="7c12b-337">Personnalisation du routage</span><span class="sxs-lookup"><span data-stu-id="7c12b-337">Customizing Routing</span></span>

<span data-ttu-id="7c12b-338">Si vous ne voulez pas avoir la chaîne de requête `?handler=JoinList` dans l’URL, vous pouvez changer l’itinéraire pour placer le nom du gestionnaire dans la partie chemin de l’URL.</span><span class="sxs-lookup"><span data-stu-id="7c12b-338">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="7c12b-339">Vous pouvez personnaliser l’itinéraire en ajoutant un modèle d’itinéraire placé entre des guillemets doubles après la directive `@page`.</span><span class="sxs-lookup"><span data-stu-id="7c12b-339">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

<span data-ttu-id="7c12b-340">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-340">[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]</span></span>


<span data-ttu-id="7c12b-341">L’itinéraire précédent place le nom du gestionnaire dans le chemin d’URL au lieu de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="7c12b-341">The preceding route puts the handler name in the URL path instead of the query string.</span></span> <span data-ttu-id="7c12b-342">Le `?` suivant `handler` signifie que le paramètre d’itinéraire est facultatif.</span><span class="sxs-lookup"><span data-stu-id="7c12b-342">The `?` following `handler` means the route parameter is optional.</span></span>

<span data-ttu-id="7c12b-343">Vous pouvez utiliser `@page` pour ajouter des segments et des paramètres supplémentaires à l’itinéraire d’une page.</span><span class="sxs-lookup"><span data-stu-id="7c12b-343">You can use `@page` to add additional segments and parameters to a page's route.</span></span> <span data-ttu-id="7c12b-344">Tout ce qui y figure est **ajouté** à l’itinéraire par défaut de la page.</span><span class="sxs-lookup"><span data-stu-id="7c12b-344">Whatever's there is **appended** to the default route of the page.</span></span> <span data-ttu-id="7c12b-345">L’utilisation d’un chemin absolu ou virtuel pour changer l’itinéraire de la page (comme `"~/Some/Other/Path"`) n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="7c12b-345">Using an absolute or virtual path to change the page's route (like `"~/Some/Other/Path"`) is not supported.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="7c12b-346">Configuration et paramètres</span><span class="sxs-lookup"><span data-stu-id="7c12b-346">Configuration and settings</span></span>

<span data-ttu-id="7c12b-347">Pour configurer les options avancées, utilisez la méthode d’extension `AddRazorPagesOptions` sur le générateur MVC :</span><span class="sxs-lookup"><span data-stu-id="7c12b-347">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

<span data-ttu-id="7c12b-348">[!code-cs[main](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="7c12b-348">[!code-cs[main](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet1)]</span></span>

<span data-ttu-id="7c12b-349">Actuellement, vous pouvez utiliser `RazorPagesOptions` pour définir le répertoire racine pour les pages, ou ajouter des conventions de modèle d’application pour les pages.</span><span class="sxs-lookup"><span data-stu-id="7c12b-349">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="7c12b-350">Nous espérons à l’avenir favoriser une extensibilité supplémentaire de cette manière.</span><span class="sxs-lookup"><span data-stu-id="7c12b-350">We hope to enable more extensibility this way in the future.</span></span>

<span data-ttu-id="7c12b-351">Pour précompiler des vues, consultez [Compilation de vue Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="7c12b-351">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="7c12b-352">[Téléchargez ou affichez des exemples de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="7c12b-352">[Download or view sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).</span></span>

<span data-ttu-id="7c12b-353">Consultez [Bien démarrer avec les pages Razor dans ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start), qui s’appuie sur cette introduction.</span><span class="sxs-lookup"><span data-stu-id="7c12b-353">See [Getting started with Razor Pages in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

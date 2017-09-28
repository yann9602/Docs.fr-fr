---
title: Zones
author: rick-anderson
description: Montre comment utiliser des zones.
keywords: ASP.NET Core, domaines, le routage, vues
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 5e16d5e8-5696-4cb2-8ec7-d36be305c922
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/areas
ms.openlocfilehash: 0f388ba090ada11a0ac7937606cbcd5a89d6263e
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="areas"></a><span data-ttu-id="d839b-104">Zones</span><span class="sxs-lookup"><span data-stu-id="d839b-104">Areas</span></span>

<span data-ttu-id="d839b-105">Par [Dhananjay Kumar](https://twitter.com/debug_mode) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d839b-105">By [Dhananjay Kumar](https://twitter.com/debug_mode)  and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d839b-106">Les zones sont une fonctionnalité d’ASP.NET MVC permet de classer les fonctionnalités connexes dans un groupe comme un espace de noms distinct (pour le routage) et la structure de dossiers (pour les vues).</span><span class="sxs-lookup"><span data-stu-id="d839b-106">Areas are an ASP.NET MVC feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="d839b-107">L’utilisation de zones crée une hiérarchie à des fins de routage en ajoutant un autre paramètre d’itinéraire, `area`à `controller` et `action`.</span><span class="sxs-lookup"><span data-stu-id="d839b-107">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action`.</span></span>

<span data-ttu-id="d839b-108">Les zones permettent de partitionner une application Web ASP.NET Core MVC volumineuse en regroupements fonctionnels plus petits.</span><span class="sxs-lookup"><span data-stu-id="d839b-108">Areas provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="d839b-109">Une zone est en réalité une structure MVC à l’intérieur d’une application.</span><span class="sxs-lookup"><span data-stu-id="d839b-109">An area is effectively an MVC structure inside an application.</span></span> <span data-ttu-id="d839b-110">Dans un projet MVC, les composants logiques tels que le modèle, le contrôleur et vue sont conservées dans des dossiers différents et MVC utilise les conventions d’affectation de noms pour créer la relation entre ces composants.</span><span class="sxs-lookup"><span data-stu-id="d839b-110">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="d839b-111">Pour une application volumineuse, il peut être avantageux de partition de l’application en différents domaines de niveau élevés de fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="d839b-111">For a large app, it may be advantageous to partition the  app into separate high level areas of functionality.</span></span> <span data-ttu-id="d839b-112">Par exemple, une application de commerce électronique avec plusieurs entités, telles que l’extraction, de facturation et de recherche, etc.. Chacune de ces unités ont leurs propres vues des composants logiques, les contrôleurs et les modèles.</span><span class="sxs-lookup"><span data-stu-id="d839b-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span> <span data-ttu-id="d839b-113">Dans ce scénario, vous pouvez utiliser des zones physiquement partitionner les composants d’entreprise dans le même projet.</span><span class="sxs-lookup"><span data-stu-id="d839b-113">In this scenario, you can use Areas to physically partition the business components in the same project.</span></span>

<span data-ttu-id="d839b-114">Une zone peut être définie comme la plus petite des unités fonctionnelles dans un projet ASP.NET MVC de base avec son propre ensemble de modèles, des vues et des contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="d839b-114">An area can be defined as smaller functional units in an ASP.NET Core MVC project with its own set of controllers, views, and models.</span></span>

<span data-ttu-id="d839b-115">Envisagez d’utiliser des zones dans un MVC projet lorsque :</span><span class="sxs-lookup"><span data-stu-id="d839b-115">Consider using Areas in an MVC project when:</span></span>

* <span data-ttu-id="d839b-116">Votre application est constituée de plusieurs composants fonctionnels de haut niveau qui doivent être logiquement séparés</span><span class="sxs-lookup"><span data-stu-id="d839b-116">Your application is made of multiple high-level functional components that should be logically separated</span></span>

* <span data-ttu-id="d839b-117">Vous souhaitez partitionner votre projet MVC afin que chaque zone fonctionnelle permettre être utilisé indépendamment</span><span class="sxs-lookup"><span data-stu-id="d839b-117">You want to partition your MVC project so that each functional area can be worked on independently</span></span>

<span data-ttu-id="d839b-118">Fonctionnalités de la zone :</span><span class="sxs-lookup"><span data-stu-id="d839b-118">Area features:</span></span>

* <span data-ttu-id="d839b-119">Une application ASP.NET MVC de base peut avoir n’importe quel nombre de zones</span><span class="sxs-lookup"><span data-stu-id="d839b-119">An ASP.NET Core MVC app can have any number of areas</span></span>

* <span data-ttu-id="d839b-120">Chaque zone comporte des contrôleurs, des modèles et des vues</span><span class="sxs-lookup"><span data-stu-id="d839b-120">Each area has its own controllers, models, and views</span></span>

* <span data-ttu-id="d839b-121">Vous permet d’organiser des grands projets MVC en plusieurs composants de haut niveau qui peuvent être utilisés indépendamment</span><span class="sxs-lookup"><span data-stu-id="d839b-121">Allows you to organize large MVC projects into multiple high-level components that can be worked on independently</span></span>

* <span data-ttu-id="d839b-122">Prend en charge plusieurs contrôleurs portant le même nom - tant qu’ils disposent différents *zones*</span><span class="sxs-lookup"><span data-stu-id="d839b-122">Supports multiple controllers with the same name - as long as they have different *areas*</span></span>

<span data-ttu-id="d839b-123">Examinons un exemple pour illustrer la façon dont les zones sont créées et utilisées.</span><span class="sxs-lookup"><span data-stu-id="d839b-123">Let's take a look at an example to illustrate how Areas are created and used.</span></span> <span data-ttu-id="d839b-124">Supposons que vous avez une application de magasin qui dispose de deux regroupements distincts de contrôleurs et vues : produits et Services.</span><span class="sxs-lookup"><span data-stu-id="d839b-124">Let's say you have a store app that has two distinct groupings of controllers and views: Products and Services.</span></span> <span data-ttu-id="d839b-125">Un dossier standard structure pour que l’utilisation de zones MVC se présente comme ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="d839b-125">A typical folder structure for that using MVC areas looks like below:</span></span>

* <span data-ttu-id="d839b-126">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="d839b-126">Project name</span></span>

  * <span data-ttu-id="d839b-127">Zones</span><span class="sxs-lookup"><span data-stu-id="d839b-127">Areas</span></span>

    * <span data-ttu-id="d839b-128">Produits</span><span class="sxs-lookup"><span data-stu-id="d839b-128">Products</span></span>

      * <span data-ttu-id="d839b-129">Contrôleurs</span><span class="sxs-lookup"><span data-stu-id="d839b-129">Controllers</span></span>

        * <span data-ttu-id="d839b-130">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d839b-130">HomeController.cs</span></span>

        * <span data-ttu-id="d839b-131">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="d839b-131">ManageController.cs</span></span>

      * <span data-ttu-id="d839b-132">Affichages</span><span class="sxs-lookup"><span data-stu-id="d839b-132">Views</span></span>

        * <span data-ttu-id="d839b-133">Accueil</span><span class="sxs-lookup"><span data-stu-id="d839b-133">Home</span></span>

          * <span data-ttu-id="d839b-134">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d839b-134">Index.cshtml</span></span>

        * <span data-ttu-id="d839b-135">Gérer</span><span class="sxs-lookup"><span data-stu-id="d839b-135">Manage</span></span>

          * <span data-ttu-id="d839b-136">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d839b-136">Index.cshtml</span></span>

    * <span data-ttu-id="d839b-137">Services</span><span class="sxs-lookup"><span data-stu-id="d839b-137">Services</span></span>

      * <span data-ttu-id="d839b-138">Contrôleurs</span><span class="sxs-lookup"><span data-stu-id="d839b-138">Controllers</span></span>

        * <span data-ttu-id="d839b-139">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d839b-139">HomeController.cs</span></span>

      * <span data-ttu-id="d839b-140">Affichages</span><span class="sxs-lookup"><span data-stu-id="d839b-140">Views</span></span>

        * <span data-ttu-id="d839b-141">Accueil</span><span class="sxs-lookup"><span data-stu-id="d839b-141">Home</span></span>

          * <span data-ttu-id="d839b-142">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d839b-142">Index.cshtml</span></span>

<span data-ttu-id="d839b-143">Quand il tente MVC restituer une vue dans une zone, par défaut, il essaie de chercher dans les emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="d839b-143">When MVC tries to render a view in an Area, by default, it tries to look in the following locations:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="d839b-144">Ce sont les emplacements par défaut qui peuvent être modifiés via le `AreaViewLocationFormats` sur la `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`.</span><span class="sxs-lookup"><span data-stu-id="d839b-144">These are the default locations which can be changed via the `AreaViewLocationFormats` on the `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`.</span></span>

<span data-ttu-id="d839b-145">Par exemple, dans le code au lieu d’avoir le nom du dossier sous forme de « Zones », ci-dessous, il a été modifié pour « Catégories ».</span><span class="sxs-lookup"><span data-stu-id="d839b-145">For example, in the below code instead of having the folder name as 'Areas', it has been changed to 'Categories'.</span></span>

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

<span data-ttu-id="d839b-146">Une chose à noter est que la structure de la *vues* dossier est le seul qui est considérée comme important ici et le contenu du reste des dossiers comme *contrôleurs* et *demodèles* est **pas** a d’importance.</span><span class="sxs-lookup"><span data-stu-id="d839b-146">One thing to note is that the structure of the *Views* folder is the only one which is considered important here and the content of the rest of the folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="d839b-147">Par exemple, vous ne devez pas avoir un *contrôleurs* et *modèles* dossier du tout.</span><span class="sxs-lookup"><span data-stu-id="d839b-147">For example, you need not have a *Controllers* and *Models* folder at all.</span></span> <span data-ttu-id="d839b-148">Cela fonctionne parce que le contenu de *contrôleurs* et *modèles* est simplement du code qui est compilé dans un fichier .dll, alors que le contenu de la *vues* jusqu'à une demande qui n’est pas vue a été effectuée.</span><span class="sxs-lookup"><span data-stu-id="d839b-148">This works because the content of *Controllers* and *Models* is just code which gets compiled into a .dll where as the content of the *Views* is not until a request to that view has been made.</span></span>

<span data-ttu-id="d839b-149">Une fois que vous avez défini l’arborescence des dossiers, vous devez indiquer MVC que chaque contrôleur est associé à une zone.</span><span class="sxs-lookup"><span data-stu-id="d839b-149">Once you've defined the folder hierarchy, you need to tell MVC that each controller is associated with an area.</span></span> <span data-ttu-id="d839b-150">Pour ce faire, la décoration de nom du contrôleur avec le `[Area]` attribut.</span><span class="sxs-lookup"><span data-stu-id="d839b-150">You do that by decorating the controller name with the `[Area]` attribute.</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4]}} -->

```csharp
...
   namespace MyStore.Areas.Products.Controllers
   {
       [Area("Products")]
       public class HomeController : Controller
       {
           // GET: /Products/Home/Index
           public IActionResult Index()
           {
               return View();
           }

           // GET: /Products/Home/Create
           public IActionResult Create()
           {
               return View();
           }
       }
   }
   ```

<span data-ttu-id="d839b-151">Définir une définition d’itinéraire qui fonctionne avec vos zones nouvellement créés.</span><span class="sxs-lookup"><span data-stu-id="d839b-151">Set up a route definition that works with your newly created areas.</span></span> <span data-ttu-id="d839b-152">Le [le routage pour les Actions de contrôleur](routing.md) article détaille comment créer des définitions de route, y compris à l’aide d’itinéraires classiques et les itinéraires d’attribut.</span><span class="sxs-lookup"><span data-stu-id="d839b-152">The [Routing to Controller Actions](routing.md) article goes into detail about how to create route definitions, including using conventional routes versus attribute routes.</span></span> <span data-ttu-id="d839b-153">Dans cet exemple, nous allons utiliser un itinéraire classique.</span><span class="sxs-lookup"><span data-stu-id="d839b-153">In this example, we'll use a conventional route.</span></span> <span data-ttu-id="d839b-154">Pour ce faire, ouvrez le *Startup.cs* de fichier et le modifier en ajoutant la `areaRoute` nommé définition d’itinéraire ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="d839b-154">To do so, open the *Startup.cs* file and modify it by adding the `areaRoute` named route definition below.</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4, 5, 6]}} -->

```csharp
...
   app.UseMvc(routes =>
   {
     routes.MapRoute(name: "areaRoute",
       template: "{area:exists}/{controller=Home}/{action=Index}");

     routes.MapRoute(
         name: "default",
         template: "{controller=Home}/{action=Index}/{id?}");
   });
   ```

<span data-ttu-id="d839b-155">Accédant à `http://<yourApp>/products`, le `Index` méthode d’action de la `HomeController` dans le `Products` zone sera appelée.</span><span class="sxs-lookup"><span data-stu-id="d839b-155">Browsing to `http://<yourApp>/products`, the `Index` action method of the `HomeController` in the `Products` area will be invoked.</span></span>

## <a name="link-generation"></a><span data-ttu-id="d839b-156">Génération du lien</span><span class="sxs-lookup"><span data-stu-id="d839b-156">Link Generation</span></span>

* <span data-ttu-id="d839b-157">Générer des liens à partir d’une action dans une zone en fonction contrôleur à une autre action au sein du même contrôleur.</span><span class="sxs-lookup"><span data-stu-id="d839b-157">Generating links from an action within an area based controller to another action within the same controller.</span></span>

  <span data-ttu-id="d839b-158">Supposons que le chemin d’accès de la requête actuelle est similaire à`/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="d839b-158">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="d839b-159">Syntaxe de HtmlHelper :`@Html.ActionLink("Go to Product's Home Page", "Index")`</span><span class="sxs-lookup"><span data-stu-id="d839b-159">HtmlHelper syntax: `@Html.ActionLink("Go to Product's Home Page", "Index")`</span></span>

  <span data-ttu-id="d839b-160">Syntaxe de TagHelper :`<a asp-action="Index">Go to Product's Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="d839b-160">TagHelper syntax: `<a asp-action="Index">Go to Product's Home Page</a>`</span></span>

  <span data-ttu-id="d839b-161">Remarquez que nous ne devons pas fournir les valeurs « zone » et « controller » ici car ils sont déjà disponibles dans le contexte de la requête actuelle.</span><span class="sxs-lookup"><span data-stu-id="d839b-161">Note that we need not supply the 'area' and 'controller' values here as they are already available in the context of the current request.</span></span> <span data-ttu-id="d839b-162">Ces types de valeurs sont appelées `ambient` valeurs.</span><span class="sxs-lookup"><span data-stu-id="d839b-162">These kind of values are called `ambient` values.</span></span>

* <span data-ttu-id="d839b-163">Générer des liens à partir d’une action dans une zone en fonction de contrôleur à une autre action sur un autre contrôleur.</span><span class="sxs-lookup"><span data-stu-id="d839b-163">Generating links from an action within an area based controller to another action on a different controller</span></span>

  <span data-ttu-id="d839b-164">Supposons que le chemin d’accès de la requête actuelle est similaire à`/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="d839b-164">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="d839b-165">Syntaxe de HtmlHelper :`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Manage")`</span><span class="sxs-lookup"><span data-stu-id="d839b-165">HtmlHelper syntax: `@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Manage")`</span></span>

  <span data-ttu-id="d839b-166">Syntaxe de TagHelper :`<a asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="d839b-166">TagHelper syntax: `<a asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`</span></span>

  <span data-ttu-id="d839b-167">Notez qu’ici la valeur ambiante d’une « zone » est utilisée, mais la valeur « controller » est spécifiée explicitement ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="d839b-167">Note that here the ambient value of an 'area' is used but the 'controller' value is specified explicitly above.</span></span>

* <span data-ttu-id="d839b-168">Générer des liens à partir d’une action dans une zone en fonction contrôleur à une autre action sur un autre contrôleur et une autre zone.</span><span class="sxs-lookup"><span data-stu-id="d839b-168">Generating links from an action within an area based controller to another action on a different controller and a different area.</span></span>

  <span data-ttu-id="d839b-169">Supposons que le chemin d’accès de la requête actuelle est similaire à`/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="d839b-169">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="d839b-170">Syntaxe de HtmlHelper :`@Html.ActionLink("Go to Services’ Home Page", "Index", "Home", new { area = "Services" })`</span><span class="sxs-lookup"><span data-stu-id="d839b-170">HtmlHelper syntax: `@Html.ActionLink("Go to Services’ Home Page", "Index", "Home", new { area = "Services" })`</span></span>

  <span data-ttu-id="d839b-171">Syntaxe de TagHelper :`<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services’ Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="d839b-171">TagHelper syntax: `<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services’ Home Page</a>`</span></span>

  <span data-ttu-id="d839b-172">Notez qu’ici sans valeurs ambiantes sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="d839b-172">Note that here no ambient values are used.</span></span>

* <span data-ttu-id="d839b-173">Générer des liens à partir d’une action au sein d’un contrôleur de zone à une autre action sur un autre contrôleur et **pas** dans une zone.</span><span class="sxs-lookup"><span data-stu-id="d839b-173">Generating links from an action within an area based controller to another action on a different controller and **not** in an area.</span></span>

  <span data-ttu-id="d839b-174">Syntaxe de HtmlHelper :`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Home", new { area = "" })`</span><span class="sxs-lookup"><span data-stu-id="d839b-174">HtmlHelper syntax: `@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Home", new { area = "" })`</span></span>

  <span data-ttu-id="d839b-175">Syntaxe de TagHelper :`<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="d839b-175">TagHelper syntax: `<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`</span></span>

  <span data-ttu-id="d839b-176">Étant donné que nous souhaitons générer des liens vers une zone non action basée sur contrôleur, nous vide la valeur ambiante de « zone ».</span><span class="sxs-lookup"><span data-stu-id="d839b-176">Since we want to generate links to a non-area based controller action, we empty the ambient value for 'area' here.</span></span>

## <a name="publishing-areas"></a><span data-ttu-id="d839b-177">Domaines de publication</span><span class="sxs-lookup"><span data-stu-id="d839b-177">Publishing Areas</span></span>

<span data-ttu-id="d839b-178">Tous les `*.cshtml` et `wwwroot/**` de sortie lorsque les fichiers sont publiés `<Project Sdk="Microsoft.NET.Sdk.Web">` est inclus dans le *.csproj* fichier.</span><span class="sxs-lookup"><span data-stu-id="d839b-178">All `*.cshtml` and `wwwroot/**` files are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the *.csproj* file.</span></span>

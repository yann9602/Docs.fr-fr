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
# <a name="areas"></a>Zones

Par [Dhananjay Kumar](https://twitter.com/debug_mode) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Les zones sont une fonctionnalité d’ASP.NET MVC permet de classer les fonctionnalités connexes dans un groupe comme un espace de noms distinct (pour le routage) et la structure de dossiers (pour les vues). L’utilisation de zones crée une hiérarchie à des fins de routage en ajoutant un autre paramètre d’itinéraire, `area`à `controller` et `action`.

Les zones permettent de partitionner une application Web ASP.NET Core MVC volumineuse en regroupements fonctionnels plus petits. Une zone est en réalité une structure MVC à l’intérieur d’une application. Dans un projet MVC, les composants logiques tels que le modèle, le contrôleur et vue sont conservées dans des dossiers différents et MVC utilise les conventions d’affectation de noms pour créer la relation entre ces composants. Pour une application volumineuse, il peut être avantageux de partition de l’application en différents domaines de niveau élevés de fonctionnalités. Par exemple, une application de commerce électronique avec plusieurs entités, telles que l’extraction, de facturation et de recherche, etc.. Chacune de ces unités ont leurs propres vues des composants logiques, les contrôleurs et les modèles. Dans ce scénario, vous pouvez utiliser des zones physiquement partitionner les composants d’entreprise dans le même projet.

Une zone peut être définie comme la plus petite des unités fonctionnelles dans un projet ASP.NET MVC de base avec son propre ensemble de modèles, des vues et des contrôleurs.

Envisagez d’utiliser des zones dans un MVC projet lorsque :

* Votre application est constituée de plusieurs composants fonctionnels de haut niveau qui doivent être logiquement séparés

* Vous souhaitez partitionner votre projet MVC afin que chaque zone fonctionnelle permettre être utilisé indépendamment

Fonctionnalités de la zone :

* Une application ASP.NET MVC de base peut avoir n’importe quel nombre de zones

* Chaque zone comporte des contrôleurs, des modèles et des vues

* Vous permet d’organiser des grands projets MVC en plusieurs composants de haut niveau qui peuvent être utilisés indépendamment

* Prend en charge plusieurs contrôleurs portant le même nom - tant qu’ils disposent différents *zones*

Examinons un exemple pour illustrer la façon dont les zones sont créées et utilisées. Supposons que vous avez une application de magasin qui dispose de deux regroupements distincts de contrôleurs et vues : produits et Services. Un dossier standard structure pour que l’utilisation de zones MVC se présente comme ci-dessous :

* Nom du projet

  * Zones

    * Produits

      * Contrôleurs

        * HomeController.cs

        * ManageController.cs

      * Affichages

        * Accueil

          * Index.cshtml

        * Gérer

          * Index.cshtml

    * Services

      * Contrôleurs

        * HomeController.cs

      * Affichages

        * Accueil

          * Index.cshtml

Quand il tente MVC restituer une vue dans une zone, par défaut, il essaie de chercher dans les emplacements suivants :

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

Ce sont les emplacements par défaut qui peuvent être modifiés via le `AreaViewLocationFormats` sur la `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`.

Par exemple, dans le code au lieu d’avoir le nom du dossier sous forme de « Zones », ci-dessous, il a été modifié pour « Catégories ».

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

Une chose à noter est que la structure de la *vues* dossier est le seul qui est considérée comme important ici et le contenu du reste des dossiers comme *contrôleurs* et *demodèles* est **pas** a d’importance. Par exemple, vous ne devez pas avoir un *contrôleurs* et *modèles* dossier du tout. Cela fonctionne parce que le contenu de *contrôleurs* et *modèles* est simplement du code qui est compilé dans un fichier .dll, alors que le contenu de la *vues* jusqu'à une demande qui n’est pas vue a été effectuée.

Une fois que vous avez défini l’arborescence des dossiers, vous devez indiquer MVC que chaque contrôleur est associé à une zone. Pour ce faire, la décoration de nom du contrôleur avec le `[Area]` attribut.

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

Définir une définition d’itinéraire qui fonctionne avec vos zones nouvellement créés. Le [le routage pour les Actions de contrôleur](routing.md) article détaille comment créer des définitions de route, y compris à l’aide d’itinéraires classiques et les itinéraires d’attribut. Dans cet exemple, nous allons utiliser un itinéraire classique. Pour ce faire, ouvrez le *Startup.cs* de fichier et le modifier en ajoutant la `areaRoute` nommé définition d’itinéraire ci-dessous.

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

Accédant à `http://<yourApp>/products`, le `Index` méthode d’action de la `HomeController` dans le `Products` zone sera appelée.

## <a name="link-generation"></a>Génération du lien

* Générer des liens à partir d’une action dans une zone en fonction contrôleur à une autre action au sein du même contrôleur.

  Supposons que le chemin d’accès de la requête actuelle est similaire à`/Products/Home/Create`

  Syntaxe de HtmlHelper :`@Html.ActionLink("Go to Product's Home Page", "Index")`

  Syntaxe de TagHelper :`<a asp-action="Index">Go to Product's Home Page</a>`

  Remarquez que nous ne devons pas fournir les valeurs « zone » et « controller » ici car ils sont déjà disponibles dans le contexte de la requête actuelle. Ces types de valeurs sont appelées `ambient` valeurs.

* Générer des liens à partir d’une action dans une zone en fonction de contrôleur à une autre action sur un autre contrôleur.

  Supposons que le chemin d’accès de la requête actuelle est similaire à`/Products/Home/Create`

  Syntaxe de HtmlHelper :`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Manage")`

  Syntaxe de TagHelper :`<a asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`

  Notez qu’ici la valeur ambiante d’une « zone » est utilisée, mais la valeur « controller » est spécifiée explicitement ci-dessus.

* Générer des liens à partir d’une action dans une zone en fonction contrôleur à une autre action sur un autre contrôleur et une autre zone.

  Supposons que le chemin d’accès de la requête actuelle est similaire à`/Products/Home/Create`

  Syntaxe de HtmlHelper :`@Html.ActionLink("Go to Services’ Home Page", "Index", "Home", new { area = "Services" })`

  Syntaxe de TagHelper :`<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services’ Home Page</a>`

  Notez qu’ici sans valeurs ambiantes sont utilisées.

* Générer des liens à partir d’une action au sein d’un contrôleur de zone à une autre action sur un autre contrôleur et **pas** dans une zone.

  Syntaxe de HtmlHelper :`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Home", new { area = "" })`

  Syntaxe de TagHelper :`<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`

  Étant donné que nous souhaitons générer des liens vers une zone non action basée sur contrôleur, nous vide la valeur ambiante de « zone ».

## <a name="publishing-areas"></a>Domaines de publication

Tous les `*.cshtml` et `wwwroot/**` de sortie lorsque les fichiers sont publiés `<Project Sdk="Microsoft.NET.Sdk.Web">` est inclus dans le *.csproj* fichier.

---
title: Disposition
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 29f12d1f-9734-48bd-bf1a-cee53a8ab700
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/layout
ms.openlocfilehash: 25aa5fc730d9076fdcf9d29cb5d9dfa75a246a1a
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="layout"></a><span data-ttu-id="04777-103">Disposition</span><span class="sxs-lookup"><span data-stu-id="04777-103">Layout</span></span>

<span data-ttu-id="04777-104">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="04777-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="04777-105">Vues partagent fréquemment des éléments visuels et par programmation.</span><span class="sxs-lookup"><span data-stu-id="04777-105">Views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="04777-106">Dans cet article, vous allez apprendre à utiliser des dispositions courantes, partager des directives et exécuter le code commun avant le rendu des vues dans votre application ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="04777-106">In this article, you'll learn how to use common layouts, share directives, and run common code before rendering views in your ASP.NET app.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="04777-107">Qu’est une mise en page</span><span class="sxs-lookup"><span data-stu-id="04777-107">What is a Layout</span></span>

<span data-ttu-id="04777-108">La plupart des applications web ont une disposition courante qui offre une expérience cohérente de l’utilisateur lorsqu’ils naviguent de page en page.</span><span class="sxs-lookup"><span data-stu-id="04777-108">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="04777-109">En général, la mise en page inclut des éléments d’interface utilisateur courantes telles que l’en-tête de l’application, la navigation ou les éléments de menu et un pied de page.</span><span class="sxs-lookup"><span data-stu-id="04777-109">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![Exemple de mise en page](layout/_static/page-layout.png)

<span data-ttu-id="04777-111">Structures HTML communes telles que des scripts et des feuilles de style sont fréquemment utilisés par nombre de pages au sein d’une application.</span><span class="sxs-lookup"><span data-stu-id="04777-111">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="04777-112">Tous ces éléments partagés peuvent être définis dans un *disposition* fichier, qui peut ensuite être référencé par n’importe quelle vue utilisée dans l’application.</span><span class="sxs-lookup"><span data-stu-id="04777-112">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="04777-113">Dispositions réduisent code en double dans les vues, de les aider à suivre le [ne répétez vous-même (sec) principe](http://deviq.com/don-t-repeat-yourself/).</span><span class="sxs-lookup"><span data-stu-id="04777-113">Layouts reduce duplicate code in views, helping them follow the [Don't Repeat Yourself (DRY) principle](http://deviq.com/don-t-repeat-yourself/).</span></span>

<span data-ttu-id="04777-114">Par convention, la disposition par défaut pour une application ASP.NET est nommée `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="04777-114">By convention, the default layout for an ASP.NET app is named `_Layout.cshtml`.</span></span> <span data-ttu-id="04777-115">Le modèle de projet Visual Studio ASP.NET Core MVC inclut ce fichier de mise en page dans le `Views/Shared` dossier :</span><span class="sxs-lookup"><span data-stu-id="04777-115">The Visual Studio ASP.NET Core MVC project template includes this layout file in the `Views/Shared` folder:</span></span>

![dossier d’affichages dans l’Explorateur de solutions](layout/_static/web-project-views.png)

<span data-ttu-id="04777-117">Cette disposition définit un modèle de niveau supérieur pour les vues dans l’application.</span><span class="sxs-lookup"><span data-stu-id="04777-117">This layout defines a top level template for views in the app.</span></span> <span data-ttu-id="04777-118">Les applications ne nécessitent pas une mise en page et applications peuvent définir plusieurs dispositions, avec des vues différentes en spécifiant des dispositions différentes.</span><span class="sxs-lookup"><span data-stu-id="04777-118">Apps do not require a layout, and apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="04777-119">Un exemple `_Layout.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="04777-119">An example `_Layout.cshtml`:</span></span>

<span data-ttu-id="04777-120">[!code-html[Main](../../common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=42,66)]</span><span class="sxs-lookup"><span data-stu-id="04777-120">[!code-html[Main](../../common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=42,66)]</span></span>

## <a name="specifying-a-layout"></a><span data-ttu-id="04777-121">Spécification d’une disposition</span><span class="sxs-lookup"><span data-stu-id="04777-121">Specifying a Layout</span></span>

<span data-ttu-id="04777-122">Les vues Razor ont un `Layout` propriété.</span><span class="sxs-lookup"><span data-stu-id="04777-122">Razor views have a `Layout` property.</span></span> <span data-ttu-id="04777-123">Des vues spécifient une disposition en définissant cette propriété :</span><span class="sxs-lookup"><span data-stu-id="04777-123">Individual views specify a layout by setting this property:</span></span>

<span data-ttu-id="04777-124">[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]</span><span class="sxs-lookup"><span data-stu-id="04777-124">[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]</span></span>

<span data-ttu-id="04777-125">La mise en page spécifiée peut utiliser un chemin d’accès complet (exemple : `/Views/Shared/_Layout.cshtml`) ou un nom partiel (exemple : `_Layout`).</span><span class="sxs-lookup"><span data-stu-id="04777-125">The layout specified can use a full path (example: `/Views/Shared/_Layout.cshtml`) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="04777-126">Lorsqu’un nom partiel est fourni, le moteur d’affichage Razor recherchera le fichier de disposition à l’aide de son processus de découverte standard.</span><span class="sxs-lookup"><span data-stu-id="04777-126">When a partial name is provided, the Razor view engine will search for the layout file using its standard discovery process.</span></span> <span data-ttu-id="04777-127">Le dossier associé au contrôleur de la recherche est effectuée en premier lieu, suivi par le `Shared` dossier.</span><span class="sxs-lookup"><span data-stu-id="04777-127">The controller-associated folder is searched first, followed by the `Shared` folder.</span></span> <span data-ttu-id="04777-128">Ce processus de découverte est identique à celui utilisé pour découvrir les [vues partielles](partial.md).</span><span class="sxs-lookup"><span data-stu-id="04777-128">This discovery process is identical to the one used to discover [partial views](partial.md).</span></span>

<span data-ttu-id="04777-129">Par défaut, chaque disposition doit appeler `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="04777-129">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="04777-130">Chaque fois que l’appel à `RenderBody` est placé, le contenu de la vue est restitué.</span><span class="sxs-lookup"><span data-stu-id="04777-130">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name=layout-sections-label></a>

### <a name="sections"></a><span data-ttu-id="04777-131">Sections</span><span class="sxs-lookup"><span data-stu-id="04777-131">Sections</span></span>

<span data-ttu-id="04777-132">Une disposition peut éventuellement faire référence à un ou plusieurs *sections*, en appelant `RenderSection`.</span><span class="sxs-lookup"><span data-stu-id="04777-132">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="04777-133">Sections fournissent un moyen d’organiser où certains éléments de la page doivent être placés.</span><span class="sxs-lookup"><span data-stu-id="04777-133">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="04777-134">Chaque appel à `RenderSection` peut spécifier si cette section est obligatoire ou facultatif.</span><span class="sxs-lookup"><span data-stu-id="04777-134">Each call to `RenderSection` can specify whether that section is required or optional.</span></span> <span data-ttu-id="04777-135">Si une section requise n’est trouvée, une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="04777-135">If a required section is not found, an exception will be thrown.</span></span> <span data-ttu-id="04777-136">Des vues spécifient le contenu à restituer dans une section à l’aide de la `@section` la syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="04777-136">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="04777-137">Si une vue définit une section, il doit être rendu (ou une erreur se produit).</span><span class="sxs-lookup"><span data-stu-id="04777-137">If a view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="04777-138">Un exemple `@section` définition dans une vue :</span><span class="sxs-lookup"><span data-stu-id="04777-138">An example `@section` definition in a view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
   }
   ```

<span data-ttu-id="04777-139">Dans le code ci-dessus, les scripts de validation sont ajoutées à la `scripts` section sur une vue qui inclut un formulaire.</span><span class="sxs-lookup"><span data-stu-id="04777-139">In the code above, validation scripts are added to the `scripts` section on a view that includes a form.</span></span> <span data-ttu-id="04777-140">Autres vues dans la même application ne nécessitent pas d’autres scripts et donc n’aurez pas besoin de définir une section de scripts.</span><span class="sxs-lookup"><span data-stu-id="04777-140">Other views in the same application might not require any additional scripts, and so wouldn't need to define a scripts section.</span></span>

<span data-ttu-id="04777-141">Les sections définies dans une vue sont disponibles uniquement dans sa page de présentation immédiate.</span><span class="sxs-lookup"><span data-stu-id="04777-141">Sections defined in a view are available only in its immediate layout page.</span></span> <span data-ttu-id="04777-142">Ils ne peuvent pas être référencés à partir d’autres parties de la vue système, affichage des composants ou aucun.</span><span class="sxs-lookup"><span data-stu-id="04777-142">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="04777-143">Ignorer des sections</span><span class="sxs-lookup"><span data-stu-id="04777-143">Ignoring sections</span></span>

<span data-ttu-id="04777-144">Par défaut, le corps et toutes les sections dans une page de contenu doivent tous être rendues par la page de disposition.</span><span class="sxs-lookup"><span data-stu-id="04777-144">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="04777-145">Le moteur d’affichage Razor applique ceci en effectuant le suivi si le corps et chaque section ont été rendues.</span><span class="sxs-lookup"><span data-stu-id="04777-145">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="04777-146">Pour indiquer au moteur de vue d’ignorer le corps ou des sections, appelez le `IgnoreBody` et `IgnoreSection` méthodes.</span><span class="sxs-lookup"><span data-stu-id="04777-146">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="04777-147">Le corps et chaque section dans une page Razor doivent être rendus soit ignorés.</span><span class="sxs-lookup"><span data-stu-id="04777-147">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name=viewimports></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="04777-148">L’importation des Directives partagés</span><span class="sxs-lookup"><span data-stu-id="04777-148">Importing Shared Directives</span></span>

<span data-ttu-id="04777-149">Vues peuvent utiliser des directives de Razor pour effectuer diverses opérations, telles que l’importation d’espaces de noms ou d’effectuer [injection de dépendance](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="04777-149">Views can use Razor directives to do many things, such as importing namespaces or performing [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="04777-150">Directives partagés par plusieurs vues peuvent être spécifiés dans un commun `_ViewImports.cshtml` fichier.</span><span class="sxs-lookup"><span data-stu-id="04777-150">Directives shared by many views may be specified in a common `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="04777-151">Le `_ViewImports` fichier prend en charge les directives suivantes :</span><span class="sxs-lookup"><span data-stu-id="04777-151">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`

* `@removeTagHelper`

* `@tagHelperPrefix`

* `@using`

* `@model`

* `@inherits`

* `@inject`

<span data-ttu-id="04777-152">Le fichier ne prend pas en charge les autres fonctionnalités Razor, telles que les fonctions et les définitions de la section.</span><span class="sxs-lookup"><span data-stu-id="04777-152">The file does not support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="04777-153">Un exemple `_ViewImports.cshtml` fichier :</span><span class="sxs-lookup"><span data-stu-id="04777-153">A sample `_ViewImports.cshtml` file:</span></span>

<span data-ttu-id="04777-154">[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="04777-154">[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]</span></span>

<span data-ttu-id="04777-155">Le `_ViewImports.cshtml` de fichiers pour une application ASP.NET MVC de base est généralement placée dans le `Views` dossier.</span><span class="sxs-lookup"><span data-stu-id="04777-155">The `_ViewImports.cshtml` file for an ASP.NET Core MVC app is typically placed in the `Views` folder.</span></span> <span data-ttu-id="04777-156">A `_ViewImports.cshtml` fichier peut être placé dans un dossier dans lequel cas seront uniquement appliquée aux vues dans ce dossier et ses sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="04777-156">A `_ViewImports.cshtml` file can be placed within any folder, in which case it will only be applied to views within that folder and its subfolders.</span></span> <span data-ttu-id="04777-157">`_ViewImports`les fichiers sont traités en commençant au niveau racine, et ensuite pour chaque dossier jusqu'à l’emplacement de la vue proprement dite, donc les paramètres spécifiés au niveau racine peut être remplacée au niveau du dossier.</span><span class="sxs-lookup"><span data-stu-id="04777-157">`_ViewImports` files are processed starting at the root level, and then for each folder leading up to the location of the view itself, so settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="04777-158">Par exemple, si un niveau racine `_ViewImports.cshtml` fichier spécifie `@model` et `@addTagHelper`et un autre `_ViewImports.cshtml` fichier dans le dossier associé au contrôleur de la vue spécifie une autre `@model` et ajoute un autre `@addTagHelper`, la vue aura accès à ces deux programmes d’assistance de balise et utilisera ce dernier `@model`.</span><span class="sxs-lookup"><span data-stu-id="04777-158">For example, if a root level `_ViewImports.cshtml` file specifies `@model` and `@addTagHelper`, and another `_ViewImports.cshtml` file in the controller-associated folder of the view specifies a different `@model` and adds another `@addTagHelper`, the view will have access to both tag helpers and will use the latter `@model`.</span></span>

<span data-ttu-id="04777-159">Si plusieurs `_ViewImports.cshtml` fichiers sont exécutées pour une vue, combinées de comportement des directives inclus dans le `ViewImports.cshtml` fichiers seront comme suit :</span><span class="sxs-lookup"><span data-stu-id="04777-159">If multiple `_ViewImports.cshtml` files are run for a view, combined behavior of the directives included in the `ViewImports.cshtml` files will be as follows:</span></span>

* <span data-ttu-id="04777-160">`@addTagHelper`, `@removeTagHelper`: tous s’exécuter dans l’ordre</span><span class="sxs-lookup"><span data-stu-id="04777-160">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>

* <span data-ttu-id="04777-161">`@tagHelperPrefix`: celui le plus proche à la vue remplace les autres noms</span><span class="sxs-lookup"><span data-stu-id="04777-161">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>

* <span data-ttu-id="04777-162">`@model`: celui le plus proche à la vue remplace les autres noms</span><span class="sxs-lookup"><span data-stu-id="04777-162">`@model`: the closest one to the view overrides any others</span></span>

* <span data-ttu-id="04777-163">`@inherits`: celui le plus proche à la vue remplace les autres noms</span><span class="sxs-lookup"><span data-stu-id="04777-163">`@inherits`: the closest one to the view overrides any others</span></span>

* <span data-ttu-id="04777-164">`@using`: toutes les sont incluses ; les doublons sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="04777-164">`@using`: all are included; duplicates are ignored</span></span>

* <span data-ttu-id="04777-165">`@inject`: pour chaque propriété, celui le plus proche à la vue remplace les autres noms portant le même nom de propriété</span><span class="sxs-lookup"><span data-stu-id="04777-165">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name=viewstart></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="04777-166">Exécution du Code avant chaque vue.</span><span class="sxs-lookup"><span data-stu-id="04777-166">Running Code Before Each View</span></span>

<span data-ttu-id="04777-167">Si vous avez un code que vous devez exécuter avant chaque vue, cela doit être placé dans le `_ViewStart.cshtml` fichier.</span><span class="sxs-lookup"><span data-stu-id="04777-167">If you have code you need to run before every view, this should be placed in the `_ViewStart.cshtml` file.</span></span> <span data-ttu-id="04777-168">Par convention, le `_ViewStart.cshtml` fichier se trouve dans le `Views` dossier.</span><span class="sxs-lookup"><span data-stu-id="04777-168">By convention, the `_ViewStart.cshtml` file is located in the `Views` folder.</span></span> <span data-ttu-id="04777-169">Les instructions figurant dans `_ViewStart.cshtml` sont exécutés avant chaque vue complète (pas de dispositions et les vues partielles pas).</span><span class="sxs-lookup"><span data-stu-id="04777-169">The statements listed in `_ViewStart.cshtml` are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="04777-170">Comme [ViewImports.cshtml](xref:mvc/views/layout#viewimports), `_ViewStart.cshtml` est hiérarchique.</span><span class="sxs-lookup"><span data-stu-id="04777-170">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), `_ViewStart.cshtml` is hierarchical.</span></span> <span data-ttu-id="04777-171">Si un `_ViewStart.cshtml` fichier est défini dans le dossier de la vue associée au contrôleur, il sera exécuté après celui défini dans la racine de la `Views` dossier (le cas échéant).</span><span class="sxs-lookup"><span data-stu-id="04777-171">If a `_ViewStart.cshtml` file is defined in the controller-associated view folder, it will be run after the one defined in the root of the `Views` folder (if any).</span></span>

<span data-ttu-id="04777-172">Un exemple `_ViewStart.cshtml` fichier :</span><span class="sxs-lookup"><span data-stu-id="04777-172">A sample `_ViewStart.cshtml` file:</span></span>

<span data-ttu-id="04777-173">[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="04777-173">[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]</span></span>

<span data-ttu-id="04777-174">Le fichier ci-dessus spécifie que toutes les vues utilisent le `_Layout.cshtml` mise en page.</span><span class="sxs-lookup"><span data-stu-id="04777-174">The file above specifies that all views will use the `_Layout.cshtml` layout.</span></span>

> [!NOTE]
> <span data-ttu-id="04777-175">Ni `_ViewStart.cshtml` ni `_ViewImports.cshtml` sont généralement placés dans le `/Views/Shared` dossier.</span><span class="sxs-lookup"><span data-stu-id="04777-175">Neither `_ViewStart.cshtml` nor `_ViewImports.cshtml` are typically placed in the `/Views/Shared` folder.</span></span> <span data-ttu-id="04777-176">Les versions de niveau de l’application de ces fichiers doivent être placées directement dans le `/Views` dossier.</span><span class="sxs-lookup"><span data-stu-id="04777-176">The app-level versions of these files should be placed directly in the `/Views` folder.</span></span>

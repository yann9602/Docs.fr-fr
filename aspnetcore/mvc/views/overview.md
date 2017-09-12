---
title: "Vue d’ensemble de vues"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 668c320d-c050-45e3-8161-2f460dc93b2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
ms.openlocfilehash: 3b33c13f2385d3b07ba9b6f0bc0fd560abc3735c
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="rendering-html-with-views-in-aspnet-core-mvc"></a><span data-ttu-id="2bd92-103">Rendu HTML avec des vues dans ASP.NET MVC de base</span><span class="sxs-lookup"><span data-stu-id="2bd92-103">Rendering HTML with views in ASP.NET Core MVC</span></span>

<span data-ttu-id="2bd92-104">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2bd92-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2bd92-105">Les contrôleurs ASP.NET MVC de base peuvent retourner des résultats mis en forme à l’aide de *vues*.</span><span class="sxs-lookup"><span data-stu-id="2bd92-105">ASP.NET Core MVC controllers can return formatted results using *views*.</span></span>

## <a name="what-are-views"></a><span data-ttu-id="2bd92-106">Que sont les vues ?</span><span class="sxs-lookup"><span data-stu-id="2bd92-106">What are Views?</span></span>

<span data-ttu-id="2bd92-107">Dans le modèle Model-View-Controller (MVC), le *vue* encapsule les détails de présentation de l’interaction utilisateur avec l’application.</span><span class="sxs-lookup"><span data-stu-id="2bd92-107">In the Model-View-Controller (MVC) pattern, the *view* encapsulates the presentation details of the user's interaction with the app.</span></span> <span data-ttu-id="2bd92-108">Les vues sont des modèles HTML contenant du code incorporé qui génèrent du contenu à envoyer au client.</span><span class="sxs-lookup"><span data-stu-id="2bd92-108">Views are HTML templates with embedded code that generate content to send to the client.</span></span> <span data-ttu-id="2bd92-109">Vues utilisent [la syntaxe Razor](razor.md), ce qui permet d’interagir avec du code HTML avec un minimum de code ou de cérémonie de code.</span><span class="sxs-lookup"><span data-stu-id="2bd92-109">Views use [Razor syntax](razor.md), which allows code to interact with HTML with minimal code or ceremony.</span></span>

<span data-ttu-id="2bd92-110">Les vues ASP.NET MVC de base sont *.cshtml* fichiers stockés par défaut dans un *vues* dossier au sein de l’application.</span><span class="sxs-lookup"><span data-stu-id="2bd92-110">ASP.NET Core MVC views are *.cshtml* files stored by default in a *Views* folder within the application.</span></span> <span data-ttu-id="2bd92-111">En règle générale, chaque contrôleur aura son propre dossier, dans lequel sont des vues pour les actions de contrôleur spécifique.</span><span class="sxs-lookup"><span data-stu-id="2bd92-111">Typically, each controller will have its own folder, in which are views for specific controller actions.</span></span>

![Dossier d’affichages dans l’Explorateur de solutions](overview/_static/views_solution_explorer.png)

<span data-ttu-id="2bd92-113">En plus des vues spécifiques à certaines actions, [vues partielles](partial.md), [mises en page et autres fichiers de vue spéciale](layout.md) peut être utilisé pour aider à réduire la répétition et permettre la réutilisation dans les affichages de l’application.</span><span class="sxs-lookup"><span data-stu-id="2bd92-113">In addition to action-specific views, [partial views](partial.md), [layouts, and other special view files](layout.md) can be used to help reduce repetition and allow for reuse within the app's views.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="2bd92-114">Avantages de l’utilisation de vues</span><span class="sxs-lookup"><span data-stu-id="2bd92-114">Benefits of Using Views</span></span>

<span data-ttu-id="2bd92-115">Les vues fournissent [séparation des préoccupations](http://deviq.com/separation-of-concerns/) au sein d’une application MVC, encapsulant la balise de niveau interface utilisateur séparément à partir de la logique métier.</span><span class="sxs-lookup"><span data-stu-id="2bd92-115">Views provide [separation of concerns](http://deviq.com/separation-of-concerns/) within an MVC app, encapsulating user interface level markup separately from business logic.</span></span> <span data-ttu-id="2bd92-116">ASP.NET MVC vues utilisent [la syntaxe Razor](razor.md) pour basculer HTML balisage et le serveur logique simple.</span><span class="sxs-lookup"><span data-stu-id="2bd92-116">ASP.NET MVC views use [Razor syntax](razor.md) to make switching between HTML markup and server side logic painless.</span></span> <span data-ttu-id="2bd92-117">Des aspects communs, répétitives d’interface utilisateur de l’application peuvent être facilement réutilisées entre les vues à l’aide de [mise en page et les directives partagés](layout.md) ou [vues partielles](partial.md).</span><span class="sxs-lookup"><span data-stu-id="2bd92-117">Common, repetitive aspects of the app's user interface can easily be reused between views using [layout and shared directives](layout.md) or [partial views](partial.md).</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="2bd92-118">Création d’une vue</span><span class="sxs-lookup"><span data-stu-id="2bd92-118">Creating a View</span></span>

<span data-ttu-id="2bd92-119">Les vues qui sont spécifiques à un contrôleur sont créées dans le *vues / [nom du contrôleur]* dossier.</span><span class="sxs-lookup"><span data-stu-id="2bd92-119">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="2bd92-120">Les vues qui sont partagées entre les contrôleurs sont placées dans le */vues/Shared* dossier.</span><span class="sxs-lookup"><span data-stu-id="2bd92-120">Views that are shared among controllers are placed in the */Views/Shared* folder.</span></span> <span data-ttu-id="2bd92-121">Nommer le fichier de vue de la même que son action de contrôleur associé et ajouter la *.cshtml* extension de fichier.</span><span class="sxs-lookup"><span data-stu-id="2bd92-121">Name the view file the same as its associated controller action, and add the *.cshtml* file extension.</span></span> <span data-ttu-id="2bd92-122">Par exemple, pour créer un affichage pour le *sur* action sur le *accueil* contrôleur, vous devez créer le *About.cshtml* de fichiers dans le  * /vues/Home*dossier.</span><span class="sxs-lookup"><span data-stu-id="2bd92-122">For example, to create a view for the *About* action on the *Home* controller, you would create the *About.cshtml* file in the */Views/Home* folder.</span></span>

<span data-ttu-id="2bd92-123">Un exemple de fichier de vue (*About.cshtml*) :</span><span class="sxs-lookup"><span data-stu-id="2bd92-123">A sample view file (*About.cshtml*):</span></span>

<span data-ttu-id="2bd92-124">[!code-html[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="2bd92-124">[!code-html[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]</span></span>

<span data-ttu-id="2bd92-125">*Razor* code est indiqué par le `@` symbole.</span><span class="sxs-lookup"><span data-stu-id="2bd92-125">*Razor* code is denoted by the `@` symbol.</span></span> <span data-ttu-id="2bd92-126">Instructions c# sont exécutées au sein de Razor blocs de code définir à off par des accolades (`{` `}`), par exemple l’affectation de « About » pour le `ViewData["Title"]` élément indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="2bd92-126">C# statements are run within Razor code blocks set off by curly braces (`{` `}`), such as the assignment of "About" to the `ViewData["Title"]` element shown above.</span></span> <span data-ttu-id="2bd92-127">Razor peut être utilisé pour afficher les valeurs dans le code HTML en référençant simplement la valeur avec le `@` symbol, comme indiqué dans le `<h2>` et `<h3>` éléments ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="2bd92-127">Razor can be used to display values within HTML by simply referencing the value with the `@` symbol, as shown within the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="2bd92-128">Cette vue se concentre sur la partie seulement de la sortie pour laquelle il est chargé.</span><span class="sxs-lookup"><span data-stu-id="2bd92-128">This view focuses on just the portion of the output for which it is responsible.</span></span> <span data-ttu-id="2bd92-129">Le reste de la mise en page et d’autres aspects courantes de la vue, spécifiées ailleurs.</span><span class="sxs-lookup"><span data-stu-id="2bd92-129">The rest of the page's layout, and other common aspects of the view, are specified elsewhere.</span></span> <span data-ttu-id="2bd92-130">En savoir plus sur [mise en page et la logique d’affichage partagé](layout.md).</span><span class="sxs-lookup"><span data-stu-id="2bd92-130">Learn more about [layout and shared view logic](layout.md).</span></span>

## <a name="how-do-controllers-specify-views"></a><span data-ttu-id="2bd92-131">Comment les contrôleurs de la spécification des affichages ?</span><span class="sxs-lookup"><span data-stu-id="2bd92-131">How do Controllers Specify Views?</span></span>

<span data-ttu-id="2bd92-132">Les vues sont généralement retournées à partir des actions comme un `ViewResult`.</span><span class="sxs-lookup"><span data-stu-id="2bd92-132">Views are typically returned from actions as a `ViewResult`.</span></span> <span data-ttu-id="2bd92-133">Votre méthode d’action peut créer et renvoyer un `ViewResult` directement, mais plus souvent si votre contrôleur hérite `Controller`, vous allez simplement utiliser le `View` méthode d’assistance, que cet exemple montre :</span><span class="sxs-lookup"><span data-stu-id="2bd92-133">Your action method can create and return a `ViewResult` directly, but more commonly if your controller inherits from `Controller`, you'll simply use the `View` helper method, as this example demonstrates:</span></span>

<span data-ttu-id="2bd92-134">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="2bd92-134">*HomeController.cs*</span></span>

<span data-ttu-id="2bd92-135">[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]</span><span class="sxs-lookup"><span data-stu-id="2bd92-135">[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]</span></span>

<span data-ttu-id="2bd92-136">Le `View` méthode d’assistance a plusieurs surcharges pour faciliter le retour des vues pour les développeurs d’applications.</span><span class="sxs-lookup"><span data-stu-id="2bd92-136">The `View` helper method has several overloads to make returning views easier for app developers.</span></span> <span data-ttu-id="2bd92-137">Vous pouvez éventuellement spécifier une vue pour retourner, ainsi que d’un objet de modèle à passer à la vue.</span><span class="sxs-lookup"><span data-stu-id="2bd92-137">You can optionally specify a view to return, as well as a model object to pass to the view.</span></span>

<span data-ttu-id="2bd92-138">Lorsque cette action est retournée, la *About.cshtml* indiqué ci-dessus est restituée :</span><span class="sxs-lookup"><span data-stu-id="2bd92-138">When this action returns, the *About.cshtml* view shown above is rendered:</span></span>

![Sur la page](overview/_static/about-page.png)

### <a name="view-discovery"></a><span data-ttu-id="2bd92-140">Détection de la vue</span><span class="sxs-lookup"><span data-stu-id="2bd92-140">View Discovery</span></span>

<span data-ttu-id="2bd92-141">Lorsqu’une action retourne une vue, un processus appelé *détection de la vue* a lieu.</span><span class="sxs-lookup"><span data-stu-id="2bd92-141">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="2bd92-142">Ce processus détermine l’afficher le fichier sera utilisé.</span><span class="sxs-lookup"><span data-stu-id="2bd92-142">This process determines which view file will be used.</span></span> <span data-ttu-id="2bd92-143">Sauf si un fichier de vue spécifiques est spécifié, le runtime recherche une vue spécifique du contrôleur, puis recherche d’abord nom correspondant de la vue dans le *Shared* dossier.</span><span class="sxs-lookup"><span data-stu-id="2bd92-143">Unless a specific view file is specified, the runtime looks for a controller-specific view first, then looks for matching view name in the *Shared* folder.</span></span>

<span data-ttu-id="2bd92-144">Lorsqu’une action retourne le `View` (méthode), par exemple `return View();`, le nom de l’action est utilisé en tant que le nom de la vue.</span><span class="sxs-lookup"><span data-stu-id="2bd92-144">When an action returns the `View` method, like so `return View();`, the action name is used as the view name.</span></span> <span data-ttu-id="2bd92-145">Par exemple, si cela était appelé à partir d’une méthode d’action nommée « Index », il serait équivalent à passer un nom d’affichage de « Index ».</span><span class="sxs-lookup"><span data-stu-id="2bd92-145">For example, if this were called from an action method named "Index", it would be equivalent to passing in a view name of "Index".</span></span> <span data-ttu-id="2bd92-146">Un nom de la vue peut être explicitement transmis à la méthode (`return View("SomeView");`).</span><span class="sxs-lookup"><span data-stu-id="2bd92-146">A view name can be explicitly passed to the method (`return View("SomeView");`).</span></span> <span data-ttu-id="2bd92-147">Dans ces deux cas, afficher les recherches de découverte pour un fichier de vue correspondant dans :</span><span class="sxs-lookup"><span data-stu-id="2bd92-147">In both of these cases, view discovery searches for a matching view file in:</span></span>

   1. <span data-ttu-id="2bd92-148">Vues /\<ControllerName > /\<ViewName > .cshtml</span><span class="sxs-lookup"><span data-stu-id="2bd92-148">Views/\<ControllerName>/\<ViewName>.cshtml</span></span>

   2. <span data-ttu-id="2bd92-149">Les vues ouShared/\<ViewName > .cshtml</span><span class="sxs-lookup"><span data-stu-id="2bd92-149">Views/Shared/\<ViewName>.cshtml</span></span>

>[!TIP]
> <span data-ttu-id="2bd92-150">Nous vous recommandons d’appliquer la convention de simplement retourner `View()` à partir d’actions lorsque cela est possible, qu’elle provoque plus flexible et plus facile à refactoriser le code.</span><span class="sxs-lookup"><span data-stu-id="2bd92-150">We recommend following the convention of simply returning `View()` from actions when possible, as it results in more flexible, easier to refactor code.</span></span>

<span data-ttu-id="2bd92-151">Un chemin d’accès du fichier de vue peut être fourni au lieu d’un nom de la vue.</span><span class="sxs-lookup"><span data-stu-id="2bd92-151">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="2bd92-152">Si vous utilisez un chemin d’accès absolu, en commençant à la racine de l’application (éventuellement en commençant par « / » ou « ~ / »), le *.cshtml* extension doit être spécifiée en tant que partie du chemin du fichier (par exemple, `return View("Views/Home/About.cshtml");`).</span><span class="sxs-lookup"><span data-stu-id="2bd92-152">If using an absolute path starting at the application root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified as part of the file path (for example, `return View("Views/Home/About.cshtml");`).</span></span> <span data-ttu-id="2bd92-153">Vous pouvez également utiliser un chemin d’accès relatif à partir du répertoire spécifique du contrôleur dans le *vues* active pour spécifier les vues dans des répertoires différents (par exemple, `return View("../Manage/Index");` à l’intérieur de la `HomeController`).</span><span class="sxs-lookup"><span data-stu-id="2bd92-153">Alternatively, you can use a relative path from the controller-specific directory within the *Views* directory to specify views in different directories (for example, `return View("../Manage/Index");` inside the `HomeController`).</span></span> <span data-ttu-id="2bd92-154">De même, vous pouvez parcourir le répertoire spécifique du contrôleur actuel (par exemple, `return View("./About");`).</span><span class="sxs-lookup"><span data-stu-id="2bd92-154">Similarly, you can traverse the current controller-specific directory (for example, `return View("./About");`).</span></span> <span data-ttu-id="2bd92-155">Notez que les chemins d’accès relatifs ne pas utiliser le *.cshtml* extension.</span><span class="sxs-lookup"><span data-stu-id="2bd92-155">Note that relative paths don't use the *.cshtml* extension.</span></span> <span data-ttu-id="2bd92-156">Comme mentionné précédemment, suivez la meilleure pratique d’organiser la structure de fichiers pour les vues afin de refléter les relations entre les contrôleurs, les actions et les vues à la facilité de maintenance et de clarté.</span><span class="sxs-lookup"><span data-stu-id="2bd92-156">As previously mentioned, follow the best practice of organizing the file structure for views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

> [!NOTE]
> <span data-ttu-id="2bd92-157">[Les vues partielles](partial.md) et [affichage des composants](view-components.md) utilisent des mécanismes de découverte similaires (mais non identiques).</span><span class="sxs-lookup"><span data-stu-id="2bd92-157">[Partial views](partial.md) and [view components](view-components.md) use similar (but not identical) discovery mechanisms.</span></span>

> [!NOTE]
> <span data-ttu-id="2bd92-158">Vous pouvez personnaliser la convention par défaut concernant où vues se trouvent dans l’application à l’aide d’un `IViewLocationExpander`.</span><span class="sxs-lookup"><span data-stu-id="2bd92-158">You can customize the default convention regarding where views are located within the app by using a custom `IViewLocationExpander`.</span></span>

>[!TIP]
> <span data-ttu-id="2bd92-159">Les noms de vue peuvent être respecte la casse selon le système de fichiers sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="2bd92-159">View names may be case sensitive depending on the underlying file system.</span></span> <span data-ttu-id="2bd92-160">Pour la compatibilité entre les systèmes d’exploitation, toujours respecter la casse entre le contrôleur et les noms d’actions associé à afficher les dossiers et les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="2bd92-160">For compatibility across operating systems, always match case between controller and action names and associated view folders and filenames.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="2bd92-161">Passage de données à des vues</span><span class="sxs-lookup"><span data-stu-id="2bd92-161">Passing Data to Views</span></span>

<span data-ttu-id="2bd92-162">Vous pouvez passer des données pour les vues à l’aide de plusieurs mécanismes.</span><span class="sxs-lookup"><span data-stu-id="2bd92-162">You can pass data to views using several mechanisms.</span></span> <span data-ttu-id="2bd92-163">L’approche la plus fiable consiste à spécifier un *modèle* type dans la vue (communément appelé une *viewmodel*, pour différencier des types de modèle de domaine métier), puis passez une instance de ce type à la vue à partir de l’action.</span><span class="sxs-lookup"><span data-stu-id="2bd92-163">The most robust approach is to specify a *model* type in the view (commonly referred to as a *viewmodel*, to distinguish it from business domain model types), and then pass an instance of this type to the view from the action.</span></span> <span data-ttu-id="2bd92-164">Nous vous recommandons de qu'utiliser un modèle ou un modèle d’affichage pour passer des données à une vue.</span><span class="sxs-lookup"><span data-stu-id="2bd92-164">We recommend you use a model or view model to pass data to a view.</span></span> <span data-ttu-id="2bd92-165">Ainsi, la vue tirer parti de la vérification de type fort.</span><span class="sxs-lookup"><span data-stu-id="2bd92-165">This allows the view to take advantage of strong type checking.</span></span> <span data-ttu-id="2bd92-166">Vous pouvez spécifier un modèle pour une vue à l’aide de la `@model` directive :</span><span class="sxs-lookup"><span data-stu-id="2bd92-166">You can specify a model for a view using the `@model` directive:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1]}} -->

```html
@model WebApplication1.ViewModels.Address
   <h2>Contact</h2>
   <address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

<span data-ttu-id="2bd92-167">Une fois qu’un modèle a été spécifié pour une vue, l’instance envoyée à la vue est accessible dans une manière fortement typée à l’aide `@Model` comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="2bd92-167">Once a model has been specified for a view, the instance sent to the view can be accessed in a strongly-typed manner using `@Model` as shown above.</span></span> <span data-ttu-id="2bd92-168">Pour fournir une instance du type de modèle à la vue, le contrôleur transmet en tant que paramètre :</span><span class="sxs-lookup"><span data-stu-id="2bd92-168">To provide an instance of the model type to the view, the controller passes it as a parameter:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [13]}} -->

```csharp
public IActionResult Contact()
   {
       ViewData["Message"] = "Your contact page.";

       var viewModel = new Address()
       {
           Name = "Microsoft",
           Street = "One Microsoft Way",
           City = "Redmond",
           State = "WA",
           PostalCode = "98052-6399"
       };
       return View(viewModel);
   }
   ```

<span data-ttu-id="2bd92-169">Il n’existe aucune restriction sur les types qui peuvent être fournis à une vue en tant que modèle.</span><span class="sxs-lookup"><span data-stu-id="2bd92-169">There are no restrictions on the types that can be provided to a view as a model.</span></span> <span data-ttu-id="2bd92-170">Nous vous recommandons de passage brut POCO Old CLR Object () afficher les modèles avec peu ou pas un comportement, afin que la logique métier peut être encapsulée ailleurs dans l’application.</span><span class="sxs-lookup"><span data-stu-id="2bd92-170">We recommend passing Plain Old CLR Object (POCO) view models with little or no behavior, so that business logic can be encapsulated elsewhere in the app.</span></span> <span data-ttu-id="2bd92-171">Un exemple de cette approche est la *adresse* viewmodel utilisé dans l’exemple ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="2bd92-171">An example of this approach is the *Address* viewmodel used in the example above:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [13]}} -->

```csharp
namespace WebApplication1.ViewModels
   {
       public class Address
       {
           public string Name { get; set; }
           public string Street { get; set; }
           public string City { get; set; }
           public string State { get; set; }
           public string PostalCode { get; set; }
       }
   }
   ```

> [!NOTE]
> <span data-ttu-id="2bd92-172">Rien ne vous empêche d’utiliser les mêmes classes que vos types de modèle d’entreprise et de vos types de modèle d’affichage.</span><span class="sxs-lookup"><span data-stu-id="2bd92-172">Nothing prevents you from using the same classes as your business model types and your display model types.</span></span> <span data-ttu-id="2bd92-173">Toutefois, les conservant à part permet à vos vues faire varier indépendamment à partir de votre modèle de domaine ou la persistance et peut offrir certains avantages de sécurité ainsi (pour les modèles que les utilisateurs envoient à l’application à l’aide de [liaison de modèle](../models/model-binding.md)).</span><span class="sxs-lookup"><span data-stu-id="2bd92-173">However, keeping them separate allows your views to vary independently from your domain or persistence model, and can offer some security benefits as well (for models that users will send to the app using [model binding](../models/model-binding.md)).</span></span>

### <a name="loosely-typed-data"></a><span data-ttu-id="2bd92-174">Données faiblement typées</span><span class="sxs-lookup"><span data-stu-id="2bd92-174">Loosely Typed Data</span></span>

<span data-ttu-id="2bd92-175">Outre les vues fortement typées, toutes les vues ont accès à une collection faiblement typée de données.</span><span class="sxs-lookup"><span data-stu-id="2bd92-175">In addition to strongly typed views, all views have access to a loosely typed collection of data.</span></span> <span data-ttu-id="2bd92-176">Cette même collection peut être référencée par le biais du `ViewData` ou `ViewBag` propriétés sur les contrôleurs et vues.</span><span class="sxs-lookup"><span data-stu-id="2bd92-176">This same collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="2bd92-177">Le `ViewBag` propriété est un wrapper autour de `ViewData` qui fournit une vue dynamique sur cette collection.</span><span class="sxs-lookup"><span data-stu-id="2bd92-177">The `ViewBag` property is a wrapper around `ViewData` that provides a dynamic view over that collection.</span></span> <span data-ttu-id="2bd92-178">Il n’est pas une collection distincte.</span><span class="sxs-lookup"><span data-stu-id="2bd92-178">It is not a separate collection.</span></span>

<span data-ttu-id="2bd92-179">`ViewData`est un objet de dictionnaire accessible via `string` clés.</span><span class="sxs-lookup"><span data-stu-id="2bd92-179">`ViewData` is a dictionary object accessed through `string` keys.</span></span> <span data-ttu-id="2bd92-180">Vous pouvez stocker et récupérer des objets qu’il contient, et vous devez les convertir en un type spécifique lorsque vous extrayez les.</span><span class="sxs-lookup"><span data-stu-id="2bd92-180">You can store and retrieve objects in it, and you'll need to cast them to a specific type when you extract them.</span></span> <span data-ttu-id="2bd92-181">Vous pouvez utiliser `ViewData` pour passer des données à partir d’un contrôleur à des vues, ainsi que dans les vues (et les vues partielles et mises en page).</span><span class="sxs-lookup"><span data-stu-id="2bd92-181">You can use `ViewData` to pass data from a controller to views, as well as within views (and partial views and layouts).</span></span> <span data-ttu-id="2bd92-182">Données de chaîne peuvent stockées et utilisées directement, sans recourir à un cast.</span><span class="sxs-lookup"><span data-stu-id="2bd92-182">String data can be stored and used directly, without the need for a cast.</span></span>

<span data-ttu-id="2bd92-183">Définir des valeurs pour `ViewData` dans une action :</span><span class="sxs-lookup"><span data-stu-id="2bd92-183">Set some values for `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
   {
       ViewData["Greeting"] = "Hello";
       ViewData["Address"]  = new Address()
       {
           Name = "Steve",
           Street = "123 Main St",
           City = "Hudson",
           State = "OH",
           PostalCode = "44236"
       };

       return View();
   }
   ```

<span data-ttu-id="2bd92-184">Utiliser les données dans une vue :</span><span class="sxs-lookup"><span data-stu-id="2bd92-184">Work with the data in a view:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3, 6]}} -->

```html
@{
       // Requires cast
       var address = ViewData["Address"] as Address;
   }

   @ViewData["Greeting"] World!

   <address>
       @address.Name<br />
       @address.Street<br />
       @address.City, @address.State @address.PostalCode
   </address>
   ```

<span data-ttu-id="2bd92-185">Le `ViewBag` objets fournit un accès dynamique pour les objets stockés dans `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="2bd92-185">The `ViewBag` objects provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="2bd92-186">Cela peut être plus pratique d’utiliser, car il ne nécessite un transtypage.</span><span class="sxs-lookup"><span data-stu-id="2bd92-186">This can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="2bd92-187">Le même exemple que ci-dessus, à l’aide de `ViewBag` au lieu de fortement typé `address` instance dans la vue :</span><span class="sxs-lookup"><span data-stu-id="2bd92-187">The same example as above, using `ViewBag` instead of a strongly typed `address` instance in the view:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1, 4, 5, 6]}} -->

```html
@ViewBag.Greeting World!

   <address>
       @ViewBag.Address.Name<br />
       @ViewBag.Address.Street<br />
       @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
   </address>
   ```

> [!NOTE]
> <span data-ttu-id="2bd92-188">Étant donné que les deux font référence au même sous-jacent `ViewData` collection, vous pouvez mélanger et correspondent entre `ViewData` et `ViewBag` lors de la lecture et l’écriture de valeurs, si le fait de.</span><span class="sxs-lookup"><span data-stu-id="2bd92-188">Since both refer to the same underlying `ViewData` collection, you can mix and match between `ViewData` and `ViewBag` when reading and writing values, if convenient.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="2bd92-189">Vues dynamiques</span><span class="sxs-lookup"><span data-stu-id="2bd92-189">Dynamic Views</span></span>

<span data-ttu-id="2bd92-190">Les vues qui ne déclarent pas un type de modèle, mais ont une instance de modèle leur passée peuvent référencer cette instance dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="2bd92-190">Views that do not declare a model type but have a model instance passed to them can reference this instance dynamically.</span></span> <span data-ttu-id="2bd92-191">Par exemple, si une instance de `Address` est passé à une vue qui ne déclare pas une `@model`, la vue sera toujours en mesure de faire référence aux propriétés de l’instance dynamiquement, comme indiqué :</span><span class="sxs-lookup"><span data-stu-id="2bd92-191">For example, if an instance of `Address` is passed to a view that doesn't declare an `@model`, the view would still be able to refer to the instance's properties dynamically as shown:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [13, 16, 17, 18]}} -->

```html
<address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

<span data-ttu-id="2bd92-192">Cette fonctionnalité peut offrir une certaine souplesse, mais n’offre aucune protection de compilation IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="2bd92-192">This feature can offer some flexibility, but does not offer any compilation protection or IntelliSense.</span></span> <span data-ttu-id="2bd92-193">Si la propriété n’existe pas, la page échouera lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="2bd92-193">If the property doesn't exist, the page will fail at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="2bd92-194">D’autres fonctionnalités d’affichage</span><span class="sxs-lookup"><span data-stu-id="2bd92-194">More View Features</span></span>

<span data-ttu-id="2bd92-195">[Programmes d’assistance de balise](tag-helpers/intro.md) permet d’ajouter un comportement côté serveur à des balises HTML existantes, ce qui évite la nécessité d’utiliser du code personnalisé ou des programmes d’assistance dans les affichages.</span><span class="sxs-lookup"><span data-stu-id="2bd92-195">[Tag helpers](tag-helpers/intro.md) make it easy to add server-side behavior to existing HTML tags, avoiding the need to use custom code or helpers within views.</span></span> <span data-ttu-id="2bd92-196">Programmes d’assistance de balise sont appliqués aux éléments HTML, qui sont ignorés par les éditeurs qui ne sont pas familiarisés avec eux, ce qui permet de balisage de la vue d’être modifié et rendus dans une variété d’outils en tant qu’attributs.</span><span class="sxs-lookup"><span data-stu-id="2bd92-196">Tag helpers are applied as attributes to HTML elements, which are ignored by editors that aren't familiar with them, allowing view markup to be edited and rendered in a variety of tools.</span></span> <span data-ttu-id="2bd92-197">Programmes d’assistance de balise ont de nombreuses utilisations et en particulier peuvent [utilisation des formulaires](working-with-forms.md) beaucoup plus facile.</span><span class="sxs-lookup"><span data-stu-id="2bd92-197">Tag helpers have many uses, and in particular can make [working with forms](working-with-forms.md) much easier.</span></span>

<span data-ttu-id="2bd92-198">Génération d’un balisage HTML personnalisé peut être obtenue avec nombreux programmes d’assistance de HTML intégrés, et une logique plus complexe de l’interface utilisateur (avec éventuellement ses propres exigences de données) peut être encapsulée dans [affichage des composants](view-components.md).</span><span class="sxs-lookup"><span data-stu-id="2bd92-198">Generating custom HTML markup can be achieved with many built-in HTML Helpers, and more complex UI logic (potentially with its own data requirements) can be encapsulated in [View Components](view-components.md).</span></span> <span data-ttu-id="2bd92-199">Affichage des composants fournissent la même séparation des préoccupations qui offrent des contrôleurs et vues et peuvent éliminer la nécessité pour les actions et les vues afin de traiter les données utilisées par les éléments d’interface utilisateur communes.</span><span class="sxs-lookup"><span data-stu-id="2bd92-199">View components provide the same separation of concerns that controllers and views offer, and can eliminate the need for actions and views to deal with data used by common UI elements.</span></span>

<span data-ttu-id="2bd92-200">Comme de nombreux aspects d’ASP.NET Core, vues prennent en charge [injection de dépendance](../../fundamentals/dependency-injection.md), autorisant les services soient [injectées dans les vues](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="2bd92-200">Like many other aspects of ASP.NET Core, views support [dependency injection](../../fundamentals/dependency-injection.md), allowing services to be [injected into views](dependency-injection.md).</span></span>

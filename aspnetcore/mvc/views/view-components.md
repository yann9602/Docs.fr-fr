---
title: Affichage des composants
author: rick-anderson
description: "Affichage des composants visent n’importe où que la logique de rendu réutilisables."
keywords: ASP.NET Core, affichage des composants, vue partielle
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: ab4705b7-59d7-4f31-bc97-ea7f292fe926
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-components
ms.openlocfilehash: 07a2aca731b8017450a1b0da00ddef25306c122e
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="view-components"></a><span data-ttu-id="f4a42-104">Affichage des composants</span><span class="sxs-lookup"><span data-stu-id="f4a42-104">View components</span></span>

<span data-ttu-id="f4a42-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f4a42-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[<span data-ttu-id="f4a42-106">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="f4a42-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample)

## <a name="introducing-view-components"></a><span data-ttu-id="f4a42-107">Présentation des composants de la vue</span><span class="sxs-lookup"><span data-stu-id="f4a42-107">Introducing view components</span></span>

<span data-ttu-id="f4a42-108">Nouveau vers ASP.NET MVC de base, affichage des composants sont similaires aux vues partielles, mais elles sont beaucoup plus puissantes.</span><span class="sxs-lookup"><span data-stu-id="f4a42-108">New to ASP.NET Core MVC, view components are similar to partial views, but they are much more powerful.</span></span> <span data-ttu-id="f4a42-109">Affichage des composants n’utiliser la liaison de modèle et dépendre uniquement les données que vous fournissez lors de l’appel dans celui-ci.</span><span class="sxs-lookup"><span data-stu-id="f4a42-109">View components don’t use model binding, and only depend on the data you provide when calling into it.</span></span> <span data-ttu-id="f4a42-110">Un composant de vue :</span><span class="sxs-lookup"><span data-stu-id="f4a42-110">A view component:</span></span>

* <span data-ttu-id="f4a42-111">Restitue un segment au lieu d’une réponse complète</span><span class="sxs-lookup"><span data-stu-id="f4a42-111">Renders a chunk rather than a whole response</span></span>
* <span data-ttu-id="f4a42-112">Inclut le mêmes séparation des intérêts et les avantages de testabilité trouvées entre un contrôleur et une vue</span><span class="sxs-lookup"><span data-stu-id="f4a42-112">Includes the same separation-of-concerns and testability benefits found between a controller and view</span></span>
* <span data-ttu-id="f4a42-113">Peut avoir des paramètres et la logique métier</span><span class="sxs-lookup"><span data-stu-id="f4a42-113">Can have parameters and business logic</span></span>
* <span data-ttu-id="f4a42-114">Est généralement appelé à partir d’une page de disposition</span><span class="sxs-lookup"><span data-stu-id="f4a42-114">Is typically invoked from a layout page</span></span>

<span data-ttu-id="f4a42-115">Affichage des composants sont destinées n’importe où que vous avez une logique de rendu réutilisables qui est trop complexe pour une vue partielle, telles que :</span><span class="sxs-lookup"><span data-stu-id="f4a42-115">View components are intended anywhere you have reusable rendering logic that is too complex for a partial view, such as:</span></span>

* <span data-ttu-id="f4a42-116">Menus de navigation dynamique</span><span class="sxs-lookup"><span data-stu-id="f4a42-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="f4a42-117">Nuage de balises (où elle interroge la base de données)</span><span class="sxs-lookup"><span data-stu-id="f4a42-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="f4a42-118">Panneau de configuration de connexion</span><span class="sxs-lookup"><span data-stu-id="f4a42-118">Login panel</span></span>
* <span data-ttu-id="f4a42-119">Panier d’achat</span><span class="sxs-lookup"><span data-stu-id="f4a42-119">Shopping cart</span></span>
* <span data-ttu-id="f4a42-120">Articles publiés récemment</span><span class="sxs-lookup"><span data-stu-id="f4a42-120">Recently published articles</span></span>
* <span data-ttu-id="f4a42-121">Contenu de la barre latérale sur un blog classique</span><span class="sxs-lookup"><span data-stu-id="f4a42-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="f4a42-122">Un panneau de connexion qui est rendu sur chaque page et affiche les liens pour déconnecter ou de journal, selon le journal dans l’état de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="f4a42-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="f4a42-123">Un composant de vue se compose de deux parties : la classe (généralement dérivé [ViewComponent](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponent)) et le résultat renvoie (en général, une vue).</span><span class="sxs-lookup"><span data-stu-id="f4a42-123">A view component consists of two parts: the class (typically derived from [ViewComponent](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="f4a42-124">Tels que les contrôleurs, un composant de vue peut être un POCO, mais il est que la plupart des développeurs tirer parti des méthodes et propriétés disponibles en dérivant de `ViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="f4a42-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="f4a42-125">Création d’un composant de vue</span><span class="sxs-lookup"><span data-stu-id="f4a42-125">Creating a view component</span></span>

<span data-ttu-id="f4a42-126">Cette section contient les exigences d’ordre général pour créer un composant de vue.</span><span class="sxs-lookup"><span data-stu-id="f4a42-126">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="f4a42-127">Plus loin dans l’article, nous examiner chaque étape en détail et créer un composant de vue.</span><span class="sxs-lookup"><span data-stu-id="f4a42-127">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="f4a42-128">La classe de composant de vue</span><span class="sxs-lookup"><span data-stu-id="f4a42-128">The view component class</span></span>

<span data-ttu-id="f4a42-129">Une classe de composant de vue peut être créée à l’aide d’une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="f4a42-129">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="f4a42-130">Dérivation de *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="f4a42-130">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="f4a42-131">Le fait de décorer une classe avec le `[ViewComponent]` attribut ou la dérivation d’une classe avec le `[ViewComponent]` attribut</span><span class="sxs-lookup"><span data-stu-id="f4a42-131">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="f4a42-132">Création d’une classe dans laquelle le nom se termine par le suffixe *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="f4a42-132">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="f4a42-133">Comme contrôleurs, affichage des composants doivent être des classes publiques, non imbriquée et non abstraite.</span><span class="sxs-lookup"><span data-stu-id="f4a42-133">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="f4a42-134">Le nom de composant de vue est le nom de classe avec le suffixe « ViewComponent » supprimé.</span><span class="sxs-lookup"><span data-stu-id="f4a42-134">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="f4a42-135">Il peut également être explicitement spécifié à l’aide de la `ViewComponentAttribute.Name` propriété.</span><span class="sxs-lookup"><span data-stu-id="f4a42-135">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="f4a42-136">Une classe de composant de vue :</span><span class="sxs-lookup"><span data-stu-id="f4a42-136">A view component class:</span></span>

* <span data-ttu-id="f4a42-137">Prend entièrement en charge le constructeur [injection de dépendance](../../fundamentals/dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="f4a42-137">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="f4a42-138">Ne font pas partie du cycle de vie du contrôleur, ce qui signifie que vous ne pouvez pas utiliser [filtres](../controllers/filters.md) dans un composant de vue</span><span class="sxs-lookup"><span data-stu-id="f4a42-138">Does not take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="f4a42-139">Afficher les méthodes de composant</span><span class="sxs-lookup"><span data-stu-id="f4a42-139">View component methods</span></span>

<span data-ttu-id="f4a42-140">Un composant de vue définit sa logique dans un `InvokeAsync` méthode qui retourne un `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="f4a42-140">A view component defines its logic in an `InvokeAsync` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="f4a42-141">Paramètres proviennent directement d’appel du composant de vue, pas à partir de la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="f4a42-141">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="f4a42-142">Un composant de vue gère jamais directement une requête.</span><span class="sxs-lookup"><span data-stu-id="f4a42-142">A view component never directly handles a request.</span></span> <span data-ttu-id="f4a42-143">En règle générale, un composant de vue Initialise un modèle et le passe à une vue en appelant le `View` (méthode).</span><span class="sxs-lookup"><span data-stu-id="f4a42-143">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="f4a42-144">En résumé, afficher les méthodes du composant :</span><span class="sxs-lookup"><span data-stu-id="f4a42-144">In summary, view component methods:</span></span>

* <span data-ttu-id="f4a42-145">Définir un `InvokeAsync` méthode qui retourne un`IViewComponentResult`</span><span class="sxs-lookup"><span data-stu-id="f4a42-145">Define an `InvokeAsync` method that returns an `IViewComponentResult`</span></span>
* <span data-ttu-id="f4a42-146">En général, initialise un modèle et le passe à une vue en appelant le `ViewComponent` `View` (méthode)</span><span class="sxs-lookup"><span data-stu-id="f4a42-146">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method</span></span>
* <span data-ttu-id="f4a42-147">Paramètres proviennent de la méthode d’appel, pas HTTP, il n’existe aucune liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="f4a42-147">Parameters come from the calling method, not HTTP, there is no model binding</span></span>
* <span data-ttu-id="f4a42-148">Sont n’est pas accessible directement en tant que point de terminaison HTTP, ils sont appelés à partir de votre code (généralement dans une vue).</span><span class="sxs-lookup"><span data-stu-id="f4a42-148">Are not reachable directly as an HTTP endpoint, they are invoked from your code (usually in a view).</span></span> <span data-ttu-id="f4a42-149">Un composant de vue gère jamais une demande</span><span class="sxs-lookup"><span data-stu-id="f4a42-149">A view component never handles a request</span></span>
* <span data-ttu-id="f4a42-150">Sont surchargées sur la signature, plutôt que des détails dans la requête HTTP actuelle</span><span class="sxs-lookup"><span data-stu-id="f4a42-150">Are overloaded on the signature rather than any details from the current HTTP request</span></span>

### <a name="view-search-path"></a><span data-ttu-id="f4a42-151">Chemin de recherche de vue</span><span class="sxs-lookup"><span data-stu-id="f4a42-151">View search path</span></span>

<span data-ttu-id="f4a42-152">Le runtime recherche dans la vue dans les chemins d’accès suivants :</span><span class="sxs-lookup"><span data-stu-id="f4a42-152">The runtime searches for the view in the following paths:</span></span>

   * <span data-ttu-id="f4a42-153">Vues /\<controller_name > /Components/\<view_component_name > /\<view_name ></span><span class="sxs-lookup"><span data-stu-id="f4a42-153">Views/\<controller_name>/Components/\<view_component_name>/\<view_name></span></span>
   * <span data-ttu-id="f4a42-154">Vues/Shared/Components/\<view_component_name > /\<view_name ></span><span class="sxs-lookup"><span data-stu-id="f4a42-154">Views/Shared/Components/\<view_component_name>/\<view_name></span></span>

<span data-ttu-id="f4a42-155">Le nom de la vue par défaut pour un composant de vue est *par défaut*, ce qui signifie que votre fichier d’affichage est généralement appelé *Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f4a42-155">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="f4a42-156">Vous pouvez spécifier un nom d’affichage différent lors de la création du résultat de composant de vue ou lorsque vous appelez le `View` (méthode).</span><span class="sxs-lookup"><span data-stu-id="f4a42-156">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="f4a42-157">Nous vous recommandons de vous nommez le fichier de vue *Default.cshtml* et utiliser le *vues/Shared/Components/\<view_component_name > /\<view_name >* chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="f4a42-157">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/\<view_component_name>/\<view_name>* path.</span></span> <span data-ttu-id="f4a42-158">Le `PriorityList` composant de la vue utilisée dans cet exemple utilise *Views/Shared/Components/PriorityList/Default.cshtml* pour l’affichage du composant.</span><span class="sxs-lookup"><span data-stu-id="f4a42-158">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="f4a42-159">Appel d’un composant de vue</span><span class="sxs-lookup"><span data-stu-id="f4a42-159">Invoking a view component</span></span>

<span data-ttu-id="f4a42-160">Pour utiliser le composant d’affichage, appelez ce qui suit à l’intérieur d’une vue :</span><span class="sxs-lookup"><span data-stu-id="f4a42-160">To use the view component, call the following inside a view:</span></span>

```html
@Component.InvokeAsync("Name of view component", <anonymous type containing parameters>)
```

<span data-ttu-id="f4a42-161">Les paramètres sont transmis à la `InvokeAsync` (méthode).</span><span class="sxs-lookup"><span data-stu-id="f4a42-161">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="f4a42-162">Le `PriorityList` développé dans l’article de composant de vue est appelée à partir de la *Views/Todo/Index.cshtml* afficher le fichier.</span><span class="sxs-lookup"><span data-stu-id="f4a42-162">The `PriorityList` view component developed in the article is invoked from the *Views/Todo/Index.cshtml* view file.</span></span> <span data-ttu-id="f4a42-163">Dans l’exemple suivant, la `InvokeAsync` méthode est appelée avec deux paramètres :</span><span class="sxs-lookup"><span data-stu-id="f4a42-163">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

<span data-ttu-id="f4a42-164">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-164">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span></span>

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="f4a42-165">Appel d’un composant de vue comme une application d’assistance de balise</span><span class="sxs-lookup"><span data-stu-id="f4a42-165">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="f4a42-166">Pour ASP.NET Core 1.1 et versions ultérieures, vous pouvez appeler un composant de la vue comme une [application d’assistance de balise](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="f4a42-166">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

<span data-ttu-id="f4a42-167">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-167">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]</span></span>

<span data-ttu-id="f4a42-168">Paramètres de classe et méthode casse Pascal pour les programmes d’assistance de balise sont traduites en leur [réduire les cas rapide](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="f4a42-168">Pascal-cased class and method parameters for Tag Helpers are translated into their [lower kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="f4a42-169">L’application d’assistance de balise pour appeler un composant de vue utilise le `<vc></vc>` élément.</span><span class="sxs-lookup"><span data-stu-id="f4a42-169">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="f4a42-170">Le composant d’affichage est spécifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="f4a42-170">The view component is specified as follows:</span></span>

```html
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="f4a42-171">Remarque : Pour pouvoir utiliser un composant de vue comme une application d’assistance de balise, vous devez inscrire l’assembly contenant le composant de vue à l’aide de la `@addTagHelper` la directive.</span><span class="sxs-lookup"><span data-stu-id="f4a42-171">Note: In order to use a View Component as a Tag Helper, you must register the assembly containing the View Component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="f4a42-172">Par exemple, si votre composant de la vue se trouve dans un assembly appelé « MyWebApp », ajoutez la directive suivante à la `_ViewImports.cshtml` fichier :</span><span class="sxs-lookup"><span data-stu-id="f4a42-172">For example, if your View Component is in an assembly called "MyWebApp", add the following directive to the `_ViewImports.cshtml` file:</span></span>

```csharp
@addTagHelper *, MyWebApp
```

<span data-ttu-id="f4a42-173">Vous pouvez inscrire un composant de vue comme une application d’assistance de balise à n’importe quel fichier qui fait référence au composant de la vue.</span><span class="sxs-lookup"><span data-stu-id="f4a42-173">You can register a View Component as a Tag Helper to any file that references the View Component.</span></span> <span data-ttu-id="f4a42-174">Consultez [la gestion de balise d’assistance étendue](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) pour plus d’informations sur l’inscription des programmes d’assistance de balise.</span><span class="sxs-lookup"><span data-stu-id="f4a42-174">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="f4a42-175">Le `InvokeAsync` méthode utilisée dans ce didacticiel :</span><span class="sxs-lookup"><span data-stu-id="f4a42-175">The `InvokeAsync` method used in this tutorial:</span></span>

<span data-ttu-id="f4a42-176">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-176">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span></span>

<span data-ttu-id="f4a42-177">Dans le balisage de l’application d’assistance de balise :</span><span class="sxs-lookup"><span data-stu-id="f4a42-177">In Tag Helper markup:</span></span>

<span data-ttu-id="f4a42-178">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-178">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]</span></span>

<span data-ttu-id="f4a42-179">Dans l’exemple ci-dessus, le `PriorityList` composant de vue devient `priority-list`.</span><span class="sxs-lookup"><span data-stu-id="f4a42-179">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="f4a42-180">Les paramètres pour le composant de vue sont passés en tant qu’attributs en minuscules rapide.</span><span class="sxs-lookup"><span data-stu-id="f4a42-180">The parameters to the view component are passed as attributes in lower kebab case.</span></span>

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="f4a42-181">Appel d’un composant de vue directement à partir d’un contrôleur</span><span class="sxs-lookup"><span data-stu-id="f4a42-181">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="f4a42-182">Affichage des composants sont généralement appelées à partir d’une vue, mais vous pouvez les appeler directement à partir d’une méthode de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f4a42-182">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="f4a42-183">Lors de l’affichage des composants ne définissent pas de points de terminaison tels que les contrôleurs, vous pouvez facilement implémenter une action du contrôleur qui retourne le contenu d’un `ViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="f4a42-183">While view components do not define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="f4a42-184">Dans cet exemple, le composant de vue est appelé directement à partir du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="f4a42-184">In this example, the view component is called directly from the controller:</span></span>

<span data-ttu-id="f4a42-185">[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-185">[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]</span></span>

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="f4a42-186">Procédure pas à pas : Création d’un composant de la vue simple</span><span class="sxs-lookup"><span data-stu-id="f4a42-186">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="f4a42-187">[Télécharger](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), générer et tester le code de démarrage.</span><span class="sxs-lookup"><span data-stu-id="f4a42-187">[Download](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="f4a42-188">Il s’agit d’un projet simple avec un `Todo` contrôleur qui affiche une liste de *Todo* éléments.</span><span class="sxs-lookup"><span data-stu-id="f4a42-188">It's a simple project with a `Todo` controller that displays a list of *Todo* items.</span></span>

![Liste des ToDos](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="f4a42-190">Ajoutez une classe ViewComponent</span><span class="sxs-lookup"><span data-stu-id="f4a42-190">Add a ViewComponent class</span></span>

<span data-ttu-id="f4a42-191">Créer un *ViewComponents* dossier et ajoutez le code suivant `PriorityListViewComponent` classe :</span><span class="sxs-lookup"><span data-stu-id="f4a42-191">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

<span data-ttu-id="f4a42-192">[!code-csharp[Main](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-192">[!code-csharp[Main](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]</span></span>

<span data-ttu-id="f4a42-193">Remarques sur le code :</span><span class="sxs-lookup"><span data-stu-id="f4a42-193">Notes on the code:</span></span>

* <span data-ttu-id="f4a42-194">Classes de composant de vue peuvent être contenus dans **tout** dossier dans le projet.</span><span class="sxs-lookup"><span data-stu-id="f4a42-194">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="f4a42-195">Étant donné que le nom de classe PriorityList**ViewComponent** se termine par le suffixe **ViewComponent**, le runtime utilise la chaîne « PriorityList » lors du référencement du composant de la classe à partir d’une vue.</span><span class="sxs-lookup"><span data-stu-id="f4a42-195">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="f4a42-196">Je vais expliquer que plus en détail ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="f4a42-196">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="f4a42-197">Le `[ViewComponent]` attribut peut modifier le nom utilisé pour faire référence à un composant de vue.</span><span class="sxs-lookup"><span data-stu-id="f4a42-197">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="f4a42-198">Par exemple, nous pourrions avoir nommé la classe `XYZ` et appliqué les `ViewComponent` attribut :</span><span class="sxs-lookup"><span data-stu-id="f4a42-198">For example, we could have named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="f4a42-199">Le `[ViewComponent]` attribut ci-dessus indique le sélecteur de composants de vue d’utiliser le nom `PriorityList` lors de la recherche pour les vues associées au composant et à utiliser la chaîne « PriorityList » lors du référencement du composant de la classe à partir d’une vue.</span><span class="sxs-lookup"><span data-stu-id="f4a42-199">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="f4a42-200">Je vais expliquer que plus en détail ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="f4a42-200">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="f4a42-201">Le composant utilise [injection de dépendance](../../fundamentals/dependency-injection.md) afin de libérer le contexte de données.</span><span class="sxs-lookup"><span data-stu-id="f4a42-201">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="f4a42-202">`InvokeAsync`expose une méthode qui peut être appelée à partir d’une vue et peut prendre un nombre arbitraire d’arguments.</span><span class="sxs-lookup"><span data-stu-id="f4a42-202">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="f4a42-203">Le `InvokeAsync` méthode retourne le jeu de `ToDo` éléments qui répondent à la `isDone` et `maxPriority` paramètres.</span><span class="sxs-lookup"><span data-stu-id="f4a42-203">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="f4a42-204">Créer la vue Razor vue du composant</span><span class="sxs-lookup"><span data-stu-id="f4a42-204">Create the view component Razor view</span></span>

* <span data-ttu-id="f4a42-205">Créer le *composants/Shared/vues* dossier.</span><span class="sxs-lookup"><span data-stu-id="f4a42-205">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="f4a42-206">Ce dossier **doit** nommé *composants*.</span><span class="sxs-lookup"><span data-stu-id="f4a42-206">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="f4a42-207">Créer le *vues/Shared/composants/PriorityList* dossier.</span><span class="sxs-lookup"><span data-stu-id="f4a42-207">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="f4a42-208">Ce nom de dossier doit correspondre à celui de la classe de composant de vue, ou le nom de la classe moins le suffixe (si nous avons suivi convention et utilisé la *ViewComponent* suffixe dans le nom de classe).</span><span class="sxs-lookup"><span data-stu-id="f4a42-208">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="f4a42-209">Si vous avez utilisé le `ViewComponent` attribut, le nom de classe doit correspondre à la désignation de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="f4a42-209">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="f4a42-210">Créer un *Views/Shared/Components/PriorityList/Default.cshtml* vue Razor : [!code-html [principal](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-210">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view: [!code-html[Main](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]</span></span>
    
   <span data-ttu-id="f4a42-211">La vue Razor utilise une liste de `TodoItem` et les affiche.</span><span class="sxs-lookup"><span data-stu-id="f4a42-211">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="f4a42-212">Si le composant de vue `InvokeAsync` méthode ne passe pas le nom de la vue (comme dans notre exemple), *par défaut* est utilisé pour le nom de la vue par convention.</span><span class="sxs-lookup"><span data-stu-id="f4a42-212">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="f4a42-213">Plus loin dans ce didacticiel, je vous montrerai comment passer le nom de la vue.</span><span class="sxs-lookup"><span data-stu-id="f4a42-213">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="f4a42-214">Pour remplacer le style par défaut pour un contrôleur spécifique, ajoutez une vue dans le dossier d’affichage propres au contrôleur (par exemple *Views/Todo/Components/PriorityList/Default.cshtml)*.</span><span class="sxs-lookup"><span data-stu-id="f4a42-214">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/Todo/Components/PriorityList/Default.cshtml)*.</span></span>
    
    <span data-ttu-id="f4a42-215">Si le composant de la vue est spécifique du contrôleur, vous pouvez l’ajouter au dossier spécifique du contrôleur (*Views/Todo/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="f4a42-215">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/Todo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="f4a42-216">Ajouter un `div` contenant un appel au composant de liste de priorité vers le bas de la *Views/Todo/index.cshtml* fichier :</span><span class="sxs-lookup"><span data-stu-id="f4a42-216">Add a `div` containing a call to the priority list component to the bottom of the *Views/Todo/index.cshtml* file:</span></span>

    <span data-ttu-id="f4a42-217">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFirst.cshtml?range=34-38)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-217">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFirst.cshtml?range=34-38)]</span></span>

<span data-ttu-id="f4a42-218">Le balisage `@await Component.InvokeAsync` illustre la syntaxe d’appel de composants de la vue.</span><span class="sxs-lookup"><span data-stu-id="f4a42-218">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="f4a42-219">Le premier argument est le nom du composant que vous souhaitez appeler ou d’appeler.</span><span class="sxs-lookup"><span data-stu-id="f4a42-219">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="f4a42-220">Les paramètres suivants sont passés au composant.</span><span class="sxs-lookup"><span data-stu-id="f4a42-220">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="f4a42-221">`InvokeAsync`peut prendre un nombre arbitraire d’arguments.</span><span class="sxs-lookup"><span data-stu-id="f4a42-221">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="f4a42-222">Tester l’application.</span><span class="sxs-lookup"><span data-stu-id="f4a42-222">Test the app.</span></span> <span data-ttu-id="f4a42-223">L’illustration suivante montre la liste de tâches et les éléments de priorité :</span><span class="sxs-lookup"><span data-stu-id="f4a42-223">The following image shows the ToDo list and the priority items:</span></span>

![éléments de liste et la priorité de tâche](view-components/_static/pi.png)

<span data-ttu-id="f4a42-225">Vous pouvez également appeler le composant de vue directement à partir du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="f4a42-225">You can also call the view component directly from the controller:</span></span>

<span data-ttu-id="f4a42-226">[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-226">[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]</span></span>

![éléments de priorité à partir de l’action de IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="f4a42-228">En spécifiant un nom de vue</span><span class="sxs-lookup"><span data-stu-id="f4a42-228">Specifying a view name</span></span>

<span data-ttu-id="f4a42-229">Un composant de vue complexe devrez peut-être spécifier une vue par défaut dans certaines conditions.</span><span class="sxs-lookup"><span data-stu-id="f4a42-229">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="f4a42-230">Le code suivant montre comment spécifier la vue « PVC » à partir de la `InvokeAsync` (méthode).</span><span class="sxs-lookup"><span data-stu-id="f4a42-230">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="f4a42-231">Mise à jour la `InvokeAsync` méthode dans la `PriorityListViewComponent` classe.</span><span class="sxs-lookup"><span data-stu-id="f4a42-231">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

<span data-ttu-id="f4a42-232">[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-232">[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]</span></span>

<span data-ttu-id="f4a42-233">Copie le *Views/Shared/Components/PriorityList/Default.cshtml* fichier à une vue nommée *Views/Shared/Components/PriorityList/PVC.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f4a42-233">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="f4a42-234">Ajouter un titre pour indiquer que la vue PVC est utilisée.</span><span class="sxs-lookup"><span data-stu-id="f4a42-234">Add a heading to indicate the PVC view is being used.</span></span>

<span data-ttu-id="f4a42-235">[!code-html[Main](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-235">[!code-html[Main](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]</span></span>

<span data-ttu-id="f4a42-236">Mise à jour *Views/TodoList/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f4a42-236">Update *Views/TodoList/Index.cshtml*:</span></span>

<!-- Views/TodoList/Index.cshtml is never imported, so change to test tutorial -->

<span data-ttu-id="f4a42-237">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-237">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span></span>

<span data-ttu-id="f4a42-238">Exécutez l’application et vérifier l’affichage de PVC.</span><span class="sxs-lookup"><span data-stu-id="f4a42-238">Run the app and verify PVC view.</span></span>

![Composant de vue de priorité](view-components/_static/pvc.png)

<span data-ttu-id="f4a42-240">Si la vue PVC n’est pas affichée, vérifiez que vous appelez le composant de vue avec une priorité de 4 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="f4a42-240">If the PVC view is not rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="f4a42-241">Examinez le chemin d’accès de la vue</span><span class="sxs-lookup"><span data-stu-id="f4a42-241">Examine the view path</span></span>

* <span data-ttu-id="f4a42-242">Modifiez le paramètre de priorité inférieure ou égale à trois afin de la vue priority n’est pas renvoyée.</span><span class="sxs-lookup"><span data-stu-id="f4a42-242">Change the priority parameter to three or less so the priority view is not returned.</span></span>
* <span data-ttu-id="f4a42-243">Renommez temporairement le *Views/Todo/Components/PriorityList/Default.cshtml* à *1Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f4a42-243">Temporarily rename the *Views/Todo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="f4a42-244">Tester l’application, vous obtiendrez l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="f4a42-244">Test the app, you'll get the following error:</span></span>

   <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' was not found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="f4a42-245">Copie *Views/Todo/Components/PriorityList/1Default.cshtml* à *Views/Shared/Components/PriorityList/Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f4a42-245">Copy *Views/Todo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="f4a42-246">Ajouter des balises à la *Shared* provient de la vue du composant Todo vue pour indiquer la vue de la *Shared* dossier.</span><span class="sxs-lookup"><span data-stu-id="f4a42-246">Add some markup to the *Shared* Todo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="f4a42-247">Test du **Shared** vue du composant.</span><span class="sxs-lookup"><span data-stu-id="f4a42-247">Test the **Shared** component view.</span></span>

![Sortie de tâches avec la vue du composant partagé](view-components/_static/shared.png)

### <a name="avoiding-magic-strings"></a><span data-ttu-id="f4a42-249">Éviter les chaînes magiques</span><span class="sxs-lookup"><span data-stu-id="f4a42-249">Avoiding magic strings</span></span>

<span data-ttu-id="f4a42-250">Si vous voulez compiler la sécurité, vous pouvez remplacer le nom du composant codé en dur la vue par le nom de classe.</span><span class="sxs-lookup"><span data-stu-id="f4a42-250">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="f4a42-251">Créer le composant de vue sans le suffixe « ViewComponent » :</span><span class="sxs-lookup"><span data-stu-id="f4a42-251">Create the view component without the "ViewComponent" suffix:</span></span>

<span data-ttu-id="f4a42-252">[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-252">[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]</span></span>

<span data-ttu-id="f4a42-253">Ajouter un `using` instruction pour votre Razor afficher le fichier et utiliser le `nameof` opérateur :</span><span class="sxs-lookup"><span data-stu-id="f4a42-253">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

<span data-ttu-id="f4a42-254">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexNameof.cshtml?range=1-6,33-)]</span><span class="sxs-lookup"><span data-stu-id="f4a42-254">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexNameof.cshtml?range=1-6,33-)]</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f4a42-255">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f4a42-255">Additional Resources</span></span>

* [<span data-ttu-id="f4a42-256">Injection de dépendances dans les vues</span><span class="sxs-lookup"><span data-stu-id="f4a42-256">Dependency injection into views</span></span>](dependency-injection.md)

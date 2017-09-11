---
title: "Pages Razor obtenues par génération de modèles automatique dans ASP.NET Core"
author: rick-anderson
description: "Décrit l’obtention de pages Razor par génération de modèles automatique."
keywords: ASP.NET Core,pages Razor,Razor,MVC
ms.author: riande
manager: wpickett
ms.date: 7/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/page
ms.openlocfilehash: 02cbc7c7caf5128167dd3ecfdc0e2340f4876df5
ms.sourcegitcommit: d9ec19e5452af83648074db5d96c0a0f4f9e7f9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2017
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="4777f-104">Pages Razor obtenues par génération de modèles automatique dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4777f-104">Scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="4777f-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4777f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4777f-106">Ce didacticiel décrit les pages Razor créées par génération de modèles automatique au cours du [didacticiel précédent](xref:tutorials/razor-pages/page).</span><span class="sxs-lookup"><span data-stu-id="4777f-106">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/page).</span></span> 

<span data-ttu-id="4777f-107">[Affichez ou téléchargez](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) l’exemple.</span><span class="sxs-lookup"><span data-stu-id="4777f-107">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="4777f-108">Pages Create, Delete, Details et Edit.</span><span class="sxs-lookup"><span data-stu-id="4777f-108">The Create, Delete, Details, and Edit pages.</span></span>

<span data-ttu-id="4777f-109">Examinez le fichier code-behind *Pages/Movies/Index.cshtml.cs* : [!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml.cs)]</span><span class="sxs-lookup"><span data-stu-id="4777f-109">Examine the *Pages/Movies/Index.cshtml.cs* code-behind file: [!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml.cs)]</span></span>

<span data-ttu-id="4777f-110">Les pages Razor sont dérivées de `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="4777f-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="4777f-111">Par convention, la classe dérivée de `PageModel` s’appelle `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="4777f-111">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="4777f-112">Le constructeur utilise l’[injection de dépendances](xref:fundamentals/dependency-injection) pour ajouter `MovieContext` à la page.</span><span class="sxs-lookup"><span data-stu-id="4777f-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `MovieContext` to the page.</span></span> <span data-ttu-id="4777f-113">Toutes les pages obtenues par génération de modèles automatique suivent ce modèle.</span><span class="sxs-lookup"><span data-stu-id="4777f-113">All the scaffolded pages follow this pattern.</span></span>

<span data-ttu-id="4777f-114">Quand une requête est effectuée pour la page, la méthode `OnGetAsync` retourne une liste de films à la page Razor.</span><span class="sxs-lookup"><span data-stu-id="4777f-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="4777f-115">`OnGetAsync` ou `OnGet` est appelé sur une page Razor pour initialiser l’état de la page.</span><span class="sxs-lookup"><span data-stu-id="4777f-115">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="4777f-116">Dans ce cas, `OnGetAsync` obtient une liste de films à afficher.</span><span class="sxs-lookup"><span data-stu-id="4777f-116">In this case, `OnGetAsync` gets a list of movies to display.</span></span>

<span data-ttu-id="4777f-117">Examinez la page Razor *Pages/Movies/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4777f-117">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

<span data-ttu-id="4777f-118">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="4777f-118">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml)]</span></span>

<span data-ttu-id="4777f-119">Razor peut passer du HTML au C# ou à des balises spécifiques à Razor.</span><span class="sxs-lookup"><span data-stu-id="4777f-119">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="4777f-120">Quand un symbole `@` est suivi d’un [mot clé réservé Razor](xref:mvc/views/razor#razor-reserved-keywords), il est converti en balise spécifique à Razor. Sinon, il est converti en C#.</span><span class="sxs-lookup"><span data-stu-id="4777f-120">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="4777f-121">La directive Razor `@page` transforme le fichier en action MVC &mdash;, ce qui lui permet de prendre en charge les requêtes.</span><span class="sxs-lookup"><span data-stu-id="4777f-121">The `@page` Razor directive makes the file into an MVC action &mdash; which means that it can handle requests.</span></span> <span data-ttu-id="4777f-122">`@page` doit être la première directive Razor sur une page.</span><span class="sxs-lookup"><span data-stu-id="4777f-122">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="4777f-123">`@page` est un exemple de conversion en balise spécifique à Razor.</span><span class="sxs-lookup"><span data-stu-id="4777f-123">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="4777f-124">Pour plus d’informations, consultez [Syntaxe Razor](xref:mvc/views/razor#razor-syntax).</span><span class="sxs-lookup"><span data-stu-id="4777f-124">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="4777f-125">Examinez l’expression lambda utilisée dans le HTML Helper suivant :</span><span class="sxs-lookup"><span data-stu-id="4777f-125">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movie[0].Title))`

<span data-ttu-id="4777f-126">Le HTML Helper `DisplayNameFor` inspecte la propriété `Title` référencée dans l’expression lambda pour déterminer le nom d’affichage.</span><span class="sxs-lookup"><span data-stu-id="4777f-126">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="4777f-127">L’expression lambda est inspectée plutôt qu’évaluée.</span><span class="sxs-lookup"><span data-stu-id="4777f-127">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="4777f-128">Cela signifie qu’il n’existe pas de violation d’accès quand `model`, `model.Movies` ou `model.Movies[0]` ont une valeur `null` ou vide.</span><span class="sxs-lookup"><span data-stu-id="4777f-128">That means there is no access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="4777f-129">Quand l’expression lambda est évaluée (par exemple avec `@Html.DisplayFor(modelItem => item.Title)`), les valeurs de propriété du modèle sont évaluées.</span><span class="sxs-lookup"><span data-stu-id="4777f-129">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>
### <a name="the-model-directive"></a><span data-ttu-id="4777f-130">Directive @model</span><span class="sxs-lookup"><span data-stu-id="4777f-130">The @model directive</span></span>

<span data-ttu-id="4777f-131">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml?range=1-2&highlight=2)]</span><span class="sxs-lookup"><span data-stu-id="4777f-131">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml?range=1-2&highlight=2)]</span></span>

<span data-ttu-id="4777f-132">La directive `@model` spécifie le type du modèle passé à la page Razor.</span><span class="sxs-lookup"><span data-stu-id="4777f-132">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="4777f-133">Dans l’exemple précédent, la ligne `@model` rend la classe dérivée `PageModel` accessible à la page Razor.</span><span class="sxs-lookup"><span data-stu-id="4777f-133">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="4777f-134">Le modèle est utilisé dans les [HTML Helpers](https://docs.microsoft.com/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` et `@Html.DisplayName` de la page.</span><span class="sxs-lookup"><span data-stu-id="4777f-134">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayName` [HTML Helpers](https://docs.microsoft.com/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

<span data-ttu-id="4777f-135"><!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>
### ViewData et disposition</span><span class="sxs-lookup"><span data-stu-id="4777f-135"><!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>
### ViewData and layout</span></span>

<span data-ttu-id="4777f-136">Examinons le code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="4777f-136">Consider the following code:</span></span>

<span data-ttu-id="4777f-137">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml?range=1-6&highlight=4-)]</span><span class="sxs-lookup"><span data-stu-id="4777f-137">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml?range=1-6&highlight=4-)]</span></span>

<span data-ttu-id="4777f-138">Le code précédent en surbrillance est un exemple de passage de Razor au C#.</span><span class="sxs-lookup"><span data-stu-id="4777f-138">The preceding highligted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="4777f-139">Les caractères `{` et `}` délimitent un bloc de code C#.</span><span class="sxs-lookup"><span data-stu-id="4777f-139">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="4777f-140">La classe de base `Controller` a une propriété de dictionnaire `ViewData` qui permet d’ajouter des données à passer à une vue.</span><span class="sxs-lookup"><span data-stu-id="4777f-140">The `Controller` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="4777f-141">Vous pouvez ajouter des objets au dictionnaire `ViewData` à l’aide d’un modèle clé/valeur.</span><span class="sxs-lookup"><span data-stu-id="4777f-141">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="4777f-142">Dans l’exemple précédent, la propriété « Title » est ajoutée au dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="4777f-142">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span> <span data-ttu-id="4777f-143">La propriété « Title » est utilisée dans le fichier *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4777f-143">The "Title" property is used in the *Pages/_Layout.cshtml* file.</span></span> <span data-ttu-id="4777f-144">La balise suivante montre les premières lignes du fichier *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4777f-144">The following markup shows the first few lines of the *Pages/_Layout.cshtml* file.</span></span>

<span data-ttu-id="4777f-145">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout1.cshtml?highlight=6-)]</span><span class="sxs-lookup"><span data-stu-id="4777f-145">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout1.cshtml?highlight=6-)]</span></span>

<span data-ttu-id="4777f-146">La ligne `@*Markup removed for brevity.*@` est un commentaire Razor.</span><span class="sxs-lookup"><span data-stu-id="4777f-146">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="4777f-147">Contrairement aux commentaires HTML (`<!-- -->`), les commentaires Razor ne sont pas envoyés au client.</span><span class="sxs-lookup"><span data-stu-id="4777f-147">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

<span data-ttu-id="4777f-148">Exécutez l’application, puis testez les liens du projet (**Home**, **About**, **Contact**, **Create**, **Edit** et **Delete**).</span><span class="sxs-lookup"><span data-stu-id="4777f-148">Run the app and test the links in the project (**Home**, **About**, **Contact**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="4777f-149">Chaque page définit le titre, que vous pouvez voir dans l’onglet du navigateur. Quand vous définissez un signet pour une page, le titre est affecté au signet.</span><span class="sxs-lookup"><span data-stu-id="4777f-149">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span> <span data-ttu-id="4777f-150">*Pages/Index.cshtml* et *Pages/Movies/Index.cshtml* ont le même titre, mais vous pouvez les modifier pour avoir des valeurs distinctes.</span><span class="sxs-lookup"><span data-stu-id="4777f-150">*Pages/Index.cshtml* and *Pages/Movies/Index.cshtml* currently have the same title, but you can modify them to have different values.</span></span>

<span data-ttu-id="4777f-151">La propriété `Layout` est définie dans le fichier *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4777f-151">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

<span data-ttu-id="4777f-152">[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="4777f-152">[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]</span></span>

<span data-ttu-id="4777f-153">Le code précédent définit le fichier de disposition *Pages/_Layout.cshtml* pour tous les fichiers Razor du dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="4777f-153">The preceding markup sets the layout file to *Pages/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="4777f-154">Pour plus d’informations, consultez [Disposition](xref:mvc/razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="4777f-154">See [Layout](xref:mvc/razor-pages/index#layout) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="4777f-155">Mettre à jour la disposition</span><span class="sxs-lookup"><span data-stu-id="4777f-155">Update the layout</span></span>

<span data-ttu-id="4777f-156">Changez l’élément `<title>` du fichier *Pages/_Layout.cshtml* pour utiliser une chaîne plus courte.</span><span class="sxs-lookup"><span data-stu-id="4777f-156">Change the `<title>` element in the *Pages/_Layout.cshtml* file to use a shorter string.</span></span>

<span data-ttu-id="4777f-157">[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=1-6&highlight=6-)]</span><span class="sxs-lookup"><span data-stu-id="4777f-157">[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=1-6&highlight=6-)]</span></span>

<span data-ttu-id="4777f-158">Recherchez l’élément anchor suivant dans le fichier *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4777f-158">Find the following anchor element in the *Pages/_Layout.cshtml* file.</span></span>

```cshtml
<a asp-page="/Index" class="navbar-brand">RazorPagesMovie</a>
```
<span data-ttu-id="4777f-159">Remplacez l’élément précédent par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="4777f-159">Replace the preceding element with the following markup.</span></span>

```cshtml
<a asp-page="/Movies/Index" class="navbar-brand">RpMovie</a>
```

<span data-ttu-id="4777f-160">L’élément anchor précédent est un [Tag Helper](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="4777f-160">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="4777f-161">Dans le cas présent, il s’agit du [Tag Helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper).</span><span class="sxs-lookup"><span data-stu-id="4777f-161">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper).</span></span> <span data-ttu-id="4777f-162">L’attribut et la valeur du Tag Helper `asp-page="/Movies/Index"` créent un lien vers la page Razor `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="4777f-162">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span>

<span data-ttu-id="4777f-163">Enregistrez vos changements, puis testez l’application en cliquant sur le lien **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="4777f-163">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="4777f-164">Consultez le fichier [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml) dans GitHub.</span><span class="sxs-lookup"><span data-stu-id="4777f-164">See the [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml) file in GitHub.</span></span>

### <a name="the-create-code-behind-page"></a><span data-ttu-id="4777f-165">Page code-behind Create</span><span class="sxs-lookup"><span data-stu-id="4777f-165">The Create code-behind page</span></span>

<span data-ttu-id="4777f-166">Examinez le fichier code-behind *Pages/Movies/Create.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="4777f-166">Examine the *Pages/Movies/Create.cshtml.cs* code-behind file:</span></span>

<span data-ttu-id="4777f-167">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml.cs?name=snippetALL)]</span><span class="sxs-lookup"><span data-stu-id="4777f-167">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml.cs?name=snippetALL)]</span></span>

<span data-ttu-id="4777f-168">La méthode `OnGet` initialise l’état nécessaire pour la page.</span><span class="sxs-lookup"><span data-stu-id="4777f-168">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="4777f-169">La page Create n’a aucun état à initialiser.</span><span class="sxs-lookup"><span data-stu-id="4777f-169">The Create page doesn't have any state to initialize.</span></span> <span data-ttu-id="4777f-170">La méthode `Page` crée un objet `PageResult` qui affiche la page *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4777f-170">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="4777f-171">La propriété `Movie` utilise l’attribut `[BindProperty]` pour adhérer à la [liaison de données](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="4777f-171">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="4777f-172">Quand le formulaire Create publie les valeurs de formulaire, le runtime ASP.NET Core lie les valeurs publiées au modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4777f-172">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="4777f-173">La méthode `OnPostAsync` est exécutée quand la page publie les données de formulaire :</span><span class="sxs-lookup"><span data-stu-id="4777f-173">The `OnPostAsync` method is run when the page posts form data:</span></span>

<span data-ttu-id="4777f-174">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml.cs?name=snippetPost)]</span><span class="sxs-lookup"><span data-stu-id="4777f-174">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml.cs?name=snippetPost)]</span></span>

<span data-ttu-id="4777f-175">S’il existe des erreurs liées au modèle, le formulaire est réaffiché, ainsi que toutes les données de formulaire publiées.</span><span class="sxs-lookup"><span data-stu-id="4777f-175">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="4777f-176">La plupart des erreurs de modèle peuvent être interceptées côté client avant la publication du formulaire.</span><span class="sxs-lookup"><span data-stu-id="4777f-176">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="4777f-177">Voici un exemple d’erreur de modèle : la publication pour le champ de date d’une valeur qui ne peut pas être convertie en date.</span><span class="sxs-lookup"><span data-stu-id="4777f-177">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="4777f-178">Nous aborderons de manière plus détaillée la validation côté client et la validation du modèle plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="4777f-178">We'll talk more about client-side validation and model validation later in the tutorial.</span></span>

<span data-ttu-id="4777f-179">S’il n’existe pas d’erreurs de modèle, les données sont enregistrées et le navigateur est redirigé vers la page Index.</span><span class="sxs-lookup"><span data-stu-id="4777f-179">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="4777f-180">Page Razor Create</span><span class="sxs-lookup"><span data-stu-id="4777f-180">The Create Razor Page</span></span>

<span data-ttu-id="4777f-181">Examinez le fichier de la page Razor *Pages/Movies/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4777f-181">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

<span data-ttu-id="4777f-182">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="4777f-182">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml)]</span></span>

<span data-ttu-id="4777f-183">Visual Studio affiche la balise `<form method="post">` dans une police distinctive utilisée pour les Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="4777f-183">Visual Studio displays the `<form method="post">` tag in a distinctive font used for Tag Helpers.</span></span> <span data-ttu-id="4777f-184">L’élément `<form method="post">` est un [Tag Helper de formulaire](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4777f-184">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="4777f-185">Le Tag Helper de formulaire inclut automatiquement un [jeton de protection contre les falsifications](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="4777f-185">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

![Vue VS17 de la page Create.cshtml](page/_static/th.png)

<span data-ttu-id="4777f-187">Le moteur de génération de modèles automatique crée le code Razor pour chaque champ du modèle (sauf l’ID) de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="4777f-187">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

<span data-ttu-id="4777f-188">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml?range=15-20)]</span><span class="sxs-lookup"><span data-stu-id="4777f-188">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml?range=15-20)]</span></span>

<span data-ttu-id="4777f-189">Les [Tag Helpers de validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` et ` <span asp-validation-for`) affichent des erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="4777f-189">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and ` <span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="4777f-190">La validation est traitée de manière plus détaillée plus loin dans cette série.</span><span class="sxs-lookup"><span data-stu-id="4777f-190">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="4777f-191">Le [Tag Helper d’étiquette](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) génère la légende de l’étiquette et l’attribut `for` pour la propriété `Title`.</span><span class="sxs-lookup"><span data-stu-id="4777f-191">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="4777f-192">Le [Tag Helper d’entrée](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) utilise les attributs [DataAnnotations](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) et produit les attributs HTML nécessaires à la validation jQuery côté client.</span><span class="sxs-lookup"><span data-stu-id="4777f-192">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) uses the [DataAnnotations](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="4777f-193">Le prochain didacticiel décrit SQL Server LocalDB et la création des valeurs initiales de la base de données.</span><span class="sxs-lookup"><span data-stu-id="4777f-193">The next tutorial explains SQL Server LocalDB and seeding the database.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="4777f-194">[Précédent : Ajout d’un modèle](xref:tutorials/razor-pages/modelz)
[Suivant : SQL Server LocalDB](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="4777f-194">[Previous: Adding a model](xref:tutorials/razor-pages/modelz)
[Next: SQL Server LocalDB](xref:tutorials/razor-pages/sql)</span></span>

---
title: "Programmes d’assistance de balise dans les formulaires dans ASP.NET Core"
author: rick-anderson
description: "Décrit la fonction intégrée programmes d’assistance de balise utilisée avec des formulaires."
keywords: "ASP.NET Core, d’assistance de balise, TagHelper, formulaire HTML, de formulaires"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 25595059-4fac-4785-8152-f88590e3169b
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/working-with-forms
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff6fee6eee539fc77b6c6180a816daa760202848
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="introduction-to-using-tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="0ddff-104">Introduction à l’utilisation de programmes d’assistance de balise dans les formulaires dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ddff-104">Introduction to using tag helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="0ddff-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Dave Paquette](https://twitter.com/Dave_Paquette), et [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="0ddff-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="0ddff-106">Ce document illustre l’utilisation de formulaires et les éléments HTML couramment utilisés dans un formulaire.</span><span class="sxs-lookup"><span data-stu-id="0ddff-106">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="0ddff-107">Le code HTML [formulaire](https://www.w3.org/TR/html401/interact/forms.html) élément fournit l’utilisation d’applications web mécanisme principal pour publier des données sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0ddff-107">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="0ddff-108">La majeure partie de ce document décrit [programmes d’assistance de balise](tag-helpers/intro.md) et comment ils peuvent vous aider à productive créer des formulaires HTML robustes.</span><span class="sxs-lookup"><span data-stu-id="0ddff-108">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="0ddff-109">Nous vous conseillons de lire [Introduction aux programmes d’assistance de balise](tag-helpers/intro.md) avant de lire ce document.</span><span class="sxs-lookup"><span data-stu-id="0ddff-109">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="0ddff-110">Dans de nombreux cas, les programmes d’assistance HTML fournissent une approche alternative à une application d’assistance de balise spécifique, mais il est important de reconnaître que les programmes d’assistance de balise ne remplacent pas les programmes d’assistance HTML et n’est pas un programme d’assistance de balise pour chaque programme d’assistance HTML.</span><span class="sxs-lookup"><span data-stu-id="0ddff-110">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers do not replace HTML Helpers and there is not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="0ddff-111">Lorsqu’un autre programme d’assistance HTML existe, il est indiqué.</span><span class="sxs-lookup"><span data-stu-id="0ddff-111">When an HTML Helper alternative exists, it is mentioned.</span></span>

<a name=my-asp-route-param-ref-label></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="0ddff-112">L’application d’assistance de balise de formulaire</span><span class="sxs-lookup"><span data-stu-id="0ddff-112">The Form Tag Helper</span></span>

<span data-ttu-id="0ddff-113">Le [formulaire](https://www.w3.org/TR/html401/interact/forms.html) d’assistance de balise :</span><span class="sxs-lookup"><span data-stu-id="0ddff-113">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="0ddff-114">Génère le code HTML [ \<formulaire >](https://www.w3.org/TR/html401/interact/forms.html) `action` valeur d’attribut pour un itinéraire nommé ou une action de contrôleur MVC</span><span class="sxs-lookup"><span data-stu-id="0ddff-114">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="0ddff-115">Génère un texte masqué [jeton de demande de vérification](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) pour empêcher la falsification de requête (lorsqu’il est utilisé avec le `[ValidateAntiForgeryToken]` attribut dans la méthode d’action HTTP Post)</span><span class="sxs-lookup"><span data-stu-id="0ddff-115">Generates a hidden [Request Verification Token](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="0ddff-116">Fournit la `asp-route-<Parameter Name>` attribut, où `<Parameter Name>` est ajoutée pour les valeurs d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="0ddff-116">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="0ddff-117">Le `routeValues` paramètres `Html.BeginForm` et `Html.BeginRouteForm` fournissent des fonctionnalités similaires.</span><span class="sxs-lookup"><span data-stu-id="0ddff-117">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="0ddff-118">Possède une alternative de programme d’assistance HTML `Html.BeginForm` et`Html.BeginRouteForm`</span><span class="sxs-lookup"><span data-stu-id="0ddff-118">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="0ddff-119">Aperçu :</span><span class="sxs-lookup"><span data-stu-id="0ddff-119">Sample:</span></span>

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="0ddff-120">L’assistance de balise de formulaire ci-dessus génère le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="0ddff-120">The Form Tag Helper above generates the following HTML:</span></span>

```HTML
<form method="post" action="/Demo/Register">
     <!-- Input and Submit elements -->
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
    </form>
```

<span data-ttu-id="0ddff-121">Le runtime MVC génère le `action` valeur d’attribut à partir des attributs d’assistance de balise de formulaire `asp-controller` et `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="0ddff-121">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="0ddff-122">L’application d’assistance de balise de formulaire génère également un masqué [jeton de demande de vérification](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) pour empêcher la falsification de requête (lorsqu’il est utilisé avec le `[ValidateAntiForgeryToken]` attribut dans la méthode d’action HTTP Post).</span><span class="sxs-lookup"><span data-stu-id="0ddff-122">The Form Tag Helper also generates a hidden [Request Verification Token](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="0ddff-123">Un formulaire HTML pur empêcher la falsification de requête est difficile, l’application d’assistance de balise de formulaire fournit ce service pour vous.</span><span class="sxs-lookup"><span data-stu-id="0ddff-123">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="0ddff-124">À l’aide d’un itinéraire nommé</span><span class="sxs-lookup"><span data-stu-id="0ddff-124">Using a named route</span></span>

<span data-ttu-id="0ddff-125">Le `asp-route` attribut d’assistance de balise peut également générer le balisage pour le code HTML `action` attribut.</span><span class="sxs-lookup"><span data-stu-id="0ddff-125">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="0ddff-126">Une application avec un [itinéraire](../../fundamentals/routing.md) nommé `register` Impossible d’utiliser le balisage suivant pour la page d’inscription :</span><span class="sxs-lookup"><span data-stu-id="0ddff-126">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="0ddff-127">Un grand nombre des vues dans le *Views/Account* dossier (généré lorsque vous créez une application web avec *comptes d’utilisateur individuels*) contiennent le [asp-itinéraire-returnurl](https://docs.microsoft.com/aspnet/core/mvc/views/working-with-forms) attribut :</span><span class="sxs-lookup"><span data-stu-id="0ddff-127">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](https://docs.microsoft.com/aspnet/core/mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="0ddff-128">Avec les modèles intégrés, `returnUrl` n’est rempli automatiquement lorsque vous essayez d’accéder à une ressource autorisée, mais ne sont pas authentifiés ou autorisés.</span><span class="sxs-lookup"><span data-stu-id="0ddff-128">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="0ddff-129">Lorsque vous essayez d’un accès non autorisé, l’intergiciel (middleware) de sécurité vous redirige vers la page de connexion avec le `returnUrl` défini.</span><span class="sxs-lookup"><span data-stu-id="0ddff-129">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-input-tag-helper"></a><span data-ttu-id="0ddff-130">L’application d’assistance de balise d’entrée</span><span class="sxs-lookup"><span data-stu-id="0ddff-130">The Input Tag Helper</span></span>

<span data-ttu-id="0ddff-131">L’application d’assistance de balise d’entrée est liée à un élément HTML [ \<d’entrée >](https://www.w3.org/wiki/HTML/Elements/input) élément à une expression de modèle dans votre affichage razor.</span><span class="sxs-lookup"><span data-stu-id="0ddff-131">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="0ddff-132">Syntaxe :</span><span class="sxs-lookup"><span data-stu-id="0ddff-132">Syntax:</span></span>

```HTML
<input asp-for="<Expression Name>" />
```

<span data-ttu-id="0ddff-133">L’application d’assistance de balise d’entrée :</span><span class="sxs-lookup"><span data-stu-id="0ddff-133">The Input Tag Helper:</span></span>

* <span data-ttu-id="0ddff-134">Génère le `id` et `name` attributs HTML pour le nom de l’expression spécifiée dans le `asp-for` attribut.</span><span class="sxs-lookup"><span data-stu-id="0ddff-134">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="0ddff-135">`asp-for="Property1.Property2"` équivaut à `m => m.Property1.Property2`.</span><span class="sxs-lookup"><span data-stu-id="0ddff-135">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="0ddff-136">Le nom de l’expression est celui qui est utilisé pour la `asp-for` valeur d’attribut.</span><span class="sxs-lookup"><span data-stu-id="0ddff-136">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="0ddff-137">Consultez le [noms d’expressions](#expression-names) section pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="0ddff-137">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="0ddff-138">Définit le code HTML `type` en fonction du type de modèle de valeur d’attribut et [annotation de données](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributs appliqués à la propriété du modèle</span><span class="sxs-lookup"><span data-stu-id="0ddff-138">Sets the HTML `type` attribute value based on the model type and  [data annotation](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="0ddff-139">Ne remplace pas le code HTML `type` valeur d’attribut s’il est spécifié</span><span class="sxs-lookup"><span data-stu-id="0ddff-139">Will not overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="0ddff-140">Génère [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) validation des attributs à partir de [annotation de données](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributs appliqués aux propriétés de modèle</span><span class="sxs-lookup"><span data-stu-id="0ddff-140">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="0ddff-141">Est une fonctionnalité du programme d’assistance HTML se chevauchent avec `Html.TextBoxFor` et `Html.EditorFor`.</span><span class="sxs-lookup"><span data-stu-id="0ddff-141">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="0ddff-142">Consultez le **alternatives de programme d’assistance HTML à l’application d’assistance de balise d’entrée** pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="0ddff-142">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="0ddff-143">Fournit un typage fort.</span><span class="sxs-lookup"><span data-stu-id="0ddff-143">Provides strong typing.</span></span> <span data-ttu-id="0ddff-144">Si le nom de la propriété est modifiée et que vous ne mettez pas à jour l’application d’assistance de balise vous obtiendrez une erreur semblable au suivant :</span><span class="sxs-lookup"><span data-stu-id="0ddff-144">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

<span data-ttu-id="0ddff-145">Le `Input` application d’assistance de balise définit le code HTML `type` attribut basé sur le type .NET.</span><span class="sxs-lookup"><span data-stu-id="0ddff-145">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="0ddff-146">Le tableau suivant répertorie certains types .NET communs et type HTML généré (pas tous les types .NET sont répertorié).</span><span class="sxs-lookup"><span data-stu-id="0ddff-146">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="0ddff-147">Type .NET</span><span class="sxs-lookup"><span data-stu-id="0ddff-147">.NET type</span></span>|<span data-ttu-id="0ddff-148">Type d’entrée</span><span class="sxs-lookup"><span data-stu-id="0ddff-148">Input Type</span></span>|
|---|---|
|<span data-ttu-id="0ddff-149">Bool</span><span class="sxs-lookup"><span data-stu-id="0ddff-149">Bool</span></span>|<span data-ttu-id="0ddff-150">type = « checkbox »</span><span class="sxs-lookup"><span data-stu-id="0ddff-150">type=”checkbox”</span></span>|
|<span data-ttu-id="0ddff-151">Chaîne</span><span class="sxs-lookup"><span data-stu-id="0ddff-151">String</span></span>|<span data-ttu-id="0ddff-152">type = « text »</span><span class="sxs-lookup"><span data-stu-id="0ddff-152">type=”text”</span></span>|
|<span data-ttu-id="0ddff-153">DateTime</span><span class="sxs-lookup"><span data-stu-id="0ddff-153">DateTime</span></span>|<span data-ttu-id="0ddff-154">type = « datetime »</span><span class="sxs-lookup"><span data-stu-id="0ddff-154">type=”datetime”</span></span>|
|<span data-ttu-id="0ddff-155">Byte</span><span class="sxs-lookup"><span data-stu-id="0ddff-155">Byte</span></span>|<span data-ttu-id="0ddff-156">type = « number »</span><span class="sxs-lookup"><span data-stu-id="0ddff-156">type=”number”</span></span>|
|<span data-ttu-id="0ddff-157">Int</span><span class="sxs-lookup"><span data-stu-id="0ddff-157">Int</span></span>|<span data-ttu-id="0ddff-158">type = « number »</span><span class="sxs-lookup"><span data-stu-id="0ddff-158">type=”number”</span></span>|
|<span data-ttu-id="0ddff-159">Single, Double</span><span class="sxs-lookup"><span data-stu-id="0ddff-159">Single, Double</span></span>|<span data-ttu-id="0ddff-160">type = « number »</span><span class="sxs-lookup"><span data-stu-id="0ddff-160">type=”number”</span></span>|


<span data-ttu-id="0ddff-161">Le tableau suivant présente certaines commun [annotations de données](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributs l’application d’assistance de balise d’entrée est mappés à des types spécifiques d’entrée (pas de chaque attribut de validation est répertorié) :</span><span class="sxs-lookup"><span data-stu-id="0ddff-161">The following table shows some common [data annotations](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>


|<span data-ttu-id="0ddff-162">Attribut</span><span class="sxs-lookup"><span data-stu-id="0ddff-162">Attribute</span></span>|<span data-ttu-id="0ddff-163">Type d’entrée</span><span class="sxs-lookup"><span data-stu-id="0ddff-163">Input Type</span></span>|
|---|---|
|<span data-ttu-id="0ddff-164">[EmailAddress]</span><span class="sxs-lookup"><span data-stu-id="0ddff-164">[EmailAddress]</span></span>|<span data-ttu-id="0ddff-165">type = « email »</span><span class="sxs-lookup"><span data-stu-id="0ddff-165">type=”email”</span></span>|
|<span data-ttu-id="0ddff-166">[Url]</span><span class="sxs-lookup"><span data-stu-id="0ddff-166">[Url]</span></span>|<span data-ttu-id="0ddff-167">type = « url »</span><span class="sxs-lookup"><span data-stu-id="0ddff-167">type=”url”</span></span>|
|<span data-ttu-id="0ddff-168">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="0ddff-168">[HiddenInput]</span></span>|<span data-ttu-id="0ddff-169">type = « hidden »</span><span class="sxs-lookup"><span data-stu-id="0ddff-169">type=”hidden”</span></span>|
|<span data-ttu-id="0ddff-170">[Phone]</span><span class="sxs-lookup"><span data-stu-id="0ddff-170">[Phone]</span></span>|<span data-ttu-id="0ddff-171">type = « téléphone »</span><span class="sxs-lookup"><span data-stu-id="0ddff-171">type=”tel”</span></span>|
|<span data-ttu-id="0ddff-172">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="0ddff-172">[DataType(DataType.Password)]</span></span>| <span data-ttu-id="0ddff-173">type = « password »</span><span class="sxs-lookup"><span data-stu-id="0ddff-173">type=”password”</span></span>|
|<span data-ttu-id="0ddff-174">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="0ddff-174">[DataType(DataType.Date)]</span></span>| <span data-ttu-id="0ddff-175">type = « date »</span><span class="sxs-lookup"><span data-stu-id="0ddff-175">type=”date”</span></span>|
|<span data-ttu-id="0ddff-176">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="0ddff-176">[DataType(DataType.Time)]</span></span>| <span data-ttu-id="0ddff-177">type = « time »</span><span class="sxs-lookup"><span data-stu-id="0ddff-177">type=”time”</span></span>|


<span data-ttu-id="0ddff-178">Aperçu :</span><span class="sxs-lookup"><span data-stu-id="0ddff-178">Sample:</span></span>

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="0ddff-179">Le code ci-dessus génère le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="0ddff-179">The code above generates the following HTML:</span></span>

```HTML
  <form method="post" action="/Demo/RegisterInput">
       Email:
       <input type="email" data-val="true"
              data-val-email="The Email Address field is not a valid e-mail address."
              data-val-required="The Email Address field is required."
              id="Email" name="Email" value="" /> <br>
       Password:
       <input type="password" data-val="true"
              data-val-required="The Password field is required."
              id="Password" name="Password" /><br>
       <button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
```

<span data-ttu-id="0ddff-180">Les annotations de données appliquées à la `Email` et `Password` propriétés génèrent des métadonnées sur le modèle.</span><span class="sxs-lookup"><span data-stu-id="0ddff-180">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="0ddff-181">Consomme les métadonnées du modèle de l’application d’assistance de balise d’entrée et produit [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributs (consultez [Validation du modèle](../models/validation.md)).</span><span class="sxs-lookup"><span data-stu-id="0ddff-181">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="0ddff-182">Ces attributs décrivent les validateurs à joindre aux champs d’entrée.</span><span class="sxs-lookup"><span data-stu-id="0ddff-182">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="0ddff-183">Cela fournit discrète HTML5 et [jQuery](https://jquery.com/) validation.</span><span class="sxs-lookup"><span data-stu-id="0ddff-183">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="0ddff-184">Les attributs non obstructifs sont au format `data-val-rule="Error Message"`, où la règle est le nom de la règle de validation (tel que `data-val-required`, `data-val-email`, `data-val-maxlength`, etc..) Si un message d’erreur est fourni dans l’attribut, il est affiché en tant que la valeur de la `data-val-rule` attribut.</span><span class="sxs-lookup"><span data-stu-id="0ddff-184">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it is displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="0ddff-185">Il existe également des attributs de la forme `data-val-ruleName-argumentName="argumentValue"` qui fournissent des détails supplémentaires sur la règle, par exemple, `data-val-maxlength-max="1024"` .</span><span class="sxs-lookup"><span data-stu-id="0ddff-185">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="0ddff-186">Alternatives de programme d’assistance HTML à l’application d’assistance de balise d’entrée</span><span class="sxs-lookup"><span data-stu-id="0ddff-186">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="0ddff-187">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` et `Html.EditorFor` se chevauchent de fonctionnalités avec l’application d’assistance de balise d’entrée.</span><span class="sxs-lookup"><span data-stu-id="0ddff-187">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="0ddff-188">L’application d’assistance de balise d’entrée définira automatiquement le `type` attribut ; `Html.TextBox` et `Html.TextBoxFor` ne seront pas.</span><span class="sxs-lookup"><span data-stu-id="0ddff-188">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` will not.</span></span> <span data-ttu-id="0ddff-189">`Html.Editor`et `Html.EditorFor` gérer les collections, les objets complexes et les modèles ; n’est pas le cas de l’application d’assistance de balise d’entrée.</span><span class="sxs-lookup"><span data-stu-id="0ddff-189">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper does not.</span></span> <span data-ttu-id="0ddff-190">L’application d’assistance de balise d’entrée, `Html.EditorFor` et `Html.TextBoxFor` sont fortement typées (elles utilisent les expressions lambda) ; `Html.TextBox` et `Html.Editor` ne sont pas (ils utilisent des noms d’expressions).</span><span class="sxs-lookup"><span data-stu-id="0ddff-190">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="0ddff-191">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="0ddff-191">HtmlAttributes</span></span>

<span data-ttu-id="0ddff-192">`@Html.Editor()`et `@Html.EditorFor()` utiliser spéciale `ViewDataDictionary` entrée nommée `htmlAttributes` lors de l’exécution de leurs modèles par défaut.</span><span class="sxs-lookup"><span data-stu-id="0ddff-192">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="0ddff-193">Ce comportement est augmenté éventuellement à l’aide `additionalViewData` paramètres.</span><span class="sxs-lookup"><span data-stu-id="0ddff-193">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="0ddff-194">La clé « htmlAttributes » respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="0ddff-194">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="0ddff-195">La clé « htmlAttributes » est gérée de façon similaire à la `htmlAttributes` objet passé à l’entrée des programmes d’assistance comme `@Html.TextBox()`.</span><span class="sxs-lookup"><span data-stu-id="0ddff-195">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="0ddff-196">Noms d’expressions</span><span class="sxs-lookup"><span data-stu-id="0ddff-196">Expression names</span></span>

<span data-ttu-id="0ddff-197">Le `asp-for` valeur d’attribut est un `ModelExpression` et la partie droite d’une expression lambda.</span><span class="sxs-lookup"><span data-stu-id="0ddff-197">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="0ddff-198">Par conséquent, `asp-for="Property1"` devient `m => m.Property1` dans le code généré qui est la raison pour laquelle vous n’avez pas besoin avec le préfixe `Model`.</span><span class="sxs-lookup"><span data-stu-id="0ddff-198">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="0ddff-199">Vous pouvez utiliser le « @ » caractère à commencer une expression inline et de passer avant le `m.`:</span><span class="sxs-lookup"><span data-stu-id="0ddff-199">You can use the "@" character to start an inline expression and move before the `m.`:</span></span>

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe" />
```

<span data-ttu-id="0ddff-200">Génère les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="0ddff-200">Generates the following:</span></span>

```HTML
<input type="text" id="joe" name="joe" value="Joe" />
```

<span data-ttu-id="0ddff-201">Avec les propriétés de la collection, `asp-for="CollectionProperty[23].Member"` génère le même nom que `asp-for="CollectionProperty[i].Member"` lorsque `i` a la valeur `23`.</span><span class="sxs-lookup"><span data-stu-id="0ddff-201">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="0ddff-202">Navigation dans les propriétés enfants</span><span class="sxs-lookup"><span data-stu-id="0ddff-202">Navigating child properties</span></span>

<span data-ttu-id="0ddff-203">Vous pouvez également accéder aux propriétés enfant à l’aide du chemin de la propriété du modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="0ddff-203">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="0ddff-204">Considérez une classe de modèle plus complexe qui contient un enfant `Address` propriété.</span><span class="sxs-lookup"><span data-stu-id="0ddff-204">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="0ddff-205">Dans la vue, nous lier à `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="0ddff-205">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="0ddff-206">Le code HTML suivant est généré pour `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="0ddff-206">The following HTML is generated for `Address.AddressLine1`:</span></span>

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="" />
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="0ddff-207">Noms d’expressions et des Collections</span><span class="sxs-lookup"><span data-stu-id="0ddff-207">Expression names and Collections</span></span>

<span data-ttu-id="0ddff-208">Exemple, un modèle qui contient un tableau de `Colors`:</span><span class="sxs-lookup"><span data-stu-id="0ddff-208">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="0ddff-209">La méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="0ddff-209">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

<span data-ttu-id="0ddff-210">Razor suivante montre comment vous accéder à un spécifique `Color` élément :</span><span class="sxs-lookup"><span data-stu-id="0ddff-210">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="0ddff-211">Le *Views/Shared/EditorTemplates/String.cshtml* modèle :</span><span class="sxs-lookup"><span data-stu-id="0ddff-211">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="0ddff-212">Exemple à l’aide de `List<T>`:</span><span class="sxs-lookup"><span data-stu-id="0ddff-212">Sample using `List<T>`:</span></span>

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="0ddff-213">Razor suivante montre comment effectuer une itération au sein d’une collection :</span><span class="sxs-lookup"><span data-stu-id="0ddff-213">The following Razor shows how to iterate over a collection:</span></span>

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="0ddff-214">Le *Views/Shared/EditorTemplates/ToDoItem.cshtml* modèle :</span><span class="sxs-lookup"><span data-stu-id="0ddff-214">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]


>[!NOTE]
><span data-ttu-id="0ddff-215">Toujours utiliser `for` (et *pas* `foreach`) pour itérer sur une liste.</span><span class="sxs-lookup"><span data-stu-id="0ddff-215">Always use `for` (and *not* `foreach`) to iterate over a list.</span></span> <span data-ttu-id="0ddff-216">L’évaluation d’un indexeur dans LINQ expression peut être coûteuse et doit être réduite.</span><span class="sxs-lookup"><span data-stu-id="0ddff-216">Evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="0ddff-217">L’exemple commenté de code ci-dessus illustre la façon de remplacer l’expression lambda avec le `@` pour accéder à chaque opérateur `ToDoItem` dans la liste.</span><span class="sxs-lookup"><span data-stu-id="0ddff-217">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="0ddff-218">L’application d’assistance de balise Textarea</span><span class="sxs-lookup"><span data-stu-id="0ddff-218">The Textarea Tag Helper</span></span>

<span data-ttu-id="0ddff-219">Le `Textarea Tag Helper` application d’assistance de balise est similaire à l’application d’assistance de balise d’entrée.</span><span class="sxs-lookup"><span data-stu-id="0ddff-219">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="0ddff-220">Génère le `id` et `name` attributs et les attributs de validation de données à partir du modèle pour un [ \<textarea >](https://www.w3.org/wiki/HTML/Elements/textarea) élément.</span><span class="sxs-lookup"><span data-stu-id="0ddff-220">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="0ddff-221">Fournit un typage fort.</span><span class="sxs-lookup"><span data-stu-id="0ddff-221">Provides strong typing.</span></span>

* <span data-ttu-id="0ddff-222">Solution de programme d’assistance HTML :`Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="0ddff-222">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="0ddff-223">Aperçu :</span><span class="sxs-lookup"><span data-stu-id="0ddff-223">Sample:</span></span>

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="0ddff-224">Le code HTML suivant est généré :</span><span class="sxs-lookup"><span data-stu-id="0ddff-224">The following HTML is generated:</span></span>

```HTML
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="0ddff-225">L’application d’assistance de balise étiquette</span><span class="sxs-lookup"><span data-stu-id="0ddff-225">The Label Tag Helper</span></span>

* <span data-ttu-id="0ddff-226">Génère la légende de l’étiquette et `for` d’attribut sur un [ <label> ](https://www.w3.org/wiki/HTML/Elements/label) élément d’un nom de l’expression</span><span class="sxs-lookup"><span data-stu-id="0ddff-226">Generates the label caption and `for` attribute on a [<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="0ddff-227">Alternative de programme d’assistance HTML : `Html.LabelFor`.</span><span class="sxs-lookup"><span data-stu-id="0ddff-227">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="0ddff-228">Le `Label Tag Helper` offre les avantages suivants sur un élément d’étiquette HTML pur :</span><span class="sxs-lookup"><span data-stu-id="0ddff-228">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="0ddff-229">Vous obtenez automatiquement la valeur de l’étiquette descriptive à partir de la `Display` attribut.</span><span class="sxs-lookup"><span data-stu-id="0ddff-229">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="0ddff-230">Le nom d’affichage souhaité peut changer dans le temps et la combinaison de `Display` attribut et le programme d’assistance de balise étiquette seront applique le `Display` partout où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="0ddff-230">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="0ddff-231">Moins de balisage dans le code source</span><span class="sxs-lookup"><span data-stu-id="0ddff-231">Less markup in source code</span></span>

* <span data-ttu-id="0ddff-232">Fort en tapant avec la propriété du modèle.</span><span class="sxs-lookup"><span data-stu-id="0ddff-232">Strong typing with the model property.</span></span>

<span data-ttu-id="0ddff-233">Aperçu :</span><span class="sxs-lookup"><span data-stu-id="0ddff-233">Sample:</span></span>

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="0ddff-234">Le code HTML suivant est généré pour le `<label>` élément :</span><span class="sxs-lookup"><span data-stu-id="0ddff-234">The following HTML is generated for the `<label>` element:</span></span>

```HTML
<label for="Email">Email Address</label>
```

<span data-ttu-id="0ddff-235">L’application d’assistance de balise étiquette généré le `for` valeur d’attribut de « Email », qui est l’ID associé à le `<input>` élément.</span><span class="sxs-lookup"><span data-stu-id="0ddff-235">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="0ddff-236">Les programmes d’assistance de balise générer cohérent `id` et `for` éléments de sorte qu’ils peuvent être correctement associés.</span><span class="sxs-lookup"><span data-stu-id="0ddff-236">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="0ddff-237">Le libellé dans cet exemple provient de la `Display` attribut.</span><span class="sxs-lookup"><span data-stu-id="0ddff-237">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="0ddff-238">Si le modèle ne contenait pas un `Display` attribut, la légende est le nom de propriété de l’expression.</span><span class="sxs-lookup"><span data-stu-id="0ddff-238">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="0ddff-239">Les programmes d’assistance de balise de Validation</span><span class="sxs-lookup"><span data-stu-id="0ddff-239">The Validation Tag Helpers</span></span>

<span data-ttu-id="0ddff-240">Il existe deux programmes d’assistance de balise de Validation.</span><span class="sxs-lookup"><span data-stu-id="0ddff-240">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="0ddff-241">Le `Validation Message Tag Helper` (qui affiche un message de validation d’une propriété unique dans votre modèle) et le `Validation Summary Tag Helper` (qui affiche un résumé des erreurs de validation).</span><span class="sxs-lookup"><span data-stu-id="0ddff-241">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="0ddff-242">Le `Input Tag Helper` ajoute des attributs de validation client côté HTML5 pour entrer des éléments en fonction des données, les attributs d’annotations dans vos classes de modèle.</span><span class="sxs-lookup"><span data-stu-id="0ddff-242">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="0ddff-243">La validation est également effectuée sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0ddff-243">Validation is also performed on the server.</span></span> <span data-ttu-id="0ddff-244">L’application d’assistance de balise de Validation affiche ces messages d’erreur lorsqu’une erreur de validation se produit.</span><span class="sxs-lookup"><span data-stu-id="0ddff-244">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="0ddff-245">L’assistance de balise de Message de Validation</span><span class="sxs-lookup"><span data-stu-id="0ddff-245">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="0ddff-246">Ajoute le [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` d’attribut pour le [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) élément, ce qui joint les messages d’erreur de validation sur le champ d’entrée de la propriété de modèle spécifié.</span><span class="sxs-lookup"><span data-stu-id="0ddff-246">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="0ddff-247">Lorsqu’une erreur de validation côté client se produit, [jQuery](https://jquery.com/) affiche le message d’erreur dans le `<span>` élément.</span><span class="sxs-lookup"><span data-stu-id="0ddff-247">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="0ddff-248">La validation a également lieu sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0ddff-248">Validation also takes place on the server.</span></span> <span data-ttu-id="0ddff-249">Les clients peuvent avoir désactivé JavaScript et une validation peut uniquement être effectuée sur le côté serveur.</span><span class="sxs-lookup"><span data-stu-id="0ddff-249">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="0ddff-250">Solution de programme d’assistance HTML :`Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="0ddff-250">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="0ddff-251">Le `Validation Message Tag Helper` est utilisé avec le `asp-validation-for` attribut sur un HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) élément.</span><span class="sxs-lookup"><span data-stu-id="0ddff-251">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```HTML
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="0ddff-252">L’application d’assistance de balise de Message de Validation génère le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="0ddff-252">The Validation Message Tag Helper will generate the following HTML:</span></span>

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="0ddff-253">Vous utilisez généralement la `Validation Message Tag Helper` après une `Input` assistance de balise pour la même propriété.</span><span class="sxs-lookup"><span data-stu-id="0ddff-253">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="0ddff-254">Cela affiche des messages d’erreur de validation près de l’entrée qui a provoqué l’erreur.</span><span class="sxs-lookup"><span data-stu-id="0ddff-254">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="0ddff-255">Vous devez disposer d’une vue avec le code JavaScript approprié et [jQuery](https://jquery.com/) références de script en place pour la validation côté client.</span><span class="sxs-lookup"><span data-stu-id="0ddff-255">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="0ddff-256">Consultez [Validation du modèle](../models/validation.md) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="0ddff-256">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="0ddff-257">Lorsqu’une erreur de validation côté serveur produit (par exemple lorsque vous disposez validation personnalisée côté serveur ou la validation côté client est désactivée), MVC place ce message d’erreur dans le corps de la `<span>` élément.</span><span class="sxs-lookup"><span data-stu-id="0ddff-257">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="0ddff-258">L’application auxiliaire de Validation Summary (balise)</span><span class="sxs-lookup"><span data-stu-id="0ddff-258">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="0ddff-259">Cibles `<div>` éléments avec le `asp-validation-summary` attribut</span><span class="sxs-lookup"><span data-stu-id="0ddff-259">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="0ddff-260">Solution de programme d’assistance HTML :`@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="0ddff-260">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="0ddff-261">Le `Validation Summary Tag Helper` est utilisé pour afficher un résumé des messages de validation.</span><span class="sxs-lookup"><span data-stu-id="0ddff-261">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="0ddff-262">Le `asp-validation-summary` valeur d’attribut peut être une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="0ddff-262">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="0ddff-263">ASP-validation-résumé</span><span class="sxs-lookup"><span data-stu-id="0ddff-263">asp-validation-summary</span></span>|<span data-ttu-id="0ddff-264">Affichées des messages de validation</span><span class="sxs-lookup"><span data-stu-id="0ddff-264">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="0ddff-265">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="0ddff-265">ValidationSummary.All</span></span>|<span data-ttu-id="0ddff-266">Niveau de la propriété et le modèle</span><span class="sxs-lookup"><span data-stu-id="0ddff-266">Property and model level</span></span>|
|<span data-ttu-id="0ddff-267">ValidationSummary.ModelOnly</span><span class="sxs-lookup"><span data-stu-id="0ddff-267">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="0ddff-268">Modèle</span><span class="sxs-lookup"><span data-stu-id="0ddff-268">Model</span></span>|
|<span data-ttu-id="0ddff-269">ValidationSummary.None</span><span class="sxs-lookup"><span data-stu-id="0ddff-269">ValidationSummary.None</span></span>|<span data-ttu-id="0ddff-270">Aucune</span><span class="sxs-lookup"><span data-stu-id="0ddff-270">None</span></span>|

### <a name="sample"></a><span data-ttu-id="0ddff-271">Exemple</span><span class="sxs-lookup"><span data-stu-id="0ddff-271">Sample</span></span>

<span data-ttu-id="0ddff-272">Dans l’exemple suivant, le modèle de données est décoré avec `DataAnnotation` attributs, ce qui génère des messages d’erreur de validation sur le `<input>` élément.</span><span class="sxs-lookup"><span data-stu-id="0ddff-272">In the following example, the data model is decorated with `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="0ddff-273">Lorsqu’une erreur de validation se produit, l’application d’assistance de balise de Validation affiche le message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="0ddff-273">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="0ddff-274">Le code HTML généré (lorsque le modèle est valid) :</span><span class="sxs-lookup"><span data-stu-id="0ddff-274">The generated HTML (when the model is valid):</span></span>

```HTML
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid e-mail address."
   data-val="true"> <br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="0ddff-275">L’application d’assistance de balise Select</span><span class="sxs-lookup"><span data-stu-id="0ddff-275">The Select Tag Helper</span></span>

* <span data-ttu-id="0ddff-276">Génère [sélectionnez](https://www.w3.org/wiki/HTML/Elements/select) et [option](https://www.w3.org/wiki/HTML/Elements/option) éléments pour les propriétés de votre modèle.</span><span class="sxs-lookup"><span data-stu-id="0ddff-276">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="0ddff-277">Possède une alternative de programme d’assistance HTML `Html.DropDownListFor` et`Html.ListBoxFor`</span><span class="sxs-lookup"><span data-stu-id="0ddff-277">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="0ddff-278">Le `Select Tag Helper` `asp-for` Spécifie le nom de propriété de modèle pour le [sélectionnez](https://www.w3.org/wiki/HTML/Elements/select) élément et `asp-items` Spécifie le [option](https://www.w3.org/wiki/HTML/Elements/option) éléments.</span><span class="sxs-lookup"><span data-stu-id="0ddff-278">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="0ddff-279">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0ddff-279">For example:</span></span>

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="0ddff-280">Aperçu :</span><span class="sxs-lookup"><span data-stu-id="0ddff-280">Sample:</span></span>

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="0ddff-281">Le `Index` méthode initialise la `CountryViewModel`, définit le pays et le passe à la `Index` vue.</span><span class="sxs-lookup"><span data-stu-id="0ddff-281">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

<span data-ttu-id="0ddff-282">La requête HTTP POST `Index` méthode affiche la sélection :</span><span class="sxs-lookup"><span data-stu-id="0ddff-282">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="0ddff-283">Le `Index` vue :</span><span class="sxs-lookup"><span data-stu-id="0ddff-283">The `Index` view:</span></span>

[!code-cshtml[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="0ddff-284">Qui génère le code HTML suivant (avec « AC » sélectionnée) :</span><span class="sxs-lookup"><span data-stu-id="0ddff-284">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
```

> [!NOTE]
> <span data-ttu-id="0ddff-285">Nous ne recommandons pas à l’aide de `ViewBag` ou `ViewData` avec l’assistance de balise sélectionnez.</span><span class="sxs-lookup"><span data-stu-id="0ddff-285">We do not recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="0ddff-286">Un modèle d’affichage est plus fiable en fournissant des métadonnées de MVC et généralement moins problématique.</span><span class="sxs-lookup"><span data-stu-id="0ddff-286">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="0ddff-287">Le `asp-for` valeur d’attribut est un cas spécial et ne nécessite pas un `Model` de préfixe, les autres ne d’attributs d’assistance de balise (tels que `asp-items`)</span><span class="sxs-lookup"><span data-stu-id="0ddff-287">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="0ddff-288">Liaison d’enum</span><span class="sxs-lookup"><span data-stu-id="0ddff-288">Enum binding</span></span>

<span data-ttu-id="0ddff-289">Il est souvent pratique d’utiliser `<select>` avec un `enum` propriété et générer le `SelectListItem` éléments à partir de la `enum` valeurs.</span><span class="sxs-lookup"><span data-stu-id="0ddff-289">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="0ddff-290">Aperçu :</span><span class="sxs-lookup"><span data-stu-id="0ddff-290">Sample:</span></span>

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="0ddff-291">Le `GetEnumSelectList` méthode génère un `SelectList` objet pour un enum.</span><span class="sxs-lookup"><span data-stu-id="0ddff-291">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="0ddff-292">Vous pouvez la décorer avec votre liste d’énumérateurs le `Display` attribut pour obtenir une interface utilisateur plus riche :</span><span class="sxs-lookup"><span data-stu-id="0ddff-292">You can decorate your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="0ddff-293">Le code HTML suivant est généré :</span><span class="sxs-lookup"><span data-stu-id="0ddff-293">The following HTML is generated:</span></span>

```HTML
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
    </form>
```

### <a name="option-group"></a><span data-ttu-id="0ddff-294">Groupe d’options</span><span class="sxs-lookup"><span data-stu-id="0ddff-294">Option Group</span></span>

<span data-ttu-id="0ddff-295">Le code HTML [ \<optgroup >](https://www.w3.org/wiki/HTML/Elements/optgroup) élément est généré lorsque le modèle d’affichage contient un ou plusieurs `SelectListGroup` objets.</span><span class="sxs-lookup"><span data-stu-id="0ddff-295">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="0ddff-296">Le `CountryViewModelGroup` groupes le `SelectListItem` éléments dans les groupes « Amérique du Nord » et « Europe » :</span><span class="sxs-lookup"><span data-stu-id="0ddff-296">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="0ddff-297">Les deux groupes sont indiquées ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="0ddff-297">The two groups are shown below:</span></span>

![exemple de groupe d’option](working-with-forms/_static/grp.png)

<span data-ttu-id="0ddff-299">Le code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="0ddff-299">The generated HTML:</span></span>

```HTML
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="0ddff-300">Sélection multiple</span><span class="sxs-lookup"><span data-stu-id="0ddff-300">Multiple select</span></span>

<span data-ttu-id="0ddff-301">L’assistance de balise sélectionnez génère automatiquement le [plusieurs = « plusieurs »](http://w3c.github.io/html-reference/select.html) attribut si la propriété spécifiée dans le `asp-for` attribut est un `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="0ddff-301">The Select Tag Helper  will automatically generate the [multiple = "multiple"](http://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="0ddff-302">Par exemple, étant donné le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="0ddff-302">For example, given the following model:</span></span>

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="0ddff-303">Avec l’affichage suivant :</span><span class="sxs-lookup"><span data-stu-id="0ddff-303">With the following view:</span></span>

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="0ddff-304">Génère le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="0ddff-304">Generates the following HTML:</span></span>

```HTML
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

### <a name="no-selection"></a><span data-ttu-id="0ddff-305">Aucune sélection</span><span class="sxs-lookup"><span data-stu-id="0ddff-305">No selection</span></span>

<span data-ttu-id="0ddff-306">Si vous vous trouvez à l’aide de l’option « non spécifiée » dans plusieurs pages, vous pouvez créer un modèle pour vous éviter de répéter le code HTML :</span><span class="sxs-lookup"><span data-stu-id="0ddff-306">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="0ddff-307">Le *Views/Shared/EditorTemplates/CountryViewModel.cshtml* modèle :</span><span class="sxs-lookup"><span data-stu-id="0ddff-307">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="0ddff-308">Ajout de code HTML [ \<option >](https://www.w3.org/wiki/HTML/Elements/option) éléments n’est pas limitée à la *aucune sélection* cas.</span><span class="sxs-lookup"><span data-stu-id="0ddff-308">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements is not limited to the *No selection* case.</span></span> <span data-ttu-id="0ddff-309">Par exemple, la méthode d’affichage et l’action suivante sera génèrent du code HTML semblable au code ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="0ddff-309">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="0ddff-310">Le bon `<option>` élément est sélectionné (contiennent le `selected="selected"` attribut) selon l’actuel `Country` valeur.</span><span class="sxs-lookup"><span data-stu-id="0ddff-310">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

```HTML
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="0ddff-311">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0ddff-311">Additional Resources</span></span>

* [<span data-ttu-id="0ddff-312">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="0ddff-312">Tag Helpers</span></span>](tag-helpers/intro.md)

* [<span data-ttu-id="0ddff-313">Élément de formulaire HTML</span><span class="sxs-lookup"><span data-stu-id="0ddff-313">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)

* [<span data-ttu-id="0ddff-314">Jeton de demande de vérification</span><span class="sxs-lookup"><span data-stu-id="0ddff-314">Request Verification Token</span></span>](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)

* [<span data-ttu-id="0ddff-315">Liaison de données</span><span class="sxs-lookup"><span data-stu-id="0ddff-315">Model Binding</span></span>](../models/model-binding.md)

* [<span data-ttu-id="0ddff-316">Validation du modèle</span><span class="sxs-lookup"><span data-stu-id="0ddff-316">Model Validation</span></span>](../models/validation.md)

* [<span data-ttu-id="0ddff-317">annotations de données</span><span class="sxs-lookup"><span data-stu-id="0ddff-317">data annotations</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)

* <span data-ttu-id="0ddff-318">[Extraits de code pour ce document de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/forms/sample).</span><span class="sxs-lookup"><span data-stu-id="0ddff-318">[Code snippets for this document](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/forms/sample).</span></span>

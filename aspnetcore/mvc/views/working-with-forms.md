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
# <a name="introduction-to-using-tag-helpers-in-forms-in-aspnet-core"></a>Introduction à l’utilisation de programmes d’assistance de balise dans les formulaires dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Dave Paquette](https://twitter.com/Dave_Paquette), et [Jerrie Pelser](https://github.com/jerriep)

Ce document illustre l’utilisation de formulaires et les éléments HTML couramment utilisés dans un formulaire. Le code HTML [formulaire](https://www.w3.org/TR/html401/interact/forms.html) élément fournit l’utilisation d’applications web mécanisme principal pour publier des données sur le serveur. La majeure partie de ce document décrit [programmes d’assistance de balise](tag-helpers/intro.md) et comment ils peuvent vous aider à productive créer des formulaires HTML robustes. Nous vous conseillons de lire [Introduction aux programmes d’assistance de balise](tag-helpers/intro.md) avant de lire ce document.

Dans de nombreux cas, les programmes d’assistance HTML fournissent une approche alternative à une application d’assistance de balise spécifique, mais il est important de reconnaître que les programmes d’assistance de balise ne remplacent pas les programmes d’assistance HTML et n’est pas un programme d’assistance de balise pour chaque programme d’assistance HTML. Lorsqu’un autre programme d’assistance HTML existe, il est indiqué.

<a name=my-asp-route-param-ref-label></a>

## <a name="the-form-tag-helper"></a>L’application d’assistance de balise de formulaire

Le [formulaire](https://www.w3.org/TR/html401/interact/forms.html) d’assistance de balise :

* Génère le code HTML [ \<formulaire >](https://www.w3.org/TR/html401/interact/forms.html) `action` valeur d’attribut pour un itinéraire nommé ou une action de contrôleur MVC

* Génère un texte masqué [jeton de demande de vérification](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) pour empêcher la falsification de requête (lorsqu’il est utilisé avec le `[ValidateAntiForgeryToken]` attribut dans la méthode d’action HTTP Post)

* Fournit la `asp-route-<Parameter Name>` attribut, où `<Parameter Name>` est ajoutée pour les valeurs d’itinéraire. Le `routeValues` paramètres `Html.BeginForm` et `Html.BeginRouteForm` fournissent des fonctionnalités similaires.

* Possède une alternative de programme d’assistance HTML `Html.BeginForm` et`Html.BeginRouteForm`

Aperçu :

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

L’assistance de balise de formulaire ci-dessus génère le code HTML suivant :

```HTML
<form method="post" action="/Demo/Register">
     <!-- Input and Submit elements -->
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
    </form>
```

Le runtime MVC génère le `action` valeur d’attribut à partir des attributs d’assistance de balise de formulaire `asp-controller` et `asp-action`. L’application d’assistance de balise de formulaire génère également un masqué [jeton de demande de vérification](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) pour empêcher la falsification de requête (lorsqu’il est utilisé avec le `[ValidateAntiForgeryToken]` attribut dans la méthode d’action HTTP Post). Un formulaire HTML pur empêcher la falsification de requête est difficile, l’application d’assistance de balise de formulaire fournit ce service pour vous.

### <a name="using-a-named-route"></a>À l’aide d’un itinéraire nommé

Le `asp-route` attribut d’assistance de balise peut également générer le balisage pour le code HTML `action` attribut. Une application avec un [itinéraire](../../fundamentals/routing.md) nommé `register` Impossible d’utiliser le balisage suivant pour la page d’inscription :

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

Un grand nombre des vues dans le *Views/Account* dossier (généré lorsque vous créez une application web avec *comptes d’utilisateur individuels*) contiennent le [asp-itinéraire-returnurl](https://docs.microsoft.com/aspnet/core/mvc/views/working-with-forms) attribut :

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>Avec les modèles intégrés, `returnUrl` n’est rempli automatiquement lorsque vous essayez d’accéder à une ressource autorisée, mais ne sont pas authentifiés ou autorisés. Lorsque vous essayez d’un accès non autorisé, l’intergiciel (middleware) de sécurité vous redirige vers la page de connexion avec le `returnUrl` défini.

## <a name="the-input-tag-helper"></a>L’application d’assistance de balise d’entrée

L’application d’assistance de balise d’entrée est liée à un élément HTML [ \<d’entrée >](https://www.w3.org/wiki/HTML/Elements/input) élément à une expression de modèle dans votre affichage razor.

Syntaxe :

```HTML
<input asp-for="<Expression Name>" />
```

L’application d’assistance de balise d’entrée :

* Génère le `id` et `name` attributs HTML pour le nom de l’expression spécifiée dans le `asp-for` attribut. `asp-for="Property1.Property2"` équivaut à `m => m.Property1.Property2`. Le nom de l’expression est celui qui est utilisé pour la `asp-for` valeur d’attribut. Consultez le [noms d’expressions](#expression-names) section pour plus d’informations.

* Définit le code HTML `type` en fonction du type de modèle de valeur d’attribut et [annotation de données](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributs appliqués à la propriété du modèle

* Ne remplace pas le code HTML `type` valeur d’attribut s’il est spécifié

* Génère [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) validation des attributs à partir de [annotation de données](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributs appliqués aux propriétés de modèle

* Est une fonctionnalité du programme d’assistance HTML se chevauchent avec `Html.TextBoxFor` et `Html.EditorFor`. Consultez le **alternatives de programme d’assistance HTML à l’application d’assistance de balise d’entrée** pour plus d’informations.

* Fournit un typage fort. Si le nom de la propriété est modifiée et que vous ne mettez pas à jour l’application d’assistance de balise vous obtiendrez une erreur semblable au suivant :

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

Le `Input` application d’assistance de balise définit le code HTML `type` attribut basé sur le type .NET. Le tableau suivant répertorie certains types .NET communs et type HTML généré (pas tous les types .NET sont répertorié).

|Type .NET|Type d’entrée|
|---|---|
|Bool|type = « checkbox »|
|Chaîne|type = « text »|
|DateTime|type = « datetime »|
|Byte|type = « number »|
|Int|type = « number »|
|Single, Double|type = « number »|


Le tableau suivant présente certaines commun [annotations de données](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributs l’application d’assistance de balise d’entrée est mappés à des types spécifiques d’entrée (pas de chaque attribut de validation est répertorié) :


|Attribut|Type d’entrée|
|---|---|
|[EmailAddress]|type = « email »|
|[Url]|type = « url »|
|[HiddenInput]|type = « hidden »|
|[Phone]|type = « téléphone »|
|[DataType(DataType.Password)]| type = « password »|
|[DataType(DataType.Date)]| type = « date »|
|[DataType(DataType.Time)]| type = « time »|


Aperçu :

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

Le code ci-dessus génère le code HTML suivant :

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

Les annotations de données appliquées à la `Email` et `Password` propriétés génèrent des métadonnées sur le modèle. Consomme les métadonnées du modèle de l’application d’assistance de balise d’entrée et produit [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributs (consultez [Validation du modèle](../models/validation.md)). Ces attributs décrivent les validateurs à joindre aux champs d’entrée. Cela fournit discrète HTML5 et [jQuery](https://jquery.com/) validation. Les attributs non obstructifs sont au format `data-val-rule="Error Message"`, où la règle est le nom de la règle de validation (tel que `data-val-required`, `data-val-email`, `data-val-maxlength`, etc..) Si un message d’erreur est fourni dans l’attribut, il est affiché en tant que la valeur de la `data-val-rule` attribut. Il existe également des attributs de la forme `data-val-ruleName-argumentName="argumentValue"` qui fournissent des détails supplémentaires sur la règle, par exemple, `data-val-maxlength-max="1024"` .

### <a name="html-helper-alternatives-to-input-tag-helper"></a>Alternatives de programme d’assistance HTML à l’application d’assistance de balise d’entrée

`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` et `Html.EditorFor` se chevauchent de fonctionnalités avec l’application d’assistance de balise d’entrée. L’application d’assistance de balise d’entrée définira automatiquement le `type` attribut ; `Html.TextBox` et `Html.TextBoxFor` ne seront pas. `Html.Editor`et `Html.EditorFor` gérer les collections, les objets complexes et les modèles ; n’est pas le cas de l’application d’assistance de balise d’entrée. L’application d’assistance de balise d’entrée, `Html.EditorFor` et `Html.TextBoxFor` sont fortement typées (elles utilisent les expressions lambda) ; `Html.TextBox` et `Html.Editor` ne sont pas (ils utilisent des noms d’expressions).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()`et `@Html.EditorFor()` utiliser spéciale `ViewDataDictionary` entrée nommée `htmlAttributes` lors de l’exécution de leurs modèles par défaut. Ce comportement est augmenté éventuellement à l’aide `additionalViewData` paramètres. La clé « htmlAttributes » respecte la casse. La clé « htmlAttributes » est gérée de façon similaire à la `htmlAttributes` objet passé à l’entrée des programmes d’assistance comme `@Html.TextBox()`.

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>Noms d’expressions

Le `asp-for` valeur d’attribut est un `ModelExpression` et la partie droite d’une expression lambda. Par conséquent, `asp-for="Property1"` devient `m => m.Property1` dans le code généré qui est la raison pour laquelle vous n’avez pas besoin avec le préfixe `Model`. Vous pouvez utiliser le « @ » caractère à commencer une expression inline et de passer avant le `m.`:

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe" />
```

Génère les éléments suivants :

```HTML
<input type="text" id="joe" name="joe" value="Joe" />
```

Avec les propriétés de la collection, `asp-for="CollectionProperty[23].Member"` génère le même nom que `asp-for="CollectionProperty[i].Member"` lorsque `i` a la valeur `23`.

### <a name="navigating-child-properties"></a>Navigation dans les propriétés enfants

Vous pouvez également accéder aux propriétés enfant à l’aide du chemin de la propriété du modèle de vue. Considérez une classe de modèle plus complexe qui contient un enfant `Address` propriété.

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

Dans la vue, nous lier à `Address.AddressLine1`:

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

Le code HTML suivant est généré pour `Address.AddressLine1`:

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="" />
```

### <a name="expression-names-and-collections"></a>Noms d’expressions et des Collections

Exemple, un modèle qui contient un tableau de `Colors`:

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

La méthode d’action :

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

Razor suivante montre comment vous accéder à un spécifique `Color` élément :

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

Le *Views/Shared/EditorTemplates/String.cshtml* modèle :

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

Exemple à l’aide de `List<T>`:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

Razor suivante montre comment effectuer une itération au sein d’une collection :

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

Le *Views/Shared/EditorTemplates/ToDoItem.cshtml* modèle :

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]


>[!NOTE]
>Toujours utiliser `for` (et *pas* `foreach`) pour itérer sur une liste. L’évaluation d’un indexeur dans LINQ expression peut être coûteuse et doit être réduite.

&nbsp;

>[!NOTE]
>L’exemple commenté de code ci-dessus illustre la façon de remplacer l’expression lambda avec le `@` pour accéder à chaque opérateur `ToDoItem` dans la liste.

## <a name="the-textarea-tag-helper"></a>L’application d’assistance de balise Textarea

Le `Textarea Tag Helper` application d’assistance de balise est similaire à l’application d’assistance de balise d’entrée.

* Génère le `id` et `name` attributs et les attributs de validation de données à partir du modèle pour un [ \<textarea >](https://www.w3.org/wiki/HTML/Elements/textarea) élément.

* Fournit un typage fort.

* Solution de programme d’assistance HTML :`Html.TextAreaFor`

Aperçu :

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

Le code HTML suivant est généré :

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

## <a name="the-label-tag-helper"></a>L’application d’assistance de balise étiquette

* Génère la légende de l’étiquette et `for` d’attribut sur un [ <label> ](https://www.w3.org/wiki/HTML/Elements/label) élément d’un nom de l’expression

* Alternative de programme d’assistance HTML : `Html.LabelFor`.

Le `Label Tag Helper` offre les avantages suivants sur un élément d’étiquette HTML pur :

* Vous obtenez automatiquement la valeur de l’étiquette descriptive à partir de la `Display` attribut. Le nom d’affichage souhaité peut changer dans le temps et la combinaison de `Display` attribut et le programme d’assistance de balise étiquette seront applique le `Display` partout où il est utilisé.

* Moins de balisage dans le code source

* Fort en tapant avec la propriété du modèle.

Aperçu :

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

Le code HTML suivant est généré pour le `<label>` élément :

```HTML
<label for="Email">Email Address</label>
```

L’application d’assistance de balise étiquette généré le `for` valeur d’attribut de « Email », qui est l’ID associé à le `<input>` élément. Les programmes d’assistance de balise générer cohérent `id` et `for` éléments de sorte qu’ils peuvent être correctement associés. Le libellé dans cet exemple provient de la `Display` attribut. Si le modèle ne contenait pas un `Display` attribut, la légende est le nom de propriété de l’expression.

## <a name="the-validation-tag-helpers"></a>Les programmes d’assistance de balise de Validation

Il existe deux programmes d’assistance de balise de Validation. Le `Validation Message Tag Helper` (qui affiche un message de validation d’une propriété unique dans votre modèle) et le `Validation Summary Tag Helper` (qui affiche un résumé des erreurs de validation). Le `Input Tag Helper` ajoute des attributs de validation client côté HTML5 pour entrer des éléments en fonction des données, les attributs d’annotations dans vos classes de modèle. La validation est également effectuée sur le serveur. L’application d’assistance de balise de Validation affiche ces messages d’erreur lorsqu’une erreur de validation se produit.

### <a name="the-validation-message-tag-helper"></a>L’assistance de balise de Message de Validation

* Ajoute le [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` d’attribut pour le [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) élément, ce qui joint les messages d’erreur de validation sur le champ d’entrée de la propriété de modèle spécifié. Lorsqu’une erreur de validation côté client se produit, [jQuery](https://jquery.com/) affiche le message d’erreur dans le `<span>` élément.

* La validation a également lieu sur le serveur. Les clients peuvent avoir désactivé JavaScript et une validation peut uniquement être effectuée sur le côté serveur.

* Solution de programme d’assistance HTML :`Html.ValidationMessageFor`

Le `Validation Message Tag Helper` est utilisé avec le `asp-validation-for` attribut sur un HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) élément.

```HTML
<span asp-validation-for="Email"></span>
```

L’application d’assistance de balise de Message de Validation génère le code HTML suivant :

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

Vous utilisez généralement la `Validation Message Tag Helper` après une `Input` assistance de balise pour la même propriété. Cela affiche des messages d’erreur de validation près de l’entrée qui a provoqué l’erreur.

> [!NOTE]
> Vous devez disposer d’une vue avec le code JavaScript approprié et [jQuery](https://jquery.com/) références de script en place pour la validation côté client. Consultez [Validation du modèle](../models/validation.md) pour plus d’informations.

Lorsqu’une erreur de validation côté serveur produit (par exemple lorsque vous disposez validation personnalisée côté serveur ou la validation côté client est désactivée), MVC place ce message d’erreur dans le corps de la `<span>` élément.

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>L’application auxiliaire de Validation Summary (balise)

* Cibles `<div>` éléments avec le `asp-validation-summary` attribut

* Solution de programme d’assistance HTML :`@Html.ValidationSummary`

Le `Validation Summary Tag Helper` est utilisé pour afficher un résumé des messages de validation. Le `asp-validation-summary` valeur d’attribut peut être une des opérations suivantes :

|ASP-validation-résumé|Affichées des messages de validation|
|--- |--- |
|ValidationSummary.All|Niveau de la propriété et le modèle|
|ValidationSummary.ModelOnly|Modèle|
|ValidationSummary.None|Aucune|

### <a name="sample"></a>Exemple

Dans l’exemple suivant, le modèle de données est décoré avec `DataAnnotation` attributs, ce qui génère des messages d’erreur de validation sur le `<input>` élément.  Lorsqu’une erreur de validation se produit, l’application d’assistance de balise de Validation affiche le message d’erreur :

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

Le code HTML généré (lorsque le modèle est valid) :

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

## <a name="the-select-tag-helper"></a>L’application d’assistance de balise Select

* Génère [sélectionnez](https://www.w3.org/wiki/HTML/Elements/select) et [option](https://www.w3.org/wiki/HTML/Elements/option) éléments pour les propriétés de votre modèle.

* Possède une alternative de programme d’assistance HTML `Html.DropDownListFor` et`Html.ListBoxFor`

Le `Select Tag Helper` `asp-for` Spécifie le nom de propriété de modèle pour le [sélectionnez](https://www.w3.org/wiki/HTML/Elements/select) élément et `asp-items` Spécifie le [option](https://www.w3.org/wiki/HTML/Elements/option) éléments.  Exemple :

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

Aperçu :

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

Le `Index` méthode initialise la `CountryViewModel`, définit le pays et le passe à la `Index` vue.

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

La requête HTTP POST `Index` méthode affiche la sélection :

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

Le `Index` vue :

[!code-cshtml[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

Qui génère le code HTML suivant (avec « AC » sélectionnée) :

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
> Nous ne recommandons pas à l’aide de `ViewBag` ou `ViewData` avec l’assistance de balise sélectionnez. Un modèle d’affichage est plus fiable en fournissant des métadonnées de MVC et généralement moins problématique.

Le `asp-for` valeur d’attribut est un cas spécial et ne nécessite pas un `Model` de préfixe, les autres ne d’attributs d’assistance de balise (tels que `asp-items`)

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Liaison d’enum

Il est souvent pratique d’utiliser `<select>` avec un `enum` propriété et générer le `SelectListItem` éléments à partir de la `enum` valeurs.

Aperçu :

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

Le `GetEnumSelectList` méthode génère un `SelectList` objet pour un enum.

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

Vous pouvez la décorer avec votre liste d’énumérateurs le `Display` attribut pour obtenir une interface utilisateur plus riche :

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

Le code HTML suivant est généré :

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

### <a name="option-group"></a>Groupe d’options

Le code HTML [ \<optgroup >](https://www.w3.org/wiki/HTML/Elements/optgroup) élément est généré lorsque le modèle d’affichage contient un ou plusieurs `SelectListGroup` objets.

Le `CountryViewModelGroup` groupes le `SelectListItem` éléments dans les groupes « Amérique du Nord » et « Europe » :

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

Les deux groupes sont indiquées ci-dessous :

![exemple de groupe d’option](working-with-forms/_static/grp.png)

Le code HTML généré :

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

### <a name="multiple-select"></a>Sélection multiple

L’assistance de balise sélectionnez génère automatiquement le [plusieurs = « plusieurs »](http://w3c.github.io/html-reference/select.html) attribut si la propriété spécifiée dans le `asp-for` attribut est un `IEnumerable`. Par exemple, étant donné le modèle suivant :

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

Avec l’affichage suivant :

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

Génère le code HTML suivant :

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

### <a name="no-selection"></a>Aucune sélection

Si vous vous trouvez à l’aide de l’option « non spécifiée » dans plusieurs pages, vous pouvez créer un modèle pour vous éviter de répéter le code HTML :

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

Le *Views/Shared/EditorTemplates/CountryViewModel.cshtml* modèle :

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

Ajout de code HTML [ \<option >](https://www.w3.org/wiki/HTML/Elements/option) éléments n’est pas limitée à la *aucune sélection* cas. Par exemple, la méthode d’affichage et l’action suivante sera génèrent du code HTML semblable au code ci-dessus :

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

Le bon `<option>` élément est sélectionné (contiennent le `selected="selected"` attribut) selon l’actuel `Country` valeur.

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

## <a name="additional-resources"></a>Ressources supplémentaires

* [Tag Helpers](tag-helpers/intro.md)

* [Élément de formulaire HTML](https://www.w3.org/TR/html401/interact/forms.html)

* [Jeton de demande de vérification](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)

* [Liaison de données](../models/model-binding.md)

* [Validation du modèle](../models/validation.md)

* [annotations de données](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)

* [Extraits de code pour ce document de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/forms/sample).

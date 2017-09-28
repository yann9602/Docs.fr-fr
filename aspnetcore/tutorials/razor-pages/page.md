---
title: "Pages Razor obtenues par génération de modèles automatique dans ASP.NET Core"
author: rick-anderson
description: "Décrit l’obtention de pages Razor par génération de modèles automatique."
keywords: ASP.NET Core,pages Razor,Razor,MVC
ms.author: riande
manager: wpickett
ms.date: 07/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/page
ms.openlocfilehash: 77462ede7b88ed22695b9ea701a7333e1667e548
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2017
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a>Pages Razor obtenues par génération de modèles automatique dans ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel décrit les pages Razor créées par génération de modèles automatique au cours du [didacticiel précédent](xref:tutorials/razor-pages/page). 

[Affichez ou téléchargez](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) l’exemple.

## <a name="the-create-delete-details-and-edit-pages"></a>Pages Create, Delete, Details et Edit.

Examinez le fichier code-behind *Pages/Movies/Index.cshtml.cs* : [!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml.cs)]

Les pages Razor sont dérivées de `PageModel`. Par convention, la classe dérivée de `PageModel` s’appelle `<PageName>Model`. Le constructeur utilise l’[injection de dépendances](xref:fundamentals/dependency-injection) pour ajouter `MovieContext` à la page. Toutes les pages obtenues par génération de modèles automatique suivent ce modèle.

Quand une requête est effectuée pour la page, la méthode `OnGetAsync` retourne une liste de films à la page Razor. `OnGetAsync` ou `OnGet` est appelé sur une page Razor pour initialiser l’état de la page. Dans ce cas, `OnGetAsync` obtient une liste de films à afficher.

Examinez la page Razor *Pages/Movies/Index.cshtml* :

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml)]

Razor peut passer du HTML au C# ou à des balises spécifiques à Razor. Quand un symbole `@` est suivi d’un [mot clé réservé Razor](xref:mvc/views/razor#razor-reserved-keywords), il est converti en balise spécifique à Razor. Sinon, il est converti en C#.

La directive Razor `@page` transforme le fichier en action MVC &mdash;, ce qui lui permet de prendre en charge les requêtes. `@page` doit être la première directive Razor sur une page. `@page` est un exemple de conversion en balise spécifique à Razor. Pour plus d’informations, consultez [Syntaxe Razor](xref:mvc/views/razor#razor-syntax).

Examinez l’expression lambda utilisée dans le HTML Helper suivant :

`@Html.DisplayNameFor(model => model.Movie[0].Title))`

Le HTML Helper `DisplayNameFor` inspecte la propriété `Title` référencée dans l’expression lambda pour déterminer le nom d’affichage. L’expression lambda est inspectée plutôt qu’évaluée. Cela signifie qu’il n’existe pas de violation d’accès quand `model`, `model.Movies` ou `model.Movies[0]` ont une valeur `null` ou vide. Quand l’expression lambda est évaluée (par exemple avec `@Html.DisplayFor(modelItem => item.Title)`), les valeurs de propriété du modèle sont évaluées.

<a name="md"></a>
### <a name="the-model-directive"></a>Directive @model

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml?range=1-2&highlight=2)]

La directive `@model` spécifie le type du modèle passé à la page Razor. Dans l’exemple précédent, la ligne `@model` rend la classe dérivée `PageModel` accessible à la page Razor. Le modèle est utilisé dans les [HTML Helpers](https://docs.microsoft.com/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` et `@Html.DisplayName` de la page.

<!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>
### ViewData et disposition

Examinons le code ci-dessous.

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml?range=1-6&highlight=4-)]

Le code précédent en surbrillance est un exemple de passage de Razor au C#. Les caractères `{` et `}` délimitent un bloc de code C#.

La classe de base `Controller` a une propriété de dictionnaire `ViewData` qui permet d’ajouter des données à passer à une vue. Vous pouvez ajouter des objets au dictionnaire `ViewData` à l’aide d’un modèle clé/valeur. Dans l’exemple précédent, la propriété « Title » est ajoutée au dictionnaire `ViewData`. La propriété « Title » est utilisée dans le fichier *Pages/_Layout.cshtml*. La balise suivante montre les premières lignes du fichier *Pages/_Layout.cshtml*.

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout1.cshtml?highlight=6-)]

La ligne `@*Markup removed for brevity.*@` est un commentaire Razor. Contrairement aux commentaires HTML (`<!-- -->`), les commentaires Razor ne sont pas envoyés au client.

Exécutez l’application, puis testez les liens du projet (**Home**, **About**, **Contact**, **Create**, **Edit** et **Delete**). Chaque page définit le titre, que vous pouvez voir dans l’onglet du navigateur. Quand vous définissez un signet pour une page, le titre est affecté au signet. *Pages/Index.cshtml* et *Pages/Movies/Index.cshtml* ont le même titre, mais vous pouvez les modifier pour avoir des valeurs distinctes.

La propriété `Layout` est définie dans le fichier *Pages/_ViewStart.cshtml* :

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]

Le code précédent définit le fichier de disposition *Pages/_Layout.cshtml* pour tous les fichiers Razor du dossier *Pages*. Pour plus d’informations, consultez [Disposition](xref:mvc/razor-pages/index#layout).

### <a name="update-the-layout"></a>Mettre à jour la disposition

Changez l’élément `<title>` du fichier *Pages/_Layout.cshtml* pour utiliser une chaîne plus courte.

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=1-6&highlight=6-)]

Recherchez l’élément anchor suivant dans le fichier *Pages/_Layout.cshtml*.

```cshtml
<a asp-page="/Index" class="navbar-brand">RazorPagesMovie</a>
```
Remplacez l’élément précédent par le code suivant.

```cshtml
<a asp-page="/Movies/Index" class="navbar-brand">RpMovie</a>
```

L’élément anchor précédent est un [Tag Helper](xref:mvc/views/tag-helpers/intro). Dans le cas présent, il s’agit du [Tag Helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper). L’attribut et la valeur du Tag Helper `asp-page="/Movies/Index"` créent un lien vers la page Razor `/Movies/Index`.

Enregistrez vos changements, puis testez l’application en cliquant sur le lien **RpMovie**. Consultez le fichier [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml) dans GitHub.

### <a name="the-create-code-behind-page"></a>Page code-behind Create

Examinez le fichier code-behind *Pages/Movies/Create.cshtml.cs* :

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml.cs?name=snippetALL)]

La méthode `OnGet` initialise l’état nécessaire pour la page. La page Create n’a aucun état à initialiser. La méthode `Page` crée un objet `PageResult` qui affiche la page *Create.cshtml*.

La propriété `Movie` utilise l’attribut `[BindProperty]` pour adhérer à la [liaison de données](xref:mvc/models/model-binding). Quand le formulaire Create publie les valeurs de formulaire, le runtime ASP.NET Core lie les valeurs publiées au modèle `Movie`.

La méthode `OnPostAsync` est exécutée quand la page publie les données de formulaire :

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml.cs?name=snippetPost)]

S’il existe des erreurs liées au modèle, le formulaire est réaffiché, ainsi que toutes les données de formulaire publiées. La plupart des erreurs de modèle peuvent être interceptées côté client avant la publication du formulaire. Voici un exemple d’erreur de modèle : la publication pour le champ de date d’une valeur qui ne peut pas être convertie en date. Nous aborderons de manière plus détaillée la validation côté client et la validation du modèle plus loin dans le didacticiel.

S’il n’existe pas d’erreurs de modèle, les données sont enregistrées et le navigateur est redirigé vers la page Index.

### <a name="the-create-razor-page"></a>Page Razor Create

Examinez le fichier de la page Razor *Pages/Movies/Create.cshtml* :

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml)]

Visual Studio affiche la balise `<form method="post">` dans une police distinctive utilisée pour les Tag Helpers. L’élément `<form method="post">` est un [Tag Helper de formulaire](xref:mvc/views/working-with-forms#the-form-tag-helper). Le Tag Helper de formulaire inclut automatiquement un [jeton de protection contre les falsifications](xref:security/anti-request-forgery).

![Vue VS17 de la page Create.cshtml](page/_static/th.png)

Le moteur de génération de modèles automatique crée le code Razor pour chaque champ du modèle (sauf l’ID) de la manière suivante :

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml?range=15-20)]

Les [Tag Helpers de validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` et ` <span asp-validation-for`) affichent des erreurs de validation. La validation est traitée de manière plus détaillée plus loin dans cette série.

Le [Tag Helper d’étiquette](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) génère la légende de l’étiquette et l’attribut `for` pour la propriété `Title`.

Le [Tag Helper d’entrée](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) utilise les attributs [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) et produit les attributs HTML nécessaires à la validation jQuery côté client.

Le prochain didacticiel décrit SQL Server LocalDB et la création des valeurs initiales de la base de données.

>[!div class="step-by-step"]
[Précédent : Ajout d’un modèle](xref:tutorials/razor-pages/modelz)
[Suivant : SQL Server LocalDB](xref:tutorials/razor-pages/sql)

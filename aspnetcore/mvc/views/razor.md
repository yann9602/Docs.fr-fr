---
title: "Référence de la syntaxe Razor pour ASP.NET Core"
author: rick-anderson
description: "Détails de la syntaxe Razor"
keywords: ASP.NET Core, Razor
ms.author: riande
manager: wpickett
ms.date: 07/04/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/razor
ms.openlocfilehash: 0e65f0e9f672f9f93256ebc039ea0db2e4ef5ae0
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="razor-syntax-for-aspnet-core"></a>Syntaxe Razor pour ASP.NET Core

Par [Taylor Mullen](https://twitter.com/ntaylormullen) et [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-is-razor"></a>Nouveautés Razor

Razor est une syntaxe de balisage pour l’incorporation de code en fonction de serveur dans les pages web. La syntaxe Razor se compose d’un balisage Razor, HTML et c#. Les fichiers contenant Razor généralement ont un *.cshtml* extension de fichier.

## <a name="rendering-html"></a>Rendu HTML

La langue de Razor par défaut est HTML. Rendu HTML à partir de Razor n’est pas différente dans un fichier HTML. Un fichier Razor avec le balisage suivant :

```html
<p>Hello World</p>
```

Rendu inchangée `<p>Hello World</p>` par le serveur.

## <a name="razor-syntax"></a>Syntaxe Razor

Razor prend en charge de c# et utilise le `@` symbole pour effectuer la transition du code HTML en c#. Razor évalue les expressions c# et les affiche dans la sortie HTML. Razor peut passer du HTML au C# ou à des balises spécifiques à Razor. Lorsqu’un `@` symbole est suivi d’un [Razor de mot clé réservé](#razor-reserved-keywords) transition vers le balisage spécifique Razor, sinon il passe à c# ordinaire.

<a name=escape-at-label></a>

HTML contenant `@` symboles peuvent doivent être échappés avec un second `@` symbole. Exemple :

```cshtml
<p>@@Username</p>
```

rendrait le code HTML suivant :

```cshtml
<p>@Username</p>
```

<a name=razor-email-ref></a>

Attributs HTML et contenu contenant les adresses de messagerie ne traitent pas les `@` symbole comme un caractère de la transition.

   `<a href="mailto:Support@contoso.com">Support@contoso.com</a>`

## <a name="implicit-razor-expressions"></a>Expressions implicites Razor

Les expressions implicites Razor commencer par `@` suivi par le code c#. Exemple :

```html
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

À l’exception de C# `await` expressions implicites du mot clé ne doivent pas contenir des espaces. Par exemple, des espaces peuvent se mélangent tant que l’instruction c# a une fin claire :

```html
<p>@await DoSomething("hello", "world")</p>
```

## <a name="explicit-razor-expressions"></a>Expressions explicites Razor

Les expressions explicites Razor se compose d’un symbole avec la parenthèse à charge équilibrée. Par exemple, pour afficher l’heure de la semaine dernière :

```html
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Tout contenu dans le @ () parenthèses est évaluée et rendus à la sortie.

En général les expressions implicites ne peuvent pas contenir d’espaces. Par exemple, dans le code ci-dessous, une semaine n’est pas soustraite de l’heure actuelle :

[!code-html[Main](razor/sample/Views/Home/Contact.cshtml?range=20)]

Qui restitue le code HTML suivant :

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Vous pouvez utiliser une expression explicite pour concaténer du texte avec un résultat de l’expression :

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Sans l’expression explicite, `<p>Age@joe.Age</p>` est considérée comme une adresse de messagerie et `<p>Age@joe.Age</p>` est rendu. Lors de l’écriture en tant qu’expression explicite `<p>Age33</p>` est rendu.

<a name=expression-encoding-label></a>

## <a name="expression-encoding"></a>Encodage de l’expression

Les expressions c# qui correspondent à une chaîne sont encodées en HTML. Les expressions c# qui correspondent aux `IHtmlContent` sont rendus directement via *IHtmlContent.WriteTo*. Les expressions c# qui ne correspondent à *IHtmlContent* sont converties en une chaîne (par *ToString*) et encodée avant d’être restitués. Par exemple, le balisage de Razor suivant :

```cshtml
@("<span>Hello World</span>")
```

Cela restitue le HTML :

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

Le navigateur restituée sous :

`<span>Hello World</span>`

`HtmlHelper``Raw` sortie n’est pas encodée mais rendue sous forme de balisage HTML.

>[!WARNING]
> À l’aide de `HtmlHelper.Raw` utilisateur unsanitized entrée est un risque de sécurité. L’entrée d’utilisateur peut contenir malveillant JavaScript ou autres attaques. Il est difficile d’expurgation de l’entrée d’utilisateur, évitez d’utiliser `HtmlHelper.Raw` sur l’entrée d’utilisateur.

Le balisage de Razor suivant :

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Cela restitue le HTML :

```html
<span>Hello World</span>
```

<a name=razor-code-blocks-label></a>

## <a name="razor-code-blocks"></a>Blocs de code Razor

Blocs de code Razor commencent par `@` et sont placés par `{}`. Contrairement aux expressions, le code c# à l’intérieur des blocs de code n’est pas rendu. Blocs de code et des expressions dans une page Razor partagent la même étendue et sont définies dans l’ordre (autrement dit, les déclarations dans un bloc de code sera dans l’étendue pour les blocs de code et des expressions).

```none
@{
    var output = "Hello World";
}

<p>The rendered result: @output</p>
```

Rendrait les :

```html
<p>The rendered result: Hello World</p>
```

<a name=implicit-transitions-label></a>

### <a name="implicit-transitions"></a>Transitions implicites

La langue par défaut dans un bloc de code est c#, mais vous pouvez effectuer la transition en HTML. HTML dans un bloc de code passera au rendu HTML :

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

<a name=explicit-delimited-transition-label></a>

### <a name="explicit-delimited-transition"></a>Transition délimitée explicite

Pour définir une sous-section d’un bloc de code qui doit être rendu HTML, entourer les caractères doivent être rendus avec Razor `<text>` balise :

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

En général, vous utilisez cette approche lorsque vous souhaitez effectuer le rendu HTML n’est pas entouré d’une balise HTML. Sans une balise HTML ou Razor, vous obtenez une erreur d’exécution Razor.

<a name=explicit-line-transition-with-label></a>

### <a name="explicit-line-transition-with-"></a>Transition de ligne explicite avec`@:`

Pour rendre le reste d’une ligne entière à l’intérieur d’un bloc de code HTML, utilisez le `@:` syntaxe :

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Sans le `@:` dans le code ci-dessus, vous obtenez une erreur d’exécution de Razor.

<a name=control-structures-razor-label></a>

## <a name="control-structures"></a>Structures de contrôle

Structures de contrôle sont une extension des blocs de code. Tous les aspects des blocs de code (transition au balisage, inline c#) également s’appliquent aux structures suivantes.

### <a name="conditionals-if-else-if-else-and-switch"></a>Instructions conditionnelles `@if`, `else if`, `else` et`@switch`

Le `@if` famille contrôle quand le code s’exécute :

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even</p>
}
```

`else`et `else if` ne nécessitent pas le `@` symbole :

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value was not large and is odd.</p>
}
```

Vous pouvez utiliser une instruction switch comme suit :

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number was not 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>Bouclage `@for`, `@foreach`, `@while`, et`@do while`

Vous pouvez effectuer le rendu basé sur un modèle HTML avec les instructions de contrôle de boucle. Par exemple, pour afficher une liste de personnes :

```cshtml
@{
    var people = new Person[]
    {
          new Person("John", 33),
          new Person("Doe", 41),
    };
}
```

Vous pouvez utiliser les instructions suivantes en boucle :

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>COMPOSÉS`@using`

En c# à l’aide d’une instruction est utilisée pour vérifier un objet est supprimé. Dans Razor ce même mécanisme peut être utilisé pour créer des programmes d’assistance HTML qui contiennent du contenu supplémentaire. Par exemple, que nous pouvons utiliser des programmes d’assistance HTML pour restituer une balise form avec la `@using` instruction :

```cshtml
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" name="Email" value="" />
        <button type="submit"> Register </button>
    </div>
}
```

Vous pouvez également effectuer des actions au niveau de portée comme ci-dessus avec [programmes d’assistance de balise](tag-helpers/index.md).

### <a name="try-catch-finally"></a>`@try`, `catch`, `finally`

La gestion des exceptions sont similaire à c# :

[!code-html[Main](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor a la possibilité de protéger les sections critiques avec des instructions de verrouillage :

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Commentaires

Razor prend en charge les commentaires c# et HTML. Le balisage suivant :

```cshtml
@{
    /* C# comment. */
    // Another C# comment.
}
<!-- HTML comment -->
```

Est restitué par le serveur en tant que :

```cshtml
<!-- HTML comment -->
```

Commentaires de Razor sont supprimés par le serveur avant que la page est rendue. Razor utilise `@*  *@` pour délimiter les commentaires. Le code suivant est commenté pour le serveur ne va pas afficher tout balisage :

```cshtml
@*
 @{
     /* C# comment. */
     // Another C# comment.
 }
 <!-- HTML comment -->
*@
```

<a name=razor-directives-label></a>

## <a name="directives"></a>Directives

Directives de Razor sont représentées par des expressions implicites avec les éléments suivants de mots clés réservés du `@` symbole. Une directive sera généralement modifier la façon dont une page est analysée ou activer des fonctionnalités différentes au sein de votre page Razor.

Comprendre comment Razor génère du code pour une vue rend plus facile à comprendre comment fonctionnent les directives. Une page Razor est utilisée pour générer un fichier c#. Par exemple, cette page Razor :

[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]

Génère une classe semblable au suivant :

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Hello World";

        WriteLiteral("/r/n<div>Output: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

[Affichage de la classe c# Razor générée pour une vue](#razor-customcompilationservice-label) explique comment afficher cette classe générée.

### `@using`

Le `@using` directive ajoutera c# `using` directive à la page razor généré :

[!code-html[Main](razor/sample/Views/Home/Contact9.cshtml)]

### `@model`

Le `@model` directive spécifie le type du modèle passé à la page Razor. Il utilise la syntaxe suivante :

```cshtml
@model TypeNameOfModel
```

Par exemple, si vous créez une application ASP.NET MVC de base avec des comptes d’utilisateur individuels, le *Views/Account/Login.cshtml* vue Razor contient la déclaration de modèle suivante :

```cshtml
@model LoginViewModel
```

Dans l’exemple précédent de la classe, la classe générée hérite `RazorPage<dynamic>`. En ajoutant un `@model` vous contrôlez ce qui est hérité. Exemple :

```cshtml
@model LoginViewModel
```

Génère la classe suivante

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Exposent des pages Razor un `Model` propriété pour accéder au modèle passé à la page.

```cshtml
<div>The Login Email: @Model.Email</div>
```

Le `@model` directive spécifié le type de cette propriété (en spécifiant la `T` dans `RazorPage<T>` qui dérive de la classe générée pour votre page). Si vous ne spécifiez pas le `@model` directive le `Model` propriété sera de type `dynamic`. La valeur du modèle est passée à la vue à partir du contrôleur. Consultez [fortement typée de modèles et les @model mot clé](../../tutorials/first-mvc-app/adding-model.md#strongly-typed-models-keyword-label) pour plus d’informations.

### `@inherits`

Le `@inherits` directive vous donne le contrôle total de la classe qui hérite de votre page Razor :

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Par exemple, supposons que nous avons eu le type de page Razor personnalisé suivant :

[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]

Razor suivante génèrent `<div>Custom text: Hello World</div>`.

[!code-html[Main](razor/sample/Views/Home/Contact10.cshtml)]

Vous ne pouvez pas utiliser `@model` et `@inherits` sur la même page. Vous pouvez avoir `@inherits` dans un *_ViewImports.cshtml* fichier importe de la page Razor. Par exemple, si votre vue Razor importé suit *_ViewImports.cshtml* fichier :

[!code-html[Main](razor/sample/Views/_ViewImportsModel.cshtml)]

La page suivante de Razor fortement typée

[!code-html[Main](razor/sample/Views/Home/Login1.cshtml)]

Génère ce balisage HTML :

```cshtml
<div>The Login Email: Rick@contoso.com</div>
<div>Custom text: Hello World</div>
```

Lorsqu’il est passé «[Rick@contoso.com](mailto:Rick@contoso.com)» dans le modèle :

   Pour plus d’informations, consultez [Disposition](layout.md).

### `@inject`

Le `@inject` directive vous permet d’injecter un service à partir de votre [conteneur de service](../../fundamentals/dependency-injection.md) dans votre page Razor pour l’utiliser. Consultez [injection de dépendances dans les vues](dependency-injection.md).

<a name="functions"></a>

### `@functions`

Le `@functions` directive permet de vous permet d’ajouter du contenu au niveau de la fonction à votre page Razor. La syntaxe est :

```cshtml
@functions { // C# Code }
```

Exemple :

[!code-html[Main](razor/sample/Views/Home/Contact6.cshtml)]

Génère le code HTML suivant :

```cshtml
<div>From method: Hello</div>
```

Généré Razor c# ressemble à ceci :

[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### `@section`

Le `@section` directive est utilisée conjointement avec la [page de disposition](layout.md) pour activer des vues à restituer le contenu dans les différentes parties de la page HTML rendue. Consultez [Sections](layout.md#layout-sections-label) pour plus d’informations.

## <a name="tag-helpers"></a>Programmes d’assistance de balise

Les éléments suivants [programmes d’assistance de balise](tag-helpers/index.md) directives sont détaillées dans les liens fournis.

* [@addTagHelper](tag-helpers/intro.md#add-helper-label)
* [@removeTagHelper](tag-helpers/intro.md#remove-razor-directives-label)
* [@tagHelperPrefix](tag-helpers/intro.md#prefix-razor-directives-label)

<a name=razor-reserved-keywords-label></a>

## <a name="razor-reserved-keywords"></a>Mots clés de Razor réservé

### <a name="razor-keywords"></a>Mots clés de Razor

* page (nécessite un cœur d’ASP.NET 2.0 et versions ultérieur)
* fonctions
* hérite
* modèle
* section
* application d’assistance (non pris en charge par ASP.NET Core.)

Mots clés de Razor peuvent être précédées de `@(Razor Keyword)`, par exemple `@(functions)`. Consultez l’exemple complet ci-dessous.

### <a name="c-razor-keywords"></a>Mots clés du langage c# Razor

* case
* do
* default
* for
* foreach
* if
* else
* lock
* switch
* try
* catch
* finally
* using
* while

Mots clés du langage c# Razor doivent être double échappement `@(@C# Razor Keyword)`, par exemple `@(@case)`. La première `@` remplace l’analyseur Razor, la seconde `@` remplace l’analyseur c#. Consultez l’exemple complet ci-dessous.

### <a name="reserved-keywords-not-used-by-razor"></a>Mots clés réservés non utilisées par Razor

* namespace
* class

<a name=razor-customcompilationservice-label></a>

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a>Affichage de la classe c# Razor générée pour une vue

Ajoutez la classe suivante à votre projet ASP.NET MVC de base :

[!code-csharp[Main](razor/sample/Services/CustomCompilationService.cs)]

Remplacer la `ICompilationService` ajouté par MVC avec la classe ci-dessus ;

[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=29-33)]

Définir un point d’arrêt sur la `Compile` méthode `CustomCompilationService` et `compilationContent`.

![Vue de compilationContent visualiseur de texte](razor/_static/tvr.png)

<a name="case"></a>
## <a name="view-lookups-and-case-sensitivity"></a>Recherches de vue et de respect de la casse

Le moteur d’affichage Razor effectue des recherches respectant la casse pour les vues. Toutefois, la recherche réelle est déterminée par la source sous-jacente :

* Source en fonction du fichier : 

    * Sur les systèmes d’exploitation avec les systèmes de fichiers de la casse (par exemple, Windows), les recherches de fournisseur de fichier physique sont insensible à la casse. Par exemple `return View("Test")` entraînerait `/Views/Home/Test.cshtml`, `/Views/home/test.cshtml` et toutes les autres variantes de casse seraient découvertes.
    * Sur les systèmes de fichiers respectant la casse, qui inclut la Linux, OS x et `EmbeddedFileProvider`, les recherches respectent la casse. Par exemple, `return View("Test")` recherche spécifiquement `/Views/Home/Test.cshtml`.
        
* Vues précompilés :

   * Avec ASP.Net Core 2.0 et versions ultérieures, de vues précompilés recherche respecte la casse sur tous les systèmes d’exploitation. Le comportement est identique au comportement du fournisseur du fichier physique sous Windows. 
   Remarque : Si les deux vues précompilés diffèrent uniquement par la casse, le résultat de recherche est non déterministe.

Les développeurs sont encouragés à correspondre à la casse des noms de fichiers et de répertoires à la casse des noms de zone, de contrôleur et d’action. Il en résulterait que vos déploiements restent agnostiques du système de fichiers sous-jacent.

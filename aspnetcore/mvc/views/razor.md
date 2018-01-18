---
title: "Référence de la syntaxe Razor pour ASP.NET Core"
author: rick-anderson
description: "En savoir plus sur la syntaxe Razor balisage pour l’incorporation de code serveur dans les pages Web."
keywords: Directives de Razor ASP.NET Core, Razor,
ms.author: riande
manager: wpickett
ms.date: 10/18/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/razor
ms.openlocfilehash: 6df769069fce52755a57d8404f88203a652a1ab9
ms.sourcegitcommit: a3e88639a6bcf8fb4d634036dac93130c464a097
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="razor-syntax-for-aspnet-core"></a>Syntaxe Razor pour ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), et [Dan Vicarel](https://github.com/Rabadash8820)

Razor est une syntaxe de balisage pour l’incorporation de code serveur dans les pages Web. La syntaxe Razor est constitué de Razor balisage, c# et HTML. Les fichiers contenant Razor généralement ont un *.cshtml* extension de fichier.

## <a name="rendering-html"></a>Rendu HTML

La langue de Razor par défaut est HTML. HTML rendu à partir du balisage de Razor n’est pas différente de rendu HTML à partir d’un fichier HTML.  Balisage HTML dans *.cshtml* fichiers Razor est restitué par le serveur sans modification.

## <a name="razor-syntax"></a>Syntaxe Razor

Razor prend en charge de c# et utilise le `@` symbole pour effectuer la transition du code HTML en c#. Razor évalue les expressions c# et les affiche dans la sortie HTML.

Lorsqu’un `@` symbole est suivi d’un [Razor de mot clé réservé](#razor-reserved-keywords), transition vers le balisage spécifique Razor. Sinon, il adopte brut c#.

Pour éviter un `@` de symboles dans le balisage de Razor, utiliser un deuxième `@` symbole :

```cshtml
<p>@@Username</p>
```

Le code est rendu en HTML avec un seul `@` symbole :

```html
<p>@Username</p>
```

Attributs HTML et contenu contenant les adresses de messagerie ne traitent pas les `@` symbole comme un caractère de la transition. Dans l’exemple suivant, les adresses de messagerie sont conservées par Razor l’analyse :

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Expressions implicites Razor

Les expressions implicites Razor commencer par `@` suivi par le code c# :

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

À l’exception de C# `await` (mot clé), les expressions implicites ne doivent pas contenir des espaces. Si l’instruction c# a une fin claire, des espaces peuvent être mélangés :

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Les expressions implicites **ne peut pas** contiennent les génériques c#, comme les caractères entre crochets (`<>`) sont interprétés comme une balise HTML. Le code suivant est **pas** valide :

```cshtml
<p>@GenericMethod<int>()</p>
```

Le code précédent génère une erreur du compilateur semblable à un des éléments suivants :

 * L’élément « int » n’a pas été fermé.  Tous les éléments doivent être à fermeture automatique ou concordent une balise de fin.
 *  Impossible de convertir le groupe de méthodes 'GenericMethod' au type 'object' non-délégué. Souhaitiez-vous appeler la méthode ? » 
 
Appels de méthode générique doivent être encapsulées dans un [expression Razor explicite](#explicit-razor-expressions) ou un [bloc de code Razor](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>Expressions explicites Razor

Les expressions explicites Razor se composent d’un `@` symbole avec la parenthèse à charge équilibrée. Pour afficher l’heure de la semaine dernière, le balisage de Razor suivant est utilisé :

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Tout contenu dans le `@()` parenthèses sont évaluée et rendus à la sortie.

En général les expressions implicites, décrites dans la section précédente, ne peut pas contenir d’espaces. Dans le code suivant, une semaine n’est pas soustraite de l’heure actuelle :

[!code-cshtml[Main](razor/sample/Views/Home/Contact.cshtml?range=17)]

Le code restitue le code HTML suivant :

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Les expressions explicites peuvent être utilisées pour concaténer du texte avec un résultat de l’expression :

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Sans l’expression explicite, `<p>Age@joe.Age</p>` est traité comme une adresse de messagerie, et `<p>Age@joe.Age</p>` est rendu. Lors de l’écriture en tant qu’expression explicite `<p>Age33</p>` est rendu.


Les expressions explicites peuvent être utilisées pour afficher la sortie à partir des méthodes génériques dans *.cshtml* fichiers. Dans une expression implicite, les caractères entre crochets (`<>`) sont interprétés comme une balise HTML. Le balisage suivant est **pas** Razor valide :

```cshtml
<p>@GenericMethod<int>()</p>
```

Le code précédent génère une erreur du compilateur semblable à un des éléments suivants :

 * L’élément « int » n’a pas été fermé.  Tous les éléments doivent être à fermeture automatique ou concordent une balise de fin.
 *  Impossible de convertir le groupe de méthodes 'GenericMethod' au type 'object' non-délégué. Souhaitiez-vous appeler la méthode ? » 
 
 Le balisage suivant illustre l’écriture de façon correcte ce code.  Le code est écrit en tant qu’expression explicite :

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Encodage de l’expression

Les expressions c# qui correspondent à une chaîne sont encodées en HTML. Les expressions c# qui correspondent aux `IHtmlContent` sont rendus directement via `IHtmlContent.WriteTo`. Les expressions c# qui ne correspondent à `IHtmlContent` sont converties en une chaîne en `ToString` et codées avant leur rendus.

```cshtml
@("<span>Hello World</span>")
```

Le code restitue le code HTML suivant :

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

Le code HTML est indiqué dans le navigateur en tant que :

```
<span>Hello World</span>
```

`HtmlHelper.Raw`sortie n’est pas encodée mais sont rendue sous forme de balisage HTML.

> [!WARNING]
> À l’aide de `HtmlHelper.Raw` utilisateur unsanitized entrée est un risque de sécurité. L’entrée d’utilisateur peut contenir malveillant JavaScript ou autres attaques. Il est difficile d’expurgation de l’entrée d’utilisateur. Évitez d’utiliser `HtmlHelper.Raw` avec l’entrée d’utilisateur.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Le code restitue le code HTML suivant :

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Blocs de code Razor

Blocs de code Razor commencent par `@` et sont placés par `{}`. Contrairement aux expressions, le code c# à l’intérieur des blocs de code n’est pas rendu. Blocs de code et des expressions dans une vue de partagent la même portée et sont définies dans l’ordre :

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

Le code restitue le code HTML suivant :

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

### <a name="implicit-transitions"></a>Transitions implicites

La langue par défaut dans un bloc de code est c#, mais la Page Razor peut effectuer la transition en HTML :

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Transition délimitée explicite

Pour définir une sous-section d’un bloc de code qui doit être rendu HTML, entourer les caractères pour le rendu de Razor  **\<texte >** balise :

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Utilisez cette approche pour le rendu HTML n’est pas entourée d’une balise HTML. Sans une balise HTML ou Razor, une erreur d’exécution Razor se produit.

Le  **\<texte >** balise est utile pour contrôler l’espace blanc lors du rendu du contenu :

* Seul le contenu entre le  **\<texte >** balise est affichée. 
* Aucun espace blanc avant ou après le  **\<texte >** étiquette s’affiche dans la sortie HTML.

### <a name="explicit-line-transition-with-"></a>Transition de ligne explicite avec @:

Pour rendre le reste d’une ligne entière à l’intérieur d’un bloc de code HTML, utilisez le `@:` syntaxe :

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Sans le `@:` dans le code, une erreur d’exécution Razor est générée.

Avertissement : Extra `@` caractères dans un fichier Razor risque de provoquer des erreurs du compilateur au niveau des instructions plus loin dans le bloc. Ces erreurs du compilateur peuvent être difficiles à comprendre, car l’erreur se produit avant l’erreur signalée.  Cette erreur est courant après la combinaison de plusieurs expressions implicite/explicite dans un bloc de code unique.

## <a name="control-structures"></a>Structures de contrôle

Structures de contrôle sont une extension des blocs de code. Tous les aspects des blocs de code (transition au balisage, inline c#) également s’appliquent aux structures suivantes :

### <a name="conditionals-if-else-if-else-and-switch"></a>Instructions conditionnelles @if, sinon si, l’autre, et@switch

`@if`détermine à quel moment le code s’exécute :

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else`et `else if` ne nécessitent pas le `@` symbole :

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

Le balisage suivant montre comment utiliser une instruction switch :

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
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>Bouclage @for, @foreach, @while, et @do tandis que

Basé sur un modèle HTML peut être rendu avec des instructions de contrôle de boucle.  Pour afficher une liste de personnes :

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

Les instructions de boucles suivantes sont prises en charge :

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

### <a name="compound-using"></a>COMPOSÉS@using

En c#, un `using` instruction est utilisée pour vérifier un objet est supprimé. Dans Razor, le même mécanisme est utilisé pour créer des programmes d’assistance HTML qui contiennent du contenu supplémentaire. Dans le code suivant, les programmes d’assistance HTML restituer une balise form avec la `@using` instruction :


```cshtml
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

Actions au niveau de l’étendue peuvent être effectuées avec [programmes d’assistance de balise](xref:mvc/views/tag-helpers/intro).

### <a name="try-catch-finally"></a>@try, catch, finally

La gestion des exceptions sont similaire à c# :

[!code-cshtml[Main](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

Razor a la possibilité de protéger les sections critiques avec des instructions de verrouillage :

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Commentaires

Razor prend en charge les commentaires HTML et c# :

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Le code restitue le code HTML suivant :

```html
<!-- HTML comment -->
```

Commentaires de Razor sont supprimés par le serveur avant le rendu de la page Web. Razor utilise `@*  *@` pour délimiter les commentaires. Le code suivant est commenté pour le serveur n’effectue aucun rendu tout balisage :

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Directives

Directives de Razor sont représentées par des expressions implicites avec les éléments suivants de mots clés réservés du `@` symbole. Une directive modifie la manière dont une vue est analysée ou active des fonctionnalités différentes.

Comprendre comment Razor génère du code pour une vue rend plus facile à comprendre comment fonctionnent les directives.

[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]

Le code génère une classe semblable au suivant :

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

Plus loin dans cet article, la section [affichage de la classe c# Razor générée pour une vue](#viewing-the-razor-c-class-generated-for-a-view) explique comment afficher cette classe générée.

### <a name="using"></a>@using

Le `@using` directive ajoute c# `using` directive à la vue générée :

[!code-cshtml[Main](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

Le `@model` directive spécifie le type du modèle passé à une vue :

```cshtml
@model TypeNameOfModel
```

Dans une application ASP.NET MVC de base créée avec les comptes d’utilisateur individuels, le *Views/Account/Login.cshtml* affichage contient la déclaration de modèle suivante :

```cshtml
@model LoginViewModel
```

La classe générée hérite `RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor expose un `Model` propriété pour accéder au modèle passées à la vue :

```cshtml
<div>The Login Email: @Model.Email</div>
```

Le `@model` directive spécifie le type de cette propriété. La directive spécifie le `T` dans `RazorPage<T>` que généré classe que la vue dérive. Si le `@model` directive n’est pas spécifié, le `Model` propriété est de type `dynamic`. La valeur du modèle est passée à la vue à partir du contrôleur. Pour plus d’informations, consultez [fortement typée de modèles et les @model (mot clé).

### <a name="inherits"></a>@inherits

Le `@inherits` directive fournit un contrôle complet de la classe qui hérite de la vue :

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Le code suivant est un type de page Razor personnalisé :

[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]

Le `CustomText` s’affiche dans une vue :

[!code-cshtml[Main](razor/sample/Views/Home/Contact10.cshtml)]

Le code restitue le code HTML suivant :

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

 `@model`et `@inherits` peut être utilisé dans la même vue.  `@inherits`peut être dans un *_ViewImports.cshtml* fichier importe de la vue :

[!code-cshtml[Main](razor/sample/Views/_ViewImportsModel.cshtml)]

Le code suivant est un exemple d’une vue fortement typée :

[!code-cshtml[Main](razor/sample/Views/Home/Login1.cshtml)]

Si «rick@contoso.com» est passé dans le modèle, la vue génère le code HTML suivant :

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject


Le `@inject` directive permet à la Page Razor injecter un service à partir de la [conteneur de service](xref:fundamentals/dependency-injection) dans une vue. Pour plus d’informations, consultez [injection de dépendances dans les vues](xref:mvc/views/dependency-injection).

### <a name="functions"></a>@functions

Le `@functions` directive permet à une Page Razor ajouter le contenu au niveau des fonctions à une vue :

```cshtml
@functions { // C# Code }
```

Exemple :

[!code-cshtml[Main](razor/sample/Views/Home/Contact6.cshtml)]

Le code génère le balisage HTML suivant :

```html
<div>From method: Hello</div>
```

Le code suivant est la classe générée Razor c# :

[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

Le `@section` directive est utilisée conjointement avec la [disposition](xref:mvc/views/layout) pour activer des vues à restituer le contenu dans les différentes parties de la page HTML. Pour plus d’informations, consultez [Sections](xref:mvc/views/layout#layout-sections-label).

## <a name="tag-helpers"></a>Programmes d’assistance de balise

Il existe trois directives qui se rapportent à [programmes d’assistance de balise](xref:mvc/views/tag-helpers/intro).

| Directive | Fonction |
| --------- | -------- |
| [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | Rend les programmes d’assistance de balise disponibles à une vue. |
| [@removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Supprime les programmes d’assistance de balise précédemment ajoutés à partir d’une vue. |
| [@tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Spécifie un préfixe de balise pour activer la prise en charge de l’application d’assistance de balise et de rendre l’utilisation du programme d’assistance de balise explicite. |

## <a name="razor-reserved-keywords"></a>Mots clés de Razor réservé

### <a name="razor-keywords"></a>Mots clés de Razor

* page (nécessite un cœur d’ASP.NET 2.0 et versions ultérieur)
* fonctions
* inherits
* modèle
* section
* application d’assistance (non prises en charge par ASP.NET Core)

Mots clés de Razor sont précédés de `@(Razor Keyword)` (par exemple, `@(functions)`).

### <a name="c-razor-keywords"></a>Mots clés du langage c# Razor

* case
* do
* default
* for
* foreach
* if
* else
* verrou
* switch
* try
* catch
* finally
* using
* while

Mots clés du langage c# Razor doivent être échappé en double avec `@(@C# Razor Keyword)` (par exemple, `@(@case)`). Le premier `@` remplace l’analyseur Razor. La seconde `@` remplace l’analyseur c#.

### <a name="reserved-keywords-not-used-by-razor"></a>Mots clés réservés non utilisées par Razor

* namespace
* classe

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a>Affichage de la classe c# Razor générée pour une vue

Ajoutez la classe suivante au projet ASP.NET MVC de base :

[!code-csharp[Main](razor/sample/Utilities/CustomTemplateEngine.cs)]

Remplacer la `RazorTemplateEngine` ajouté par MVC avec la `CustomTemplateEngine` classe :

[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=10-14)]

Définir un point d’arrêt sur la `return csharpDocument` instruction de `CustomTemplateEngine`. Lorsque l’exécution du programme s’arrête au point d’arrêt, afficher la valeur de `generatedCode`.

![Vue de generatedCode visualiseur de texte](razor/_static/tvr.png)

## <a name="view-lookups-and-case-sensitivity"></a>Recherches de vue et de respect de la casse

Le moteur d’affichage Razor effectue des recherches respectant la casse pour les vues. Toutefois, la recherche réelle est déterminée par le système de fichiers sous-jacent :

* Source en fonction du fichier : 
  * Sur les systèmes d’exploitation avec les systèmes de fichiers de la casse (par exemple, Windows), les recherches de fournisseur de fichier physique sont insensible à la casse. Par exemple, `return View("Test")` entraîne des correspondances pour */Views/Home/Test.cshtml*, */Views/home/test.cshtml*et n’importe quel autre variante de la casse.
  * Sur les systèmes de fichiers respectant la casse (par exemple, Linux, OS x et avec `EmbeddedFileProvider`), les recherches respectent la casse. Par exemple, `return View("Test")` spécifiquement les correspondances */Views/Home/Test.cshtml*.
* Précompilés vues : avec le cœur d’ASP.NET 2.0 et versions ultérieur, de vues précompilés recherche respecte la casse sur tous les systèmes d’exploitation. Le comportement est identique au comportement du fournisseur du fichier physique sous Windows. Si deux vues précompilés diffèrent uniquement par la casse, le résultat de recherche est non déterministe.

Les développeurs sont encouragés à correspondre à la casse des noms de fichiers et de répertoires à la casse de :

    * Noms de zone, le contrôleur et action. 
    * Pages Razor.
    
Casse garantit que les déploiements de rechercher leur point de vue, quelle que soit le système de fichiers sous-jacent.

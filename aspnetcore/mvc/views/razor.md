---
title: "Référence de la syntaxe Razor pour ASP.NET Core"
author: guardrex
description: "En savoir plus sur la syntaxe Razor balisage pour l’incorporation de code serveur dans les pages Web."
keywords: Directives de Razor ASP.NET Core, Razor,
ms.author: riande
manager: wpickett
ms.date: 09/29/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/razor
ms.openlocfilehash: 532e278597a0029b5bae93068af5b7b147c35688
ms.sourcegitcommit: e45f8912ce32b4071bf7e83b8f8315cd8bba3520
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2017
---
# <a name="razor-syntax-for-aspnet-core"></a><span data-ttu-id="3b101-104">Syntaxe Razor pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b101-104">Razor syntax for ASP.NET Core</span></span>

<span data-ttu-id="3b101-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), et [Taylor Mullen](https://twitter.com/ntaylormullen)</span><span class="sxs-lookup"><span data-stu-id="3b101-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), and [Taylor Mullen](https://twitter.com/ntaylormullen)</span></span>

<span data-ttu-id="3b101-106">Razor est une syntaxe de balisage pour l’incorporation de code serveur dans les pages Web.</span><span class="sxs-lookup"><span data-stu-id="3b101-106">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="3b101-107">La syntaxe Razor est constitué de Razor balisage, c# et HTML.</span><span class="sxs-lookup"><span data-stu-id="3b101-107">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="3b101-108">Les fichiers contenant Razor généralement ont un *.cshtml* extension de fichier.</span><span class="sxs-lookup"><span data-stu-id="3b101-108">Files containing Razor generally have a *.cshtml* file extension.</span></span>

## <a name="rendering-html"></a><span data-ttu-id="3b101-109">Rendu HTML</span><span class="sxs-lookup"><span data-stu-id="3b101-109">Rendering HTML</span></span>

<span data-ttu-id="3b101-110">La langue de Razor par défaut est HTML.</span><span class="sxs-lookup"><span data-stu-id="3b101-110">The default Razor language is HTML.</span></span> <span data-ttu-id="3b101-111">HTML rendu à partir du balisage de Razor n’est pas différente de rendu HTML à partir d’un fichier HTML.</span><span class="sxs-lookup"><span data-stu-id="3b101-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="3b101-112">Si vous placez un balisage HTML dans une *.cshtml* fichier Razor, il est restitué par le serveur sans modification.</span><span class="sxs-lookup"><span data-stu-id="3b101-112">If you place HTML markup into a *.cshtml* Razor file, it's rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="3b101-113">Syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="3b101-113">Razor syntax</span></span>

<span data-ttu-id="3b101-114">Razor prend en charge de c# et utilise le `@` symbole pour effectuer la transition du code HTML en c#.</span><span class="sxs-lookup"><span data-stu-id="3b101-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="3b101-115">Razor évalue les expressions c# et les affiche dans la sortie HTML.</span><span class="sxs-lookup"><span data-stu-id="3b101-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="3b101-116">Lorsqu’un `@` symbole est suivi d’un [Razor de mot clé réservé](#razor-reserved-keywords), transition vers le balisage spécifique Razor.</span><span class="sxs-lookup"><span data-stu-id="3b101-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="3b101-117">Sinon, il adopte brut c#.</span><span class="sxs-lookup"><span data-stu-id="3b101-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="3b101-118">Pour éviter un `@` de symboles dans le balisage de Razor, utiliser un deuxième `@` symbole :</span><span class="sxs-lookup"><span data-stu-id="3b101-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="3b101-119">Le code est rendu en HTML avec un seul `@` symbole :</span><span class="sxs-lookup"><span data-stu-id="3b101-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="3b101-120">Attributs HTML et contenu contenant les adresses de messagerie ne traitent pas les `@` symbole comme un caractère de la transition.</span><span class="sxs-lookup"><span data-stu-id="3b101-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="3b101-121">Dans l’exemple suivant, les adresses de messagerie sont conservées par Razor l’analyse :</span><span class="sxs-lookup"><span data-stu-id="3b101-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="3b101-122">Expressions implicites Razor</span><span class="sxs-lookup"><span data-stu-id="3b101-122">Implicit Razor expressions</span></span>

<span data-ttu-id="3b101-123">Les expressions implicites Razor commencer par `@` suivi par le code c# :</span><span class="sxs-lookup"><span data-stu-id="3b101-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="3b101-124">À l’exception de C# `await` (mot clé), les expressions implicites ne doivent pas contenir des espaces.</span><span class="sxs-lookup"><span data-stu-id="3b101-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="3b101-125">Vous pouvez se mélangent espaces si l’instruction c# a une fin claire :</span><span class="sxs-lookup"><span data-stu-id="3b101-125">You can intermingle spaces if the C# statement has a clear ending:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

## <a name="explicit-razor-expressions"></a><span data-ttu-id="3b101-126">Expressions explicites Razor</span><span class="sxs-lookup"><span data-stu-id="3b101-126">Explicit Razor expressions</span></span>

<span data-ttu-id="3b101-127">Les expressions explicites Razor se composent d’un `@` symbole avec la parenthèse à charge équilibrée.</span><span class="sxs-lookup"><span data-stu-id="3b101-127">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="3b101-128">Pour afficher l’heure de la semaine dernière, le balisage de Razor suivant est utilisé :</span><span class="sxs-lookup"><span data-stu-id="3b101-128">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="3b101-129">Tout contenu dans le `@()` parenthèses sont évaluée et rendus à la sortie.</span><span class="sxs-lookup"><span data-stu-id="3b101-129">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="3b101-130">En général les expressions implicites, décrites dans la section précédente, ne peut pas contenir d’espaces.</span><span class="sxs-lookup"><span data-stu-id="3b101-130">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="3b101-131">Dans le code suivant, une semaine n’est pas soustraite de l’heure actuelle :</span><span class="sxs-lookup"><span data-stu-id="3b101-131">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="3b101-132">Le code restitue le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="3b101-132">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="3b101-133">Vous pouvez utiliser une expression explicite pour concaténer du texte avec un résultat de l’expression :</span><span class="sxs-lookup"><span data-stu-id="3b101-133">You can use an explicit expression to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="3b101-134">Sans l’expression explicite, `<p>Age@joe.Age</p>` est traité comme une adresse de messagerie, et `<p>Age@joe.Age</p>` est rendu.</span><span class="sxs-lookup"><span data-stu-id="3b101-134">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="3b101-135">Lors de l’écriture en tant qu’expression explicite `<p>Age33</p>` est rendu.</span><span class="sxs-lookup"><span data-stu-id="3b101-135">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

## <a name="expression-encoding"></a><span data-ttu-id="3b101-136">Encodage de l’expression</span><span class="sxs-lookup"><span data-stu-id="3b101-136">Expression encoding</span></span>

<span data-ttu-id="3b101-137">Les expressions c# qui correspondent à une chaîne sont encodées en HTML.</span><span class="sxs-lookup"><span data-stu-id="3b101-137">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="3b101-138">Les expressions c# qui correspondent aux `IHtmlContent` sont rendus directement via `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="3b101-138">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="3b101-139">Les expressions c# qui ne correspondent à `IHtmlContent` sont converties en une chaîne en `ToString` et codées avant leur rendus.</span><span class="sxs-lookup"><span data-stu-id="3b101-139">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="3b101-140">Le code restitue le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="3b101-140">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="3b101-141">Le code HTML est indiqué dans le navigateur en tant que :</span><span class="sxs-lookup"><span data-stu-id="3b101-141">The HTML is shown in the browser as:</span></span>

```
<span>Hello World</span>
```

<span data-ttu-id="3b101-142">`HtmlHelper.Raw`sortie n’est pas encodée mais sont rendue sous forme de balisage HTML.</span><span class="sxs-lookup"><span data-stu-id="3b101-142">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="3b101-143">À l’aide de `HtmlHelper.Raw` utilisateur unsanitized entrée est un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="3b101-143">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="3b101-144">L’entrée d’utilisateur peut contenir malveillant JavaScript ou autres attaques.</span><span class="sxs-lookup"><span data-stu-id="3b101-144">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="3b101-145">Il est difficile d’expurgation de l’entrée d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3b101-145">Sanitizing user input is difficult.</span></span> <span data-ttu-id="3b101-146">Évitez d’utiliser `HtmlHelper.Raw` avec l’entrée d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3b101-146">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="3b101-147">Le code restitue le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="3b101-147">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="3b101-148">Blocs de code Razor</span><span class="sxs-lookup"><span data-stu-id="3b101-148">Razor code blocks</span></span>

<span data-ttu-id="3b101-149">Blocs de code Razor commencent par `@` et sont placés par `{}`.</span><span class="sxs-lookup"><span data-stu-id="3b101-149">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="3b101-150">Contrairement aux expressions, le code c# à l’intérieur des blocs de code n’est pas rendu.</span><span class="sxs-lookup"><span data-stu-id="3b101-150">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="3b101-151">Blocs de code et des expressions dans une vue de partagent la même portée et sont définies dans l’ordre :</span><span class="sxs-lookup"><span data-stu-id="3b101-151">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="3b101-152">Le code restitue le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="3b101-152">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

### <a name="implicit-transitions"></a><span data-ttu-id="3b101-153">Transitions implicites</span><span class="sxs-lookup"><span data-stu-id="3b101-153">Implicit transitions</span></span>

<span data-ttu-id="3b101-154">La langue par défaut dans un bloc de code est c#, mais vous pouvez effectuer la transition en HTML :</span><span class="sxs-lookup"><span data-stu-id="3b101-154">The default language in a code block is C#, but you can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="3b101-155">Transition délimitée explicite</span><span class="sxs-lookup"><span data-stu-id="3b101-155">Explicit delimited transition</span></span>

<span data-ttu-id="3b101-156">Pour définir une sous-section d’un bloc de code qui doit être rendu HTML, entourer les caractères pour le rendu de Razor  **\<texte >** balise :</span><span class="sxs-lookup"><span data-stu-id="3b101-156">To define a sub-section of a code block that should render HTML, surround the characters for rendering with the Razor **\<text>** tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="3b101-157">Utilisez cette approche lorsque vous souhaitez effectuer le rendu HTML n’est pas entourée d’une balise HTML.</span><span class="sxs-lookup"><span data-stu-id="3b101-157">Use this approach when you want to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="3b101-158">Sans une balise HTML ou Razor, vous recevez une erreur d’exécution Razor.</span><span class="sxs-lookup"><span data-stu-id="3b101-158">Without an HTML or Razor tag, you receive a Razor runtime error.</span></span>

<span data-ttu-id="3b101-159">Le  **\<texte >** étiquette est également utile de contrôler les espaces blancs lors du rendu du contenu.</span><span class="sxs-lookup"><span data-stu-id="3b101-159">The **\<text>** tag is also useful to control whitespace when rendering content.</span></span> <span data-ttu-id="3b101-160">Uniquement le contenu entre le  **\<texte >** balises est rendu et aucun espace blanc avant ou après le  **\<texte >** balises apparaît dans la sortie HTML.</span><span class="sxs-lookup"><span data-stu-id="3b101-160">Only the content between the **\<text>** tags is rendered, and no whitespace before or after the **\<text>** tags appears in the HTML output.</span></span>

### <a name="explicit-line-transition-with-"></a><span data-ttu-id="3b101-161">Transition de ligne explicite avec @:</span><span class="sxs-lookup"><span data-stu-id="3b101-161">Explicit Line Transition with @:</span></span>

<span data-ttu-id="3b101-162">Pour rendre le reste d’une ligne entière à l’intérieur d’un bloc de code HTML, utilisez le `@:` syntaxe :</span><span class="sxs-lookup"><span data-stu-id="3b101-162">To render the rest of an entire line as HTML inside a code block, use the `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="3b101-163">Sans le `@:` dans le code, vous recevez une erreur d’exécution Razor.</span><span class="sxs-lookup"><span data-stu-id="3b101-163">Without the `@:` in the code, you recieve a Razor runtime error.</span></span>

## <a name="control-structures"></a><span data-ttu-id="3b101-164">Structures de contrôle</span><span class="sxs-lookup"><span data-stu-id="3b101-164">Control Structures</span></span>

<span data-ttu-id="3b101-165">Structures de contrôle sont une extension des blocs de code.</span><span class="sxs-lookup"><span data-stu-id="3b101-165">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="3b101-166">Tous les aspects des blocs de code (transition au balisage, inline c#) également s’appliquent aux structures suivantes.</span><span class="sxs-lookup"><span data-stu-id="3b101-166">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures.</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="3b101-167">Instructions conditionnelles @if, sinon si, l’autre, et@switch</span><span class="sxs-lookup"><span data-stu-id="3b101-167">Conditionals @if, else if, else, and @switch</span></span>

<span data-ttu-id="3b101-168">`@if`détermine à quel moment le code s’exécute :</span><span class="sxs-lookup"><span data-stu-id="3b101-168">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="3b101-169">`else`et `else if` ne nécessitent pas le `@` symbole :</span><span class="sxs-lookup"><span data-stu-id="3b101-169">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="3b101-170">Vous pouvez utiliser une instruction switch comme suit :</span><span class="sxs-lookup"><span data-stu-id="3b101-170">You can use a switch statement like this:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="3b101-171">Bouclage @for, @foreach, @while, et @do tandis que</span><span class="sxs-lookup"><span data-stu-id="3b101-171">Looping @for, @foreach, @while, and @do while</span></span>

<span data-ttu-id="3b101-172">Vous pouvez effectuer le rendu basé sur un modèle HTML avec les instructions de contrôle de boucle.</span><span class="sxs-lookup"><span data-stu-id="3b101-172">You can render templated HTML with looping control statements.</span></span> <span data-ttu-id="3b101-173">Pour afficher une liste de personnes :</span><span class="sxs-lookup"><span data-stu-id="3b101-173">To render a list of people:</span></span>

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

<span data-ttu-id="3b101-174">Vous pouvez utiliser les instructions suivantes en boucle :</span><span class="sxs-lookup"><span data-stu-id="3b101-174">You can use any of the following looping statements:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="3b101-175">COMPOSÉS@using</span><span class="sxs-lookup"><span data-stu-id="3b101-175">Compound @using</span></span>

<span data-ttu-id="3b101-176">En c#, un `using` instruction est utilisée pour vérifier un objet est supprimé.</span><span class="sxs-lookup"><span data-stu-id="3b101-176">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="3b101-177">Dans Razor, le même mécanisme est utilisé pour créer des programmes d’assistance HTML qui contiennent du contenu supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="3b101-177">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="3b101-178">Par exemple, vous pouvez utiliser des programmes d’assistance HTML pour restituer une balise form avec la `@using` instruction :</span><span class="sxs-lookup"><span data-stu-id="3b101-178">For instance, you can utilize HTML Helpers to render a form tag with the `@using` statement:</span></span>

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

<span data-ttu-id="3b101-179">Vous pouvez également effectuer des actions d’étendue avec [programmes d’assistance de balise](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="3b101-179">You can also perform scope-level actions with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

### <a name="try-catch-finally"></a><span data-ttu-id="3b101-180">@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="3b101-180">@try, catch, finally</span></span>

<span data-ttu-id="3b101-181">La gestion des exceptions sont similaire à c# :</span><span class="sxs-lookup"><span data-stu-id="3b101-181">Exception handling is similar to C#:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

<span data-ttu-id="3b101-182">Razor a la possibilité de protéger les sections critiques avec des instructions de verrouillage :</span><span class="sxs-lookup"><span data-stu-id="3b101-182">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="3b101-183">Commentaires</span><span class="sxs-lookup"><span data-stu-id="3b101-183">Comments</span></span>

<span data-ttu-id="3b101-184">Razor prend en charge les commentaires HTML et c# :</span><span class="sxs-lookup"><span data-stu-id="3b101-184">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="3b101-185">Le code restitue le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="3b101-185">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="3b101-186">Commentaires de Razor sont supprimés par le serveur avant le rendu de la page Web.</span><span class="sxs-lookup"><span data-stu-id="3b101-186">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="3b101-187">Razor utilise `@*  *@` pour délimiter les commentaires.</span><span class="sxs-lookup"><span data-stu-id="3b101-187">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="3b101-188">Le code suivant est commenté pour le serveur n’effectue aucun rendu tout balisage :</span><span class="sxs-lookup"><span data-stu-id="3b101-188">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="3b101-189">Directives</span><span class="sxs-lookup"><span data-stu-id="3b101-189">Directives</span></span>

<span data-ttu-id="3b101-190">Directives de Razor sont représentées par des expressions implicites avec les éléments suivants de mots clés réservés du `@` symbole.</span><span class="sxs-lookup"><span data-stu-id="3b101-190">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="3b101-191">Une directive modifie la manière dont une vue est analysée ou active des fonctionnalités différentes.</span><span class="sxs-lookup"><span data-stu-id="3b101-191">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="3b101-192">Comprendre comment Razor génère du code pour une vue rend plus facile à comprendre comment fonctionnent les directives.</span><span class="sxs-lookup"><span data-stu-id="3b101-192">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="3b101-193">Le code génère une classe semblable au suivant :</span><span class="sxs-lookup"><span data-stu-id="3b101-193">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="3b101-194">Plus loin dans cet article, la section [affichage de la classe c# Razor générée pour une vue](#viewing-the-razor-c-class-generated-for-a-view) explique comment afficher cette classe générée.</span><span class="sxs-lookup"><span data-stu-id="3b101-194">Later in this article, the section [Viewing the Razor C# class generated for a view](#viewing-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="using"></a>@using

<span data-ttu-id="3b101-195">Le `@using` directive ajoute c# `using` directive à la vue générée :</span><span class="sxs-lookup"><span data-stu-id="3b101-195">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

<span data-ttu-id="3b101-196">Le `@model` directive spécifie le type du modèle passé à une vue :</span><span class="sxs-lookup"><span data-stu-id="3b101-196">The `@model` directive specifies the type of the model passed to a view:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="3b101-197">Si vous créez une application ASP.NET MVC de base avec des comptes d’utilisateur individuels, le *Views/Account/Login.cshtml* affichage contient la déclaration de modèle suivante :</span><span class="sxs-lookup"><span data-stu-id="3b101-197">If you create an ASP.NET Core MVC app with individual user accounts, the *Views/Account/Login.cshtml* view contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="3b101-198">La classe générée hérite `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="3b101-198">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="3b101-199">Razor expose un `Model` propriété pour accéder au modèle passées à la vue :</span><span class="sxs-lookup"><span data-stu-id="3b101-199">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="3b101-200">Le `@model` directive spécifie le type de cette propriété.</span><span class="sxs-lookup"><span data-stu-id="3b101-200">The `@model` directive specifies the type of this property.</span></span> <span data-ttu-id="3b101-201">La directive spécifie le `T` dans `RazorPage<T>` que généré classe que votre vue dérive.</span><span class="sxs-lookup"><span data-stu-id="3b101-201">The directive specifies the `T` in `RazorPage<T>` that the generated class that your view derives from.</span></span> <span data-ttu-id="3b101-202">Si vous ne spécifiez pas le `@model` directive, le `Model` propriété est de type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3b101-202">If you don't specify the `@model` directive, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="3b101-203">La valeur du modèle est passée à la vue à partir du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="3b101-203">The value of the model is passed from the controller to the view.</span></span> <span data-ttu-id="3b101-204">Consultez [fortement typée de modèles et les @model mot clé](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-keyword-label) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="3b101-204">See [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-keyword-label) for more information.</span></span>

### <a name="inherits"></a>@inherits

<span data-ttu-id="3b101-205">Le `@inherits` directive vous donne le contrôle total de la classe qui hérite de la vue :</span><span class="sxs-lookup"><span data-stu-id="3b101-205">The `@inherits` directive gives you full control of the class your view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="3b101-206">Ceci est un type de page Razor personnalisé :</span><span class="sxs-lookup"><span data-stu-id="3b101-206">The following is a custom Razor page type:</span></span>

[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="3b101-207">Le `CustomText` s’affiche dans une vue :</span><span class="sxs-lookup"><span data-stu-id="3b101-207">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="3b101-208">Le code restitue le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="3b101-208">The code renders the following HTML:</span></span>

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

<span data-ttu-id="3b101-209">Vous ne pouvez pas utiliser `@model` et `@inherits` dans la même vue.</span><span class="sxs-lookup"><span data-stu-id="3b101-209">You can't use `@model` and `@inherits` in the same view.</span></span> <span data-ttu-id="3b101-210">Vous pouvez avoir `@inherits` dans un *_ViewImports.cshtml* fichier importe de la vue :</span><span class="sxs-lookup"><span data-stu-id="3b101-210">You can have `@inherits` in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[Main](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="3b101-211">Voici un exemple d’une vue fortement typée :</span><span class="sxs-lookup"><span data-stu-id="3b101-211">The following is an example of a strongly-typed view:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="3b101-212">Si «rick@contoso.com» est passé dans le modèle, la vue génère le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="3b101-212">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject

<span data-ttu-id="3b101-213">Le `@inject` directive vous permet d’injecter un service à partir de votre [conteneur de service](xref:fundamentals/dependency-injection) dans votre affichage.</span><span class="sxs-lookup"><span data-stu-id="3b101-213">The `@inject` directive enables you to inject a service from your [service container](xref:fundamentals/dependency-injection) into your view.</span></span> <span data-ttu-id="3b101-214">Consultez [injection de dépendances dans les vues](xref:mvc/views/dependency-injection) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="3b101-214">See [Dependency injection into views](xref:mvc/views/dependency-injection) for more information.</span></span>

### <a name="functions"></a>@functions

<span data-ttu-id="3b101-215">Le `@functions` directive permet de vous permet d’ajouter du contenu au niveau de la fonction à une vue :</span><span class="sxs-lookup"><span data-stu-id="3b101-215">The `@functions` directive enables you to add function-level content to a view:</span></span>

```cshtml
@functions { // C# Code }
```

<span data-ttu-id="3b101-216">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3b101-216">For example:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="3b101-217">Le code génère le balisage HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="3b101-217">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="3b101-218">Le code suivant est la classe générée Razor c# :</span><span class="sxs-lookup"><span data-stu-id="3b101-218">The following code is the generated Razor C# class:</span></span>

[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

<span data-ttu-id="3b101-219">Le `@section` directive est utilisée conjointement avec la [disposition](xref:mvc/views/layout) pour activer des vues à restituer le contenu dans les différentes parties de la page HTML.</span><span class="sxs-lookup"><span data-stu-id="3b101-219">The `@section` directive is used in conjunction with the [layout](xref:mvc/views/layout) to enable views to render content in different parts of the HTML page.</span></span> <span data-ttu-id="3b101-220">Consultez [Sections](xref:mvc/views/layout#layout-sections-label) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="3b101-220">See [Sections](xref:mvc/views/layout#layout-sections-label) for more information.</span></span>

## <a name="tag-helpers"></a><span data-ttu-id="3b101-221">Programmes d’assistance de balise</span><span class="sxs-lookup"><span data-stu-id="3b101-221">Tag Helpers</span></span>

<span data-ttu-id="3b101-222">Il existe trois directives qui se rapportent à [programmes d’assistance de balise](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="3b101-222">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="3b101-223">Directive</span><span class="sxs-lookup"><span data-stu-id="3b101-223">Directive</span></span> | <span data-ttu-id="3b101-224">Fonction</span><span class="sxs-lookup"><span data-stu-id="3b101-224">Function</span></span> |
| --------- | -------- |
| [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="3b101-225">Rend les programmes d’assistance de balise disponibles à une vue.</span><span class="sxs-lookup"><span data-stu-id="3b101-225">Makes Tag Helpers available to a view.</span></span> |
| [@removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="3b101-226">Supprime les programmes d’assistance de balise précédemment ajoutés à partir d’une vue.</span><span class="sxs-lookup"><span data-stu-id="3b101-226">Removes Tag Helpers previously added from a view.</span></span> |
| [@tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="3b101-227">Spécifie un préfixe de balise pour activer la prise en charge de l’application d’assistance de balise et de rendre l’utilisation du programme d’assistance de balise explicite.</span><span class="sxs-lookup"><span data-stu-id="3b101-227">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="3b101-228">Mots clés de Razor réservé</span><span class="sxs-lookup"><span data-stu-id="3b101-228">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="3b101-229">Mots clés de Razor</span><span class="sxs-lookup"><span data-stu-id="3b101-229">Razor keywords</span></span>

* <span data-ttu-id="3b101-230">page (nécessite un cœur d’ASP.NET 2.0 et versions ultérieur)</span><span class="sxs-lookup"><span data-stu-id="3b101-230">page (Requires ASP.NET Core 2.0 and later)</span></span>
* <span data-ttu-id="3b101-231">fonctions</span><span class="sxs-lookup"><span data-stu-id="3b101-231">functions</span></span>
* <span data-ttu-id="3b101-232">hérite</span><span class="sxs-lookup"><span data-stu-id="3b101-232">inherits</span></span>
* <span data-ttu-id="3b101-233">modèle</span><span class="sxs-lookup"><span data-stu-id="3b101-233">model</span></span>
* <span data-ttu-id="3b101-234">section</span><span class="sxs-lookup"><span data-stu-id="3b101-234">section</span></span>
* <span data-ttu-id="3b101-235">application d’assistance (non prises en charge par ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="3b101-235">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="3b101-236">Mots clés de Razor sont précédés de `@(Razor Keyword)` (par exemple, `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="3b101-236">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="3b101-237">Mots clés du langage c# Razor</span><span class="sxs-lookup"><span data-stu-id="3b101-237">C# Razor keywords</span></span>

* <span data-ttu-id="3b101-238">case</span><span class="sxs-lookup"><span data-stu-id="3b101-238">case</span></span>
* <span data-ttu-id="3b101-239">do</span><span class="sxs-lookup"><span data-stu-id="3b101-239">do</span></span>
* <span data-ttu-id="3b101-240">default</span><span class="sxs-lookup"><span data-stu-id="3b101-240">default</span></span>
* <span data-ttu-id="3b101-241">for</span><span class="sxs-lookup"><span data-stu-id="3b101-241">for</span></span>
* <span data-ttu-id="3b101-242">foreach</span><span class="sxs-lookup"><span data-stu-id="3b101-242">foreach</span></span>
* <span data-ttu-id="3b101-243">if</span><span class="sxs-lookup"><span data-stu-id="3b101-243">if</span></span>
* <span data-ttu-id="3b101-244">else</span><span class="sxs-lookup"><span data-stu-id="3b101-244">else</span></span>
* <span data-ttu-id="3b101-245">lock</span><span class="sxs-lookup"><span data-stu-id="3b101-245">lock</span></span>
* <span data-ttu-id="3b101-246">switch</span><span class="sxs-lookup"><span data-stu-id="3b101-246">switch</span></span>
* <span data-ttu-id="3b101-247">try</span><span class="sxs-lookup"><span data-stu-id="3b101-247">try</span></span>
* <span data-ttu-id="3b101-248">catch</span><span class="sxs-lookup"><span data-stu-id="3b101-248">catch</span></span>
* <span data-ttu-id="3b101-249">finally</span><span class="sxs-lookup"><span data-stu-id="3b101-249">finally</span></span>
* <span data-ttu-id="3b101-250">using</span><span class="sxs-lookup"><span data-stu-id="3b101-250">using</span></span>
* <span data-ttu-id="3b101-251">while</span><span class="sxs-lookup"><span data-stu-id="3b101-251">while</span></span>

<span data-ttu-id="3b101-252">Mots clés du langage c# Razor doivent être échappé en double avec `@(@C# Razor Keyword)` (par exemple, `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="3b101-252">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="3b101-253">Le premier `@` remplace l’analyseur Razor.</span><span class="sxs-lookup"><span data-stu-id="3b101-253">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="3b101-254">La seconde `@` remplace l’analyseur c#.</span><span class="sxs-lookup"><span data-stu-id="3b101-254">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="3b101-255">Mots clés réservés non utilisées par Razor</span><span class="sxs-lookup"><span data-stu-id="3b101-255">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="3b101-256">namespace</span><span class="sxs-lookup"><span data-stu-id="3b101-256">namespace</span></span>
* <span data-ttu-id="3b101-257">class</span><span class="sxs-lookup"><span data-stu-id="3b101-257">class</span></span>

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="3b101-258">Affichage de la classe c# Razor générée pour une vue</span><span class="sxs-lookup"><span data-stu-id="3b101-258">Viewing the Razor C# class generated for a view</span></span>

<span data-ttu-id="3b101-259">Ajoutez la classe suivante à votre projet ASP.NET MVC de base :</span><span class="sxs-lookup"><span data-stu-id="3b101-259">Add the following class to your ASP.NET Core MVC project:</span></span>

[!code-csharp[Main](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="3b101-260">Remplacer la `RazorTemplateEngine` ajouté par MVC avec la `CustomTemplateEngine` classe :</span><span class="sxs-lookup"><span data-stu-id="3b101-260">Override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="3b101-261">Définir un point d’arrêt sur la `return csharpDocument` instruction de `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="3b101-261">Set a break point on the `return csharpDocument` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="3b101-262">Lorsque l’exécution du programme s’arrête au point d’arrêt, afficher la valeur de `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="3b101-262">When program execution stops at the break point, view the value of `generatedCode`.</span></span>

![Vue de generatedCode visualiseur de texte](razor/_static/tvr.png)

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="3b101-264">Recherches de vue et de respect de la casse</span><span class="sxs-lookup"><span data-stu-id="3b101-264">View lookups and case sensitivity</span></span>

<span data-ttu-id="3b101-265">Le moteur d’affichage Razor effectue des recherches respectant la casse pour les vues.</span><span class="sxs-lookup"><span data-stu-id="3b101-265">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="3b101-266">Toutefois, la recherche réelle est déterminée par le système de fichiers sous-jacent :</span><span class="sxs-lookup"><span data-stu-id="3b101-266">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="3b101-267">Source en fonction du fichier :</span><span class="sxs-lookup"><span data-stu-id="3b101-267">File based source:</span></span> 
  * <span data-ttu-id="3b101-268">Sur les systèmes d’exploitation avec les systèmes de fichiers de la casse (par exemple, Windows), les recherches de fournisseur de fichier physique sont insensible à la casse.</span><span class="sxs-lookup"><span data-stu-id="3b101-268">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="3b101-269">Par exemple, `return View("Test")` entraîne des correspondances pour */Views/Home/Test.cshtml*, */Views/home/test.cshtml*et n’importe quel autre variante de la casse.</span><span class="sxs-lookup"><span data-stu-id="3b101-269">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="3b101-270">Sur les systèmes de fichiers respectant la casse (par exemple, Linux, OS x et avec `EmbeddedFileProvider`), les recherches respectent la casse.</span><span class="sxs-lookup"><span data-stu-id="3b101-270">On case sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case sensitive.</span></span> <span data-ttu-id="3b101-271">Par exemple, `return View("Test")` spécifiquement les correspondances */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3b101-271">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="3b101-272">Précompilés vues : avec le cœur d’ASP.Net 2.0 et versions ultérieur, de vues précompilés recherche respecte la casse sur tous les systèmes d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="3b101-272">Precompiled views: With ASP.Net Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="3b101-273">Le comportement est identique au comportement du fournisseur du fichier physique sous Windows.</span><span class="sxs-lookup"><span data-stu-id="3b101-273">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="3b101-274">Si deux vues précompilés diffèrent uniquement par la casse, le résultat de recherche est non déterministe.</span><span class="sxs-lookup"><span data-stu-id="3b101-274">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="3b101-275">Les développeurs sont encouragés à correspondre à la casse des noms de fichiers et de répertoires à la casse des noms de zone, le contrôleur et action.</span><span class="sxs-lookup"><span data-stu-id="3b101-275">Developers are encouraged to match the casing of file and directory names to the casing of area, controller, and action names.</span></span> <span data-ttu-id="3b101-276">Cela garantit que vos déploiements trouverez leur point de vue, quelle que soit le système de fichiers sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="3b101-276">This ensures your deployments will find their views regardless of the underlying file system.</span></span>

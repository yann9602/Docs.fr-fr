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
ms.openlocfilehash: 066fe3b2486c63bd4de2ccb865ad432a67846d77
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="razor-syntax-for-aspnet-core"></a><span data-ttu-id="8c0cb-104">Syntaxe Razor pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c0cb-104">Razor syntax for ASP.NET Core</span></span>

<span data-ttu-id="8c0cb-105">Par [Taylor Mullen](https://twitter.com/ntaylormullen) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8c0cb-105">By [Taylor Mullen](https://twitter.com/ntaylormullen) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-is-razor"></a><span data-ttu-id="8c0cb-106">Nouveautés Razor</span><span class="sxs-lookup"><span data-stu-id="8c0cb-106">What is Razor?</span></span>

<span data-ttu-id="8c0cb-107">Razor est une syntaxe de balisage pour l’incorporation de code en fonction de serveur dans les pages web.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-107">Razor is a markup syntax for embedding server based code into web pages.</span></span> <span data-ttu-id="8c0cb-108">La syntaxe Razor se compose d’un balisage Razor, HTML et c#.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-108">The Razor syntax consists of Razor markup, C# and HTML.</span></span> <span data-ttu-id="8c0cb-109">Les fichiers contenant Razor généralement ont un *.cshtml* extension de fichier.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-109">Files containing Razor generally have a *.cshtml* file extension.</span></span>

## <a name="rendering-html"></a><span data-ttu-id="8c0cb-110">Rendu HTML</span><span class="sxs-lookup"><span data-stu-id="8c0cb-110">Rendering HTML</span></span>

<span data-ttu-id="8c0cb-111">La langue de Razor par défaut est HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-111">The default Razor language is HTML.</span></span> <span data-ttu-id="8c0cb-112">Rendu HTML à partir de Razor n’est pas différente dans un fichier HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-112">Rendering HTML from Razor is no different than in an HTML file.</span></span> <span data-ttu-id="8c0cb-113">Un fichier Razor avec le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-113">A Razor file with the following markup:</span></span>

```html
<p>Hello World</p>
   ```

<span data-ttu-id="8c0cb-114">Rendu inchangée `<p>Hello World</p>` par le serveur.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-114">Is rendered unchanged as `<p>Hello World</p>` by the server.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="8c0cb-115">Syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="8c0cb-115">Razor syntax</span></span>

<span data-ttu-id="8c0cb-116">Razor prend en charge de c# et utilise le `@` symbole pour effectuer la transition du code HTML en c#.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-116">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="8c0cb-117">Razor évalue les expressions c# et les affiche dans la sortie HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-117">Razor evaluates C# expressions and renders them in the HTML output.</span></span> <span data-ttu-id="8c0cb-118">Razor peut passer du HTML au C# ou à des balises spécifiques à Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-118">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="8c0cb-119">Lorsqu’un `@` symbole est suivi d’un [Razor de mot clé réservé](#razor-reserved-keywords) transition vers le balisage spécifique Razor, sinon il passe à c# ordinaire.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-119">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords) it transitions into Razor-specific markup, otherwise it transitions into plain C#.</span></span>

<a name=escape-at-label></a>

<span data-ttu-id="8c0cb-120">HTML contenant `@` symboles peuvent doivent être échappés avec un second `@` symbole.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-120">HTML containing `@` symbols may need to be escaped with a second `@` symbol.</span></span> <span data-ttu-id="8c0cb-121">Exemple :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-121">For example:</span></span>

```html
<p>@@Username</p>
   ```

<span data-ttu-id="8c0cb-122">rendrait le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-122">would render the following HTML:</span></span>

```html
<p>@Username</p>
   ```

<a name=razor-email-ref></a>

<span data-ttu-id="8c0cb-123">Attributs HTML et contenu contenant les adresses de messagerie ne traitent pas les `@` symbole comme un caractère de la transition.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-123">HTML attributes and content containing email addresses don’t treat the `@` symbol as a transition character.</span></span>

   `<a href="mailto:Support@contoso.com">Support@contoso.com</a>`

## <a name="implicit-razor-expressions"></a><span data-ttu-id="8c0cb-124">Expressions implicites Razor</span><span class="sxs-lookup"><span data-stu-id="8c0cb-124">Implicit Razor expressions</span></span>

<span data-ttu-id="8c0cb-125">Les expressions implicites Razor commencer par `@` suivi par le code c#.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-125">Implicit Razor expressions start with `@` followed by C# code.</span></span> <span data-ttu-id="8c0cb-126">Exemple :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-126">For example:</span></span>

```html
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="8c0cb-127">À l’exception de C# `await` expressions implicites du mot clé ne doivent pas contenir des espaces.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-127">With the exception of the C# `await` keyword implicit expressions must not contain spaces.</span></span> <span data-ttu-id="8c0cb-128">Par exemple, des espaces peuvent se mélangent tant que l’instruction c# a une fin claire :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-128">For example, you can intermingle spaces as long as the C# statement has a clear ending:</span></span>

```html
<p>@await DoSomething("hello", "world")</p>
```

## <a name="explicit-razor-expressions"></a><span data-ttu-id="8c0cb-129">Expressions explicites Razor</span><span class="sxs-lookup"><span data-stu-id="8c0cb-129">Explicit Razor expressions</span></span>

<span data-ttu-id="8c0cb-130">Les expressions explicites Razor se compose d’un symbole avec la parenthèse à charge équilibrée.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-130">Explicit Razor expressions consists of an @ symbol with balanced parenthesis.</span></span> <span data-ttu-id="8c0cb-131">Par exemple, pour afficher l’heure de la semaine dernière :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-131">For example, to render last week's time:</span></span>

```html
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="8c0cb-132">Tout contenu dans le @ () parenthèses est évaluée et rendus à la sortie.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-132">Any content within the @() parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="8c0cb-133">En général les expressions implicites ne peuvent pas contenir d’espaces.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-133">Implicit expressions generally cannot contain spaces.</span></span> <span data-ttu-id="8c0cb-134">Par exemple, dans le code ci-dessous, une semaine n’est pas soustraite de l’heure actuelle :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-134">For example, in the code below, one week is not subtracted from the current time:</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact.cshtml?range=20)]

<span data-ttu-id="8c0cb-135">Qui restitue le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-135">Which renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
   ```

<span data-ttu-id="8c0cb-136">Vous pouvez utiliser une expression explicite pour concaténer du texte avec un résultat de l’expression :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-136">You can use an explicit expression to concatenate text with an expression result:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [5]}} -->

```none
@{
    var joe = new Person("Joe", 33);
 }

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="8c0cb-137">Sans l’expression explicite, `<p>Age@joe.Age</p>` est considérée comme une adresse de messagerie et `<p>Age@joe.Age</p>` est rendu.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-137">Without the explicit expression, `<p>Age@joe.Age</p>` would be treated as an email address and `<p>Age@joe.Age</p>` would be rendered.</span></span> <span data-ttu-id="8c0cb-138">Lors de l’écriture en tant qu’expression explicite `<p>Age33</p>` est rendu.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-138">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<a name=expression-encoding-label></a>

## <a name="expression-encoding"></a><span data-ttu-id="8c0cb-139">Encodage de l’expression</span><span class="sxs-lookup"><span data-stu-id="8c0cb-139">Expression encoding</span></span>

<span data-ttu-id="8c0cb-140">Les expressions c# qui correspondent à une chaîne sont encodées en HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-140">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="8c0cb-141">Les expressions c# qui correspondent aux `IHtmlContent` sont rendus directement via *IHtmlContent.WriteTo*.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-141">C# expressions that evaluate to `IHtmlContent` are rendered directly through *IHtmlContent.WriteTo*.</span></span> <span data-ttu-id="8c0cb-142">Les expressions c# qui ne correspondent à *IHtmlContent* sont converties en une chaîne (par *ToString*) et encodée avant d’être restitués.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-142">C# expressions that don't evaluate to *IHtmlContent* are converted to a string (by *ToString*) and encoded before they are rendered.</span></span> <span data-ttu-id="8c0cb-143">Par exemple, le balisage de Razor suivant :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-143">For example, the following Razor markup:</span></span>

```html
@("<span>Hello World</span>")
   ```

<span data-ttu-id="8c0cb-144">Cela restitue le HTML :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-144">Renders this HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
   ```

<span data-ttu-id="8c0cb-145">Le navigateur restituée sous :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-145">Which the browser renders as:</span></span>

`<span>Hello World</span>`

<span data-ttu-id="8c0cb-146">`HtmlHelper``Raw` sortie n’est pas encodée mais rendue sous forme de balisage HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-146">`HtmlHelper` `Raw` output is not encoded but rendered as HTML markup.</span></span>

>[!WARNING]
> <span data-ttu-id="8c0cb-147">À l’aide de `HtmlHelper.Raw` utilisateur unsanitized entrée est un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-147">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="8c0cb-148">L’entrée d’utilisateur peut contenir malveillant JavaScript ou autres attaques.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-148">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="8c0cb-149">Il est difficile d’expurgation de l’entrée d’utilisateur, évitez d’utiliser `HtmlHelper.Raw` sur l’entrée d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-149">Sanitizing user input is difficult, avoid using `HtmlHelper.Raw` on user input.</span></span>

<span data-ttu-id="8c0cb-150">Le balisage de Razor suivant :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-150">The following Razor markup:</span></span>

```html
@Html.Raw("<span>Hello World</span>")
   ```

<span data-ttu-id="8c0cb-151">Cela restitue le HTML :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-151">Renders this HTML:</span></span>

```html
<span>Hello World</span>
   ```

<a name=razor-code-blocks-label></a>

## <a name="razor-code-blocks"></a><span data-ttu-id="8c0cb-152">Blocs de code Razor</span><span class="sxs-lookup"><span data-stu-id="8c0cb-152">Razor code blocks</span></span>

<span data-ttu-id="8c0cb-153">Blocs de code Razor commencent par `@` et sont placés par `{}`.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-153">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="8c0cb-154">Contrairement aux expressions, le code c# à l’intérieur des blocs de code n’est pas rendu.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-154">Unlike expressions, C# code inside code blocks is not rendered.</span></span> <span data-ttu-id="8c0cb-155">Blocs de code et des expressions dans une page Razor partagent la même étendue et sont définies dans l’ordre (autrement dit, les déclarations dans un bloc de code sera dans l’étendue pour les blocs de code et des expressions).</span><span class="sxs-lookup"><span data-stu-id="8c0cb-155">Code blocks and expressions in a Razor page share the same scope and are defined in order (that is, declarations in a code block will be in scope for later code blocks and expressions).</span></span>

```none
@{
    var output = "Hello World";
}

<p>The rendered result: @output</p>
```

<span data-ttu-id="8c0cb-156">Rendrait les :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-156">Would render:</span></span>

```html
<p>The rendered result: Hello World</p>
   ```

<a name=implicit-transitions-label></a>

### <a name="implicit-transitions"></a><span data-ttu-id="8c0cb-157">Transitions implicites</span><span class="sxs-lookup"><span data-stu-id="8c0cb-157">Implicit transitions</span></span>

<span data-ttu-id="8c0cb-158">La langue par défaut dans un bloc de code est c#, mais vous pouvez effectuer la transition en HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-158">The default language in a code block is C#, but you can transition back to HTML.</span></span> <span data-ttu-id="8c0cb-159">HTML dans un bloc de code passera au rendu HTML :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-159">HTML within a code block will transition back into rendering HTML:</span></span>

```none
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

<a name=explicit-delimited-transition-label></a>

### <a name="explicit-delimited-transition"></a><span data-ttu-id="8c0cb-160">Transition délimitée explicite</span><span class="sxs-lookup"><span data-stu-id="8c0cb-160">Explicit delimited transition</span></span>

<span data-ttu-id="8c0cb-161">Pour définir une sous-section d’un bloc de code qui doit être rendu HTML, entourer les caractères doivent être rendus avec Razor `<text>` balise :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-161">To define a sub-section of a code block that should render HTML, surround the characters to be rendered with the Razor `<text>` tag:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [4]}} -->

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="8c0cb-162">En général, vous utilisez cette approche lorsque vous souhaitez effectuer le rendu HTML n’est pas entouré d’une balise HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-162">You generally use this approach when you want to render HTML that is not surrounded by an HTML tag.</span></span> <span data-ttu-id="8c0cb-163">Sans une balise HTML ou Razor, vous obtenez une erreur d’exécution Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-163">Without an HTML or Razor tag, you get a Razor runtime error.</span></span>

<a name=explicit-line-transition-with-label></a>

### <a name="explicit-line-transition-with-"></a><span data-ttu-id="8c0cb-164">Transition de ligne explicite avec`@:`</span><span class="sxs-lookup"><span data-stu-id="8c0cb-164">Explicit Line Transition with `@:`</span></span>

<span data-ttu-id="8c0cb-165">Pour rendre le reste d’une ligne entière à l’intérieur d’un bloc de code HTML, utilisez le `@:` syntaxe :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-165">To render the rest of an entire line as HTML inside a code block, use the `@:` syntax:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [4]}} -->

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="8c0cb-166">Sans le `@:` dans le code ci-dessus, vous obtenez une erreur d’exécution de Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-166">Without the `@:` in the code above, you'd get a Razor run time error.</span></span>

<a name=control-structures-razor-label></a>

## <a name="control-structures"></a><span data-ttu-id="8c0cb-167">Structures de contrôle</span><span class="sxs-lookup"><span data-stu-id="8c0cb-167">Control Structures</span></span>

<span data-ttu-id="8c0cb-168">Structures de contrôle sont une extension des blocs de code.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-168">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="8c0cb-169">Tous les aspects des blocs de code (transition au balisage, inline c#) également s’appliquent aux structures suivantes.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-169">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures.</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="8c0cb-170">Instructions conditionnelles `@if`, `else if`, `else` et`@switch`</span><span class="sxs-lookup"><span data-stu-id="8c0cb-170">Conditionals `@if`, `else if`, `else` and `@switch`</span></span>

<span data-ttu-id="8c0cb-171">Le `@if` famille contrôle quand le code s’exécute :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-171">The `@if` family controls when code runs:</span></span>

```none
@if (value % 2 == 0)
{
    <p>The value was even</p>
}
```

<span data-ttu-id="8c0cb-172">`else`et `else if` ne nécessitent pas le `@` symbole :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-172">`else` and `else if` don't require the `@` symbol:</span></span>

```none
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

<span data-ttu-id="8c0cb-173">Vous pouvez utiliser une instruction switch comme suit :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-173">You can use a switch statement like this:</span></span>

```none
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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="8c0cb-174">Bouclage `@for`, `@foreach`, `@while`, et`@do while`</span><span class="sxs-lookup"><span data-stu-id="8c0cb-174">Looping `@for`, `@foreach`, `@while`, and `@do while`</span></span>

<span data-ttu-id="8c0cb-175">Vous pouvez effectuer le rendu basé sur un modèle HTML avec les instructions de contrôle de boucle.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-175">You can render templated HTML with looping control statements.</span></span> <span data-ttu-id="8c0cb-176">Par exemple, pour afficher une liste de personnes :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-176">For example, to render a list of people:</span></span>

```none
@{
    var people = new Person[]
    {
          new Person("John", 33),
          new Person("Doe", 41),
    };
}
```

<span data-ttu-id="8c0cb-177">Vous pouvez utiliser les instructions suivantes en boucle :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-177">You can use any of the following looping statements:</span></span>

`@for`

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```none
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```none
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

```none
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="8c0cb-178">COMPOSÉS`@using`</span><span class="sxs-lookup"><span data-stu-id="8c0cb-178">Compound `@using`</span></span>

<span data-ttu-id="8c0cb-179">En c# à l’aide d’une instruction est utilisée pour vérifier un objet est supprimé.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-179">In C# a using statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="8c0cb-180">Dans Razor ce même mécanisme peut être utilisé pour créer des programmes d’assistance HTML qui contiennent du contenu supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-180">In Razor this same mechanism can be used to create HTML helpers that contain additional content.</span></span> <span data-ttu-id="8c0cb-181">Par exemple, que nous pouvons utiliser des programmes d’assistance HTML pour restituer une balise form avec la `@using` instruction :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-181">For instance, we can utilize HTML Helpers to render a form tag with the `@using` statement:</span></span>

```none
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" name="Email" value="" />
        <button type="submit"> Register </button>
    </div>
}
```

<span data-ttu-id="8c0cb-182">Vous pouvez également effectuer des actions au niveau de portée comme ci-dessus avec [programmes d’assistance de balise](tag-helpers/index.md).</span><span class="sxs-lookup"><span data-stu-id="8c0cb-182">You can also perform scope level actions like the above with [Tag Helpers](tag-helpers/index.md).</span></span>

### <a name="try-catch-finally"></a><span data-ttu-id="8c0cb-183">`@try`, `catch`, `finally`</span><span class="sxs-lookup"><span data-stu-id="8c0cb-183">`@try`, `catch`, `finally`</span></span>

<span data-ttu-id="8c0cb-184">La gestion des exceptions sont similaire à c# :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-184">Exception handling is similar to  C#:</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="8c0cb-185">Razor a la possibilité de protéger les sections critiques avec des instructions de verrouillage :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-185">Razor has the capability to protect critical sections with lock statements:</span></span>

```none
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="8c0cb-186">Commentaires</span><span class="sxs-lookup"><span data-stu-id="8c0cb-186">Comments</span></span>

<span data-ttu-id="8c0cb-187">Razor prend en charge les commentaires c# et HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-187">Razor supports C# and HTML comments.</span></span> <span data-ttu-id="8c0cb-188">Le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-188">The following markup:</span></span>

```none
@{
    /* C# comment. */
    // Another C# comment.
}
<!-- HTML comment -->
```

<span data-ttu-id="8c0cb-189">Est restitué par le serveur en tant que :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-189">Is rendered by the server as:</span></span>

```none
<!-- HTML comment -->
```

<span data-ttu-id="8c0cb-190">Commentaires de Razor sont supprimés par le serveur avant que la page est rendue.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-190">Razor comments are removed by the server before the page is rendered.</span></span> <span data-ttu-id="8c0cb-191">Razor utilise `@*  *@` pour délimiter les commentaires.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-191">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="8c0cb-192">Le code suivant est commenté pour le serveur ne va pas afficher tout balisage :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-192">The following code is commented out, so the server will not render any markup:</span></span>

```none
 @*
 @{
     /* C# comment. */
     // Another C# comment.
 }
 <!-- HTML comment -->
*@
```

<a name=razor-directives-label></a>

## <a name="directives"></a><span data-ttu-id="8c0cb-193">Directives</span><span class="sxs-lookup"><span data-stu-id="8c0cb-193">Directives</span></span>

<span data-ttu-id="8c0cb-194">Directives de Razor sont représentées par des expressions implicites avec les éléments suivants de mots clés réservés du `@` symbole.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-194">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="8c0cb-195">Une directive sera généralement modifier la façon dont une page est analysée ou activer des fonctionnalités différentes au sein de votre page Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-195">A directive will typically change the way a page is parsed or enable different functionality within your Razor page.</span></span>

<span data-ttu-id="8c0cb-196">Comprendre comment Razor génère du code pour une vue rend plus facile à comprendre comment fonctionnent les directives.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-196">Understanding how Razor generates code for a view will make it easier to understand how directives work.</span></span> <span data-ttu-id="8c0cb-197">Une page Razor est utilisée pour générer un fichier c#.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-197">A Razor page is used to generate a C# file.</span></span> <span data-ttu-id="8c0cb-198">Par exemple, cette page Razor :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-198">For example, this Razor page:</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="8c0cb-199">Génère une classe semblable au suivant :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-199">Generates a class similar to the following:</span></span>

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

<span data-ttu-id="8c0cb-200">[Affichage de la classe c# Razor générée pour une vue](#razor-customcompilationservice-label) explique comment afficher cette classe générée.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-200">[Viewing the Razor C# class generated for a view](#razor-customcompilationservice-label) explains how to view this generated class.</span></span>

### `@using`

<span data-ttu-id="8c0cb-201">Le `@using` directive ajoutera c# `using` directive à la page razor généré :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-201">The `@using` directive will add the c# `using` directive to the generated razor page:</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact9.cshtml)]

### `@model`

<span data-ttu-id="8c0cb-202">Le `@model` directive spécifie le type du modèle passé à la page Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-202">The `@model` directive specifies the type of the model passed to the Razor page.</span></span> <span data-ttu-id="8c0cb-203">Il utilise la syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-203">It uses the following syntax:</span></span>

```none
@model TypeNameOfModel
   ```

<span data-ttu-id="8c0cb-204">Par exemple, si vous créez une application ASP.NET MVC de base avec des comptes d’utilisateur individuels, le *Views/Account/Login.cshtml* vue Razor contient la déclaration de modèle suivante :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-204">For example, if you create an ASP.NET Core MVC app with individual user accounts, the *Views/Account/Login.cshtml* Razor view contains the following model declaration:</span></span>

```csharp
@model LoginViewModel
   ```

<span data-ttu-id="8c0cb-205">Dans l’exemple précédent de la classe, la classe générée hérite `RazorPage<dynamic>`.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-205">In the preceding class example, the class generated inherits from `RazorPage<dynamic>`.</span></span> <span data-ttu-id="8c0cb-206">En ajoutant un `@model` vous contrôlez ce qui est hérité.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-206">By adding an `@model` you control what’s inherited.</span></span> <span data-ttu-id="8c0cb-207">Exemple :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-207">For example</span></span>

```csharp
@model LoginViewModel
   ```

<span data-ttu-id="8c0cb-208">Génère la classe suivante</span><span class="sxs-lookup"><span data-stu-id="8c0cb-208">Generates the following class</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
   ```

<span data-ttu-id="8c0cb-209">Exposent des pages Razor un `Model` propriété pour accéder au modèle passé à la page.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-209">Razor pages expose a `Model` property for accessing the model passed to the page.</span></span>

```html
<div>The Login Email: @Model.Email</div>
   ```

<span data-ttu-id="8c0cb-210">Le `@model` directive spécifié le type de cette propriété (en spécifiant la `T` dans `RazorPage<T>` qui dérive de la classe générée pour votre page).</span><span class="sxs-lookup"><span data-stu-id="8c0cb-210">The `@model` directive specified the type of this property (by specifying the `T` in `RazorPage<T>` that the generated class for your page derives from).</span></span> <span data-ttu-id="8c0cb-211">Si vous ne spécifiez pas le `@model` directive le `Model` propriété sera de type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-211">If you don't specify the `@model` directive the `Model` property will be of type `dynamic`.</span></span> <span data-ttu-id="8c0cb-212">La valeur du modèle est passée à la vue à partir du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-212">The value of the model is passed from the controller to the view.</span></span> <span data-ttu-id="8c0cb-213">Consultez [fortement typée de modèles et les @model mot clé](../../tutorials/first-mvc-app/adding-model.md#strongly-typed-models-keyword-label) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-213">See [Strongly typed models and the @model keyword](../../tutorials/first-mvc-app/adding-model.md#strongly-typed-models-keyword-label) for more information.</span></span>

### `@inherits`

<span data-ttu-id="8c0cb-214">Le `@inherits` directive vous donne le contrôle total de la classe qui hérite de votre page Razor :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-214">The `@inherits` directive gives you full control of the class your Razor page inherits:</span></span>

```none
@inherits TypeNameOfClassToInheritFrom
   ```

<span data-ttu-id="8c0cb-215">Par exemple, supposons que nous avons eu le type de page Razor personnalisé suivant :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-215">For instance, let’s say we had the following custom Razor page type:</span></span>

[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="8c0cb-216">Razor suivante génèrent `<div>Custom text: Hello World</div>`.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-216">The following Razor would generate `<div>Custom text: Hello World</div>`.</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="8c0cb-217">Vous ne pouvez pas utiliser `@model` et `@inherits` sur la même page.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-217">You can't use `@model` and `@inherits` on the same page.</span></span> <span data-ttu-id="8c0cb-218">Vous pouvez avoir `@inherits` dans un *_ViewImports.cshtml* fichier importe de la page Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-218">You can have `@inherits` in a *_ViewImports.cshtml* file that the Razor page imports.</span></span> <span data-ttu-id="8c0cb-219">Par exemple, si votre vue Razor importé suit *_ViewImports.cshtml* fichier :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-219">For example, if your Razor view imported the following *_ViewImports.cshtml* file:</span></span>

[!code-html[Main](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="8c0cb-220">La page suivante de Razor fortement typée</span><span class="sxs-lookup"><span data-stu-id="8c0cb-220">The following strongly typed Razor page</span></span>

[!code-html[Main](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="8c0cb-221">Génère ce balisage HTML :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-221">Generates this HTML markup:</span></span>

```none
<div>The Login Email: Rick@contoso.com</div>
<div>Custom text: Hello World</div>
```

<span data-ttu-id="8c0cb-222">Lorsqu’il est passé «[Rick@contoso.com](mailto:Rick@contoso.com)» dans le modèle :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-222">When passed "[Rick@contoso.com](mailto:Rick@contoso.com)" in the model:</span></span>

   <span data-ttu-id="8c0cb-223">Pour plus d’informations, consultez [Disposition](layout.md).</span><span class="sxs-lookup"><span data-stu-id="8c0cb-223">See [Layout](layout.md) for more information.</span></span>

### `@inject`

<span data-ttu-id="8c0cb-224">Le `@inject` directive vous permet d’injecter un service à partir de votre [conteneur de service](../../fundamentals/dependency-injection.md) dans votre page Razor pour l’utiliser.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-224">The `@inject` directive enables you to inject a service from your [service container](../../fundamentals/dependency-injection.md)  into your Razor page for use.</span></span> <span data-ttu-id="8c0cb-225">Consultez [injection de dépendances dans les vues](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="8c0cb-225">See [Dependency injection into views](dependency-injection.md).</span></span>

<a name="functions"></a>

### `@functions`

<span data-ttu-id="8c0cb-226">Le `@functions` directive permet de vous permet d’ajouter du contenu au niveau de la fonction à votre page Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-226">The `@functions` directive enables you to add function level content to your Razor page.</span></span> <span data-ttu-id="8c0cb-227">La syntaxe est :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-227">The syntax is:</span></span>

```none
@functions { // C# Code }
   ```

<span data-ttu-id="8c0cb-228">Exemple :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-228">For example:</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="8c0cb-229">Génère le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-229">Generates the following HTML markup:</span></span>

```none
<div>From method: Hello</div>
   ```

<span data-ttu-id="8c0cb-230">Généré Razor c# ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-230">The generated Razor C# looks like:</span></span>

[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### `@section`

<span data-ttu-id="8c0cb-231">Le `@section` directive est utilisée conjointement avec la [page de disposition](layout.md) pour activer des vues à restituer le contenu dans les différentes parties de la page HTML rendue.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-231">The `@section` directive is used in conjunction with the [layout page](layout.md) to enable views to render content in different parts of the rendered HTML page.</span></span> <span data-ttu-id="8c0cb-232">Consultez [Sections](layout.md#layout-sections-label) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-232">See [Sections](layout.md#layout-sections-label) for more information.</span></span>

## <a name="tag-helpers"></a><span data-ttu-id="8c0cb-233">Programmes d’assistance de balise</span><span class="sxs-lookup"><span data-stu-id="8c0cb-233">Tag Helpers</span></span>

<span data-ttu-id="8c0cb-234">Les éléments suivants [programmes d’assistance de balise](tag-helpers/index.md) directives sont détaillées dans les liens fournis.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-234">The following [Tag Helpers](tag-helpers/index.md) directives are detailed in the links provided.</span></span>

* [@addTagHelper](tag-helpers/intro.md#add-helper-label)
* [@removeTagHelper](tag-helpers/intro.md#remove-razor-directives-label)
* [@tagHelperPrefix](tag-helpers/intro.md#prefix-razor-directives-label)

<a name=razor-reserved-keywords-label></a>

## <a name="razor-reserved-keywords"></a><span data-ttu-id="8c0cb-235">Mots clés de Razor réservé</span><span class="sxs-lookup"><span data-stu-id="8c0cb-235">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="8c0cb-236">Mots clés de Razor</span><span class="sxs-lookup"><span data-stu-id="8c0cb-236">Razor keywords</span></span>

* <span data-ttu-id="8c0cb-237">page (nécessite un cœur d’ASP.NET 2.0 et versions ultérieur)</span><span class="sxs-lookup"><span data-stu-id="8c0cb-237">page (Requires ASP.NET Core 2.0 and later)</span></span>
* <span data-ttu-id="8c0cb-238">fonctions</span><span class="sxs-lookup"><span data-stu-id="8c0cb-238">functions</span></span>
* <span data-ttu-id="8c0cb-239">hérite</span><span class="sxs-lookup"><span data-stu-id="8c0cb-239">inherits</span></span>
* <span data-ttu-id="8c0cb-240">modèle</span><span class="sxs-lookup"><span data-stu-id="8c0cb-240">model</span></span>
* <span data-ttu-id="8c0cb-241">section</span><span class="sxs-lookup"><span data-stu-id="8c0cb-241">section</span></span>
* <span data-ttu-id="8c0cb-242">application d’assistance (non pris en charge par ASP.NET Core.)</span><span class="sxs-lookup"><span data-stu-id="8c0cb-242">helper   (Not supported by ASP.NET Core.)</span></span>

<span data-ttu-id="8c0cb-243">Mots clés de Razor peuvent être précédées de `@(Razor Keyword)`, par exemple `@(functions)`.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-243">Razor keywords can be escaped with `@(Razor Keyword)`, for example `@(functions)`.</span></span> <span data-ttu-id="8c0cb-244">Consultez l’exemple complet ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-244">See the complete sample below.</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="8c0cb-245">Mots clés du langage c# Razor</span><span class="sxs-lookup"><span data-stu-id="8c0cb-245">C# Razor keywords</span></span>

* <span data-ttu-id="8c0cb-246">case</span><span class="sxs-lookup"><span data-stu-id="8c0cb-246">case</span></span>
* <span data-ttu-id="8c0cb-247">do</span><span class="sxs-lookup"><span data-stu-id="8c0cb-247">do</span></span>
* <span data-ttu-id="8c0cb-248">default</span><span class="sxs-lookup"><span data-stu-id="8c0cb-248">default</span></span>
* <span data-ttu-id="8c0cb-249">for</span><span class="sxs-lookup"><span data-stu-id="8c0cb-249">for</span></span>
* <span data-ttu-id="8c0cb-250">foreach</span><span class="sxs-lookup"><span data-stu-id="8c0cb-250">foreach</span></span>
* <span data-ttu-id="8c0cb-251">if</span><span class="sxs-lookup"><span data-stu-id="8c0cb-251">if</span></span>
* <span data-ttu-id="8c0cb-252">else</span><span class="sxs-lookup"><span data-stu-id="8c0cb-252">else</span></span>
* <span data-ttu-id="8c0cb-253">lock</span><span class="sxs-lookup"><span data-stu-id="8c0cb-253">lock</span></span>
* <span data-ttu-id="8c0cb-254">switch</span><span class="sxs-lookup"><span data-stu-id="8c0cb-254">switch</span></span>
* <span data-ttu-id="8c0cb-255">try</span><span class="sxs-lookup"><span data-stu-id="8c0cb-255">try</span></span>
* <span data-ttu-id="8c0cb-256">catch</span><span class="sxs-lookup"><span data-stu-id="8c0cb-256">catch</span></span>
* <span data-ttu-id="8c0cb-257">finally</span><span class="sxs-lookup"><span data-stu-id="8c0cb-257">finally</span></span>
* <span data-ttu-id="8c0cb-258">using</span><span class="sxs-lookup"><span data-stu-id="8c0cb-258">using</span></span>
* <span data-ttu-id="8c0cb-259">while</span><span class="sxs-lookup"><span data-stu-id="8c0cb-259">while</span></span>

<span data-ttu-id="8c0cb-260">Mots clés du langage c# Razor doivent être double échappement `@(@C# Razor Keyword)`, par exemple `@(@case)`.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-260">C# Razor keywords need to be double escaped with `@(@C# Razor Keyword)`, for example `@(@case)`.</span></span> <span data-ttu-id="8c0cb-261">La première `@` remplace l’analyseur Razor, la seconde `@` remplace l’analyseur c#.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-261">The first `@` escapes the Razor parser, the second `@` escapes the C# parser.</span></span> <span data-ttu-id="8c0cb-262">Consultez l’exemple complet ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-262">See the complete sample below.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="8c0cb-263">Mots clés réservés non utilisées par Razor</span><span class="sxs-lookup"><span data-stu-id="8c0cb-263">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="8c0cb-264">namespace</span><span class="sxs-lookup"><span data-stu-id="8c0cb-264">namespace</span></span>
* <span data-ttu-id="8c0cb-265">class</span><span class="sxs-lookup"><span data-stu-id="8c0cb-265">class</span></span>

<a name=razor-customcompilationservice-label></a>

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="8c0cb-266">Affichage de la classe c# Razor générée pour une vue</span><span class="sxs-lookup"><span data-stu-id="8c0cb-266">Viewing the Razor C# class generated for a view</span></span>

<span data-ttu-id="8c0cb-267">Ajoutez la classe suivante à votre projet ASP.NET MVC de base :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-267">Add the following class to your ASP.NET Core MVC project:</span></span>

[!code-csharp[Main](razor/sample/Services/CustomCompilationService.cs)]

<span data-ttu-id="8c0cb-268">Remplacer la `ICompilationService` ajouté par MVC avec la classe ci-dessus ;</span><span class="sxs-lookup"><span data-stu-id="8c0cb-268">Override the `ICompilationService` added by MVC with the above class;</span></span>

[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=29-33)]

<span data-ttu-id="8c0cb-269">Définir un point d’arrêt sur la `Compile` méthode `CustomCompilationService` et `compilationContent`.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-269">Set a break point on the `Compile` method of `CustomCompilationService` and view `compilationContent`.</span></span>

![Vue de compilationContent visualiseur de texte](razor/_static/tvr.png)

<a name="case"></a>
## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="8c0cb-271">Recherches de vue et de respect de la casse</span><span class="sxs-lookup"><span data-stu-id="8c0cb-271">View lookups and case sensitivity</span></span>

<span data-ttu-id="8c0cb-272">Le moteur d’affichage Razor effectue des recherches respectant la casse pour les vues.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-272">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="8c0cb-273">Toutefois, la recherche réelle est déterminée par la source sous-jacente :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-273">However, the actual lookup is determined by the underlying source:</span></span>

* <span data-ttu-id="8c0cb-274">Source en fonction du fichier :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-274">File based source:</span></span> 

    * <span data-ttu-id="8c0cb-275">Sur les systèmes d’exploitation avec les systèmes de fichiers de la casse (par exemple, Windows), les recherches de fournisseur de fichier physique sont insensible à la casse.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-275">On operating systems with case insensitive file systems (like Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="8c0cb-276">Par exemple `return View("Test")` entraînerait `/Views/Home/Test.cshtml`, `/Views/home/test.cshtml` et toutes les autres variantes de casse seraient découvertes.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-276">For example `return View("Test")` would result in `/Views/Home/Test.cshtml`, `/Views/home/test.cshtml` and all other casing variants would be discovered.</span></span>
    * <span data-ttu-id="8c0cb-277">Sur les systèmes de fichiers respectant la casse, qui inclut la Linux, OS x et `EmbeddedFileProvider`, les recherches respectent la casse.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-277">On case sensitive file systems, which includes Linux, OSX and `EmbeddedFileProvider`, lookups are case sensitive.</span></span> <span data-ttu-id="8c0cb-278">Par exemple, `return View("Test")` recherche spécifiquement `/Views/Home/Test.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-278">For example, `return View("Test")` would specifically look for `/Views/Home/Test.cshtml`.</span></span>
        
* <span data-ttu-id="8c0cb-279">Vues précompilés :</span><span class="sxs-lookup"><span data-stu-id="8c0cb-279">Precompiled views:</span></span>

   * <span data-ttu-id="8c0cb-280">Avec ASP.Net Core 2.0 et versions ultérieures, de vues précompilés recherche respecte la casse sur tous les systèmes d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-280">With ASP.Net Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="8c0cb-281">Le comportement est identique au comportement du fournisseur du fichier physique sous Windows.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-281">The behavior is identical to physical file provider's behavior on Windows.</span></span> 
   <span data-ttu-id="8c0cb-282">Remarque : Si les deux vues précompilés diffèrent uniquement par la casse, le résultat de recherche est non déterministe.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-282">Note: If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="8c0cb-283">Les développeurs sont encouragés à correspondre à la casse des noms de fichiers et de répertoires à la casse des noms de zone, de contrôleur et d’action.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-283">Developers are encouraged to match the casing of file and directory names to the casing of area, controller and action names.</span></span> <span data-ttu-id="8c0cb-284">Il en résulterait que vos déploiements restent agnostiques du système de fichiers sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="8c0cb-284">This would ensure your deployments remain agnostic of the underlying file system.</span></span>

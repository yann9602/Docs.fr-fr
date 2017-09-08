---
title: "Empêche le script entre sites"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 95790927-2bfe-445e-b1fd-429c2c7030ce
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/cross-site-scripting
ms.openlocfilehash: 1816977837efd82f374a03d9f776db21358e2850
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="preventing-cross-site-scripting"></a><span data-ttu-id="222f3-103">Empêche le script entre sites</span><span class="sxs-lookup"><span data-stu-id="222f3-103">Preventing Cross-Site Scripting</span></span>

<a name=security-cross-site-scripting></a>

<span data-ttu-id="222f3-104">Écriture de scripts entre sites (XSS) est une faille de sécurité qui permet à un attaquant d’installer les scripts côté client (généralement JavaScript) dans les pages web.</span><span class="sxs-lookup"><span data-stu-id="222f3-104">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="222f3-105">Lorsque vous chargez des pages concernées, les scripts des personnes malveillantes s’exécutent les autres utilisateurs, l’activation de la personne malveillante de voler des cookies et des jetons, modifier le contenu de la page web via la manipulation du modèle DOM ou rediriger le navigateur vers une autre page.</span><span class="sxs-lookup"><span data-stu-id="222f3-105">When other users load affected pages the attackers scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="222f3-106">Vulnérabilités XSS se produisent généralement lorsqu’une application accepte l’entrée utilisateur et génère dans une page sans validation, d’encodage ou d’échappement.</span><span class="sxs-lookup"><span data-stu-id="222f3-106">XSS vulnerabilities generally occur when an application takes user input and outputs it in a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="222f3-107">Protection de votre application par rapport à XSS</span><span class="sxs-lookup"><span data-stu-id="222f3-107">Protecting your application against XSS</span></span>

<span data-ttu-id="222f3-108">À une protection au niveau de base fonctionne par ruses votre application en insérant un `<script>` balise dans votre page rendue ou en insérant un `On*` événement dans un élément.</span><span class="sxs-lookup"><span data-stu-id="222f3-108">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="222f3-109">Développeurs doivent utiliser les étapes de prévention suivantes pour éviter d’introduire XSS dans leur application.</span><span class="sxs-lookup"><span data-stu-id="222f3-109">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="222f3-110">Placez jamais des données non fiables dans votre entrée HTML, sauf si vous suivez le reste des étapes ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="222f3-110">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="222f3-111">Données non approuvées sont toutes les données qui peuvent être contrôlées par une personne malveillante, des entrées de formulaire HTML, chaînes de requête, les en-têtes HTTP, même les données provenant d’une base de données comme une personne malveillante peut être en mesure de violation de votre base de données même si elles ne peuvent pas de faille de votre application.</span><span class="sxs-lookup"><span data-stu-id="222f3-111">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="222f3-112">Avant de placer des données non fiables à l’intérieur d’un élément HTML, assurez-vous qu’il est encodé au format HTML.</span><span class="sxs-lookup"><span data-stu-id="222f3-112">Before putting untrusted data inside an HTML element ensure it is HTML encoded.</span></span> <span data-ttu-id="222f3-113">L’encodage HTML accepte les caractères comme &lt; et les transforme en un formulaire sécurisé comme &amp;lt ;</span><span class="sxs-lookup"><span data-stu-id="222f3-113">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="222f3-114">Avant de placer des données non fiables dans un attribut HTML, assurez-vous qu’il est l’attribut HTML encodé.</span><span class="sxs-lookup"><span data-stu-id="222f3-114">Before putting untrusted data into an HTML attribute ensure it is HTML attribute encoded.</span></span> <span data-ttu-id="222f3-115">Encodage d’attribut HTML est un sur-ensemble de l’encodage HTML et encode les caractères supplémentaires tels que « et ».</span><span class="sxs-lookup"><span data-stu-id="222f3-115">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="222f3-116">Avant de mettre des données non fiables en JavaScript placer les données dans un élément HTML dont le contenu lors de l’exécution de la récupérer.</span><span class="sxs-lookup"><span data-stu-id="222f3-116">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="222f3-117">Si ce n’est pas possible, puis vérifiez que les données est codée JavaScript.</span><span class="sxs-lookup"><span data-stu-id="222f3-117">If this is not possible then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="222f3-118">Encodage de JavaScript prend caractères dangereuses pour JavaScript et les remplace par leur hex, par exemple &lt; soit codée en tant que `\u003C`.</span><span class="sxs-lookup"><span data-stu-id="222f3-118">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="222f3-119">Avant de placer des données non fiables dans une chaîne de requête URL Assurez-vous qu’il est codé URL.</span><span class="sxs-lookup"><span data-stu-id="222f3-119">Before putting untrusted data into a URL query string ensure it is URL encoded.</span></span>

## <a name="html-encoding-using-razor"></a><span data-ttu-id="222f3-120">Codage HTML à l’aide de Razor</span><span class="sxs-lookup"><span data-stu-id="222f3-120">HTML Encoding using Razor</span></span>

<span data-ttu-id="222f3-121">Le moteur Razor automatiquement utilisé dans MVC encode tous provenant de sortie à partir de variables, sauf si vous travaillez dur pour empêcher cette opération.</span><span class="sxs-lookup"><span data-stu-id="222f3-121">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="222f3-122">Il utilise l’attribut HTML règles de codage chaque fois que vous utilisez le  *@*  la directive.</span><span class="sxs-lookup"><span data-stu-id="222f3-122">It uses HTML Attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="222f3-123">Au format HTML codage d’attribut est un sur-ensemble de codage HTML cela signifie que vous n’êtes pas obligé de vous préoccuper si vous devez utiliser l’encodage HTML ou encodage d’attribut HTML.</span><span class="sxs-lookup"><span data-stu-id="222f3-123">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="222f3-124">Vous devez vous assurer que vous uniquement utilisez dans un contexte HTML, ne pas lors de la tentative d’insertion des entrées non approuvées directement dans JavaScript.</span><span class="sxs-lookup"><span data-stu-id="222f3-124">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="222f3-125">Programmes d’assistance de balise codera également entrée que vous utilisez dans les paramètres de la balise.</span><span class="sxs-lookup"><span data-stu-id="222f3-125">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="222f3-126">Prenez l’affichage Razor suivant ;</span><span class="sxs-lookup"><span data-stu-id="222f3-126">Take the following Razor view;</span></span>

```none
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="222f3-127">Cette vue renvoie le contenu de la *untrustedInput* variable.</span><span class="sxs-lookup"><span data-stu-id="222f3-127">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="222f3-128">Cette variable inclut certains caractères utilisés dans les attaques XSS, à savoir &lt;, « et &gt;.</span><span class="sxs-lookup"><span data-stu-id="222f3-128">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="222f3-129">Examen de la source présente la sortie rendue encodée sous la forme :</span><span class="sxs-lookup"><span data-stu-id="222f3-129">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="222f3-130">ASP.NET MVC de base fournit un `HtmlString` classe qui n’est pas encodé automatiquement sur les résultats.</span><span class="sxs-lookup"><span data-stu-id="222f3-130">ASP.NET Core MVC provides an `HtmlString` class which is not automatically encoded upon output.</span></span> <span data-ttu-id="222f3-131">Cela ne doit jamais être utilisé en association avec une entrée non approuvée comme ceci expose une vulnérabilité XSS.</span><span class="sxs-lookup"><span data-stu-id="222f3-131">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-razor"></a><span data-ttu-id="222f3-132">Encodage de JavaScript à l’aide de Razor</span><span class="sxs-lookup"><span data-stu-id="222f3-132">Javascript Encoding using Razor</span></span>

<span data-ttu-id="222f3-133">Il peut arriver que vous souhaitez insérer une valeur JavaScript à traiter dans votre affichage.</span><span class="sxs-lookup"><span data-stu-id="222f3-133">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="222f3-134">Il existe deux manières de procéder.</span><span class="sxs-lookup"><span data-stu-id="222f3-134">There are two ways to do this.</span></span> <span data-ttu-id="222f3-135">Le moyen le plus sûr pour insérer des valeurs simples consiste à placer la valeur dans un attribut de données d’une balise et de récupérer dans JavaScript.</span><span class="sxs-lookup"><span data-stu-id="222f3-135">The safest way to insert simple values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="222f3-136">Exemple :</span><span class="sxs-lookup"><span data-stu-id="222f3-136">For example:</span></span>

```none
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="222f3-137">Cela génère le code HTML suivant</span><span class="sxs-lookup"><span data-stu-id="222f3-137">This will produce the following HTML</span></span>

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="222f3-138">Qui, lorsqu’il s’exécute, apparaîtront comme suit.</span><span class="sxs-lookup"><span data-stu-id="222f3-138">Which, when it runs, will render the following;</span></span>

```none
<"123">
   <"123">
   ```

<span data-ttu-id="222f3-139">Vous pouvez également appeler l’encodeur JavaScript directement,</span><span class="sxs-lookup"><span data-stu-id="222f3-139">You can also call the JavaScript encoder directly,</span></span>

```none
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
   ```

<span data-ttu-id="222f3-140">Cela affichera dans le navigateur comme suit :</span><span class="sxs-lookup"><span data-stu-id="222f3-140">This will render in the browser as follows;</span></span>

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> <span data-ttu-id="222f3-141">Ne concaténez pas d’entrée non fiable dans JavaScript pour créer des éléments DOM.</span><span class="sxs-lookup"><span data-stu-id="222f3-141">Do not concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="222f3-142">Vous devez utiliser `createElement()` et attribuer des valeurs de propriété correctement comme `node.TextContent=`, ou utilisez `element.SetAttribute()` / `element[attribute]=` sinon vous exposez à XSS de basé sur le DOM.</span><span class="sxs-lookup"><span data-stu-id="222f3-142">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="222f3-143">L’accès à des encodeurs dans le code</span><span class="sxs-lookup"><span data-stu-id="222f3-143">Accessing encoders in code</span></span>

<span data-ttu-id="222f3-144">Les encodeurs HTML, JavaScript et URL sont disponibles pour votre code de deux manières, vous pouvez injecter via [injection de dépendance](../fundamentals/dependency-injection.md#fundamentals-dependency-injection) ou vous pouvez utiliser les encodeurs par défaut contenus dans le `System.Text.Encodings.Web` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="222f3-144">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](../fundamentals/dependency-injection.md#fundamentals-dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="222f3-145">Si vous utilisez les encodeurs par défaut, puis les que vous avez appliqué aux plages de caractères pour être considérées comme sécurisés ne prendront pas effet - les encodeurs par défaut utilisent les règles de codage plus sûrs possible.</span><span class="sxs-lookup"><span data-stu-id="222f3-145">If you use the default encoders then any  you applied to character ranges to be treated as safe will not take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="222f3-146">Pour utiliser les encodeurs configurables via DI votre constructeurs doivent prendre un *HtmlEncoder*, *JavaScriptEncoder* et *UrlEncoder* paramètre selon les besoins.</span><span class="sxs-lookup"><span data-stu-id="222f3-146">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="222f3-147">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="222f3-147">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="222f3-148">Paramètres d’encodage URL</span><span class="sxs-lookup"><span data-stu-id="222f3-148">Encoding URL Parameters</span></span>

<span data-ttu-id="222f3-149">Si vous souhaitez générer une chaîne de requête URL avec une entrée non approuvée en tant qu’une valeur utilisez le `UrlEncoder` pour encoder la valeur.</span><span class="sxs-lookup"><span data-stu-id="222f3-149">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="222f3-150">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="222f3-150">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="222f3-151">Après le codage du encodedValue variable contiendra `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span><span class="sxs-lookup"><span data-stu-id="222f3-151">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="222f3-152">Des espaces, des guillemets, signes de ponctuation et autres caractères non sécurisés sont pourcentage encodées dans leur valeur hexadécimale, par exemple, un caractère d’espace deviendra % 20.</span><span class="sxs-lookup"><span data-stu-id="222f3-152">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="222f3-153">N’utilisez pas d’entrée non fiable dans le cadre d’un chemin d’accès URL.</span><span class="sxs-lookup"><span data-stu-id="222f3-153">Do not use untrusted input as part of a URL path.</span></span> <span data-ttu-id="222f3-154">Toujours passer entrée non fiable en tant que valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="222f3-154">Always pass untrusted input as a query string value.</span></span>

<a name=security-cross-site-scripting-customization></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="222f3-155">Personnaliser les encodeurs</span><span class="sxs-lookup"><span data-stu-id="222f3-155">Customizing the Encoders</span></span>

<span data-ttu-id="222f3-156">Par défaut les encodeurs utilisent une liste verte est limitée à la plage de base Unicode Latin et coder tous les caractères en dehors de cette plage comme leurs équivalents de code de caractère.</span><span class="sxs-lookup"><span data-stu-id="222f3-156">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="222f3-157">Ce comportement affecte également rendu Razor TagHelper et HtmlHelper car il utilise les encodeurs pour vos chaînes de sortie.</span><span class="sxs-lookup"><span data-stu-id="222f3-157">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="222f3-158">Le raisonnement est pour vous protéger contre les bogues de navigateur inconnu ou ultérieure (précédente navigateur bogues ont dépassé de l’analyse basée sur le traitement des caractères non anglais).</span><span class="sxs-lookup"><span data-stu-id="222f3-158">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="222f3-159">Si votre site web utilise de façon intensive les caractères non latins, telles que le chinois, cyrillique, ou autres cela est probablement pas le comportement souhaité.</span><span class="sxs-lookup"><span data-stu-id="222f3-159">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="222f3-160">Vous pouvez personnaliser les listes safe encodeur pour inclure Unicode plages appropriés à votre application lors du démarrage, dans `ConfigureServices()`.</span><span class="sxs-lookup"><span data-stu-id="222f3-160">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="222f3-161">Par exemple, à l’aide de la configuration par défaut que vous pouvez utiliser un HtmlHelper Razor comme ;</span><span class="sxs-lookup"><span data-stu-id="222f3-161">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="222f3-162">Lorsque vous affichez la source de la page web vous verrez qu'a été restitué comme suit, avec le texte chinois encodé ;</span><span class="sxs-lookup"><span data-stu-id="222f3-162">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="222f3-163">Pour élargir les caractères traités comme sécurisés par l’encodeur vous permet d’insérer la ligne suivante dans le `ConfigureServices()` méthode dans `startup.cs`;</span><span class="sxs-lookup"><span data-stu-id="222f3-163">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="222f3-164">Cet exemple s’étend de la liste pour inclure les CjkUnifiedIdeographs plage Unicode.</span><span class="sxs-lookup"><span data-stu-id="222f3-164">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="222f3-165">La sortie rendue deviendrait maintenant</span><span class="sxs-lookup"><span data-stu-id="222f3-165">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="222f3-166">Les plages de liste verte sont spécifiées sous forme de graphiques de code Unicode, pas les langues.</span><span class="sxs-lookup"><span data-stu-id="222f3-166">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="222f3-167">Le [norme Unicode](http://unicode.org/) possède une liste de [code graphiques](http://www.unicode.org/charts/index.html) que vous pouvez utiliser pour rechercher le graphique qui contient les caractères.</span><span class="sxs-lookup"><span data-stu-id="222f3-167">The [Unicode standard](http://unicode.org/) has a list of [code charts](http://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="222f3-168">Chaque encodeur, Html, JavaScript et Url, doit être configuré séparément.</span><span class="sxs-lookup"><span data-stu-id="222f3-168">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="222f3-169">Personnalisation de la liste n’affecte que les encodeurs provenant via DI.</span><span class="sxs-lookup"><span data-stu-id="222f3-169">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="222f3-170">Si vous accédez directement à un encodeur via `System.Text.Encodings.Web.*Encoder.Default` ensuite la valeur par défaut, Latin de base uniquement les listes fiables sera utilisé.</span><span class="sxs-lookup"><span data-stu-id="222f3-170">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="222f3-171">Où doit encodage effectuée ?</span><span class="sxs-lookup"><span data-stu-id="222f3-171">Where should encoding take place?</span></span>

<span data-ttu-id="222f3-172">Général acceptés consiste encodage intervient au point de sortie et les valeurs encodées ne doivent jamais être stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="222f3-172">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="222f3-173">Encodage dans la phase de sortie vous permet de modifier l’utilisation des données, par exemple, à partir de HTML à une valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="222f3-173">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="222f3-174">Aussi, il vous permet de rechercher facilement vos données sans avoir à encoder les valeurs avant de les rechercher et vous permet de tirer parti des modifications ou des correctifs de bogues apportées aux encodeurs.</span><span class="sxs-lookup"><span data-stu-id="222f3-174">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="222f3-175">Validation comme une technique de prévention XSS</span><span class="sxs-lookup"><span data-stu-id="222f3-175">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="222f3-176">La validation peut être un outil utile dans limitation des attaques XSS.</span><span class="sxs-lookup"><span data-stu-id="222f3-176">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="222f3-177">Par exemple, une chaîne numérique simple contenant uniquement les caractères 0-9 ne déclenche pas d’une attaque XSS.</span><span class="sxs-lookup"><span data-stu-id="222f3-177">For example, a simple numeric string containing only the characters 0-9 will not trigger an XSS attack.</span></span> <span data-ttu-id="222f3-178">Validation devient plus complexe que vous souhaitez accepter HTML dans l’entrée d’utilisateur - l’analyse d’entrée HTML est difficile, voire impossible.</span><span class="sxs-lookup"><span data-stu-id="222f3-178">Validation becomes more complicated should you wish to accept HTML in user input - parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="222f3-179">MarkDown et autres formats de texte est une option plus sûre pour l’entrée riche.</span><span class="sxs-lookup"><span data-stu-id="222f3-179">MarkDown and other text formats would be a safer option for rich input.</span></span> <span data-ttu-id="222f3-180">Vous ne devez jamais vous appuyer sur la validation uniquement.</span><span class="sxs-lookup"><span data-stu-id="222f3-180">You should never rely on validation alone.</span></span> <span data-ttu-id="222f3-181">Toujours encoder une entrée non approuvée avant la sortie, quel que soit le quel type de validation que vous avez effectué.</span><span class="sxs-lookup"><span data-stu-id="222f3-181">Always encode untrusted input before output, no matter what validation you have performed.</span></span>

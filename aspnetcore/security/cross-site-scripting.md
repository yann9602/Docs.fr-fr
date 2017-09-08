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
# <a name="preventing-cross-site-scripting"></a>Empêche le script entre sites

<a name=security-cross-site-scripting></a>

Écriture de scripts entre sites (XSS) est une faille de sécurité qui permet à un attaquant d’installer les scripts côté client (généralement JavaScript) dans les pages web. Lorsque vous chargez des pages concernées, les scripts des personnes malveillantes s’exécutent les autres utilisateurs, l’activation de la personne malveillante de voler des cookies et des jetons, modifier le contenu de la page web via la manipulation du modèle DOM ou rediriger le navigateur vers une autre page. Vulnérabilités XSS se produisent généralement lorsqu’une application accepte l’entrée utilisateur et génère dans une page sans validation, d’encodage ou d’échappement.

## <a name="protecting-your-application-against-xss"></a>Protection de votre application par rapport à XSS

À une protection au niveau de base fonctionne par ruses votre application en insérant un `<script>` balise dans votre page rendue ou en insérant un `On*` événement dans un élément. Développeurs doivent utiliser les étapes de prévention suivantes pour éviter d’introduire XSS dans leur application.

1. Placez jamais des données non fiables dans votre entrée HTML, sauf si vous suivez le reste des étapes ci-dessous. Données non approuvées sont toutes les données qui peuvent être contrôlées par une personne malveillante, des entrées de formulaire HTML, chaînes de requête, les en-têtes HTTP, même les données provenant d’une base de données comme une personne malveillante peut être en mesure de violation de votre base de données même si elles ne peuvent pas de faille de votre application.

2. Avant de placer des données non fiables à l’intérieur d’un élément HTML, assurez-vous qu’il est encodé au format HTML. L’encodage HTML accepte les caractères comme &lt; et les transforme en un formulaire sécurisé comme &amp;lt ;

3. Avant de placer des données non fiables dans un attribut HTML, assurez-vous qu’il est l’attribut HTML encodé. Encodage d’attribut HTML est un sur-ensemble de l’encodage HTML et encode les caractères supplémentaires tels que « et ».

4. Avant de mettre des données non fiables en JavaScript placer les données dans un élément HTML dont le contenu lors de l’exécution de la récupérer. Si ce n’est pas possible, puis vérifiez que les données est codée JavaScript. Encodage de JavaScript prend caractères dangereuses pour JavaScript et les remplace par leur hex, par exemple &lt; soit codée en tant que `\u003C`.

5. Avant de placer des données non fiables dans une chaîne de requête URL Assurez-vous qu’il est codé URL.

## <a name="html-encoding-using-razor"></a>Codage HTML à l’aide de Razor

Le moteur Razor automatiquement utilisé dans MVC encode tous provenant de sortie à partir de variables, sauf si vous travaillez dur pour empêcher cette opération. Il utilise l’attribut HTML règles de codage chaque fois que vous utilisez le  *@*  la directive. Au format HTML codage d’attribut est un sur-ensemble de codage HTML cela signifie que vous n’êtes pas obligé de vous préoccuper si vous devez utiliser l’encodage HTML ou encodage d’attribut HTML. Vous devez vous assurer que vous uniquement utilisez dans un contexte HTML, ne pas lors de la tentative d’insertion des entrées non approuvées directement dans JavaScript. Programmes d’assistance de balise codera également entrée que vous utilisez dans les paramètres de la balise.

Prenez l’affichage Razor suivant ;

```none
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Cette vue renvoie le contenu de la *untrustedInput* variable. Cette variable inclut certains caractères utilisés dans les attaques XSS, à savoir &lt;, « et &gt;. Examen de la source présente la sortie rendue encodée sous la forme :

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET MVC de base fournit un `HtmlString` classe qui n’est pas encodé automatiquement sur les résultats. Cela ne doit jamais être utilisé en association avec une entrée non approuvée comme ceci expose une vulnérabilité XSS.

## <a name="javascript-encoding-using-razor"></a>Encodage de JavaScript à l’aide de Razor

Il peut arriver que vous souhaitez insérer une valeur JavaScript à traiter dans votre affichage. Il existe deux manières de procéder. Le moyen le plus sûr pour insérer des valeurs simples consiste à placer la valeur dans un attribut de données d’une balise et de récupérer dans JavaScript. Exemple :

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

Cela génère le code HTML suivant

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

Qui, lorsqu’il s’exécute, apparaîtront comme suit.

```none
<"123">
   <"123">
   ```

Vous pouvez également appeler l’encodeur JavaScript directement,

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

Cela affichera dans le navigateur comme suit :

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> Ne concaténez pas d’entrée non fiable dans JavaScript pour créer des éléments DOM. Vous devez utiliser `createElement()` et attribuer des valeurs de propriété correctement comme `node.TextContent=`, ou utilisez `element.SetAttribute()` / `element[attribute]=` sinon vous exposez à XSS de basé sur le DOM.

## <a name="accessing-encoders-in-code"></a>L’accès à des encodeurs dans le code

Les encodeurs HTML, JavaScript et URL sont disponibles pour votre code de deux manières, vous pouvez injecter via [injection de dépendance](../fundamentals/dependency-injection.md#fundamentals-dependency-injection) ou vous pouvez utiliser les encodeurs par défaut contenus dans le `System.Text.Encodings.Web` espace de noms. Si vous utilisez les encodeurs par défaut, puis les que vous avez appliqué aux plages de caractères pour être considérées comme sécurisés ne prendront pas effet - les encodeurs par défaut utilisent les règles de codage plus sûrs possible.

Pour utiliser les encodeurs configurables via DI votre constructeurs doivent prendre un *HtmlEncoder*, *JavaScriptEncoder* et *UrlEncoder* paramètre selon les besoins. Par exemple :

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

## <a name="encoding-url-parameters"></a>Paramètres d’encodage URL

Si vous souhaitez générer une chaîne de requête URL avec une entrée non approuvée en tant qu’une valeur utilisez le `UrlEncoder` pour encoder la valeur. Par exemple :

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Après le codage du encodedValue variable contiendra `%22Quoted%20Value%20with%20spaces%20and%20%26%22`. Des espaces, des guillemets, signes de ponctuation et autres caractères non sécurisés sont pourcentage encodées dans leur valeur hexadécimale, par exemple, un caractère d’espace deviendra % 20.

>[!WARNING]
> N’utilisez pas d’entrée non fiable dans le cadre d’un chemin d’accès URL. Toujours passer entrée non fiable en tant que valeur de chaîne de requête.

<a name=security-cross-site-scripting-customization></a>

## <a name="customizing-the-encoders"></a>Personnaliser les encodeurs

Par défaut les encodeurs utilisent une liste verte est limitée à la plage de base Unicode Latin et coder tous les caractères en dehors de cette plage comme leurs équivalents de code de caractère. Ce comportement affecte également rendu Razor TagHelper et HtmlHelper car il utilise les encodeurs pour vos chaînes de sortie.

Le raisonnement est pour vous protéger contre les bogues de navigateur inconnu ou ultérieure (précédente navigateur bogues ont dépassé de l’analyse basée sur le traitement des caractères non anglais). Si votre site web utilise de façon intensive les caractères non latins, telles que le chinois, cyrillique, ou autres cela est probablement pas le comportement souhaité.

Vous pouvez personnaliser les listes safe encodeur pour inclure Unicode plages appropriés à votre application lors du démarrage, dans `ConfigureServices()`.

Par exemple, à l’aide de la configuration par défaut que vous pouvez utiliser un HtmlHelper Razor comme ;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Lorsque vous affichez la source de la page web vous verrez qu'a été restitué comme suit, avec le texte chinois encodé ;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Pour élargir les caractères traités comme sécurisés par l’encodeur vous permet d’insérer la ligne suivante dans le `ConfigureServices()` méthode dans `startup.cs`;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

Cet exemple s’étend de la liste pour inclure les CjkUnifiedIdeographs plage Unicode. La sortie rendue deviendrait maintenant

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Les plages de liste verte sont spécifiées sous forme de graphiques de code Unicode, pas les langues. Le [norme Unicode](http://unicode.org/) possède une liste de [code graphiques](http://www.unicode.org/charts/index.html) que vous pouvez utiliser pour rechercher le graphique qui contient les caractères. Chaque encodeur, Html, JavaScript et Url, doit être configuré séparément.

> [!NOTE]
> Personnalisation de la liste n’affecte que les encodeurs provenant via DI. Si vous accédez directement à un encodeur via `System.Text.Encodings.Web.*Encoder.Default` ensuite la valeur par défaut, Latin de base uniquement les listes fiables sera utilisé.

## <a name="where-should-encoding-take-place"></a>Où doit encodage effectuée ?

Général acceptés consiste encodage intervient au point de sortie et les valeurs encodées ne doivent jamais être stockées dans une base de données. Encodage dans la phase de sortie vous permet de modifier l’utilisation des données, par exemple, à partir de HTML à une valeur de chaîne de requête. Aussi, il vous permet de rechercher facilement vos données sans avoir à encoder les valeurs avant de les rechercher et vous permet de tirer parti des modifications ou des correctifs de bogues apportées aux encodeurs.

## <a name="validation-as-an-xss-prevention-technique"></a>Validation comme une technique de prévention XSS

La validation peut être un outil utile dans limitation des attaques XSS. Par exemple, une chaîne numérique simple contenant uniquement les caractères 0-9 ne déclenche pas d’une attaque XSS. Validation devient plus complexe que vous souhaitez accepter HTML dans l’entrée d’utilisateur - l’analyse d’entrée HTML est difficile, voire impossible. MarkDown et autres formats de texte est une option plus sûre pour l’entrée riche. Vous ne devez jamais vous appuyer sur la validation uniquement. Toujours encoder une entrée non approuvée avant la sortie, quel que soit le quel type de validation que vous avez effectué.

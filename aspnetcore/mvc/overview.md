---
title: "Vue d’ensemble des principaux d’ASP.NET MVC"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 89af38d1-52e0-4db7-b791-dbce909b0714
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/overview
ms.openlocfilehash: 3e95ccd8aeda54a050cd656ea0c831fd928f53a2
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="overview-of-aspnet-core-mvc"></a>Vue d’ensemble des principaux d’ASP.NET MVC

Par [Steve Smith](http://ardalis.com)

Le cœur de ASP.NET MVC est une infrastructure riche pour la création d’applications web et API à l’aide du modèle-Vue-contrôleur concevoir le modèle.

## <a name="what-is-the-mvc-pattern"></a>Quel est le modèle MVC ?

Le modèle d’architecture Model-View-Controller (MVC) sépare une application en trois groupes de composants principaux : les modèles, vues et contrôleurs. Ce modèle permet d’améliorer [séparation des préoccupations](http://deviq.com/separation-of-concerns/). Demandes de l’utilisateur à l’aide de ce modèle, sont acheminés vers un contrôleur qui est responsable de l’utilisation du modèle pour effectuer des actions de l’utilisateur et/ou de récupérer les résultats de requêtes. Le contrôleur choisit la vue à afficher à l’utilisateur et lui fournit toutes les données de modèle qu’il requiert.

Le diagramme suivant montre les trois composants principaux et ceux qui font référence aux autres :

![Modèle MVC](overview/_static/mvc.png)

Cette limite de responsabilités vous permet de faire évoluer l’application en termes de complexité, car il est plus facile de code, déboguer et tester un élément (modèle, vue ou contrôleur) qui a un seul travail (et suit le [principe de responsabilité unique ](http://deviq.com/single-responsibility-principle/)). Il est plus difficile de mise à jour, de test et le code de débogage qui a des dépendances réparties sur deux ou plusieurs de ces trois zones. Par exemple, logique de l’interface utilisateur a tendance à changer plus fréquemment que la logique métier. Si la présentation code et la logique métier est combinée en un seul objet, vous devez modifier l’objet contenant la logique métier chaque fois que vous modifiez l’interface utilisateur. Ceci est susceptible d’introduire des erreurs et nécessitent un nouveau test de toute la logique métier après modification de chaque interface utilisateur minimale.

> [!NOTE]
> La vue et le contrôleur dépendent du modèle. Toutefois, le modèle dépend de la vue, ni le contrôleur. Il s’agit d’un des principaux avantages de la séparation. Cette séparation permet le modèle pour être générées et testées indépendamment de la présentation visuelle.

### <a name="model-responsibilities"></a>Responsabilités du modèle

Le modèle dans une application MVC représente l’état de l’application et de toute logique métier ou d’opérations qui doivent être effectuées par celui-ci. Logique métier doit être encapsulée dans le modèle, ainsi que toute logique d’implémentation de la persistance de l’état de l’application. Vues fortement typées utilisera généralement types ViewModel spécifiquement conçus pour contenir les données à afficher sur cette vue ; le contrôleur crée et remplit ces instances ViewModel à partir du modèle.

> [!NOTE]
> Il existe de nombreuses façons d’organiser le modèle dans une application qui utilise le modèle architectural MVC. En savoir plus sur certaines [différents types de types de modèles](http://deviq.com/kinds-of-models/).

### <a name="view-responsibilities"></a>Afficher les responsabilités

Les vues sont chargés pour la présentation du contenu via l’interface utilisateur. Ils utilisent le [moteur d’affichage Razor](#razor-view-engine) pour incorporer le code .NET dans le balisage HTML. Il doit y avoir minimum de logique dans les vues, et toute logique dans les doit être liés à la présentation du contenu. Si vous trouvez la nécessité d’effectuer une grande partie de la logique dans l’affichage des fichiers pour afficher des données à partir d’un modèle complexe, envisagez d’utiliser un [composant de vue](views/view-components.md), ViewModel, ou le modèle d’affichage pour simplifier l’affichage.

### <a name="controller-responsibilities"></a>Responsabilités de contrôleur

Les contrôleurs sont les composants qui gèrent l’intervention de l’utilisateur, travailler avec le modèle et finalement sélectionnent une vue à restituer. Dans une application MVC, la vue affiche uniquement les informations ; le contrôleur gère et répond à l’entrée d’utilisateur et d’interaction. Dans le modèle MVC, le contrôleur est le point d’entrée initial et est chargé de sélectionner quel modèle pour travailler avec les types et la vue à restituer (par conséquent, son nom - elle contrôle la manière dont l’application répond à une requête donnée).

> [!NOTE]
> Contrôleurs de ne doivent pas être trop complexe par trop de responsabilités. Pour conserver la logique du contrôleur de devenir trop complexe, utilisez la [principe de responsabilité unique](http://deviq.com/single-responsibility-principle/) par émission de données logique d’entreprise dans le modèle de domaine et le contrôleur.

>[!TIP]
> Si vous trouvez que les actions de contrôleur effectuent fréquemment les mêmes types d’actions, vous pouvez suivre le [vous-même ne répétez principe](http://deviq.com/don-t-repeat-yourself/) en déplaçant ces actions courantes dans [filtres](#filters).

## <a name="what-is-aspnet-core-mvc"></a>Nouveautés d’ASP.NET MVC de base

L’infrastructure ASP.NET MVC de base est une léger et open source, framework de présentation simple et facilement testable optimisé pour une utilisation avec ASP.NET Core.

Cœur d’ASP.NET MVC offre un moyen de basée sur des modèles pour créer des sites Web dynamiques qui permet une séparation claire des préoccupations. Il vous donne un contrôle total sur le balisage, prend en charge les développement TDD rapide et convivial et utilise les normes web les plus récentes.

## <a name="features"></a>Fonctionnalités

Base d’ASP.NET MVC inclut les éléments suivants :

* [Routage](#routing)
* [Liaison de modèle](#model-binding)
* [Validation du modèle](#model-validation)
* [Injection de dépendance](../fundamentals/dependency-injection.md)
* [Filtres](#filters)
* [Zones](#areas)
* [API Web](#web-apis)
* [Testabilité](#testability)
* [Moteur d’affichage Razor](#razor-view-engine)
* [Vues fortement typées](#strongly-typed-views)
* [Programmes d’assistance de balise](#tag-helpers)
* [Affichage des composants](#view-components)

### <a name="routing"></a>Routage

ASP.NET MVC de base est construit sur [ASP.NET Core routage](../fundamentals/routing.md), un composant de mappage d’URL puissant qui vous permet de créer des applications ayant des URL compréhensibles et de recherches. Cela vous permet de définir d’affectation de noms des modèles d’URL de votre application qui fonctionnent bien pour l’optimisation des moteurs de recherche (SEO) et pour la génération de lien, sans tenir compte de la façon dont les fichiers sur votre serveur web sont organisés. Vous pouvez définir votre itinéraires à l’aide d’une syntaxe de modèle d’itinéraire pratique qui prend en charge les contraintes de valeur d’itinéraire, les valeurs par défaut et les valeurs facultatives.

*Le routage basé sur une convention* Active globalement définir l’URL qui met en forme votre application accepte et comment chacun de ces formats mappe à une méthode d’action spécifique sur donné contrôleur. Lorsqu’une demande entrante est reçue, le moteur de routage analyse l’URL correspond à l’un des formats d’URL définies et appelle ensuite la méthode d’action du contrôleur associé.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

*Attribut de routage* vous permet de spécifier des informations de routage en décorant vos contrôleurs et vos actions avec les attributs qui définissent des itinéraires de votre application. Cela signifie que vos définitions de route sont placées en regard de l’action avec lequel ils sont associés et le contrôleur.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [1, 4]}} -->

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
  [HttpGet("{id}")]
  public IActionResult GetProduct(int id)
  {
    ...
  }
}
```

### <a name="model-binding"></a>Liaison de modèle

ASP.NET Core MVC [liaison de modèle](models/model-binding.md) convertit les données de demande client (valeurs de formulaire, données d’itinéraire, les paramètres de chaîne de requête, les en-têtes HTTP) en objets que le contrôleur peut traiter. Par conséquent, votre logique de contrôleur ne doit pas nécessairement faire le travail d’identifier les données de demande entrant ; Il a simplement les données en tant que paramètres aux méthodes d’action.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a>validation du modèle

ASP.NET MVC de base prend en charge [validation](models/validation.md) en décorant votre objet de modèle avec les attributs de validation de données d’annotation. Les attributs de validation sont vérifiées sur le côté client avant que les valeurs sont publiées sur le serveur, ainsi que sur le serveur avant l’action du contrôleur est appelée.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4, 5, 8, 9]}} -->

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

Une action de contrôleur :

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [3]}} -->

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // If we got this far, something failed, redisplay form
    return View(model);
}
```

Le framework gère la validation des données de demande à la fois sur le client et sur le serveur. La logique de validation spécifiée sur les types de modèle est ajoutée aux vues rendus sous forme d’annotations discrètes et est appliquée dans le navigateur avec [jQuery Validation](http://jqueryvalidation.org/).

### <a name="dependency-injection"></a>Injection de dépendance

ASP.NET Core a une prise en charge intégrée pour [injection de dépendance (DI)](../fundamentals/dependency-injection.md). Dans ASP.NET MVC de base, [contrôleurs](controllers/dependency-injection.md) pouvez demande nécessaires services via leurs constructeurs, ce qui leur permet de suivre les [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/).

Votre application peut également utiliser [injection de dépendances des fichiers dans la vue](views/dependency-injection.md), à l’aide du `@inject` la directive :

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1]}} -->

```html
@inject SomeService ServiceName
<!DOCTYPE html>
<html>
<head>
  <title>@ServiceName.GetTitle</title>
</head>
<body>
  <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>Filtres

[Filtres](controllers/filters.md) aider les développeurs d’encapsuler des problèmes transversaux, telles que la gestion des exceptions ou d’autorisation. Les filtres permettent une logique personnalisée en cours d’exécution et de post-traitement pour les méthodes d’action et peuvent être configurées pour s’exécuter à certains points dans le pipeline de l’exécution pour une demande donnée. Les filtres peuvent être appliquées aux contrôleurs ou les actions en tant qu’attributs (ou peuvent être exécutés de manière globale). Plusieurs filtres (tels que `Authorize`) sont inclus dans le framework.


```csharp
[Authorize]
   public class AccountController : Controller
   {

```

### <a name="areas"></a>Zones

[Zones](controllers/areas.md) fournissent un moyen de partitionner une application Web ASP.NET Core MVC volumineuse en regroupements fonctionnels plus petits. Une zone est en réalité une structure MVC à l’intérieur d’une application. Dans un projet MVC, les composants logiques tels que le modèle, le contrôleur et vue sont conservées dans des dossiers différents et MVC utilise les conventions d’affectation de noms pour créer la relation entre ces composants. Pour une application volumineuse, il peut être avantageux de partition de l’application en différents domaines de niveau élevés de fonctionnalités. Par exemple, une application de commerce électronique avec plusieurs entités, telles que l’extraction, de facturation et de recherche, etc.. Chacune de ces unités ont leurs propres vues des composants logiques, les contrôleurs et les modèles.

### <a name="web-apis"></a>API Web

En plus de constituer une plate-forme idéale pour la création de sites web, ASP.NET MVC de base est très bien en charge API Web de génération. Vous pouvez créer des services qui peuvent atteindre un large éventail de clients, y compris les navigateurs et périphériques mobiles.

Le framework prend en charge pour la négociation de contenu HTTP avec prise en charge intégrée pour [mise en forme des données](models/formatting.md) en tant que JSON ou XML. Écrire [formateurs personnalisés](advanced/custom-formatters.md) pour ajouter la prise en charge de vos propres formats.

Utilisez la génération de la liaison pour prendre en charge hypermédia. Facilement activer la prise en charge de [ressources cross-origin (CORS) de partage](http://www.w3.org/TR/cors/) afin que vos API Web peuvent être partagés entre plusieurs applications Web.

### <a name="testability"></a>Testabilité

Utilisation de l’infrastructure des interfaces et injection de dépendances conviennent parfaitement aux tests unitaires et l’infrastructure comprend des fonctionnalités (par exemple, un fournisseur TestHost et en mémoire pour Entity Framework) qui [test d’intégration](../testing/integration-testing.md) rapide simple et également. En savoir plus sur [logique du contrôleur de test](controllers/testing.md).

### <a name="razor-view-engine"></a>Moteur d’affichage Razor

[Les vues ASP.NET MVC de base](views/overview.md) utiliser le [moteur d’affichage Razor](views/razor.md) pour restituer des vues. Razor est un langage de balisage de modèle compact, expressif et fluide pour définir des vues à l’aide de code c# incorporé. Razor est utilisé pour générer dynamiquement le contenu web sur le serveur. Vous pouvez correctement mélangez du code de serveur avec le code et le contenu du côté client.

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

Le moteur d’affichage Razor que vous pouvez définir à l’aide de [dispositions](views/layout.md), [vues partielles](views/partial.md) et sections remplaçables.

### <a name="strongly-typed-views"></a>Vues fortement typées

Vues Razor dans MVC peuvent être fortement typées en fonction de votre modèle. Contrôleurs peuvent passer un modèle fortement typé à l’activation de vos vues pour que la vérification du type et IntelliSense prend en charge les vues.

Par exemple, la vue suivante définit un modèle de type `IEnumerable<Product>`:

```html
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>Programmes d’assistance de balise

[Programmes d’assistance de balise](views/tag-helpers/intro.md) permettre au code côté serveur participer à la création et le rendu des éléments HTML dans les fichiers Razor. Vous pouvez utiliser des programmes d’assistance de balise pour définir des balises personnalisées (par exemple, `<environment>`) ou pour modifier le comportement de balises existantes (par exemple, `<label>`). Programmes d’assistance de balise lier à des éléments spécifiques, selon le nom de l’élément et ses attributs. Ils fournissent les avantages de rendu côté serveur tout en conservant un expérience d’édition de HTML.

Il existe de nombreuses applications auxiliaires balise intégrés pour les tâches courantes - telles que la création de formulaires, des liens, des ressources de chargement et plus - et encore plus disponibles dans les référentiels GitHub publics et comme NuGet packages. Programmes d’assistance de balise sont créés en c#, et ils conviennent à des éléments HTML en fonction de la balise parente, nom d’attribut ou nom de l’élément. Par exemple, la fonction intégrée LinkTagHelper peut être utilisé pour créer un lien vers le `Login` action de la `AccountsController`:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3]}} -->

```html
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

Le `EnvironmentTagHelper` peut être utilisé pour inclure des scripts différents dans vos affichages (par exemple, raw ou réduite) en fonction de l’environnement d’exécution, telles que le développement, intermédiaire ou de Production :

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1, 3, 4, 9]}} -->

```html
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

Programmes d’assistance de balise fournissent une expérience de développement HTML convivial et un environnement IntelliSense riche pour la création d’un balisage HTML et Razor. La plupart des programmes d’assistance de balise intégrée cible des éléments HTML existants et fournit des attributs de côté serveur pour l’élément.

### <a name="view-components"></a>Affichage des composants

[Afficher les composants](views/view-components.md) vous permettent d’empaqueter la logique de rendu et la réutiliser dans toute l’application. Elles ressemblent à [vues partielles](views/partial.md), mais avec la logique associée.

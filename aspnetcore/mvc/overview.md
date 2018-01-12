---
title: "Vue d’ensemble du modèle MVC d’ASP.NET Core"
author: ardalis
description: "Découvrez comment ASP.NET Core MVC est une infrastructure riche pour la création d’applications web et les API à l’aide du design pattern Modèle-Vue-Contrôleur."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 01/08/2018
ms.topic: article
ms.assetid: 89af38d1-52e0-4db7-b791-dbce909b0714
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/overview
ms.openlocfilehash: 33c293e15c0a7f18bbace9dc564fe11d93a7d509
ms.sourcegitcommit: df2157ae9aeea0075772719c29784425c783e82a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="overview-of-aspnet-core-mvc"></a>Vue d’ensemble des principes d’ASP.NET Core MVC
---

Par [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC est une infrastructure riche pour la création d’applications web et d'APIs à l’aide du modèle de conception Model-View-Controller.

## <a name="what-is-the-mvc-pattern"></a>Quel est le modèle de conception MVC ?

Le modèle d’architecture Model-View-Controller (MVC) sépare une application en trois groupes de composants principaux : les modèles, les vues et les contrôleurs. Ce modèle permet d’effectur de la [séparation des préoccupations](http://deviq.com/separation-of-concerns/). En utilisant ce modèle, les demandes de l’utilisateur sont acheminése vers un contrôleur qui à la responsabilité de fonctionner avec le modèle pour effectuer des actions de l’utilisateur et/ou de récupérer les résultats de requêtes. Le contrôleur choisit la vue à afficher à l’utilisateur et lui fournit toutes les données de modèle dont elle a besoin.

Le diagramme suivant montre les trois composants principaux et les références entre eux :

![Modèle MVC](overview/_static/mvc.png)

Cette délimitation des responsabilités vous permet de faire évoluer l’application en terme de complexité, car il est plus facile de coder, déboguer et tester un élément (modèle, vue ou contrôleur) qui a une seule fonction (et suit le [principe de responsabilité unique ](http://deviq.com/single-responsibility-principle/)). Il est plus difficile de mettre à jour, de tester et de déboguer du code qui a des dépendances réparties sur deux ou plusieurs de ces trois zones. Par exemple, la logique de l’interface utilisateur a tendance à changer plus fréquemment que la logique métier. Si le code de présentation et la logique métier sont combinées en un seul objet, vous devez modifier l’objet contenant la logique métier chaque fois que vous modifiez l’interface utilisateur. Ceci est susceptible d’introduire des erreurs et nécessite un nouveau test de toute la logique métier après chaque modification mineure de l'interface utilisateur.

> [!NOTE]
> La vue et le contrôleur dépendent du modèle. Toutefois, le modèle ne dépend ni de la vue, ni du contrôleur. Il s’agit d’un des principaux avantages de la séparation. Cette séparation permet au modèle d'être généré et testé indépendamment de la présentation visuelle.

### <a name="model-responsibilities"></a>Responsabilités du modèle

Le modèle dans une application MVC représente l’état de l’application et de n'importe quelles logiques métier ou opérations qui doivent être effectuées par celui-ci. La logique métier doit être encapsulée dans le modèle, ainsi que toute la logique d’implémentation de la persistance de l’état de l’application. Les vues fortement typées utiliseront généralement des types ViewModel spécifiquement conçus pour contenir les données à afficher dans cette vue; le contrôleur créera et remplit ces instances ViewModel à partir du modèle.

> [!NOTE]
> Il existe de nombreuses façons d’organiser le modèle dans une application qui utilise le modèle d'architecture MVC. En savoir plus sur les [différents types de types de modèles](http://deviq.com/kinds-of-models/).

### <a name="view-responsibilities"></a>Afficher les responsabilités

Les vues sont chargées de présenter du contenu via l’interface utilisateur. Elles utilisent le [moteur d’affichage Razor](#razor-view-engine) pour incorporer le code .NET dans le balisage HTML. Il doit y avoir un minimum de logique dans les vues, et toute logique présente doit être liée à la présentation du contenu. Si vous trouvez nécessaire d’effectuer une grande partie de la logique dans l’affichage des fichiers pour afficher des données à partir d’un modèle complexe, envisagez d’utiliser un [composant de vue (View Component)](views/view-components.md), un ViewModel, ou un modèle d’affichage (view template) pour simplifier l’affichage.

### <a name="controller-responsibilities"></a>Responsabilités du contrôleur

Les contrôleurs sont les composants qui gèrent l'interaction avec l’utilisateur, travaillent avec le modèle et finalement sélectionnent une vue à restituer. Dans une application MVC, la vue affiche uniquement les informations ; le contrôleur gère et répond à la saisie de l’utilisateur et à l’interaction. Dans le modèle MVC, le contrôleur est le point d’entrée initial et est chargé de sélectionner les types de modèle avec lequels travailler et la vue à restituer (d'où son nom - il contrôle la manière dont l’application répond à une requête donnée).

> [!NOTE]
> Les contrôleurs ne doivent pas être trop complexes en terme de responsabilités. Pour empêcher la logique du contrôleur de devenir trop complexe, utilisez le [principe de responsabilité unique](http://deviq.com/single-responsibility-principle/) enlevant de la logique métier en dehors du contrôleur pour la placer dans le modèle de domaine.

>[!TIP]
> Si vous trouvez que les actions du contrôleur effectuent fréquemment les mêmes types d’actions, vous pouvez suivre le [principe DRY (Don't Repeat Yourself : Ne vous répétez pas) ](http://deviq.com/don-t-repeat-yourself/) en déplaçant ces actions courantes dans des [filtres](#filters).

## <a name="what-is-aspnet-core-mvc"></a>Nouveautés d’ASP.NET MVC Core

L’infrastructure ASP.NET MVC Core est framework de présentation léger, open source, facilement testable et optimisé pour une utilisation avec ASP.NET Core.

ASP.NET MVC Core offre un fonctionnement basé sur des patterns pour créer des sites Web dynamiques qui permet une séparation claire des préoccupations. Il vous donne un contrôle total sur le balisage, prend en charge les développements TDD et utilise les standards web les plus récentes.

## <a name="features"></a>Fonctionnalités

ASP.NET MVC core inclut les éléments suivants :

* [Le routage](#routing)
* [La liaison de modèle (Model binding)](#model-binding)
* [La validation de modèle](#model-validation)
* [L'injection de dépendance](../fundamentals/dependency-injection.md)
* [Les filtres](#filters)
* [Les zones (areas)](#areas)
* [Les APIs Web](#web-apis)
* [La testabilité](#testability)
* [Le moteur d’affichage Razor](#razor-view-engine)
* [Les vues fortement typées](#strongly-typed-views)
* [Les Tag Helpers](#tag-helpers)
* [Les composants de vues (View components)](#view-components)

### <a name="routing"></a>Routage

ASP.NET MVC Core est construit sur [le routage d'ASP.NET Core](../fundamentals/routing.md), un composant de mappage d’URL puissant qui vous permet de créer des applications ayant des URLs compréhensibles et découvrables. Cela vous permet de définir les patterns de noms d’URL de votre application qui fonctionnent bien pour l’optimisation des moteurs de recherche (SEO) et pour la génération de lien, sans tenir compte de la façon dont les fichiers sont organisés sur votre serveur web. Vous pouvez définir vos routes à l’aide d’une syntaxe pratique de modèle de routes (route template) qui prend en charge les contraintes de valeur, les valeurs par défaut et les valeurs facultatives de routes.

*Le routage basé sur une convention* vous permet de définir globalement les formats d'URL que votre application accepte et comment chacun de ces formats est mappé à une méthode d’action spécifique sur un contrôleur donné. Lorsqu’une demande entrante est reçue, le moteur de routage analyse l’URL et le fait correspondre à l’un des formats d’URL définis et appelle ensuite la méthode d’action du contrôleur associé.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

*Le routage par attribut (Attribute routing)* vous permet de spécifier des informations de routage en décorant vos contrôleurs et vos actions avec les attributs qui définissent les routes de votre application. Cela signifie que vos définitions de route sont placées dans le contrôleur au-dessus de l’action avec laquelle elles sont associées.

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

### <a name="model-binding"></a>Liaison de modèle (Modèle binding)

La [liaison de modèle](models/model-binding.md) ASP.NET Core MVC convertit les données de requête client (les valeurs de formulaire, les données de routage, les paramètres de chaîne de requête, les en-têtes HTTP) en objets que le contrôleur peut traiter. Par conséquent, votre logique de contrôleur ne doit pas nécessairement faire le travail d’identifier les données de requête entrante; Il a simplement les données en tant que paramètres dans ces méthodes d’action.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a>validation du modèle

ASP.NET Core MVC prend en charge la [validation](models/validation.md) en décorant votre objet de modèle avec des attributs de validation de données d’annotation. Les attributs de validation sont vérifiés côté client avant que les valeurs ne soient publiées sur le serveur, ainsi que sur le serveur avant que l’action du contrôleur ne soit appelée.

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

Une action de contrôleur :

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

Le framework gèrera la validation des données d'une requête à la fois côté client et côté serveur. La logique de validation spécifiée sur les types de modèle est ajoutée aux vues rendues sous forme d’annotations discrètes (unobtrusive) et est réalisée dans le navigateur avec [jQuery Validation](https://jqueryvalidation.org/).

### <a name="dependency-injection"></a>Injection de dépendance

ASP.NET Core a une prise en charge intégrée pour l'[injection de dépendance (DI)](../fundamentals/dependency-injection.md). Dans ASP.NET Core MVC, les [contrôleurs](controllers/dependency-injection.md) peuvent faire appel à des services nécessaires via leurs constructeurs, ce qui leur permet de suivre le [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/).

Votre application peut également utiliser l'[injection de dépendance dans les fichiers de vue](views/dependency-injection.md), à l’aide de la directive `@inject` :

```cshtml
@inject SomeService ServiceName
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>Filtres

Les [filtres](controllers/filters.md) aident les développeurs à encapsuler des problèmes transversaux, tels que la gestion des exceptions ou l’autorisation. Les filtres permettent une logique personnalisée en cours d’exécution et de post-traitement pour les méthodes d’action et peuvent être configurés pour s’exécuter à certains endroits dans le pipeline de l’exécution pour une demande donnée. Les filtres peuvent être appliqués aux contrôleurs ou aux actions sous forme d’attributs (ou peuvent être exécutés de manière globale). Plusieurs filtres (tels que `Authorize`) sont inclus dans le framework.


```csharp
[Authorize]
   public class AccountController : Controller
   {
```

### <a name="areas"></a>Zones (Areas)

Les [zones](controllers/areas.md) fournissent un moyen de partitionner une application Web ASP.NET Core MVC volumineuse en regroupements fonctionnels plus petits. Une zone est en réalité une structure MVC à l’intérieur d’une application. Dans un projet MVC, les composants logiques tels que les modèles, les contrôleurs et les vues sont conservés dans des dossiers différents et MVC utilise les conventions de nommage pour créer la relation entre ces composants. Pour une application volumineuse, il peut être avantageux de partitionner l’application en différentes zones de fonctionnalités de premier niveau. Par exemple, une application de commerce électronique avec plusieurs entités, telles que le paiement, le facturation et la recherche, etc.. Chacune de ces unités ont leurs propres composants logiques de vues, contrôleurs et modèles.

### <a name="web-apis"></a>API Web

En plus de constituer une plate-forme idéale pour la création de sites web, ASP.NET Core MVC a une très bonne prise en charge pour la génération d'API Web. Vous pouvez créer des services qui peuvent atteindre un large éventail de clients, y compris les navigateurs et périphériques mobiles.

Le framework inclut le support de la négociation de contenu HTTP avec prise en charge intégrée pour la [mise en forme des données](models/formatting.md) en JSON ou XML. Vous pouvez écrire des [formateurs personnalisés](advanced/custom-formatters.md) pour ajouter la prise en charge de vos propres formats.

Utilisez la génération de la lien pour activer la prise en charge de liens hypermédia. Activez facilement la prise en charge de [Le partage de ressources cross-origin (CORS)](http://www.w3.org/TR/cors/) afin que vos APIs Web puissent être partagées entre plusieurs applications Web.

### <a name="testability"></a>Testabilité

L'utilisation des interfaces et de l'injection de dépendances de l’infrastructure conviennent parfaitement aux tests unitaires et l’infrastructure comprend des fonctionnalités (par exemple, un fournisseur TestHost et InMemory pour Entity Framework) qui rendent les [tests d’intégration](../testing/integration-testing.md) rapides et simples. En savoir plus sur [logique de test de contrôleur](controllers/testing.md).

### <a name="razor-view-engine"></a>Moteur d’affichage Razor

[Les vues ASP.NET Core MVC](views/overview.md) utilisent le [moteur d’affichage Razor](views/razor.md) pour restituer les vues. Razor est un langage de balisage de modèle compact, expressif et fluide pour définir des vues à l’aide de code c# incorporé. Razor est utilisé pour générer dynamiquement le contenu web sur le serveur. Vous pouvez mélangez proprement du code serveur avec du contenu et du code côté client.

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

En utilisant le moteur d’affichage Razor, vous pouvez définir des [dispositions](views/layout.md), des [vues partielles](views/partial.md) et des sections remplaçables.

### <a name="strongly-typed-views"></a>Vues fortement typées

Les vues Razor dans MVC peuvent être fortement typées en fonction de votre modèle. les contrôleurs peuvent passer un modèle fortement typé aux vues en permettant la vérification du type et le support de l'IntelliSense.

Par exemple, la vue suivante définit un modèle de type `IEnumerable<Product>`:

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>Tag helpers

Les [Tag helpers](views/tag-helpers/intro.md) permettent au code côté serveur de participer à la création et le rendu des éléments HTML dans les fichiers Razor. Vous pouvez utiliser des Tag helpers pour définir des balises personnalisées (par exemple, `<environment>`) ou pour modifier le comportement de balises existantes (par exemple, `<label>`). Les Tag helpers associent des éléments spécifiques en fonction du nom de l’élément et des ses attributs. Ils fournissent les avantages de rendu côté serveur tout en conservant la possibilité d'éditer le HTML.

Il existe de nombreux Tag helpers intégrés pour les tâches courantes - telles que la création de formulaires, des liens, de chargement de ressources et plus - et bien d'autres sont disponibles dans les dépôts GitHub publics et sous forme de NuGet packages. Les Tag helpers sont créés en c#, et ils ciblent des éléments HTML en fonction de la balise parente, du nom d’attribut ou du nom de l’élément. Par exemple, la fonction intégrée LinkTagHelper peut être utilisée pour créer un lien vers l'action `Login` du `AccountController`:

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

L'`EnvironmentTagHelper` peut être utilisé pour inclure des scripts différents dans vos vues (par exemple, bruts ou minifiés) en fonction de l’environnement d’exécution, telles que le développement, le staging ou la production :

```cshtml
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

Les Tag helpers fournissent une expérience de développement HTML conviviale et un environnement IntelliSense riche pour la création de balises HTML et Razor. La plupart des Tag helpers intégrés ciblent des éléments HTML existants et fournissent des attributs côté serveur pour l’élément.

### <a name="view-components"></a>Composants de vues

Les [Composants de vues](views/view-components.md) vous permettent d’empaqueter la logique de rendu et la réutiliser dans toute l’application. Ils ressemblent aux [vues partielles](views/partial.md), mais avec de la logique associée.

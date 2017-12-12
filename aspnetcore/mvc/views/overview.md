---
title: "Vues de base d’ASP.NET MVC"
author: ardalis
description: "Découvrez comment gérer les vues de présentation des données de l’application et l’interaction utilisateur dans ASP.NET MVC de base."
keywords: ASP.NET Core, afficher, MVC, razor, viewmodel, viewdata, viewbag
ms.author: riande
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.assetid: 668c320d-c050-45e3-8161-2f460dc93b2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
ms.openlocfilehash: 4530d2f500dd887bf649a753283fb3e4af995322
ms.sourcegitcommit: c2f6c593d81fbd90e6ddd672fe0a5636d06b615a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="views-in-aspnet-core-mvc"></a>Vues de base d’ASP.NET MVC

Par [Steve Smith](https://ardalis.com/) et [Luke Latham](https://github.com/guardrex)

Dans le **M**odèle -**V**UE -**C**ontroller (MVC), modèle, le *vue* gère l’interaction utilisateur et de présentation des données de l’application. Une vue est un modèle HTML incorporé [balisage de Razor](xref:mvc/views/razor). Balisage de Razor est un code qui interagit avec le balisage HTML pour générer une page Web qui est envoyée au client.

Dans ASP.NET MVC de base, les vues sont *.cshtml* fichiers qui utilisent le [langage de programmation c#](/dotnet/csharp/) dans le balisage de Razor. En règle générale, afficher les fichiers sont regroupés dans des dossiers nommés pour chacune de l’application [contrôleurs](xref:mvc/controllers/actions). Les dossiers sont stockées dans un *vues* dossier à la racine de l’application :

![Dossier de vues dans l’Explorateur de solutions de Visual Studio est ouvert avec le dossier de base ouvert pour afficher les fichiers About.cshtml, Contact.cshtml et Index.cshtml](overview/_static/views_solution_explorer.png)

Le *accueil* contrôleur est représenté par un *accueil* dossier à l’intérieur de la *vues* dossier. Le *accueil* dossier contient les vues pour la *sur*, *Contact*, et *Index* des pages Web (page d’accueil). Quand un utilisateur demande une de ces trois des pages Web, les actions de contrôleur dans le *accueil* contrôleur déterminer lequel des trois vues est utilisé pour générer et retourner une page Web à l’utilisateur.

Utilisez [dispositions](xref:mvc/views/layout) pour fournir des sections de la page Web cohérente et de réduire la redondance du code. Dispositions contiennent souvent l’en-tête, des éléments de menu et de navigation et le pied de page. L’en-tête et le pied de page contiennent généralement de balisage standard pour de nombreux éléments de métadonnées et des liens vers des ressources de script et de style. Dispositions de vous aident à éviter ce balisage réutilisable dans vos vues.

[Les vues partielles](xref:mvc/views/partial) réduire la duplication de code grâce à la gestion de parties réutilisables de vues. Par exemple, une vue partielle est utile pour biographie de l’auteur sur un site Web de blog qui apparaît dans plusieurs vues. Biographie de l’auteur est d’ordinaire afficher le contenu et ne nécessite pas le code à exécuter afin de produire le contenu de la page Web. Créer du contenu biographie est disponible à l’affichage par la liaison de modèle uniquement, par conséquent, à l’aide d’une vue partielle pour ce type de contenu est idéale.

[Affichage des composants](xref:mvc/views/view-components) sont des vues similaires sur la valeur partielle dans la mesure où ils vous permettent de réduire le code répétitif, mais ils sont appropriés pour l’affichage du contenu qui nécessite que du code à exécuter sur le serveur pour restituer la page Web. Affichage des composants sont utiles lorsque le contenu rendu requiert une interaction de base de données, comme pour un site Web du panier d’achat. Affichage des composants ne sont pas limitées à la liaison de modèle afin de produire la sortie de la page Web.

## <a name="benefits-of-using-views"></a>Avantages de l’utilisation de vues

Les vues permettent d’établir une [ **S**eparation **o**f **C**oncerns (SoC) conception](http://deviq.com/separation-of-concerns/) au sein d’une application MVC en séparant le balisage d’interface utilisateur à partir de autres parties de l’application. Suivant SoC conception rend votre application modulaire, ce qui offre plusieurs avantages :

* L’application est plus facile à gérer, car il est mieux organisée. Les vues sont généralement regroupés en fonction de l’application. Cela rend plus facile à trouver les vues associées lorsque vous travaillez sur une fonctionnalité.
* Les parties de l’application sont faiblement couplés. Vous pouvez générer et mettre à jour des vues de l’application séparément dans les composants accès logique et les données d’entreprise. Vous pouvez modifier les vues de l’application sans nécessairement devoir mettre à jour des autres parties de l’application.
* Il est plus facile de tester des parties de l’interface utilisateur de l’application, car les vues sont des unités distinctes.
* En raison d’une meilleure organisation, il est moins probable que vous allez accidentellement sections de répétitions de l’interface utilisateur.

## <a name="creating-a-view"></a>Création d’une vue

Les vues qui sont spécifiques à un contrôleur sont créées dans le *vues / [nom du contrôleur]* dossier. Les vues qui sont partagées entre les contrôleurs sont placées dans le *Views/Shared* dossier. Pour créer une vue, ajoutez un nouveau fichier et lui donner le même nom que son action de contrôleur associé avec le *.cshtml* extension de fichier. Pour créer une vue qui correspond à la *sur* action dans le *accueil* contrôleur, créez un *About.cshtml* de fichiers dans le *Views/Home*dossier :

[!code-cshtml[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* balisage commence par la `@` symbole. Les instructions c# exécution en plaçant c# du code dans [blocs de code Razor](xref:mvc/views/razor#razor-code-blocks) définir à off par des accolades (`{ ... }`). Par exemple, consultez l’affectation de « About » pour `ViewData["Title"]` ci-dessus. Vous pouvez afficher des valeurs dans le code HTML en référençant simplement la valeur avec le `@` symbole. Consultez le contenu de la `<h2>` et `<h3>` éléments ci-dessus.

Le contenu de la vue ci-dessus n'est qu’une partie de la page Web entière qui est restituée à l’utilisateur. Le reste de la mise en page et d’autres aspects courantes de la vue sont spécifiés dans d’autres fichiers de vue. Pour plus d’informations, consultez la [rubrique de présentation](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>La façon dont les contrôleurs spécifient les vues

Les vues sont généralement retournées à partir d’actions en tant qu’un [ViewResult](/aspnet/core/api/microsoft.aspnetcore.mvc.viewresult), qui est un type de [ActionResult](/aspnet/core/api/microsoft.aspnetcore.mvc.actionresult). Votre méthode d’action peut créer et renvoyer un `ViewResult` directement, mais qui n’est pas généralement effectué. Étant donné que la plupart des contrôleurs héritent de [contrôleur](/aspnet/core/api/microsoft.aspnetcore.mvc.controller), vous utilisez simplement le `View` méthode d’assistance pour retourner le `ViewResult`:

*HomeController.cs*

[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Lorsque cette action est retournée, la *About.cshtml* indiqué dans la dernière section de vue est restituée en tant que la page Web suivante :

![Sur la page rendue dans le navigateur Edge](overview/_static/about-page.png)

Le `View` méthode d’assistance a plusieurs surcharges. Vous pouvez éventuellement spécifier :

* Un affichage explicite pour renvoyer :

  ```csharp
  return View("Orders");
  ```
* A [modèle](xref:mvc/models/model-binding) à passer à la la vue :

  ```csharp
  return View(Orders);
  ```
* Une vue et un modèle :

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Détection de la vue

Lorsqu’une action retourne une vue, un processus appelé *détection de la vue* a lieu. Ce processus détermine l’afficher le fichier est utilisé en fonction du nom de la vue. 

Le comportement par défaut de la `View` (méthode) (`return View();`) doit retourner une vue avec le même nom que la méthode d’action à partir de laquelle elle est appelée. Par exemple, le *sur* `ActionResult` nom de la méthode du contrôleur est utilisé pour rechercher un fichier de vue nommé *About.cshtml*. Tout d’abord, le runtime recherche le *vues / [nom du contrôleur]* dossier pour l’affichage. S’il ne trouve pas une vue correspondante, il recherche le *Shared* dossier pour l’affichage.

Peu importe si vous retournez implicitement le `ViewResult` avec `return View();` ou explicitement passer le nom d’affichage pour le `View` méthode avec `return View("<ViewName>");`. Dans les deux cas, afficher les recherches de découverte pour un fichier de vue correspondant dans cet ordre :

   1. *Vues /\[ControllerName]\[ViewName] .cshtml*
   1. *Les vues ouShared/\[ViewName] .cshtml*

Un chemin d’accès du fichier de vue peut être fourni au lieu d’un nom de la vue. Si vous utilisez un chemin d’accès absolu, commençant à la racine de l’application (éventuellement en commençant par « / » ou « ~ / »), le *.cshtml* extension doit être spécifiée :

```csharp
return View("Views/Home/About.cshtml");
```

Vous pouvez également utiliser un chemin d’accès relatif pour spécifier les vues dans des répertoires différents sans le *.cshtml* extension. À l’intérieur de la `HomeController`, vous pouvez retourner le *Index* afficher de votre *gérer* vues avec un chemin d’accès relatif :

```csharp
return View("../Manage/Index");
```

De même, vous pouvez indiquer le répertoire spécifique du contrôleur actif avec le «. / » préfixe :

```csharp
return View("./About");
```

[Les vues partielles](xref:mvc/views/partial) et [affichage des composants](xref:mvc/views/view-components) utilisent des mécanismes de découverte similaires (mais non identiques).

Vous pouvez personnaliser la convention par défaut pour comment les vues sont situés dans l’application à l’aide d’un [IViewLocationExpander](/aspnet/core/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).

Découverte de la vue s’appuie sur la recherche d’afficher les fichiers par nom de fichier. Si le système de fichiers sous-jacent respecte la casse, les noms des vues sont probablement respecte la casse. Pour la compatibilité entre les systèmes d’exploitation, respecter la casse entre le contrôleur et les noms d’actions et associé à afficher les dossiers et les noms de fichiers. Si vous rencontrez une erreur Impossible de trouver un fichier de vue lorsque vous travaillez avec un système de fichiers respectant la casse, vérifiez que la casse identique dans le fichier de la vue demandée et le nom de fichier réel de l’affichage.

Suivre la meilleure pratique d’organiser la structure de fichiers pour les vues afin de refléter les relations entre les contrôleurs, les actions et les vues à la facilité de maintenance et de clarté.

## <a name="passing-data-to-views"></a>Passage de données à des vues

Vous pouvez passer des données pour les vues à l’aide de plusieurs approches. L’approche la plus fiable consiste à spécifier un [modèle](xref:mvc/models/model-binding) type dans la vue. Ce modèle est communément appelé une *viewmodel*. Vous passez une instance du type viewmodel à la vue à partir de l’action.

À l’aide d’un viewmodel pour passer des données à une vue permet de tirer parti de la vue de *fort* la vérification du type. *Un typage fort* (ou *fortement typé*) signifie que chaque variable et constante a un type défini explicitement (par exemple, `string`, `int`, ou `DateTime`). La validité des types utilisés dans une vue est vérifiée au moment de la compilation.

[Visual Studio](https://www.visualstudio.com/vs/) et [Visual Studio Code](https://code.visualstudio.com/) répertorient les membres de classe fortement typée à l’aide d’une fonctionnalité appelée [IntelliSense](/visualstudio/ide/using-intellisense). Lorsque vous souhaitez afficher les propriétés d’un viewmodel, tapez le nom de variable pour le viewmodel suivi d’un point (`.`). Cela vous permet d’écrire du code plus rapidement avec moins d’erreurs.

Spécifier un modèle à l’aide de la `@model` la directive. Utiliser le modèle avec `@Model`:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Pour fournir le modèle à la vue, le contrôleur transmet en tant que paramètre :

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

Il n’existe aucune restriction sur les types de modèles que vous pouvez fournir à une vue. Nous vous recommandons d’utiliser **P**brut **O**%ld **C**LR **O**ViewModel objet (POCO) avec peu ou pas comportement (méthodes) défini. En règle générale, les classes viewmodel sont soit stockés dans le *modèles* dossier ou distinct *ViewModel* dossier à la racine de l’application. Le *adresse* viewmodel utilisé dans l’exemple ci-dessus est un viewmodel POCO stocké dans un fichier nommé *Address.cs*:

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

> [!NOTE]
> Rien ne vous empêche d’utiliser les mêmes classes pour vos types viewmodel et vos types de modèle d’entreprise. Toutefois, à l’aide des modèles distincts permet à vos vues varier indépendamment à partir de la logique métier et les données des accès des parties de votre application. Séparation des modèles et ViewModel offre également des avantages de sécurité lorsque les modèles utilisent [liaison de modèle](xref:mvc/models/model-binding) et [validation](xref:mvc/models/validation) pour les données envoyées à l’application par l’utilisateur.


<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-and-viewbag"></a>Données faiblement typée (ViewData et ViewBag)

Remarque : `ViewBag` n’est pas disponible dans les Pages Razor.

Outre les vues fortement typées, vues ont accès à un *faiblement typée* (également appelé *faiblement typé*) collecte des données. Contrairement aux types forts, *types faibles* (ou *de perdre des types*) signifie que vous ne déclariez pas explicitement le type de données que vous utilisez. Vous pouvez utiliser la collection de données faiblement typé pour le passage de petites quantités de données vers et depuis les contrôleurs et les vues.

| Passer des données entre un...                        | Exemple                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Contrôleur et une vue                             | Remplissage d’une liste déroulante avec des données.                                          |
| Affichage et un [mode](xref:mvc/views/layout)   | Définition de la  **\<titre >** contenu de l’élément dans la vue mise en page à partir d’un fichier de vue.  |
| [Vue partielle](xref:mvc/views/partial) et une vue | Un widget qui affiche des données en fonction de la page Web demandée par l’utilisateur.      |

Cette collection peut être référencée par le biais du `ViewData` ou `ViewBag` propriétés sur les contrôleurs et vues. Le `ViewData` propriété est un dictionnaire d’objets de faiblement typée. Le `ViewBag` propriété est un wrapper autour de `ViewData` qui fournit des propriétés dynamiques pour sous-jacent `ViewData` collection.

`ViewData`et `ViewBag` sont résolues dynamiquement lors de l’exécution. Dans la mesure où ils n’offrent pas de vérification de type lors de la compilation, les deux sont généralement plus sujettes aux erreurs que l’utilisation d’un modèle de vues. Pour cette raison, certains développeurs préfèrent minimale ou jamais utiliser `ViewData` et `ViewBag`.


<a name="VD"></a>

**ViewData**

`ViewData`est un [ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) objet accédé via `string` clés. Données de chaîne peuvent être stockées et utilisées directement, sans la nécessité d’un cast, mais vous devez effectuer un cast des autres `ViewData` valeurs à des types spécifiques de l’objet lorsque vous extrayez les. Vous pouvez utiliser `ViewData` pour passer des données à partir des contrôleurs vers des affichages et dans les affichages, y compris [vues partielles](xref:mvc/views/partial) et [dispositions](xref:mvc/views/layout).

Voici un exemple qui définit les valeurs pour un message d’accueil et une adresse à l’aide `ViewData` dans une action :

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

Utiliser les données dans une vue :

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

**Élément ViewBag**

Remarque : `ViewBag` n’est pas disponible dans les Pages Razor.

`ViewBag`est un [DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) objet qui fournit un accès dynamique pour les objets stockés dans `ViewData`. `ViewBag`peut être plus pratique d’utiliser, car il ne nécessite un transtypage. L’exemple suivant montre comment utiliser `ViewBag` avec le même résultat qu’à l’aide de `ViewData` ci-dessus :

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**À l’aide de ViewData et ViewBag simultanément**

Remarque : `ViewBag` n’est pas disponible dans les Pages Razor.

Étant donné que `ViewData` et `ViewBag` font référence au même sous-jacent `ViewData` collection, vous pouvez utiliser les deux `ViewData` et `ViewBag` et combiner entre eux lors de la lecture et l’écriture de valeurs.

Définir le titre à l’aide `ViewBag` et la description à l’aide `ViewData` en haut d’un *About.cshtml* vue :

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Lire les propriétés, mais l’inverse de l’utilisation de `ViewData` et `ViewBag`. Dans le *_Layout.cshtml* de fichiers, d’obtenir le titre à l’aide de `ViewData` et obtenez la description en utilisant `ViewBag`:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Souvenez-vous que les chaînes ne nécessitent un cast pour `ViewData`. Vous pouvez utiliser `@ViewData["Title"]` sans cast.

L’utilisation des deux `ViewData` et `ViewBag` sur les travaux de même temps, en tant que fait mélangeant et en lire et écrire les propriétés. Le balisage suivant est rendu :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Résumé des différences entre ViewData et ViewBag**

 `ViewBag`n’est pas disponible dans les Pages Razor.

* `ViewData`
  * Dérive de [ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), de sorte qu’elle possède des propriétés du dictionnaire qui peuvent être utiles, telles que `ContainsKey`, `Add`, `Remove`, et `Clear`.
  * Clés du dictionnaire sont des chaînes, un espace blanc est autorisé. Exemple : `ViewData["Some Key With Whitespace"]`
  * Tout type autre qu’un `string` doit être effectué dans la vue à utiliser `ViewData`.
* `ViewBag`
  * Dérive de [DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), donc il permet la création de propriétés dynamiques à l’aide de la notation par points (`@ViewBag.SomeKey = <value or object>`), et aucune conversion n’est requise. La syntaxe de `ViewBag` rend plus rapide de les ajouter à des contrôleurs et vues.
  * La plus simple vérifier les valeurs null. Exemple : `@ViewBag.Person?.Name`

**Quand utiliser ViewData ou ViewBag**

Les deux `ViewData` et `ViewBag` sont également des approches valides pour le passage de petites quantités de données entre les contrôleurs et les vues. Le choix de l’application à utiliser est selon la préférence. Vous pouvez combiner des `ViewData` et `ViewBag` objets, toutefois, le code est plus facile à lire et à maintenir avec une approche utilisée régulièrement. Les deux approches sont résolues dynamiquement lors de l’exécution et donc enclin à provoquer des erreurs d’exécution. Certaines équipes de développement de les évitent.

### <a name="dynamic-views"></a>Vues dynamiques

Les vues qui ne déclarent pas un modèle de type à l’aide de `@model` mais qui ont passé à une instance de modèle (par exemple, `return View(Address);`) peuvent référencer dynamiquement les propriétés de l’instance :

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Cette fonctionnalité offre une souplesse, mais ne propose pas de protection de la compilation ou d’IntelliSense. Si la propriété n’existe pas, la génération de la page Web échoue lors de l’exécution.

## <a name="more-view-features"></a>D’autres fonctionnalités d’affichage

[Programmes d’assistance de balise](xref:mvc/views/tag-helpers/intro) rendent facile d’ajouter le comportement côté serveur pour les balises HTML existants. À l’aide de programmes d’assistance de balise évite d’avoir à écrire du code personnalisé ou des programmes d’assistance dans les vues. Programmes d’assistance de balise sont appliquées en tant qu’attributs aux éléments HTML et sont ignorés par les éditeurs qui ne peut pas les traiter. Cela vous permet de modifier et de restituer le balisage de vue dans une variété d’outils.

Génération d’un balisage HTML personnalisé peut être obtenue avec nombreux programmes d’assistance de HTML intégrés. Logique de l’interface utilisateur plus complexe peut être gérée par [affichage des composants](xref:mvc/views/view-components). Affichage des composants fournissent le même SoC qui contrôleurs et offrent des vues. Il peuvent éliminer la nécessité pour les actions et les vues qui traitent les données utilisées par les éléments d’interface courants.

Comme de nombreux aspects d’ASP.NET Core, vues prennent en charge [injection de dépendance](xref:fundamentals/dependency-injection), autorisant les services soient [injectées dans les vues](xref:mvc/views/dependency-injection).

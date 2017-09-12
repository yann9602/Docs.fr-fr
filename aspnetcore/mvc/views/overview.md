---
title: "Vue d’ensemble de vues"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 668c320d-c050-45e3-8161-2f460dc93b2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
ms.openlocfilehash: 3b33c13f2385d3b07ba9b6f0bc0fd560abc3735c
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="rendering-html-with-views-in-aspnet-core-mvc"></a>Rendu HTML avec des vues dans ASP.NET MVC de base

Par [Steve Smith](https://ardalis.com/)

Les contrôleurs ASP.NET MVC de base peuvent retourner des résultats mis en forme à l’aide de *vues*.

## <a name="what-are-views"></a>Que sont les vues ?

Dans le modèle Model-View-Controller (MVC), le *vue* encapsule les détails de présentation de l’interaction utilisateur avec l’application. Les vues sont des modèles HTML contenant du code incorporé qui génèrent du contenu à envoyer au client. Vues utilisent [la syntaxe Razor](razor.md), ce qui permet d’interagir avec du code HTML avec un minimum de code ou de cérémonie de code.

Les vues ASP.NET MVC de base sont *.cshtml* fichiers stockés par défaut dans un *vues* dossier au sein de l’application. En règle générale, chaque contrôleur aura son propre dossier, dans lequel sont des vues pour les actions de contrôleur spécifique.

![Dossier d’affichages dans l’Explorateur de solutions](overview/_static/views_solution_explorer.png)

En plus des vues spécifiques à certaines actions, [vues partielles](partial.md), [mises en page et autres fichiers de vue spéciale](layout.md) peut être utilisé pour aider à réduire la répétition et permettre la réutilisation dans les affichages de l’application.

## <a name="benefits-of-using-views"></a>Avantages de l’utilisation de vues

Les vues fournissent [séparation des préoccupations](http://deviq.com/separation-of-concerns/) au sein d’une application MVC, encapsulant la balise de niveau interface utilisateur séparément à partir de la logique métier. ASP.NET MVC vues utilisent [la syntaxe Razor](razor.md) pour basculer HTML balisage et le serveur logique simple. Des aspects communs, répétitives d’interface utilisateur de l’application peuvent être facilement réutilisées entre les vues à l’aide de [mise en page et les directives partagés](layout.md) ou [vues partielles](partial.md).

## <a name="creating-a-view"></a>Création d’une vue

Les vues qui sont spécifiques à un contrôleur sont créées dans le *vues / [nom du contrôleur]* dossier. Les vues qui sont partagées entre les contrôleurs sont placées dans le */vues/Shared* dossier. Nommer le fichier de vue de la même que son action de contrôleur associé et ajouter la *.cshtml* extension de fichier. Par exemple, pour créer un affichage pour le *sur* action sur le *accueil* contrôleur, vous devez créer le *About.cshtml* de fichiers dans le  * /vues/Home*dossier.

Un exemple de fichier de vue (*About.cshtml*) :

[!code-html[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* code est indiqué par le `@` symbole. Instructions c# sont exécutées au sein de Razor blocs de code définir à off par des accolades (`{` `}`), par exemple l’affectation de « About » pour le `ViewData["Title"]` élément indiqué ci-dessus. Razor peut être utilisé pour afficher les valeurs dans le code HTML en référençant simplement la valeur avec le `@` symbol, comme indiqué dans le `<h2>` et `<h3>` éléments ci-dessus.

Cette vue se concentre sur la partie seulement de la sortie pour laquelle il est chargé. Le reste de la mise en page et d’autres aspects courantes de la vue, spécifiées ailleurs. En savoir plus sur [mise en page et la logique d’affichage partagé](layout.md).

## <a name="how-do-controllers-specify-views"></a>Comment les contrôleurs de la spécification des affichages ?

Les vues sont généralement retournées à partir des actions comme un `ViewResult`. Votre méthode d’action peut créer et renvoyer un `ViewResult` directement, mais plus souvent si votre contrôleur hérite `Controller`, vous allez simplement utiliser le `View` méthode d’assistance, que cet exemple montre :

*HomeController.cs*

[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Le `View` méthode d’assistance a plusieurs surcharges pour faciliter le retour des vues pour les développeurs d’applications. Vous pouvez éventuellement spécifier une vue pour retourner, ainsi que d’un objet de modèle à passer à la vue.

Lorsque cette action est retournée, la *About.cshtml* indiqué ci-dessus est restituée :

![Sur la page](overview/_static/about-page.png)

### <a name="view-discovery"></a>Détection de la vue

Lorsqu’une action retourne une vue, un processus appelé *détection de la vue* a lieu. Ce processus détermine l’afficher le fichier sera utilisé. Sauf si un fichier de vue spécifiques est spécifié, le runtime recherche une vue spécifique du contrôleur, puis recherche d’abord nom correspondant de la vue dans le *Shared* dossier.

Lorsqu’une action retourne le `View` (méthode), par exemple `return View();`, le nom de l’action est utilisé en tant que le nom de la vue. Par exemple, si cela était appelé à partir d’une méthode d’action nommée « Index », il serait équivalent à passer un nom d’affichage de « Index ». Un nom de la vue peut être explicitement transmis à la méthode (`return View("SomeView");`). Dans ces deux cas, afficher les recherches de découverte pour un fichier de vue correspondant dans :

   1. Vues /\<ControllerName > /\<ViewName > .cshtml

   2. Les vues ouShared/\<ViewName > .cshtml

>[!TIP]
> Nous vous recommandons d’appliquer la convention de simplement retourner `View()` à partir d’actions lorsque cela est possible, qu’elle provoque plus flexible et plus facile à refactoriser le code.

Un chemin d’accès du fichier de vue peut être fourni au lieu d’un nom de la vue. Si vous utilisez un chemin d’accès absolu, en commençant à la racine de l’application (éventuellement en commençant par « / » ou « ~ / »), le *.cshtml* extension doit être spécifiée en tant que partie du chemin du fichier (par exemple, `return View("Views/Home/About.cshtml");`). Vous pouvez également utiliser un chemin d’accès relatif à partir du répertoire spécifique du contrôleur dans le *vues* active pour spécifier les vues dans des répertoires différents (par exemple, `return View("../Manage/Index");` à l’intérieur de la `HomeController`). De même, vous pouvez parcourir le répertoire spécifique du contrôleur actuel (par exemple, `return View("./About");`). Notez que les chemins d’accès relatifs ne pas utiliser le *.cshtml* extension. Comme mentionné précédemment, suivez la meilleure pratique d’organiser la structure de fichiers pour les vues afin de refléter les relations entre les contrôleurs, les actions et les vues à la facilité de maintenance et de clarté.

> [!NOTE]
> [Les vues partielles](partial.md) et [affichage des composants](view-components.md) utilisent des mécanismes de découverte similaires (mais non identiques).

> [!NOTE]
> Vous pouvez personnaliser la convention par défaut concernant où vues se trouvent dans l’application à l’aide d’un `IViewLocationExpander`.

>[!TIP]
> Les noms de vue peuvent être respecte la casse selon le système de fichiers sous-jacent. Pour la compatibilité entre les systèmes d’exploitation, toujours respecter la casse entre le contrôleur et les noms d’actions associé à afficher les dossiers et les noms de fichiers.

## <a name="passing-data-to-views"></a>Passage de données à des vues

Vous pouvez passer des données pour les vues à l’aide de plusieurs mécanismes. L’approche la plus fiable consiste à spécifier un *modèle* type dans la vue (communément appelé une *viewmodel*, pour différencier des types de modèle de domaine métier), puis passez une instance de ce type à la vue à partir de l’action. Nous vous recommandons de qu'utiliser un modèle ou un modèle d’affichage pour passer des données à une vue. Ainsi, la vue tirer parti de la vérification de type fort. Vous pouvez spécifier un modèle pour une vue à l’aide de la `@model` directive :

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1]}} -->

```html
@model WebApplication1.ViewModels.Address
   <h2>Contact</h2>
   <address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

Une fois qu’un modèle a été spécifié pour une vue, l’instance envoyée à la vue est accessible dans une manière fortement typée à l’aide `@Model` comme indiqué ci-dessus. Pour fournir une instance du type de modèle à la vue, le contrôleur transmet en tant que paramètre :

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [13]}} -->

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

Il n’existe aucune restriction sur les types qui peuvent être fournis à une vue en tant que modèle. Nous vous recommandons de passage brut POCO Old CLR Object () afficher les modèles avec peu ou pas un comportement, afin que la logique métier peut être encapsulée ailleurs dans l’application. Un exemple de cette approche est la *adresse* viewmodel utilisé dans l’exemple ci-dessus :

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [13]}} -->

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
> Rien ne vous empêche d’utiliser les mêmes classes que vos types de modèle d’entreprise et de vos types de modèle d’affichage. Toutefois, les conservant à part permet à vos vues faire varier indépendamment à partir de votre modèle de domaine ou la persistance et peut offrir certains avantages de sécurité ainsi (pour les modèles que les utilisateurs envoient à l’application à l’aide de [liaison de modèle](../models/model-binding.md)).

### <a name="loosely-typed-data"></a>Données faiblement typées

Outre les vues fortement typées, toutes les vues ont accès à une collection faiblement typée de données. Cette même collection peut être référencée par le biais du `ViewData` ou `ViewBag` propriétés sur les contrôleurs et vues. Le `ViewBag` propriété est un wrapper autour de `ViewData` qui fournit une vue dynamique sur cette collection. Il n’est pas une collection distincte.

`ViewData`est un objet de dictionnaire accessible via `string` clés. Vous pouvez stocker et récupérer des objets qu’il contient, et vous devez les convertir en un type spécifique lorsque vous extrayez les. Vous pouvez utiliser `ViewData` pour passer des données à partir d’un contrôleur à des vues, ainsi que dans les vues (et les vues partielles et mises en page). Données de chaîne peuvent stockées et utilisées directement, sans recourir à un cast.

Définir des valeurs pour `ViewData` dans une action :

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

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3, 6]}} -->

```html
@{
       // Requires cast
       var address = ViewData["Address"] as Address;
   }

   @ViewData["Greeting"] World!

   <address>
       @address.Name<br />
       @address.Street<br />
       @address.City, @address.State @address.PostalCode
   </address>
   ```

Le `ViewBag` objets fournit un accès dynamique pour les objets stockés dans `ViewData`. Cela peut être plus pratique d’utiliser, car il ne nécessite un transtypage. Le même exemple que ci-dessus, à l’aide de `ViewBag` au lieu de fortement typé `address` instance dans la vue :

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1, 4, 5, 6]}} -->

```html
@ViewBag.Greeting World!

   <address>
       @ViewBag.Address.Name<br />
       @ViewBag.Address.Street<br />
       @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
   </address>
   ```

> [!NOTE]
> Étant donné que les deux font référence au même sous-jacent `ViewData` collection, vous pouvez mélanger et correspondent entre `ViewData` et `ViewBag` lors de la lecture et l’écriture de valeurs, si le fait de.

### <a name="dynamic-views"></a>Vues dynamiques

Les vues qui ne déclarent pas un type de modèle, mais ont une instance de modèle leur passée peuvent référencer cette instance dynamiquement. Par exemple, si une instance de `Address` est passé à une vue qui ne déclare pas une `@model`, la vue sera toujours en mesure de faire référence aux propriétés de l’instance dynamiquement, comme indiqué :

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [13, 16, 17, 18]}} -->

```html
<address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

Cette fonctionnalité peut offrir une certaine souplesse, mais n’offre aucune protection de compilation IntelliSense. Si la propriété n’existe pas, la page échouera lors de l’exécution.

## <a name="more-view-features"></a>D’autres fonctionnalités d’affichage

[Programmes d’assistance de balise](tag-helpers/intro.md) permet d’ajouter un comportement côté serveur à des balises HTML existantes, ce qui évite la nécessité d’utiliser du code personnalisé ou des programmes d’assistance dans les affichages. Programmes d’assistance de balise sont appliqués aux éléments HTML, qui sont ignorés par les éditeurs qui ne sont pas familiarisés avec eux, ce qui permet de balisage de la vue d’être modifié et rendus dans une variété d’outils en tant qu’attributs. Programmes d’assistance de balise ont de nombreuses utilisations et en particulier peuvent [utilisation des formulaires](working-with-forms.md) beaucoup plus facile.

Génération d’un balisage HTML personnalisé peut être obtenue avec nombreux programmes d’assistance de HTML intégrés, et une logique plus complexe de l’interface utilisateur (avec éventuellement ses propres exigences de données) peut être encapsulée dans [affichage des composants](view-components.md). Affichage des composants fournissent la même séparation des préoccupations qui offrent des contrôleurs et vues et peuvent éliminer la nécessité pour les actions et les vues afin de traiter les données utilisées par les éléments d’interface utilisateur communes.

Comme de nombreux aspects d’ASP.NET Core, vues prennent en charge [injection de dépendance](../../fundamentals/dependency-injection.md), autorisant les services soient [injectées dans les vues](dependency-injection.md).

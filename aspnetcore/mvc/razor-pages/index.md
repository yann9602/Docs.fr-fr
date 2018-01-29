---
title: "Présentation des pages Razor dans ASP.NET Core"
author: Rick-Anderson
description: "Didacticiel ASP.NET Core sur les pages Razor. Inclut MVC Core, ASP.NET Core 2.x, introduction au développement web et Visual Studio 2017. Ce document offre une vue d’ensemble de l’utilisation des pages Razor dans ASP.NET Core qui permet de faciliter le développement de scénarios orientés page."
ms.author: riande
manager: wpickett
ms.date: 09/12/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/razor-pages/index
ms.openlocfilehash: a08c1b59c7be3a27fc11e6737a1cb4b4208f2901
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>Présentation des pages Razor dans ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/rynowak)

Les pages Razor sont une nouvelle fonctionnalité d’ASP.NET Core MVC qui permet de développer des scénarios orientés page de façon plus simple et plus productive.

Si vous avez besoin d’un didacticiel qui utilise l’approche Model-View-Controller, consultez [Bien démarrer avec ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Ce document fournit une introduction aux pages Razor. Il ne s’agit pas d’un didacticiel pas à pas. Si certaines sections vous semblent difficiles à suivre, consultez [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start).

<a name="prerequisites"></a>

## <a name="aspnet-core-20-prerequisites"></a>Prérequis d’ASP.NET Core 2.0

Installez [.NET Core](https://www.microsoft.com/net/core) 2.0.0 ou ultérieur.

Si vous utilisez Visual Studio, installez [Visual Studio](https://www.visualstudio.com/vs/) 2017 version 15.3 ou ultérieure avec les charges de travail suivantes :

* **Développement web et ASP.NET**
* **Développement multiplateforme .NET Core**

<a name="rpvs17"></a>

## <a name="creating-a-razor-pages-project"></a>Création d’un projet de pages Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Pour obtenir des instructions détaillées sur la façon de créer un projet de pages Razor avec Visual Studio, consultez [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start).

#   <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Exécutez `dotnet new razor` à partir de la ligne de commande.

Ouvrez le fichier *.csproj* généré à partir de Visual Studio pour Mac.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

Exécutez `dotnet new razor` à partir de la ligne de commande.

#   <a name="net-core-clitabnetcore-cli"></a>[CLI .NET Core](#tab/netcore-cli) 

Exécutez `dotnet new razor` à partir de la ligne de commande.

---

## <a name="razor-pages"></a>Pages Razor

La fonctionnalité Pages Razor est activée dans *Startup.cs* :

[!code-cs[main](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Considérez une page de base : <a name="OnGet"></a>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Le code précédent ressemble beaucoup à un fichier vue Razor. Ce qui le rend différent est la directive `@page`. `@page` fait du fichier une action MVC, ce qui signifie qu’il gère les demandes directement, sans passer par un contrôleur. `@page` doit être la première directive Razor sur une page. `@page` affecte le comportement d’autres constructions Razor.

Une page similaire, utilisant une classe `PageModel`, est illustrée dans les deux fichiers suivants. Le fichier *Pages/Index2.cshtml* :

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Le fichier « code-behind » *Pages/Index2.cshtml.cs* :

[!code-cs[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Par convention, le fichier de classe `PageModel` a le même nom que le fichier Page Razor, avec *.cs* en plus. Par exemple, la page Razor précédente est *Pages/Index2.cshtml*. Le fichier contenant la classe `PageModel` se nomme *Pages/Index2.cshtml.cs*.

Les associations des chemins d’URL aux pages sont déterminées par l’emplacement de la page dans le système de fichiers. Le tableau suivant montre un chemin Page Razor et l’URL correspondante :

| Nom et chemin de fichier               | URL correspondante |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` ou `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` ou `/Store/Index` |

Remarques :

* Le runtime recherche les fichiers Pages Razor dans le dossier *Pages* par défaut.
* `Index` est la page par défaut quand une URL n’inclut pas de page.

## <a name="writing-a-basic-form"></a>Écriture d’un formulaire de base

Les fonctionnalités des pages Razor sont conçues pour simplifier les modèles courants utilisés avec les navigateurs web. La [liaison de modèle](xref:mvc/models/model-binding), les [Tag Helpers](xref:mvc/views/tag-helpers/intro)et les assistances HTML *fonctionnent tous* avec les propriétés définies dans une classe Page Razor. Considérez une page qui implémente un formulaire « Nous contacter » de base pour le modèle `Contact` :

Pour les exemples de ce document, le `DbContext` est initialisé dans le fichier [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).

[!code-cs[main](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Le modèle de données :

[!code-cs[main](index/sample/RazorPagesContacts/Data/Customer.cs)]

Le contexte de la base de données :

[!code-cs[main](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Le fichier vue *Pages/Create.cshtml* :

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Le fichier code-behind *Pages/Create.cshtml.cs* de la vue :

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Par convention, la classe `PageModel` se nomme `<PageName>Model` et se trouve dans le même espace de noms que la page.

La classe `PageModel` permet de séparer la logique d’une page de sa présentation. Elle définit des gestionnaires de page pour les demandes envoyées à la page et les données utilisées pour l’afficher. Cette séparation permet de gérer les dépendances entre les pages au moyen de [l’injection de dépendances](xref:fundamentals/dependency-injection) et d’effectuer des [tests unitaires](xref:testing/razor-pages-testing) sur les pages.

La page a une *méthode de gestionnaire* `OnPostAsync`, qui s’exécute sur les requêtes `POST` (quand un utilisateur poste le formulaire). Vous pouvez ajouter des méthodes de gestionnaire pour n’importe quel verbe HTTP. Les gestionnaires les plus courants sont :

* `OnGet` pour initialiser l’état nécessaire pour la page. Exemple [OnGet](#OnGet).
* `OnPost` pour gérer les envois de formulaire.

Le suffixe de nommage `Async` est facultatif, mais souvent utilisé par convention pour les fonctions asynchrones. Le code `OnPostAsync` dans l’exemple précédent est similaire à celui que vous écririez normalement dans un contrôleur. Le code précédent est typique des pages Razor. La plupart des primitives MVC comme la [liaison de modèle](xref:mvc/models/model-binding), la [validation](xref:mvc/models/validation) et les résultats d’action sont partagées.  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

La méthode `OnPostAsync` précédente :

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Le flux de base de `OnPostAsync` :

Recherchez les erreurs de validation.

*  S’il n’y a aucune erreur, enregistrez les données et redirigez.
*  S’il y a des erreurs, réaffichez la page avec des messages de validation. La validation côté client est identique à celle des applications ASP.NET Core MVC traditionnelles. Dans de nombreux cas, les erreurs de validation sont détectées sur le client et jamais envoyées au serveur.

Quand les données sont entrées correctement, la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `RedirectToPage` pour retourner une instance de `RedirectToPageResult`. `RedirectToPage` est un nouveau résultat d’action, semblable à `RedirectToAction` ou `RedirectToRoute`, mais personnalisé pour les pages. Dans l’exemple précédent, il redirige vers la page Index racine (`/Index`). `RedirectToPage` est détaillé dans la section [Génération d’URL pour les pages](#url_gen).

Quand le formulaire envoyé comporte des erreurs de validation (qui sont passées au serveur), la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `Page`. `Page` retourne une instance de `PageResult`. Le retour de `Page` est similaire à la façon dont les actions dans les contrôleurs retournent `View`. `PageResult` est le type de retour <!-- Review  --> par défaut pour une méthode de gestionnaire. Une méthode de gestionnaire qui retourne `void` restitue la page.

La propriété `Customer` utilise l’attribut `[BindProperty]` pour accepter la liaison de modèle.

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Par défaut, les pages Razor lient les propriétés uniquement avec les verbes non-GET. La liaison aux propriétés peut réduire la quantité de code à écrire. Elle réduit la quantité de code en utilisant la même propriété pour afficher les champs de formulaire (`<input asp-for="Customer.Name" />`) et accepter l’entrée.

La page d’accueil (*Index.cshtml*) :

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Le fichier code-behind *Index.cshtml.cs* :

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Le fichier *Index.cshtml* contient le balisage suivant pour créer un lien d’édition pour chaque contact :

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

Le [tag helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) a utilisé l’attribut `asp-route-{value}` pour générer un lien vers la page Edit. Le lien contient des données d’itinéraire avec l’ID de contact. Par exemple, `http://localhost:5000/Edit/1`.

Le fichier *Pages/Edit.cshtml* :

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

La première ligne contient la directive `@page "{id:int}"`. La contrainte de routage `"{id:int}"` indique à la page qu’elle doit accepter les requêtes qui contiennent des données d’itinéraire `int`. Si une requête à la page ne contient de données d’itinéraire qui peuvent être converties en `int`, le runtime retourne une erreur HTTP 404 (introuvable).

Le fichier *Pages/Edit.cshtml.cs* :

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Le fichier *Index.cshtml* contient également le balisage pour créer un bouton Supprimer pour chaque contact client :

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Lorsque le bouton Supprimer est rendu en HTML, son `formaction` comprend des paramètres pour :

* L’ID du contact client spécifié par l’attribut `asp-route-id`.
* Le `handler` spécifié par l’attribut `asp-page-handler`.

Voici un exemple de bouton Supprimer rendu avec un ID de contact client de `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Quand le bouton est sélectionné, une demande `POST` de forumaire est envoyée au serveur. Par convention, le nom de la méthode de gestionnaire est sélectionné en fonction de la valeur du paramètre `handler` conformément au schéma `OnPost[handler]Async`.

Étant donné que le `handler` est `delete` dans cet exemple, la méthode de gestionnaire `OnPostDeleteAsync` est utilisée pour traiter la demande `POST`. Si `asp-page-handler` est défini avec une autre valeur, telle que `remove`, une méthode de gestionnaire de page avec le nom `OnPostRemoveAsync` est sélectionnée.

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

La méthode `OnPostDeleteAsync` :

* Accepte l’`id` de la chaîne de requête.
* Interroge la base de données pour le contact client avec `FindAsync`.
* Si le contact client est trouvé, il est supprimé de la liste des contacts client. La base de données est mise à jour.
* Appelle `RedirectToPage` pour rediriger vers la page Index racine (`/Index`).

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF et pages Razor

Vous n’avez aucun code à écrire pour la [validation anti-contrefaçon](xref:security/anti-request-forgery). La validation et la génération de jetons anti-contrefaçon sont automatiquement incluses dans les pages Razor.

<a name="layout"></a>
## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Utilisation de dispositions, partiels, modèles et Tag Helpers avec les pages Razor

Les pages Razor fonctionnent avec toutes les fonctionnalités du moteur de vue Razor. Les dispositions, partiels, modèles, Tag Helpers, *_ViewStart.cshtml* et *_ViewImports.cshtml* fonctionnent de la même façon que pour les vues Razor classiques.

Nous allons nettoyer un peu cette page en tirant parti de certaines de ces fonctionnalités.

Ajoutez une [page de disposition](xref:mvc/views/layout) à *Pages/_Layout.cshtml* :

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

La [disposition](xref:mvc/views/layout) :

* Contrôle la disposition de chaque page (à moins que la page ne refuse la disposition).
* Importe des structures HTML telles que JavaScript et les feuilles de style.

Pour plus d’informations, consultez [Page de disposition](xref:mvc/views/layout).

La propriété [Layout](xref:mvc/views/layout#specifying-a-layout) est définie dans *Pages/_ViewStart.cshtml* :

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

**Remarque :** La disposition est dans le dossier *Pages*. Les pages recherchent d’autres vues (dispositions, modèles, partiels) hiérarchiquement, en commençant dans le même dossier que la page active. Une disposition dans le dossier *Pages* peut être utilisée à partir de n’importe quelle page Razor sous le dossier *Pages*.

Nous vous recommandons de **ne pas** placer le fichier de disposition dans le dossier *Views/Shared*. *Views/Shared* est un modèle de vues MVC. Les pages Razor sont censées s’appuyer sur la hiérarchie des dossiers, pas sur les conventions de chemins.

La recherche de vue à partir d’une page Razor inclut le dossier *Pages*. Les dispositions, modèles et partiels que vous utilisez avec les contrôleurs MVC et les vues Razor conventionnelles *fonctionnent simplement*.

Ajoutez un fichier *Pages/_ViewImports.cshtml* :

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` est expliqué plus loin dans le didacticiel. La directive `@addTagHelper` permet de bénéficier des [Tag Helpers intégrés](xref:mvc/views/tag-helpers/builtin-th/Index) dans toutes les pages du dossier *Pages*.

<a name="namespace"></a>

Quand la directive `@namespace` est utilisée explicitement sur une page :

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

La directive définit l’espace de noms pour la page. La directive `@model` n’a pas besoin d’inclure l’espace de noms.

Quand la directive `@namespace` est contenue dans *_ViewImports.cshtml*, l’espace de noms spécifié fournit le préfixe de l’espace de noms généré dans la Page qui importe la directive `@namespace`. Le reste de l’espace de noms généré (la partie suffixe) est le chemin relatif séparé par un point entre le dossier contenant *_ViewImports.cshtml* et le dossier contenant la page.

Par exemple, le fichier code-behind *Pages/Customers/Edit.cshtml.cs* définit explicitement l’espace de noms :

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Le fichier *Pages/_ViewImports.cshtml* définit l’espace de noms suivant :

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

L’espace de noms généré pour la page Razor *Pages/Customers/Edit.cshtml* est identique au fichier code-behind. La directive `@namespace` a été conçue pour que les classes C# ajoutées à un projet et au code généré par les pages *fonctionnent simplement* sans avoir à ajouter de directive `@using` pour le fichier code-behind.

**Remarque :** `@namespace` fonctionne également avec les vues Razor classiques.

Le fichier de vue *Pages/Create.cshtml* d’origine :

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Le fichier vue *Pages/Create.cshtml* mis à jour :

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

Le [projet de démarrage de pages Razor](#rpvs17) contient *Pages/_ValidationScriptsPartial.cshtml*, qui connecte la validation côté client.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Génération d’URL pour les pages

La page `Create`, illustrée précédemment, utilise `RedirectToPage` :

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

L’application a la structure de fichiers/dossiers suivante :

* */Pages*

  * *Index.cshtml*
  * */Customer*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Une fois l’opération réussie, les pages *Pages/Customers/Create.cshtml* et *Pages/Customers/Edit.cshtml* redirigent vers *Pages/Index.cshtml*. La chaîne `/Index` fait partie de l’URI pour accéder à la page précédente. La chaîne `/Index` peut être utilisée pour générer l’URI de la page *Pages/Index.cshtml*. Exemple :

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Le nom de la page est le chemin de la page à partir du dossier racine */Pages* (y compris un `/` de début, par exemple `/Index`). Les exemples de génération d’URL précédents sont beaucoup plus riches en fonctionnalités que le simple codage en dur d’une URL. La génération d’URL utilise le [routage](xref:mvc/controllers/routing) et peut générer et encoder des paramètres en fonction de la façon dont l’itinéraire est défini dans le chemin de destination.

La génération d’URL pour les pages prend en charge les noms relatifs. Le tableau suivant montre la page Index sélectionnée avec différents paramètres `RedirectToPage` à partir de *Pages/Customers/Create.cshtml* :

| RedirectToPage(x)| Page |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` et `RedirectToPage("../Index")` sont des *noms relatifs*. Le paramètre `RedirectToPage` est *combiné* avec le chemin de la page active pour calculer le nom de la page de destination.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page. -- page name, not page path -->

La liaison de nom relatif est utile lors de la création de sites avec une structure complexe. Si vous utilisez des noms relatifs pour établir une liaison entre les pages d’un dossier, vous pouvez renommer ce dossier. Tous les liens fonctionneront encore (car ils n’incluent pas le nom du dossier).

## <a name="tempdata"></a>TempData

ASP.NET Core expose la propriété [TempData](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) sur un [contrôleur](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller). Cette propriété stocke les données jusqu’à ce qu’elles soient lues. Vous pouvez utiliser les méthodes `Keep` et `Peek` pour examiner les données sans suppression. `TempData` est utile pour la redirection, quand des données sont nécessaires pour plusieurs requêtes.

L’attribut `[TempData]` est une nouveauté dans ASP.NET Core 2.0. Il est pris en charge sur les contrôleurs et les pages.

Le code suivant définit la valeur de `Message` à l’aide de `TempData` :

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Le balisage suivant dans le fichier *Pages/Customers/Index.cshtml* affiche la valeur de `Message` à l’aide de `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Le fichier code-behind *Pages/Customers/Index.cshtml.cs* applique l’attribut `[TempData]` à la propriété `Message`.

```cs
[TempData]
public string Message { get; set; }
```

Pour plus d’informations, consultez [TempData](xref:fundamentals/app-state#temp).

<a name="mhpp"></a>
## <a name="multiple-handlers-per-page"></a>Plusieurs gestionnaires par page

La page suivante génère un balisage pour deux gestionnaires de page à l’aide du Tag Helper `asp-page-handler` :

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Le formulaire dans l’exemple précédent contient deux boutons d’envoi, chacun utilisant `FormActionTagHelper` pour envoyer à une URL différente. L’attribut `asp-page-handler` est un complément de `asp-page`. `asp-page-handler` génère des URL qui envoient à chacune des méthodes de gestionnaire définies par une page. `asp-page` n’est pas spécifié, car l’exemple établit une liaison à la page active.

Le fichier code-behind :

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Le code précédent utilise des *méthodes de gestionnaire nommées*. Pour créer des méthodes de gestionnaire nommées, il faut prendre le texte dans le nom après `On<HTTP Verb>` et avant `Async` (le cas échéant). Dans l’exemple précédent, les méthodes de page sont OnPost**JoinList**Async et OnPost**JoinListUC**Async. Avec *OnPost* et *Async* supprimés, les noms des gestionnaires sont `JoinList` et `JoinListUC`.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `http://localhost:5000/Customers/CreateFATH?handler=JoinList`. Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.

## <a name="customizing-routing"></a>Personnalisation du routage

Si vous ne voulez pas avoir la chaîne de requête `?handler=JoinList` dans l’URL, vous pouvez changer l’itinéraire pour placer le nom du gestionnaire dans la partie chemin de l’URL. Vous pouvez personnaliser l’itinéraire en ajoutant un modèle d’itinéraire placé entre des guillemets doubles après la directive `@page`.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

L’itinéraire précédent place le nom du gestionnaire dans le chemin d’URL au lieu de la chaîne de requête. Le `?` suivant `handler` signifie que le paramètre d’itinéraire est facultatif.

Vous pouvez utiliser `@page` pour ajouter des segments et des paramètres supplémentaires à l’itinéraire d’une page. Tout ce qui y figure est **ajouté** à l’itinéraire par défaut de la page. L’utilisation d’un chemin absolu ou virtuel pour changer l’itinéraire de la page (comme `"~/Some/Other/Path"`) n’est pas prise en charge.

## <a name="configuration-and-settings"></a>Configuration et paramètres

Pour configurer les options avancées, utilisez la méthode d’extension `AddRazorPagesOptions` sur le générateur MVC :

[!code-cs[main](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Actuellement, vous pouvez utiliser `RazorPagesOptions` pour définir le répertoire racine pour les pages, ou ajouter des conventions de modèle d’application pour les pages. Nous permettrons à l’avenir une plus grande extensibilité en ce sens.

Pour précompiler des vues, consultez [Compilation de vue Razor](xref:mvc/views/view-compilation).

[Téléchargez ou affichez des exemples de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).

Consultez [Bien démarrer avec les pages Razor dans ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start), qui s’appuie sur cette introduction.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Spécifier que les pages Razor se trouvent à la racine du contenu

Par défaut, les pages Razor sont associées à la racine */Pages*. Ajoutez [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) à [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) pour spécifier que vos pages Razor se trouvent à la racine de contenu ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) de l’application :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Spécifier que les pages Razor se trouvent dans un répertoire racine personnalisé

Ajoutez [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) à [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) pour spécifier que vos pages Razor se trouvent dans le répertoire racine personnalisé de l’application (fournissez un chemin relatif) :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="see-also"></a>Voir aussi

* [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Conventions d’autorisation des pages Razor](xref:security/authorization/razor-pages-authorization)
* [Itinéraire personnalisé des pages Razor et fournisseurs de modèle de page](xref:mvc/razor-pages/razor-pages-convention-features)
* [Test unitaire et d’intégration des pages Razor](xref:testing/razor-pages-testing)

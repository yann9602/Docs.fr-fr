---
title: "Razor Pages itinéraire et application convention fonctionnalités dans ASP.NET Core"
author: guardrex
description: "Découvrez comment itinéraire et application des fonctionnalités de convention modèle fournisseur vous aident routage de page de contrôle, de découverte et de traitement."
ms.author: riande
manager: wpickett
ms.date: 10/23/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/razor-pages/razor-pages-convention-features
ms.openlocfilehash: 69475ca9abd4e732dc704ad6a8a2fffe219984f7
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="razor-pages-route-and-app-convention-features-in-aspnet-core"></a>Razor Pages itinéraire et application convention fonctionnalités dans ASP.NET Core

Par [Luke Latham](https://github.com/guardrex)

Apprenez à utiliser la page routage et l’application fournisseur convention des fonctionnalités de modèle pour contrôler le routage de la page, de découverte et de traitement dans les applications de Pages Razor. Lorsque vous devez configurer des itinéraires de page personnalisée pour des pages individuelles, configurer le routage vers les pages avec la [AddPageRoute convention](#configure-a-page-route) décrite plus loin dans cette rubrique.

Utilisez le [exemple d’application](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/razor-pages-convention-features/sample) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample)) pour Explorer les fonctionnalités décrites dans cette rubrique.

| Fonctionnalités | L’exemple montre comment... |
| -------- | --------------------------- |
| [Conventions de modèle itinéraire et application](#add-route-and-app-model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li></ul> | Ajout d’un modèle d’itinéraire et un en-tête pour les pages d’une application. |
| [Conventions d’action de routage de page](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Ajout d’un modèle d’itinéraire vers les pages dans un dossier et à une seule page. |
| [Conventions d’action de modèle de page](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe de filtre, une expression lambda ou la fabrique de filtre)</li></ul> | Ajout d’un en-tête pour les pages dans un dossier, ajoutant un en-tête à une seule page et en configurant un [fabrique de filtre](xref:mvc/controllers/filters#ifilterfactory) pour ajouter un en-tête pour les pages d’une application. |
| [Fournisseur de modèle par défaut page application](#replace-the-default-page-app-model-provider) | En remplaçant le fournisseur de modèle de page par défaut pour modifier les conventions d’affectation de noms de gestionnaire. |

## <a name="add-route-and-app-model-conventions"></a>Ajouter l’itinéraire et application des conventions de modèle

Ajouter un délégué pour [IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention) pour ajouter l’itinéraire et application conventions de modèle qui s’appliquent aux Pages Razor.

**Ajouter une convention de modèle d’itinéraire à toutes les pages**

Utilisez [Conventions](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.razorpagesoptions.conventions) pour créer et ajouter un [IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention) à la collection de [IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention) instances qui sont appliqués au cours de modèle d’itinéraire et de page construction.

L’exemple d’application ajoute un `{globalTemplate?}` modèle d’itinéraire à toutes les pages dans l’application :

[!code-csharp[Main](razor-pages-convention-features/sample/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

> [!NOTE]
> Le `Order` propriété pour le `AttributeRouteModel` a la valeur `0` (zéro). Cela garantit que ce modèle a la priorité pour la première position de valeur de données itinéraire lorsqu’une valeur du seul itinéraire est fournie. Par exemple, l’exemple ajoute un `{aboutTemplate?}` modèle d’itinéraire ultérieurement dans cette rubrique. Le `{aboutTemplate?}` modèle est donné un `Order` de `1`. Lorsque la page à propos est demandée à `/About/RouteDataValue`, « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` (`Order = 0`) et non pas `RouteData.Values["aboutTemplate"]` (`Order = 1`) en raison du paramètre le `Order` propriété.

*Startup.cs*:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet1)]

Demander à cette page de l’exemple à `localhost:5000/About/GlobalRouteValue` et inspecter le résultat :

![La page à propos est demandée sur un segment de l’itinéraire du GlobalRouteValue. La page restituée montre que la valeur de données d’itinéraire est capturée dans la méthode OnGet de la page.](razor-pages-convention-features/_static/about-page-global-template.png)

**Ajouter une convention de modèle d’application pour toutes les pages**

Utilisez [Conventions](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.razorpagesoptions.conventions) pour créer et ajouter un [IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention) à la collection de [IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention) instances qui sont appliqués au cours de route et de page construction du modèle.

Pour illustrer cela et autres conventions ultérieurement dans cette rubrique, l’exemple d’application inclut un `AddHeaderAttribute` classe. Le constructeur de classe accepte un `name` chaîne et un `values` tableau de chaînes. Ces valeurs sont utilisées dans son `OnResultExecuting` pour définir un en-tête de réponse. La classe complète est indiquée dans le [Page conventions d’action de modèle](#page-model-action-conventions) section ultérieurement dans cette rubrique.

L’application d’exemple utilise le `AddHeaderAttribute` classe pour ajouter un en-tête, `GlobalHeader`, pour toutes les pages dans l’application :

[!code-csharp[Main](razor-pages-convention-features/sample/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet2)]

Demander à cette page de l’exemple à `localhost:5000/About` et inspecter les en-têtes pour afficher le résultat :

![En-têtes de réponse de la page à propos montrent que le GlobalHeader a été ajouté.](razor-pages-convention-features/_static/about-page-global-header.png)

## <a name="page-route-action-conventions"></a>Conventions d’action de routage de page

Le fournisseur de modèle d’itinéraire par défaut qui dérive de [IPageRouteModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelprovider) appelle les conventions qui sont conçues pour fournir des points d’extensibilité pour la configuration des itinéraires de la page.

**Convention de modèle d’itinéraire dossier**

Utilisez [AddFolderRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addfolderroutemodelconvention) pour créer et ajouter un [IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention) qui appelle une action sur le [PageRouteModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageroutemodel) pour toutes les pages sous le dossier spécifié.

L’application exemple utilise `AddFolderRouteModelConvention` pour ajouter un `{otherPagesTemplate?}` modèle d’itinéraire à des pages dans le *AutresPages* dossier :

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet3)]

> [!NOTE]
> Le `Order` propriété pour le `AttributeRouteModel` a la valeur `1`. Cela garantit que le modèle pour `{globalTemplate?}` (définie plus haut dans la rubrique) a la priorité pour les données d’itinéraire première valeur position lorsqu’une valeur du seul itinéraire est fournie. Si la page de la page 1 est demandée à `/OtherPages/Page1/RouteDataValue`, « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` (`Order = 0`) et non pas `RouteData.Values["otherPagesTemplate"]` (`Order = 1`) en raison du paramètre le `Order` propriété.

Demande de page de page 1 de l’exemple à `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` et inspecter le résultat :

![Page 1 dans le dossier AutresPages est demandé sur un segment de l’itinéraire du GlobalRouteValue et OtherPagesRouteValue. La page restituée montre que les valeurs de données d’itinéraire sont capturés dans la méthode OnGet de la page.](razor-pages-convention-features/_static/otherpages-page1-global-and-otherpages-templates.png)

**Convention de modèle d’itinéraire page**

Utilisez [AddPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addpageroutemodelconvention) pour créer et ajouter un [IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention) qui appelle une action sur le [PageRouteModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageroutemodel) pour la page avec le texte spécifié nom.

L’application exemple utilise `AddPageRouteModelConvention` pour ajouter un `{aboutTemplate?}` modèle d’itinéraire à la page à propos de :

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet4)]

> [!NOTE]
> Le `Order` propriété pour le `AttributeRouteModel` a la valeur `1`. Cela garantit que le modèle pour `{globalTemplate?}` (définie plus haut dans la rubrique) a la priorité pour les données d’itinéraire première valeur position lorsqu’une valeur du seul itinéraire est fournie. Si la page à propos est demandée à `/About/RouteDataValue`, « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` (`Order = 0`) et non pas `RouteData.Values["aboutTemplate"]` (`Order = 1`) en raison du paramètre le `Order` propriété.

Demander à cette page de l’exemple à `localhost:5000/About/GlobalRouteValue/AboutRouteValue` et inspecter le résultat :

![Sur la page est demandée avec les segments de l’itinéraire pour GlobalRouteValue et AboutRouteValue. La page restituée montre que les valeurs de données d’itinéraire sont capturés dans la méthode OnGet de la page.](razor-pages-convention-features/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Configurer un itinéraire de page

Utilisez [AddPageRoute](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.addpageroute) pour configurer un itinéraire vers une page à l’emplacement de la page spécifiée. Liens générés à la page utilisent votre itinéraire spécifié. `AddPageRoute`utilise `AddPageRouteModelConvention` pour établir l’itinéraire.

L’exemple d’application crée un itinéraire vers `/TheContactPage` pour *Contact.cshtml*:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet5)]

La page de Contact peut également être atteint à `/Contact` via l’itinéraire par défaut.

Itinéraire personnalisé de l’application d’exemple à la page permettant facultatif `text` segment d’itinéraire (`{text?}`). Cette page inclut également ce segment facultatif dans son `@page` directive au cas où le visiteur accède à la page à son `/Contact` itinéraire :

[!code-cshtml[Main](razor-pages-convention-features/sample/Pages/Contact.cshtml?highlight=1)]

Notez que l’URL générée pour le **Contact** lien dans la page rendue reflète l’itinéraire de mise à jour :

![Exemple de lien de Contact de l’application dans la barre de navigation](razor-pages-convention-features/_static/contact-link.png)

![En inspectant le lien de Contact dans le code HTML restitué indique que le href est défini sur « / TheContactPage »](razor-pages-convention-features/_static/contact-link-source.png)

Visitez la page de Contact soit son itinéraire ordinaire, `/Contact`, ou l’itinéraire personnalisé, `/TheContactPage`. Si vous fournissez un autre `text` segment d’itinéraire, la page affiche le segment codée en HTML, vous devez fournir :

![Exemple de navigateur Edge pour fournir un segment de routage facultatif 'text' de 'Texte' dans l’URL. La page restituée montre la valeur du segment 'text'.](razor-pages-convention-features/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Conventions d’action de modèle de page

Le fournisseur de modèle de page par défaut qui implémente [IPageApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelprovider) appelle les conventions qui sont conçues pour fournir des points d’extensibilité pour configurer les modèles de page. Ces conventions sont utiles lors de la création et la modification de détection de page et les fonctionnalités de traitement.

Pour les exemples de cette section, l’exemple d’application utilise un `AddHeaderAttribute` (classe), qui est un [ResultFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.resultfilterattribute), qui s’applique à un en-tête de réponse :

[!code-csharp[Main](razor-pages-convention-features/sample/Filters/AddHeader.cs?name=snippet1)]

À l’aide de conventions, l’exemple montre comment appliquer l’attribut à toutes les pages dans un dossier et à une seule page.

**Convention de modèle d’application dossier**

Utilisez [AddFolderApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addfolderapplicationmodelconvention) pour créer et ajouter un [IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention) qui appelle une action sur [PageApplicationModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageapplicationmodel) pour les instances toutes les pages dans le dossier spécifié.

L’exemple illustre l’utilisation de `AddFolderApplicationModelConvention` en ajoutant un en-tête, `OtherPagesHeader`, pour les pages à l’intérieur de la *AutresPages* dossier de l’application :

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet6)]

Demande de page de page 1 de l’exemple à `localhost:5000/OtherPages/Page1` et inspecter les en-têtes pour afficher le résultat :

![En-têtes de réponse de la page AutresPages/page 1 indiquent que la OtherPagesHeader a été ajouté.](razor-pages-convention-features/_static/page1-otherpages-header.png)

**Convention de modèle page application**

Utilisez [AddPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addpageapplicationmodelconvention) pour créer et ajouter un [IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention) qui appelle une action sur le [PageApplicationModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageapplicationmodel) pour la page avec le nom speciifed.

L’exemple illustre l’utilisation de `AddPageApplicationModelConvention` en ajoutant un en-tête, `AboutHeader`, à la page à propos de :

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet7)]

Demander à cette page de l’exemple à `localhost:5000/About` et inspecter les en-têtes pour afficher le résultat :

![En-têtes de réponse de la page à propos montrent que le AboutHeader a été ajouté.](razor-pages-convention-features/_static/about-page-about-header.png)

**Configurer un filtre**

[ConfigureFilter](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.configurefilter) configure appliquer le filtre spécifié. Vous pouvez implémenter une classe de filtre, mais l’exemple d’application montre comment implémenter un filtre dans une expression lambda, qui est implémentée en arrière-plan en tant que fabrique qui retourne un filtre :

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet8)]

Le modèle d’application de page est utilisé pour vérifier le chemin d’accès relatif pour les segments qui mènent à la page 2 dans la *AutresPages* dossier. Si la condition réussit, un en-tête est ajouté. Si ce n’est pas le cas, le `EmptyFilter` est appliqué.

`EmptyFilter`est un [filtre d’Action](xref:mvc/controllers/filters#action-filters). Étant donné que les filtres d’Action sont ignorés par les Pages Razor, le `EmptyFilter` non-ops comme prévu si le chemin d’accès ne contient pas `OtherPages/Page2`.

Demande de page 2 de l’exemple à `localhost:5000/OtherPages/Page2` et inspecter les en-têtes pour afficher le résultat :

![Le OtherPagesPage2Header est ajouté à la réponse pour la page 2.](razor-pages-convention-features/_static/page2-filter-header.png)

**Configurer une fabrique de filtre**

[ConfigureFilter](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.configurefilter?view=aspnetcore-2.0#Microsoft_Extensions_DependencyInjection_PageConventionCollectionExtensions_ConfigureFilter_Microsoft_AspNetCore_Mvc_ApplicationModels_PageConventionCollection_System_Func_Microsoft_AspNetCore_Mvc_ApplicationModels_PageApplicationModel_Microsoft_AspNetCore_Mvc_Filters_IFilterMetadata__) configure la fabrique spécifiée pour appliquer [filtres](xref:mvc/controllers/filters) à toutes les Pages Razor.

L’exemple d’application fournit un exemple d’utilisation une [fabrique de filtre](xref:mvc/controllers/filters#ifilterfactory) en ajoutant un en-tête, `FilterFactoryHeader`, avec deux valeurs vers les pages de l’application :

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[Main](razor-pages-convention-features/sample/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Demander à cette page de l’exemple à `localhost:5000/About` et inspecter les en-têtes pour afficher le résultat :

![En-têtes de réponse de la page à propos montrent que les deux en-têtes FilterFactoryHeader ont été ajoutés.](razor-pages-convention-features/_static/about-page-filter-factory-header.png)

## <a name="replace-the-default-page-app-model-provider"></a>Remplacez le fournisseur de modèle par défaut page application

Pages Razor utilise le `IPageApplicationModelProvider` interface permettant de créer un [DefaultPageApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.internal.defaultpageapplicationmodelprovider). Vous pouvez hériter du fournisseur de modèle par défaut pour fournir votre propre logique d’implémentation pour le traitement et la découverte du gestionnaire. L’implémentation par défaut ([source de référence](https://github.com/aspnet/Mvc/blob/rel/2.0.1/src/Microsoft.AspNetCore.Mvc.RazorPages/Internal/DefaultPageApplicationModelProvider.cs)) établit des conventions pour *sans nom* et *nommé* Gestionnaire d’attribution de noms, ce qui est décrit ci-dessous.

**Les méthodes de gestionnaire sans nom par défaut**

Méthodes de gestionnaire pour les verbes HTTP (les méthodes du gestionnaire « sans nom ») suivent une convention : `On<HTTP verb>[Async]` (ajout `Async` est facultative mais recommandée pour les méthodes async).

| Méthode de gestionnaire sans nom     | Opération                      |
| -------------------------- | ------------------------------ |
| `OnGet`/`OnGetAsync`       | Initialiser l’état de la page.     |
| `OnPost`/`OnPostAsync`     | Gérer les demandes POST.          |
| `OnDelete`/`OnDeleteAsync` | Gérer les demandes de suppression &#8224;. |
| `OnPut`/`OnPutAsync`       | Gérer les demandes PUT &#8224;.    |
| `OnPatch`/`OnPatchAsync`   | Gérer les demandes de correctif logiciel &#8224;.  |

&#8224; Utilisé pour effectuer des appels d’API à la page.

**Les méthodes du Gestionnaire de nommé par défaut**

Les méthodes du gestionnaire fournies par le développeur ( » nommé « méthodes de gestionnaire) suivent une convention similaire. Le nom du gestionnaire apparaît après le verbe HTTP, ou entre le verbe HTTP et `Async`: `On<HTTP verb><handler name>[Async]` (ajout `Async` est facultative mais recommandée pour les méthodes async). Par exemple, les méthodes qui traitent les messages peuvent prendre l’attribution de noms indiqué dans le tableau ci-dessous.

| Exemple appelée méthode de gestionnaire             | Opération de cet exemple        |
| ---------------------------------------- | ------------------------ |
| `OnGetMessage`/`OnGetMessageAsync`       | Obtenir un message.        |
| `OnPostMessage`/`OnPostMessageAsync`     | PUBLIER un message.          |
| `OnDeleteMessage`/`OnDeleteMessageAsync` | SUPPRIMER un message &#8224;. |
| `OnPutMessage`/`OnPutMessageAsync`       | PLACER un message &#8224;.    |
| `OnPatchMessage`/`OnPatchMessageAsync`   | CORRECTIF d’un message &#8224;.  |

&#8224; Utilisé pour effectuer des appels d’API à la page.

**Personnaliser les noms de méthode de gestionnaire**

Supposons que vous souhaitez modifier la manière dont les méthodes du gestionnaire nommés et sans nom sont nommés. Un autre schéma d’affectation de noms consiste à ne pas démarrer les noms de méthode avec « On » et le premier segment de word pour déterminer le verbe HTTP. Vous pouvez apporter d’autres modifications, telles que la conversion des verbes pour la suppression, placer et appliquer le correctif à valider. Un tel système fournit les noms de méthode indiqués dans le tableau suivant.

| Méthode de gestionnaire                       | Opération                      |
| ------------------------------------ | ------------------------------ |
| `Get`                                | Initialiser l’état de la page.     |
| `Post`/`PostAsync`                   | Gérer les demandes POST.          |
| `Delete`/`DeleteAsync`               | Gérer les demandes de suppression &#8224;. |
| `Put`/`PutAsync`                     | Gérer les demandes PUT &#8224;.    |
| `Patch`/`PatchAsync`                 | Gérer les demandes de correctif logiciel &#8224;.  |
| `GetMessage`                         | Obtenir un message.              |
| `PostMessage`/`PostMessageAsync`     | PUBLIER un message.                |
| `DeleteMessage`/`DeleteMessageAsync` | ENVOYER un message à supprimer.      |
| `PutMessage`/`PutMessageAsync`       | PUBLIER un message à insérer.         |
| `PatchMessage`/`PatchMessageAsync`   | PUBLIER un message pour le correctif.       |

&#8224; Utilisé pour effectuer des appels d’API à la page.

Pour établir ce schéma, héritent de la `DefaultPageApplicationModelProvider` classe et substituer les [CreateHandlerModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.internal.defaultpageapplicationmodelprovider.createhandlermodel) méthode pour fournir une logique personnalisée pour la résolution [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) les noms de gestionnaires. L’exemple d’application vous montre comment procéder son `CustomPageApplicationModelProvider` classe :

[!code-csharp[Main](razor-pages-convention-features/sample/CustomPageApplicationModelProvider.cs?name=snippet1&highlight=1-2,45-46,64-68,78-85,87,92,106)]

Met en surbrillance de la classe est les suivantes :

* Hérite de la classe `DefaultPageApplicationModelProvider`.
* Le `TryParseHandlerMethod` traite un gestionnaire pour déterminer le verbe HTTP (`httpMethod`) et le nom de gestionnaire nommé (`handlerName`) lors de la création du `PageHandlerModel`.
  * Un `Async` postfix est ignorée, le cas échéant.
  * La casse est utilisée pour analyser le verbe HTTP à partir du nom de la méthode.
  * Lorsque le nom de méthode (sans `Async`) est égal à celui de verbe HTTP, il n’existe aucun gestionnaire nommé. Le `handlerName` a la valeur `null`, et le nom de la méthode est `Get`, `Post`, `Delete`, `Put`, ou `Patch`.
  * Lorsque le nom de méthode (sans `Async`) est plus longue que le nom du verbe HTTP, il existe un gestionnaire nommé. La propriété `handlerName` a la valeur `<method name (less 'Async', if present)>`. Par exemple, « GetMessage » et « GetMessageAsync » produisent un nom de gestionnaire de « GetMessage ».
  * Verbes HTTP de correctif logiciel, PUT et DELETE sont convertis en POST.

Inscrire le `CustomPageApplicationModelProvider` dans la `Startup` classe :

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet10)]

Le fichier code-behind *Index.cshtml.cs* illustre la manière dont les conventions de nom de méthode de gestionnaire ordinaires sont modifiées pour les pages de l’application. Particulière « Sur « préfixe d’affectation de noms utilisé avec les Pages Razor est supprimé. La méthode qui initialise l’état de la page est maintenant nommée `Get`. Vous pouvez voir cette convention utilisée dans toute l’application si vous ouvrez un fichier code-behind pour les pages.

Chacune des autres méthodes démarrer avec le verbe HTTP qui décrit son traitement. Les deux méthodes qui commencent par `Delete` serait normalement être traitées comme les verbes HTTP DELETE, mais la logique de `TryParseHandlerMethod` définit explicitement le verbe POST pour les deux gestionnaires.

Notez que `Async` est facultative entre `DeleteAllMessages` et `DeleteMessageAsync`. Ils sont les deux méthodes asynchrones, mais vous pouvez choisir d’utiliser le `Async` postfix ou non ; nous vous recommandons d’effectuer. `DeleteAllMessages`est utilisé ici à des fins de démonstration, mais nous vous conseillons de nommer une telle méthode `DeleteAllMessagesAsync`. Il n’affecte pas le traitement de l’exemple implémentation, mais à l’aide de la `Async` postfix appels externes du fait qu’il s’agit d’une méthode asynchrone.

[!code-csharp[Main](razor-pages-convention-features/sample/Pages/Index.cshtml.cs?name=snippet1&highlight=1,6,16,29)]

Notez les noms des gestionnaires fournis dans *Index.cshtml* correspond à la `DeleteAllMessages` et `DeleteMessageAsync` les méthodes du gestionnaire :

[!code-cshtml[Main](razor-pages-convention-features/sample/Pages/Index.cshtml?range=29-60&highlight=7-8,24-25)]

`Async`dans le nom de la méthode de gestionnaire `DeleteMessageAsync` est factorisée out par le `TryParseHandlerMethod` pour la correspondance de gestionnaire de requête POST pour la méthode. Le `asp-page-handler` nom de `DeleteMessage` mise en correspondance avec la méthode de gestionnaire `DeleteMessageAsync`.

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtres MVC et le filtre de Page (IPageFilter)

MVC [filtres d’Action](xref:mvc/controllers/filters#action-filters) sont ignorés par les Pages Razor, étant donné que les Pages Razor utilise les méthodes du gestionnaire. Autres types de filtres MVC sont disponibles, vous pouvez utiliser : [autorisation](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [ressource](xref:mvc/controllers/filters#resource-filters), et [résultat](xref:mvc/controllers/filters#result-filters). Pour plus d’informations, consultez la [filtres](xref:mvc/controllers/filters) rubrique.

Le filtre de Page ([IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter)) est un filtre qui s’applique aux Pages Razor. Il entoure l’exécution d’une méthode de gestionnaire de page. Il permet de traiter le code personnalisé à des étapes d’exécution de méthode de gestionnaire de page. Voici un exemple de l’exemple d’application :

[!code-csharp[Main](razor-pages-convention-features/sample/Filters/ReplaceRouteValueFilterAttribute.cs?name=snippet1)]

Ce filtre vérifie un `globalTemplate` acheminer la valeur de « TriggerValue » et les échanges dans « ReplacementValue ».

Le `ReplaceRouteValueFilter` attribut peut être appliqué directement à un `PageModel` dans le code-behind :

[!code-csharp[Main](razor-pages-convention-features/sample/Pages/OtherPages/Page3.cshtml.cs?range=10-12&highlight=1)]

Demander à l’exemple d’application avec à Page3 `localhost:5000/OtherPages/Page3/TriggerValue`. Notez la façon dont le filtre remplace la valeur de l’itinéraire :

![La demande d’AutresPages/Page3 avec un TriggerValue itinéraire segment entraîne le filtre en remplaçant la valeur de l’itinéraire par ReplacementValue.](razor-pages-convention-features/_static/otherpages-page3-filter-replacement-value.png)

## <a name="see-also"></a>Voir aussi

* [Conventions d’autorisation des pages Razor](xref:security/authorization/razor-pages-authorization)

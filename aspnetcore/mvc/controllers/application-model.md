---
title: "Utilisation du modèle d’Application"
author: ardalis
description: 
keywords: "ASP.NET MVC de base Core,ASP.NET, modèle d’application"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4eb7e52f-5665-41a4-a3e3-e348d07337f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/application-model
ms.openlocfilehash: 3c35184921dbe26cde100fd3d5124e38ea0d06cf
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="working-with-the-application-model"></a>Utilisation du modèle d’Application

Par [Steve Smith](https://ardalis.com/)

ASP.NET MVC de base définit un *modèle d’application* représentant les composants d’une application MVC. Vous pouvez lire et manipuler ce modèle pour modifier le comportement des éléments MVC. Par défaut, MVC suit certaines conventions pour déterminer les classes qui sont considérés comme étant des contrôleurs, les méthodes sur ces classes sont des actions et le comportement des paramètres et le routage. Vous pouvez personnaliser ce comportement en fonction des besoins de votre application en créant vos propres conventions et de les appliquer globalement ou en tant qu’attributs.

## <a name="models-and-providers"></a>Les modèles et les fournisseurs

Le modèle d’application ASP.NET MVC de base incluent les interfaces abstraites et les classes d’implémentation concrète qui décrivent une application MVC. Ce modèle est le résultat de MVC, découverte des contrôleurs, actions, paramètres d’action, itinéraires et filtres selon les conventions de la valeur par défaut de l’application. En travaillant avec le modèle d’application, vous pouvez modifier votre application pour suivre les conventions différentes du comportement de MVC par défaut. Les paramètres, les noms, les itinéraires et les filtres sont utilisés comme données de configuration pour les actions et les contrôleurs de.

Le modèle d’Application MVC ASP.NET Core a la structure suivante :

* ApplicationModel
    * Contrôleurs (ControllerModel)
        * Actions (ActionModel)
            * Paramètres (ParameterModel)

Chaque niveau du modèle a accès à un commun `Properties` collection et les niveaux inférieurs peuvent accéder et remplacer les valeurs de propriété définies par les niveaux supérieurs de la hiérarchie. Les propriétés sont rendues persistantes dans le `ActionDescriptor.Properties` lorsque les actions sont créées. Puis lorsqu’une demande est traitée, toutes les propriétés une convention ajoutée ou modifiée est accessible via `ActionContext.ActionDescriptor.Properties`. À l’aide de propriétés est un excellent moyen de configurer vos filtres et les classeurs de modèles, à la fois par action.

> [!NOTE]
> Le `ActionDescriptor.Properties` collection n’est pas thread-safe (pour les écritures) une fois que le démarrage de l’application a terminé. Les conventions sont la meilleure façon d’ajouter en toute sécurité des données à cette collection.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

Cœur d’ASP.NET MVC charge le modèle d’application à l’aide d’un modèle de fournisseur, défini par le [IApplicationModelProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) interface. Cette section aborde certains aspects de l’implémentation interne de la façon de fonctions de ce fournisseur. Il s’agit d’une rubrique avancée : la plupart des applications qui tirent parti du modèle d’application doit se faire en utilisant des conventions.

Les implémentations de la `IApplicationModelProvider` interface « encapsuler », avec chaque appel de l’implémentation `OnProvidersExecuting` dans l’ordre croissant selon son `Order` propriété. Le `OnProvidersExecuted` méthode est ensuite appelée dans l’ordre inverse. Le framework définit plusieurs fournisseurs :

Premier (`Order=-1000`) :

* [`DefaultApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

Puis (`Order=-990`) :

* [`AuthorizationApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> L’ordre dans les deux fournisseurs avec la même valeur pour `Order` sont appelés n’est pas défini et par conséquent pas être sur.

> [!NOTE]
> `IApplicationModelProvider`est un concept avancé pour les auteurs d’infrastructure à étendre. En général, les applications doivent utiliser les conventions et infrastructures utilisent des fournisseurs. La principale différence est que les fournisseurs s’exécutent toujours avant les conventions.

Le `DefaultApplicationModelProvider` établit la plupart des comportements par défaut utilisés par ASP.NET MVC de base. Ses responsabilités sont les suivantes :

* Ajout de filtres globaux au contexte
* Ajout de contrôleurs dans le contexte
* Ajout de méthodes publiques de contrôleur en tant qu’actions
* Ajout de paramètres de méthode d’action au contexte
* Application de routage et autres attributs

Certains comportements intégrés sont implémentées par le `DefaultApplicationModelProvider`. Ce fournisseur est responsable de la construction de la [ `ControllerModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), qui à son tour fait référence à [ `ActionModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ActionModel), [ `PropertyModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), et [ `ParameterModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ParameterModel) instances. La `DefaultApplicationModelProvider` classe est un détail d’implémentation interne de framework qui peuvent et ne changera à l’avenir. 

Le `AuthorizationApplicationModelProvider` est chargé d’appliquer le comportement associé à la `AuthorizeFilter` et `AllowAnonymousFilter` attributs. [En savoir plus sur ces attributs](xref:security/authorization/simple).

Le `CorsApplicationModelProvider` implémente le comportement associé à la `IEnableCorsAttribute` et `IDisableCorsAttribute`et le `DisableCorsAuthorizationFilter`. [En savoir plus sur les règles CORS](xref:security/cors).

## <a name="conventions"></a>Conventions

Le modèle d’application définit les abstractions de convention qui fournissent un moyen plus simple pour personnaliser le comportement des modèles de substitution de l’ensemble du modèle ou le fournisseur. Ces abstractions sont la méthode recommandée pour modifier le comportement de votre application. Conventions permettent de vous permet d’écrire du code qui s’appliquent dynamiquement les personnalisations. Alors que [filtres](xref:mvc/controllers/filters) fournissent un moyen de la modification de comportement de l’infrastructure, les personnalisations vous permettent de contrôler la façon dont l’application entière est reliée entre elles.

Les conventions suivantes sont disponibles :

* [`IApplicationModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

Les conventions sont appliquées en les ajoutant aux options de MVC ou en implémentant `Attribute`s et les appliquer aux contrôleurs, les actions ou les paramètres d’action (semblable à [ `Filters` ](xref:mvc/controllers/filters)). Contrairement aux filtres, les conventions sont uniquement exécutées lors du démarrage de l’application, pas dans le cadre de chaque demande.

### <a name="sample-modifying-the-applicationmodel"></a>Exemple : Modification de la ApplicationModel

La convention suivante permet d’ajouter une propriété au modèle d’application. 

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

Conventions de modèle d’application sont appliquées en tant qu’options lors de l’ajout dans MVC `ConfigureServices` dans `Startup`.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

Les propriétés sont accessibles à partir de la `ActionDescriptor` collection de propriétés dans les actions de contrôleur :

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>Exemple : Modification de la Description de ControllerModel

Comme dans l’exemple précédent, le modèle de contrôleur peut également être modifié pour inclure des propriétés personnalisées. Ceux-ci remplacent les propriétés existantes portant le même nom que celui spécifié dans le modèle d’application. L’attribut de convention suivant ajoute une description au niveau du contrôleur :

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

Cette convention est appliquée en tant qu’attribut sur un contrôleur.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

Accède à la propriété « description » de la même manière que dans les exemples précédents.

### <a name="sample-modifying-the-actionmodel-description"></a>Exemple : Modification de la Description de ActionModel

Une convention d’attribut distinct peut être appliquée aux actions individuelles, substituer le comportement déjà appliqué au niveau du contrôleur ou d’application.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

Cette application à une action dans le contrôleur de l’exemple précédent montre comment elle substitue à la convention de niveau contrôleur :

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>Exemple : Modification de la ParameterModel

La convention suivante peut être appliquée aux paramètres d’action pour modifier leur `BindingInfo`. La convention suivante requiert que le paramètre soit un paramètre d’itinéraire ; autres sources potentielles de la liaison (par exemple, les valeurs de chaîne de requête) sont ignorés.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

L’attribut peut être appliqué à n’importe quel paramètre d’action :

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>Exemple : Modification du nom de ActionModel

La convention suivante modifie le `ActionModel` pour mettre à jour le *nom* de l’action à laquelle elle est appliquée. Le nouveau nom est fourni en tant que paramètre à l’attribut. Ce nouveau nom est utilisé par le routage, afin qu’il affecte l’itinéraire utilisé pour accéder à cette méthode d’action.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

Cet attribut est appliqué à une méthode d’action dans le `HomeController`:

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

Même si le nom de la méthode est `SomeName`, l’attribut se substitue à la convention MVC d’en utilisant le nom de méthode et remplace le nom de l’action avec `MyCoolAction`. Par conséquent, l’itinéraire utilisé pour accéder à cette action est `/Home/MyCoolAction`.

> [!NOTE]
> Cet exemple est essentiellement identique à l’aide de la fonction intégrée [ActionName](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.actionnameattribute) attribut.

### <a name="sample-custom-routing-convention"></a>Exemple : Convention de routage personnalisé

Vous pouvez utiliser un `IApplicationModelConvention` pour personnaliser le fonctionnement du routage. Par exemple, la convention suivante intègrent des espaces de noms de contrôleurs dans leurs routes, en remplaçant `.` dans l’espace de noms `/` dans l’itinéraire :

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

La convention est ajoutée en tant qu’option de démarrage.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> Vous pouvez ajouter des conventions pour votre [intergiciel (middleware)](xref:fundamentals/middleware) en accédant à `MvcOptions` à l’aide de`services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`

Cet exemple s’applique à cette convention pour les itinéraires qui n’utilisent pas l’attribut routage où le contrôleur a « Namespace » dans son nom. Le contrôleur suivant illustre cette convention :

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>Utilisation du modèle d’application dans WebApiCompatShim

ASP.NET Core MVC utilise un autre ensemble de conventions d’ASP.NET Web API 2. À l’aide des conventions personnalisées, vous pouvez modifier le comportement d’une application ASP.NET MVC de base pour être cohérent avec celui d’une application API Web. Est fourni avec Microsoft le [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) spécialement dans ce but.

> [!NOTE]
> En savoir plus sur [migration à partir de l’API Web ASP.NET](xref:migration/webapi).

Pour utiliser le Shim de compatibilité d’API Web, vous devez ajouter le package à votre projet, puis ajouter les conventions pour MVC en appelant `AddWebApiConventions` dans `Startup`:

```c#
services.AddMvc().AddWebApiConventions();
```

Les conventions fournies par le shim sont appliquées uniquement à des parties de l’application ont été certains attributs appliqués. Les quatre attributs suivants sont utilisés pour contrôler quels contrôleurs doivent avoir leurs conventions modifiées par les conventions de shim :

* [UseWebApiActionConventionsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>Conventions d’action

Le `UseWebApiActionConventionsAttribute` est utilisé pour mapper la méthode HTTP à des actions en fonction de leur nom (par exemple, `Get` mappe sur `HttpGet`). Il s’applique uniquement à des actions qui n’utilisent pas de routage d’attributs.

### <a name="overloading"></a>Surcharge

Le `UseWebApiOverloadingAttribute` est utilisé pour appliquer la `WebApiOverloadingApplicationModelConvention` convention. Cette convention ajoute une `OverloadActionConstraint` au processus de sélection d’action, ce qui limite les actions de candidat à ceux pour lesquels la demande répond à tous les paramètres non facultatifs.

### <a name="parameter-conventions"></a>Conventions de paramètre

Le `UseWebApiParameterConventionsAttribute` est utilisé pour appliquer la `WebApiParameterConventionsApplicationModelConvention` convention d’action. Cette convention Spécifie que les types simples utilisés comme paramètres d’action sont liés à partir de l’URI par défaut, alors que les types complexes sont liés à partir du corps de la demande.

### <a name="routes"></a>Itinéraires

Le `UseWebApiRoutesAttribute` contrôles si le `WebApiApplicationModelConvention` convention de contrôleur est appliquée. Lorsque activé, cette convention est utilisée pour ajouter la prise en charge de [zones](xref:mvc/controllers/areas) vers l’itinéraire.

En plus d’un ensemble de conventions, le package de compatibilité inclut un `System.Web.Http.ApiController` classe de base qui remplace celui fourni par l’API Web. Ainsi, vos contrôleurs écrits pour API Web et l’héritage à partir de son `ApiController` fonctionne comme prévus, lors de l’exécution d’ASP.NET MVC de base. Cette classe de base de contrôleur est décorée avec tous les `UseWebApi*` attributs répertoriés ci-dessus. Le `ApiController` expose les propriétés, méthodes et types de résultats qui sont compatibles avec ceux de l’API Web.

## <a name="using-apiexplorer-to-document-your-app"></a>À l’aide de ApiExplorer pour documenter votre application

Le modèle d’application expose une [ `ApiExplorer` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) propriété à chaque niveau qui peut être utilisé pour parcourir la structure de l’application. Cela peut être utilisé pour [générer des pages d’aide pour votre API Web à l’aide des outils tels que Swagger](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger). Le `ApiExplorer` propriété expose un `IsVisible` propriété qui peut être définie pour spécifier les parties du modèle de votre application doivent être exposées. Vous pouvez configurer ce paramètre en utilisant une convention :

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

À l’aide de cette approche (et des conventions supplémentaires si nécessaire), vous pouvez activer ou désactiver la visibilité des API n’importe quel niveau au sein de votre application. 

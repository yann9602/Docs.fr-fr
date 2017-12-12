---
uid: web-api/overview/advanced/configuring-aspnet-web-api
title: "Configuration d’ASP.NET Web API 2 | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2014
ms.topic: article
ms.assetid: 9e10a700-8d91-4d2e-a31e-b8b569fe867c
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/advanced/configuring-aspnet-web-api
msc.type: authoredcontent
ms.openlocfilehash: 1c007c4c327b7cde6ff52c6b0022acdff3c9b137
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="configuring-aspnet-web-api-2"></a>Configuration d’ASP.NET Web API 2
====================
par [Mike Wasson](https://github.com/MikeWasson)

Cette rubrique décrit comment configurer l’API Web ASP.NET.

- [Paramètres de configuration](#settings)
- [Configuration des API Web avec hébergement ASP.NET](#webhost)
- [Configuration des API Web avec l’auto-hébergement OWIN](#selfhost)
- [Services API Web globale](#services)
- [Configuration par contrôleur](#percontrollerconfig)

<a id="settings"></a>
## <a name="configuration-settings"></a>Paramètres de configuration

Paramètres de configuration d’API Web sont définies dans le [HttpConfiguration](https://msdn.microsoft.com/en-us/library/system.web.http.httpconfiguration.aspx) classe.

| Membre | Description |
| --- | --- |
| **DependencyResolver** | Permet l’injection de dépendances pour les contrôleurs. Consultez [à l’aide de la résolution des dépendances Web API](dependency-injection.md). |
| **Filtres** | Filtres d’action. |
| **Formateurs** | [Formateurs de type de média](../formats-and-model-binding/media-formatters.md). |
| **IncludeErrorDetailPolicy** | Spécifie si le serveur doit inclure les détails de l’erreur, telles que les messages d’exception et des traces de pile, dans les messages de réponse HTTP. Consultez [IncludeErrorDetailPolicy](https://msdn.microsoft.com/en-us/library/system.web.http.includeerrordetailpolicy(v=vs.108)). |
| **Initialiseur** | Une fonction qui effectue l’initialisation finale de la **HttpConfiguration**. |
| **MessageHandlers** | [Gestionnaires de messages HTTP](http-message-handlers.md). |
| **ParameterBindingRules** | Une collection de règles pour la liaison de paramètres sur les actions de contrôleur. |
| **Propriétés** | Un sac de propriétés générique. |
| **Itinéraires** | La collection d’itinéraires. Consultez [le routage ASP.NET Web API](../web-api-routing-and-actions/routing-in-aspnet-web-api.md). |
| **Services** | La collection de services. Consultez [Services](#services). |


## <a name="prerequisites"></a>Conditions préalables

[Visual Studio 2017](https://www.visualstudio.com/vs/) Community, Professional ou Enterprise Edition.

<a id="webhost"></a>
## <a name="configuring-web-api-with-aspnet-hosting"></a>Configuration des API Web avec hébergement ASP.NET

Dans une application ASP.NET, configurez l’API Web en appelant [GlobalConfiguration.Configure](https://msdn.microsoft.com/en-us/library/system.web.http.globalconfiguration.configure.aspx) dans les **Application\_Démarrer** (méthode). Le **configurer** méthode prend un délégué avec un seul paramètre de type **HttpConfiguration**. Effectuez toutes les votre configuration à l’intérieur du délégué.

Voici un exemple d’utilisation d’un délégué anonyme :

[!code-csharp[Main](configuring-aspnet-web-api/samples/sample1.cs)]

Dans Visual Studio 2017, le modèle de projet « Application de Web ASP.NET » configure automatiquement le code de configuration, si vous sélectionnez « API Web » dans le **nouveau projet ASP.NET** boîte de dialogue.

[![](configuring-aspnet-web-api/_static/image2.png)](configuring-aspnet-web-api/_static/image1.png)

Le modèle de projet crée un fichier nommé WebApiConfig.cs à l’intérieur de l’application\_dossier de démarrage. Ce fichier de code définit le délégué où vous placez votre code de configuration Web API.

![](configuring-aspnet-web-api/_static/image3.png)

[!code-csharp[Main](configuring-aspnet-web-api/samples/sample2.cs?highlight=12)]

Le modèle de projet ajoute également le code qui appelle le délégué à partir de **Application\_Démarrer**.

[!code-csharp[Main](configuring-aspnet-web-api/samples/sample3.cs?highlight=5)]

<a id="selfhost"></a>
## <a name="configuring-web-api-with-owin-self-hosting"></a>Configuration des API Web avec l’auto-hébergement OWIN

Si vous êtes d’auto-hébergement avec OWIN, créez un nouveau **HttpConfiguration** instance. Effectuer la configuration sur cette instance, puis passez l’instance à la **Owin.UseWebApi** méthode d’extension.

[!code-csharp[Main](configuring-aspnet-web-api/samples/sample4.cs)]

Le didacticiel [OWIN utilisez Self-Host ASP.NET Web API 2](../hosting-aspnet-web-api/use-owin-to-self-host-web-api.md) montre les étapes à suivre.

<a id="services"></a>
## <a name="global-web-api-services"></a>Services API Web globale

Le **HttpConfiguration.Services** collection contient un ensemble de services globaux qui utilise des API Web pour effectuer diverses tâches, telles que de la négociation de contenu et de la sélection du contrôleur.

> [!NOTE]
> Le **Services** collection n’est pas un mécanisme à usage général pour l’injection de découverte ou la dépendance du service. Elle stocke uniquement les types de service qui sont connus de l’infrastructure de l’API Web.


Le **Services** la collection est initialisée avec un ensemble par défaut des services, et vous pouvez fournir vos propres implémentations personnalisées. Certains services prend en charge plusieurs instances, alors que d’autres utilisateurs peuvent avoir qu’une seule instance. (Toutefois, vous pouvez également fournir des services au niveau du contrôleur ; consultez [par contrôleur Configuration](#percontrollerconfig).

Services d’Instance unique


| Service | Description |
| --- | --- |
| **IActionValueBinder** | Obtient une liaison pour un paramètre. |
| **IApiExplorer** | Obtient les descriptions des API exposées par l’application. Consultez [création d’une Page d’aide pour une API Web](../getting-started-with-aspnet-web-api/creating-api-help-pages.md). |
| **IAssembliesResolver** | Obtient une liste des assemblys de l’application. Consultez [routage et sélection de l’Action](../web-api-routing-and-actions/routing-and-action-selection.md). |
| **IBodyModelValidator** | Valide un modèle qui est lu à partir du corps de demande par un formateur de type de média. |
| **IContentNegotiator** | Effectue une négociation de contenu. |
| **IDocumentationProvider** | Fournit la documentation pour les API. La valeur par défaut est **null**. Consultez [création d’une Page d’aide pour une API Web](../getting-started-with-aspnet-web-api/creating-api-help-pages.md). |
| **IHostBufferPolicySelector** | Indique si l’ordinateur hôte doit mettre en mémoire tampon corps d’entité HTTP message. |
| **IHttpActionInvoker** | Appelle une action du contrôleur. Consultez [routage et sélection de l’Action](../web-api-routing-and-actions/routing-and-action-selection.md). |
| **IHttpActionSelector** | Sélectionne une action du contrôleur. Consultez [routage et sélection de l’Action](../web-api-routing-and-actions/routing-and-action-selection.md). |
| **IHttpControllerActivator** | Active un contrôleur. Consultez [routage et sélection de l’Action](../web-api-routing-and-actions/routing-and-action-selection.md). |
| **IHttpControllerSelector** | Sélectionne un contrôleur. Consultez [routage et sélection de l’Action](../web-api-routing-and-actions/routing-and-action-selection.md). |
| **IHttpControllerTypeResolver** | Fournit une liste des types de contrôleur d’API Web dans l’application. Consultez [routage et sélection de l’Action](../web-api-routing-and-actions/routing-and-action-selection.md). |
| **ITraceManager** | Initialise l’infrastructure de suivi. Consultez [Tracing in ASP.NET Web API](../testing-and-debugging/tracing-in-aspnet-web-api.md). |
| **ITraceWriter** | Fournit un writer de suivi. La valeur par défaut est un writer de suivi de « no-op ». Consultez [Tracing in ASP.NET Web API](../testing-and-debugging/tracing-in-aspnet-web-api.md). |
| **IModelValidatorCache** | Fournit un cache de validateurs du modèle. |

Services de plusieurs instances


| Service | Description |
| --- | --- |
| **IFilterProvider** | Retourne une liste de filtres pour une action de contrôleur. |
| **ModelBinderProvider** | Retourne un classeur de modèles pour un type donné. |
| **ModelMetadataProvider** | Fournit des métadonnées pour un modèle. |
| **ModelValidatorProvider** | Fournit un validateur pour un modèle. |
| **ValueProviderFactory** | Crée un fournisseur de valeur. Pour plus d’informations, voir blog de Mike Stall [la création d’un fournisseur de valeur personnalisée dans WebAPI](https://blogs.msdn.com/b/jmstall/archive/2012/04/23/how-to-create-a-custom-value-provider-in-webapi.aspx) |.

Pour ajouter une implémentation personnalisée à un service à plusieurs instances, appelez **ajouter** ou **insérer** sur la **Services** collection :

[!code-csharp[Main](configuring-aspnet-web-api/samples/sample5.cs)]

Pour remplacer un service à instance unique avec une implémentation personnalisée, appelez **remplacer** sur la **Services** collection :

[!code-csharp[Main](configuring-aspnet-web-api/samples/sample6.cs)]

<a id="percontrollerconfig"></a>
## <a name="per-controller-configuration"></a>Configuration par contrôleur

Vous pouvez remplacer les paramètres suivants sur une base par contrôleur :

- Formateurs de type de média
- Règles de liaison de paramètre
- Services

Pour ce faire, définissez un attribut personnalisé qui implémente le **IControllerConfiguration** interface. Ensuite, appliquez l’attribut au contrôleur.

L’exemple suivant remplace les formateurs de type de média par défaut par un formateur personnalisé.

[!code-csharp[Main](configuring-aspnet-web-api/samples/sample7.cs)]

Le **IControllerConfiguration.Initialize** méthode accepte deux paramètres :

- Un **HttpControllerSettings** objet
- Un **HttpControllerDescriptor** objet

Le **HttpControllerDescriptor** contient une description du contrôleur, vous pouvez examiner à titre d’information (par exemple, pour faire la distinction entre les deux contrôleurs).

Utilisez le **HttpControllerSettings** objet pour configurer le contrôleur. Cet objet contient le sous-ensemble des paramètres de configuration que vous pouvez substituer sur une base par contrôleur. Tous les paramètres que vous ne modifiez pas par défaut pour global **HttpConfiguration** objet.

---
uid: web-api/overview/releases/whats-new-in-aspnet-web-api-odata-53
title: "Quelles sont les nouveautés dans ASP.NET Web API OData 5.3 | Documents Microsoft"
author: microsoft
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/16/2014
ms.topic: article
ms.assetid: e39eaa25-83ff-41dc-869d-3818d59a88ae
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/releases/whats-new-in-aspnet-web-api-odata-53
msc.type: authoredcontent
ms.openlocfilehash: e918f86ebd813f31ad0c21566e617482fc7dd963
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="whats-new-in-aspnet-web-api-odata-53"></a>Quelles sont les nouveautés dans ASP.NET Web API OData 5.3
====================
par [Microsoft](https://github.com/microsoft)

Cette rubrique décrit les nouveautés de ASP.NET Web API OData 5.3.

- [Télécharger](#download)
- [Documentation](#documentation)
- [Bibliothèques principales d’OData](#corelib)
- [Nouvelles fonctionnalités](#newf)
- [Problèmes connus et les modifications avec rupture](#known-issues)
- [Correctifs de bogues](#bug-fixes)
- [ASP.NET Web API OData 5.3.1](#OD)

<a id="download"></a>
## <a name="download"></a>Téléchargement

Les fonctions d’exécution sont publiées en tant que packages NuGet lors de la galerie NuGet. Vous pouvez installer ou mettre à jour pour les packages NuGet publiés à l’aide de la Console du Gestionnaire de Package NuGet :

[!code-console[Main](whats-new-in-aspnet-web-api-odata-53/samples/sample1.cmd)]

<a id="documentation"></a>
## <a name="documentation"></a>Documentation

Vous pouvez rechercher des didacticiels et autres documentations sur les API Web ASP.NET OData à le [site web ASP.NET](../odata-support-in-aspnet-web-api/index.md).

<a id="corelib"></a>
## <a name="odata-core-libraries"></a>Bibliothèques principales d’OData

Pour OData v4, API Web utilise désormais ODataLib version 6.5.0

<a id="newf"></a>
## <a name="new-features-in-aspnet-web-api-odata-53"></a>Nouvelles fonctionnalités dans ASP.NET Web API OData 5.3

### <a name="support-for-levels-in-expand"></a>Prise en charge pour $levels dans $développez

Vous pouvez utiliser la $levels option dans $développer des requêtes de requête. Exemple :

[!code-console[Main](whats-new-in-aspnet-web-api-odata-53/samples/sample2.cmd)]

Cette requête est équivalente à :

[!code-console[Main](whats-new-in-aspnet-web-api-odata-53/samples/sample3.cmd)]

<a id="open-entity-types"></a>
### <a name="support-for-open-entity-types"></a>Prise en charge des Types d’entités ouvert

Un *ouvrir type* est un type de stuctured qui contient les propriétés dynamiques, en plus de toutes les propriétés qui sont déclarées dans la définition de type. Les types ouverts permettent d’ajouter une grande souplesse à vos modèles de données. Pour plus d’informations, consultez xxxx.

### <a name="support-for-dynamic-collection-properties-in-open-types"></a>Prise en charge pour les propriétés de collection dynamique dans les types ouverts

Auparavant, une propriété dynamique devait être une valeur unique. 5.3, les propriétés dynamiques peuvent avoir des valeurs de la collection. Par exemple, dans la charge utile JSON suivante, le `Emails` est une propriété dynamique et est de collection de type chaîne :

[!code-console[Main](whats-new-in-aspnet-web-api-odata-53/samples/sample4.cmd)]

### <a name="support-for-inheritance-for-complex-types"></a>Prise en charge de l’héritage pour les types complexes

Désormais, les types complexes peuvent hériter d’un type de base. Par exemple, un service OData peut définir des types complexes suivants :

[!code-csharp[Main](whats-new-in-aspnet-web-api-odata-53/samples/sample5.cs)]

Voici le modèle EDM pour cet exemple :

[!code-xml[Main](whats-new-in-aspnet-web-api-odata-53/samples/sample6.xml?highlight=8,15)]

Pour plus d’informations, consultez [exemple d’héritage de Type complexe de OData](http://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OData/v4/ODataComplexTypeInheritanceSample/ReadMe.txt).

<a id="known-issues"></a>
## <a name="known-issues-and-breaking-changes"></a>Problèmes connus et les modifications avec rupture

Cette section décrit les problèmes connus et les modifications avec rupture dans les 5.3 ASP.NET Web API OData.

### <a name="odata-v4"></a>OData v4

#### <a name="query-options"></a>Options de requête

Problème : À l’aide de $ imbriquée développez avec $levels = max entraîne une profondeur d’expansion incorrect.

Par exemple, étant donné la requête suivante :

[!code-console[Main](whats-new-in-aspnet-web-api-odata-53/samples/sample7.cmd)]

Si `MaxExpansionDepth` est 5, cette requête entraînerait une profondeur d’expansion de 6.

<a id="bug-fixes"></a>
## <a name="bug-fixes-and-minor-feature-updates"></a>Correctifs de bogues et mises à jour mineures de fonctionnalité

Cette version inclut également plusieurs correctifs de bogues et les fonctionnalités mineures mises à jour. Vous trouverez la liste complète ici :

- [Correctifs de bogues](https://aspnetwebstack.codeplex.com/workitem/list/advanced?keyword=&status=All&type=All&priority=All&release=v5.3%20Beta&assignedTo=All&component=Web%20API|Web%20API%20OData&sortField=AssignedTo&sortDirection=Ascending&page=0&reasonClosed=Fixed)

<a id="OD"></a>
## <a name="aspnet-web-api-odata-531"></a>ASP.NET Web API OData 5.3.1

Dans cette version, nous avons apporté un [bogue](https://aspnetwebstack.codeplex.com/workitem/list/advanced?keyword=&amp;status=All&amp;type=All&amp;priority=All&amp;release=v5.3.1%20Beta&amp;assignedTo=All&amp;component=Web%20API%20OData&amp;sortField=LastUpdatedDate&amp;sortDirection=Descending&amp;page=0&amp;reasonClosed=All) à certaines des énumérations AllowedFunctions. Cette version ne prend toutes les autres correctifs de bogues ou les nouvelles fonctionnalités.

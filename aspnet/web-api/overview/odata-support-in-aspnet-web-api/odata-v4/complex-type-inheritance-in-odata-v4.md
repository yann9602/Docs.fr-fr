---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/complex-type-inheritance-in-odata-v4
title: "Héritage de Type complexe dans OData v4 avec ASP.NET Web API | Documents Microsoft"
author: microsoft
description: "Selon la spécification OData v4, un type complexe peut hériter d’un autre type complex. (Un type complexe est un type structuré sans une clé.) API Web..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/16/2014
ms.topic: article
ms.assetid: a00d3600-9c2a-41bc-9460-06cc527904e2
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/complex-type-inheritance-in-odata-v4
msc.type: authoredcontent
ms.openlocfilehash: be2dbfa82b99b6c48928e4e767716852c14a463b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="complex-type-inheritance-in-odata-v4-with-aspnet-web-api"></a>Héritage de Type complexe dans OData v4 avec l’API Web ASP.NET
====================
par [Microsoft](https://github.com/microsoft)

> Selon la norme OData v4 [spécification](http://www.odata.org/documentation/odata-version-4-0/), un type complex peut hériter d’un autre type complexe. (A *complexes* type est un type structuré sans une clé.) API Web OData 5.3 prend en charge l’héritage de type complexe.
> 
> Cette rubrique montre comment créer un entity data model (EDM) avec des types complexes d’héritage. Pour le code source complet, consultez [exemple d’héritage de Type complexe de OData](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/OData/v4/ODataComplexTypeInheritanceSample/ReadMe.txt).
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - OData d’API Web 5.3
> - OData v4


## <a name="model-hierarchy"></a>Hiérarchie de modèle

Pour illustrer l’héritage de type complexe, nous allons utiliser la hiérarchie de classe suivante.

![](complex-type-inheritance-in-odata-v4/_static/image1.png)

`Shape`est un type complexe abstrait. `Rectangle`, `Triangle`, et `Circle` sont des types complexes dérivés `Shape`, et `RoundRectangle` dérive `Rectangle`. `Window`est un type d’entité et contient un `Shape` instance.

Voici les classes CLR qui définissent ces types.

[!code-csharp[Main](complex-type-inheritance-in-odata-v4/samples/sample1.cs)]

## <a name="build-the-edm-model"></a>Générer le modèle EDM

Pour créer le modèle EDM, vous pouvez utiliser **ODataConventionModelBuilder**, lequel déduit les relations d’héritage à partir des types CLR.

[!code-csharp[Main](complex-type-inheritance-in-odata-v4/samples/sample2.cs)]

Vous pouvez également générer le modèle EDM explicitement, à l’aide de **odatamodelbuilder ayant**. Cela prend plus de code, mais vous donne davantage de contrôle sur le modèle EDM.

[!code-csharp[Main](complex-type-inheritance-in-odata-v4/samples/sample3.cs)]

Ces deux exemples de créent le même schéma EDM.

## <a name="metadata-document"></a>Document de métadonnées

Voici le document de métadonnées OData, montrant l’héritage de type complexe.

[!code-xml[Main](complex-type-inheritance-in-odata-v4/samples/sample4.xml?highlight=13,17,25,30)]

À partir du document de métadonnées, vous pouvez voir que :

- Le `Shape` type complexe est abstraite.
- Le `Rectangle`, `Triangle`, et `Circle` type complexe ont le type de base `Shape`.
- Le `RoundRectangle` type a le type de base `Rectangle`.

## <a name="casting-complex-types"></a>Conversion de Types complexes

Effectuer un cast sur des types complexes est maintenant pris en charge. Par exemple, la requête suivante casts un `Shape` à un `Rectangle`.

[!code-console[Main](complex-type-inheritance-in-odata-v4/samples/sample5.cmd)]

Voici la charge utile de réponse :

[!code-console[Main](complex-type-inheritance-in-odata-v4/samples/sample6.cmd)]

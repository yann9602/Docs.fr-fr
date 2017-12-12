---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/odata-containment-in-web-api-22
title: "Relation contenant-contenu dans OData v4 à l’aide des API 2.2 Web | Documents Microsoft"
author: rick-anderson
description: "En règle générale, une entité est uniquement accessible si elle a été encapsulé dans un jeu d’entités. Mais OData v4 fournit deux options supplémentaires, Singleton et Con..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/27/2014
ms.topic: article
ms.assetid: 5fbfefad-a17a-4c46-8646-f1ccd154cd56
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/odata-containment-in-web-api-22
msc.type: authoredcontent
ms.openlocfilehash: 7d3c81bf3d2a43faa3e71155637e031f81143782
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="containment-in-odata-v4-using-web-api-22"></a>Relation contenant-contenu dans OData v4 à l’aide de Web API 2.2
====================
par Jinfu Tan

> En règle générale, une entité est uniquement accessible si elle a été encapsulé dans un jeu d’entités. Mais OData v4 fournit deux options supplémentaires, Singleton et relation contenant-contenu, qui prend en charge WebAPI 2.2.


Cette rubrique montre comment définir une relation contenant-contenu dans un point de terminaison OData WebApi 2.2. Pour plus d’informations sur la relation contenant-contenu, consultez [relation contenant-contenu à venir avec OData v4](https://blogs.msdn.com/b/odatateam/archive/2014/03/13/containment-is-coming-with-odata-v4.aspx). Pour créer un point de terminaison OData V4 dans l’API Web, consultez [créer un Using ASP.NET Web API 2.2 OData v4 point de terminaison](create-an-odata-v4-endpoint.md).

Tout d’abord, nous allons créer un modèle de domaine de relation contenant-contenu dans le service OData, à l’aide de ce modèle de données :

![Modèle de données](odata-containment-in-web-api-22/_static/image1.png)

Un compte contient de nombreux PaymentInstruments (PI), mais nous ne définissez pas une jeu d’entités pour un PI. Au lieu de cela, les instructions de traitement n’est accessible via un compte.

Vous pouvez télécharger la solution utilisée dans cette rubrique à partir de [CodePlex](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OData/v4/ODataContainmentSample/).

## <a name="defining-the-data-model"></a>Définition du modèle de données

1. Définir les types CLR.

    [!code-csharp[Main](odata-containment-in-web-api-22/samples/sample1.cs)]

    Le `Contained` attribut est utilisé pour les propriétés de navigation de relation contenant-contenu.
2. Générer le modèle EDM basé sur les types CLR.

    [!code-csharp[Main](odata-containment-in-web-api-22/samples/sample2.cs)]

    Le `ODataConventionModelBuilder` gère la création du modèle EDM si le `Contained` attribut est ajouté à la propriété de navigation correspondante. Si la propriété est un type de collection, un `GetCount(string NameContains)` fonction sera également créée.

    Les métadonnées générées seront présente comme suit :

    [!code-xml[Main](odata-containment-in-web-api-22/samples/sample3.xml?highlight=10)]

    Le `ContainsTarget` attribut indique que la propriété de navigation est une relation contenant-contenu.

## <a name="define-the-containing-entity-set-controller"></a>Définissez le contrôleur de jeu d’entité contenant

Entités de relation contenant-contenues ne possèdent pas leur propres contrôleur ; l’action est définie dans le contrôleur de jeu d’entité conteneur. Dans cet exemple, il est un AccountsController, mais aucun PaymentInstrumentsController.

[!code-csharp[Main](odata-containment-in-web-api-22/samples/sample4.cs)]

Si le chemin d’accès OData est 4 ou plusieurs segments, uniquement attribut fonctionne le routage, tel que `[ODataRoute("Accounts({accountId})/PayinPIs({paymentInstrumentId})")]` dans le contrôleur ci-dessus. Dans le cas contraire, les attributs et le routage classique fonctionne : par exemple, `GetPayInPIs(int key)` correspond à `GET ~/Accounts(1)/PayinPIs`.

*Merci d’avoir à Leo Hu pour le contenu d’origine de cet article.*

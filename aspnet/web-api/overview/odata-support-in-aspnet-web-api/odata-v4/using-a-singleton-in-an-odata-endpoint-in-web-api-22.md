---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/using-a-singleton-in-an-odata-endpoint-in-web-api-22
title: "Créer un Singleton dans OData v4, à l’aide des API 2.2 Web | Documents Microsoft"
author: rick-anderson
description: "Cette rubrique montre comment définir un singleton dans un point de terminaison OData 2.2 d’API Web."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/27/2014
ms.topic: article
ms.assetid: 4064ab14-26ee-4d5c-ae58-1bdda525ad06
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/using-a-singleton-in-an-odata-endpoint-in-web-api-22
msc.type: authoredcontent
ms.openlocfilehash: 92c5056548b1e39defb28ac36f83b001dd108f5f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="create-a-singleton-in-odata-v4-using-web-api-22"></a>Créer un Singleton dans OData v4, à l’aide de Web API 2.2
====================
par Zoe Luo

> En règle générale, une entité est uniquement accessible si elle a été encapsulé dans un jeu d’entités. Mais OData v4 fournit deux options supplémentaires, Singleton et relation contenant-contenu, qui prend en charge WebAPI 2.2.


Cet article explique comment définir un singleton dans un point de terminaison OData 2.2 d’API Web. Pour plus d’informations sur l’un singleton est et comment vous pouvez bénéficier de l’utiliser, consultez [à l’aide d’un singleton pour définir votre entité spéciale](https://blogs.msdn.com/b/odatateam/archive/2014/03/05/use-singleton-to-define-your-special-entity.aspx). Pour créer un point de terminaison OData V4 dans l’API Web, consultez [créer un Using ASP.NET Web API 2.2 OData v4 point de terminaison](create-an-odata-v4-endpoint.md). 

Nous allons créer un singleton dans votre projet d’API Web à l’aide du modèle de données suivantes :

![Modèle de données](using-a-singleton-in-an-odata-endpoint-in-web-api-22/_static/image1.png)

Un singleton nommé `Umbrella` sont définies selon le type `Company`et une entité nommée `Employees` sont définies selon le type `Employee`.

La solution utilisée dans ce didacticiel peut être téléchargée à partir de [CodePlex](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/OData/v4/ODataSingletonSample/).

## <a name="define-the-data-model"></a>Définir le modèle de données

1. Définir les types CLR.

    [!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample1.cs)]
2. Générer le modèle EDM basé sur les types CLR.

    [!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample2.cs)]

    Ici, `builder.Singleton<Company>("Umbrella")` indique le Générateur de modèles pour créer un singleton nommé `Umbrella` dans le modèle EDM.

    Les métadonnées générées seront présente comme suit :

    [!code-xml[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample3.xml)]

    À partir des métadonnées, nous pouvons voir que la propriété de navigation `Company` dans les `Employees` jeu d’entités est lié à singleton `Umbrella`. La liaison est effectuée automatiquement par `ODataConventionModelBuilder`, étant donné que seuls `Umbrella` a le `Company` type. S’il existe toute ambiguïté dans le modèle, vous pouvez utiliser `HasSingletonBinding` lier explicitement une propriété de navigation à un singleton ; `HasSingletonBinding` a le même effet que l’utilisation de la `Singleton` attribut dans la définition du type CLR :

    [!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample4.cs)]

## <a name="define-the-singleton-controller"></a>Définissez le contrôleur de singleton

Comme le contrôleur de l’EntitySet, le contrôleur de singleton hérite `ODataController`, et le nom du contrôleur singleton doit être `[singletonName]Controller`.

[!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample5.cs)]

Afin de gérer les différents types de requêtes, les actions sont requises pour être prédéfinies dans le contrôleur. **Attribut de routage** est activée par défaut dans WebApi 2.2. Par exemple, pour définir une action pour gérer les requêtes `Revenue` de `Company` à l’aide de routage d’attributs, utilisez les éléments suivants :

[!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample6.cs)]

Si vous n’êtes pas prêt définir les attributs pour chaque action, simplement définir vos actions suivant [Conventions de routage OData](../odata-routing-conventions.md). Dans la mesure où une clé n’est pas requise pour l’interrogation d’un singleton, les actions définies dans le contrôleur de singleton sont légèrement différentes de ceux indiqués dans le contrôleur entityset.

Pour référence, les signatures de méthode pour chaque définition de l’action dans le contrôleur de singleton sont répertoriées ci-dessous.

[!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample7.cs)]

Cela est en fait, vous devez simplement sur le côté service. Le [exemple de projet](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/OData/v4/ODataSingletonSample/) contient tout le code pour la solution et le client OData qui montre comment utiliser le singleton. Le client est construit en suivant les étapes dans [créer une application de Client OData v4](create-an-odata-v4-client-app.md).

. 

*Merci d’avoir à Leo Hu pour le contenu d’origine de cet article.*

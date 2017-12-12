---
uid: web-api/overview/odata-support-in-aspnet-web-api/using-select-expand-and-value
title: "À l’aide de $select, $expand et $value dans ASP.NET Web API 2 OData | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/11/2013
ms.topic: article
ms.assetid: 43279a80-a96c-4564-b6ea-ad992a2d6828
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/using-select-expand-and-value
msc.type: authoredcontent
ms.openlocfilehash: f229cdbd8850a787dd3585e0640e8e66f6109331
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-select-expand-and-value-in-aspnet-web-api-2-odata"></a>À l’aide de $select, $expand et $value dans ASP.NET Web API 2 OData
====================
par [Mike Wasson](https://github.com/MikeWasson)

API Web 2 ajoute la prise en charge pour le $expand, $select et options $value OData. Ces options permettent à un client contrôler la représentation, il récupère à partir du serveur.

- **$expand** provoque des entités associées être incluses inline dans la réponse.
- **$select** sélectionne un sous-ensemble de propriétés à inclure dans la réponse.
- **$value** Obtient la valeur brute d’une propriété.

## <a name="example-schema"></a>Schéma de l’exemple

Pour cet article, je vais utiliser un service OData qui définit trois entités : produit, fournisseur et la catégorie. Chaque produit possède une catégorie et un seul fournisseur.

![](using-select-expand-and-value/_static/image1.png)

Voici les classes c# qui définissent les modèles d’entité :

[!code-csharp[Main](using-select-expand-and-value/samples/sample1.cs)]

Notez que la `Product` classe définit les propriétés de navigation pour la `Supplier` et `Category`. La `Category` classe définit une propriété de navigation pour les produits dans chaque catégorie.

Pour créer un point de terminaison OData pour ce schéma, utilisez la génération de modèles automatique Visual Studio 2013, comme décrit dans [création d’un point de terminaison OData dans ASP.NET Web API](odata-v3/creating-an-odata-endpoint.md). Ajouter des contrôleurs distincts pour le fournisseur, catégorie et produit.

## <a name="enabling-expand-and-select"></a>L’activation de $développez et $select

Dans Visual Studio 2013, la génération de modèles automatique OData d’API Web crée un contrôleur qui prend automatiquement en charge $expand et un $select. Pour référence, voici la configuration requise pour prendre en charge $développez et $select dans un contrôleur.

Pour les de collections, le contrôleur `Get` méthode doit retourner un **IQueryable**.

[!code-csharp[Main](using-select-expand-and-value/samples/sample2.cs)]

Pour les entités uniques, retourner un **SingleResult&lt;T&gt;**, où T est un **IQueryable** qui contient les entités zéro ou un.

[!code-csharp[Main](using-select-expand-and-value/samples/sample3.cs)]

Également, décorer votre `Get` méthodes avec les **[Queryable]** d’attribut, comme indiqué dans les extraits de code précédent. Vous pouvez également appeler **EnableQuerySupport** sur la **HttpConfiguration** objet au démarrage. (Pour plus d’informations, consultez [l’activation des Options de requête OData](supporting-odata-query-options.md#enable).)

## <a name="using-expand"></a>À l’aide de $développez

Lorsque vous interrogez une entité OData ou la collection, la réponse par défaut n’inclut pas les entités associées. Par exemple, voici la réponse par défaut pour le jeu d’entités catégories :

[!code-console[Main](using-select-expand-and-value/samples/sample4.cmd)]

Comme vous pouvez le voir, la réponse n’inclut pas tous les produits, même si l’entité Category possède un lien de navigation de produits. Toutefois, le client peut utiliser $développer pour obtenir la liste des produits pour chaque catégorie. Le $expand option s’intègre dans la chaîne de requête de la demande :

[!code-console[Main](using-select-expand-and-value/samples/sample5.cmd)]

Le serveur inclut désormais les produits pour chaque catégorie, inline avec les catégories. Voici la charge utile de réponse :

[!code-console[Main](using-select-expand-and-value/samples/sample6.cmd)]

Notez que chaque entrée dans le tableau « value » contient une liste de produits.

Le $expand liste prend séparées par des virgules des options de propriétés de navigation à développer. La requête suivante étend la catégorie et le fournisseur pour un produit.

[!code-console[Main](using-select-expand-and-value/samples/sample7.cmd)]

Voici le corps de réponse :

[!code-console[Main](using-select-expand-and-value/samples/sample8.cmd)]

Vous pouvez développer plus d’un niveau de la propriété de navigation. L’exemple suivant inclut tous les produits pour une catégorie, ainsi que le fournisseur pour chaque produit.

[!code-console[Main](using-select-expand-and-value/samples/sample9.cmd)]

Voici le corps de réponse :

[!code-console[Main](using-select-expand-and-value/samples/sample10.cmd)]

Par défaut, les API Web limite la profondeur d’expansion maximale à 2. Le client qui empêche d’envoyer des requêtes complexes telles que `$expand=Orders/OrderDetails/Product/Supplier/Region`, qui peut être inefficace pour interroger et créer des réponses de grande taille. Pour substituer la valeur par défaut, définissez la **MaxExpansionDepth** propriété sur le **[Queryable]** attribut.

[!code-csharp[Main](using-select-expand-and-value/samples/sample11.cs)]

Pour plus d’informations sur la $développez option, consultez [développez système requête Option ($expand)](http://www.odata.org/documentation/odata-v2-documentation/uri-conventions/#46_Expand_System_Query_Option_expand) dans la documentation officielle de OData.

## <a name="using-select"></a>À l’aide de $select

L’option $select spécifie un sous-ensemble de propriétés à inclure dans le corps de réponse. Par exemple, pour obtenir uniquement le nom et le prix de chaque produit, utilisez la requête suivante :

[!code-console[Main](using-select-expand-and-value/samples/sample12.cmd)]

Voici le corps de réponse :

[!code-console[Main](using-select-expand-and-value/samples/sample13.cmd)]

Vous pouvez combiner $select et $expand dans la même requête. Veillez à inclure la propriété développée dans l’option $select. Par exemple, la requête suivante obtient le nom de produit et le fournisseur.

[!code-console[Main](using-select-expand-and-value/samples/sample14.cmd)]

Voici le corps de réponse :

[!code-console[Main](using-select-expand-and-value/samples/sample15.cmd)]

Vous pouvez également sélectionner les propriétés dans une propriété développée. La requête suivante étend les produits et sélectionne le nom de la catégorie et le nom du produit.

[!code-console[Main](using-select-expand-and-value/samples/sample16.cmd)]

Voici le corps de réponse :

[!code-console[Main](using-select-expand-and-value/samples/sample17.cmd)]

Pour plus d’informations sur l’option $select, consultez [système Option de requête Select ($select)](http://www.odata.org/documentation/odata-v2-documentation/uri-conventions/#48_Select_System_Query_Option_select) dans la documentation officielle de OData.

## <a name="getting-individual-properties-of-an-entity-value"></a>Obtention des propriétés individuelles d’une entité ($value)

Il existe deux façons pour un client OData obtenir une propriété individuelle d’une entité. Le client peut obtenir la valeur dans le format OData, soit obtenir la valeur brute de la propriété.

La requête suivante obtient une propriété dans le format OData.

[!code-console[Main](using-select-expand-and-value/samples/sample18.cmd)]

Voici un exemple de réponse au format JSON :

[!code-console[Main](using-select-expand-and-value/samples/sample19.cmd)]

Pour obtenir la valeur brute de la propriété, ajoute $value à l’URI :

[!code-console[Main](using-select-expand-and-value/samples/sample20.cmd)]

Voici la réponse. Notez que le type de contenu est « text/plain », pas JSON.

[!code-console[Main](using-select-expand-and-value/samples/sample21.cmd)]

Pour prendre en charge ces requêtes dans votre contrôleur OData, ajoutez une méthode nommée `GetProperty`, où `Property` est le nom de la propriété. Par exemple, la méthode à obtenir la propriété Name est nommée `GetName`. La méthode doit retourner la valeur de cette propriété :

[!code-csharp[Main](using-select-expand-and-value/samples/sample22.cs)]

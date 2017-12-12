---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/entity-relations-in-odata-v4
title: "Relations d’entité dans OData v4 à l’aide d’ASP.NET Web API 2.2 | Documents Microsoft"
author: MikeWasson
description: "La plupart des jeux de données définissent les relations entre des entités : les clients ont passé des commandes ; les livres peuvent avoir auteurs ; les produits ont des fournisseurs. L’utilisation d’OData, les clients peuvent naviguer sur..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2014
ms.topic: article
ms.assetid: 72657550-ec09-4779-9bfc-2fb15ecd51c7
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/entity-relations-in-odata-v4
msc.type: authoredcontent
ms.openlocfilehash: 4127fb2fa83f513a4faeb222068ca99f234ece22
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="entity-relations-in-odata-v4-using-aspnet-web-api-22"></a>Relations d’entité dans OData v4 à l’aide d’ASP.NET Web API 2.2
====================
par [Mike Wasson](https://github.com/MikeWasson)

> La plupart des jeux de données définissent les relations entre des entités : les clients ont passé des commandes ; les livres peuvent avoir auteurs ; les produits ont des fournisseurs. L’utilisation d’OData, les clients peuvent naviguer sur les relations d’entité. Étant donné un produit, vous pouvez trouver le fournisseur. Vous pouvez également créer ou supprimer des relations. Par exemple, vous pouvez définir le fournisseur pour un produit.
> 
> Ce didacticiel montre comment prendre en charge ces opérations dans OData v4, à l’aide des API Web ASP.NET. Le didacticiel s’appuie sur le didacticiel [créer un Using ASP.NET Web API 2 OData v4 point de terminaison](create-an-odata-v4-endpoint.md).
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Web API 2.1
> - OData v4
> - [Visual Studio 2013 Update 2](https://www.visualstudio.com/downloads/download-visual-studio-vs)
> - Entity Framework 6
> - .NET 4.5
> 
> 
> ## <a name="tutorial-versions"></a>Versions du didacticiels
> 
> Pour la Version 3 d’OData, consultez [prenant en charge les Relations entité OData V3](https://asp.net/web-api/overview/odata-support-in-aspnet-web-api/odata-v3/working-with-entity-relations).


## <a name="add-a-supplier-entity"></a>Ajouter une entité de fournisseur

> [!NOTE]
> Le didacticiel s’appuie sur le didacticiel [créer un Using ASP.NET Web API 2 OData v4 point de terminaison](create-an-odata-v4-endpoint.md).


Tout d’abord, nous avons besoin d’une entité associée. Ajoutez une classe nommée `Supplier` dans le dossier de modèles.

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample1.cs)]

Ajouter une propriété de navigation pour la `Product` classe :

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample2.cs?highlight=13-15)]

Ajouter un nouveau **DbSet** à la `ProductsContext` classe, afin qu’Entity Framework inclut la table de fournisseur dans la base de données.

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample3.cs?highlight=10)]

Dans WebApiConfig.cs, ajoutez un &quot;fournisseurs&quot; du jeu d’entités pour l’entity data model :

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample4.cs?highlight=6)]

## <a name="add-a-suppliers-controller"></a>Ajouter un contrôleur de fournisseurs

Ajouter un `SuppliersController` classe dans le dossier contrôleurs.

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample5.cs)]

Comment ajouter des opérations CRUD pour ce contrôleur n’indiquent pas. Les étapes sont les mêmes que pour le contrôleur de produits (voir [créer un point de terminaison OData v4](create-an-odata-v4-endpoint.md)).

## <a name="getting-related-entities"></a>Lors de l’obtention des entités associées

Pour obtenir le fournisseur pour un produit, le client envoie une demande GET :

[!code-console[Main](entity-relations-in-odata-v4/samples/sample6.cmd)]

Pour prendre en charge de cette demande, ajoutez la méthode suivante à la `ProductsController` classe :

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample7.cs)]

Cette méthode utilise une convention d’affectation de noms par défaut

- Nom de la méthode : GetX, où X est la propriété de navigation.
- Nom du paramètre : *clé*

Si vous suivez cette convention d’affectation de noms, Web API mappe automatiquement la requête HTTP à la méthode de contrôleur.

Requête d’exemple HTTP :

[!code-console[Main](entity-relations-in-odata-v4/samples/sample8.cmd)]

Réponse de l’exemple HTTP :

[!code-console[Main](entity-relations-in-odata-v4/samples/sample9.cmd)]

### <a name="getting-a-related-collection"></a>Obtention d’une collection connexe

Dans l’exemple précédent, un produit a un seul fournisseur. Une propriété de navigation peut également retourner une collection. Le code suivant obtient les produits pour un fournisseur :

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample10.cs)]

Dans ce cas, la méthode retourne un **IQueryable** au lieu d’un **SingleResult&lt;T&gt;**

Requête d’exemple HTTP :

[!code-console[Main](entity-relations-in-odata-v4/samples/sample11.cmd)]

Réponse de l’exemple HTTP :

[!code-console[Main](entity-relations-in-odata-v4/samples/sample12.cmd)]

## <a name="creating-a-relationship-between-entities"></a>Création d’une relation entre des entités

OData prend en charge la création ou la suppression des relations entre deux entités existantes. Dans la terminologie d’OData v4, la relation est une &quot;référence&quot;. (Dans OData v3, la relation a été appelée une *lien*. Les différences de protocole n’a pas d’importance pour ce didacticiel.)

Une référence a son propre URI, le formulaire `/Entity/NavigationProperty/$ref`. Par exemple, voici l’URI à résoudre la référence entre un produit et son fournisseur :

[!code-console[Main](entity-relations-in-odata-v4/samples/sample13.cmd)]

Pour ajouter une relation, le client envoie une demande POST ou PUT à cette adresse.

- Si la propriété de navigation est une entité unique, tel que `Product.Supplier`.
- VALIDER si la propriété de navigation est une collection, tel que `Supplier.Products`.

Le corps de la demande contient l’URI de l’autre entité dans la relation. Voici un exemple de demande :

[!code-console[Main](entity-relations-in-odata-v4/samples/sample14.cmd)]

Dans cet exemple, le client envoie une demande PUT pour `/Products(6)/Supplier/$ref`, qui est l’URI $ref pour la `Supplier` du produit avec l’ID = 6. Si la demande réussit, le serveur envoie la réponse 204 (aucun contenu) :

[!code-console[Main](entity-relations-in-odata-v4/samples/sample15.cmd)]

Voici la méthode de contrôleur pour ajouter une relation à un `Product`:

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample16.cs)]

Le *navigationProperty* paramètre spécifie quelle relation à définir. (S’il existe plus d’une propriété de navigation sur l’entité, vous pouvez ajouter d’autres `case` instructions.)

Le *lien* paramètre contient l’URI du fournisseur. API Web analyse automatiquement le corps de la requête pour obtenir la valeur de ce paramètre.

Pour rechercher le fournisseur, nous avons besoin de l’ID (ou clé), qui fait partie de la *lien* paramètre. Pour ce faire, utilisez la méthode d’assistance suivante :

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample17.cs)]

En fait, cette méthode utilise la bibliothèque OData à fractionner le chemin d’accès de l’URI en segments, sélectionnez le segment qui contient la clé et convertir la clé dans le type correct.

## <a name="deleting-a-relationship-between-entities"></a>Suppression d’une relation entre des entités

Pour supprimer une relation, le client envoie une requête HTTP DELETE à l’URI de $ref :

[!code-console[Main](entity-relations-in-odata-v4/samples/sample18.cmd)]

Voici la méthode de contrôleur pour supprimer la relation entre un produit et un fournisseur :

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample19.cs)]

Dans ce cas, `Product.Supplier` est la &quot;1&quot; fin d’une relation 1-à-plusieurs, vous pouvez donc supprimer la relation en définissant simplement `Product.Supplier` à `null`.

Dans le &quot;nombreux&quot; fin d’une relation, le client doit spécifier quels connexes d’entité à supprimer. Pour ce faire, le client envoie l’URI de l’entité associée dans la chaîne de requête de la demande. Par exemple, pour supprimer « Product 1 » de « fournisseur 1 » :

[!code-console[Main](entity-relations-in-odata-v4/samples/sample20.cmd?highlight=1)]

Pour prendre en charge cela dans l’API Web, nous devons inclure un paramètre supplémentaire dans le `DeleteRef` (méthode). Voici la méthode de contrôleur pour supprimer un produit à partir de la `Supplier.Products` relation.

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample21.cs)]

Le *clé* paramètre est la clé pour le fournisseur et le *relatedKey* paramètre est la clé du produit à supprimer de la `Products` relation. Notez que les API Web obtient automatiquement la clé de la chaîne de requête.

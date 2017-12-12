---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v3/working-with-entity-relations
title: "Prise en charge des Relations d’entité dans OData v3 avec l’API Web 2 | Documents Microsoft"
author: MikeWasson
description: "La plupart des jeux de données définissent les relations entre des entités : les clients ont passé des commandes ; les livres peuvent avoir auteurs ; les produits ont des fournisseurs. L’utilisation d’OData, les clients peuvent naviguer sur..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/26/2014
ms.topic: article
ms.assetid: 1e4c2eb4-b6cf-42ff-8a65-4d71ddca0394
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v3/working-with-entity-relations
msc.type: authoredcontent
ms.openlocfilehash: dec7e10e59cc2441c967afe062df227b105106a1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="supporting-entity-relations-in-odata-v3-with-web-api-2"></a>Prise en charge des Relations d’entité dans OData v3 avec l’API Web 2
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-Web-API-OData-cecdb524)

> La plupart des jeux de données définissent les relations entre des entités : les clients ont passé des commandes ; les livres peuvent avoir auteurs ; les produits ont des fournisseurs. L’utilisation d’OData, les clients peuvent naviguer sur les relations d’entité. Étant donné un produit, vous pouvez trouver le fournisseur. Vous pouvez également créer ou supprimer des relations. Par exemple, vous pouvez définir le fournisseur pour un produit.
> 
> Ce didacticiel montre comment prendre en charge ces opérations dans l’API Web ASP.NET. Le didacticiel s’appuie sur le didacticiel [création d’un point de terminaison OData v3 avec l’API Web 2](creating-an-odata-endpoint.md).
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - API Web 2
> - OData Version 3
> - Entity Framework 6


## <a name="add-a-supplier-entity"></a>Ajouter une entité de fournisseur

Nous devons d’abord ajouter un nouveau type d’entité à notre flux OData. Nous allons ajouter un `Supplier` classe.

[!code-csharp[Main](working-with-entity-relations/samples/sample1.cs)]

Cette classe utilise une chaîne pour la clé d’entité. Dans la pratique, qui peut être moins fréquentes qu’à l’aide d’une clé de type entier. Mais il est intéressant de voir comment OData gère les autres types de clés en plus des entiers.

Ensuite, nous allons créer une relation en ajoutant un `Supplier` propriété le `Product` classe :

[!code-csharp[Main](working-with-entity-relations/samples/sample2.cs)]

Ajouter un nouveau **DbSet** à la `ProductServiceContext` classe, afin qu’Entity Framework inclura le `Supplier` table dans la base de données.

[!code-csharp[Main](working-with-entity-relations/samples/sample3.cs?highlight=9)]

Dans WebApiConfig.cs, ajoutez une entité « Fournisseurs » pour le modèle EDM :

[!code-csharp[Main](working-with-entity-relations/samples/sample4.cs?highlight=4)]

## <a name="navigation-properties"></a>Propriétés de navigation

Pour obtenir le fournisseur pour un produit, le client envoie une demande GET :

[!code-console[Main](working-with-entity-relations/samples/sample5.cmd)]

« Fournisseur » est une propriété de navigation sur le `Product` type. Dans ce cas, `Supplier` fait référence à un élément unique, mais une navigation de propriété peut également retourner une collection (relation un-à-plusieurs ou plusieurs-à-plusieurs).

Pour prendre en charge de cette demande, ajoutez la méthode suivante à la `ProductsController` classe :

[!code-csharp[Main](working-with-entity-relations/samples/sample6.cs)]

Le *clé* paramètre est la clé du produit. La méthode retourne l’entité associée &#8212;dans ce cas, un `Supplier` instance. Le nom de la méthode et le nom de paramètre sont cruciales. En règle générale, si la propriété de navigation est nommée « X », vous devez ajouter une méthode nommée « GetX ». La méthode doit prendre un paramètre nommé «*clé*» qui correspond au type de données de clé du parent.

Il est également important d’inclure le **[FromOdataUri]** d’attribut dans le *clé* paramètre. Cet attribut indique à l’API Web à utiliser les règles de syntaxe OData lorsqu’il analyse la clé à partir de l’URI de requête.

## <a name="creating-and-deleting-links"></a>Création et suppression de liens

OData prend en charge la création ou la suppression des relations entre deux entités. Dans la terminologie d’OData, la relation est un lien «. » Chaque lien a un URI avec le formulaire *entité*/$links /*entité*. Par exemple, le lien à partir du produit fournisseur ressemble à ceci :

[!code-console[Main](working-with-entity-relations/samples/sample7.cmd)]

Pour créer un nouveau lien, le client envoie une demande POST à l’URI de lien. Le corps de la demande est l’URI de l’entité cible. Par exemple, qu'un fournisseur avec la clé « CTSO ». Pour créer un lien entre « Product(1) » et « Supplier('CTSO') », le client envoie une requête comme suit :

[!code-console[Main](working-with-entity-relations/samples/sample8.cmd)]

Pour supprimer un lien, le client envoie une demande de suppression pour l’URI de lien.

**Création de liens**

Pour permettre à un client créer des liens du fournisseur du produit, ajoutez le code suivant à la `ProductsController` classe :

[!code-csharp[Main](working-with-entity-relations/samples/sample9.cs)]

Cette méthode accepte trois paramètres :

- *clé*: la clé à l’entité parente (le produit)
- *navigationProperty*: le nom de la propriété de navigation. Dans cet exemple, la propriété de navigation uniquement valide est « Fournisseur ».
- *lien*: OData URI de l’entité associée. Cette valeur provient de corps de la demande. Par exemple, le lien URI peut être «`http://localhost/odata/Suppliers('CTSO')`, ce qui signifie que le fournisseur avec l’ID = 'CTSO'.

La méthode utilise le lien pour rechercher le fournisseur. Si le fournisseur correspondant est trouvé, la méthode définit le `Product.Supplier` propriété et enregistre le résultat dans la base de données.

La partie la plus difficile est l’analyse de l’URI de lien. En fait, vous devez simuler le résultat de l’envoi d’une requête GET à cet URI. La méthode d’assistance suivante montre comment effectuer cette opération. La méthode appelle le processus de routage d’API Web et récupère un **ODataPath** instance qui représente le chemin d’accès OData analysé. Pour un URI de lien, un des segments doit être la clé d’entité. (Dans le cas contraire, le client a envoyé un URI incorrect.)

[!code-csharp[Main](working-with-entity-relations/samples/sample10.cs)]

**Suppression de liens**

Pour supprimer un lien, ajoutez le code suivant à la `ProductsController` classe :

[!code-csharp[Main](working-with-entity-relations/samples/sample11.cs)]

Dans cet exemple, la propriété de navigation est un seul `Supplier` entité. Si la propriété de navigation est une collection, l’URI pour supprimer un lien doit inclure une clé pour l’entité associée. Exemple :

[!code-console[Main](working-with-entity-relations/samples/sample12.cmd)]

Cette requête supprime l’ordre 1 client 1. Dans ce cas, la méthode DeleteLink aura la signature suivante :

[!code-csharp[Main](working-with-entity-relations/samples/sample13.cs)]

Le *relatedKey* paramètre indique la clé pour l’entité associée. C’est le cas dans votre `DeleteLink` méthode, rechercher l’entité principale par le *clé* paramètre, rechercher l’entité associée par le *relatedKey* paramètre, puis supprimez l’association. Selon votre modèle de données, vous devrez peut-être implémenter les deux versions de `DeleteLink`. API Web appellera la version correcte en fonction de l’URI de requête.

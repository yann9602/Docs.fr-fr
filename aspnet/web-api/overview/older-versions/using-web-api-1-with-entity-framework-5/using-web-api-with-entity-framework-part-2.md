---
uid: web-api/overview/older-versions/using-web-api-1-with-entity-framework-5/using-web-api-with-entity-framework-part-2
title: "Partie 2 : Créer les modèles de domaine | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/03/2012
ms.topic: article
ms.assetid: fe3ef85f-bdc6-4e10-9768-25aa565c01d0
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/older-versions/using-web-api-1-with-entity-framework-5/using-web-api-with-entity-framework-part-2
msc.type: authoredcontent
ms.openlocfilehash: 5d4c7d7d02ced5a99db5b59f9e2e1adf6588208a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-2-creating-the-domain-models"></a>Partie 2 : Créer les modèles de domaine
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASP-NET-Web-API-with-afa30545)

## <a name="add-models"></a>Ajouter des modèles

Il existe trois façons de l’approche Entity Framework :

- Base de données en premier : vous démarrez avec une base de données, et Entity Framework génère le code.
- Modèle d’abord : vous démarrez avec un modèle visual et Entity Framework génère la base de données et le code.
- Code-first : vous démarrez avec le code et Entity Framework génère la base de données.

Nous utilisons l’approche code-first, donc nous allons commencer en définissant les objets de domaine en tant que POCOs (des objets brut-old CLR). Avec l’approche code-first, objets de domaine n’avez pas besoin tout code supplémentaire pour prendre en charge de la couche de base de données, telles que les transactions ou la persistance. (Plus précisément, ils n’avez pas besoin d’hériter la [EntityObject](https://msdn.microsoft.com/en-us/library/system.data.objects.dataclasses.entityobject.aspx) classe.) Vous pouvez toujours utiliser des annotations de données pour contrôler comment Entity Framework crée le schéma de base de données.

Étant donné que POCOs ne s’appliquent pas toutes les propriétés supplémentaires qui décrivent [de base de données d’état](https://msdn.microsoft.com/en-us/library/system.data.entitystate.aspx), ils peuvent facilement être sérialisés vers JSON ou XML. Toutefois, cela ne signifie pas vous devez toujours exposer vos modèles Entity Framework directement aux clients, comme nous le verrons plus tard dans le didacticiel.

Nous allons créer les POCOs suivants :

- Produit
- Trier
- OrderDetail

Pour créer chaque classe, cliquez sur le dossier de modèles dans l’Explorateur de solutions. Dans le menu contextuel, sélectionnez **ajouter** , puis sélectionnez **classe.**

![](using-web-api-with-entity-framework-part-2/_static/image1.png)

Ajouter un `Product` classe avec l’implémentation suivante :

[!code-csharp[Main](using-web-api-with-entity-framework-part-2/samples/sample1.cs)]

Par convention, Entity Framework utilise le `Id` propriété comme clé primaire et le mappe à une colonne d’identité dans la table de base de données. Lorsque vous créez un nouveau `Product` instance, vous ne définissez une valeur pour `Id`, car la base de données génère la valeur.

Le **ScaffoldColumn** attribut indique à ASP.NET MVC à ignorer le `Id` propriété lors de la génération d’un éditeur de formulaire. Le **requis** attribut est utilisé pour valider le modèle. Il spécifie que le `Name` propriété doit être une chaîne non vide.

Ajouter la `Order` classe :

[!code-csharp[Main](using-web-api-with-entity-framework-part-2/samples/sample2.cs)]

Ajouter la `OrderDetail` classe :

[!code-csharp[Main](using-web-api-with-entity-framework-part-2/samples/sample3.cs)]

## <a name="foreign-key-relations"></a>Relations de clés étrangères

Une commande contient de nombreux détails de commande, et chaque détail de la commande fait référence à un seul produit. Pour représenter ces relations, la `OrderDetail` classe définit des propriétés nommées `OrderId` et `ProductId`. Entity Framework déduit que ces propriétés représentent des clés étrangères et ajoute les contraintes foreign key à la base de données.

![](using-web-api-with-entity-framework-part-2/_static/image2.png)

Le `Order` et `OrderDetail` classes incluent également des propriétés de « navigation », qui contiennent des références aux objets connexes. Étant donné une commande, vous pouvez accéder à des produits dans l’ordre en suivant les propriétés de navigation.

Compilez le projet maintenant. Entity Framework utilise la réflexion pour découvrir les propriétés des modèles, de sorte qu’elle nécessite un assembly compilé créer le schéma de base de données.

## <a name="configure-the-media-type-formatters"></a>Configurer les formateurs de Type de média

A [formateur de type de média](../../formats-and-model-binding/media-formatters.md) est un objet qui sérialise les données lors de l’API Web écrit le corps de réponse HTTP. Les formateurs intégrés prend en charge la sortie JSON et XML. Par défaut, les deux de ces formateurs de sérialiser tous les objets par valeur.

Sérialisation par valeur pose un problème si un graphique d’objet contient des références circulaires. C’est exactement le cas avec les `Order` et `OrderDetail` des classes, car chacun conserve une référence à l’autre. Le module de formatage est suivent les références, l’écriture de chaque objet par valeur et vous accédez en cercles. Par conséquent, nous devons modifier le comportement par défaut.

Dans l’Explorateur de solutions, développez l’application\_dossier Démarrer et d’ouvrir le fichier nommé WebApiConfig.cs. Ajoutez le code suivant à la classe `WebApiConfig` :

[!code-csharp[Main](using-web-api-with-entity-framework-part-2/samples/sample4.cs?highlight=11)]

Ce code définit le formateur JSON pour conserver les références d’objet et supprime le formateur XML provenant du pipeline entièrement. (Vous pouvez configurer le formateur XML pour conserver les références d’objet, mais il est un peu plus de travail, et nous avons besoin uniquement de JSON pour cette application. Pour plus d’informations, consultez [la gestion des références d’objet circulaires](../../formats-and-model-binding/json-and-xml-serialization.md#handling_circular_object_references).)

>[!div class="step-by-step"]
[Précédent](using-web-api-with-entity-framework-part-1.md)
[Suivant](using-web-api-with-entity-framework-part-3.md)

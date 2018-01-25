---
uid: web-api/overview/older-versions/using-web-api-1-with-entity-framework-5/using-web-api-with-entity-framework-part-6
title: "Partie 6 : Création de produit et les contrôleurs de commande | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/04/2012
ms.topic: article
ms.assetid: 91ee29ee-0689-40ee-914a-e7dd733b6622
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/older-versions/using-web-api-1-with-entity-framework-5/using-web-api-with-entity-framework-part-6
msc.type: authoredcontent
ms.openlocfilehash: 3b33543f02479b97112a63eb3879967ae31ccfb3
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="part-6-creating-product-and-order-controllers"></a>Partie 6 : Création d’un produit et contrôleurs de commande
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASP-NET-Web-API-with-afa30545)

## <a name="add-a-products-controller"></a>Ajouter un contrôleur de produits

Le contrôleur de l’administrateur est pour les utilisateurs disposant des privilèges d’administrateur. Clients, quant à eux, peuvent afficher les produits, mais ne peut pas créer, mettre à jour ou supprimez-les.

Nous pouvons facilement restreindre l’accès aux méthodes Post, Put et Delete, en laissant les méthodes Get ouvert. Mais les données retournées pour un produit :

[!code-json[Main](using-web-api-with-entity-framework-part-6/samples/sample1.json?highlight=1)]

Le `ActualCost` propriété ne doit pas être visible pour les clients ! La solution consiste à définir un *objet de transfert de données* (DTO) qui inclut un sous-ensemble de propriétés qui doivent être visibles aux clients. Nous allons utiliser LINQ au projet `Product` instances à `ProductDTO` instances.

Ajoutez une classe nommée `ProductDTO` dans le dossier de modèles.

[!code-csharp[Main](using-web-api-with-entity-framework-part-6/samples/sample2.cs)]

Maintenant, ajoutez le contrôleur. Dans l’Explorateur de solutions, cliquez sur le dossier contrôleurs. Sélectionnez **ajouter**, puis sélectionnez **contrôleur**. Dans le **ajouter un contrôleur** boîte de dialogue, le nom du contrôleur &quot;ProductsController&quot;. Sous **modèle**, sélectionnez **contrôleur d’API vide**.

![](using-web-api-with-entity-framework-part-6/_static/image1.png)

Remplacer tous les éléments dans le fichier source avec le code suivant :

[!code-csharp[Main](using-web-api-with-entity-framework-part-6/samples/sample3.cs)]

Le contrôleur utilise toujours le `OrdersContext` pour interroger la base de données. Mais au lieu de retourner `Product` instances directement, nous appelons `MapProducts` pour projeter les sur `ProductDTO` instances :

[!code-csharp[Main](using-web-api-with-entity-framework-part-6/samples/sample4.cs?highlight=1)]

Le `MapProducts` méthode retourne un **IQueryable**, de sorte que nous pouvons composer le résultat avec les autres paramètres de requête. Vous pouvez le constater dans le `GetProduct` méthode, qui ajoute un **où** clause à la requête :

[!code-csharp[Main](using-web-api-with-entity-framework-part-6/samples/sample5.cs?highlight=2)]

## <a name="add-an-orders-controller"></a>Ajouter un contrôleur de commandes

Ensuite, ajoutez un contrôleur qui permet aux utilisateurs de créer et afficher les commandes.

Nous allons commencer par une autre DTO. Dans l’Explorateur de solutions, cliquez sur le dossier Modèles et ajoutez une classe nommée `OrderDTO` utiliser l’implémentation suivante :

[!code-csharp[Main](using-web-api-with-entity-framework-part-6/samples/sample6.cs)]

Maintenant, ajoutez le contrôleur. Dans l’Explorateur de solutions, cliquez sur le dossier contrôleurs. Sélectionnez **ajouter**, puis sélectionnez **contrôleur**. Dans le **ajouter un contrôleur** boîte de dialogue, définissez les options suivantes :

- Sous **nom de contrôleur**, entrez « OrdersController ».
- Sous **modèle**, sélectionnez « Contrôleur d’API avec actions en lecture/écriture, à l’aide d’Entity Framework ».
- Sous **classe de modèle**, sélectionnez &quot;ordre (ProductStore.Models)&quot;.
- Sous **classe de contexte de données**, sélectionnez &quot;OrdersContext (ProductStore.Models)&quot;.

![](using-web-api-with-entity-framework-part-6/_static/image2.png)

Cliquez sur **Ajouter**. Cela ajoute un fichier nommé OrdersController.cs. Ensuite, nous avons besoin de modifier l’implémentation par défaut du contrôleur.

Supprimez d’abord le `PutOrder` et `DeleteOrder` méthodes. Pour cet exemple, les clients ne peuvent pas modifier ou supprimer des commandes existantes. Dans une application réelle, vous devez beaucoup de logique principal pour gérer ces cas. (Par exemple, la commande déjà expédiée ?)

Modifier la `GetOrders` méthode pour retourner uniquement les commandes qui appartiennent à l’utilisateur :

[!code-csharp[Main](using-web-api-with-entity-framework-part-6/samples/sample7.cs)]

Modifier la `GetOrder` méthode comme suit :

[!code-csharp[Main](using-web-api-with-entity-framework-part-6/samples/sample8.cs)]

Voici les modifications que nous avons apporté à la méthode :

- La valeur de retour est un `OrderDTO` instance, au lieu d’un `Order`.
- Lorsque nous interroger la base de données pour la commande, nous utilisons le [DbQuery.Include](https://msdn.microsoft.com/library/gg696395) méthode pour extraire le `OrderDetail` et `Product` entités.
- Nous aplatir le résultats à l’aide d’une projection.

La réponse HTTP contient un tableau de produits et quantités :

[!code-json[Main](using-web-api-with-entity-framework-part-6/samples/sample9.json)]

Ce format est plus facile pour les clients à utiliser que le graphique d’objet d’origine, qui contient les entités imbriquées (ordre, les détails et produits).

La dernière méthode de prendre en compte `PostOrder`. Actuellement, cette méthode prend un `Order` instance. Voyons ce qui se passe si un client envoie un corps de demande comme suit :

[!code-json[Main](using-web-api-with-entity-framework-part-6/samples/sample10.json)]

Il s’agit d’un ordre bien structuré et Entity Framework sera heureusement insérer dans la base de données. Mais il contient une entité Product qui n’existe pas encore. Le client venez de créer un nouveau produit dans notre base de données ! Il s’agit une surprise au service Fulfillment ordre, lorsqu’ils voient un ordre pour koala porte. La morale est, vous méfier réellement les données que vous acceptez une demande POST ou PUT.

Pour éviter ce problème, modifiez le `PostOrder` méthode prendre une `OrderDTO` instance. Utilisez le `OrderDTO` pour créer le `Order`.

[!code-csharp[Main](using-web-api-with-entity-framework-part-6/samples/sample11.cs)]

Notez que nous utilisons le `ProductID` et `Quantity` propriétés et nous ignorer toutes les valeurs que le client a envoyé pour nom de produit ou de prix. Si l’ID de produit n’est pas valide, il enfreint la contrainte de clé étrangère dans la base de données, et l’instruction insert échouera, comme il le devrait.

Voici l’ensemble `PostOrder` méthode :

[!code-csharp[Main](using-web-api-with-entity-framework-part-6/samples/sample12.cs)]

Enfin, ajoutez le **Authorize** d’attribut pour le contrôleur :

[!code-csharp[Main](using-web-api-with-entity-framework-part-6/samples/sample13.cs)]

Maintenant, seuls les utilisateurs inscrits peuvent créer ou afficher les commandes.

>[!div class="step-by-step"]
[Précédent](using-web-api-with-entity-framework-part-5.md)
[Suivant](using-web-api-with-entity-framework-part-7.md)

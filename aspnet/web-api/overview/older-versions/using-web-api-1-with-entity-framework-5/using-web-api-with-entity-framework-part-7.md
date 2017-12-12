---
uid: web-api/overview/older-versions/using-web-api-1-with-entity-framework-5/using-web-api-with-entity-framework-part-7
title: "Partie 7 : Création de la Main Page | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/04/2012
ms.topic: article
ms.assetid: eb32a17b-626c-4373-9a7d-3387992f3c04
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/older-versions/using-web-api-1-with-entity-framework-5/using-web-api-with-entity-framework-part-7
msc.type: authoredcontent
ms.openlocfilehash: a17b9f26ec48b5410211d6dad6e4deec971642d7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-7-creating-the-main-page"></a>Partie 7 : Création de la Main Page
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASP-NET-Web-API-with-afa30545)

## <a name="creating-the-main-page"></a>Création de la Main Page

Dans cette section, vous allez créer la page principale de l’application. Cette page sera plus complexe que la page d’administration, nous allons l’approche en plusieurs étapes. Avant cela, vous verrez certaines techniques de Knockout.js plus avancées. Voici la disposition de la page de base :

![](using-web-api-with-entity-framework-part-7/_static/image1.png)

- « Products » contient un tableau de produits.
- « Achat » conserve un tableau de produits et quantités. En cliquant sur « Add to Cart » met à jour le panier d’achat.
- « Orders » contient un tableau de numéros de commande.
- « Détails » contient un détail de commande, qui est un tableau d’éléments (produits avec des quantités)

Nous allons commencer en définissant une disposition de base en HTML, sans liaison de données ou d’un script. Ouvrez le fichier Views/Home/Index.cshtml et remplacez tout le contenu avec les éléments suivants :

[!code-html[Main](using-web-api-with-entity-framework-part-7/samples/sample1.html)]

Ensuite, ajoutez une section de Scripts et créer un modèle de vue vide :

[!code-cshtml[Main](using-web-api-with-entity-framework-part-7/samples/sample2.cshtml)]

Selon la conception esquissée précédemment, notre modèle de vue doit observables pour les produits, achat, commandes et détails. Ajoutez les variables suivantes à la `AppViewModel` objet :

[!code-javascript[Main](using-web-api-with-entity-framework-part-7/samples/sample3.js)]

Les utilisateurs peuvent ajouter des éléments dans la liste de produits dans le panier et supprimer des éléments dans le panier d’achat. Pour encapsuler ces fonctions, nous allons créer une autre classe de modèle d’affichage qui représente un produit. Ajoutez le code suivant à `AppViewModel` :

[!code-javascript[Main](using-web-api-with-entity-framework-part-7/samples/sample4.js?highlight=4)]

Le `ProductViewModel` classe contient deux fonctions qui sont utilisées pour déplacer le produit vers et depuis le panier d’achat : `addItemToCart` ajoute une unité du produit dans le panier d’achat, et `removeAllFromCart` supprime toutes les quantités du produit.

Les utilisateurs peuvent sélectionner une commande existante et obtenir les détails de commande. Nous allons encapsuler cette fonctionnalité dans un autre modèle de vue :

[!code-javascript[Main](using-web-api-with-entity-framework-part-7/samples/sample5.js?highlight=4)]

Le `OrderDetailsViewModel` est initialisé avec une commande, et il extrait les détails de commande en envoyant une requête AJAX au serveur.

En outre, notez le `total` propriété sur le `OrderDetailsViewModel`. Cette propriété est un type spécial d’observable appelé un [calculée observable](http://knockoutjs.com/documentation/computedObservables.html). Comme son nom l’indique, un observable calculée vous permet de lier des données à une valeur calculée &#8212; dans ce cas, le coût total de la commande.

Ensuite, ajoutez ces fonctions à `AppViewModel`:

- `resetCart`Supprime tous les éléments du panier.
- `getDetails`Obtient les détails d’une commande (par pusing un nouveau `OrderDetailsViewModel` sur la `details` liste).
- `createOrder`Crée une nouvelle commande et vide le panier d’achat.


[!code-javascript[Main](using-web-api-with-entity-framework-part-7/samples/sample6.js?highlight=4)]

Enfin, initialiser le modèle d’affichage en effectuant les requêtes AJAX pour les produits et les commandes :

[!code-javascript[Main](using-web-api-with-entity-framework-part-7/samples/sample7.js)]

OK, cela représente beaucoup de code, mais nous l’avons réalisé des pas à pas, et j’espère que la conception est désactivée. Maintenant, nous pouvons ajouter certaines liaisons Knockout.js au code HTML.

**Produits**

Voici les liaisons pour la liste de produits :

[!code-html[Main](using-web-api-with-entity-framework-part-7/samples/sample8.html)]

Il effectue une itération sur le tableau de produits et affiche le nom et le prix. Le bouton « Ajouter à la commande » est visible uniquement lorsque l’utilisateur est connecté.

Les appels de bouton « Ajouter à la commande » `addItemToCart` sur la `ProductViewModel` instance pour le produit. Cela montre une fonctionnalité intéressante de Knockout.js : lorsqu’un modèle d’affichage contient des autres modèles de vue, vous pouvez appliquer ces liaisons au modèle interne. Dans cet exemple, les liaisons dans le `foreach` sont appliquées à chaque le `ProductViewModel` instances. Cette approche est beaucoup plus claire que le placement de toutes les fonctionnalités dans un modèle d’affichage unique.

**Panier**

Voici les liaisons pour le panier d’achat :

[!code-html[Main](using-web-api-with-entity-framework-part-7/samples/sample9.html)]

Il effectue une itération sur le tableau de panier et affiche le nom, le prix et la quantité. Notez que le lien « Supprimer » et le bouton « Créer une commande » sont liées à des fonctions de modèle d’affichage.

**Commandes**

Voici les liaisons pour la liste des commandes :

[!code-html[Main](using-web-api-with-entity-framework-part-7/samples/sample10.html)]

Il effectue une itération sur les commandes et affiche l’ID de commande. L’événement de clic sur le lien est lié à la `getDetails` (fonction).

**Détails de la commande**

Voici les liaisons pour les détails de commande :

[!code-html[Main](using-web-api-with-entity-framework-part-7/samples/sample11.html)]

Il effectue une itération sur les éléments dans l’ordre et affiche les produits, prix et quantité. L’élément div environnant est visible uniquement si le tableau de détails contient un ou plusieurs éléments.

## <a name="conclusion"></a>Conclusion

Dans ce didacticiel, vous avez créé une application qui utilise Entity Framework pour communiquer avec la base de données et les API de Web ASP.NET pour fournir une interface publique sur la couche données. ASP.NET MVC 4 nous permettent de restituer les pages HTML et Knockout.js plus jQuery pour fournir des interactions dynamiques sans rechargements de la page.

Ressources supplémentaires :

- [ASP.NET Data Access Content Map](https://msdn.microsoft.com/en-us/library/6759sth4.aspx)
- [Centre de développement Entity Framework](https://msdn.microsoft.com/en-US/data/ef)

>[!div class="step-by-step"]
[Précédent](using-web-api-with-entity-framework-part-6.md)

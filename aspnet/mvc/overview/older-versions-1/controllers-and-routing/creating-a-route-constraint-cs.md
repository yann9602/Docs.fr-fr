---
uid: mvc/overview/older-versions-1/controllers-and-routing/creating-a-route-constraint-cs
title: "Création d’une contrainte d’itinéraire (c#) | Documents Microsoft"
author: StephenWalther
description: "Dans ce didacticiel, Stephen Walther montre comment vous pouvez contrôler la façon dont le navigateur demande itinéraires à la correspondance en créant des contraintes d’itinéraire avec des expressions régulières."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/16/2009
ms.topic: article
ms.assetid: 0bfd06b1-12d3-4fbb-9779-a82e5eb7fe7d
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/creating-a-route-constraint-cs
msc.type: authoredcontent
ms.openlocfilehash: ee83a134dcbdd1abfb296f3126a64c7d4ebab7f5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-route-constraint-c"></a>Création d’une contrainte d’itinéraire (c#)
====================
par [Stephen Walther](https://github.com/StephenWalther)

> Dans ce didacticiel, Stephen Walther montre comment vous pouvez contrôler la façon dont le navigateur demande itinéraires à la correspondance en créant des contraintes d’itinéraire avec des expressions régulières.


Contraintes d’itinéraire vous permet de restreindre les demandes du navigateur qui correspond à un itinéraire particulier. Vous pouvez utiliser une expression régulière pour spécifier une contrainte d’itinéraire.

Par exemple, imaginez que vous avez défini l’itinéraire dans la liste 1 dans le fichier Global.asax.

**La liste 1 - Global.asax.cs**

[!code-csharp[Main](creating-a-route-constraint-cs/samples/sample1.cs)]

Le listing 1 contient un itinéraire nommé produit. Vous pouvez utiliser la gamme de produits pour mapper les demandes du navigateur à la ProductController contenu dans la liste 2.

**Listing 2 - Controllers\ProductController.cs**

[!code-csharp[Main](creating-a-route-constraint-cs/samples/sample2.cs)]

Notez que l’action Details() exposée par le contrôleur produit accepte un seul paramètre nommé productId. Ce paramètre est un paramètre de type entier.

L’itinéraire défini dans la liste 1 correspond à une des URL suivantes :

- / Produit/23
- / Produit/7

Malheureusement, l’itinéraire correspond également les URL suivantes :

- / Produits/texte
- / Produits/apple

Étant donné que l’action Details() attend un paramètre de type entier, une demande qui contient l’autre chose qu’une valeur entière provoque une erreur. Par exemple, si vous tapez l’URL /Product/apple dans votre navigateur vous obtenez la page d’erreur dans la Figure 1.


[![La boîte de dialogue Nouveau projet](creating-a-route-constraint-cs/_static/image1.jpg)](creating-a-route-constraint-cs/_static/image1.png)

**Figure 01**: voir une page éclater ([cliquez pour afficher l’image en taille réelle](creating-a-route-constraint-cs/_static/image2.png))


Vous souhaitez vraiment faire est uniquement en correspondance les URL qui contiennent un productId entier approprié. Vous pouvez utiliser une contrainte lors de la définition d’un itinéraire pour restreindre les URL qui correspondent à l’itinéraire. La gamme de produit modifiée dans la liste 3 contient une contrainte d’expression régulière qui correspond uniquement à des entiers.

**La liste 3 - Global.asax.cs**

[!code-csharp[Main](creating-a-route-constraint-cs/samples/sample3.cs)]

L’expression régulière \d+ correspond à un ou plusieurs des entiers. Cette contrainte entraîne la gamme de produits faire correspondre les URL suivantes :

- / Produit/3
- / Produits/8999

Mais pas les URL suivantes :

- / Produits/apple
- / Produit

- Ces demandes du navigateur seront gérées par un autre itinéraire ou, si aucun itinéraire correspondant, un *Impossible de trouver la ressource* erreur est renvoyée.

>[!div class="step-by-step"]
[Précédent](creating-custom-routes-cs.md)
[Suivant](creating-a-custom-route-constraint-cs.md)

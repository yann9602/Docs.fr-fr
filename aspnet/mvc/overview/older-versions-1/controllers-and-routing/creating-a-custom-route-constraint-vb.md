---
uid: mvc/overview/older-versions-1/controllers-and-routing/creating-a-custom-route-constraint-vb
title: "Création d’une contrainte d’itinéraire de personnalisée (VB) | Documents Microsoft"
author: StephenWalther
description: "Stephen Walther montre comment vous pouvez créer une contrainte d’itinéraire personnalisées. Nous implémentons un simple contrainte personnalisé qui empêche un itinéraire mis en correspondance w..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/16/2009
ms.topic: article
ms.assetid: 892edb27-1cc2-4eaf-8314-dbc2efc6228a
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/creating-a-custom-route-constraint-vb
msc.type: authoredcontent
ms.openlocfilehash: 41b04c1fea267e7ee9f8a0b1c2f0d4fe4bb96d15
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-custom-route-constraint-vb"></a>Création d’une contrainte d’itinéraire de personnalisée (VB)
====================
par [Stephen Walther](https://github.com/StephenWalther)

> Stephen Walther montre comment vous pouvez créer une contrainte d’itinéraire personnalisées. Nous implémentons une contrainte personnalisée simple qui empêche un itinéraire à partir de la mise en correspondance lors d’une demande de navigateur à partir d’un ordinateur distant.


L’objectif de ce didacticiel est de montrer comment vous pouvez créer une contrainte d’itinéraire personnalisée. Une contrainte d’itinéraire personnalisée vous permet de vous permet d’empêcher un itinéraire à partir de la mise en correspondance, sauf si une condition personnalisée est mis en correspondance.

Dans ce didacticiel, nous créer une contrainte d’itinéraire Localhost. La contrainte d’itinéraire Localhost correspond uniquement aux demandes effectuées à partir de l’ordinateur local. Requêtes distantes à partir de sur Internet ne correspondent pas.

Vous implémentez une contrainte d’itinéraire personnalisée en implémentant l’interface IRouteConstraint. Il s’agit d’une interface très simple qui décrit une méthode unique :

[!code-vb[Main](creating-a-custom-route-constraint-vb/samples/sample1.vb)]

La méthode retourne une valeur booléenne. Si vous retournez la valeur False, l’itinéraire associé à la contrainte ne correspond à la demande du navigateur.

La contrainte de Localhost est contenue dans la liste 1.

**La liste 1 - LocalhostConstraint.vb**

[!code-vb[Main](creating-a-custom-route-constraint-vb/samples/sample2.vb)]

La contrainte dans la liste 1 tire parti de la propriété IsLocal exposée par la classe HttpRequest. Cette propriété retourne la valeur true lorsque l’adresse IP de la demande est soit 127.0.0.1 ou lorsque l’adresse IP de la demande est le même que l’adresse IP du serveur.

Vous utilisez une contrainte personnalisée au sein d’un itinéraire défini dans le fichier Global.asax. Le fichier Global.asax dans le Listing 2 utilise la contrainte de Localhost pour empêcher toute personne de demander une page d’administration, sauf si elles effectuer la demande à partir du serveur local. Par exemple, une demande de /Admin/DeleteAll échoue lors d’un serveur distant.

**Listing 2 - Global.asax**

[!code-vb[Main](creating-a-custom-route-constraint-vb/samples/sample3.vb)]

La contrainte de Localhost est utilisée dans la définition de l’itinéraire de l’administrateur. Cet itinéraire ne doit correspondre à une demande de navigateur à distance. Toutefois, sachez qu’autres itinéraires définis dans Global.asax peuvent correspondre à la même demande. Il est important de comprendre qu’une contrainte empêche un itinéraire particulier à partir d’une demande de mise en correspondance, et pas tous les itinéraires définis dans le fichier Global.asax.

Notez que l’itinéraire par défaut a été commenté à partir du fichier Global.asax dans le Listing 2. Si vous incluez l’itinéraire par défaut, l’itinéraire par défaut correspondrait demandes pour le contrôleur de l’administrateur. Dans ce cas, les utilisateurs distants peuvent toujours invoquer des actions du contrôleur Admin même si leurs demandes ne correspond à l’itinéraire de l’administrateur.

>[!div class="step-by-step"]
[Précédent](creating-a-route-constraint-vb.md)

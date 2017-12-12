---
uid: mvc/overview/older-versions-1/controllers-and-routing/creating-custom-routes-vb
title: "Création des itinéraires personnalisés (VB) | Documents Microsoft"
author: microsoft
description: "Découvrez comment ajouter des itinéraires personnalisés à une application ASP.NET MVC. Dans ce didacticiel, vous allez apprendre à modifier la table d’itinéraires par défaut dans le fichier Global.asax."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/16/2009
ms.topic: article
ms.assetid: 6ac5758b-6199-42af-adcb-21954b864951
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/creating-custom-routes-vb
msc.type: authoredcontent
ms.openlocfilehash: 3d3161bd1bf74df425d3c53873875a1abcfbfa05
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-custom-routes-vb"></a>Création des itinéraires personnalisés (VB)
====================
par [Microsoft](https://github.com/microsoft)

> Découvrez comment ajouter des itinéraires personnalisés à une application ASP.NET MVC. Dans ce didacticiel, vous allez apprendre à modifier la table d’itinéraires par défaut dans le fichier Global.asax.


Dans ce didacticiel, vous allez apprendre à ajouter un itinéraire personnalisé à une application ASP.NET MVC. Vous apprenez à modifier la table d’itinéraires par défaut dans le fichier Global.asax avec un itinéraire personnalisé.

Dans les applications ASP.NET MVC, la table d’itinéraires par défaut fonctionne parfaitement. Toutefois, vous pouvez découvrir que vous avez routage des besoins spécifiques. Dans ce cas, vous pouvez créer un itinéraire personnalisé.

Par exemple, imaginez que vous créez une application de blog. Vous pouvez souhaiter gérer les demandes entrantes ressemblent à ceci :

/ Archive/12-25-2009

Lorsqu’un utilisateur entre cette demande, vous souhaitez renvoyer l’entrée de blog qui correspond à la date 12/25/2009. Afin de gérer ce type de requête, vous devez créer un itinéraire personnalisé.

Le fichier Global.asax dans la liste 1 contient un nouvel itinéraire personnalisé, nommé Blog, qui traite les requêtes qui ressemblent à /Archive/*date d’entrée*.

**La liste 1 - Global.asax (avec itinéraire personnalisé)**

[!code-vb[Main](creating-custom-routes-vb/samples/sample1.vb)]

L’ordre des itinéraires que vous ajoutez à la table de routage est important. Notre nouvel itinéraire Blog personnalisé est ajouté avant l’itinéraire par défaut existant. Si vous le l’ordre inverse, puis l’itinéraire par défaut est toujours appelé au lieu de l’itinéraire personnalisé.

L’itinéraire de Blog personnalisée correspond à toute demande qui commence par/archivage. Par conséquent, il correspond à toutes les URL suivantes :

- / Archive/12-25-2009

- / Archive/10-6-2004

- / Archive/apple

L’itinéraire personnalisé mappe la demande entrante à un contrôleur nommé Archive et appelle l’action Entry(). Lorsque la méthode Entry() est appelée, la date d’entrée est passée comme un paramètre nommé entryDate.

Vous pouvez utiliser l’itinéraire personnalisé Blog avec le contrôleur dans la liste 2.

**Listing 2 - ArchiveController.vb**

[!code-vb[Main](creating-custom-routes-vb/samples/sample2.vb)]

Notez que la méthode Entry() dans la liste 2 accepte un paramètre de type DateTime. L’infrastructure MVC est assez intelligente pour convertir automatiquement la date d’entrée à partir de l’URL en une valeur DateTime. Si le paramètre de date d’entrée à partir de l’URL ne peut pas être converti en une valeur DateTime, une erreur est générée (voir Figure 1).

**Figure 1 : erreur de conversion de paramètre**


[![La boîte de dialogue Nouveau projet](creating-custom-routes-vb/_static/image1.jpg)](creating-custom-routes-vb/_static/image1.png)

**Figure 01**: erreur de conversion de paramètre ([cliquez pour afficher l’image en taille réelle](creating-custom-routes-vb/_static/image2.png))


## <a name="summary"></a>Résumé

L’objectif de ce didacticiel a pour illustrer comment vous pouvez créer un itinéraire personnalisé. Vous avez appris comment ajouter un itinéraire personnalisé à la table de routage dans le fichier Global.asax qui représente les entrées de blog. Nous avons expliqué comment mapper des demandes d’entrées de blog à un contrôleur nommé ArchiveController et une action de contrôleur nommé Entry().

>[!div class="step-by-step"]
[Précédent](asp-net-mvc-controller-overview-vb.md)
[Suivant](creating-a-route-constraint-vb.md)

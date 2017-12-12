---
uid: web-forms/overview/ajax-control-toolkit/numericupdown/creating-a-numeric-up-down-control-with-a-web-service-backend-vb
title: "Création d’une valeur numérique contrôle NumericUpDown avec un principal de Service Web (VB) | Documents Microsoft"
author: wenz
description: "Au lieu de laisser un utilisateur de taper une valeur dans une case à cocher, contrôle (qui existe sur Windows et d’autres systèmes d’exploitation) vers le haut/bas numérique peut s’avérer plus c..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: afa59dfa-fef1-43d3-8fdd-aea3be36ed3c
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/numericupdown/creating-a-numeric-up-down-control-with-a-web-service-backend-vb
msc.type: authoredcontent
ms.openlocfilehash: 5ceefd6c18761c2abe3f3a4298d340642a0951d6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-numeric-updown-control-with-a-web-service-backend-vb"></a>Création de contrôle vers le haut/bas numérique avec un principal de Service Web (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/numericupdown1.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/numericupdown1VB.pdf)

> Au lieu de laisser un utilisateur de taper une valeur dans une case à cocher, une valeur numérique haut/bas contrôle (qui existe sur Windows et d’autres systèmes d’exploitation) peut s’avérer plus à l’aise. Par défaut, le contrôle NumericUpDown toujours augmente ou diminue une valeur de 1, mais un service web s’avère plus de souplesse.


## <a name="overview"></a>Vue d'ensemble

Au lieu de laisser un utilisateur de taper une valeur dans une case à cocher, une valeur numérique haut/bas contrôle (qui existe sur Windows et d’autres systèmes d’exploitation) peut s’avérer plus à l’aise. Par défaut, le `NumericUpDown` contrôle toujours augmente ou diminue une valeur de 1, mais un service web s’avère plus de souplesse.

## <a name="steps"></a>Étapes

Les outils de contrôle ASP.NET AJAX contient le `NumericUpDown` extendeur qui ajoute automatiquement les deux boutons à une zone de texte : une pour augmenter sa valeur, une pour diminuer. Toutefois le contrôle prend également en charge un appel de service web (ou l’appel de méthode de page). Chaque fois que le haut ou vers le bas du bouton est activé, le code JavaScript de code se connecte au serveur web et exécute une méthode il. La signature de méthode est le suivant :

[!code-vb[Main](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/samples/sample1.vb)]

Le `current` argument est la valeur actuelle dans la zone de texte ; le `tag` attribut est de données de contexte supplémentaire qui peuvent être définies en tant que propriété de la `NumericUpDown` extendeur (mais n’est pas obligatoire).

Pour cet exemple, numérique contrôle NumericUpDown doit autoriser uniquement les valeurs qui sont des puissances de deux : 1, 2, 4, 8, 16, 32, 64 et ainsi de suite. Par conséquent, la méthode d’exécution lorsque l’utilisateur souhaite augmenter la valeur doit double l’ancienne valeur ; l’autre méthode doit diviser la valeur par deux. Voici donc le service web complète :

[!code-aspx[Main](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/samples/sample2.aspx)]

Enfin, créez une nouvelle page ASP.NET. Comme d’habitude, vous devez un `ScriptManager` (contrôle), un `TextBox` contrôle et un `NumericUpDownExtender` contrôle. Dans le second cas, vous devez fournir les informations du service web :

- `ServiceDownMethod`nom de vers le bas méthode web ou de page (méthode)
- `ServiceDownPath`chemin d’accès au service web avec la méthode de service bas ; omettre si vous utilisez une méthode de page
- `ServiceUpMethod`nom de la distance méthode web ou de page (méthode)
- `ServiceUpPath`chemin d’accès au service web avec la méthode de service haut ; omettre si vous utilisez une méthode de page

Voici le balisage complet pour la page :

[!code-aspx[Main](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/samples/sample3.aspx)]

Si vous exécutez la page, notez la façon dont la valeur dans la zone de texte double toujours lorsque vous cliquez sur le bouton du haut, êtes divisée en deux lorsque vous cliquez sur le bouton du bas.


[![Seuls les nombres sont une puissance de 2 s’affichent.](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/_static/image2.png)](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/_static/image1.png)

Seuls les nombres sont une puissance de 2 apparaissent ([cliquez pour afficher l’image en taille réelle](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](creating-a-numeric-up-down-control-with-a-web-service-backend-cs.md)

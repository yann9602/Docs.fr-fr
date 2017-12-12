---
uid: web-forms/overview/ajax-control-toolkit/dropshadow/adjusting-the-z-index-of-a-dropshadow-vb
title: "Ajustement de l’Index Z d’un DropShadow (VB) | Documents Microsoft"
author: wenz
description: "Le contrôle dans la boîte à outils de contrôle AJAX DropShadow étend un panneau avec une ombre portée. Toutefois cette ombre parfois est en conflit avec d’autres contrôles, d’insta..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: ecb004b5-82c0-44fb-bcaf-233fffac6195
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/dropshadow/adjusting-the-z-index-of-a-dropshadow-vb
msc.type: authoredcontent
ms.openlocfilehash: 844ea00c2ef1c974aa72c7dd627819b0429d612e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adjusting-the-z-index-of-a-dropshadow-vb"></a>Ajustement de l’Index Z d’un DropShadow (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/5/1/6/51652a81-500b-4f6b-88d3-617103e7941e/DropShadow1.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dropshadow1VB.pdf)

> Le contrôle dans la boîte à outils de contrôle AJAX DropShadow étend un panneau avec une ombre portée. Toutefois cette ombre parfois entre en conflit avec d’autres contrôles, par exemple le contrôle Menu ASP.NET. Lorsqu’une entrée de menu s’affiche, il apparaît derrière l’ombre.


## <a name="overview"></a>Vue d'ensemble

Le contrôle dans la boîte à outils de contrôle AJAX DropShadow étend un panneau avec une ombre portée. Toutefois cette ombre parfois entre en conflit avec d’autres contrôles, par exemple le contrôle Menu ASP.NET. Lorsqu’une entrée de menu s’affiche, il apparaît derrière l’ombre.

## <a name="steps"></a>Étapes

Le code commence avec le panneau de configuration lui-même, contenant suffisamment de texte afin que le panneau de configuration contient suffisamment de texte pour l’effet soit visible :

[!code-aspx[Main](adjusting-the-z-index-of-a-dropshadow-vb/samples/sample1.aspx)]

Un autre panneau est placé directement avant la `panelShadow` Panneau de configuration. Il contient un menu avec l’orientation horizontale afin que les entrées de menu s’affiche au-dessus (ou plutôt : sous) le `dropShadow` Panneau de configuration) :

[!code-aspx[Main](adjusting-the-z-index-of-a-dropshadow-vb/samples/sample2.aspx)]

Ensuite, la `DropShadowExtender` est ajouté pour étendre le `panelShadow` Panneau de configuration avec un effet d’ombre portée :

[!code-aspx[Main](adjusting-the-z-index-of-a-dropshadow-vb/samples/sample3.aspx)]

Enfin, ASP.NET AJAX `ScriptManager` contrôle permet de la boîte à outils de contrôle à utiliser :

[!code-aspx[Main](adjusting-the-z-index-of-a-dropshadow-vb/samples/sample4.aspx)]

Lorsque vous exécutez ce script, les entrées de menu s’affichent sous le panneau de configuration. Toutefois le menu utilise la classe CSS `panel` où vous devez définir deux choses à faire apparaître devant l’autre panneau les éléments :

- Positionnement relatif
- Un z-index positif

[!code-css[Main](adjusting-the-z-index-of-a-dropshadow-vb/samples/sample5.css)]

Ensuite, la `DropShadowExtender` contrôle ne sont pas en conflit plus avec le contrôle de Menu.


[![Avant : L’entrée de menu n’est pas visible](adjusting-the-z-index-of-a-dropshadow-vb/_static/image2.png)](adjusting-the-z-index-of-a-dropshadow-vb/_static/image1.png)

Avant : L’entrée de menu n’est pas visible ([cliquez pour afficher l’image en taille réelle](adjusting-the-z-index-of-a-dropshadow-vb/_static/image3.png))


[![Après : L’entrée de menu s’affiche](adjusting-the-z-index-of-a-dropshadow-vb/_static/image5.png)](adjusting-the-z-index-of-a-dropshadow-vb/_static/image4.png)

Après : L’entrée de menu s’affiche ([cliquez pour afficher l’image en taille réelle](adjusting-the-z-index-of-a-dropshadow-vb/_static/image6.png))

>[!div class="step-by-step"]
[Précédent](manipulating-dropshadow-properties-from-client-code-cs.md)
[Suivant](manipulating-dropshadow-properties-from-client-code-vb.md)

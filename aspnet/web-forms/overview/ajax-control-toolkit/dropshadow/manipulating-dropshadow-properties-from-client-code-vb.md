---
uid: web-forms/overview/ajax-control-toolkit/dropshadow/manipulating-dropshadow-properties-from-client-code-vb
title: "Manipulation des propriétés DropShadow à partir du Code Client (VB) | Documents Microsoft"
author: wenz
description: "Le contrôle dans la boîte à outils de contrôle AJAX DropShadow étend un panneau avec une ombre portée. Propriétés de cette extension peuvent également être modifiées à l’aide du client JavaScrip..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 11be4211-2fb9-4e15-b6d4-2aa623d81f3e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/dropshadow/manipulating-dropshadow-properties-from-client-code-vb
msc.type: authoredcontent
ms.openlocfilehash: 706ccb5a95873aad4c1b9e0942ab06cf4162710a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="manipulating-dropshadow-properties-from-client-code-vb"></a>Manipulation des propriétés DropShadow à partir du Code Client (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/5/1/6/51652a81-500b-4f6b-88d3-617103e7941e/DropShadow2.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dropshadow2VB.pdf)

> Le contrôle dans la boîte à outils de contrôle AJAX DropShadow étend un panneau avec une ombre portée. Propriétés de cette extension peuvent également être modifiées à l’aide du code JavaScript client.


## <a name="overview"></a>Vue d'ensemble

Le contrôle dans la boîte à outils de contrôle AJAX DropShadow étend un panneau avec une ombre portée. Propriétés de cette extension peuvent également être modifiées à l’aide du code JavaScript client.

## <a name="steps"></a>Étapes

Le code commence par un panneau de configuration contenant des lignes de texte :

[!code-aspx[Main](manipulating-dropshadow-properties-from-client-code-vb/samples/sample1.aspx)]

La classe CSS associée donne le panneau de configuration une couleur d’arrière-plan intéressante :

[!code-css[Main](manipulating-dropshadow-properties-from-client-code-vb/samples/sample2.css)]

Le `DropShadowExtender` est ajouté pour étendre le panneau de configuration avec un effet d’ombre portée, opacité définie à 50 % :

[!code-aspx[Main](manipulating-dropshadow-properties-from-client-code-vb/samples/sample3.aspx)]

Ensuite, ASP.NET AJAX `ScriptManager` contrôle permet de la boîte à outils de contrôle à utiliser :

[!code-aspx[Main](manipulating-dropshadow-properties-from-client-code-vb/samples/sample4.aspx)]

Un autre panneau contient deux liens JavaScript pour définir l’opacité de l’ombre : le lien moins diminue l’opacité de l’ombre, le lien plu l’augmente.

[!code-aspx[Main](manipulating-dropshadow-properties-from-client-code-vb/samples/sample5.aspx)]

La fonction JavaScript `changeOpacity()` doit tout d’abord recherchez le `DropShadowExtender` contrôle sur la page. ASP.NET AJAX définit le `$find()` méthode pour exactement cette tâche. Ensuite, la `get_Opacity()` méthode extrait l’opacité en cours, le `set_Opacity()` lui affecte la méthode. Le code JavaScript puis place la valeur d’opacité actuelle dans le `<label>` élément :

[!code-html[Main](manipulating-dropshadow-properties-from-client-code-vb/samples/sample6.html)]


[![L’opacité est modifiée du côté client](manipulating-dropshadow-properties-from-client-code-vb/_static/image2.png)](manipulating-dropshadow-properties-from-client-code-vb/_static/image1.png)

L’opacité est modifiée du côté client ([cliquez pour afficher l’image en taille réelle](manipulating-dropshadow-properties-from-client-code-vb/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](adjusting-the-z-index-of-a-dropshadow-vb.md)

---
uid: web-forms/overview/ajax-control-toolkit/popup/using-multiple-popup-controls-cs
title: "À l’aide de plusieurs contrôles de fenêtre contextuelle (c#) | Documents Microsoft"
author: wenz
description: "L’extendeur PopupControl dans la boîte à outils de contrôle AJAX offre un moyen simple de déclencher une fenêtre contextuelle lorsque n’importe quel autre contrôle est activé. Il est également possible d’utiliser m..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 91511b0b-311d-481f-9e7c-73f07b813b79
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/popup/using-multiple-popup-controls-cs
msc.type: authoredcontent
ms.openlocfilehash: d8c9742bb39b655115cb1862ff6e867989e63665
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-multiple-popup-controls-c"></a>À l’aide de plusieurs contrôles de fenêtre contextuelle (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/PopupControl1.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/popupcontrol1CS.pdf)

> L’extendeur PopupControl dans la boîte à outils de contrôle AJAX offre un moyen simple de déclencher une fenêtre contextuelle lorsque n’importe quel autre contrôle est activé. Il est également possible d’utiliser plus d’un contrôle de menu contextuel sur une page.


## <a name="overview"></a>Vue d'ensemble

L’extendeur PopupControl dans la boîte à outils de contrôle AJAX offre un moyen simple de déclencher une fenêtre contextuelle lorsque n’importe quel autre contrôle est activé. Il est également possible d’utiliser plus d’un contrôle de menu contextuel sur une page.

## <a name="steps"></a>Étapes

Pour activer la fonctionnalité d’ASP.NET AJAX et les outils de contrôle, le `ScriptManager` contrôle doit être placé n’importe où sur la page (mais dans le `<form>` élément) :

[!code-aspx[Main](using-multiple-popup-controls-cs/samples/sample1.aspx)]

Ensuite, ajoutez un panneau qui sert de la fenêtre contextuelle. Dans le scénario en cours, le panneau de configuration contient un `Calendar` contrôle. Afin d’éviter l’actualisation de la page a provoqué par les publications du calendrier, le panneau de configuration est placé dans un `UpdatePanel` contrôle :

[!code-aspx[Main](using-multiple-popup-controls-cs/samples/sample2.aspx)]

La page contient également les deux zones de texte. Chaque zone de texte, le menu contextuel du calendrier apparaît une fois que la zone de texte est activée.

[!code-aspx[Main](using-multiple-popup-controls-cs/samples/sample3.aspx)]

À présent étendre les deux zones de texte avec un `PopupControlExtender`. Le `TargetControlID` attribut fournit l’ID du contrôle lié à l’extendeur. Le `PopupControlID` attribut contient l’ID du Panneau de la fenêtre contextuelle. Dans ce cas, les deux extendeurs affichent le même volet, mais différents panneaux est également possibles.

[!code-aspx[Main](using-multiple-popup-controls-cs/samples/sample4.aspx)]

Maintenant lorsque vous cliquez dans un champ de texte, un calendrier s’affiche sous le champ, ce qui vous permet de sélectionner une date. (Pour obtenir la date sélectionnée dans les zones de texte est abordée dans un autre didacticiel.)


[![Le calendrier s’affiche lorsque l’utilisateur clique dans la zone de texte](using-multiple-popup-controls-cs/_static/image2.png)](using-multiple-popup-controls-cs/_static/image1.png)

Le calendrier s’affiche lorsque l’utilisateur clique dans la zone de texte ([cliquez pour afficher l’image en taille réelle](using-multiple-popup-controls-cs/_static/image3.png))

>[!div class="step-by-step"]
[Next](handling-postbacks-from-a-popup-control-with-an-updatepanel-cs.md)

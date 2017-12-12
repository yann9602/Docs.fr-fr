---
uid: web-forms/overview/ajax-control-toolkit/animation/animating-an-updatepanel-control-cs
title: "Animer un contrôle UpdatePanel (c#) | Documents Microsoft"
author: wenz
description: "Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Pour le contenu d’un..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: e57f8c7c-3940-4bc0-9468-3a0ca69158ea
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/animating-an-updatepanel-control-cs
msc.type: authoredcontent
ms.openlocfilehash: 7e6d8954d2ec886994cdd723121e540b471131f6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="animating-an-updatepanel-control-c"></a>Animer un contrôle UpdatePanel (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/UpdatePanelAnimation1.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/updatepanelanimation1CS.pdf)

> Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Pour le contenu de UpdatePanel, un extendeur spécial existe qui s’appuie fortement sur le framework d’animation : UpdatePanelAnimation. Ce didacticiel montre comment configurer une animation de ce type pour un UpdatePanel.


## <a name="overview"></a>Vue d'ensemble

Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Pour le contenu d’un `UpdatePanel`, un extendeur spécial existe qui s’appuie fortement sur le framework d’animation : `UpdatePanelAnimation`. Ce didacticiel montre comment configurer ce type d’une animation pour un `UpdatePanel`.

## <a name="steps"></a>Étapes

La première étape consiste comme d’habitude pour inclure la `ScriptManager` dans la page afin que la bibliothèque ASP.NET AJAX est chargée et les outils de contrôle peut être utilisé :

[!code-aspx[Main](animating-an-updatepanel-control-cs/samples/sample1.aspx)]

L’animation dans ce scénario s’appliqueront à ASP.NET `Wizard` contrôle web résidant dans un `UpdatePanel`. Trois étapes (arbitraires) fournissent suffisamment options pour déclencher des publications (postback) :

[!code-aspx[Main](animating-an-updatepanel-control-cs/samples/sample2.aspx)]

Le balisage nécessaire pour le `UpdatePanelAnimationExtender` contrôle est très semblable à celui utilisé pour le `AnimationExtender`. Dans le `TargetControlID` attribut nous fournir le `ID` de la `UpdatePanel` pour animer ; dans le `UpdatePanelAnimationExtender` (contrôle), le `<Animations>` élément contient le balisage XML pour les ou les animations. Toutefois, il existe une différence : la quantité d’événements et gestionnaires d’événements est limitée par rapport à `AnimationExtender`. Pour `UpdatePanels`, seulement deux d'entre elles existent :

- `<OnUpdated>`Lorsque le contrôle UpdatePanel a été mis à jour
- `<OnUpdating>`UpdatePanel démarrage de la mise à jour

Dans ce scénario, le contenu de la `UpdatePanel` (après la publication) est en fondu. Il s’agit de la balise nécessaire pour que :

[!code-aspx[Main](animating-an-updatepanel-control-cs/samples/sample3.aspx)]

Désormais chaque fois qu’une publication (postback) se produit dans le contrôle UpdatePanel, le nouveau contenu dans le panneau de configuration Fondu léger.


[![L’atténuation de l’étape suivante de l’Assistant](animating-an-updatepanel-control-cs/_static/image2.png)](animating-an-updatepanel-control-cs/_static/image1.png)

L’étape suivante de l’Assistant est du fondu ([cliquez pour afficher l’image en taille réelle](animating-an-updatepanel-control-cs/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](changing-an-animation-using-client-side-code-cs.md)
[Suivant](dynamically-controlling-updatepanel-animations-cs.md)

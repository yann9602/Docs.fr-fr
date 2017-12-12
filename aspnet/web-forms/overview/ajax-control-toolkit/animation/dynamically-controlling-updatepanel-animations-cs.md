---
uid: web-forms/overview/ajax-control-toolkit/animation/dynamically-controlling-updatepanel-animations-cs
title: "Contrôle dynamique des Animations de UpdatePanel (c#) | Documents Microsoft"
author: wenz
description: "Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Pour le contenu d’un..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 5138b8fe-98ff-4e73-a00b-e263fc3ff11d
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/dynamically-controlling-updatepanel-animations-cs
msc.type: authoredcontent
ms.openlocfilehash: 0408556e322a46211388fbd557d7a967044129ef
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="dynamically-controlling-updatepanel-animations-c"></a>Contrôle dynamique des Animations de UpdatePanel (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/UpdatePanelAnimation2.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/updatepanelanimation2CS.pdf)

> Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Pour le contenu de UpdatePanel, un extendeur spécial existe qui s’appuie fortement sur le framework d’animation : UpdatePanelAnimation. Il peut également fonctionner avec des déclencheurs de UpdatePanel.


## <a name="overview"></a>Vue d'ensemble

Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Pour le contenu d’un `UpdatePanel`, un extendeur spécial existe qui s’appuie fortement sur le framework d’animation : `UpdatePanelAnimation`. Il peut également fonctionner conjointement avec `UpdatePanel` déclencheurs.

## <a name="steps"></a>Étapes

La première étape consiste comme d’habitude pour inclure la `ScriptManager` dans la page afin que la bibliothèque ASP.NET AJAX est chargée et les outils de contrôle peut être utilisé :


[!code-aspx[Main](dynamically-controlling-updatepanel-animations-cs/samples/sample1.aspx)]

L’animation dans ce scénario sera appliquée à un affichage de l’heure actuelle. Ces informations peuvent être écrites dans une étiquette à l’aide de la `Page_Load()` (méthode), ou (par souci de simplicité) le code inline suivant est utilisé :


[!code-aspx[Main](dynamically-controlling-updatepanel-animations-cs/samples/sample2.aspx)]

En outre, un bouton pour déclencher la mise à jour de l’heure est créé :


[!code-aspx[Main](dynamically-controlling-updatepanel-animations-cs/samples/sample3.aspx)]

Ce code est ensuite placé dans le `<ContentTemplate>` section d’un `UpdatePanel` élément. Du panneau `UpdateMode` attribut doit être défini sur `"Conditional"`, car seuls les déclencheurs peuvent mettre à jour le contenu du panneau. Dans le `<Triggers>` section de la `UpdatePanel`, un déclencheur de publication (postback) asynchrone est créé et lié à la `Click` événements du bouton. Par conséquent, si l’utilisateur clique sur le bouton, le `UpdatePanel` est actualisé. Voici le balisage de la `UpdatePanel` contrôle :


[!code-aspx[Main](dynamically-controlling-updatepanel-animations-cs/samples/sample4.aspx)]

Enfin, le `UpdatePanelAnimationExtender` doit être configuré : définir le `TargetControlID` d’attribut à l’ID du panneau et définir une animation au sein de l’extendeur. Effet de fondu rend sens, ce qui crée une jolie visual mettant l’accent sur l’heure de mise à jour. Votre balisage d’extendeur peut ensuite ressembler à ceci :


[!code-aspx[Main](dynamically-controlling-updatepanel-animations-cs/samples/sample5.aspx)]

Exécutez le fichier dans le navigateur. Lorsque vous cliquez sur le bouton, l’heure actuelle est affichée dans le panneau de configuration, toujours du fondu pour la durée d’une seconde.


[![L’heure actuelle est du fondu](dynamically-controlling-updatepanel-animations-cs/_static/image2.png)](dynamically-controlling-updatepanel-animations-cs/_static/image1.png)

L’heure actuelle est du fondu ([cliquez pour afficher l’image en taille réelle](dynamically-controlling-updatepanel-animations-cs/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](animating-an-updatepanel-control-cs.md)
[Suivant](adding-animation-to-a-control-vb.md)

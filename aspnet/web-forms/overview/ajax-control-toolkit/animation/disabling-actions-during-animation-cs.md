---
uid: web-forms/overview/ajax-control-toolkit/animation/disabling-actions-during-animation-cs
title: "La désactivation des Actions au cours de l’Animation (c#) | Documents Microsoft"
author: wenz
description: "Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Il prend également en charge d’action en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 918026b4-2f63-421d-8546-df12856960a8
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/disabling-actions-during-animation-cs
msc.type: authoredcontent
ms.openlocfilehash: 875c6be5e190c31fac030fc17ef040a934233f16
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="disabling-actions-during-animation-c"></a>La désactivation des Actions au cours de l’Animation (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation7.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation7CS.pdf)

> Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Il prend également en charge les actions, telles que les clics de souris. Toutefois lorsqu’un clic de souris démarre une animation, il est souhaitable de désactiver les clics de souris lors de l’animation.


## <a name="overview"></a>Vue d'ensemble

Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Il prend également en charge les actions, telles que les clics de souris. Toutefois lorsqu’un clic de souris démarre une animation, il est souhaitable de désactiver les clics de souris lors de l’animation.

## <a name="steps"></a>Étapes

Tout d’abord, incluez le `ScriptManager` dans la page ; ensuite, la bibliothèque ASP.NET AJAX est chargée, ce qui permet d’utiliser les outils de contrôle :

[!code-aspx[Main](disabling-actions-during-animation-cs/samples/sample1.aspx)]

L’animation sera appliquée à un bouton HTML comme suit :

[!code-aspx[Main](disabling-actions-during-animation-cs/samples/sample2.aspx)]

Notez qu’un contrôle HTML est utilisé au lieu d’un contrôle Web dans la mesure où nous ne souhaitez pas que le bouton pour créer une publication (postback) ; Il est simplement lancer l’animation côté client pour nous.

Ensuite, ajoutez le `AnimationExtender` à la page, en fournissant une `ID`, le `TargetControlID` attribut et le texte obligatoire `runat="server"`:

[!code-aspx[Main](disabling-actions-during-animation-cs/samples/sample3.aspx)]

Dans le `<Animations>` nœud, `<OnClick>` est l’élément approprié à gérer le clic de souris. Toutefois, le bouton peut être activé lors de l’animation. Le `<EnableAction>` peut prendre en charge de cet élément. Paramètre `Enabled="false"` désactive le bouton dans le cadre de l’animation. Étant donné que nous utilisons plusieurs animations individuelles (désactiver le bouton et les animations réelles), le `<Parallel>` élément est requis pour coller les animations uniques ensemble en une seule. Voici le balisage complet pour `AnimationExtender`:

[!code-aspx[Main](disabling-actions-during-animation-cs/samples/sample4.aspx)]

Il serait également possible d’activer de nouveau au bouton après l’animation, à l’aide de l’élément XML suivant à la fin de la liste :

[!code-xml[Main](disabling-actions-during-animation-cs/samples/sample5.xml)]

Toutefois dans le scénario fourni cela serait inutile puisque le bouton Fondu et n’est pas visible à la fin de l’animation.


[![Le bouton est désactivé, dès que l’animation s’exécute.](disabling-actions-during-animation-cs/_static/image2.png)](disabling-actions-during-animation-cs/_static/image1.png)

Le bouton est désactivé, dès que l’animation s’exécute ([cliquez pour afficher l’image en taille réelle](disabling-actions-during-animation-cs/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](animating-in-response-to-user-interaction-cs.md)
[Suivant](triggering-an-animation-in-another-control-cs.md)

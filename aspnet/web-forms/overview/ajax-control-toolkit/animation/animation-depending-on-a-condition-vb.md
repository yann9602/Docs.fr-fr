---
uid: web-forms/overview/ajax-control-toolkit/animation/animation-depending-on-a-condition-vb
title: Animation selon une Condition (VB) | Documents Microsoft
author: wenz
description: "Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Si une animation est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 1b87d8d6-b3f7-4126-b51c-d41442fbf947
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/animation-depending-on-a-condition-vb
msc.type: authoredcontent
ms.openlocfilehash: cc8600f33f9c27e1045f5083a126b9d2d1e90303
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="animation-depending-on-a-condition-vb"></a>Animation selon une Condition (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation4.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation4VB.pdf)

> Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Si une animation est exécutée ou non peut également dépendre une condition sous forme d’un code JavaScript.


## <a name="overview"></a>Vue d'ensemble

Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. Si une animation est exécutée ou non peut également dépendre une condition sous forme d’un code JavaScript.

## <a name="steps"></a>Étapes

Tout d’abord, incluez le `ScriptManager` dans la page ; ensuite, la bibliothèque ASP.NET AJAX est chargée, ce qui permet d’utiliser les outils de contrôle :

[!code-aspx[Main](animation-depending-on-a-condition-vb/samples/sample1.aspx)]

L’animation sera appliquée à un volet de texte qui ressemble à ceci :

[!code-aspx[Main](animation-depending-on-a-condition-vb/samples/sample2.aspx)]

Dans la classe CSS associée pour le panneau de configuration, définir une couleur d’arrière-plan agréable et également définir une largeur fixe pour le panneau de configuration :

[!code-css[Main](animation-depending-on-a-condition-vb/samples/sample3.css)]

Ensuite, ajoutez le `AnimationExtender` à la page, en fournissant une `ID`, le `TargetControlID` attribut et le texte obligatoire`runat="server":`

[!code-aspx[Main](animation-depending-on-a-condition-vb/samples/sample4.aspx)]

Dans le `<Animations>` nœud, utilisez `<OnLoad>` pour exécuter les animations, une fois que la page a été complètement chargée. Au lieu d’un des animations régulières, le `<Condition>` élément entre en jeu. Le code JavaScript fourni comme valeur de la `ConditionScript` attribut est exécuté lors de l’exécution. Si sa valeur est true, l’animation est exécutée, sinon pas. Le balisage suivant fournit deux animations, chacun d’eux en cours d’exécution de 50 % des cas à aléatoire. Dans la mesure où il ne peut exister qu’une animation dans `<OnLoad>`, les deux `<Condition>` animations sont jointes à l’aide de la `<Sequence>` élément :

[!code-aspx[Main](animation-depending-on-a-condition-vb/samples/sample5.aspx)]

Notez que le signe inférieur à (`<`) dans le `ConditionScript` attribut doit être la séquence d’échappement (). Lorsque vous exécutez ce script, aucune animation s’exécute, ou l’un des deux n’ou tous deux faire.


[![Le panneau est fondu sans redimensionnement, donc n’a pas de la deuxième animation s’exécute l'](animation-depending-on-a-condition-vb/_static/image2.png)](animation-depending-on-a-condition-vb/_static/image1.png)

Le panneau est fondu sans redimensionnement, donc n’a pas de la deuxième animation s’exécute le ([cliquez pour afficher l’image en taille réelle](animation-depending-on-a-condition-vb/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](executing-several-animations-after-each-other-vb.md)
[Suivant](picking-one-animation-out-of-a-list-vb.md)

---
uid: web-forms/overview/ajax-control-toolkit/textboxwatermark/using-textboxwatermark-with-validation-controls-cs
title: "À l’aide de TextBoxWatermark avec des contrôles de Validation (c#) | Documents Microsoft"
author: wenz
description: "Le contrôle TextBoxWatermark dans la boîte à outils de contrôle AJAX étend une zone de texte afin qu’un texte s’affiche dans la zone. Lorsqu’un utilisateur clique dans la zone, il vous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: d49940cb-d38c-456a-b800-5f0eb705d09f
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/textboxwatermark/using-textboxwatermark-with-validation-controls-cs
msc.type: authoredcontent
ms.openlocfilehash: 61fa55c8c4580800de1097b7242c7077cda27115
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-textboxwatermark-with-validation-controls-c"></a>À l’aide de TextBoxWatermark avec des contrôles de Validation (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/TextBoxWatermark2.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/textboxwatermark2CS.pdf)

> Le contrôle TextBoxWatermark dans la boîte à outils de contrôle AJAX étend une zone de texte afin qu’un texte s’affiche dans la zone. Lorsqu’un utilisateur clique dans la zone, elle est vidée. Si l’utilisateur quitte la zone de sans saisie de texte, le texte prérempli s’affiche de nouveau. Il peut entrer en conflit avec les contrôles de Validation sur la même page, mais ces problèmes peuvent être surmontés.


## <a name="overview"></a>Vue d'ensemble

Le `TextBoxWatermark` contrôle dans la boîte à outils de contrôle AJAX étend une zone de texte afin qu’un texte s’affiche dans la zone. Lorsqu’un utilisateur clique dans la zone, elle est vidée. Si l’utilisateur quitte la zone de sans saisie de texte, le texte prérempli s’affiche de nouveau. Il peut entrer en conflit avec les contrôles de Validation sur la même page, mais ces problèmes peuvent être surmontés.

## <a name="steps"></a>Étapes

Le programme d’installation de base de l’exemple est le suivant : un `TextBox` contrôle apparaît à l’aide un `TextBoxWatermarkExtender` contrôle. Un bouton déclenche une publication (postback) et sera utilisé ultérieurement pour déclencher les contrôles de validation sur la page. En outre, un `ScriptManager` contrôle est requis pour initialiser ASP.NET AJAX :

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-cs/samples/sample1.aspx)]

Ajoutez maintenant un `RequiredFieldValidator` contrôle qui vérifie si le champ n’est texte quand le formulaire est envoyé. Le `InitialValue` propriété du validateur doit être définie sur la même valeur que celle qui est utilisée dans le `TextBoxWatermarkExtender` contrôle : lorsque le formulaire est envoyé, la valeur d’une zone de texte sans modification est la valeur de filigrane dans celui-ci :

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-cs/samples/sample2.aspx)]

Toutefois, il existe un problème avec cette approche : si le client désactive JavaScript, le champ de texte n’est pas donc prérempli avec le texte de filigrane, le `RequiredFieldValidator` ne déclenche pas d’un message d’erreur. Par conséquent, un deuxième `RequiredFieldValidator` contrôle est requise qui vérifie une zone de texte vide (l’omission de la `InitialValue` attribut).

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-cs/samples/sample3.aspx)]

Étant donné que les deux validateurs utilisent `Display` = `"Dynamic"`, l’utilisateur final ne peut pas distinguer l’apparence visuelle parmi les deux validateurs a été déclenché ; au lieu de cela, il semblerait qu’un seul d'entre eux.

Enfin, ajoutez du code côté serveur pour le texte dans le champ de sortie si aucun validateur n’émis un message d’erreur :

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-cs/samples/sample4.aspx)]


[![Le programme de validation indique que la taille que le champ n’est pas de texte](using-textboxwatermark-with-validation-controls-cs/_static/image2.png)](using-textboxwatermark-with-validation-controls-cs/_static/image1.png)

Le programme de validation indique que la taille que le champ n’est pas de texte ([cliquez pour afficher l’image en taille réelle](using-textboxwatermark-with-validation-controls-cs/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](using-textboxwatermark-in-a-formview-cs.md)
[Suivant](using-textboxwatermark-in-a-formview-vb.md)

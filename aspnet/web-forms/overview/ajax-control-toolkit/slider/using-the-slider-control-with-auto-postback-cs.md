---
uid: web-forms/overview/ajax-control-toolkit/slider/using-the-slider-control-with-auto-postback-cs
title: "Utilisation du contrôle de curseur avec publication automatique (c#) | Documents Microsoft"
author: wenz
description: "Le contrôle de curseur dans la boîte à outils de contrôle AJAX fournit un contrôle slider graphique qui peut être contrôlé à l’aide de la souris. Il est possible d’effectuer la comptabilisation automatique curseur..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 4d85e9fb-91e6-41f2-9c13-754549b19c27
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/slider/using-the-slider-control-with-auto-postback-cs
msc.type: authoredcontent
ms.openlocfilehash: f6291f4162b3069a6316a60b4b29f82f55121aac
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-the-slider-control-with-auto-postback-c"></a>Utilisation du contrôle de curseur avec publication automatique (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/Slider1.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/slider1CS.pdf)

> Le contrôle de curseur dans la boîte à outils de contrôle AJAX fournit un contrôle slider graphique qui peut être contrôlé à l’aide de la souris. Il est possible d’effectuer la curseur autopostback une fois sa valeur change.


## <a name="overview"></a>Vue d'ensemble

Le contrôle de curseur dans la boîte à outils de contrôle AJAX fournit un contrôle slider graphique qui peut être contrôlé à l’aide de la souris. Il est possible d’effectuer la curseur autopostback une fois sa valeur change.

## <a name="steps"></a>Étapes

Pour faciliter le curseur automatiquement publication (postback) à une modification, les deux zones de texte doivent l’attribut `AutoPostBack="true"`: la zone de texte qui deviendra le curseur lui-même et la zone de texte qui contient la position du curseur. Voici le balisage requis pour qui :

[!code-aspx[Main](using-the-slider-control-with-auto-postback-cs/samples/sample1.aspx)]

Le `SliderExtender` contrôle à partir de la boîte à outils de contrôle ASP.NET AJAX affecte les fonctionnalités de curseur à deux zones de texte :

[!code-aspx[Main](using-the-slider-control-with-auto-postback-cs/samples/sample2.aspx)]

Un élément supplémentaire de l’étiquette sera utilisé ultérieurement pour informer l’utilisateur d’une publication :

[!code-aspx[Main](using-the-slider-control-with-auto-postback-cs/samples/sample3.aspx)]

Enfin, le `ScriptManager` contrôle d’ASP.NET AJAX charge le JavaScript requis pour la boîte à outils de contrôle à utiliser :

[!code-aspx[Main](using-the-slider-control-with-auto-postback-cs/samples/sample4.aspx)]

Maintenant le curseur est publication ; sur le côté serveur, cet événement peut être intercepté et réactions :

[!code-aspx[Main](using-the-slider-control-with-auto-postback-cs/samples/sample5.aspx)]


[![En déplaçant le curseur déclenche une publication (postback)](using-the-slider-control-with-auto-postback-cs/_static/image2.png)](using-the-slider-control-with-auto-postback-cs/_static/image1.png)

En déplaçant le curseur déclenche une publication ([cliquez pour afficher l’image en taille réelle](using-the-slider-control-with-auto-postback-cs/_static/image3.png))


[![Ensuite, la date de cette modification est écrite dans l’étiquette](using-the-slider-control-with-auto-postback-cs/_static/image5.png)](using-the-slider-control-with-auto-postback-cs/_static/image4.png)

Ensuite, la date de cette modification est écrite dans l’étiquette ([cliquez pour afficher l’image en taille réelle](using-the-slider-control-with-auto-postback-cs/_static/image6.png))

>[!div class="step-by-step"]
[Next](databinding-the-slider-control-cs.md)

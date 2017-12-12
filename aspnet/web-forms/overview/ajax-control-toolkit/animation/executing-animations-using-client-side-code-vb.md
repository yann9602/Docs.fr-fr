---
uid: web-forms/overview/ajax-control-toolkit/animation/executing-animations-using-client-side-code-vb
title: "L’exécution des Animations à l’aide de Code du côté Client (VB) | Documents Microsoft"
author: wenz
description: "Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. L’exécution de l’animation en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: f7073f50-d765-456d-9957-926ce60f35f6
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/executing-animations-using-client-side-code-vb
msc.type: authoredcontent
ms.openlocfilehash: c97ce87addd566ed1ba63ccdb81206c6449f49a2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="executing-animations-using-client-side-code-vb"></a>Animations en cours d’exécution à l’aide de Code du côté Client (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation10.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation10VB.pdf)

> Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. L’exécution de l’animation peut également être déclenchée à l’aide du code JavaScript côté client personnalisé.


## <a name="overview"></a>Vue d'ensemble

Le contrôle de l’Animation dans la boîte à outils de contrôle ASP.NET AJAX n’est pas simplement un contrôle, mais une infrastructure entière pour ajouter des animations à un contrôle. L’exécution de l’animation peut également être déclenchée à l’aide du code JavaScript côté client personnalisé.

## <a name="steps"></a>Étapes

Tout d’abord, incluez le `ScriptManager` dans la page ; ensuite, la bibliothèque ASP.NET AJAX est chargée, ce qui permet d’utiliser les outils de contrôle :

[!code-aspx[Main](executing-animations-using-client-side-code-vb/samples/sample1.aspx)]

L’animation sera appliquée à un volet de texte qui ressemble à ceci :

[!code-aspx[Main](executing-animations-using-client-side-code-vb/samples/sample2.aspx)]

Dans la classe CSS associée pour le panneau de configuration, définir une couleur d’arrière-plan agréable et également définir une largeur fixe pour le panneau de configuration :

[!code-css[Main](executing-animations-using-client-side-code-vb/samples/sample3.css)]

Ensuite, ajoutez le `AnimationExtender` à la page, en fournissant une `ID`, le `TargetControlID` attribut et le texte obligatoire `runat="server"`:

[!code-aspx[Main](executing-animations-using-client-side-code-vb/samples/sample4.aspx)]

Dans le `<Animations>` nœud, utilisez `<OnClick>` pour exécuter les animations une fois l’utilisateur clique sur le panneau de configuration. Ajoutez deux animations doit être exécuté en :

[!code-xml[Main](executing-animations-using-client-side-code-vb/samples/sample5.xml)]

Pour des raisons de démonstration, cette animation (et toute autre animation créé à l’aide de la boîte à outils de contrôle) sont exécutées à l’aide de code JavaScript, une fois que la page s’exécute. Tout d’abord nous avons besoin d’accéder à la `AnimationExtender` contrôle. La bibliothèque ASP.NET AJAX fournit le `$find()` fonction pour cette tâche :

[!code-csharp[Main](executing-animations-using-client-side-code-vb/samples/sample6.cs)]

Le `AnimationExtender` contrôle expose une API riche, y compris les méthodes avec des noms identiques aux gestionnaires d’événements utilisés dans le balisage XML : `OnClick()`, `OnLoad()`, et ainsi de suite. Par exemple, un appel de la `OnClick()` méthode s’exécute l’animation dans le `<OnClick>` élément de la `AnimationExtender` contrôle :

[!code-javascript[Main](executing-animations-using-client-side-code-vb/samples/sample7.js)]

Voici le code JavaScript côté client complet qui émule le, cliquez sur le panneau de configuration une fois que la page a été entièrement chargée Notez que le `pageLoad()` nom de la fonction est utilisée, qui est appelée par ASP.NET AJAX une fois la page et tous les inclus JavaScript bibliothèques ont été chargé.

[!code-html[Main](executing-animations-using-client-side-code-vb/samples/sample8.html)]


[![L’animation s’exécute immédiatement, sans un clic de souris](executing-animations-using-client-side-code-vb/_static/image2.png)](executing-animations-using-client-side-code-vb/_static/image1.png)

L’animation s’exécute immédiatement, sans un clic de souris ([cliquez pour afficher l’image en taille réelle](executing-animations-using-client-side-code-vb/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](modifying-animations-from-the-server-side-vb.md)
[Suivant](changing-an-animation-using-client-side-code-vb.md)

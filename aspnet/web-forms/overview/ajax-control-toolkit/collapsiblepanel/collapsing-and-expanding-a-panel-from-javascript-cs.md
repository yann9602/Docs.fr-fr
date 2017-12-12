---
uid: web-forms/overview/ajax-control-toolkit/collapsiblepanel/collapsing-and-expanding-a-panel-from-javascript-cs
title: "Réduire et développer un panneau de configuration à partir de JavaScript (c#) | Documents Microsoft"
author: wenz
description: "Le contrôle de CollapsiblePanel dans ASP.NET AJAX Control Toolkit étend un panneau de configuration et lui fournit la possibilité de réduire son contenu et le développer un..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: de5500be-75e5-461a-8064-b70ae52ea6a4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/collapsiblepanel/collapsing-and-expanding-a-panel-from-javascript-cs
msc.type: authoredcontent
ms.openlocfilehash: 666f3e212ccdd5b26b466f4672134ce751dc5dd1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="collapsing-and-expanding-a-panel-from-javascript-c"></a>Réduire et développer un panneau de configuration à partir de JavaScript (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/8/a/a/8aab3c3e-de6f-463f-805c-5fda567eef6e/CollapsiblePanel1.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/collapsiblepanel1CS.pdf)

> Le contrôle de CollapsiblePanel dans ASP.NET AJAX Control Toolkit étend un panneau de configuration et lui fournit la possibilité de réduire son contenu et le développer à nouveau. Ces deux actions peuvent également être déclenchées à partir du code JavaScript personnalisé.


## <a name="overview"></a>Vue d'ensemble

Le contrôle de CollapsiblePanel dans ASP.NET AJAX Control Toolkit étend un panneau de configuration et lui fournit la possibilité de réduire son contenu et le développer à nouveau. Ces deux actions peuvent également être déclenchées à partir du code JavaScript personnalisé.

## <a name="steps"></a>Étapes

Tout d’abord, créez une page ASP.NET et inclure le `ScriptManager` les `<form>` élément. Cette opération charge la bibliothèque ASP.NET AJAX, ce qui est requis par les outils de contrôle :

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample1.aspx)]

Ensuite, créez un panneau avec du texte afin que l’effet de la commande de réduction/développement peut être affichée :

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample2.aspx)]

Comme vous pouvez le voir, le panneau de configuration fait référence à une classe CSS qui est présentée ici (et fondamentalement définit une couleur d’arrière-plan et la largeur de son) :

[!code-css[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample3.css)]

Le `CollapsiblePanelExtender` contrôle requiert le `TargetControlID` attribut afin que la boîte à outils sache quel panneau pour réduire ou développer à la demande :

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample4.aspx)]

Malheureusement, l’extendeur actuellement n’expose pas une API spécifique pour réduire ou de développer le panneau de configuration, mais certaines méthodes non documentées effectuera. Tout d’abord, ajoutez trois boutons HTML à la page, ce qui déclenchera puis le JavaScript côté client pour réduire ou développer le contenu du panneau :

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample5.aspx)]

Dans le code JavaScript côté client (main `<script type="text/javascript">`), la `$find()` méthode doit être utilisée pour accéder à la `CollapsiblePanelExtender`. `$find("cpe")`Retourne une référence à celui-ci. Ensuite, des méthodes spécifiques permettent de résoudre la tâche en cours.

La méthode d’ouverture (développement), le panneau de configuration est appelée `_doOpen()`; le code suivant implémente la `doOpen()` fonction appelée lorsque le premier bouton est activé :

[!code-javascript[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample6.js)]

Pour la fermeture ou de réduire le panneau de configuration, le `_doClose()` (méthode) doit être exécutée. Par conséquent, lorsque l’utilisateur clique sur le deuxième bouton, le code JavaScript suivant est appelé :

[!code-javascript[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample7.js)]

Le troisième bouton bascule l’état du panneau : à partir de réduite en développée et vice versa. Le `CollapsiblePanelExtender` expose le `toggle()` méthode savoir exactement : inverse l’état du panneau. Toutefois, il existe également une autre approche (qui est utilisé en interne par le `toggle()` (méthode)) : le `get_Collapsed()` méthode de la `CollapsiblePanelExtender()` indique si le panneau est réduit ou non. Selon la valeur de retour de cette fonction, le panneau de configuration est alors soit développé (`_doOpen()` méthode) ou réduite (`_doClose()`) méthode :

[!code-javascript[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample8.js)]


[![Le troisième bouton modifie l’état du panneau : à partir de réduite en arrière et développé](collapsing-and-expanding-a-panel-from-javascript-cs/_static/image2.png)](collapsing-and-expanding-a-panel-from-javascript-cs/_static/image1.png)

Le troisième bouton modifie l’état du panneau : à partir de réduite en arrière et développé ([cliquez pour afficher l’image en taille réelle](collapsing-and-expanding-a-panel-from-javascript-cs/_static/image3.png))

>[!div class="step-by-step"]
[Next](collapsing-and-expanding-a-panel-from-javascript-vb.md)

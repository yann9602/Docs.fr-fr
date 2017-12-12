---
uid: web-forms/overview/ajax-control-toolkit/modalpopup/launching-a-modal-popup-window-from-server-code-cs
title: "Lancement d’une fenêtre contextuelle modale à partir du Code serveur (c#) | Documents Microsoft"
author: wenz
description: "Le contrôle ModalPopup dans la boîte à outils de contrôle AJAX offre un moyen simple de créer une fenêtre modale, à l’aide des moyens de côté client. Toutefois, certains scénarios requièrent que t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 2f67d8ef-73ca-447d-a0cc-6e3168431e6a
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/modalpopup/launching-a-modal-popup-window-from-server-code-cs
msc.type: authoredcontent
ms.openlocfilehash: cc2ccf8153de4f2633cc46ebbee2da199ba9d06e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="launching-a-modal-popup-window-from-server-code-c"></a>Lancement d’une fenêtre contextuelle modale à partir du Code serveur (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/2/4/0/24052038-f942-4336-905b-b60ae56f0dd5/ModalPopup1.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/modalpopup1CS.pdf)

> Le contrôle ModalPopup dans la boîte à outils de contrôle AJAX offre un moyen simple de créer une fenêtre modale, à l’aide des moyens de côté client. Toutefois certains scénarios requièrent que l’ouverture de la fenêtre contextuelle modale est déclenchée sur le côté serveur.


## <a name="overview"></a>Vue d'ensemble

Le contrôle ModalPopup dans la boîte à outils de contrôle AJAX offre un moyen simple de créer une fenêtre modale, à l’aide des moyens de côté client. Toutefois certains scénarios requièrent que l’ouverture de la fenêtre contextuelle modale est déclenchée sur le côté serveur.

## <a name="steps"></a>Étapes

Tout d’abord, un contrôle web ASP.NET est requis pour illustrer comment le contrôle ModalPopup fonctionne. Ajouter un bouton de ce type au sein de la &lt;formulaire&gt; élément sur une nouvelle page :

[!code-aspx[Main](launching-a-modal-popup-window-from-server-code-cs/samples/sample1.aspx)]

Ensuite, vous devez le balisage pour la fenêtre contextuelle que vous souhaitez créer. Définir comme étant un `<asp:Panel>` de contrôle et assurez-vous qu’il inclut un contrôle bouton. Le contrôle ModalPopup offre les fonctionnalités pour rendre ce bouton Fermer la fenêtre contextuelle ; Sinon, il n’existe aucun moyen simple pour lui faire disparaître.

[!code-aspx[Main](launching-a-modal-popup-window-from-server-code-cs/samples/sample2.aspx)]

Ensuite, ajoutez le contrôle ModalPopup d’ASP.NET AJAX Toolkit à la page. Définir des propriétés pour le bouton qui charge le contrôle, le bouton qui fait disparaître et l’ID de la fenêtre contextuelle réelle.

[!code-aspx[Main](launching-a-modal-popup-window-from-server-code-cs/samples/sample3.aspx)]

Comme avec toutes les pages web basée sur ASP.NET AJAX ; le Gestionnaire de Script est requis pour charger les bibliothèques JavaScript nécessaires pour les navigateurs cible différent :

[!code-aspx[Main](launching-a-modal-popup-window-from-server-code-cs/samples/sample4.aspx)]

Exécutez l’exemple dans le navigateur. Lorsque vous cliquez sur le bouton, la fenêtre contextuelle modale s’affiche. Afin d’obtenir le même effet à l’aide du code côté serveur, un nouveau bouton est nécessaire :

[!code-aspx[Main](launching-a-modal-popup-window-from-server-code-cs/samples/sample5.aspx)]

Comme vous pouvez le voir, un clic sur le bouton génère une publication (postback) et qu’il exécute la `ServerButton_Click()` méthode sur le serveur. Dans cette méthode, une fonction JavaScript appelée `launchModal()` est exécutée pour être précis, la fonction JavaScript sera exécutée une fois que la page a été chargée :

[!code-aspx[Main](launching-a-modal-popup-window-from-server-code-cs/samples/sample6.aspx)]

Le travail de `launchModal()` consiste à afficher le ModalPopup. Le `launchModal()` fonction est exécutée une fois que la page HTML a été chargée. À ce moment, toutefois, l’infrastructure ASP.NET AJAX n'a pas été entièrement chargé encore. Par conséquent, le `launchModal()` fonction définit simplement une variable qui contrôle ModalPopup doit être affiché ultérieurement :

[!code-html[Main](launching-a-modal-popup-window-from-server-code-cs/samples/sample7.html)]

Le `pageLoad()` fonction JavaScript est une fonction spéciale qui est exécutée une fois ASP.NET AJAX a été complètement chargé. Par conséquent, nous ajoutons le code à cette fonction pour afficher le contrôle de ModalPopup, mais uniquement si `launchModal()` a été appelé avant :

[!code-javascript[Main](launching-a-modal-popup-window-from-server-code-cs/samples/sample8.js)]

Le `$find()` fonction recherche un élément nommé dans la page et attend l’ID côté serveur en tant que paramètre. Par conséquent, `$find("mpe")` retourne la représentation sous forme de client du contrôle ModalPopup ; sa `show()` méthode vous permet de la fenêtre contextuelle apparaît.


[![La fenêtre contextuelle modale s’affiche lorsque vous cliquez sur un des boutons](launching-a-modal-popup-window-from-server-code-cs/_static/image2.png)](launching-a-modal-popup-window-from-server-code-cs/_static/image1.png)

La fenêtre contextuelle modale s’affiche lorsque vous cliquez sur un des boutons ([cliquez pour afficher l’image en taille réelle](launching-a-modal-popup-window-from-server-code-cs/_static/image3.png))

>[!div class="step-by-step"]
[Next](using-modalpopup-with-a-repeater-control-cs.md)

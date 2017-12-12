---
uid: web-forms/overview/ajax-control-toolkit/popup/handling-postbacks-from-a-popup-control-with-an-updatepanel-vb
title: "La gestion des publications (postback) à partir d’un contrôle Popup avec un UpdatePanel (VB) | Documents Microsoft"
author: wenz
description: "L’extendeur PopupControl dans la boîte à outils de contrôle AJAX offre un moyen simple de déclencher une fenêtre contextuelle lorsque n’importe quel autre contrôle est activé. Une attention particulière doit être portée..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: ec9db57c-9f68-402a-bf4c-0d63d5f6908e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/popup/handling-postbacks-from-a-popup-control-with-an-updatepanel-vb
msc.type: authoredcontent
ms.openlocfilehash: 4445437f25925429d240b7fe2d019855afc52fe0
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="handling-postbacks-from-a-popup-control-with-an-updatepanel-vb"></a>La gestion des publications (postback) à partir d’un contrôle Popup avec un UpdatePanel (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/PopupControl2.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/popupcontrol2VB.pdf)

> L’extendeur PopupControl dans la boîte à outils de contrôle AJAX offre un moyen simple de déclencher une fenêtre contextuelle lorsque n’importe quel autre contrôle est activé. Une attention particulière est à entreprendre lorsqu’une publication (postback) se produit au sein de ce type une fenêtre contextuelle.


## <a name="overview"></a>Vue d'ensemble

L’extendeur PopupControl dans la boîte à outils de contrôle AJAX offre un moyen simple de déclencher une fenêtre contextuelle lorsque n’importe quel autre contrôle est activé. Une attention particulière est à entreprendre lorsqu’une publication (postback) se produit au sein de ce type une fenêtre contextuelle.

## <a name="steps"></a>Étapes

Lorsque vous utilisez un `PopupControl` avec une publication, un `UpdatePanel` peut empêcher l’actualisation de la page a provoqué par la publication (postback). Le balisage suivant définit deux éléments importants :

- A `ScriptManager` contrôle afin que les outils de contrôle ASP.NET AJAX fonctionne
- Deux `TextBox` contrôles, ce qui déclencheront à la fois une fenêtre contextuelle
- A `Panel` contrôle qui servira de la fenêtre contextuelle
- Dans le panneau de configuration, un `Calendar` contrôle est incorporé dans un `UpdatePanel` contrôle
- Deux `PopupControlExtender` contrôles qui affectent le panneau de configuration pour les zones de texte

[!code-aspx[Main](handling-postbacks-from-a-popup-control-with-an-updatepanel-vb/samples/sample1.aspx)]

Notez que la `OnSelectionChanged` attribut de la `Calendar` contrôle est défini. Donc lorsque l’utilisateur sélectionne une date dans le calendrier, une publication (postback) se produit et la méthode côté serveur `c1_SelectionChanged()` est exécutée. Dans cette méthode, la date actuelle doit être récupérée et mises à jour dans la zone de texte.

La syntaxe est la suivante : tout d’abord, un proxy de l’objet pour le `PopupControlExtender` sur la page doit être généré. Les outils de contrôle ASP.NET AJAX offre le `GetProxyForCurrentPopup()` (méthode). Prend en charge de l’objet de cette méthode retourne la `Commit()` méthode qui envoie une valeur au contrôle qui a déclenché la fenêtre contextuelle (pas le contrôle qui a déclenché l’appel de méthode !). Le code suivant fournit la date sélectionnée comme argument pour la `Commit()` méthode, à l’origine du code à réécrire la date sélectionnée dans la zone de texte :

[!code-aspx[Main](handling-postbacks-from-a-popup-control-with-an-updatepanel-vb/samples/sample2.aspx)]

Lorsque vous cliquez sur une date du calendrier, la date sélectionnée s’affiche dans la zone de texte correspondante, création d’un contrôle de sélecteur de dates qui permettre actuellement se trouve désormais sur de nombreux sites Web.


[![Le calendrier s’affiche lorsque l’utilisateur clique dans la zone de texte](handling-postbacks-from-a-popup-control-with-an-updatepanel-vb/_static/image2.png)](handling-postbacks-from-a-popup-control-with-an-updatepanel-vb/_static/image1.png)

Le calendrier s’affiche lorsque l’utilisateur clique dans la zone de texte ([cliquez pour afficher l’image en taille réelle](handling-postbacks-from-a-popup-control-with-an-updatepanel-vb/_static/image3.png))


[![En cliquant sur une date place dans la zone de texte](handling-postbacks-from-a-popup-control-with-an-updatepanel-vb/_static/image5.png)](handling-postbacks-from-a-popup-control-with-an-updatepanel-vb/_static/image4.png)

En cliquant sur une date place dans la zone de texte ([cliquez pour afficher l’image en taille réelle](handling-postbacks-from-a-popup-control-with-an-updatepanel-vb/_static/image6.png))

>[!div class="step-by-step"]
[Précédent](using-multiple-popup-controls-vb.md)
[Suivant](handling-postbacks-from-a-popup-control-without-an-updatepanel-vb.md)

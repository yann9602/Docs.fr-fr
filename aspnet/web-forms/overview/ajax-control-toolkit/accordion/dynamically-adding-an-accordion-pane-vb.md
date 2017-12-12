---
uid: web-forms/overview/ajax-control-toolkit/accordion/dynamically-adding-an-accordion-pane-vb
title: "Ajout dynamique d’un volet Accordéon (VB) | Documents Microsoft"
author: wenz
description: "Le contrôle Accordéon dans la boîte à outils de contrôle AJAX fournit plusieurs volets et permet à l’utilisateur afficher l’un d'entre eux à la fois. Panneaux est généralement déclaré w..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: fae968c9-1902-487d-b053-86a46dd52c3f
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/accordion/dynamically-adding-an-accordion-pane-vb
msc.type: authoredcontent
ms.openlocfilehash: 6adc0b7985e5ba3da1684b645ae1b71b5112594a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="dynamically-adding-an-accordion-pane-vb"></a>Ajout dynamique d’un volet Accordéon (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/5/6/d/56d50cef-2011-4c8f-9891-7edc6dc57df9/Accordion2.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/accordion2VB.pdf)

> Le contrôle Accordéon dans la boîte à outils de contrôle AJAX fournit plusieurs volets et permet à l’utilisateur afficher l’un d'entre eux à la fois. Panneaux est généralement déclaré dans la page elle-même, mais le code côté serveur peut être utilisé pour obtenir le même résultat.


## <a name="overview"></a>Vue d'ensemble

Le contrôle Accordéon dans la boîte à outils de contrôle AJAX fournit plusieurs volets et permet à l’utilisateur afficher l’un d'entre eux à la fois. Panneaux est généralement déclaré dans la page elle-même, mais le code côté serveur peut être utilisé pour obtenir le même résultat.

## <a name="steps"></a>Étapes

Le contrôle Accordéon expose toutes les propriétés importantes pour le code côté serveur. Entre autres choses, le `Panes` propriété accorde l’accès à la collection de volets qui composent l’accordéon. Chaque volet est de type `AccordionPane`. Par conséquent, il est facile pour créer un volet de ce type :

[!code-vb[Main](dynamically-adding-an-accordion-pane-vb/samples/sample1.vb)]

Le `HeaderContainer` propriété du `AccordionPane` fournit l’accès aux contrôles ASP.NET dans la section d’en-tête du volet ; le `ContentContainer` propriété de `AccordionPane` fait de même pour la section de contenu du volet. Cela permet au code ASP.NET ajouter du contenu vers les volets :

[!code-vb[Main](dynamically-adding-an-accordion-pane-vb/samples/sample2.vb)]

Enfin, la pane(s) doit être ajouté à la `Panes` collection de l’accordéon :

[!code-vb[Main](dynamically-adding-an-accordion-pane-vb/samples/sample3.vb)]

Voici un code côté serveur complet qui ajoute deux volets à un contrôle Accordéon :

[!code-aspx[Main](dynamically-adding-an-accordion-pane-vb/samples/sample4.aspx)]

Le seul élément manquant est Accordéon lui-même, ce qui dépend de la présence de ASP.NET `ScriptManager` contrôle :

[!code-aspx[Main](dynamically-adding-an-accordion-pane-vb/samples/sample5.aspx)]

Pour terminer l’exemple, les deux classes CSS référencés dans le contrôle Accordéon fournissent des informations de style pour le navigateur :

[!code-css[Main](dynamically-adding-an-accordion-pane-vb/samples/sample6.css)]


[![Les données dans l’accordéon a été ajoutées dynamiquement par le code côté serveur](dynamically-adding-an-accordion-pane-vb/_static/image2.png)](dynamically-adding-an-accordion-pane-vb/_static/image1.png)

Les données dans l’accordéon a été ajoutées dynamiquement par le code côté serveur ([cliquez pour afficher l’image en taille réelle](dynamically-adding-an-accordion-pane-vb/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](databinding-to-an-accordion-vb.md)

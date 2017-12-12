---
uid: web-forms/overview/ajax-control-toolkit/reorderlist/using-postbacks-with-reorderlist-cs
title: "L’utilisation de publications (postback) avec ReorderList fournissant (c#) | Documents Microsoft"
author: wenz
description: "Le contrôle ReorderList fournissant dans la boîte à outils de contrôle AJAX fournit une liste qui peut être réorganisée par l’utilisateur via la fonction glisser- déposer. Chaque fois que la liste est réorganisée, un bon de commande en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 70d5d106-b547-442c-a7fd-3492b3e3d646
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/reorderlist/using-postbacks-with-reorderlist-cs
msc.type: authoredcontent
ms.openlocfilehash: 5f5c5e253f6d85203a488152c5ad908157e6ee0c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-postbacks-with-reorderlist-c"></a>L’utilisation de publications (postback) avec ReorderList fournissant (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/ReorderList4.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/reorderlist4CS.pdf)

> Le contrôle ReorderList fournissant dans la boîte à outils de contrôle AJAX fournit une liste qui peut être réorganisée par l’utilisateur via la fonction glisser- déposer. Chaque fois que la liste est réorganisée, une publication (postback) informe le serveur de la modification.


## <a name="overview"></a>Vue d'ensemble

Le `ReorderList` contrôle dans la boîte à outils de contrôle AJAX fournit une liste qui peut être réorganisée par l’utilisateur via la fonction glisser- déposer. Chaque fois que la liste est réorganisée, une publication (postback) informe le serveur de la modification.

## <a name="steps"></a>Étapes

Il existe plusieurs sources de données possibles pour le `ReorderList` contrôle. Un consiste à utiliser un `XmlDataSource` contrôle :

[!code-aspx[Main](using-postbacks-with-reorderlist-cs/samples/sample1.aspx)]

Afin de lier ce code XML pour un `ReorderList` doivent avoir la valeur contrôle et activer les publications, les attributs suivants :

- `DataSourceID`: L’ID de la source de données
- `SortOrderField`: La propriété selon laquelle trier par
- `AllowReorder`: S’il faut autoriser l’utilisateur à réorganiser les éléments de liste
- `PostBackOnReorder`: Si vous souhaitez créer une publication (postback) chaque fois que la liste est réorganisée.

Voici le balisage approprié pour le contrôle :

[!code-aspx[Main](using-postbacks-with-reorderlist-cs/samples/sample2.aspx)]

Dans le `ReorderList` contrôle, les données spécifiques à partir de la source de données peut être lié à l’aide de la `Eval()` méthode :

[!code-aspx[Main](using-postbacks-with-reorderlist-cs/samples/sample3.aspx)]

À une position arbitraire sur la page, une étiquette contiendra les informations lors de la réorganisation dernière s’est produite :

[!code-aspx[Main](using-postbacks-with-reorderlist-cs/samples/sample4.aspx)]

Cette étiquette est remplie avec le texte dans le code côté serveur, gérer la publication :

[!code-aspx[Main](using-postbacks-with-reorderlist-cs/samples/sample5.aspx)]

Enfin, pour activer la fonctionnalité d’ASP.NET AJAX et les outils de contrôle, le `ScriptManager` contrôle doit être placé sur la page :

[!code-aspx[Main](using-postbacks-with-reorderlist-cs/samples/sample6.aspx)]


[![Chaque réorganisation déclenche une publication (postback)](using-postbacks-with-reorderlist-cs/_static/image2.png)](using-postbacks-with-reorderlist-cs/_static/image1.png)

Chaque réorganisation déclenche une publication ([cliquez pour afficher l’image en taille réelle](using-postbacks-with-reorderlist-cs/_static/image3.png))

>[!div class="step-by-step"]
[Next](drag-and-drop-via-reorderlist-cs.md)

---
uid: web-forms/overview/ajax-control-toolkit/mutuallyexclusivecheckbox/creating-mutually-exclusive-checkboxes-vb
title: "Création de cases à cocher s’excluent mutuellement (VB) | Documents Microsoft"
author: wenz
description: "Lorsque seul un ensemble d’options peut être sélectionné, cases d’option sont généralement utilisées. Il existe cependant un inconvénient, : une fois qu’une case d’option dans un groupe est sélectionnée,..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: e9dd1d5a-a1db-4114-981d-6a91acb1d709
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/mutuallyexclusivecheckbox/creating-mutually-exclusive-checkboxes-vb
msc.type: authoredcontent
ms.openlocfilehash: 023ca0b145de8147a98e78f4dba20846dc344f06
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-mutually-exclusive-checkboxes-vb"></a>Création de cases à cocher s’excluent mutuellement (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/MutuallyExclusiveCheckBox0.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/mutuallyexclusivecheckbox0VB.pdf)

> Lorsque seul un ensemble d’options peut être sélectionné, cases d’option sont généralement utilisées. Il existe cependant un inconvénient, : une fois qu’une case d’option dans un groupe est sélectionnée, il n’est pas possible de désactiver toutes les cases d’option. Cases à cocher ne peut être désactivés à tout moment, toutefois, ne sont pas mutuellement exclusives. Ce didacticiel offre le meilleur des deux approches : les cases à cocher qui s’excluent mutuellement.


## <a name="overview"></a>Vue d'ensemble

Lorsque seul un ensemble d’options peut être sélectionné, cases d’option sont généralement utilisées. Il existe cependant un inconvénient, : une fois qu’une case d’option dans un groupe est sélectionnée, il n’est pas possible de désactiver toutes les cases d’option. Cases à cocher ne peut être désactivés à tout moment, toutefois, ne sont pas mutuellement exclusives. Ce didacticiel offre le meilleur des deux approches : les cases à cocher qui s’excluent mutuellement.

## <a name="steps"></a>Étapes

Les outils de contrôle ASP.NET AJAX contient l’extendeur MutuallyExclusiveCheckBox. Cela permet aux programmeurs d’affecter les case à cocher à un nom de groupe (`Key` attribut). À partir de toutes les cases à cocher dans le même groupe, une seule peut être sélectionnée à la fois.

Commençons par placer les deux cases à cocher sur une page ASP.NET. Il peut y avoir plusieurs, mais deux d'entre eux suffit pour illustrer le principe :

[!code-aspx[Main](creating-mutually-exclusive-checkboxes-vb/samples/sample1.aspx)]

Pour les deux cases à cocher, un contrôle MutuallyExclusiveCheckBoxExtender doit être mis sur la page. Les deux attributs de clé doivent avoir la même valeur, tout comme la valeur d’attributs d’éléments de bouton radio HTML doivent être identiques pour indiquer le groupe qu'auquel ils appartiennent. La propriété TargetControlID de l’extendeur pointe vers l’ID de la case à cocher.

[!code-aspx[Main](creating-mutually-exclusive-checkboxes-vb/samples/sample2.aspx)]

Enfin, inclure ASP.NET AJAX `ScriptManager` requis par tous les éléments de la boîte à outils de contrôle ASP.NET AJAX :

[!code-aspx[Main](creating-mutually-exclusive-checkboxes-vb/samples/sample3.aspx)]

Enregistrez et exécutez la page : vous pouvez vérifier et désactivez les cases à cocher, toutefois à aucun moment peut les deux cases à cocher est activées.


[![Case à cocher qu’une seule peut être vérifiée à la fois](creating-mutually-exclusive-checkboxes-vb/_static/image2.png)](creating-mutually-exclusive-checkboxes-vb/_static/image1.png)

Case à cocher qu’une seule peut être vérifiée à la fois ([cliquez pour afficher l’image en taille réelle](creating-mutually-exclusive-checkboxes-vb/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](creating-mutually-exclusive-checkboxes-cs.md)

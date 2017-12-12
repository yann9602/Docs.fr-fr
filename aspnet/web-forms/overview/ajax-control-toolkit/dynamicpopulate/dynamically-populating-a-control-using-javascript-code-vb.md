---
uid: web-forms/overview/ajax-control-toolkit/dynamicpopulate/dynamically-populating-a-control-using-javascript-code-vb
title: "Remplissage dynamique d’un contrôle à l’aide du Code JavaScript (VB) | Documents Microsoft"
author: wenz
description: "Le contrôle DynamicPopulate dans les outils de contrôle ASP.NET AJAX appelle un service web (ou une méthode de page) et remplit la valeur obtenue dans un contrôle cible t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 90582e54-3e90-432a-9da5-689fb39ed56b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/dynamicpopulate/dynamically-populating-a-control-using-javascript-code-vb
msc.type: authoredcontent
ms.openlocfilehash: b4090b3a785059c8f09de266df79eba0914e9f13
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="dynamically-populating-a-control-using-javascript-code-vb"></a>Remplissage dynamique d’un contrôle à l’aide du Code JavaScript (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/d/8/f/d8f2f6f9-1b7c-46ad-9252-e1fc81bdea3e/dynamicpopulate1.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dynamicpopulate1VB.pdf)

> Le contrôle DynamicPopulate dans les outils de contrôle ASP.NET AJAX appelle un service web (ou une méthode de page) et remplit la valeur obtenue dans un contrôle cible dans la page, sans une actualisation de la page. Il est également possible de déclencher le remplissage à l’aide du code JavaScript côté client personnalisé.


## <a name="overview"></a>Vue d'ensemble

Le `DynamicPopulate` contrôle dans la boîte à outils de contrôle ASP.NET AJAX appelle un service web (ou une méthode de page) et remplit la valeur obtenue dans un contrôle cible dans la page, sans une actualisation de la page. Il est également possible de déclencher le remplissage à l’aide du code JavaScript côté client personnalisé.

## <a name="steps"></a>Étapes

Vous devez tout d’abord, Service Web ASP.NET qui implémente la méthode à être appelée par le `DynamicPopulateExtender` contrôle. Le service web implémente la méthode `getDate()` qui attend un argument de type chaîne, appelée `contextKey`, étant donné que le `DynamicPopulate` contrôle envoie une partie des informations de contexte à chaque appel de service web. Voici le code (fichier `DynamicPopulate.vb.asmx`) qui Récupère la date actuelle dans un des trois formats :

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-vb/samples/sample1.aspx)]

Dans l’étape suivante, créer un nouveau site ASP.NET et démarrer avec le contrôle ASP.NET AJAX ScriptManager :

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-vb/samples/sample2.aspx)]

Ensuite, ajoutez un contrôle label (par exemple à l’aide du contrôle HTML portant le même nom, ou le `<asp:Label />` contrôle web) qui affichera ultérieurement le résultat de l’appel de service web.

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-vb/samples/sample3.aspx)]

Ensuite, inclure un `DynamicPopulateExtender` contrôler et de fournir des informations sur le service web, contrôle de la cible, mais pas le nom du contrôle qui déclenche la population de cette opération est effectuée ultérieurement à l’aide de code JavaScript personnalisé !

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-vb/samples/sample4.aspx)]

Pour la partie JavaScript. Le `$find()` fonction, définie par la bibliothèque ASP.NET AJAX, retourne une référence aux objets côté serveur de la boîte à outils de contrôle ASP.NET AJAX comme `DynamicPopulateExtender`. Dans le fichier actuel, `$find("dpe")` retourne une référence à le `DynamicPopulateExtender` contrôle dans la page. Il expose une méthode appelée `populate()` qui déclenche le processus de remplissage dynamique. Le `populate()` méthode nécessite un seul argument : la clé de contexte qui servira à l’argument de la `getDate()` méthode web. Par exemple, `$find("dpe").populate("format1")` remplirait l’étiquette avec la date actuelle au format de mois-jour-année.

Pour rendre l’exemple un peu plus souple, l’utilisateur peut désormais le choix entre plusieurs formats de date. Pour chacune d'entre elles, une case s’affiche. Une fois l’utilisateur clique sur un bouton radio, du code JavaScript renseigne dynamiquement l’étiquette avec le format de date sélectionnée. Voici les cases d’option :

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-vb/samples/sample5.aspx)]

Notez que dans le contexte d’une case d’option, l’expression JavaScript `this.value` fait référence à la valeur du bouton actuel, qui se trouve être exactement les mêmes informations la `getDate()` méthode peut fonctionner avec.


[![Un clic sur le bouton récupère la date à partir du serveur, dans le format spécifié](dynamically-populating-a-control-using-javascript-code-vb/_static/image2.png)](dynamically-populating-a-control-using-javascript-code-vb/_static/image1.png)

Un clic sur le bouton récupère la date à partir du serveur, dans le format spécifié ([cliquez pour afficher l’image en taille réelle](dynamically-populating-a-control-using-javascript-code-vb/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](dynamically-populating-a-control-vb.md)
[Suivant](using-dynamicpopulate-with-a-user-control-and-javascript-vb.md)

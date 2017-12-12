---
uid: web-forms/overview/ajax-control-toolkit/dynamicpopulate/using-dynamicpopulate-with-a-user-control-and-javascript-vb
title: "À l’aide de DynamicPopulate avec un contrôle utilisateur et le JavaScript (VB) | Documents Microsoft"
author: wenz
description: "Le contrôle DynamicPopulate dans les outils de contrôle ASP.NET AJAX appelle un service web (ou une méthode de page) et remplit la valeur obtenue dans un contrôle cible t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 778b9009-76f2-4665-940e-afc0e35bc917
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/dynamicpopulate/using-dynamicpopulate-with-a-user-control-and-javascript-vb
msc.type: authoredcontent
ms.openlocfilehash: 69066edc18b58cc3148a738fe8dd48cb92a84f11
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-dynamicpopulate-with-a-user-control-and-javascript-vb"></a>À l’aide de DynamicPopulate avec un contrôle utilisateur et le JavaScript (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/d/8/f/d8f2f6f9-1b7c-46ad-9252-e1fc81bdea3e/dynamicpopulate2.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dynamicpopulate2VB.pdf)

> Le contrôle DynamicPopulate dans les outils de contrôle ASP.NET AJAX appelle un service web (ou une méthode de page) et remplit la valeur obtenue dans un contrôle cible dans la page, sans une actualisation de la page. Il est également possible de déclencher le remplissage à l’aide du code JavaScript côté client personnalisé. Toutefois, une attention particulière doit entreprendre lorsque l’extendeur se trouve dans un contrôle utilisateur.


## <a name="overview"></a>Vue d'ensemble

Le `DynamicPopulate` contrôle dans la boîte à outils de contrôle ASP.NET AJAX appelle un service web (ou une méthode de page) et remplit la valeur obtenue dans un contrôle cible dans la page, sans une actualisation de la page. Il est également possible de déclencher le remplissage à l’aide du code JavaScript côté client personnalisé. Toutefois, une attention particulière doit entreprendre lorsque l’extendeur se trouve dans un contrôle utilisateur.

## <a name="steps"></a>Étapes

Vous devez tout d’abord, Service Web ASP.NET qui implémente la méthode à être appelée par le `DynamicPopulateExtender` contrôle. Le service web implémente la méthode `getDate()` qui attend un argument de type chaîne, appelée `contextKey`, étant donné que le `DynamicPopulate` contrôle envoie une partie des informations de contexte à chaque appel de service web. Voici le code (fichiers `DynamicPopulate.vb.asmx`) qui Récupère la date actuelle dans un des trois formats :

[!code-aspx[Main](using-dynamicpopulate-with-a-user-control-and-javascript-vb/samples/sample1.aspx)]

Dans l’étape suivante, créez un nouveau contrôle utilisateur (`.ascx` fichier), il est signalé par la déclaration suivante dans la première ligne :

[!code-aspx[Main](using-dynamicpopulate-with-a-user-control-and-javascript-vb/samples/sample2.aspx)]

A &lt; `label` &gt; élément servira pour afficher les données provenant du serveur.

[!code-aspx[Main](using-dynamicpopulate-with-a-user-control-and-javascript-vb/samples/sample3.aspx)]

Également dans le fichier de contrôle utilisateur, nous utiliserons des trois cases d’option, chacune représentant un des trois formats de date possibles prises en charge par le service web. Lorsque l’utilisateur clique sur l’un des boutons radio, le navigateur exécute le code JavaScript qui ressemble à ceci :

[!code-powershell[Main](using-dynamicpopulate-with-a-user-control-and-javascript-vb/samples/sample4.ps1)]

Ce code accède à la `DynamicPopulateExtender` (ne vous inquiétez pas sur l’ID d’étrange encore, ce point sera abordé plus tard) et déclenche le remplissage dynamique avec des données. Dans le contexte de la case en cours, `this.value` fait référence à sa valeur, qui est `format1`, `format2` ou `format3` exactement ce qu’attend la méthode web.

La seule chose que manquant encore dans le contrôle utilisateur est le `DynamicPopulateExtender` contrôle qui lie les cases au service web.

[!code-aspx[Main](using-dynamicpopulate-with-a-user-control-and-javascript-vb/samples/sample5.aspx)]

À nouveau, vous pouvez noter l’ID étrange utilisé dans le contrôle : `mcd1$myDate` au lieu de `myDate`. Auparavant, le code JavaScript utilisé `mcd1_dpe1` pour accéder à la `DynamicPopulateExtender` au lieu de `dpe1`. Cette stratégie d’affectation de noms est un besoin particulier lorsque vous utilisez `DynamicPopulateExtender` au sein d’un contrôle utilisateur. En outre, vous devez incorporer le contrôle utilisateur de manière spécifique pour que tout fonctionne. Créer une page ASP.NET et inscrire un préfixe de balise pour le contrôle utilisateur que vous venez d’implémenter :

[!code-aspx[Main](using-dynamicpopulate-with-a-user-control-and-javascript-vb/samples/sample6.aspx)]

Ensuite, inclure ASP.NET AJAX `ScriptManager` contrôle sur la nouvelle page :

[!code-aspx[Main](using-dynamicpopulate-with-a-user-control-and-javascript-vb/samples/sample7.aspx)]

Enfin, ajoutez le contrôle utilisateur à la page. Il vous suffit de définir son `ID` attribut (et `runat="server"`, bien sûr), mais vous devez également lui affecter un nom spécifique : `mcd1` puisque c’est le préfixe utilisé dans le contrôle utilisateur pour accéder à l’aide de JavaScript.

[!code-aspx[Main](using-dynamicpopulate-with-a-user-control-and-javascript-vb/samples/sample8.aspx)]

Et voilà ! La page se comporte comme prévu : un utilisateur clique sur des boutons radio, le contrôle de la boîte à outils appelle le service web et affiche la date actuelle au format souhaité.


[![Les boutons de case d’option se trouvent dans un contrôle utilisateur](using-dynamicpopulate-with-a-user-control-and-javascript-vb/_static/image2.png)](using-dynamicpopulate-with-a-user-control-and-javascript-vb/_static/image1.png)

Les boutons de case d’option se trouvent dans un contrôle utilisateur ([cliquez pour afficher l’image en taille réelle](using-dynamicpopulate-with-a-user-control-and-javascript-vb/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](dynamically-populating-a-control-using-javascript-code-vb.md)

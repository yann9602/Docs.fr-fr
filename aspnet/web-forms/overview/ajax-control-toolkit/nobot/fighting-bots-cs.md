---
uid: web-forms/overview/ajax-control-toolkit/nobot/fighting-bots-cs
title: Lutte contre robots (c#) | Documents Microsoft
author: wenz
description: "Robots automatisés coller les blogs et autres sites Web avec courrier indésirable, l’envoi de formulaires de commentaire sans intervention de l’utilisateur. Le contrôle NoBot dans la Con AJAX ASP.NET..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 0a1917e0-884a-4576-8e93-9ed660faae51
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/nobot/fighting-bots-cs
msc.type: authoredcontent
ms.openlocfilehash: b8eedff4691c1115e242be884f9e74663dc0b4f9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="fighting-bots-c"></a>Lutte contre robots (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/NoBot0.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/nobot0CS.pdf)

> Robots automatisés coller les blogs et autres sites Web avec courrier indésirable, l’envoi de formulaires de commentaire sans intervention de l’utilisateur. Le contrôle NoBot dans les outils de contrôle ASP.NET AJAX permettent de combattre les robots.


## <a name="overview"></a>Vue d'ensemble

Robots automatisés coller les blogs et autres sites Web avec courrier indésirable, l’envoi de formulaires de commentaire sans intervention de l’utilisateur. Le contrôle NoBot dans les outils de contrôle ASP.NET AJAX permettent de combattre les robots.

## <a name="steps"></a>Étapes

Une approche commune pour lutter robots est CAPTCHAs entièrement automatisée publique Turing test permet d’indiquer les uns des autres utilisateurs et ordinateurs. Un test Turing a été initialement un test sur lequel un utilisateur nécessaires pour déterminer si un partenaire de communication est un être humain ou un ordinateur. Dans le site web, un test CAPTCHA se compose généralement d’une image avec certaines lettres déformées sur celui-ci. L’idée est que seulement une personne peut lire les lettres de l’image, tandis que les algorithmes OCR échouera.

Il existe plusieurs avantages et inconvénients de cette approche, mais une discussion de ce est dépasse le cadre de ce didacticiel. Il existe toutefois un contrôle dans les outils de contrôle ASP.NET AJAX qui fournit une approche similaire : `NoBot`. Il est plus facile de résoudre à un test CAPTCHA, mais est très facile à utiliser et tarifs parfaitement sur les sites Web tels que des blogs, où il est considéré comme un succès si la plupart des tentatives de spam invalidées, ce qui le `NoBot` peut faire.

`NoBot`intercepte la publication du formulaire web ASP.NET en cours si au moins une des conditions suivantes est remplie :

- Le navigateur ne parvient pas à résoudre un puzzle JavaScript (par exemple lorsque JavaScript est désactivé)
- L’utilisateur a envoyé le formulaire à rapide
- L’adresse IP du client a envoyé le formulaire trop souvent dans un certain temps.

Pour vérifier ces conditions, la `NoBot` contrôle requiert ces attributs (tous les facultatif) :

- `ResponseMinimumDelaySeconds`quantité minimale de secondes entre les publications (postback)
- `CutoffWindowSeconds`longueur d’intervalle de temps dans laquelle les publications (postback) à partir d’une adresse IP est des mesures
- `CutoffMaximumInstances`quantité maximale de l’intervalle de temps, en secondes

Les demandes de balisage suivant au moins deux secondes s’écoulent entre publications (postback) et qu’il existe cinq publications (postback) ou moins dans un intervalle de 30 secondes :

[!code-aspx[Main](fighting-bots-cs/samples/sample1.aspx)]

Comme d’habitude veillez à inclure le `ScriptManager` dans la page afin que la bibliothèque ASP.NET AJAX est chargée et les outils de contrôle peut être utilisé :

[!code-aspx[Main](fighting-bots-cs/samples/sample2.aspx)]

Puisque la majorité des contrôles `NoBot` fait se produisent sur le côté serveur, vous devez vérifier le résultat de ces validations. Cela peut être effectué en appelant `NoBot`de `IsValid()` (méthode). Il possède un seul argument (comme un `out` paramètre /`ByRef` paramètre) qui est de type `NoBotState`. Représentation sous forme de chaîne contient la raison lorsque la vérification échoue et `Valid` dans le cas contraire. Le code suivant génère un message en fonction de `NoBot`du résultat :

[!code-aspx[Main](fighting-bots-cs/samples/sample3.aspx)]

Enfin, vous avez besoin d’un formulaire à envoyer et un élément de l’étiquette pour le message de sortie, et que vous avez terminé !

[!code-aspx[Main](fighting-bots-cs/samples/sample4.aspx)]

Lorsque vous exécutez ce script et désactivez JavaScript ou envoyez le formulaire dans les deux premières secondes ou envoyez le formulaire sept fois dans les 30 secondes, vous obtiendrez un message d’erreur. Toutefois utiliser judicieusement ce contrôle, environ 90-95 % des utilisateurs ayant JavaScript activé, par conséquent, 5 et 10 % des utilisateurs ne sera pas `NoBot`du test.


[![Ce message d’erreur peut être dû à un bot](fighting-bots-cs/_static/image2.png)](fighting-bots-cs/_static/image1.png)

Ce message d’erreur peut être dû à un bot ([cliquez pour afficher l’image en taille réelle](fighting-bots-cs/_static/image3.png))

>[!div class="step-by-step"]
[Next](fighting-bots-vb.md)

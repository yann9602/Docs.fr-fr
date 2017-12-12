---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-8
title: "Afficher les détails de l’élément | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: 75ef94b1-bbec-4681-9210-452dba816144
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-8
msc.type: authoredcontent
ms.openlocfilehash: 0b6ae9384843712cae824ea662b984a40f021e57
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="display-item-details"></a>Affichage des détails d’élément
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](https://github.com/MikeWasson/BookService)

Dans cette section, vous allez ajouter la possibilité d’afficher les détails de chaque livre. Dans app.js, ajoutez le code suivant pour le modèle d’affichage :

[!code-javascript[Main](part-8/samples/sample1.js)]

Dans Views/Home/Index.cshtml, ajoutez un élément de liaison de données pour le lien Détails :

[!code-html[Main](part-8/samples/sample2.html?highlight=5)]

Ceci lie le gestionnaire click pour le &lt;un&gt; élément à la `getBookDetail` fonction sur le modèle d’affichage.

Dans le même fichier, remplacez le marquer des suivantes :

[!code-html[Main](part-8/samples/sample3.html)]

par le code :

[!code-html[Main](part-8/samples/sample4.html)]

Cette balise crée une table qui est lié aux données pour les propriétés de la `detail` observable dans le modèle d’affichage.

Le «&lt;!--ko--&gt; &quot; syntaxe vous permet d’inclure une liaison Knockout en dehors d’un élément DOM. Dans ce cas, le `if` liaison provoque cette section du balisage s’affiche uniquement lorsque `details` n’est pas null.

[!code-html[Main](part-8/samples/sample5.html)]

Maintenant, si vous exécutez l’application et cliquez sur un de la &quot;détail&quot; liens, l’application affiche les détails de l’annuaire.

[![](part-8/_static/image2.png)](part-8/_static/image1.png)

>[!div class="step-by-step"]
[Précédent](part-7.md)
[Suivant](part-9.md)

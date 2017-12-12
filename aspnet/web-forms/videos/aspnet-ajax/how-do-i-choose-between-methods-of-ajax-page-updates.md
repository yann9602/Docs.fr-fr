---
uid: web-forms/videos/aspnet-ajax/how-do-i-choose-between-methods-of-ajax-page-updates
title: "[Comment faire] Le choix entre les méthodes d’AJAX Page mises à jour ? | Microsoft Docs"
author: JoeStagner
description: "Dans cette vidéo Joe Stagner compare les deux principales méthodes de mise à jour de page de style AJAX dans une application ASP.NET. La première méthode consiste à utiliser un Upd..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/09/2007
ms.topic: article
ms.assetid: a5e33a7d-ccb2-483f-a955-3d39f72ba4ec
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/videos/aspnet-ajax/how-do-i-choose-between-methods-of-ajax-page-updates
msc.type: video
ms.openlocfilehash: cc3721c24c9fed0cb028d755330a5c6189b613b7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="how-do-i-choose-between-methods-of-ajax-page-updates"></a>[Comment faire] Le choix entre les méthodes d’AJAX Page mises à jour ?
====================
par [Joe Stagner](https://github.com/JoeStagner)

Dans cette vidéo Joe Stagner compare les deux principales méthodes de mise à jour de page de style AJAX dans une application ASP.NET. La première consiste à utiliser un UpdatePanel, où aucun code supplémentaire ne doit être écrit sur le côté client ou côté serveur. L’avantage de l’utilisation de UpdatePanel est que tout fonctionne automatiquement. La pénalité qui correspond au niveau du client, qu'il requiert une grande quantité de données à inclure dans la requête AJAX et la réponse et au niveau du serveur, qu'il requiert un cycle de vie d’une page complète à exécuter. La deuxième consiste à utiliser les rappels de réseau, où le code supplémentaire doit être écrits sur le côté client et côté serveur. L’avantage de l’utilisation de rappels de réseau est qu’au niveau du client, elle nécessite très peu de données à inclure dans la requête AJAX et la réponse et au niveau du serveur nécessite uniquement la méthode de service appelé à être exécutée. Le penality est le temps de travail que nécessaire pour écrire le code nécessaire. Joe conclut la vidéo en expliquant que vous devez prendre en compte lors du choix entre les deux méthodes principales de mises à jour de page de style AJAX. (Cette vidéo utilise le code à partir de la [comment faire pour démarrer avec ASP.NET AJAX](how-do-i-get-started-with-aspnet-ajax.md) vidéo et [comment faire pour effectuer des rappels réseau côté Client avec ASP.NET AJAX](how-do-i-make-client-side-network-callbacks-with-aspnet-ajax.md) vidéo.)

[&#9654; Regardez la vidéo (minutes 11)](https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/how-do-i-choose-between-methods-of-ajax-page-updates)

>[!div class="step-by-step"]
[Précédent](how-do-i-update-multiple-regions-of-a-page-with-aspnet-ajax.md)
[Suivant](how-do-i-use-other-javascript-user-interface-libraries-with-aspnet-ajax.md)

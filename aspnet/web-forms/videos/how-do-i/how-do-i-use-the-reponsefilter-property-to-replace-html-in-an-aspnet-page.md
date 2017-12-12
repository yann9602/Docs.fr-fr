---
uid: web-forms/videos/how-do-i/how-do-i-use-the-reponsefilter-property-to-replace-html-in-an-aspnet-page
title: "[Comment faire] La propriété Reponse.Filter permet de remplacer le code HTML dans une Page ASP.NET | Documents Microsoft"
author: rick-anderson
description: "Dans cette Chris Pels vidéo montre comment utiliser la propriété Reponse.Filter pour intercepter et modifier le code HTML envoyé à une page. Un exemple de page est d’abord créé w..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/29/2009
ms.topic: article
ms.assetid: 3e5ae74a-9798-47d8-a2b3-0d8ad42dd4bc
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/videos/how-do-i/how-do-i-use-the-reponsefilter-property-to-replace-html-in-an-aspnet-page
msc.type: video
ms.openlocfilehash: 3e7c1f2a947d185d7c2a01deb75e42c92ba914c3
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="how-do-i-use-the-reponsefilter-property-to-replace-html-in-an-aspnet-page"></a>[Comment faire] La propriété Reponse.Filter permet de remplacer le code HTML dans une Page ASP.NET
====================
par [Chris PEL](https://twitter.com/chrispels)

Dans cette Chris Pels vidéo montre comment utiliser la propriété Reponse.Filter pour intercepter et modifier le code HTML envoyé à une page. Tout d’abord, un exemple de page est créé avec du texte simple. Ensuite, une classe de flux personnalisée est créée qui sert le flux de remplacement pour le flux actuel est envoyé au navigateur de l’utilisateur. Dans cette classe de flux de données personnalisé, le contenu de la page est récupéré à partir du flux, modifiées et puis écrits dans le flux de réponse. Dans cette classe de flux personnalisée, la méthode Write est personnalisée pour remplacer le code HTML dans le flux de réponse de base, altérant ainsi ce qui est envoyé au navigateur de l’utilisateur. Enfin, la nouvelle classe de flux de données est attribuée à la propriété Response.Filter dans la Page\_charge l’événement, ce qui, en fournissant le mécanisme permettant de modifier le contenu de la page.

[&#9654; Regardez la vidéo (13 minutes)](https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/how-do-i-use-the-reponsefilter-property-to-replace-html-in-an-aspnet-page)

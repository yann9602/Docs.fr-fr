---
uid: mvc/overview/getting-started/lifecycle-of-an-aspnet-mvc-5-application
title: "Cycle de vie d’une Application ASP.NET MVC 5 | Documents Microsoft"
author: cephalin
description: "Télécharger un document PDF que les graphiques du cycle de vie d’une application ASP.NET MVC 5. Ce document de cycle de vie fournit une vue d’ensemble du cycle de vie MVC un..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/28/2014
ms.topic: article
ms.assetid: 9c1e3a75-b644-4480-8326-11300b1ec4b3
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/lifecycle-of-an-aspnet-mvc-5-application
msc.type: authoredcontent
ms.openlocfilehash: 5692c43168eb261c91f40e2046897a1e5d31a028
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="lifecycle-of-an-aspnet-mvc-5-application"></a>Cycle de vie d’une Application ASP.NET MVC 5
====================
par [Cephas Lin](https://github.com/cephalin)

[Téléchargez le Document PDF](lifecycle-of-an-aspnet-mvc-5-application/_static/lifecycle-of-an-aspnet-mvc-5-application1.pdf)

Ici, vous pouvez télécharger un document PDF graphiques le cycle de vie des applications ASP.NET MVC 5, à partir de réception HTTP demande d’envoi de la réponse HTTP au client. Il est conçu comme un outil de formation pour ceux qui sont nouveaux pour ASP.NET MVC et également en tant que référence pour ceux qui ont besoin d’Explorer les aspects spécifiques de l’application. Le document PDF présente les caractéristiques suivantes :

- Applique [HttpApplication](https://msdn.microsoft.com/en-us/library/system.web.httpapplication.aspx) étapes pour vous aider à comprendre où MVC intègre les [cycle de vie des applications ASP.NET](https://msdn.microsoft.com/en-us/library/bb470252.aspx).
- Une vue d’ensemble du cycle de vie application MVC, où vous pouvez comprendre les étapes majeures que chaque application MVC traverse dans le pipeline de traitement de la requête.  
    ![](lifecycle-of-an-aspnet-mvc-5-application/_static/image1.jpg)
- Un affichage des détails qui montre extrait vers le bas dans les détails du pipeline de traitement de la requête. Vous pouvez comparer la vue d’ensemble et l’affichage des détails pour voir comment les détails des cycles de vie sont rassemblés dans les différentes étapes. [Télécharger le PDF](lifecycle-of-an-aspnet-mvc-5-application/_static/lifecycle-of-an-aspnet-mvc-5-application1.pdf) pour obtenir une vue agrandie.
    ![](lifecycle-of-an-aspnet-mvc-5-application/_static/image2.jpg)
- La sélection élective et l’objectif de toutes les méthodes substituables sur le [contrôleur](https://msdn.microsoft.com/en-us/library/system.web.mvc.controller.aspx) objet dans le pipeline de traitement de demande. Vous pouvez ou ne pouvez pas avoir la nécessité de remplacer n’importe quelle un méthode, mais il est important de comprendre leur rôle dans le cycle de vie d’application afin que vous pouvez écrire du code à l’étape du cycle de vie approprié pour l’effet que vous avez l’intention.
- Diagrammes de haut dirigé montrant comment chacun des types de filtre (l’authentification, l’autorisation, action et résultats) est appelé.
- Lier à un article utile ou un blog à partir de chaque point d’intérêt dans l’affichage des détails.


## <a name="next-steps"></a>Étapes suivantes

Ce document répond à vos besoins ? Nous aimerions connaître votre opinion. Si vous avez des questions sur le cycle de vie ASP.NET MVC dans votre application, [Stackoverflow](http://stackoverflow.com/help) et [forums d’ASP.NET MVC](https://forums.asp.net/1146.aspx) sont des emplacements formidables pour demander. Suivez [me](https://twitter.com/Cephas_MSFT) sur twitter, vous pouvez obtenir les mises à jour sur mon didacticiels plus récente.

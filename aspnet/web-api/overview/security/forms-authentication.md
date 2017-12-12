---
uid: web-api/overview/security/forms-authentication
title: "L’authentification par formulaire dans ASP.NET Web API | Documents Microsoft"
author: MikeWasson
description: "Décrit l’utilisation de l’authentification par formulaire dans ASP.NET Web API."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/12/2012
ms.topic: article
ms.assetid: 9f06c1f2-ffaa-4831-94a0-2e4a3befdf07
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/security/forms-authentication
msc.type: authoredcontent
ms.openlocfilehash: 9027d76bcf8854fc85f11906d3651511f350cd32
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="forms-authentication-in-aspnet-web-api"></a>Authentification par formulaire dans ASP.NET Web API
====================
par [Mike Wasson](https://github.com/MikeWasson)

L’authentification par formulaire utilise un formulaire HTML pour envoyer des informations d’identification de l’utilisateur sur le serveur. Il n’est pas une norme Internet. L’authentification par formulaire n’est appropriée pour le web API est appelées à partir d’une application web, afin que l’utilisateur peut interagir avec le formulaire HTML.

| Avantages | Inconvénients |
| --- | --- |
| -Facile à implémenter : intégré à ASP.NET. -Utilise le fournisseur d’appartenances ASP.NET, ce qui le rend facile à gérer les comptes d’utilisateur. | -N’est pas un mécanisme HTTP standard d’authentification ; utilise des cookies HTTP au lieu de l’en-tête d’autorisation standard. -Nécessite un navigateur client. -Informations d’identification sont envoyées en texte clair. -Vulnérable à la falsification de requête (CSRF) ; nécessite les mesures anti-CSRF. -Difficile à utiliser à partir de clients de nonbrowser. Ouverture de session nécessite un navigateur. -Informations d’identification sont envoyées dans la demande. -Certains utilisateurs désactivent les cookies. |

En bref, l’authentification par formulaire dans ASP.NET fonctionne comme suit :

1. Le client demande une ressource qui nécessite une authentification.
2. Si l’utilisateur n’est pas authentifié, le serveur renvoie HTTP 302 (trouvé) et le redirige vers une page de connexion.
3. L’utilisateur entre les informations d’identification et envoie le formulaire.
4. Le serveur renvoie un autre HTTP 302 qui redirige vers l’URI d’origine. Cette réponse comprend un cookie d’authentification.
5. Le client demande la ressource à nouveau. La requête inclut le cookie d’authentification pour le serveur autorise la demande.

![](forms-authentication/_static/image1.png)

Pour plus d’informations, consultez [une vue d’ensemble de l’authentification par formulaire.](../../../web-forms/overview/older-versions-security/introduction/an-overview-of-forms-authentication-cs.md)

## <a name="using-forms-authentication-with-web-api"></a>À l’aide de l’authentification par formulaire avec l’API Web

Pour créer une application qui utilise l’authentification par formulaire, sélectionnez le modèle de « Application Internet » dans l’Assistant de projet MVC 4. Ce modèle crée des contrôleurs MVC pour la gestion des comptes. Vous pouvez également utiliser le modèle « Application à Page unique », disponible dans la mise à jour ASP.NET automne 2012.

Dans les contrôleurs de votre API web, vous pouvez restreindre l’accès à l’aide de la `[Authorize]` d’attribut, comme décrit dans [à l’aide de l’attribut [Authorize]](authentication-and-authorization-in-aspnet-web-api.md#auth3).

Authentification par formulaire utilise un cookie de session pour authentifier les demandes. Les navigateurs envoient automatiquement tous les cookies pertinentes pour le site de destination. Cette fonctionnalité permet l’authentification par formulaire potentiellement vulnérables à voir des attaques par falsification (CSRF) de requête intersites [empêcher Cross-Site demande Forgery (CSRF) attaques](preventing-cross-site-request-forgery-csrf-attacks.md).

L’authentification par formulaire ne chiffre pas les informations d’identification de l’utilisateur. Par conséquent, l’authentification par formulaire n’est pas sécurisée, sauf si utilisée avec SSL. Consultez [utilisation de SSL dans l’API Web](working-with-ssl-in-web-api.md).

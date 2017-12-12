---
uid: web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
title: "Prévention Cross-Site Request Forgery (CSRF) dans l’API Web ASP.NET | Documents Microsoft"
author: MikeWasson
description: "Décrit l’attaque de falsification (CSRF) demande entre sites et comment implémenter des mesures de l’anti-CSRF dans l’API Web ASP.NET."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/12/2012
ms.topic: article
ms.assetid: 81d46f14-8f48-4d8c-830d-cc8d594dc11b
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
msc.type: authoredcontent
ms.openlocfilehash: 1cd03f3b396cc2ece1d8dbe6820f6277c02d8e62
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="preventing-cross-site-request-forgery-csrf-attacks-in-aspnet-web-api"></a>Prévention Cross-Site Request Forgery (CSRF) dans l’API Web ASP.NET
====================
par [Mike Wasson](https://github.com/MikeWasson)

Falsification de requête intersites (CSRF) est une attaque où un site malveillant envoie une demande à un site vulnérable où l’utilisateur est actuellement connecté

Voici un exemple d’une attaque CSRF :

1. Un utilisateur se connecte en www.example.com, à l’aide de l’authentification par formulaire.
2. Le serveur authentifie l’utilisateur. La réponse du serveur inclut un cookie d’authentification.
3. Sans la déconnexion, l’utilisateur visite un site web malveillant. Ce site malveillant contient le formulaire HTML suivant : 

    [!code-html[Main](preventing-cross-site-request-forgery-csrf-attacks/samples/sample1.html)]

    Notez que l’action de formulaire valide sur le site vulnérable, pas pour le site malveillant. Il s’agit de la partie « cross-site » de CSRF.
4. L’utilisateur clique sur le bouton Envoyer. Le navigateur inclut le cookie d’authentification avec la demande.
5. La requête s’exécute sur le serveur avec le contexte de l’utilisateur d’authentification et peut faire tout ce qu’un utilisateur authentifié est autorisé à faire.

Bien que cet exemple requiert que l’utilisateur de cliquer sur le bouton du formulaire, la page malveillante pourrait aussi facilement exécuter un script qui envoie le formulaire automatiquement. En outre, l’utilisation de SSL n’empêche pas une attaque CSRF, car le site malveillant peut envoyer une demande de « https:// ».

En règle générale, les attaques CSRF sont possibles contre les sites web qui utilisent des cookies pour l’authentification, étant donné que les navigateurs envoient tous les cookies pertinentes pour le site de destination. Toutefois, les attaques CSRF n’êtes pas limités pour exploiter les cookies. Par exemple, l’authentification de base et Digest sont également vulnérables. Après un utilisateur se connecte avec l’authentification de base ou Digest. le navigateur envoie automatiquement les informations d’identification jusqu'à ce que la session se termine.

## <a name="anti-forgery-tokens"></a>Les jetons anti-contrefaçon

Pour aider à empêcher les attaques par CSRF, ASP.NET MVC utilise les jetons anti-contrefaçon, également appelés *demander des jetons de vérification*.

1. Le client demande une page HTML contenant un formulaire.
2. Le serveur comprend deux jetons dans la réponse. Un jeton est envoyé en tant que cookie. L’autre est placé dans un champ de formulaire masqué. Les jetons sont générés au hasard afin qu’un adversaire ne peut pas deviner les valeurs.
3. Lorsque le client envoie le formulaire, il doit envoyer les deux jetons sur le serveur. Le client envoie le jeton de cookie en tant que cookie, et il envoie le jeton de formulaire à l’intérieur des données de formulaire. (Un client de navigateur fait automatiquement lorsque l’utilisateur envoie le formulaire.)
4. Si une demande n’inclut pas les deux jetons, le serveur n’autorise pas la demande.

Voici un exemple d’un formulaire HTML avec un jeton de formulaire masqué :

[!code-html[Main](preventing-cross-site-request-forgery-csrf-attacks/samples/sample2.html)]

Les jetons anti-contrefaçon fonctionnent, car la page malveillante ne peut pas lire les jetons de l’utilisateur, en raison des stratégies de la même origine. ([Des stratégies de même origine](http://www.w3.org/Security/wiki/Same_Origin_Policy) empêcher les documents hébergés sur deux sites différents d’accéder au contenu de l’autre. Par conséquent, dans l’exemple précédent, la page malveillante peut envoyer des demandes à exemple.com, mais il ne peut pas lire la réponse.)

Pour empêcher des attaques CSRF, utilisez les jetons anti-contrefaçon avec tout protocole d’authentification où le navigateur envoie en mode silencieux les informations d’identification une fois que l’utilisateur se connecte. Cela inclut les protocoles de l’authentification basée sur les cookies, telles que l’authentification par formulaire, ainsi que des protocoles tels que l’authentification de base et Digest.

Vous devez demander les jetons anti-contrefaçon toutes les méthodes nonsafe (POST, PUT, DELETE). En outre, assurez-vous que les méthodes sans échec (GET, HEAD) n’ont pas de tous les effets. En outre, si vous activez la prise en charge des domaines, tels que CORS ou JSONP, même sans échec méthodes telles que GET sont potentiellement exposés aux attaques CSRF, l’attaquant lire des données potentiellement sensibles.

## <a name="anti-forgery-tokens-in-aspnet-mvc"></a>Les jetons anti-contrefaçon dans ASP.NET MVC

Pour ajouter les jetons anti-contrefaçon vers une page Razor, utilisez le **HtmlHelper.AntiForgeryToken** méthode d’assistance :

[!code-cshtml[Main](preventing-cross-site-request-forgery-csrf-attacks/samples/sample3.cshtml)]

Cette méthode ajoute le champ de formulaire masqué et définit également le jeton de cookie.

## <a name="anti-csrf-and-ajax"></a>Anti-CSRF et AJAX

Le jeton de formulaire peut être un problème pour les requêtes AJAX, car une requête AJAX peut envoyer des données JSON, pas les données de formulaire HTML. Une solution consiste à envoyer les jetons dans un en-tête HTTP personnalisé. Le code suivant utilise la syntaxe Razor pour générer les jetons, puis ajoute les jetons à une requête AJAX. Les jetons sont générés sur le serveur en appelant **AntiForgery.GetTokens**.

[!code-html[Main](preventing-cross-site-request-forgery-csrf-attacks/samples/sample4.html)]

Lorsque vous traitez la demande, extraire les jetons à partir de l’en-tête de demande. Appelez ensuite la **AntiForgery.Validate** méthode pour valider les jetons. Le **Validate** méthode lève une exception si les jetons ne sont pas valides.

[!code-csharp[Main](preventing-cross-site-request-forgery-csrf-attacks/samples/sample5.cs)]

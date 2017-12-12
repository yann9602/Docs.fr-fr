---
uid: web-api/overview/security/basic-authentication
title: "L’authentification de base dans l’API Web ASP.NET | Documents Microsoft"
author: MikeWasson
description: "Décrit l’utilisation de l’authentification de base dans l’API Web ASP.NET."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/02/2014
ms.topic: article
ms.assetid: 41423767-0021-47c3-9e53-0021b457c39f
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/security/basic-authentication
msc.type: authoredcontent
ms.openlocfilehash: 4b8e6410668b2db289488bb4b6cd26d881e70f4c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="basic-authentication-in-aspnet-web-api"></a>Authentification de base dans l’API Web ASP.NET
====================
par [Mike Wasson](https://github.com/MikeWasson)

L’authentification de base est définie dans [RFC 2617, l’authentification HTTP : authentification de base et Digest accès](http://www.ietf.org/rfc/rfc2617.txt).

Inconvénients

- Informations d’identification utilisateur sont envoyées dans la demande.
- Informations d’identification sont envoyées en texte clair.
- Informations d’identification sont envoyées avec chaque demande.
- Aucun moyen de se déconnecter, sauf à la fin de la session de navigateur.
- Vulnérable aux intersites falsification de requête intersites (CSRF) ; nécessite les mesures anti-CSRF.

Avantages

- Standard d’Internet.
- Prise en charge par tous les principaux navigateurs.
- Protocole relativement simple.

L’authentification de base fonctionne comme suit :

1. Si une demande nécessite une authentification, le serveur retourne 401 (non autorisé). La réponse inclut un en-tête WWW-Authenticate, indiquant que le serveur prend en charge l’authentification de base.
2. Le client envoie une autre demande, avec les informations d’identification du client dans l’en-tête d’autorisation. Les informations d’identification sont mises en forme en tant que la chaîne « nom : mot de passe », codé en base64. Les informations d’identification ne sont pas chiffrées.

L’authentification de base est effectuée dans le contexte d’un « domaine ». Le serveur inclut le nom du domaine dans l’en-tête WWW-Authenticate. Les informations d’identification sont valides dans ce domaine. L’étendue exacte d’un domaine est définie par le serveur. Par exemple, vous pouvez définir plusieurs domaines dans l’ordre aux ressources de la partition.

![](basic-authentication/_static/image1.png)

Étant donné que les informations d’identification sont envoyées non chiffrées, l’authentification de base n’est sécurisée via le protocole HTTPS. Consultez [utilisation de SSL dans l’API Web](working-with-ssl-in-web-api.md).

L’authentification de base est également vulnérable aux attaques CSRF. Une fois que l’utilisateur entre les informations d’identification, le navigateur envoie automatiquement les sur les demandes suivantes au même domaine, pour la durée de la session. Cela inclut les requêtes AJAX. Consultez [empêcher les attaques de Cross-Site Request Forgery (CSRF)](preventing-cross-site-request-forgery-csrf-attacks.md).

## <a name="basic-authentication-with-iis"></a>Authentification de base avec IIS

IIS prend en charge l’authentification de base, mais il existe un inconvénient : l’utilisateur est authentifié par rapport à leurs informations d’identification Windows. Cela signifie que l’utilisateur doit avoir un compte sur le domaine du serveur. Pour un site web accessible au public, vous souhaiterez généralement s’authentifier auprès d’un fournisseur d’appartenances ASP.NET.

Pour activer l’authentification de base à l’aide d’IIS, définissez le mode d’authentification pour « Windows » dans le fichier Web.config de votre projet ASP.NET :

[!code-xml[Main](basic-authentication/samples/sample1.xml)]

Dans ce mode, IIS utilise les informations d’identification Windows pour s’authentifier. En outre, vous devez activer l’authentification de base dans IIS. Dans le Gestionnaire IIS, accédez à l’affichage des fonctionnalités, sélectionnez l’authentification et activer l’authentification de base.

![](basic-authentication/_static/image2.png)

Dans votre projet d’API Web, ajoutez le `[Authorize]` attribut pour toutes les actions de contrôleur qui nécessitent une authentification.

Un client s’authentifie en définissant l’en-tête d’autorisation dans la demande. Les clients de navigateur effectuent cette étape automatiquement. Les clients de nonbrowser devez définir l’en-tête.

## <a name="basic-authentication-with-custom-membership"></a>Authentification de base d’appartenance personnalisée

Comme indiqué, l’authentification de base intégré à IIS utilise les informations d’identification Windows. Cela signifie que vous avez besoin créer des comptes pour vos utilisateurs sur le serveur d’hébergement. Mais pour une application internet, les comptes d’utilisateur sont généralement stockés dans une base de données externe.

Le code suivant comment un module HTTP qui effectue l’authentification de base. Vous pouvez facilement incorporer un fournisseur d’appartenances ASP.NET en remplaçant le `CheckPassword` (méthode), qui est une méthode factice dans cet exemple.

Dans l’API Web 2, vous devez envisager d’écrire un [filtre d’authentification](authentication-filters.md) ou [intergiciel (middleware) OWIN](../../../aspnet/overview/owin-and-katana/index.md), au lieu d’un module HTTP.

[!code-csharp[Main](basic-authentication/samples/sample2.cs)]

Pour activer le module HTTP, ajoutez le code suivant à votre fichier web.config dans le **system.webServer** section :

[!code-xml[Main](basic-authentication/samples/sample3.xml?highlight=4)]

Remplacez « YourAssemblyName » par le nom de l’assembly (sans l’extension « dll »).

Vous devez désactiver les autres schémas d’authentification, tels que des connexions avec authentification Forms ou Windows

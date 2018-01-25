---
title: "Fonctionnalités de demande dans ASP.NET Core"
author: ardalis
description: "En savoir plus sur les détails d’implémentation de serveur web liés aux requêtes HTTP et les réponses qui sont définies dans les interfaces pour ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/request-features
ms.openlocfilehash: f0e371f5ea6c6688ef32adcacf667a412e4625e5
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="request-features-in-aspnet-core"></a>Fonctionnalités de demande dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

Les détails d’implémentation d’un serveur web relatifs aux requêtes HTTP et aux réponses sont définis dans les interfaces. Ces interfaces sont utilisées par les implémentations de serveur et d’intergiciel (middleware) pour créer et modifier le pipeline d’hébergement de l’application.

## <a name="feature-interfaces"></a>Interfaces de fonctionnalité

ASP.NET Core définit un nombre d’interfaces de fonctionnalité HTTP dans `Microsoft.AspNetCore.Http.Features` utilisés par les serveurs pour identifier les fonctionnalités prises en charge. Les interfaces suivantes de la fonctionnalité gérer les demandes et renvoient des réponses :

`IHttpRequestFeature`Définit la structure d’une requête HTTP, y compris le protocole de chemin d’accès, chaîne de requête, en-têtes et corps.

`IHttpResponseFeature`Définit la structure d’une réponse HTTP, y compris le code d’état, en-têtes et corps de la réponse.

`IHttpAuthenticationFeature`Définit la prise en charge pour l’identification des utilisateurs basés sur un `ClaimsPrincipal` et en spécifiant un gestionnaire d’authentification.

`IHttpUpgradeFeature`Définit la prise en charge pour [mises à niveau HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), qui permettent au client de spécifier les autres protocoles qu’elle souhaite utiliser si le serveur souhaite faire basculer les protocoles.

`IHttpBufferingFeature`Définit les méthodes permettant de désactiver la mise en mémoire tampon des réponses aux demandes et/ou.

`IHttpConnectionFeature`Définit les propriétés pour les ports et les adresses locales et distantes.

`IHttpRequestLifetimeFeature`Définit la prise en charge de l’abandon de connexions, ou détecter si une demande a été arrêtée prématurément, tels que par une déconnexion du client.

`IHttpSendFileFeature`Définit une méthode pour envoyer des fichiers de façon asynchrone.

`IHttpWebSocketFeature`Définit une API de prise en charge de WebSocket.

`IHttpRequestIdentifierFeature`Ajoute une propriété qui peut être implémentée pour identifier les demandes de façon unique.

`ISessionFeature`Définit `ISessionFactory` et `ISession` abstractions pour prendre en charge les sessions utilisateur.

`ITlsConnectionFeature`Définit une API pour la récupération des certificats clients.

`ITlsTokenBindingFeature`Définit des méthodes pour travailler avec des paramètres de liaison de jeton TLS.

> [!NOTE]
> `ISessionFeature`n’est pas une fonctionnalité de serveur, mais est implémenté par le `SessionMiddleware` (consultez [gestion d’état Application](app-state.md)).

## <a name="feature-collections"></a>Collections de fonctionnalité

Le `Features` propriété du `HttpContext` fournit une interface pour l’obtention et définition des fonctionnalités HTTP disponibles pour la requête actuelle. Étant donné que la collection de la fonctionnalité est mutable même dans le contexte d’une demande, intergiciel (middleware) peut servir à modifier la collection et d’ajouter la prise en charge des fonctionnalités supplémentaires.

## <a name="middleware-and-request-features"></a>Fonctionnalités d’intergiciel (middleware) et de la demande

Alors que les serveurs sont responsables de la création de la collection de fonctionnalité, intergiciel (middleware) peut ajouter à cette collection et utilisent les fonctionnalités de la collection. Par exemple, le `StaticFileMiddleware` accède à la `IHttpSendFileFeature` fonctionnalité. Si la fonction existe, il est utilisé pour envoyer le fichier statique demandé à partir de son chemin d’accès physique. Sinon, une autre méthode plus lente est utilisée pour envoyer le fichier. Lorsqu’il est disponible, le `IHttpSendFileFeature` permet au système d’exploitation ouvrir le fichier et effectuer une copie du mode noyau direct à la carte réseau.

En outre, intergiciel (middleware) peut ajouter à la collection de fonctionnalité établie par le serveur. Fonctionnalités existantes peuvent même être remplacées par un intergiciel (middleware), ce qui permet de l’intergiciel (middleware) compléter les fonctionnalités du serveur. Fonctionnalités ajoutées à la collection sont disponibles immédiatement pour les autres intergiciel (middleware) ou l’application sous-jacente plus loin dans le pipeline de requête.

En combinant les implémentations de serveur personnalisé et des améliorations de l’intergiciel (middleware) spécifique, le jeu précis d’une application requiert des fonctionnalités peut être construit. Ainsi, manquantes fonctionnalités pour être ajoutés sans nécessiter une modification de serveur et permet de s’assurer que la quantité minimale de fonctionnalités sont exposées, ce qui limite les attaques de surface zone et améliorer les performances.

## <a name="summary"></a>Récapitulatif

Fonctionnalité interfaces définissent des fonctionnalités HTTP spécifiques une demande donnée peut prendre en charge. Serveurs définissent des ensembles de fonctionnalités et l’ensemble initial des fonctionnalités prises en charge par ce serveur, mais l’intergiciel (middleware) peut être utilisé pour améliorer ces fonctionnalités.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Serveurs](servers/index.md)

* [Intergiciel (middleware)](middleware.md)

* [OWIN (Open Web Interface pour .NET)](owin.md)

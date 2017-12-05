---
title: "Implémentations de serveurs web dans ASP.NET Core"
author: tdykstra
description: "Présente les serveurs web Kestrel et WebListener pour ASP.NET Core. Vous aide à déterminer quand les choisir et à quel moment les utiliser avec un serveur proxy inverse."
keywords: ASP.NET Core, IServer, serveur web, Kestrel, WebListener, proxy inverse
ms.author: tdykstra
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.assetid: dba74f39-58cd-4dee-a061-6d15f7346959
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/index
ms.openlocfilehash: 04dee100dff91f7868175ff4be01156787e13e81
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="web-server-implementations-in-aspnet-core"></a>Implémentations de serveurs web dans ASP.NET Core

De [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) et [Chris Ross](https://github.com/Tratcher)

Une application ASP.NET Core s’exécute avec une implémentation de serveur HTTP in-process. L’implémentation du serveur écoute les requêtes HTTP et les expose à l’application sous forme de [fonctionnalités de requêtes](https://docs.microsoft.com/aspnet/core/fundamentals/request-features) composées dans `HttpContext`.

ASP.NET Core est livré avec deux implémentations de serveurs :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

* [Kestrel](kestrel.md) est un serveur HTTP multiplateforme basé sur [libuv](https://github.com/libuv/libuv), une bibliothèque d’E/S asynchrones multiplateforme.

* [HTTP.sys](httpsys.md) est un serveur HTTP exclusivement Windows basé sur le [pilote de noyau Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* [Kestrel](kestrel.md) est un serveur HTTP multiplateforme basé sur [libuv](https://github.com/libuv/libuv), une bibliothèque d’E/S asynchrones multiplateforme.

* [WebListener](weblistener.md) est un serveur HTTP exclusivement Windows basé sur le [pilote de noyau Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).

---

## <a name="kestrel"></a>Kestrel

Kestrel est le serveur web inclus par défaut dans les modèles de nouveaux projets ASP.NET Core. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Vous pouvez utiliser uniquement Kestrel ou l’associer à un *serveur proxy inverse*, par exemple IIS, Nginx ou Apache. Un serveur proxy inverse reçoit les requêtes HTTP en provenance d’Internet et les transmet à Kestrel après un traitement préliminaire.

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

L’une ou l’autre des configurations, &mdash;avec ou sans serveur proxy inverse&mdash;, peut également être utilisée si Kestrel est exposé uniquement à un réseau interne.

Pour plus d’informations sur l’utilisation de Kestrel avec un proxy inverse, consultez [Présentation de Kestrel](kestrel.md).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Si votre application accepte uniquement les requêtes d’un réseau interne, vous pouvez utiliser Kestrel de manière autonome.

![Kestrel communique directement avec votre réseau interne](kestrel/_static/kestrel-to-internal.png)

Si vous exposez votre application à Internet, vous devez utiliser IIS, Nginx ou Apache en tant que *serveur proxy inverse*. Un serveur proxy inverse reçoit les requêtes HTTP en provenance d’Internet et les transmet à Kestrel après un traitement préliminaire, comme illustré dans le diagramme suivant.

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

La raison la plus importante qui justifie l’utilisation d’un proxy inverse pour les déploiements Edge (exposés au trafic en provenance d’Internet) est la sécurité. Les versions 1.x de Kestrel n’ont pas un ensemble complet de mesures de défense contre les attaques. Cela inclut, entre autres choses, les délais d’attente appropriés, les limites de taille et les limites de connexions simultanées.

Pour plus d’informations sur l’utilisation de Kestrel avec un proxy inverse, consultez [Présentation de Kestrel](kestrel.md).

---

Vous ne pouvez pas utiliser IIS, Nginx ou Apache sans Kestrel ou une [implémentation de serveur personnalisée](#custom-servers). ASP.NET Core a été conçu pour s’exécuter dans son propre processus et se comporter de manière cohérente entre les plateformes. IIS, Nginx et Apache dictent leur propre processus et environnement de démarrage. Pour les utiliser directement, ASP.NET Core doit s’adapter aux besoins de chacun d’eux. L’utilisation d’une implémentation de serveur web telle que celle de Kestrel donne à ASP.NET Core un contrôle du processus et de l’environnement de démarrage. Ainsi, plutôt que d’essayer d’adapter ASP.NET Core à IIS, Nginx ou Apache, configurez simplement ces serveurs web en serveur proxy des requêtes vers Kestrel. Grâce à cette solution, vos classes `Program.Main` et `Startup` restent essentiellement les mêmes, quel que soit l’emplacement de déploiement.

### <a name="iis-with-kestrel"></a>IIS avec Kestrel

Quand vous utilisez IIS ou IIS Express en tant que proxy inverse pour ASP.NET Core, l’application ASP.NET Core s’exécute dans un processus distinct du processus de travail IIS. Dans le processus IIS, un module IIS spécial s’exécute pour coordonner la relation du proxy inverse.  Il s’agit du *module ASP.NET Core*. Les principales fonctions du module ASP.NET Core consistent à démarrer l’application ASP.NET Core, la redémarrer quand elle se bloque et lui transférer le trafic HTTP. Pour plus d’informations, consultez [Module ASP.NET Core](aspnet-core-module.md). 

### <a name="nginx-with-kestrel"></a>Nginx avec Kestrel

Pour plus d’informations sur l’utilisation de Nginx sur Linux en tant que serveur proxy inverse pour Kestrel, consultez [Publier sur un environnement de production Linux](../../publishing/linuxproduction.md).

### <a name="apache-with-kestrel"></a>Apache avec Kestrel

Pour plus d’informations sur l’utilisation d’Apache sur Linux en tant que serveur proxy inverse pour Kestrel, consultez [Utilisation du serveur web Apache en tant que proxy inverse](../../publishing/apache-proxy.md).

## <a name="httpsys"></a>HTTP.sys

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Si vous exécutez votre application ASP.NET Core sur Windows, HTTP.sys est une alternative à Kestrel. Vous pouvez utiliser HTTP.sys dans les scénarios où vous exposez votre application à Internet et où vous avez besoin des fonctionnalités HTTP.sys que Kestrel ne prend pas en charge. 

![HTTP.sys communique directement avec Internet](httpsys/_static/httpsys-to-internet.png)

HTTP.sys peut également être utilisé pour les applications qui sont exposées uniquement à un réseau interne. 

![HTTP.sys communique directement avec votre réseau interne](httpsys/_static/httpsys-to-internal.png)

Dans le cas des réseaux internes, Kestrel est généralement recommandé pour obtenir les meilleures performances. Toutefois, dans certains scénarios, vous pouvez être amené à utiliser une fonctionnalité uniquement offerte par HTTP.sys. Pour plus d’informations sur les fonctionnalités de HTTP.sys, consultez [HTTP.sys](httpsys.md).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

HTTP.sys se nomme WebListener dans ASP.NET Core 1.x. Si vous exécutez votre application ASP.NET Core sur Windows, WebListener est une alternative que vous pouvez utiliser dans les scénarios où vous souhaitez exposer votre application à Internet, mais où vous ne pouvez pas recourir à IIS.

![WebListener communique directement avec Internet](weblistener/_static/weblistener-to-internet.png)

WebListener peut également être utilisé à la place de Kestrel pour les applications exposées uniquement à un réseau interne, si vous avez besoin de fonctionnalités WebListener non prises en charge par Kestrel. 

![WebListener communique directement avec votre réseau interne](weblistener/_static/weblistener-to-internal.png)

Dans le cas des réseaux internes, Kestrel est généralement recommandé pour obtenir les meilleures performances. Toutefois, dans certains scénarios, vous pouvez être amené à utiliser une fonctionnalité uniquement offerte par WebListener. Pour plus d’informations sur les fonctionnalités de WebListener, consultez [WebListener](weblistener.md).

---

## <a name="notes-about-aspnet-core-server-infrastructure"></a>Remarques sur l’infrastructure serveur d’ASP.NET Core

Le [`IApplicationBuilder`](/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder) disponible dans la méthode `Configure` de la classe `Startup` expose la propriété `ServerFeatures` de type [`IFeatureCollection`](/aspnet/core/api/microsoft.aspnetcore.http.features.ifeaturecollection). Kestrel et WebListener exposent uniquement la fonctionnalité [`IServerAddressesFeature`](/aspnet/core/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature) mais les autres implémentations de serveurs peuvent exposer des fonctionnalités supplémentaires.

`IServerAddressesFeature` permet de déterminer quel est le port lié à l’implémentation de serveur au moment de l’exécution.

## <a name="custom-servers"></a>Serveurs personnalisés

Si les serveurs intégrés ne répondent pas à vos besoins, vous pouvez créer une implémentation de serveur personnalisé. Le [guide OWIN (Open Web Interface pour .NET)](../owin.md) montre comment écrire une implémentation [IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) basée sur [Nowin](https://github.com/Bobris/Nowin). Vous êtes libre d’implémenter uniquement les interfaces de fonctionnalités dont votre application a besoin. Toutefois, vous devez assurer au minimum la prise en charge de [IHttpRequestFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttprequestfeature) et [IHttpResponseFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpresponsefeature).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, reportez-vous aux ressources suivantes :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

- [Kestrel](kestrel.md)
- [Kestrel avec IIS](aspnet-core-module.md)
- [Kestrel avec Nginx](../../publishing/linuxproduction.md)
- [Kestrel avec Apache](../../publishing/apache-proxy.md)
- [HTTP.sys](httpsys.md)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

- [Kestrel](kestrel.md)
- [Kestrel avec IIS](aspnet-core-module.md)
- [Kestrel avec Nginx](../../publishing/linuxproduction.md)
- [Kestrel avec Apache](../../publishing/apache-proxy.md)
- [WebListener](weblistener.md)

---

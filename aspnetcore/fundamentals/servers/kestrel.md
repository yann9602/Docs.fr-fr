---
title: "Implémentation du serveur web kestrel ASP.NET Core"
author: tdykstra
description: "Introduit Kestrel, le serveur web d’inter-plateformes pour ASP.NET Core selon libuv."
ms.author: tdykstra
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/kestrel
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3695a6a127f77bd90538d72af6112ccf507f3482
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="introduction-to-kestrel-web-server-implementation-in-aspnet-core"></a>Introduction à l’implémentation du serveur web Kestrel ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), et [Stephen Halter](https://twitter.com/halter73)

Kestrel est un inter-plateforme [serveur web pour ASP.NET Core](index.md) selon [libuv](https://github.com/libuv/libuv), une bibliothèque d’e/s asynchrones inter-plateformes. Kestrel est le serveur web qui est inclus par défaut dans les modèles de projet ASP.NET Core. 

Kestrel prend en charge les fonctionnalités suivantes :

  * HTTPS
  * Permet d’activer de mise à niveau opaque [WebSockets](https://github.com/aspnet/websockets)
  * Sockets UNIX pour des performances élevées derrière Nginx 

Kestrel est pris en charge sur toutes les plateformes et les versions qui prend en charge de .NET Core.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[Afficher ou télécharger l’exemple de code pour 2.x](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample2) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[Afficher ou télécharger l’exemple de code pour 1.x](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample1) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))

---

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Quand utiliser Kestrel avec un proxy inverse

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Vous pouvez utiliser uniquement Kestrel ou l’associer à un *serveur proxy inverse*, par exemple IIS, Nginx ou Apache. Un serveur proxy inverse reçoit les requêtes HTTP en provenance d’Internet et les transmet à Kestrel après un traitement préliminaire.

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

L’une ou l’autre des configurations, &mdash;avec ou sans serveur proxy inverse&mdash;, peut également être utilisée si Kestrel est exposé uniquement à un réseau interne.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Si votre application accepte uniquement les requêtes d’un réseau interne, vous pouvez utiliser Kestrel de manière autonome.

![Kestrel communique directement avec votre réseau interne](kestrel/_static/kestrel-to-internal.png)

Si vous exposez votre application à Internet, vous devez utiliser IIS, Nginx ou Apache en tant que *serveur proxy inverse*. Un serveur proxy inverse reçoit les requêtes HTTP en provenance d’Internet et les transmet à Kestrel après un traitement préliminaire.

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

Un proxy inverse est requis pour les déploiements de bord (exposés au trafic Internet) pour des raisons de sécurité. Les versions 1.x de Kestrel n’ont pas un ensemble complet de mesures de défense contre les attaques. Cela inclut, mais n’est pas limité à des délais d’attente appropriée, les limites de taille et limite de connexions simultanées.

---

Un scénario qui nécessite un proxy inverse est lorsque vous possédez plusieurs applications qui partagent la même adresse IP et le même port en cours d’exécution sur un seul serveur. Cela ne fonctionne pas avec Kestrel directement Kestrel ne gère pas l’adresse IP et un port entre plusieurs processus même de partage. Lorsque vous configurez Kestrel à écouter un port, il gère tout le trafic pour ce port, quelle que soit l’en-tête d’hôte. Un proxy inverse qui permettre partager des ports doit ensuite transmettre à Kestrel sur une adresse IP et un port unique.

Même si un serveur proxy inverse n’est pas nécessaire, à l’aide d’un peut être un bon choix pour d’autres raisons :

* Il peut limiter votre surface exposée.
* Il fournit une couche supplémentaire facultatif de la configuration et de la défense.
* Il peut mieux intégrer à l’infrastructure existante.
* Elle simplifie la configuration SSL et l’équilibrage de charge. Seulement votre serveur proxy inverse requiert un certificat SSL, et ce serveur peut communiquer avec vos serveurs d’applications sur le réseau interne à l’aide de HTTP brut.

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Comment utiliser Kestrel dans les applications ASP.NET Core

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Le [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package est inclus dans le [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).

Modèles de projet ASP.NET Core utilisent Kestrel par défaut. Dans *Program.cs*, le modèle de code appelle `CreateDefaultBuilder`, qui appelle [UseKestrel](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions#Microsoft_AspNetCore_Hosting_WebHostBuilderKestrelExtensions_UseKestrel_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) en arrière-plan.

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Si vous avez besoin configurer les options de Kestrel, appelez `UseKestrel` dans *Program.cs* comme indiqué dans l’exemple suivant :

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_DefaultBuilder&highlight=9-16)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Installer le [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package NuGet.

Appelez le [UseKestrel](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions#Microsoft_AspNetCore_Hosting_WebHostBuilderKestrelExtensions_UseKestrel_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) méthode d’extension sur `WebHostBuilder` dans votre `Main` méthode, en spécifiant les [options de Kestrel](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions) dont vous avez besoin, comme indiqué dans la section suivante.

[!code-csharp[](kestrel/sample1/Program.cs?name=snippet_Main&highlight=13-19)]

---

### <a name="kestrel-options"></a>Options de kestrel

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Le serveur web Kestrel a des options de configuration de contrainte sont particulièrement utiles dans les déploiements exposés à Internet. Voici quelques-unes des limites que vous pouvez définir :

- Nombre maximale de connexions client
- Taille maximale du corps de la requête
- Débit données minimal du corps de la requête

Vous définissez ces contraintes et autres dans le `Limits` propriété de la [KestrelServerOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerOptions.cs) classe. Le `Limits` propriété conserve une instance de la [KestrelServerLimits](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerLimits.cs) classe. 

**Connexions clientes maximale**

Le nombre maximal de connexions TCP simultanées et ouvertes peut être défini pour l’application entière avec le code suivant :

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_Limits&highlight=3-4)]

Il existe une limite distincte pour les connexions qui ont été mis à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket). Une fois une connexion est mise à niveau, il n’est pas compté dans le `MaxConcurrentConnections` limite. 

Le nombre maximal de connexions est illimité (null) par défaut.

**Taille du corps de demande maximale**

La taille du corps de demande maximale par défaut est 30 000 000 octets, qui est d’environ 28,6 Mo. 

La méthode recommandée pour remplacer la limite dans une application ASP.NET MVC de base consiste à utiliser le [RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs) attribut sur une méthode d’action :

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Voici un exemple qui montre comment configurer la contrainte pour l’application entière, chaque demande :

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_Limits&highlight=5)]

Vous pouvez remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :

[!code-csharp[](kestrel/sample2/Startup.cs?name=snippet_Limits&highlight=3-4)]
 
Une exception est levée si vous essayez de configurer la limite sur une demande une fois que l’application a démarré la lecture de la demande. Il existe un `IsReadOnly` propriété qui indique si le `MaxRequestBodySize` propriété est en lecture seule, ce qui signifie qu’il est trop tard pour configurer la limite.

**Taux de données de corps de demande minimale**

Kestrel vérifie chaque seconde si les données arrivent à la vitesse spécifiée en octets/seconde. Si le taux est inférieur au minimum, la délai de connexion. La période de grâce est la quantité de temps que Kestrel donne le client à augmenter sa vitesse de transmission jusqu'à la limite minimale ; la vitesse n’est pas vérifiée pendant ce temps. La période de grâce permet d’éviter la suppression des connexions qui initialement envoient des données à une vitesse lente en raison de démarrage lent TCP.

La fréquence minimale par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.

Un taux minimum s’applique également à la réponse. Le code pour définir la limite de demandes et de la limite de la réponse est identique à l’exception des `RequestBody` ou `Response` dans les noms de propriété et d’interface. 

Voici un exemple qui montre comment configurer les taux de données minimal dans *Program.cs*:

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_Limits&highlight=6-9)]

Vous pouvez configurer les taux de demande dans l’intergiciel (middleware) :

[!code-csharp[](kestrel/sample2/Startup.cs?name=snippet_Limits&highlight=5-8)]

Pour plus d’informations sur les autres options Kestrel, consultez les classes suivantes :

* [KestrelServerOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerOptions.cs)
* [KestrelServerLimits](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerLimits.cs)
* [ListenOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/ListenOptions.cs)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Pour plus d’informations sur les options de Kestrel, consultez [KestrelServerOptions classe](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions).

---

### <a name="endpoint-configuration"></a>Configuration de point de terminaison

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Par défaut, ASP.NET Core lie à `http://localhost:5000`. Vous configurez des préfixes d’URL et ports pour Kestrel d’écouter en appelant `Listen` ou `ListenUnixSocket` méthodes sur `KestrelServerOptions`. (`UseUrls`, le `urls` argument de ligne de commande et la variable d’environnement ASPNETCORE_URLS fonctionne également mais ils présentent les limitations de noter [plus loin dans cet article](#useurls-limitations).)

**Lier à un socket TCP**

Le `Listen` méthode lie à un socket TCP, et une expression lambda options vous permet de configurer un certificat SSL :

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_DefaultBuilder&highlight=9-16)]

Notez la façon dont cet exemple configure le SSL pour un point de terminaison particulier à l’aide de [ListenOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/ListenOptions.cs). Vous pouvez utiliser la même API pour configurer les autres paramètres Kestrel des points de terminaison particuliers.

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

**Lier à un socket Unix**

Vous pouvez écouter sur un socket Unix pour améliorer les performances avec Nginx, comme illustré dans cet exemple :

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_UnixSocket)]

**Port 0**

Si vous spécifiez le numéro de port 0, Kestrel lie dynamiquement un port disponible. L’exemple suivant montre comment déterminer le port Kestrel effectivement lié à l’exécution :

[!code-csharp[](kestrel/sample2/Startup.cs?name=snippet_Configure&highlight=3,13,16-17)]

<a id="useurls-limitations"></a>

**Limitations de UseUrls**

Vous pouvez configurer des points de terminaison en appelant le `UseUrls` méthode ou à l’aide de la `urls` argument de ligne de commande ou la variable d’environnement ASPNETCORE_URLS. Ces méthodes sont utiles si vous souhaitez que votre code fonctionne avec les serveurs autres que Kestrel. Toutefois, tenez compte de ces limitations :

* Vous ne pouvez pas utiliser SSL avec ces méthodes.
* Si vous utilisez à la fois le `Listen` (méthode) et `UseUrls`, le `Listen` substituer des points de terminaison le `UseUrls` points de terminaison.

**Configuration de point de terminaison pour IIS**

Si vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons que vous définissez en appelant `Listen` ou `UseUrls`. Pour plus d’informations, consultez [Introduction au Module de base ASP.NET](aspnet-core-module.md).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Par défaut, ASP.NET Core lie à `http://localhost:5000`. Vous pouvez configurer des préfixes d’URL et ports pour Kestrel à écouter à l’aide de la `UseUrls` méthode d’extension, le `urls` argument de ligne de commande ou dans le système de configuration ASP.NET Core. Pour plus d’informations sur ces méthodes, consultez [hébergement](../../fundamentals/hosting.md). Pour plus d’informations sur le fonctionne de la liaison d’URL lorsque vous utilisez IIS comme un proxy inverse, consultez [ASP.NET Core Module](aspnet-core-module.md). 

---

### <a name="url-prefixes"></a>Préfixes d’URL

Si vous appelez `UseUrls` ou utilisez le `urls` argument de ligne de commande ou la variable d’environnement ASPNETCORE_URLS, les préfixes d’URL peuvent être l’un des formats suivants. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Seuls les préfixes d’URL HTTP sont valides ; Kestrel ne prend pas en charge SSL lorsque vous configurez les liaisons de l’URL à l’aide de `UseUrls`.

* Adresse IPv4 avec le numéro de port

  ```
  http://65.55.39.10:80/
  ```

  0.0.0.0 est un cas spécial qui est lié à toutes les adresses IPv4.


* Adresse IPv6 avec le numéro de port

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/ 
  ```

  [ :] équivaut à IPv6 IPv4 0.0.0.0.


* Nom d’hôte avec le numéro de port

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Héberger des noms, *, et +, ne sont pas spécifiques. Tout ce qui n’est pas une adresse IP ou le « localhost » reconnu est lié à tous les IPv4 et IPv6 des adresses IP. Si vous devez lier des noms d’hôte différents pour les applications ASP.NET Core différentes sur le même port, utilisez [HTTP.sys](httpsys.md) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.

* Nom de « Localhost » avec IP de bouclage ou de numéro de port avec le numéro de port

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Lorsque `localhost` est spécifié, Kestrel essaie de lier aux interfaces de bouclage IPv4 et IPv6. Si le port demandé est en cours d’utilisation par un autre service sur l’interface de bouclage, Kestrel ne démarre pas. Si l’interface de bouclage est indisponible pour une raison quelconque (la plupart des couramment car IPv6 n’est pas pris en charge), Kestrel consigne un avertissement. 

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* Adresse IPv4 avec le numéro de port

  ```
  http://65.55.39.10:80/
  https://65.55.39.10:443/
  ```

  0.0.0.0 est un cas spécial qui est lié à toutes les adresses IPv4.


* Adresse IPv6 avec le numéro de port

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/ 
  https://[0:0:0:0:0:ffff:4137:270a]:443/ 
  ```

  [ :] équivaut à IPv6 IPv4 0.0.0.0.


* Nom d’hôte avec le numéro de port

  ```
  http://contoso.com:80/
  http://*:80/
  https://contoso.com:443/
  https://*:443/
  ```

  Les noms, d’hôte \*, et + ne sont pas spéciales. Tout ce qui n’est pas une adresse IP ou le « localhost » reconnu est liée à tous les IPv4 et IPv6 des adresses IP. Si vous devez lier des noms d’hôte différents pour les applications ASP.NET Core différentes sur le même port, utilisez [WebListener](weblistener.md) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.

* Nom de « Localhost » avec IP de bouclage ou de numéro de port avec le numéro de port

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Lorsque `localhost` est spécifié, Kestrel essaie de lier aux interfaces de bouclage IPv4 et IPv6. Si le port demandé est en cours d’utilisation par un autre service sur l’interface de bouclage, Kestrel ne démarre pas. Si l’interface de bouclage est indisponible pour une raison quelconque (la plupart des couramment car IPv6 n’est pas pris en charge), Kestrel consigne un avertissement. 

* Socket d’UNIX

  ```
  http://unix:/run/dan-live.sock  
  ```

**Port 0**

Si vous spécifiez le numéro de port 0, Kestrel lie dynamiquement un port disponible. Liaison de port 0 est autorisée pour n’importe quel nom d’hôte ou l’adresse IP à l’exception de `localhost` nom.

L’exemple suivant montre comment déterminer le port Kestrel effectivement lié à l’exécution :

[!code-csharp[](kestrel/sample1/Startup.cs?name=snippet_Configure)]

**Préfixes d’URL pour le protocole SSL**

Veillez à inclure les préfixes d’URL avec `https:` si vous appelez le `UseHttps` méthode d’extension, comme indiqué ci-dessous.

```csharp
var host = new WebHostBuilder() 
    .UseKestrel(options => 
    { 
        options.UseHttps("testCert.pfx", "testPassword"); 
    }) 
   .UseUrls("http://localhost:5000", "https://localhost:5001") 
   .UseContentRoot(Directory.GetCurrentDirectory()) 
   .UseStartup<Startup>() 
   .Build(); 
```

> [!NOTE]
> HTTPS et HTTP ne peut pas être hébergé sur le même port.

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

---
## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, reportez-vous aux ressources suivantes :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

* [Exemple d’application pour 2.x](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample2)
* [Code source de kestrel](https://github.com/aspnet/KestrelHttpServer)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* [Exemple d’application pour 1.x](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample1)
* [Code source de kestrel](https://github.com/aspnet/KestrelHttpServer)

---

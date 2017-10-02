---
title: "Implémentation du serveur web HTTP.sys dans ASP.NET Core"
author: rick-anderson
description: "Introduit HTTP.sys, un serveur web pour ASP.NET Core sur Windows. Basé sur le pilote de mode noyau Http.Sys, HTTP.sys est une alternative à Kestrel qui peut être utilisé pour une connexion directe à Internet sans IIS."
keywords: "Préfixes ASP.NET Core,HttpSys,HTTP.sys,HttpListener,url, SSL"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: 0a7286e4-6428-424e-b5c4-5c98815cf61c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/httpsys
ms.openlocfilehash: d3f9eb4943ed62b674d6bb2ab1b275b0a3c02343
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2017
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Implémentation du serveur web HTTP.sys dans ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra) et [Ross de Chris](https://github.com/Tratcher)

> [!NOTE]
> Cette rubrique s’applique uniquement à ASP.NET Core 2.0 et versions ultérieures. Dans les versions antérieures d’ASP.NET Core, HTTP.sys est nommé [WebListener](xref:fundamentals/servers/weblistener).

HTTP.sys est un [serveur web pour ASP.NET Core](index.md) qui s’exécute uniquement sous Windows. Il repose sur le [pilote de mode noyau Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx). HTTP.sys est une alternative à [Kestrel](kestrel.md) qui offre des fonctionnalités qui ne de Kestel. **HTTP.sys ne peut pas être utilisée avec IIS ou IIS Express, car il n’est pas compatible avec le [ASP.NET Core Module](aspnet-core-module.md).**

HTTP.sys prend en charge les fonctionnalités suivantes :

- [Authentification Windows](xref:security/authentication/windowsauth)
- Le partage de port
- HTTPS avec SNI
- HTTP/2 sur TLS (Windows 10)
- Transmission de fichier direct
- Réponse mise en cache
- WebSockets (Windows 8)

Versions de Windows prises en charge :

- Windows 7 et Windows Server 2008 R2 et versions ultérieures

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Quand utiliser HTTP.sys

HTTP.sys est utile pour les déploiements où vous devez exposer le serveur directement à Internet sans l’aide d’IIS.

![HTTP.sys communique directement avec Internet](httpsys/_static/httpsys-to-internet.png)

Comme il repose sur Http.Sys, HTTP.sys ne nécessite pas un serveur proxy inverse pour la protection contre les attaques. Http.Sys est une technologie mature qui protège contre les nombreux types d’attaques et fournit la fiabilité, la sécurité et l’évolutivité d’un serveur web complet. IIS s’exécute comme un écouteur HTTP sur Http.Sys. 

HTTP.sys est un bon choix pour les déploiements internes lorsque vous avez besoin d’une fonctionnalité non disponible dans Kestrel, telles que l’authentification Windows.

![HTTP.sys communique directement avec votre réseau interne](httpsys/_static/httpsys-to-internal.png)

## <a name="how-to-use-httpsys"></a>L’utilisation de HTTP.sys

Voici une vue d’ensemble des tâches de configuration pour le système d’exploitation hôte et de votre application ASP.NET Core.

### <a name="configure-windows-server"></a>Configurer Windows Server

* Installez la version de .NET requis par votre application, tel que [.NET Core](https://www.microsoft.com/net/download/core) ou [.NET Framework](https://www.microsoft.com/net/download/framework).

* Préenregistrer des préfixes d’URL pour lier à HTTP.sys et configurer des certificats SSL

   Si vous ne préenregistrer des préfixes d’URL dans Windows, vous devez exécuter votre application avec des privilèges d’administrateur. La seule exception est si vous liez à localhost, à l’aide de HTTP pas HTTPS avec un numéro de port supérieur à 1 024 ; Dans ce cas, des privilèges d’administrateur ne sont pas nécessaires.

   Pour plus d’informations, consultez [préenregistrer préfixes et de configuration SSL](#preregister-url-prefixes-and-configure-ssl) plus loin dans cet article.

* Ouvrir les ports du pare-feu pour autoriser le trafic atteindre HTTP.sys.

   Vous pouvez utiliser *netsh.exe* ou [applets de commande PowerShell](https://technet.microsoft.com/library/jj554906).

Il existe également [paramètres de Registre Http.Sys](https://support.microsoft.com/kb/820129).

### <a name="configure-your-aspnet-core-application-to-use-httpsys"></a>Configurer votre application ASP.NET Core utilise HTTP.sys

* Aucune installation de package n’est nécessaire si vous utilisez la [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage. Le [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) package est inclus dans le metapackage.

* Appelez le `UseHttpSys` méthode d’extension sur `WebHostBuilder` dans votre `Main` méthode, en spécifiant les [HTTP.sys options](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs) dont vous avez besoin, comme indiqué dans l’exemple suivant :

  [!code-csharp[](httpsys/sample/Program.cs?name=snippet_Main&highlight=11-19)]

### <a name="configure-httpsys-options"></a>Configurer les options de HTTP.sys

Voici quelques-unes des limites que vous pouvez configurer les paramètres de HTTP.sys.

**Connexions clientes maximale**

Le nombre maximal de connexions TCP simultanées et ouvertes peut être défini pour l’application entière avec le code suivant dans *Program.cs*:

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Options&highlight=5)]

Le nombre maximal de connexions est illimité (null) par défaut.

**Taille du corps de demande maximale**

La taille du corps de demande maximale par défaut est 30 000 000 octets, qui est d’environ 28,6 Mo.

La méthode recommandée pour remplacer la limite dans une application ASP.NET MVC de base consiste à utiliser le [RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs) attribut sur une méthode d’action :

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Voici un exemple qui montre comment configurer la contrainte pour l’application entière, chaque demande :

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Options&highlight=6)]

Vous pouvez remplacer le paramètre sur une demande spécifique dans *Startup.cs*:

[!code-csharp[](httpsys/sample/Startup.cs?name=snippet_Configure&highlight=9-10)]
 
Une exception est levée si vous essayez de configurer la limite sur une demande une fois que l’application a démarré la lecture de la demande. Il existe un `IsReadOnly` propriété qui indique si le `MaxRequestBodySize` propriété est en lecture seule, ce qui signifie qu’il est trop tard pour configurer la limite.

Pour plus d’informations sur les autres options de HTTP.sys, consultez [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs). 

### <a name="configure-urls-and-ports-to-listen-on"></a>Configurer les ports et les URL pour écouter sur 

Par défaut, ASP.NET Core lie à `http://localhost:5000`. Pour configurer les ports et les préfixes d’URL, vous pouvez utiliser la `UseUrls` méthode d’extension, le `urls` argument de ligne de commande, la variable d’environnement ASPNETCORE_URLS ou `UrlPrefixes` propriété sur [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs). Le code suivant utilise des exemple `UrlPrefixes`.

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Main&highlight=17)]

Un avantage de `UrlPrefixes` est que vous obtenez un message d’erreur immédiatement si vous essayez d’ajouter un préfixe au format incorrect. Un avantage de `UseUrls` (partagé avec `urls` et ASPNETCORE_URLS) que vous pouvez plus facilement basculer entre Kestrel et HTTP.sys.

Si vous utilisez les deux `UseUrls` (ou `urls` ou ASPNETCORE_URLS) et `UrlPrefixes`, les paramètres de `UrlPrefixes` remplacent celles de `UseUrls`. Pour plus d’informations, consultez [hébergement](xref:fundamentals/hosting).

HTTP.sys utilise le [formats de chaîne HTTP Server API UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

> [!NOTE]
> Assurez-vous que vous spécifiez les mêmes chaînes de préfixe dans `UseUrls` ou `UrlPrefixes` qui vous préenregistrer sur le serveur. 

### <a name="dont-use-iis"></a>N’utilisez pas IIS

Assurez-vous que votre application n’est pas configurée pour exécuter les services IIS ou IIS Express.

Dans Visual Studio, le profil de démarrage par défaut est pour IIS Express. Pour exécuter le projet en tant qu’une application console, modifiez manuellement le profil sélectionné, comme indiqué dans la capture d’écran suivante.

![Sélectionnez le profil d’application console](httpsys/_static/vs-choose-profile.png)

## <a name="preregister-url-prefixes-and-configure-ssl"></a>Préenregistrer des préfixes d’URL et de configurer le protocole SSL

IIS et HTTP.sys s’appuient sur le pilote sous-jacent de mode noyau Http.Sys pour écouter les demandes et le traitement initiale. Dans IIS, l’interface utilisateur de gestion vous donne un moyen relativement facile à configurer tous les éléments. Toutefois, vous devez configurer Http.Sys vous-même. L’outil intégré permettant d’effectuer d’autres termes *netsh.exe*. 

Avec *netsh.exe* vous pouvez réserver des préfixes d’URL et attribuer des certificats SSL. L’outil nécessite des privilèges d’administrateur.

L’exemple suivant montre le minimum requis pour réserver des préfixes d’URL pour les ports 80 et 443 :

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

L’exemple suivant montre comment assigner un certificat SSL :

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}"
```

Voici la documentation de référence *netsh.exe*:

* [Commandes Netsh pour Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
* [Chaînes UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

Les ressources suivantes fournissent des instructions détaillées pour plusieurs scénarios. Les articles qui font référence à HttpListener s’appliquent également à HTTP.sys, comme les deux reposent sur Http.Sys.

* [Comment : configurer un Port avec un certificat SSL](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-configure-a-port-with-an-ssl-certificate)
* [Les communications HTTPS - HttpListener basé hébergement et la Certification Client](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) blog de tiers et est assez ancien mais dispose toujours des informations utiles.
* [Comment : HttpListener à l’aide de procédure pas à pas ou le serveur Http du code non managé (C++) comme serveur SSL Simple](https://blogs.msdn.microsoft.com/jpsanders/2009/09/29/how-to-walkthrough-using-httplistener-or-http-server-unmanaged-code-c-as-an-ssl-simple-server/) ce qui est également un blog plus anciens avec des informations utiles.

Voici certains des outils tiers qui peuvent être plus faciles à utiliser que les *netsh.exe* ligne de commande. Ils ne sont pas fournies par ou recommandés par Microsoft. Les outils exécutés en tant qu’administrateur par défaut, depuis *netsh.exe* lui-même requiert des privilèges d’administrateur.

* [HTTP.sys Manager](http://httpsysmanager.codeplex.com/) fournit l’interface utilisateur pour la liste et la configuration des options et des certificats SSL, les réservations de préfixe et listes de certificats fiables. 
* [HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) vous permet de répertorier ou configurer des certificats SSL et les préfixes d’URL. L’interface utilisateur est plus précis que http.sys Manager et expose quelques options de configuration supplémentaires, mais dans le cas contraire, il fournit des fonctionnalités similaires. Il ne peut pas créer une nouvelle liste de certificats (CTL), mais que vous pouvez affecter existants.

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, reportez-vous aux ressources suivantes :

* [Exemple d’application pour cet article](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample)
* [Code source de HTTP.sys](https://github.com/aspnet/HttpSysServer/)
* [Hébergement d’applications WPF](xref:fundamentals/hosting)

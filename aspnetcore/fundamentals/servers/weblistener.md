---
title: "Implémentation du serveur web WebListener ASP.NET Core"
author: rick-anderson
description: "Introduit WebListener, un serveur web pour ASP.NET Core sur Windows. Basé sur le pilote de mode noyau Http.Sys, WebListener est une alternative à Kestrel qui peut être utilisé pour une connexion directe à Internet sans IIS."
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/weblistener
ms.openlocfilehash: f1bdbc723e4602c2e53723aff91ec5d254f4bd93
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="weblistener-web-server-implementation-in-aspnet-core"></a>Implémentation du serveur web WebListener ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra) et [Ross de Chris](https://github.com/Tratcher)

> [!NOTE]
> Cette rubrique s’applique uniquement aux ASP.NET Core 1.x. Dans ASP.NET 2.0 de noyaux, nommé WebListener [HTTP.sys](httpsys.md).

WebListener est un [serveur web pour ASP.NET Core](index.md) qui s’exécute uniquement sous Windows. Il repose sur le [pilote de mode noyau Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx). WebListener est une alternative à [Kestrel](kestrel.md) qui peut être utilisé pour une connexion directe à Internet sans se baser sur IIS comme un serveur proxy inverse. En fait, **WebListener ne peut pas être utilisée avec IIS ou IIS Express, car il n’est pas compatible avec le [ASP.NET Core Module](aspnet-core-module.md).**

Bien que WebListener a été développé pour ASP.NET Core, il peut être utilisé directement dans n’importe quelle application .NET Core ou .NET Framework via la [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) package NuGet.

WebListener prend en charge les fonctionnalités suivantes :

- [Authentification Windows](xref:security/authentication/windowsauth)
- Le partage de port
- HTTPS avec SNI
- HTTP/2 sur TLS (Windows 10)
- Transmission de fichier direct
- Réponse mise en cache
- WebSockets (Windows 8)

Versions de Windows prises en charge :

- Windows 7 et Windows Server 2008 R2 et versions ultérieures

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="when-to-use-weblistener"></a>Quand utiliser WebListener

WebListener est utile pour les déploiements où vous devez exposer le serveur directement à Internet sans l’aide d’IIS.

![WebListener communique directement avec Internet](weblistener/_static/weblistener-to-internet.png)

Comme il repose sur Http.Sys, WebListener ne nécessite pas un serveur proxy inverse pour la protection contre les attaques. Http.Sys est une technologie mature qui protège contre les nombreux types d’attaques et fournit la fiabilité, la sécurité et l’évolutivité d’un serveur web complet. IIS s’exécute comme un écouteur HTTP sur Http.Sys. 

WebListener est également un bon choix pour les déploiements internes lorsque vous avez besoin de l’une des fonctionnalités que vous ne pouvez pas obtenir à l’aide de Kestrel propose.

![WebListener communique directement avec votre réseau interne](weblistener/_static/weblistener-to-internal.png)

## <a name="how-to-use-weblistener"></a>Comment utiliser WebListener

Voici une vue d’ensemble des tâches de configuration pour le système d’exploitation hôte et de votre application ASP.NET Core.

### <a name="configure-windows-server"></a>Configurer Windows Server

* Installez la version de .NET requis par votre application, tel que [.NET Core](https://download.microsoft.com/download/0/A/3/0A372822-205D-4A86-BFA7-084D2CBE9EDF/DotNetCore.1.0.1-SDK.1.0.0.Preview2-003133-x64.exe) ou .NET Framework 4.5.1.

* Préenregistrer des préfixes d’URL pour les lier à WebListener et configurer des certificats SSL

   Si vous ne préenregistrer des préfixes d’URL dans Windows, vous devez exécuter votre application avec des privilèges d’administrateur. La seule exception est si vous liez à localhost, à l’aide de HTTP pas HTTPS avec un numéro de port supérieur à 1 024 ; Dans ce cas des privilèges d’administrateur ne sont pas nécessaires.

   Pour plus d’informations, consultez [préenregistrer préfixes et de configuration SSL](#preregister-url-prefixes-and-configure-ssl) plus loin dans cet article.

* Ouvrir les ports du pare-feu pour autoriser le trafic atteindre WebListener.

   Vous pouvez utiliser netsh.exe ou [applets de commande PowerShell](https://technet.microsoft.com/library/jj554906).

Il existe également [paramètres de Registre Http.Sys](https://support.microsoft.com/kb/820129).

### <a name="configure-your-aspnet-core-application"></a>Configurer votre application ASP.NET Core

* Installez le package NuGet [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/). Cette commande installe également [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) en tant que dépendance.

* Appelez le `UseWebListener` méthode d’extension sur [WebHostBuilder](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder) dans votre `Main` méthode, en spécifiant les WebListener [options](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) et [paramètres](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) dont vous avez besoin , comme indiqué dans l’exemple suivant :

  [!code-csharp[](weblistener/sample/Program.cs?name=snippet_Main&highlight=13-17)]

* Configurer les ports et les URL pour écouter sur 

  Par défaut, ASP.NET Core lie à `http://localhost:5000`. Pour configurer les ports et les préfixes d’URL, vous pouvez utiliser la `UseURLs` méthode d’extension, le `urls` argument de ligne de commande ou le système de configuration ASP.NET Core. Pour plus d’informations, consultez [Hébergement](../../fundamentals/hosting.md).

  Web récepteur utilise le [formats de chaîne de préfixe Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx). Il n’existe aucune exigence de format de chaîne de préfixe qui sont spécifiques à WebListener.

  > [!NOTE]
  > Assurez-vous que vous spécifiez les mêmes chaînes de préfixe dans `UseUrls` qui vous préenregistrer sur le serveur. 

* Assurez-vous que votre application n’est pas configurée pour exécuter les services IIS ou IIS Express.

  Dans Visual Studio, le profil de démarrage par défaut est pour IIS Express.  Vous devez modifier manuellement le profil sélectionné, pour exécuter le projet en tant qu’une application console comme indiqué dans la capture d’écran suivante.

  ![Sélectionnez le profil d’application console](weblistener/_static/vs-choose-profile.png)

## <a name="how-to-use-weblistener-outside-of-aspnet-core"></a>L’utilisation de WebListener en dehors d’ASP.NET Core

* Installer le [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) package NuGet.

* [Préenregistrer des préfixes d’URL pour les lier à WebListener et configurer des certificats SSL](#preregister-url-prefixes-and-configure-ssl) comme vous le feriez pour une utilisation dans ASP.NET Core.

Il existe également [paramètres de Registre Http.Sys](https://support.microsoft.com/kb/820129).


Voici un exemple de code qui illustre l’utilisation de WebListener en dehors d’ASP.NET Core :

```csharp
var settings = new WebListenerSettings();
settings.UrlPrefixes.Add("http://localhost:8080");

using (WebListener listener = new WebListener(settings))
{
    listener.Start();

    while (true)
    {
        var context = await listener.AcceptAsync();
        byte[] bytes = Encoding.ASCII.GetBytes("Hello World: " + DateTime.Now);
        context.Response.ContentLength = bytes.Length;
        context.Response.ContentType = "text/plain";

        await context.Response.Body.WriteAsync(bytes, 0, bytes.Length);
        context.Dispose();
    }
}
```

## <a name="preregister-url-prefixes-and-configure-ssl"></a>Préenregistrer des préfixes d’URL et de configurer le protocole SSL

IIS et WebListener s’appuient sur le pilote sous-jacent de mode noyau Http.Sys pour écouter les demandes et le traitement initiale. Dans IIS, l’interface utilisateur de gestion vous donne un moyen relativement facile à configurer tous les éléments. Toutefois, si vous utilisez WebListener, vous devez configurer Http.Sys vous-même. L’outil intégré correspondant est netsh.exe. 

Les tâches les plus courantes que vous devez utiliser netsh.exe pour sont réservation des préfixes d’URL et affectation des certificats SSL.

NetSh.exe n’est pas un outil simple à utiliser pour les débutants. L’exemple suivant montre le strict minimum nécessaire pour réserver des préfixes d’URL pour les ports 80 et 443 :

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

L’exemple suivant montre comment assigner un certificat SSL :

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}".
```

Voici la documentation de référence officiel :

* [Commandes Netsh pour Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
* [Chaînes UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

Les ressources suivantes fournissent des instructions détaillées pour plusieurs scénarios. Les articles qui font référence aux `HttpListener` s’appliquent aussi à `WebListener`, comme les deux reposent sur Http.Sys.

* [Guide pratique pour configurer un port avec un certificat SSL](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-configure-a-port-with-an-ssl-certificate)
* [Les communications HTTPS - HttpListener basé hébergement et la Certification Client](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) blog de tiers et est assez ancien mais dispose toujours des informations utiles.
* [Comment : HttpListener à l’aide de procédure pas à pas ou le serveur Http du code non managé (C++) comme serveur SSL Simple](https://blogs.msdn.microsoft.com/jpsanders/2009/09/29/how-to-walkthrough-using-httplistener-or-http-server-unmanaged-code-c-as-an-ssl-simple-server/) ce qui est également un blog plus anciens avec des informations utiles.
* [Comment configurer un WebListener de noyaux .NET avec SSL ?](https://blogs.msdn.microsoft.com/timomta/2016/11/04/how-do-i-set-up-a-net-core-weblistener-with-ssl/)

Voici certains des outils tiers qui peuvent être plus faciles à utiliser que la ligne de commande netsh.exe. Ils ne sont pas fournies par ou recommandés par Microsoft. Les outils exécutés en tant qu’administrateur par défaut, étant donné que netsh.exe lui-même nécessite des privilèges d’administrateur.

* [HTTP.sys Manager](http://httpsysmanager.codeplex.com/) fournit l’interface utilisateur pour la liste et la configuration des options et des certificats SSL, les réservations de préfixe et listes de certificats fiables. 
* [HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) vous permet de répertorier ou configurer des certificats SSL et les préfixes d’URL. L’interface utilisateur est plus précis que http.sys Manager et expose quelques options de configuration supplémentaires, mais dans le cas contraire, il fournit des fonctionnalités similaires. Il ne peut pas créer une nouvelle liste de certificats (CTL), mais que vous pouvez affecter existants.

Pour générer des certificats SSL auto-signés, Microsoft fournit des outils de ligne de commande : [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) et l’applet de commande PowerShell [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pki/new-selfsignedcertificate). Il existe également des outils d’interface utilisateur tiers qui le rendent plus facile de générer des certificats SSL auto-signés :

* [SelfCert](https://www.pluralsight.com/blog/software-development/selfcert-create-a-self-signed-certificate-interactively-gui-or-programmatically-in-net)
* [Interface utilisateur de Makecert](http://makecertui.codeplex.com/)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, reportez-vous aux ressources suivantes :

* [Exemple d’application pour cet article](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)
* [Code source de WebListener](https://github.com/aspnet/HttpSysServer/)
* [Hébergement d’applications WPF](../hosting.md)

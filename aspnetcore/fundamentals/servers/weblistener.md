---
title: "Implémentation du serveur web WebListener ASP.NET Core"
author: rick-anderson
description: "Introduit WebListener, un serveur web pour ASP.NET Core sur Windows. Basé sur le pilote de mode noyau Http.Sys, WebListener est une alternative à Kestrel qui peut être utilisé pour une connexion directe à Internet sans IIS."
keywords: "ASP.NET Core, WebListener, HttpListener, préfixes d’url, SSL"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: 0a7286e4-6428-424e-b5c4-5c98815cf61c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/weblistener
ms.openlocfilehash: bcd225875cfe2a544581c331231c704094780ea3
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2017
---
# <a name="weblistener-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="7feec-105">Implémentation du serveur web WebListener ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7feec-105">WebListener web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="7feec-106">Par [Tom Dykstra](http://github.com/tdykstra) et [Ross de Chris](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="7feec-106">By [Tom Dykstra](http://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

> [!NOTE]
> <span data-ttu-id="7feec-107">Cette rubrique s’applique uniquement aux ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="7feec-107">This topic applies only to ASP.NET Core 1.x.</span></span> <span data-ttu-id="7feec-108">Dans ASP.NET 2.0 de noyaux, nommé WebListener [HTTP.sys](httpsys.md).</span><span class="sxs-lookup"><span data-stu-id="7feec-108">In ASP.NET Core 2.0, WebListener is named [HTTP.sys](httpsys.md).</span></span>

<span data-ttu-id="7feec-109">WebListener est un [serveur web pour ASP.NET Core](index.md) qui s’exécute uniquement sous Windows.</span><span class="sxs-lookup"><span data-stu-id="7feec-109">WebListener is a [web server for ASP.NET Core](index.md) that runs only on Windows.</span></span> <span data-ttu-id="7feec-110">Il repose sur le [pilote de mode noyau Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span><span class="sxs-lookup"><span data-stu-id="7feec-110">It's built on the [Http.Sys kernel mode driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span> <span data-ttu-id="7feec-111">WebListener est une alternative à [Kestrel](kestrel.md) qui peut être utilisé pour une connexion directe à Internet sans se baser sur IIS comme un serveur proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="7feec-111">WebListener is an alternative to [Kestrel](kestrel.md) that can be used for direct connection to the Internet without relying on IIS as a reverse proxy server.</span></span> <span data-ttu-id="7feec-112">En fait, **WebListener ne peut pas être utilisée avec IIS ou IIS Express, car il n’est pas compatible avec le [ASP.NET Core Module](aspnet-core-module.md).**</span><span class="sxs-lookup"><span data-stu-id="7feec-112">In fact, **WebListener can't be used with IIS or IIS Express, as it isn't compatible with the [ASP.NET Core Module](aspnet-core-module.md).**</span></span>

<span data-ttu-id="7feec-113">Bien que WebListener a été développé pour ASP.NET Core, il peut être utilisé directement dans n’importe quelle application .NET Core ou .NET Framework via la [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) package NuGet.</span><span class="sxs-lookup"><span data-stu-id="7feec-113">Although WebListener was developed for ASP.NET Core, it can be used directly in any .NET Core or .NET Framework application via the [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet package.</span></span>

<span data-ttu-id="7feec-114">WebListener prend en charge les fonctionnalités suivantes :</span><span class="sxs-lookup"><span data-stu-id="7feec-114">WebListener supports the following features:</span></span>

- [<span data-ttu-id="7feec-115">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="7feec-115">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
- <span data-ttu-id="7feec-116">Le partage de port</span><span class="sxs-lookup"><span data-stu-id="7feec-116">Port sharing</span></span>
- <span data-ttu-id="7feec-117">HTTPS avec SNI</span><span class="sxs-lookup"><span data-stu-id="7feec-117">HTTPS with SNI</span></span>
- <span data-ttu-id="7feec-118">HTTP/2 sur TLS (Windows 10)</span><span class="sxs-lookup"><span data-stu-id="7feec-118">HTTP/2 over TLS (Windows 10)</span></span>
- <span data-ttu-id="7feec-119">Transmission de fichier direct</span><span class="sxs-lookup"><span data-stu-id="7feec-119">Direct file transmission</span></span>
- <span data-ttu-id="7feec-120">Réponse mise en cache</span><span class="sxs-lookup"><span data-stu-id="7feec-120">Response caching</span></span>
- <span data-ttu-id="7feec-121">WebSockets (Windows 8)</span><span class="sxs-lookup"><span data-stu-id="7feec-121">WebSockets (Windows 8)</span></span>

<span data-ttu-id="7feec-122">Versions de Windows prises en charge :</span><span class="sxs-lookup"><span data-stu-id="7feec-122">Supported Windows versions:</span></span>

- <span data-ttu-id="7feec-123">Windows 7 et Windows Server 2008 R2 et versions ultérieures</span><span class="sxs-lookup"><span data-stu-id="7feec-123">Windows 7 and Windows Server 2008 R2 and later</span></span>

[<span data-ttu-id="7feec-124">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="7feec-124">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)

## <a name="when-to-use-weblistener"></a><span data-ttu-id="7feec-125">Quand utiliser WebListener</span><span class="sxs-lookup"><span data-stu-id="7feec-125">When to use WebListener</span></span>

<span data-ttu-id="7feec-126">WebListener est utile pour les déploiements où vous devez exposer le serveur directement à Internet sans l’aide d’IIS.</span><span class="sxs-lookup"><span data-stu-id="7feec-126">WebListener is useful for deployments where you need to expose the server directly to the Internet without using IIS.</span></span>

![Weblistener communique directement avec Internet](weblistener/_static/weblistener-to-internet.png)

<span data-ttu-id="7feec-128">Comme il repose sur Http.Sys, WebListener ne nécessite pas un serveur proxy inverse pour la protection contre les attaques.</span><span class="sxs-lookup"><span data-stu-id="7feec-128">Because it's built on Http.Sys, WebListener doesn't require a reverse proxy server for protection against attacks.</span></span> <span data-ttu-id="7feec-129">Http.Sys est une technologie mature qui protège contre les nombreux types d’attaques et fournit la fiabilité, la sécurité et l’évolutivité d’un serveur web complet.</span><span class="sxs-lookup"><span data-stu-id="7feec-129">Http.Sys is mature technology that protects against many kinds of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="7feec-130">IIS s’exécute comme un écouteur HTTP sur Http.Sys.</span><span class="sxs-lookup"><span data-stu-id="7feec-130">IIS itself runs as an HTTP listener on top of Http.Sys.</span></span> 

<span data-ttu-id="7feec-131">WebListener est également un bon choix pour les déploiements internes lorsque vous avez besoin de l’une des fonctionnalités que vous ne pouvez pas obtenir à l’aide de Kestrel propose.</span><span class="sxs-lookup"><span data-stu-id="7feec-131">WebListener is also a good choice for internal deployments when you need one of the features it offers that you can't get by using Kestrel.</span></span>

![Weblistener communique directement avec votre réseau interne](weblistener/_static/weblistener-to-internal.png)

## <a name="how-to-use-weblistener"></a><span data-ttu-id="7feec-133">Comment utiliser WebListener</span><span class="sxs-lookup"><span data-stu-id="7feec-133">How to use WebListener</span></span>

<span data-ttu-id="7feec-134">Voici une vue d’ensemble des tâches de configuration pour le système d’exploitation hôte et de votre application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7feec-134">Here's an overview of setup tasks for the host OS and your ASP.NET Core application.</span></span>

### <a name="configure-windows-server"></a><span data-ttu-id="7feec-135">Configurer Windows Server</span><span class="sxs-lookup"><span data-stu-id="7feec-135">Configure Windows Server</span></span>

* <span data-ttu-id="7feec-136">Installez la version de .NET requis par votre application, tel que [.NET Core](https://go.microsoft.com/fwlink/?LinkID=827524) ou .NET Framework 4.5.1.</span><span class="sxs-lookup"><span data-stu-id="7feec-136">Install the version of .NET that your application requires, such as [.NET Core](https://go.microsoft.com/fwlink/?LinkID=827524) or .NET Framework 4.5.1.</span></span>

* <span data-ttu-id="7feec-137">Préenregistrer des préfixes d’URL pour les lier à WebListener et configurer des certificats SSL</span><span class="sxs-lookup"><span data-stu-id="7feec-137">Preregister URL prefixes to bind to WebListener, and set up SSL certificates</span></span>

   <span data-ttu-id="7feec-138">Si vous ne préenregistrer des préfixes d’URL dans Windows, vous devez exécuter votre application avec des privilèges d’administrateur.</span><span class="sxs-lookup"><span data-stu-id="7feec-138">If you don't preregister URL prefixes in Windows, you have to run your application with administrator privileges.</span></span> <span data-ttu-id="7feec-139">La seule exception est si vous liez à localhost, à l’aide de HTTP pas HTTPS avec un numéro de port supérieur à 1 024 ; Dans ce cas des privilèges d’administrateur ne sont pas nécessaires.</span><span class="sxs-lookup"><span data-stu-id="7feec-139">The only exception is if you bind to localhost using HTTP (not HTTPS) with a port number greater than 1024; in that case administrator privileges aren't required.</span></span>

   <span data-ttu-id="7feec-140">Pour plus d’informations, consultez [préenregistrer préfixes et de configuration SSL](#preregister-url-prefixes-and-configure-ssl) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="7feec-140">For details, see [How to preregister prefixes and configure SSL](#preregister-url-prefixes-and-configure-ssl) later in this article.</span></span>

* <span data-ttu-id="7feec-141">Ouvrir les ports du pare-feu pour autoriser le trafic atteindre WebListener.</span><span class="sxs-lookup"><span data-stu-id="7feec-141">Open firewall ports to allow traffic to reach WebListener.</span></span>

   <span data-ttu-id="7feec-142">Vous pouvez utiliser netsh.exe ou [applets de commande PowerShell](https://technet.microsoft.com/library/jj554906).</span><span class="sxs-lookup"><span data-stu-id="7feec-142">You can use netsh.exe or [PowerShell cmdlets](https://technet.microsoft.com/library/jj554906).</span></span>

<span data-ttu-id="7feec-143">Il existe également [paramètres de Registre Http.Sys](https://support.microsoft.com/kb/820129).</span><span class="sxs-lookup"><span data-stu-id="7feec-143">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>

### <a name="configure-your-aspnet-core-application"></a><span data-ttu-id="7feec-144">Configurer votre application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7feec-144">Configure your ASP.NET Core application</span></span>

* <span data-ttu-id="7feec-145">Installez le package NuGet [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/).</span><span class="sxs-lookup"><span data-stu-id="7feec-145">Install the NuGet package [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/).</span></span> <span data-ttu-id="7feec-146">Cette commande installe également [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) en tant que dépendance.</span><span class="sxs-lookup"><span data-stu-id="7feec-146">This also installs [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) as a dependency.</span></span>

* <span data-ttu-id="7feec-147">Appelez le [ `UseWebListener` ](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilderKestrelExtensions/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilderWebListenerExtensions.UseWebListener.md) méthode d’extension sur [WebHostBuilder](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilder/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilder.md) dans votre `Main` méthode, en spécifiant les WebListener [options](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) et [ paramètres](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) dont vous avez besoin, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7feec-147">Call the [`UseWebListener`](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilderKestrelExtensions/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilderWebListenerExtensions.UseWebListener.md) extension method on [WebHostBuilder](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilder/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilder.md) in your `Main` method, specifying any WebListener [options](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) and [settings](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) that you need, as shown in the following example:</span></span>

  [!code-csharp[](weblistener/sample/Program.cs?name=snippet_Main&highlight=13-17)]

* <span data-ttu-id="7feec-148">Configurer les ports et les URL pour écouter sur</span><span class="sxs-lookup"><span data-stu-id="7feec-148">Configure URLs and ports to listen on</span></span> 

  <span data-ttu-id="7feec-149">Par défaut, ASP.NET Core lie à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="7feec-149">By default ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="7feec-150">Pour configurer les ports et les préfixes d’URL, vous pouvez utiliser la `UseURLs` méthode d’extension, le `urls` argument de ligne de commande ou le système de configuration ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7feec-150">To configure URL prefixes and ports, you can use the `UseURLs` extension method, the `urls` command-line argument or the ASP.NET Core configuration system.</span></span> <span data-ttu-id="7feec-151">Pour plus d’informations, consultez [hébergement](../../fundamentals/hosting.md).</span><span class="sxs-lookup"><span data-stu-id="7feec-151">For more information, see [Hosting](../../fundamentals/hosting.md).</span></span>

  <span data-ttu-id="7feec-152">Web récepteur utilise le [formats de chaîne de préfixe Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span><span class="sxs-lookup"><span data-stu-id="7feec-152">Web Listener uses the [Http.Sys prefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span> <span data-ttu-id="7feec-153">Il n’existe aucune exigence de format de chaîne de préfixe qui sont spécifiques à WebListener.</span><span class="sxs-lookup"><span data-stu-id="7feec-153">There are no prefix string format requirements that are specific to WebListener.</span></span>

  > [!NOTE]
  > <span data-ttu-id="7feec-154">Assurez-vous que vous spécifiez les mêmes chaînes de préfixe dans `UseUrls` qui vous préenregistrer sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="7feec-154">Make sure that you specify the same prefix strings in `UseUrls` that you preregister on the server.</span></span> 

* <span data-ttu-id="7feec-155">Assurez-vous que votre application n’est pas configurée pour exécuter les services IIS ou IIS Express.</span><span class="sxs-lookup"><span data-stu-id="7feec-155">Make sure your application is not configured to run IIS or IIS Express.</span></span>

  <span data-ttu-id="7feec-156">Dans Visual Studio, le profil de démarrage par défaut est pour IIS Express.</span><span class="sxs-lookup"><span data-stu-id="7feec-156">In Visual Studio, the default launch profile is for IIS Express.</span></span>  <span data-ttu-id="7feec-157">Vous devez modifier manuellement le profil sélectionné, pour exécuter le projet en tant qu’une application console comme indiqué dans la capture d’écran suivante.</span><span class="sxs-lookup"><span data-stu-id="7feec-157">To run the project as a console application you have to manually change the selected profile, as shown in the following screen shot.</span></span>

  ![Sélectionnez le profil d’application console](weblistener/_static/vs-choose-profile.png)

## <a name="how-to-use-weblistener-outside-of-aspnet-core"></a><span data-ttu-id="7feec-159">L’utilisation de WebListener en dehors d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7feec-159">How to use WebListener outside of ASP.NET Core</span></span>

* <span data-ttu-id="7feec-160">Installer le [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) package NuGet.</span><span class="sxs-lookup"><span data-stu-id="7feec-160">Install the [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet package.</span></span>

* <span data-ttu-id="7feec-161">[Préenregistrer des préfixes d’URL pour les lier à WebListener et configurer des certificats SSL](#preregister-url-prefixes-and-configure-ssl) comme vous le feriez pour une utilisation dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7feec-161">[Preregister URL prefixes to bind to WebListener, and set up SSL certificates](#preregister-url-prefixes-and-configure-ssl) as you would for use in ASP.NET Core.</span></span>

<span data-ttu-id="7feec-162">Il existe également [paramètres de Registre Http.Sys](https://support.microsoft.com/kb/820129).</span><span class="sxs-lookup"><span data-stu-id="7feec-162">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>


<span data-ttu-id="7feec-163">Voici un exemple de code qui illustre l’utilisation de WebListener en dehors d’ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="7feec-163">Here's a code sample that demonstrates WebListener use outside of ASP.NET Core:</span></span>

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

## <a name="preregister-url-prefixes-and-configure-ssl"></a><span data-ttu-id="7feec-164">Préenregistrer des préfixes d’URL et de configurer le protocole SSL</span><span class="sxs-lookup"><span data-stu-id="7feec-164">Preregister URL prefixes and configure SSL</span></span>

<span data-ttu-id="7feec-165">IIS et WebListener s’appuient sur le pilote sous-jacent de mode noyau Http.Sys pour écouter les demandes et le traitement initiale.</span><span class="sxs-lookup"><span data-stu-id="7feec-165">Both IIS and WebListener rely on the underlying Http.Sys kernel mode driver to listen for requests and do initial processing.</span></span> <span data-ttu-id="7feec-166">Dans IIS, l’interface utilisateur de gestion vous donne un moyen relativement facile à configurer tous les éléments.</span><span class="sxs-lookup"><span data-stu-id="7feec-166">In IIS, the management UI gives you a relatively easy way to configure everything.</span></span> <span data-ttu-id="7feec-167">Toutefois, si vous utilisez WebListener, vous devez configurer Http.Sys vous-même.</span><span class="sxs-lookup"><span data-stu-id="7feec-167">However, if you're using WebListener you need to configure Http.Sys yourself.</span></span> <span data-ttu-id="7feec-168">L’outil intégré correspondant est netsh.exe.</span><span class="sxs-lookup"><span data-stu-id="7feec-168">The built-in tool for doing that is netsh.exe.</span></span> 

<span data-ttu-id="7feec-169">Les tâches les plus courantes que vous devez utiliser netsh.exe pour sont réservation des préfixes d’URL et affectation des certificats SSL.</span><span class="sxs-lookup"><span data-stu-id="7feec-169">The most common tasks you need to use netsh.exe for are reserving URL prefixes and assigning SSL certificates.</span></span>

<span data-ttu-id="7feec-170">NetSh.exe n’est pas un outil simple à utiliser pour les débutants.</span><span class="sxs-lookup"><span data-stu-id="7feec-170">NetSh.exe is not an easy tool to use for beginners.</span></span> <span data-ttu-id="7feec-171">L’exemple suivant montre le strict minimum nécessaire pour réserver des préfixes d’URL pour les ports 80 et 443 :</span><span class="sxs-lookup"><span data-stu-id="7feec-171">The following example shows the bare minimum needed to reserve URL prefixes for ports 80 and 443:</span></span>

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

<span data-ttu-id="7feec-172">L’exemple suivant montre comment assigner un certificat SSL :</span><span class="sxs-lookup"><span data-stu-id="7feec-172">The following example shows how to assign an SSL certificate:</span></span>

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}".
```

<span data-ttu-id="7feec-173">Voici la documentation de référence officiel :</span><span class="sxs-lookup"><span data-stu-id="7feec-173">Here is the official reference documentation:</span></span>

* [<span data-ttu-id="7feec-174">Commandes Netsh pour Hypertext Transfer Protocol (HTTP)</span><span class="sxs-lookup"><span data-stu-id="7feec-174">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](http://technet.microsoft.com/library/cc725882.aspx)
* [<span data-ttu-id="7feec-175">Chaînes UrlPrefix</span><span class="sxs-lookup"><span data-stu-id="7feec-175">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

<span data-ttu-id="7feec-176">Les ressources suivantes fournissent des instructions détaillées pour plusieurs scénarios.</span><span class="sxs-lookup"><span data-stu-id="7feec-176">The following resources provide detailed instructions for several scenarios.</span></span> <span data-ttu-id="7feec-177">Les articles qui font référence aux `HttpListener` s’appliquent aussi à `WebListener`, comme les deux reposent sur Http.Sys.</span><span class="sxs-lookup"><span data-stu-id="7feec-177">Articles that refer to `HttpListener` apply equally to `WebListener`, as both are based on Http.Sys.</span></span>

* [<span data-ttu-id="7feec-178">Comment : configurer un Port avec un certificat SSL</span><span class="sxs-lookup"><span data-stu-id="7feec-178">How to: Configure a Port with an SSL Certificate</span></span>](http://msdn.microsoft.com/library/ms733791.aspx)
* <span data-ttu-id="7feec-179">[Les communications HTTPS - HttpListener basé hébergement et la Certification Client](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) blog de tiers et est assez ancien mais dispose toujours des informations utiles.</span><span class="sxs-lookup"><span data-stu-id="7feec-179">[HTTPS Communication - HttpListener based Hosting and Client Certification](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) This is a third-party blog and is fairly old but still has useful information.</span></span>
* <span data-ttu-id="7feec-180">[Comment : HttpListener à l’aide de procédure pas à pas ou le serveur Http du code non managé (C++) comme serveur SSL Simple](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx) ce qui est également un blog plus anciens avec des informations utiles.</span><span class="sxs-lookup"><span data-stu-id="7feec-180">[How To: Walkthrough Using HttpListener or Http Server unmanaged code (C++) as an SSL Simple Server](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx) This too is an older blog with useful information.</span></span>
* [<span data-ttu-id="7feec-181">Comment configurer un WebListener de noyaux .NET avec SSL ?</span><span class="sxs-lookup"><span data-stu-id="7feec-181">How Do I Set Up A .NET Core WebListener With SSL?</span></span>](https://blogs.msdn.microsoft.com/timomta/2016/11/04/how-do-i-set-up-a-net-core-weblistener-with-ssl/)

<span data-ttu-id="7feec-182">Voici certains des outils tiers qui peuvent être plus faciles à utiliser que la ligne de commande netsh.exe.</span><span class="sxs-lookup"><span data-stu-id="7feec-182">Here are some third-party tools that can be easier to use than the netsh.exe command line.</span></span> <span data-ttu-id="7feec-183">Ils ne sont pas fournies par ou recommandés par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7feec-183">These are not provided by or endorsed by Microsoft.</span></span> <span data-ttu-id="7feec-184">Les outils exécutés en tant qu’administrateur par défaut, étant donné que netsh.exe lui-même nécessite des privilèges d’administrateur.</span><span class="sxs-lookup"><span data-stu-id="7feec-184">The tools run as administrator by default, since netsh.exe itself requires administrator privileges.</span></span>

* <span data-ttu-id="7feec-185">[HTTP.sys Manager](http://httpsysmanager.codeplex.com/) fournit l’interface utilisateur pour la liste et la configuration des options et des certificats SSL, les réservations de préfixe et listes de certificats fiables.</span><span class="sxs-lookup"><span data-stu-id="7feec-185">[http.sys Manager](http://httpsysmanager.codeplex.com/) provides UI for listing and configuring SSL certificates and options, prefix reservations, and certificate trust lists.</span></span> 
* <span data-ttu-id="7feec-186">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) vous permet de répertorier ou configurer des certificats SSL et les préfixes d’URL.</span><span class="sxs-lookup"><span data-stu-id="7feec-186">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) lets you list or configure SSL certificates and URL prefixes.</span></span> <span data-ttu-id="7feec-187">L’interface utilisateur est plus précis que http.sys Manager et expose quelques options de configuration supplémentaires, mais dans le cas contraire, il fournit des fonctionnalités similaires.</span><span class="sxs-lookup"><span data-stu-id="7feec-187">The UI is more refined than http.sys Manager and exposes a few more configuration options, but otherwise it provides similar functionality.</span></span> <span data-ttu-id="7feec-188">Il ne peut pas créer une nouvelle liste de certificats (CTL), mais que vous pouvez affecter existants.</span><span class="sxs-lookup"><span data-stu-id="7feec-188">It cannot create a new certificate trust list (CTL), but can assign existing ones.</span></span>

<span data-ttu-id="7feec-189">Pour générer des certificats SSL auto-signés, Microsoft fournit des outils de ligne de commande : [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) et l’applet de commande PowerShell [New-SelfSignedCertificate](https://technet.microsoft.com/library/hh848633).</span><span class="sxs-lookup"><span data-stu-id="7feec-189">For generating self-signed SSL certificates, Microsoft provides command-line tools: [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) and the PowerShell cmdlet [New-SelfSignedCertificate](https://technet.microsoft.com/library/hh848633).</span></span> <span data-ttu-id="7feec-190">Il existe également des outils d’interface utilisateur tiers qui le rendent plus facile de générer des certificats SSL auto-signés :</span><span class="sxs-lookup"><span data-stu-id="7feec-190">There are also third-party UI tools that make it easier for you to generate self-signed SSL certificates:</span></span>

* [<span data-ttu-id="7feec-191">SelfCert</span><span class="sxs-lookup"><span data-stu-id="7feec-191">SelfCert</span></span>](https://www.pluralsight.com/blog/software-development/selfcert-create-a-self-signed-certificate-interactively-gui-or-programmatically-in-net)
* [<span data-ttu-id="7feec-192">Interface utilisateur de Makecert</span><span class="sxs-lookup"><span data-stu-id="7feec-192">Makecert UI</span></span>](http://makecertui.codeplex.com/)

## <a name="next-steps"></a><span data-ttu-id="7feec-193">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="7feec-193">Next steps</span></span>

<span data-ttu-id="7feec-194">Pour plus d'informations, reportez-vous aux ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="7feec-194">For more information, see the following resources:</span></span>

* [<span data-ttu-id="7feec-195">Exemple d’application pour cet article</span><span class="sxs-lookup"><span data-stu-id="7feec-195">Sample app for this article</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)
* [<span data-ttu-id="7feec-196">Code source de WebListener</span><span class="sxs-lookup"><span data-stu-id="7feec-196">WebListener source code</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="7feec-197">Hébergement d’applications WPF</span><span class="sxs-lookup"><span data-stu-id="7feec-197">Hosting</span></span>](../hosting.md)

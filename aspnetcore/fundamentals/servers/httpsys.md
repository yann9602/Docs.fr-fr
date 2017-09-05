---
title: "Implémentation du serveur web HTTP.sys dans ASP.NET Core"
author: rick-anderson
description: "Introduit HTTP.sys, un serveur web pour ASP.NET Core sur Windows. Basé sur le pilote de mode noyau Http.Sys, HTTP.sys est une alternative à Kestrel qui peut être utilisé pour une connexion directe à Internet sans IIS."
keywords: "ASP.NET Core, HttpSys, HTTP.sys, HttpListener, préfixes d’url, SSL"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: 0a7286e4-6428-424e-b5c4-5c98815cf61c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/httpsys
ms.openlocfilehash: cff6f171432febac5ec3e7adf9cf77953e0ece2d
ms.sourcegitcommit: 4e84d8bf5f404bb77f3d41665cf7e7374fc39142
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2017
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="42eb7-105">Implémentation du serveur web HTTP.sys dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42eb7-105">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="42eb7-106">Par [Tom Dykstra](http://github.com/tdykstra) et [Ross de Chris](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="42eb7-106">By [Tom Dykstra](http://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

> [!NOTE]
> <span data-ttu-id="42eb7-107">Cette rubrique s’applique uniquement à ASP.NET Core 2.0 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="42eb7-107">This topic applies only to ASP.NET Core 2.0 and later.</span></span> <span data-ttu-id="42eb7-108">Dans les versions antérieures d’ASP.NET Core, HTTP.sys est nommé [WebListener](xref:fundamentals/servers/weblistener).</span><span class="sxs-lookup"><span data-stu-id="42eb7-108">In earlier versions of ASP.NET Core, HTTP.sys is named [WebListener](xref:fundamentals/servers/weblistener).</span></span>

<span data-ttu-id="42eb7-109">HTTP.sys est un [serveur web pour ASP.NET Core](index.md) qui s’exécute uniquement sous Windows.</span><span class="sxs-lookup"><span data-stu-id="42eb7-109">HTTP.sys is a [web server for ASP.NET Core](index.md) that runs only on Windows.</span></span> <span data-ttu-id="42eb7-110">Il repose sur le [pilote de mode noyau Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span><span class="sxs-lookup"><span data-stu-id="42eb7-110">It's built on the [Http.Sys kernel mode driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span> <span data-ttu-id="42eb7-111">HTTP.sys est une alternative à [Kestrel](kestrel.md) qui offre des fonctionnalités qui ne de Kestel.</span><span class="sxs-lookup"><span data-stu-id="42eb7-111">HTTP.sys is an alternative to [Kestrel](kestrel.md) that offers some features that Kestel doesn't.</span></span> <span data-ttu-id="42eb7-112">**HTTP.sys ne peut pas être utilisée avec IIS ou IIS Express, car il n’est pas compatible avec le [ASP.NET Core Module](aspnet-core-module.md).**</span><span class="sxs-lookup"><span data-stu-id="42eb7-112">**HTTP.sys can't be used with IIS or IIS Express, as it isn't compatible with the [ASP.NET Core Module](aspnet-core-module.md).**</span></span>

<span data-ttu-id="42eb7-113">HTTP.sys prend en charge les fonctionnalités suivantes :</span><span class="sxs-lookup"><span data-stu-id="42eb7-113">HTTP.sys supports the following features:</span></span>

- [<span data-ttu-id="42eb7-114">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="42eb7-114">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
- <span data-ttu-id="42eb7-115">Le partage de port</span><span class="sxs-lookup"><span data-stu-id="42eb7-115">Port sharing</span></span>
- <span data-ttu-id="42eb7-116">HTTPS avec SNI</span><span class="sxs-lookup"><span data-stu-id="42eb7-116">HTTPS with SNI</span></span>
- <span data-ttu-id="42eb7-117">HTTP/2 sur TLS (Windows 10)</span><span class="sxs-lookup"><span data-stu-id="42eb7-117">HTTP/2 over TLS (Windows 10)</span></span>
- <span data-ttu-id="42eb7-118">Transmission de fichier direct</span><span class="sxs-lookup"><span data-stu-id="42eb7-118">Direct file transmission</span></span>
- <span data-ttu-id="42eb7-119">Réponse mise en cache</span><span class="sxs-lookup"><span data-stu-id="42eb7-119">Response caching</span></span>
- <span data-ttu-id="42eb7-120">WebSockets (Windows 8)</span><span class="sxs-lookup"><span data-stu-id="42eb7-120">WebSockets (Windows 8)</span></span>

<span data-ttu-id="42eb7-121">Versions de Windows prises en charge :</span><span class="sxs-lookup"><span data-stu-id="42eb7-121">Supported Windows versions:</span></span>

- <span data-ttu-id="42eb7-122">Windows 7 et Windows Server 2008 R2 et versions ultérieures</span><span class="sxs-lookup"><span data-stu-id="42eb7-122">Windows 7 and Windows Server 2008 R2 and later</span></span>

[<span data-ttu-id="42eb7-123">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="42eb7-123">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample)

## <a name="when-to-use-httpsys"></a><span data-ttu-id="42eb7-124">Quand utiliser HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="42eb7-124">When to use HTTP.sys</span></span>

<span data-ttu-id="42eb7-125">HTTP.sys est utile pour les déploiements où vous devez exposer le serveur directement à Internet sans l’aide d’IIS.</span><span class="sxs-lookup"><span data-stu-id="42eb7-125">HTTP.sys is useful for deployments where you need to expose the server directly to the Internet without using IIS.</span></span>

![HTTP.sys communique directement avec Internet](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="42eb7-127">Comme il repose sur Http.Sys, HTTP.sys ne nécessite pas un serveur proxy inverse pour la protection contre les attaques.</span><span class="sxs-lookup"><span data-stu-id="42eb7-127">Because it's built on Http.Sys, HTTP.sys doesn't require a reverse proxy server for protection against attacks.</span></span> <span data-ttu-id="42eb7-128">Http.Sys est une technologie mature qui protège contre les nombreux types d’attaques et fournit la fiabilité, la sécurité et l’évolutivité d’un serveur web complet.</span><span class="sxs-lookup"><span data-stu-id="42eb7-128">Http.Sys is mature technology that protects against many kinds of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="42eb7-129">IIS s’exécute comme un écouteur HTTP sur Http.Sys.</span><span class="sxs-lookup"><span data-stu-id="42eb7-129">IIS itself runs as an HTTP listener on top of Http.Sys.</span></span> 

<span data-ttu-id="42eb7-130">HTTP.sys est un bon choix pour les déploiements internes lorsque vous avez besoin d’une fonctionnalité non disponible dans Kestrel, telles que l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="42eb7-130">HTTP.sys is a good choice for internal deployments when you need a feature not available in Kestrel, such as Windows authentication.</span></span>

![HTTP.sys communique directement avec votre réseau interne](httpsys/_static/httpsys-to-internal.png)

## <a name="how-to-use-httpsys"></a><span data-ttu-id="42eb7-132">L’utilisation de HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="42eb7-132">How to use HTTP.sys</span></span>

<span data-ttu-id="42eb7-133">Voici une vue d’ensemble des tâches de configuration pour le système d’exploitation hôte et de votre application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="42eb7-133">Here's an overview of setup tasks for the host OS and your ASP.NET Core application.</span></span>

### <a name="configure-windows-server"></a><span data-ttu-id="42eb7-134">Configurer Windows Server</span><span class="sxs-lookup"><span data-stu-id="42eb7-134">Configure Windows Server</span></span>

* <span data-ttu-id="42eb7-135">Installez la version de .NET requis par votre application, tel que [.NET Core](https://www.microsoft.com/net/download/core) ou [.NET Framework](https://www.microsoft.com/net/download/framework).</span><span class="sxs-lookup"><span data-stu-id="42eb7-135">Install the version of .NET that your application requires, such as [.NET Core](https://www.microsoft.com/net/download/core) or [.NET Framework](https://www.microsoft.com/net/download/framework).</span></span>

* <span data-ttu-id="42eb7-136">Préenregistrer des préfixes d’URL pour lier à HTTP.sys et configurer des certificats SSL</span><span class="sxs-lookup"><span data-stu-id="42eb7-136">Preregister URL prefixes to bind to HTTP.sys, and set up SSL certificates</span></span>

   <span data-ttu-id="42eb7-137">Si vous ne préenregistrer des préfixes d’URL dans Windows, vous devez exécuter votre application avec des privilèges d’administrateur.</span><span class="sxs-lookup"><span data-stu-id="42eb7-137">If you don't preregister URL prefixes in Windows, you have to run your application with administrator privileges.</span></span> <span data-ttu-id="42eb7-138">La seule exception est si vous liez à localhost, à l’aide de HTTP pas HTTPS avec un numéro de port supérieur à 1 024 ; Dans ce cas, des privilèges d’administrateur ne sont pas nécessaires.</span><span class="sxs-lookup"><span data-stu-id="42eb7-138">The only exception is if you bind to localhost using HTTP (not HTTPS) with a port number greater than 1024; in that case, administrator privileges aren't required.</span></span>

   <span data-ttu-id="42eb7-139">Pour plus d’informations, consultez [préenregistrer préfixes et de configuration SSL](#preregister-url-prefixes-and-configure-ssl) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="42eb7-139">For details, see [How to preregister prefixes and configure SSL](#preregister-url-prefixes-and-configure-ssl) later in this article.</span></span>

* <span data-ttu-id="42eb7-140">Ouvrir les ports du pare-feu pour autoriser le trafic atteindre HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="42eb7-140">Open firewall ports to allow traffic to reach HTTP.sys.</span></span>

   <span data-ttu-id="42eb7-141">Vous pouvez utiliser *netsh.exe* ou [applets de commande PowerShell](https://technet.microsoft.com/library/jj554906).</span><span class="sxs-lookup"><span data-stu-id="42eb7-141">You can use *netsh.exe* or [PowerShell cmdlets](https://technet.microsoft.com/library/jj554906).</span></span>

<span data-ttu-id="42eb7-142">Il existe également [paramètres de Registre Http.Sys](https://support.microsoft.com/kb/820129).</span><span class="sxs-lookup"><span data-stu-id="42eb7-142">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>

### <a name="configure-your-aspnet-core-application-to-use-httpsys"></a><span data-ttu-id="42eb7-143">Configurer votre application ASP.NET Core utilise HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="42eb7-143">Configure your ASP.NET Core application to use HTTP.sys</span></span>

* <span data-ttu-id="42eb7-144">Aucune installation de package n’est nécessaire si vous utilisez la [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage.</span><span class="sxs-lookup"><span data-stu-id="42eb7-144">No package install is needed if you use the [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage.</span></span> <span data-ttu-id="42eb7-145">Le [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) package est inclus dans le metapackage.</span><span class="sxs-lookup"><span data-stu-id="42eb7-145">The [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) package is included in the metapackage.</span></span>

* <span data-ttu-id="42eb7-146">Appelez le `UseHttpSys` méthode d’extension sur `WebHostBuilder` dans votre `Main` méthode, en spécifiant les [HTTP.sys options](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs) dont vous avez besoin, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="42eb7-146">Call the `UseHttpSys` extension method on `WebHostBuilder` in your `Main` method, specifying any [HTTP.sys options](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs) that you need, as shown in the following example:</span></span>

  [!code-csharp[](httpsys/sample/Program.cs?name=snippet_Main&highlight=11-19)]

### <a name="configure-httpsys-options"></a><span data-ttu-id="42eb7-147">Configurer les options de HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="42eb7-147">Configure HTTP.sys options</span></span>

<span data-ttu-id="42eb7-148">Voici quelques-unes des limites que vous pouvez configurer les paramètres de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="42eb7-148">Here are some of the HTTP.sys settings and limits that you can configure.</span></span>

<span data-ttu-id="42eb7-149">**Connexions clientes maximale**</span><span class="sxs-lookup"><span data-stu-id="42eb7-149">**Maximum client connections**</span></span>

<span data-ttu-id="42eb7-150">Le nombre maximal de connexions TCP simultanées et ouvertes peut être défini pour l’application entière avec le code suivant dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="42eb7-150">The maximum number of concurrent open TCP connections can be set for the entire application with the following code in *Program.cs*:</span></span>

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Options&highlight=5)]

<span data-ttu-id="42eb7-151">Le nombre maximal de connexions est illimité (null) par défaut.</span><span class="sxs-lookup"><span data-stu-id="42eb7-151">The maximum number of connections is unlimited (null) by default.</span></span>

<span data-ttu-id="42eb7-152">**Taille du corps de demande maximale**</span><span class="sxs-lookup"><span data-stu-id="42eb7-152">**Maximum request body size**</span></span>

<span data-ttu-id="42eb7-153">La taille du corps de demande maximale par défaut est 30 000 000 octets, qui est d’environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="42eb7-153">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6MB.</span></span>

<span data-ttu-id="42eb7-154">La méthode recommandée pour remplacer la limite dans une application ASP.NET MVC de base consiste à utiliser le [RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs) attribut sur une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="42eb7-154">The recommended way to override the limit in an ASP.NET Core MVC app is to use the [RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs) attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="42eb7-155">Voici un exemple qui montre comment configurer la contrainte pour l’application entière, chaque demande :</span><span class="sxs-lookup"><span data-stu-id="42eb7-155">Here's an example that shows how to configure the constraint for the entire application, every request:</span></span>

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Options&highlight=6)]

<span data-ttu-id="42eb7-156">Vous pouvez remplacer le paramètre sur une demande spécifique dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="42eb7-156">You can override the setting on a specific request in *Startup.cs*:</span></span>

[!code-csharp[](httpsys/sample/Startup.cs?name=snippet_Configure&highlight=9-10)]
 
<span data-ttu-id="42eb7-157">Une exception est levée si vous essayez de configurer la limite sur une demande une fois que l’application a démarré la lecture de la demande.</span><span class="sxs-lookup"><span data-stu-id="42eb7-157">An exception is thrown if you try to configure the limit on a request after the application has started reading the request.</span></span> <span data-ttu-id="42eb7-158">Il existe un `IsReadOnly` propriété qui indique si le `MaxRequestBodySize` propriété est en lecture seule, ce qui signifie qu’il est trop tard pour configurer la limite.</span><span class="sxs-lookup"><span data-stu-id="42eb7-158">There's an `IsReadOnly` property that tells you if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="42eb7-159">Pour plus d’informations sur les autres options de HTTP.sys, consultez [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="42eb7-159">For information about other HTTP.sys options, see [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs).</span></span> 

### <a name="configure-urls-and-ports-to-listen-on"></a><span data-ttu-id="42eb7-160">Configurer les ports et les URL pour écouter sur</span><span class="sxs-lookup"><span data-stu-id="42eb7-160">Configure URLs and ports to listen on</span></span> 

<span data-ttu-id="42eb7-161">Par défaut, ASP.NET Core lie à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="42eb7-161">By default ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="42eb7-162">Pour configurer les ports et les préfixes d’URL, vous pouvez utiliser la `UseUrls` méthode d’extension, le `urls` argument de ligne de commande, la variable d’environnement ASPNETCORE_URLS ou `UrlPrefixes` propriété sur [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="42eb7-162">To configure URL prefixes and ports, you can use the `UseUrls` extension method, the `urls` command-line argument, the ASPNETCORE_URLS environment variable, or the `UrlPrefixes` property on [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs).</span></span> <span data-ttu-id="42eb7-163">Le code suivant utilise des exemple `UrlPrefixes`.</span><span class="sxs-lookup"><span data-stu-id="42eb7-163">The following code example uses `UrlPrefixes`.</span></span>

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Main&highlight=17)]

<span data-ttu-id="42eb7-164">Un avantage de `UrlPrefixes` est que vous obtenez un message d’erreur immédiatement si vous essayez d’ajouter un préfixe au format incorrect.</span><span class="sxs-lookup"><span data-stu-id="42eb7-164">An advantage of `UrlPrefixes` is that you get an error message immediately if you try to add a prefix that is formatted wrong.</span></span> <span data-ttu-id="42eb7-165">Un avantage de `UseUrls` (partagé avec `urls` et ASPNETCORE_URLS) que vous pouvez plus facilement basculer entre Kestrel et HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="42eb7-165">An advantage of `UseUrls` (shared with `urls` and ASPNETCORE_URLS) is that you can more easily switch between Kestrel and HTTP.sys.</span></span>

<span data-ttu-id="42eb7-166">Si vous utilisez les deux `UseUrls` (ou `urls` ou ASPNETCORE_URLS) et `UrlPrefixes`, les paramètres de `UrlPrefixes` remplacent celles de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="42eb7-166">If you use both `UseUrls` (or `urls` or ASPNETCORE_URLS) and `UrlPrefixes`, the settings in `UrlPrefixes` override the ones in `UseUrls`.</span></span> <span data-ttu-id="42eb7-167">Pour plus d’informations, consultez [hébergement](xref:fundamentals/hosting).</span><span class="sxs-lookup"><span data-stu-id="42eb7-167">For more information, see [Hosting](xref:fundamentals/hosting).</span></span>

<span data-ttu-id="42eb7-168">HTTP.sys utilise le [formats de chaîne HTTP Server API UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span><span class="sxs-lookup"><span data-stu-id="42eb7-168">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

> [!NOTE]
> <span data-ttu-id="42eb7-169">Assurez-vous que vous spécifiez les mêmes chaînes de préfixe dans `UseUrls` ou `UrlPrefixes` qui vous préenregistrer sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="42eb7-169">Make sure that you specify the same prefix strings in `UseUrls` or `UrlPrefixes` that you preregister on the server.</span></span> 

### <a name="dont-use-iis"></a><span data-ttu-id="42eb7-170">N’utilisez pas IIS</span><span class="sxs-lookup"><span data-stu-id="42eb7-170">Don't use IIS</span></span>

<span data-ttu-id="42eb7-171">Assurez-vous que votre application n’est pas configurée pour exécuter les services IIS ou IIS Express.</span><span class="sxs-lookup"><span data-stu-id="42eb7-171">Make sure your application isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="42eb7-172">Dans Visual Studio, le profil de démarrage par défaut est pour IIS Express.</span><span class="sxs-lookup"><span data-stu-id="42eb7-172">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="42eb7-173">Pour exécuter le projet en tant qu’une application console, modifiez manuellement le profil sélectionné, comme indiqué dans la capture d’écran suivante.</span><span class="sxs-lookup"><span data-stu-id="42eb7-173">To run the project as a console application, manually change the selected profile, as shown in the following screen shot.</span></span>

![Sélectionnez le profil d’application console](httpsys/_static/vs-choose-profile.png)

## <a name="preregister-url-prefixes-and-configure-ssl"></a><span data-ttu-id="42eb7-175">Préenregistrer des préfixes d’URL et de configurer le protocole SSL</span><span class="sxs-lookup"><span data-stu-id="42eb7-175">Preregister URL prefixes and configure SSL</span></span>

<span data-ttu-id="42eb7-176">IIS et HTTP.sys s’appuient sur le pilote sous-jacent de mode noyau Http.Sys pour écouter les demandes et le traitement initiale.</span><span class="sxs-lookup"><span data-stu-id="42eb7-176">Both IIS and HTTP.sys rely on the underlying Http.Sys kernel mode driver to listen for requests and do initial processing.</span></span> <span data-ttu-id="42eb7-177">Dans IIS, l’interface utilisateur de gestion vous donne un moyen relativement facile à configurer tous les éléments.</span><span class="sxs-lookup"><span data-stu-id="42eb7-177">In IIS, the management UI gives you a relatively easy way to configure everything.</span></span> <span data-ttu-id="42eb7-178">Toutefois, vous devez configurer Http.Sys vous-même.</span><span class="sxs-lookup"><span data-stu-id="42eb7-178">However, you need to configure Http.Sys yourself.</span></span> <span data-ttu-id="42eb7-179">L’outil intégré permettant d’effectuer d’autres termes *netsh.exe*.</span><span class="sxs-lookup"><span data-stu-id="42eb7-179">The built-in tool for doing that is *netsh.exe*.</span></span> 

<span data-ttu-id="42eb7-180">Avec *netsh.exe* vous pouvez réserver des préfixes d’URL et attribuer des certificats SSL.</span><span class="sxs-lookup"><span data-stu-id="42eb7-180">With *netsh.exe* you can reserve URL prefixes and assign SSL certificates.</span></span> <span data-ttu-id="42eb7-181">L’outil nécessite des privilèges d’administrateur.</span><span class="sxs-lookup"><span data-stu-id="42eb7-181">The tool requires administrative privileges.</span></span>

<span data-ttu-id="42eb7-182">L’exemple suivant montre le minimum requis pour réserver des préfixes d’URL pour les ports 80 et 443 :</span><span class="sxs-lookup"><span data-stu-id="42eb7-182">The following example shows the minimum needed to reserve URL prefixes for ports 80 and 443:</span></span>

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

<span data-ttu-id="42eb7-183">L’exemple suivant montre comment assigner un certificat SSL :</span><span class="sxs-lookup"><span data-stu-id="42eb7-183">The following example shows how to assign an SSL certificate:</span></span>

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}"
```

<span data-ttu-id="42eb7-184">Voici la documentation de référence *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="42eb7-184">Here is the reference documentation for *netsh.exe*:</span></span>

* [<span data-ttu-id="42eb7-185">Commandes Netsh pour Hypertext Transfer Protocol (HTTP)</span><span class="sxs-lookup"><span data-stu-id="42eb7-185">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](http://technet.microsoft.com/library/cc725882.aspx)
* [<span data-ttu-id="42eb7-186">Chaînes UrlPrefix</span><span class="sxs-lookup"><span data-stu-id="42eb7-186">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

<span data-ttu-id="42eb7-187">Les ressources suivantes fournissent des instructions détaillées pour plusieurs scénarios.</span><span class="sxs-lookup"><span data-stu-id="42eb7-187">The following resources provide detailed instructions for several scenarios.</span></span> <span data-ttu-id="42eb7-188">Les articles qui font référence à HttpListener s’appliquent également à HTTP.sys, comme les deux reposent sur Http.Sys.</span><span class="sxs-lookup"><span data-stu-id="42eb7-188">Articles that refer to HttpListener apply equally to HTTP.sys, as both are based on Http.Sys.</span></span>

* [<span data-ttu-id="42eb7-189">Comment : configurer un Port avec un certificat SSL</span><span class="sxs-lookup"><span data-stu-id="42eb7-189">How to: Configure a Port with an SSL Certificate</span></span>](http://msdn.microsoft.com/library/ms733791.aspx)
* <span data-ttu-id="42eb7-190">[Les communications HTTPS - HttpListener basé hébergement et la Certification Client](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) blog de tiers et est assez ancien mais dispose toujours des informations utiles.</span><span class="sxs-lookup"><span data-stu-id="42eb7-190">[HTTPS Communication - HttpListener based Hosting and Client Certification](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) This is a third-party blog and is fairly old but still has useful information.</span></span>
* <span data-ttu-id="42eb7-191">[Comment : HttpListener à l’aide de procédure pas à pas ou le serveur Http du code non managé (C++) comme serveur SSL Simple](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx) ce qui est également un blog plus anciens avec des informations utiles.</span><span class="sxs-lookup"><span data-stu-id="42eb7-191">[How To: Walkthrough Using HttpListener or Http Server unmanaged code (C++) as an SSL Simple Server](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx) This too is an older blog with useful information.</span></span>

<span data-ttu-id="42eb7-192">Voici certains des outils tiers qui peuvent être plus faciles à utiliser que les *netsh.exe* ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="42eb7-192">Here are some third-party tools that can be easier to use than the *netsh.exe* command line.</span></span> <span data-ttu-id="42eb7-193">Ils ne sont pas fournies par ou recommandés par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="42eb7-193">These are not provided by or endorsed by Microsoft.</span></span> <span data-ttu-id="42eb7-194">Les outils exécutés en tant qu’administrateur par défaut, depuis *netsh.exe* lui-même requiert des privilèges d’administrateur.</span><span class="sxs-lookup"><span data-stu-id="42eb7-194">The tools run as administrator by default, since *netsh.exe* itself requires administrator privileges.</span></span>

* <span data-ttu-id="42eb7-195">[HTTP.sys Manager](http://httpsysmanager.codeplex.com/) fournit l’interface utilisateur pour la liste et la configuration des options et des certificats SSL, les réservations de préfixe et listes de certificats fiables.</span><span class="sxs-lookup"><span data-stu-id="42eb7-195">[http.sys Manager](http://httpsysmanager.codeplex.com/) provides UI for listing and configuring SSL certificates and options, prefix reservations, and certificate trust lists.</span></span> 
* <span data-ttu-id="42eb7-196">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) vous permet de répertorier ou configurer des certificats SSL et les préfixes d’URL.</span><span class="sxs-lookup"><span data-stu-id="42eb7-196">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) lets you list or configure SSL certificates and URL prefixes.</span></span> <span data-ttu-id="42eb7-197">L’interface utilisateur est plus précis que http.sys Manager et expose quelques options de configuration supplémentaires, mais dans le cas contraire, il fournit des fonctionnalités similaires.</span><span class="sxs-lookup"><span data-stu-id="42eb7-197">The UI is more refined than http.sys Manager and exposes a few more configuration options, but otherwise it provides similar functionality.</span></span> <span data-ttu-id="42eb7-198">Il ne peut pas créer une nouvelle liste de certificats (CTL), mais que vous pouvez affecter existants.</span><span class="sxs-lookup"><span data-stu-id="42eb7-198">It cannot create a new certificate trust list (CTL), but can assign existing ones.</span></span>

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

## <a name="next-steps"></a><span data-ttu-id="42eb7-199">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="42eb7-199">Next steps</span></span>

<span data-ttu-id="42eb7-200">Pour plus d'informations, reportez-vous aux ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="42eb7-200">For more information, see the following resources:</span></span>

* [<span data-ttu-id="42eb7-201">Exemple d’application pour cet article</span><span class="sxs-lookup"><span data-stu-id="42eb7-201">Sample app for this article</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/HttpSys/sample)
* [<span data-ttu-id="42eb7-202">Code source de HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="42eb7-202">HTTP.sys source code</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="42eb7-203">Hébergement d’applications WPF</span><span class="sxs-lookup"><span data-stu-id="42eb7-203">Hosting</span></span>](xref:fundamentals/hosting)

---
title: "Hébergement dans ASP.NET Core"
author: guardrex
description: "En savoir plus sur l’hôte web dans ASP.NET Core, qui est responsable de la gestion de démarrage et la durée de vie des applications."
keywords: "ASP.NET Core, web hôte, IWebHost, WebHostBuilder, IHostingEnvironment, IApplicationLifetime"
ms.author: riande
manager: wpickett
ms.date: 09/10/2017
ms.topic: article
ms.assetid: 4e45311d-8d56-46e2-b99d-6f65b648a277
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4eb57cf80399abdb7c6d05546ea2b0d5718c56c3
ms.sourcegitcommit: 0a3f215b4f665afc6f2678642968eea698102346
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2017
---
# <a name="hosting-in-aspnet-core"></a><span data-ttu-id="82779-104">Hébergement dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82779-104">Hosting in ASP.NET Core</span></span>

<span data-ttu-id="82779-105">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="82779-105">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="82779-106">Applications ASP.NET Core configurer et lancer une *hôte*, qui est responsable de la gestion de démarrage et la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="82779-106">ASP.NET Core apps configure and launch a *host*, which is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="82779-107">Au minimum, l’hôte configure un serveur et un pipeline de traitement de la requête.</span><span class="sxs-lookup"><span data-stu-id="82779-107">At a minimum, the host configures a server and a request processing pipeline.</span></span>

## <a name="setting-up-a-host"></a><span data-ttu-id="82779-108">Configuration d’un ordinateur hôte</span><span class="sxs-lookup"><span data-stu-id="82779-108">Setting up a host</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="82779-110">Créer un hôte à l’aide d’une instance de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="82779-110">Create a host using an instance of [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> <span data-ttu-id="82779-111">Cette opération est généralement effectuée dans le point d’entrée de votre application, le `Main` (méthode).</span><span class="sxs-lookup"><span data-stu-id="82779-111">This is typically performed in your app's entry point, the `Main` method.</span></span> <span data-ttu-id="82779-112">Dans les modèles de projet, `Main` se trouve dans *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="82779-112">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="82779-113">Par défaut *Program.cs* appelle [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) pour lancer l’installation d’un ordinateur hôte :</span><span class="sxs-lookup"><span data-stu-id="82779-113">A typical *Program.cs* calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main)]

<span data-ttu-id="82779-114">`CreateDefaultBuilder`effectue les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="82779-114">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="82779-115">Configure [Kestrel](servers/kestrel.md) que le serveur web.</span><span class="sxs-lookup"><span data-stu-id="82779-115">Configures [Kestrel](servers/kestrel.md) as the web server.</span></span> <span data-ttu-id="82779-116">Pour les options par défaut Kestrel, consultez [le Kestrel options section d’implémentation du serveur web Kestrel ASP.NET Core](xref:fundamentals/servers/kestrel#kestrel-options).</span><span class="sxs-lookup"><span data-stu-id="82779-116">For the Kestrel default options, see [the Kestrel options section of Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel#kestrel-options).</span></span>
* <span data-ttu-id="82779-117">Affecte à la racine du contenu [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="82779-117">Sets the content root to [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="82779-118">Configuration facultative de charge à partir de :</span><span class="sxs-lookup"><span data-stu-id="82779-118">Loads optional configuration from:</span></span>
  * <span data-ttu-id="82779-119">*appSettings.JSON*.</span><span class="sxs-lookup"><span data-stu-id="82779-119">*appsettings.json*.</span></span>
  * <span data-ttu-id="82779-120">*appSettings. {Environnement} .json*.</span><span class="sxs-lookup"><span data-stu-id="82779-120">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="82779-121">[Les secrets utilisateur](xref:security/app-secrets) lorsque l’application s’exécute le `Development` environnement.</span><span class="sxs-lookup"><span data-stu-id="82779-121">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="82779-122">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="82779-122">Environment variables.</span></span>
  * <span data-ttu-id="82779-123">Arguments de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="82779-123">Command-line arguments.</span></span>
* <span data-ttu-id="82779-124">Configure [journalisation](xref:fundamentals/logging) pour la sortie de console et de débogage avec [filtrage de journal](xref:fundamentals/logging#log-filtering) les règles spécifiées dans une section de configuration de journalisation d’un *appsettings.json* ou *appsettings. {Environnement} .json* fichier.</span><span class="sxs-lookup"><span data-stu-id="82779-124">Configures [logging](xref:fundamentals/logging) for console and debug output with [log filtering](xref:fundamentals/logging#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="82779-125">En cas de retard IIS, Active [intégration des services Internet](xref:publishing/iis) en configurant le chemin d’accès de base et le port du serveur doit écouter lors de l’utilisation du [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="82779-125">When running behind IIS, enables [IIS integration](xref:publishing/iis) by configuring the base path and port the server should listen on when using the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="82779-126">Le module crée un proxy inverse entre IIS et Kestrel.</span><span class="sxs-lookup"><span data-stu-id="82779-126">The module creates a reverse-proxy between IIS and Kestrel.</span></span> <span data-ttu-id="82779-127">Configure également l’application de [capturer les erreurs de démarrage](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="82779-127">Also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="82779-128">Pour les options par défaut d’IIS, consultez [IIS options de section de l’hôte ASP.NET Core sur Windows avec IIS](xref:publishing/iis#iis-options).</span><span class="sxs-lookup"><span data-stu-id="82779-128">For the IIS default options, see [the IIS options section of Host ASP.NET Core on Windows with IIS](xref:publishing/iis#iis-options).</span></span>

<span data-ttu-id="82779-129">Le *racine du contenu* détermine où l’hôte recherche les fichiers de contenu, tels que les fichiers de vue MVC.</span><span class="sxs-lookup"><span data-stu-id="82779-129">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="82779-130">La racine de contenu par défaut est [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="82779-130">The default content root is [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span> <span data-ttu-id="82779-131">Cela entraîne l’utilisation de dossier racine du projet web en tant que la racine de contenu lors de l’application est lancée à partir du dossier racine (par exemple, l’appel [dotnet exécuter](/dotnet/core/tools/dotnet-run) à partir du dossier du projet).</span><span class="sxs-lookup"><span data-stu-id="82779-131">This results in using the web project's root folder as the content root when the app is started from the root folder (for example, calling [dotnet run](/dotnet/core/tools/dotnet-run) from the project folder).</span></span> <span data-ttu-id="82779-132">Ceci est la valeur par défaut utilisée dans [Visual Studio](https://www.visualstudio.com/) et [dotnet nouveaux modèles](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="82779-132">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="82779-133">Consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration) pour plus d’informations sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="82779-133">See [Configuration in ASP.NET Core](xref:fundamentals/configuration) for more information on app configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="82779-134">Comme alternative à l’aide de la méthode statique `CreateDefaultBuilder` méthode, la création d’un hôte à partir de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) est une approche pris en charge avec ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="82779-134">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span> <span data-ttu-id="82779-135">Consultez l’onglet 1.x ASP.NET Core pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="82779-135">See the ASP.NET Core 1.x tab for more information.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-136">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-136">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="82779-137">Créer un hôte à l’aide d’une instance de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="82779-137">Create a host using an instance of [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> <span data-ttu-id="82779-138">Cette opération est généralement effectuée dans le point d’entrée de votre application, le `Main` (méthode).</span><span class="sxs-lookup"><span data-stu-id="82779-138">This is typically performed in your app's entry point, the `Main` method.</span></span> <span data-ttu-id="82779-139">Dans les modèles de projet, `Main` se trouve dans *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="82779-139">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="82779-140">Les éléments suivants *Program.cs* montre comment utiliser `WebHostBuilder` pour générer l’hôte :</span><span class="sxs-lookup"><span data-stu-id="82779-140">The following *Program.cs* demonstrates how to use `WebHostBuilder` to build the host:</span></span>

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs)]

<span data-ttu-id="82779-141">`WebHostBuilder`nécessite un [serveur qui implémente IServer](servers/index.md).</span><span class="sxs-lookup"><span data-stu-id="82779-141">`WebHostBuilder` requires a [server that implements IServer](servers/index.md).</span></span> <span data-ttu-id="82779-142">Serveurs intégrés sont [Kestrel](servers/kestrel.md) et [HTTP.sys](servers/httpsys.md) (avant la version d’ASP.NET 2.0 de noyaux, HTTP.sys a été appelé [WebListener](xref:fundamentals/servers/weblistener)).</span><span class="sxs-lookup"><span data-stu-id="82779-142">The built-in servers are [Kestrel](servers/kestrel.md) and [HTTP.sys](servers/httpsys.md) (prior to the release of ASP.NET Core 2.0, HTTP.sys was called [WebListener](xref:fundamentals/servers/weblistener)).</span></span> <span data-ttu-id="82779-143">Dans cet exemple, le [méthode d’extension UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) Spécifie le serveur Kestrel.</span><span class="sxs-lookup"><span data-stu-id="82779-143">In this example, the [UseKestrel extension method](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) specifies the Kestrel server.</span></span>

<span data-ttu-id="82779-144">Le *racine du contenu* détermine où l’hôte recherche les fichiers de contenu, tels que les fichiers de vue MVC.</span><span class="sxs-lookup"><span data-stu-id="82779-144">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="82779-145">La racine de contenu par défaut fourni à `UseContentRoot` est [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1).</span><span class="sxs-lookup"><span data-stu-id="82779-145">The default content root supplied to `UseContentRoot` is [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1).</span></span> <span data-ttu-id="82779-146">Cela entraîne l’utilisation de dossier racine du projet web en tant que la racine de contenu lors de l’application est lancée à partir du dossier racine (par exemple, l’appel [dotnet exécuter](/dotnet/core/tools/dotnet-run) à partir du dossier du projet).</span><span class="sxs-lookup"><span data-stu-id="82779-146">This results in using the web project's root folder as the content root when the app is started from the root folder (for example, calling [dotnet run](/dotnet/core/tools/dotnet-run) from the project folder).</span></span> <span data-ttu-id="82779-147">Ceci est la valeur par défaut utilisée dans [Visual Studio](https://www.visualstudio.com/) et [dotnet nouveaux modèles](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="82779-147">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="82779-148">Pour utiliser IIS comme un proxy inverse, appelez [UseIISIntegration](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) dans le cadre de la création de l’ordinateur hôte.</span><span class="sxs-lookup"><span data-stu-id="82779-148">To use IIS as a reverse proxy, call [UseIISIntegration](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) as part of building the host.</span></span> <span data-ttu-id="82779-149">`UseIISIntegration`ne configure pas un *server*, comme [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) est.</span><span class="sxs-lookup"><span data-stu-id="82779-149">`UseIISIntegration` doesn't configure a *server*, like [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) does.</span></span> <span data-ttu-id="82779-150">`UseIISIntegration`Configure le chemin d’accès de base et le port que le serveur doit écouter lors de l’utilisation du [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) pour créer un proxy inverse entre Kestrel et IIS.</span><span class="sxs-lookup"><span data-stu-id="82779-150">`UseIISIntegration` configures the base path and port the server should listen on when using the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) to create a reverse-proxy between Kestrel and IIS.</span></span> <span data-ttu-id="82779-151">Pour utiliser IIS avec ASP.NET Core, vous devez spécifier les deux `UseKestrel` et `UseIISIntegration`.</span><span class="sxs-lookup"><span data-stu-id="82779-151">To use IIS with ASP.NET Core, you must specify both `UseKestrel` and `UseIISIntegration`.</span></span> <span data-ttu-id="82779-152">`UseIISIntegration`Active uniquement lors de l’exécution derrière IIS ou IIS Express.</span><span class="sxs-lookup"><span data-stu-id="82779-152">`UseIISIntegration` only activates when running behind IIS or IIS Express.</span></span> <span data-ttu-id="82779-153">Pour plus d’informations, consultez [Introduction à ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) et [référence de configuration ASP.NET Core Module](xref:hosting/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="82779-153">For more information, see [Introduction to ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and [ASP.NET Core Module configuration reference](xref:hosting/aspnet-core-module).</span></span>

<span data-ttu-id="82779-154">Une implémentation minimale qui configure un ordinateur hôte (et une application ASP.NET Core) consiste à spécifier un serveur et la configuration du pipeline de demande de l’application :</span><span class="sxs-lookup"><span data-stu-id="82779-154">A minimal implementation that configures a host (and an ASP.NET Core app) includes specifying a server and configuration of the app's request pipeline:</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .Configure(app =>
    {
        app.Run(context => context.Response.WriteAsync("Hello World!"));
    })
    .Build();

host.Run();
```

---

<span data-ttu-id="82779-155">Lorsque vous configurez un ordinateur hôte, vous pouvez fournir [configurer](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) et [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) méthodes.</span><span class="sxs-lookup"><span data-stu-id="82779-155">When setting up a host, you can provide [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) methods.</span></span> <span data-ttu-id="82779-156">Si vous spécifiez un `Startup` (classe), il doit définir un `Configure` (méthode).</span><span class="sxs-lookup"><span data-stu-id="82779-156">If you specify a `Startup` class, it must define a `Configure` method.</span></span> <span data-ttu-id="82779-157">Pour plus d’informations, consultez [démarrage de l’Application dans ASP.NET Core](startup.md).</span><span class="sxs-lookup"><span data-stu-id="82779-157">For more information, see [Application Startup in ASP.NET Core](startup.md).</span></span> <span data-ttu-id="82779-158">Appels multiples à `ConfigureServices` ajouter à un autre.</span><span class="sxs-lookup"><span data-stu-id="82779-158">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="82779-159">Appels multiples à `Configure` ou `UseStartup` sur la `WebHostBuilder` remplacer les paramètres précédents.</span><span class="sxs-lookup"><span data-stu-id="82779-159">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="82779-160">Valeurs de configuration d’hôte</span><span class="sxs-lookup"><span data-stu-id="82779-160">Host configuration values</span></span>

<span data-ttu-id="82779-161">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) fournit des méthodes pour la définition de la plupart des valeurs de configuration disponibles pour l’hôte, qui peut également être définie directement avec [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) et la clé associée.</span><span class="sxs-lookup"><span data-stu-id="82779-161">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) provides methods for setting most of the available configuration values for the host, which can also be set directly with [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="82779-162">Lors de la définition d’une valeur avec `UseSetting`, la valeur est définie sous forme de chaîne (entre guillemets) quel que soit le type.</span><span class="sxs-lookup"><span data-stu-id="82779-162">When setting a value with `UseSetting`, the value is set as a string (in quotes) regardless of the type.</span></span>

### <a name="capture-startup-errors"></a><span data-ttu-id="82779-163">Capturer les erreurs de démarrage</span><span class="sxs-lookup"><span data-stu-id="82779-163">Capture Startup Errors</span></span>

<span data-ttu-id="82779-164">Ce paramètre contrôle la capture des erreurs de démarrage.</span><span class="sxs-lookup"><span data-stu-id="82779-164">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="82779-165">**Clé**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="82779-165">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="82779-166">**Type**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="82779-166">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="82779-167">**Par défaut**: valeur par défaut est `false` , sauf si l’application s’exécute avec Kestrel derrière IIS, où la valeur par défaut est `true`.</span><span class="sxs-lookup"><span data-stu-id="82779-167">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="82779-168">**À l’aide de**:`CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="82779-168">**Set using**: `CaptureStartupErrors`</span></span>

<span data-ttu-id="82779-169">Lorsque `false`, erreurs de résultat de démarrage de l’ordinateur hôte en cours de fermeture.</span><span class="sxs-lookup"><span data-stu-id="82779-169">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="82779-170">Lorsque `true`, capture des exceptions lors du démarrage de l’ordinateur hôte et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="82779-170">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-171">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-171">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-172">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-172">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .CaptureStartupErrors(true)
    ...
```

---

### <a name="content-root"></a><span data-ttu-id="82779-173">Racine du contenu</span><span class="sxs-lookup"><span data-stu-id="82779-173">Content Root</span></span>

<span data-ttu-id="82779-174">Ce paramètre détermine où ASP.NET Core commence à rechercher les fichiers de contenu, tels que les vues MVC.</span><span class="sxs-lookup"><span data-stu-id="82779-174">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="82779-175">**Clé**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="82779-175">**Key**: contentRoot</span></span>  
<span data-ttu-id="82779-176">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="82779-176">**Type**: *string*</span></span>  
<span data-ttu-id="82779-177">**Par défaut**: le dossier où réside l’application par défaut.</span><span class="sxs-lookup"><span data-stu-id="82779-177">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="82779-178">**À l’aide de**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="82779-178">**Set using**: `UseContentRoot`</span></span>

<span data-ttu-id="82779-179">La racine du contenu est également utilisée comme le chemin d’accès de base pour le [paramètre de la racine Web](#web-root).</span><span class="sxs-lookup"><span data-stu-id="82779-179">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="82779-180">Si le chemin d’accès n’existe pas, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="82779-180">If the path doesn't exist, the host fails to start.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-181">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-181">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\mywebsite")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-182">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-182">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
    ...
```

---

### <a name="detailed-errors"></a><span data-ttu-id="82779-183">Erreurs détaillées</span><span class="sxs-lookup"><span data-stu-id="82779-183">Detailed Errors</span></span>

<span data-ttu-id="82779-184">Détermine si les détails les erreurs doivent être capturés.</span><span class="sxs-lookup"><span data-stu-id="82779-184">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="82779-185">**Clé**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="82779-185">**Key**: detailedErrors</span></span>  
<span data-ttu-id="82779-186">**Type**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="82779-186">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="82779-187">**Par défaut**: false</span><span class="sxs-lookup"><span data-stu-id="82779-187">**Default**: false</span></span>  
<span data-ttu-id="82779-188">**À l’aide de**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="82779-188">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="82779-189">Lorsque activé (ou lorsque la <a href="#environment">environnement</a> a la valeur `Development`), l’application capture les exceptions détaillées.</span><span class="sxs-lookup"><span data-stu-id="82779-189">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-190">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-190">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-191">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-191">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

---

### <a name="environment"></a><span data-ttu-id="82779-192">Environnement</span><span class="sxs-lookup"><span data-stu-id="82779-192">Environment</span></span>

<span data-ttu-id="82779-193">Définit l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="82779-193">Sets the app's environment.</span></span>

<span data-ttu-id="82779-194">**Clé**: environnement</span><span class="sxs-lookup"><span data-stu-id="82779-194">**Key**: environment</span></span>  
<span data-ttu-id="82779-195">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="82779-195">**Type**: *string*</span></span>  
<span data-ttu-id="82779-196">**Par défaut**: Production</span><span class="sxs-lookup"><span data-stu-id="82779-196">**Default**: Production</span></span>  
<span data-ttu-id="82779-197">**À l’aide de**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="82779-197">**Set using**: `UseEnvironment`</span></span>

<span data-ttu-id="82779-198">Vous pouvez définir le *environnement* n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="82779-198">You can set the *Environment* to any value.</span></span> <span data-ttu-id="82779-199">Les valeurs définies par l’infrastructure sont `Development`, `Staging`, et `Production`.</span><span class="sxs-lookup"><span data-stu-id="82779-199">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="82779-200">Les valeurs ne sont pas respecter la casse.</span><span class="sxs-lookup"><span data-stu-id="82779-200">Values aren't case sensitive.</span></span> <span data-ttu-id="82779-201">Par défaut, le *environnement* est lu depuis le `ASPNETCORE_ENVIRONMENT` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="82779-201">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="82779-202">Lorsque vous utilisez [Visual Studio](https://www.visualstudio.com/), variables d’environnement peuvent être définies dans le *launchSettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="82779-202">When using [Visual Studio](https://www.visualstudio.com/), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="82779-203">Pour plus d’informations, consultez [Utilisation de plusieurs environnements](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="82779-203">For more information, see [Working with Multiple Environments](xref:fundamentals/environments).</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-204">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-204">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-205">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-205">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseEnvironment("Development")
    ...
```

---

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="82779-206">Hébergement des assemblys de démarrage</span><span class="sxs-lookup"><span data-stu-id="82779-206">Hosting Startup Assemblies</span></span>

<span data-ttu-id="82779-207">Définit les assemblys de démarrage de l’application hôte.</span><span class="sxs-lookup"><span data-stu-id="82779-207">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="82779-208">**Clé**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="82779-208">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="82779-209">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="82779-209">**Type**: *string*</span></span>  
<span data-ttu-id="82779-210">**Par défaut**: une chaîne vide</span><span class="sxs-lookup"><span data-stu-id="82779-210">**Default**: Empty string</span></span>  
<span data-ttu-id="82779-211">**À l’aide de**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="82779-211">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="82779-212">Une chaîne délimitée par des points-virgules d’héberger des assemblys de démarrage à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="82779-212">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="82779-213">Cette fonctionnalité est une nouveauté dans ASP.NET 2.0 de base.</span><span class="sxs-lookup"><span data-stu-id="82779-213">This feature is new in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="82779-214">Bien que la valeur de configuration par défaut est une chaîne vide, les assemblys de démarrage hébergement toujours incluent assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="82779-214">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="82779-215">Lorsque vous fournissez des assemblys de démarrage hébergement, elles sont ajoutées à l’assembly de l’application pour le chargement lorsque l’application génère ses services courants lors du démarrage.</span><span class="sxs-lookup"><span data-stu-id="82779-215">When you provide hosting startup assemblies, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-216">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-216">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-217">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-217">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="82779-218">Cette fonctionnalité n’est pas disponible dans ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="82779-218">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="prefer-hosting-urls"></a><span data-ttu-id="82779-219">Préférez l’hébergement d’URL</span><span class="sxs-lookup"><span data-stu-id="82779-219">Prefer Hosting URLs</span></span>

<span data-ttu-id="82779-220">Indique si l’ordinateur hôte doit écouter les URL configurées avec le `WebHostBuilder` au lieu de ceux configurés avec la `IServer` implémentation.</span><span class="sxs-lookup"><span data-stu-id="82779-220">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="82779-221">**Clé**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="82779-221">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="82779-222">**Type**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="82779-222">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="82779-223">**Par défaut**: true</span><span class="sxs-lookup"><span data-stu-id="82779-223">**Default**: true</span></span>  
<span data-ttu-id="82779-224">**À l’aide de**:`PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="82779-224">**Set using**: `PreferHostingUrls`</span></span>

<span data-ttu-id="82779-225">Cette fonctionnalité est une nouveauté dans ASP.NET 2.0 de base.</span><span class="sxs-lookup"><span data-stu-id="82779-225">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-226">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-226">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-227">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-227">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="82779-228">Cette fonctionnalité n’est pas disponible dans ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="82779-228">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="prevent-hosting-startup"></a><span data-ttu-id="82779-229">Empêcher le démarrage d’hébergement</span><span class="sxs-lookup"><span data-stu-id="82779-229">Prevent Hosting Startup</span></span>

<span data-ttu-id="82779-230">Empêche le chargement automatique des assemblys de démarrage, y compris d’assembly de l’application d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="82779-230">Prevents the automatic loading of hosting startup assemblies, including the app's assembly.</span></span>

<span data-ttu-id="82779-231">**Clé**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="82779-231">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="82779-232">**Type**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="82779-232">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="82779-233">**Par défaut**: false</span><span class="sxs-lookup"><span data-stu-id="82779-233">**Default**: false</span></span>  
<span data-ttu-id="82779-234">**À l’aide de**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="82779-234">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="82779-235">Cette fonctionnalité est une nouveauté dans ASP.NET 2.0 de base.</span><span class="sxs-lookup"><span data-stu-id="82779-235">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-236">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-236">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-237">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-237">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="82779-238">Cette fonctionnalité n’est pas disponible dans ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="82779-238">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="server-urls"></a><span data-ttu-id="82779-239">URL du serveur</span><span class="sxs-lookup"><span data-stu-id="82779-239">Server URLs</span></span>

<span data-ttu-id="82779-240">Indique les adresses IP ou les adresses d’hôte avec les ports et protocoles que le serveur doit écouter les demandes.</span><span class="sxs-lookup"><span data-stu-id="82779-240">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="82779-241">**Clé**: URL</span><span class="sxs-lookup"><span data-stu-id="82779-241">**Key**: urls</span></span>  
<span data-ttu-id="82779-242">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="82779-242">**Type**: *string*</span></span>  
<span data-ttu-id="82779-243">**Par défaut**: http://localhost : 5000</span><span class="sxs-lookup"><span data-stu-id="82779-243">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="82779-244">**À l’aide de**:`UseUrls`</span><span class="sxs-lookup"><span data-stu-id="82779-244">**Set using**: `UseUrls`</span></span>

<span data-ttu-id="82779-245">La valeur séparées par des points-virgules ( ;) des préfixes de la liste des URL à laquelle le serveur doit répondre.</span><span class="sxs-lookup"><span data-stu-id="82779-245">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="82779-246">Par exemple, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="82779-246">For example, `http://localhost:123`.</span></span> <span data-ttu-id="82779-247">Utilisez «\*» pour indiquer que le serveur doit écouter les demandes sur des adresses IP ou le nom d’hôte en utilisant le port spécifié et le protocole (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="82779-247">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="82779-248">Le protocole (`http://` ou `https://`) doivent être incluses avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="82779-248">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="82779-249">Formats pris en charge varient entre les serveurs.</span><span class="sxs-lookup"><span data-stu-id="82779-249">Supported formats vary between servers.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-250">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-250">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

<span data-ttu-id="82779-251">Kestrel a sa propre API de configuration du point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="82779-251">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="82779-252">Pour plus d’informations, consultez [Implémentation du serveur web Kestrel dans ASP.NET Core](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="82779-252">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-253">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-253">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

---

### <a name="shutdown-timeout"></a><span data-ttu-id="82779-254">Délai d’arrêt</span><span class="sxs-lookup"><span data-stu-id="82779-254">Shutdown Timeout</span></span>

<span data-ttu-id="82779-255">Spécifie la quantité de temps à attendre avant l’arrêt hôte web.</span><span class="sxs-lookup"><span data-stu-id="82779-255">Specifies the amount of time to wait for the web host to shutdown.</span></span>

<span data-ttu-id="82779-256">**Clé**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="82779-256">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="82779-257">**Type**: *int*</span><span class="sxs-lookup"><span data-stu-id="82779-257">**Type**: *int*</span></span>  
<span data-ttu-id="82779-258">**Par défaut**: 5</span><span class="sxs-lookup"><span data-stu-id="82779-258">**Default**: 5</span></span>  
<span data-ttu-id="82779-259">**À l’aide de**:`UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="82779-259">**Set using**: `UseShutdownTimeout`</span></span>

<span data-ttu-id="82779-260">Bien que la clé accepte un *int* avec `UseSetting` (par exemple, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), la `UseShutdownTimeout` méthode d’extension prend un `TimeSpan`.</span><span class="sxs-lookup"><span data-stu-id="82779-260">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the `UseShutdownTimeout` extension method takes a `TimeSpan`.</span></span> <span data-ttu-id="82779-261">Cette fonctionnalité est une nouveauté dans ASP.NET 2.0 de base.</span><span class="sxs-lookup"><span data-stu-id="82779-261">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-262">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-262">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-263">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-263">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="82779-264">Cette fonctionnalité n’est pas disponible dans ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="82779-264">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="startup-assembly"></a><span data-ttu-id="82779-265">Assembly de démarrage</span><span class="sxs-lookup"><span data-stu-id="82779-265">Startup Assembly</span></span>

<span data-ttu-id="82779-266">Détermine l’assembly à rechercher la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="82779-266">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="82779-267">**Clé**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="82779-267">**Key**: startupAssembly</span></span>  
<span data-ttu-id="82779-268">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="82779-268">**Type**: *string*</span></span>  
<span data-ttu-id="82779-269">**Par défaut**: assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="82779-269">**Default**: The app's assembly</span></span>  
<span data-ttu-id="82779-270">**À l’aide de**:`UseStartup`</span><span class="sxs-lookup"><span data-stu-id="82779-270">**Set using**: `UseStartup`</span></span>

<span data-ttu-id="82779-271">Vous pouvez référencer l’assembly par nom (`string`) ou de type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="82779-271">You can reference the assembly by name (`string`) or type (`TStartup`).</span></span> <span data-ttu-id="82779-272">Si plusieurs `UseStartup` méthodes sont appelées, le dernier est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="82779-272">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-273">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-273">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
    ...
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-274">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-274">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
    ...
```

```csharp
var host = new WebHostBuilder()
    .UseStartup<TStartup>()
    ...
```

---

### <a name="web-root"></a><span data-ttu-id="82779-275">Racine du site Web</span><span class="sxs-lookup"><span data-stu-id="82779-275">Web Root</span></span>

<span data-ttu-id="82779-276">Définit le chemin d’accès relatif aux ressources statique de l’application.</span><span class="sxs-lookup"><span data-stu-id="82779-276">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="82779-277">**Clé**: webroot</span><span class="sxs-lookup"><span data-stu-id="82779-277">**Key**: webroot</span></span>  
<span data-ttu-id="82779-278">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="82779-278">**Type**: *string*</span></span>  
<span data-ttu-id="82779-279">**Par défaut**: si non spécifié, la valeur par défaut est "(Content Root)/wwwroot », si le chemin d’accès existe.</span><span class="sxs-lookup"><span data-stu-id="82779-279">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="82779-280">Si le chemin d’accès n’existe pas, un fournisseur de fichiers de l’absence d’opération est utilisé.</span><span class="sxs-lookup"><span data-stu-id="82779-280">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="82779-281">**À l’aide de**:`UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="82779-281">**Set using**: `UseWebRoot`</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-282">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-282">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-283">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-283">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseWebRoot("public")
    ...
```

---

## <a name="overriding-configuration"></a><span data-ttu-id="82779-284">Substitution de configuration</span><span class="sxs-lookup"><span data-stu-id="82779-284">Overriding configuration</span></span>

<span data-ttu-id="82779-285">Utilisez [Configuration](configuration.md) pour configurer l’hôte.</span><span class="sxs-lookup"><span data-stu-id="82779-285">Use [Configuration](configuration.md) to configure the host.</span></span> <span data-ttu-id="82779-286">Dans l’exemple suivant, configuration de l’hôte est éventuellement spécifiée dans un *hosting.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="82779-286">In the following example, host configuration is optionally specified in a *hosting.json* file.</span></span> <span data-ttu-id="82779-287">Toute configuration chargée à partir de la *hosting.json* fichier peut être remplacé par des arguments de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="82779-287">Any configuration loaded from the *hosting.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="82779-288">La configuration intégrée (dans `config`) est utilisé pour configurer l’ordinateur hôte avec `UseConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="82779-288">The built configuration (in `config`) is used to configure the host with `UseConfiguration`.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-289">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-289">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="82779-290">*Hosting.JSON*:</span><span class="sxs-lookup"><span data-stu-id="82779-290">*hosting.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

<span data-ttu-id="82779-291">Substitution de la configuration fournie par `UseUrls` avec *hosting.json* config du premier argument de ligne de commande config deuxième :</span><span class="sxs-lookup"><span data-stu-id="82779-291">Overriding the configuration provided by `UseUrls` with *hosting.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        BuildWebHost(args).Run();
    }

    public static IWebHost BuildWebHost(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();
    }
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-292">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-292">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="82779-293">*Hosting.JSON*:</span><span class="sxs-lookup"><span data-stu-id="82779-293">*hosting.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

<span data-ttu-id="82779-294">Substitution de la configuration fournie par `UseUrls` avec *hosting.json* config du premier argument de ligne de commande config deuxième :</span><span class="sxs-lookup"><span data-stu-id="82779-294">Overriding the configuration provided by `UseUrls` with *hosting.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        var host = new WebHostBuilder()
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .UseKestrel()
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();

        host.Run();
    }
}
```

---

> [!NOTE]
> <span data-ttu-id="82779-295">Le `UseConfiguration` méthode d’extension n’est pas actuellement capable d’analyser une section de configuration retournée par `GetSection` (par exemple, `.UseConfiguration(Configuration.GetSection("section"))`.</span><span class="sxs-lookup"><span data-stu-id="82779-295">The `UseConfiguration` extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="82779-296">Le `GetSection` méthode filtre les clés de configuration à la section demandée, mais laisse le nom de la section sur les clés (par exemple, `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="82779-296">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="82779-297">Le `UseConfiguration` méthode attend les clés pour faire correspondre le `WebHostBuilder` clés (par exemple, `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="82779-297">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="82779-298">La présence d’un nom de la section sur les clés empêche des valeurs de la section à partir de la configuration de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="82779-298">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="82779-299">Ce problème sera résolu dans une prochaine mise en production.</span><span class="sxs-lookup"><span data-stu-id="82779-299">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="82779-300">Pour plus d’informations et des solutions de contournement, consultez [en passant de la section de configuration dans WebHostBuilder.UseConfiguration utilise des clés complètes](https://github.com/aspnet/Hosting/issues/839).</span><span class="sxs-lookup"><span data-stu-id="82779-300">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>

<span data-ttu-id="82779-301">Pour spécifier l’hôte s’exécutent sur une URL particulière, vous pouvez transmettre la valeur souhaitée à partir d’une invite de commandes lors de l’exécution `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="82779-301">To specify the host run on a particular URL, you could pass in the desired value from a command prompt when executing `dotnet run`.</span></span> <span data-ttu-id="82779-302">L’argument de ligne de commande remplace le `urls` valeur à partir de la *hosting.json* fichier et que le serveur écoute sur le port 8080 :</span><span class="sxs-lookup"><span data-stu-id="82779-302">The command-line argument overrides the `urls` value from the *hosting.json* file, and the server listens on port 8080:</span></span>

```console
dotnet run --urls "http://*:8080"
```

## <a name="ordering-importance"></a><span data-ttu-id="82779-303">Ordre d’importance</span><span class="sxs-lookup"><span data-stu-id="82779-303">Ordering importance</span></span>

<span data-ttu-id="82779-304">Parmi les `WebHostBuilder` paramètres sont tout d’abord lus à partir de variables d’environnement, si définie.</span><span class="sxs-lookup"><span data-stu-id="82779-304">Some of the `WebHostBuilder` settings are first read from environment variables, if set.</span></span> <span data-ttu-id="82779-305">Ces variables d’environnement utilisent le format `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="82779-305">These environment variables use the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="82779-306">Pour définir les URL que le serveur écoute sur par défaut, vous définissez `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="82779-306">To set the URLs that the server listens on by default, you set `ASPNETCORE_URLS`.</span></span>

<span data-ttu-id="82779-307">Vous pouvez remplacer ces valeurs de variable d’environnement en spécifiant la configuration (à l’aide de `UseConfiguration`) ou en définissant la valeur de manière explicite (à l’aide de `UseSetting` ou l’une des méthodes d’extension explicite, tel que `UseUrls`).</span><span class="sxs-lookup"><span data-stu-id="82779-307">You can override any of these environment variable values by specifying configuration (using `UseConfiguration`) or by setting the value explicitly (using `UseSetting` or one of the explicit extension methods, such as `UseUrls`).</span></span> <span data-ttu-id="82779-308">L’hôte utilise l’option qui définit la valeur en dernier.</span><span class="sxs-lookup"><span data-stu-id="82779-308">The host uses whichever option sets the value last.</span></span> <span data-ttu-id="82779-309">Si vous souhaitez définir l’URL par défaut à une valeur par programmation, mais lui permettent d’être substitué par une configuration, vous pouvez utiliser la configuration de ligne de commande après avoir défini l’URL.</span><span class="sxs-lookup"><span data-stu-id="82779-309">If you want to programmatically set the default URL to one value but allow it to be overridden with configuration, you can use command-line configuration after setting the URL.</span></span> <span data-ttu-id="82779-310">Consultez [configuration de remplacement](#overriding-configuration).</span><span class="sxs-lookup"><span data-stu-id="82779-310">See [Overriding configuration](#overriding-configuration).</span></span>

## <a name="starting-the-host"></a><span data-ttu-id="82779-311">Démarrage de l’ordinateur hôte</span><span class="sxs-lookup"><span data-stu-id="82779-311">Starting the host</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82779-312">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82779-312">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="82779-313">**Exécuter**</span><span class="sxs-lookup"><span data-stu-id="82779-313">**Run**</span></span>

<span data-ttu-id="82779-314">Le `Run` méthode démarre l’application web et bloque le thread appelant jusqu'à ce que l’ordinateur hôte est arrêtée :</span><span class="sxs-lookup"><span data-stu-id="82779-314">The `Run` method starts the web app and blocks the calling thread until the host is shutdown:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="82779-315">**Start**</span><span class="sxs-lookup"><span data-stu-id="82779-315">**Start**</span></span>

<span data-ttu-id="82779-316">Vous pouvez exécuter l’hôte de manière non bloquante en appelant son `Start` méthode :</span><span class="sxs-lookup"><span data-stu-id="82779-316">You can run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="82779-317">Si vous passez une liste d’URL pour le `Start` (méthode), il est à l’écoute sur les URL spécifiées :</span><span class="sxs-lookup"><span data-stu-id="82779-317">If you pass a list of URLs to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="82779-318">Vous pouvez initialiser et démarrer un nouvel hôte à l’aide des valeurs par défaut préconfigurés de `CreateDefaultBuilder` à l’aide d’une méthode pratique statique.</span><span class="sxs-lookup"><span data-stu-id="82779-318">You can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="82779-319">Ces méthodes de démarrer le serveur sans la sortie de console et avec [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) attendre un saut (Ctrl-C/SIGINT ou SIGTERM) :</span><span class="sxs-lookup"><span data-stu-id="82779-319">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="82779-320">**Démarrer (application RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="82779-320">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="82779-321">Démarrer avec un `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="82779-321">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="82779-322">Effectuer une demande dans le navigateur pour `http://localhost:5000` pour recevoir la réponse « Hello World ! »</span><span class="sxs-lookup"><span data-stu-id="82779-322">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="82779-323">`WaitForShutdown`bloque jusqu'à ce qu’un saut (Ctrl-C/SIGINT ou SIGTERM) est émis.</span><span class="sxs-lookup"><span data-stu-id="82779-323">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="82779-324">L’application s’affiche la `Console.WriteLine` message et attend d’une touche quitter.</span><span class="sxs-lookup"><span data-stu-id="82779-324">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="82779-325">**Démarrer (string url RequestDelegate application)**</span><span class="sxs-lookup"><span data-stu-id="82779-325">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="82779-326">Démarrer avec une URL et `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="82779-326">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="82779-327">Produit le même résultat en tant que **début (application RequestDelegate)**, à l’exception de l’application répond à `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="82779-327">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="82779-328">**Démarrer (Action<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="82779-328">**Start(Action<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="82779-329">Utiliser une instance de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) à utiliser le routage intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="82779-329">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="82779-330">Utilisez les demandes suivantes du navigateur avec l’exemple :</span><span class="sxs-lookup"><span data-stu-id="82779-330">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="82779-331">Requête</span><span class="sxs-lookup"><span data-stu-id="82779-331">Request</span></span>                                    | <span data-ttu-id="82779-332">Réponse</span><span class="sxs-lookup"><span data-stu-id="82779-332">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="82779-333">Hello, Martin !</span><span class="sxs-lookup"><span data-stu-id="82779-333">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="82779-334">Buenos dias, Catrina !</span><span class="sxs-lookup"><span data-stu-id="82779-334">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="82779-335">Lève une exception avec la chaîne « ooops ! »</span><span class="sxs-lookup"><span data-stu-id="82779-335">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="82779-336">Lève une exception avec la chaîne « Uh oh ! »</span><span class="sxs-lookup"><span data-stu-id="82779-336">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="82779-337">Santé, Kevin !</span><span class="sxs-lookup"><span data-stu-id="82779-337">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="82779-338">Hello World!</span><span class="sxs-lookup"><span data-stu-id="82779-338">Hello World!</span></span>                             |

<span data-ttu-id="82779-339">`WaitForShutdown`bloque jusqu'à ce qu’un saut (Ctrl-C/SIGINT ou SIGTERM) est émis.</span><span class="sxs-lookup"><span data-stu-id="82779-339">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="82779-340">L’application s’affiche la `Console.WriteLine` message et attend d’une touche quitter.</span><span class="sxs-lookup"><span data-stu-id="82779-340">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="82779-341">**Démarrer (string url, Action<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="82779-341">**Start(string url, Action<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="82779-342">Utiliser une URL et une instance de `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="82779-342">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="82779-343">Produit le même résultat en tant que **Démarrer (Action<IRouteBuilder> routeBuilder)**, à l’exception de l’application répond à `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="82779-343">Produces the same result as **Start(Action<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="82779-344">**StartWith (Action<IApplicationBuilder> application)**</span><span class="sxs-lookup"><span data-stu-id="82779-344">**StartWith(Action<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="82779-345">Fournissez un délégué pour configurer un `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="82779-345">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="82779-346">Effectuer une demande dans le navigateur pour `http://localhost:5000` pour recevoir la réponse « Hello World ! »</span><span class="sxs-lookup"><span data-stu-id="82779-346">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="82779-347">`WaitForShutdown`bloque jusqu'à ce qu’un saut (Ctrl-C/SIGINT ou SIGTERM) est émis.</span><span class="sxs-lookup"><span data-stu-id="82779-347">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="82779-348">L’application s’affiche la `Console.WriteLine` message et attend d’une touche quitter.</span><span class="sxs-lookup"><span data-stu-id="82779-348">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="82779-349">**StartWith (string url, Action<IApplicationBuilder> application)**</span><span class="sxs-lookup"><span data-stu-id="82779-349">**StartWith(string url, Action<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="82779-350">Fournir une URL et un délégué pour configurer un `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="82779-350">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="82779-351">Produit le même résultat en tant que **StartWith (Action<IApplicationBuilder> app)**, à l’exception de l’application répond à `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="82779-351">Produces the same result as **StartWith(Action<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82779-352">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82779-352">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="82779-353">**Exécuter**</span><span class="sxs-lookup"><span data-stu-id="82779-353">**Run**</span></span>

<span data-ttu-id="82779-354">Le `Run` méthode démarre l’application web et bloque le thread appelant jusqu'à ce que l’ordinateur hôte est arrêtée :</span><span class="sxs-lookup"><span data-stu-id="82779-354">The `Run` method starts the web app and blocks the calling thread until the host is shutdown:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="82779-355">**Start**</span><span class="sxs-lookup"><span data-stu-id="82779-355">**Start**</span></span>

<span data-ttu-id="82779-356">Vous pouvez exécuter l’hôte de manière non bloquante en appelant son `Start` méthode :</span><span class="sxs-lookup"><span data-stu-id="82779-356">You can run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="82779-357">Si vous passez une liste d’URL pour le `Start` (méthode), il est à l’écoute sur les URL spécifiées :</span><span class="sxs-lookup"><span data-stu-id="82779-357">If you pass a list of URLs to the `Start` method, it listens on the URLs specified:</span></span>


```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

---

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="82779-358">Interface de IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="82779-358">IHostingEnvironment interface</span></span>

<span data-ttu-id="82779-359">Le [IHostingEnvironment interface](/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) fournit des informations sur l’environnement d’hébergement web de l’application.</span><span class="sxs-lookup"><span data-stu-id="82779-359">The [IHostingEnvironment interface](/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="82779-360">Vous pouvez utiliser [injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir le `IHostingEnvironment` afin d’utiliser ses propriétés et les méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="82779-360">You can use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="82779-361">Vous pouvez utiliser un [approche basée sur une convention](xref:fundamentals/environments#startup-conventions) pour configurer votre application au démarrage sur l’environnement.</span><span class="sxs-lookup"><span data-stu-id="82779-361">You can use a [convention-based approach](xref:fundamentals/environments#startup-conventions) to configure your app at startup based on the environment.</span></span> <span data-ttu-id="82779-362">Ou bien, vous pouvez injecter la `IHostingEnvironment` dans les `Startup` constructeur pour une utilisation dans `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="82779-362">Alternatively, you can inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="82779-363">Outre la `IsDevelopment` méthode d’extension, `IHostingEnvironment` offre `IsStaging`, `IsProduction`, et `IsEnvironment(string environmentName)` méthodes.</span><span class="sxs-lookup"><span data-stu-id="82779-363">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="82779-364">Consultez [fonctionne avec plusieurs environnements](xref:fundamentals/environments) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="82779-364">See [Working with multiple environments](xref:fundamentals/environments) for details.</span></span>

<span data-ttu-id="82779-365">Le `IHostingEnvironment` service peut également être injecté directement dans le `Configure` méthode de configuration de votre pipeline de traitement :</span><span class="sxs-lookup"><span data-stu-id="82779-365">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up your processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the developer exception page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="82779-366">Vous pouvez injecter `IHostingEnvironment` dans les `Invoke` méthode lors de la création personnalisée [intergiciel (middleware)](xref:fundamentals/middleware#writing-middleware):</span><span class="sxs-lookup"><span data-stu-id="82779-366">You can inject `IHostingEnvironment` into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware#writing-middleware):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="82779-367">Interface de IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="82779-367">IApplicationLifetime interface</span></span>

<span data-ttu-id="82779-368">Le [IApplicationLifetime interface](/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) vous permet d’effectuer les activités postérieur au démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="82779-368">The [IApplicationLifetime interface](/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows you to perform post-startup and shutdown activities.</span></span> <span data-ttu-id="82779-369">Trois propriétés sur l’interface sont des jetons d’annulation que vous pouvez inscrire avec `Action` méthodes pour définir les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="82779-369">Three properties on the interface are cancellation tokens that you can register with `Action` methods to define startup and shutdown events.</span></span> <span data-ttu-id="82779-370">Il existe également un `StopApplication` (méthode).</span><span class="sxs-lookup"><span data-stu-id="82779-370">There's also a `StopApplication` method.</span></span>

| <span data-ttu-id="82779-371">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="82779-371">Cancellation Token</span></span>    | <span data-ttu-id="82779-372">Déclenché lors de le &#8230;</span><span class="sxs-lookup"><span data-stu-id="82779-372">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="82779-373">L’ordinateur hôte a entièrement démarré.</span><span class="sxs-lookup"><span data-stu-id="82779-373">The host has fully started.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="82779-374">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="82779-374">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="82779-375">Les demandes peuvent être encore en cours.</span><span class="sxs-lookup"><span data-stu-id="82779-375">Requests may still be processing.</span></span> <span data-ttu-id="82779-376">Blocs d’arrêt jusqu'à la fin de cet événement.</span><span class="sxs-lookup"><span data-stu-id="82779-376">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="82779-377">L’ordinateur hôte est la fin de l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="82779-377">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="82779-378">Toutes les demandes doivent être complètement traitées.</span><span class="sxs-lookup"><span data-stu-id="82779-378">All requests should be completely processed.</span></span> <span data-ttu-id="82779-379">Blocs d’arrêt jusqu'à la fin de cet événement.</span><span class="sxs-lookup"><span data-stu-id="82779-379">Shutdown blocks until this event completes.</span></span> |

| <span data-ttu-id="82779-380">Méthode</span><span class="sxs-lookup"><span data-stu-id="82779-380">Method</span></span>            | <span data-ttu-id="82779-381">Action</span><span class="sxs-lookup"><span data-stu-id="82779-381">Action</span></span>                                           |
| ----------------- | ------------------------------------------------ |
| `StopApplication` | <span data-ttu-id="82779-382">Arrêt de demandes de l’application actuelle.</span><span class="sxs-lookup"><span data-stu-id="82779-382">Requests termination of the current application.</span></span> |

```csharp
public class Startup 
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime) 
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

## <a name="troubleshooting-systemargumentexception"></a><span data-ttu-id="82779-383">Résolution des problèmes de System.ArgumentException</span><span class="sxs-lookup"><span data-stu-id="82779-383">Troubleshooting System.ArgumentException</span></span>

<span data-ttu-id="82779-384">**S’applique à ASP.NET Core 2.0 uniquement**</span><span class="sxs-lookup"><span data-stu-id="82779-384">**Applies to ASP.NET Core 2.0 Only**</span></span>

<span data-ttu-id="82779-385">Si vous créez l’ordinateur hôte en injectant `IStartup` directement dans le conteneur d’injection de dépendance au lieu d’appeler `UseStartup` ou `Configure`, vous pouvez rencontrer l’erreur suivante : `Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided`.</span><span class="sxs-lookup"><span data-stu-id="82779-385">If you build the host by injecting `IStartup` directly into the dependency injection container rather than calling `UseStartup` or `Configure`, you may encounter the following error: `Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided`.</span></span>

<span data-ttu-id="82779-386">Cela se produit car le [applicationName(ApplicationKey)](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) (l’assembly actuel) est requis pour analyser pour `HostingStartupAttributes`.</span><span class="sxs-lookup"><span data-stu-id="82779-386">This occurs because the [applicationName(ApplicationKey)](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) (the current assembly) is required to scan for `HostingStartupAttributes`.</span></span> <span data-ttu-id="82779-387">Si vous avez injecter manuellement `IStartup` dans le conteneur d’injection de dépendance, ajoutez l’appel suivant à votre `WebHostBuilder` portant le nom de l’assembly spécifié :</span><span class="sxs-lookup"><span data-stu-id="82779-387">If you manually inject `IStartup` into the dependency injection container, add the following call to your `WebHostBuilder` with the assembly name specified:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("applicationName", "<Assembly Name>")
    ...
```

<span data-ttu-id="82779-388">Vous pouvez également ajouter un fantôme `Configure` à votre `WebHostBuilder`, qui affecte le `applicationName`(`ApplicationKey`) automatiquement :</span><span class="sxs-lookup"><span data-stu-id="82779-388">Alternatively, add a dummy `Configure` to your `WebHostBuilder`, which sets the `applicationName`(`ApplicationKey`) automatically:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .Configure(_ => { })
    ...
```

<span data-ttu-id="82779-389">**Remarque**: cela est uniquement nécessaire avec la version de ASP.NET Core 2.0 et uniquement lorsque vous n’appelez pas `UseStartup` ou `Configure`.</span><span class="sxs-lookup"><span data-stu-id="82779-389">**NOTE**: This is only required with the ASP.NET Core 2.0 release and only when you don't call `UseStartup` or `Configure`.</span></span>

<span data-ttu-id="82779-390">Pour plus d’informations, consultez [annonces : Microsoft.Extensions.PlatformAbstractions a été supprimé (commentaire)](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) et [StartupInjection exemple](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs).</span><span class="sxs-lookup"><span data-stu-id="82779-390">For more information, see [Announcements: Microsoft.Extensions.PlatformAbstractions has been removed (comment)](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) and the [StartupInjection sample](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82779-391">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="82779-391">Additional resources</span></span>

* [<span data-ttu-id="82779-392">Publier sur Windows à l’aide d’IIS</span><span class="sxs-lookup"><span data-stu-id="82779-392">Publish to Windows using IIS</span></span>](../publishing/iis.md)
* [<span data-ttu-id="82779-393">Publier sur Linux à l’aide de Nginx</span><span class="sxs-lookup"><span data-stu-id="82779-393">Publish to Linux using Nginx</span></span>](../publishing/linuxproduction.md)
* [<span data-ttu-id="82779-394">Publier sur Linux à l’aide d’Apache</span><span class="sxs-lookup"><span data-stu-id="82779-394">Publish to Linux using Apache</span></span>](../publishing/apache-proxy.md)
* [<span data-ttu-id="82779-395">Ordinateur hôte dans un Service Windows</span><span class="sxs-lookup"><span data-stu-id="82779-395">Host in a Windows Service</span></span>](xref:hosting/windows-service)

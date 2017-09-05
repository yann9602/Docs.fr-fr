---
title: "Hébergement dans ASP.NET Core | Documents Microsoft"
author: ardalis
description: "Introduction aux hôtes web ASP.NET Core."
keywords: "ASP.NET Core, hôte web, IWebHost"
ms.author: riande
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.assetid: 4e45311d-8d56-46e2-b99d-6f65b648a277
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0725f3d2c130068094792e5ba9e17481155e4294
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2017
---
# <a name="introduction-to-hosting-in-aspnet-core"></a><span data-ttu-id="2eb49-104">Introduction à l’hébergement dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2eb49-104">Introduction to hosting in ASP.NET Core</span></span>

<span data-ttu-id="2eb49-105">Par [Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="2eb49-105">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="2eb49-106">Pour exécuter une application ASP.NET Core, vous devez configurer et de lancer un ordinateur hôte à l’aide de `WebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-106">To run an ASP.NET Core app, you need to configure and launch a host using `WebHostBuilder`.</span></span>

## <a name="what-is-a-host"></a><span data-ttu-id="2eb49-107">Qu’est un ordinateur hôte ?</span><span class="sxs-lookup"><span data-stu-id="2eb49-107">What is a Host?</span></span>

<span data-ttu-id="2eb49-108">Les applications ASP.NET Core nécessitent un *hôte* dans lequel exécuter.</span><span class="sxs-lookup"><span data-stu-id="2eb49-108">ASP.NET Core apps require a *host* in which to execute.</span></span> <span data-ttu-id="2eb49-109">Un ordinateur hôte doit implémenter la `IWebHost` interface qui expose des ensembles de fonctionnalités et les services, et un `Start` (méthode).</span><span class="sxs-lookup"><span data-stu-id="2eb49-109">A host must implement the `IWebHost` interface, which exposes collections of features and services, and a `Start` method.</span></span> <span data-ttu-id="2eb49-110">L’ordinateur hôte est généralement créé à l’aide d’une instance d’un `WebHostBuilder`, ce qui génère et retourne un `WebHost` instance.</span><span class="sxs-lookup"><span data-stu-id="2eb49-110">The host is typically created using an instance of a `WebHostBuilder`, which builds and returns a  `WebHost` instance.</span></span> <span data-ttu-id="2eb49-111">Le `WebHost` fait référence au serveur qui va gérer les demandes.</span><span class="sxs-lookup"><span data-stu-id="2eb49-111">The `WebHost` references the server that will handle requests.</span></span> <span data-ttu-id="2eb49-112">En savoir plus sur [serveurs](servers/index.md).</span><span class="sxs-lookup"><span data-stu-id="2eb49-112">Learn more about [servers](servers/index.md).</span></span>

### <a name="what-is-the-difference-between-a-host-and-a-server"></a><span data-ttu-id="2eb49-113">Quelle est la différence entre un hôte et un serveur ?</span><span class="sxs-lookup"><span data-stu-id="2eb49-113">What is the difference between a host and a server?</span></span>

<span data-ttu-id="2eb49-114">L’hôte est responsable de la gestion de démarrage et la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="2eb49-114">The host is responsible for application startup and lifetime management.</span></span> <span data-ttu-id="2eb49-115">Le serveur est chargé d’accepter les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="2eb49-115">The server is responsible for accepting HTTP requests.</span></span> <span data-ttu-id="2eb49-116">Partie de la responsabilité de l’ordinateur hôte inclut la vérification que des services de l’application et le serveur sont disponible et correctement configuré.</span><span class="sxs-lookup"><span data-stu-id="2eb49-116">Part of the host's responsibility includes ensuring the application's services and the server are available and properly configured.</span></span> <span data-ttu-id="2eb49-117">Vous pouvez considérer l’hôte comme étant un wrapper autour du serveur.</span><span class="sxs-lookup"><span data-stu-id="2eb49-117">You can think of the host as being a wrapper around the server.</span></span> <span data-ttu-id="2eb49-118">L’hôte est configuré pour utiliser un serveur particulier ; le serveur n’a pas connaissance de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2eb49-118">The host is configured to use a particular server; the server is unaware of its host.</span></span>

## <a name="setting-up-a-host"></a><span data-ttu-id="2eb49-119">Configuration d’un ordinateur hôte</span><span class="sxs-lookup"><span data-stu-id="2eb49-119">Setting up a Host</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="2eb49-120">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="2eb49-120">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="2eb49-121">Vous créez un hôte à l’aide d’une instance de `WebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-121">You create a host using an instance of `WebHostBuilder`.</span></span> <span data-ttu-id="2eb49-122">Cette opération s’effectue généralement dans le point d’entrée de votre application : `public static void Main` (qui, dans les modèles de projet se trouve dans un *Program.cs* fichier).</span><span class="sxs-lookup"><span data-stu-id="2eb49-122">This is typically done in your app's entry point: `public static void Main` (which in the project templates is located in a *Program.cs* file).</span></span> <span data-ttu-id="2eb49-123">Par défaut *Program.cs*, comme illustré ci-dessous, montre comment utiliser un `WebHostBuilder` pour créer un ordinateur hôte.</span><span class="sxs-lookup"><span data-stu-id="2eb49-123">A typical *Program.cs*, shown below, demonstrates how to use a `WebHostBuilder` to build a host.</span></span>

<span data-ttu-id="2eb49-124">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=14,15,16,17,18,19,20,21)]</span><span class="sxs-lookup"><span data-stu-id="2eb49-124">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=14,15,16,17,18,19,20,21)]</span></span>

<span data-ttu-id="2eb49-125">Le `WebHostBuilder` est responsable de la création de l’hôte qui sera amorcer le serveur pour l’application.</span><span class="sxs-lookup"><span data-stu-id="2eb49-125">The `WebHostBuilder` is responsible for creating the host that will bootstrap the server for the app.</span></span> <span data-ttu-id="2eb49-126">`WebHostBuilder`nécessite de vous fournir un serveur qui implémente `IServer` (`UseKestrel` dans le code ci-dessus).</span><span class="sxs-lookup"><span data-stu-id="2eb49-126">`WebHostBuilder` requires you provide a server that implements `IServer` (`UseKestrel` in the code above).</span></span> <span data-ttu-id="2eb49-127">`UseKestrel`Spécifie que le serveur Kestrel sera utilisé par l’application.</span><span class="sxs-lookup"><span data-stu-id="2eb49-127">`UseKestrel` specifies the Kestrel server will be used by the app.</span></span>

<span data-ttu-id="2eb49-128">Le serveur *racine du contenu* détermine où il recherche les fichiers de contenu, tels que les fichiers de vue MVC.</span><span class="sxs-lookup"><span data-stu-id="2eb49-128">The server's *content root* determines where it searches for content files, like MVC View files.</span></span> <span data-ttu-id="2eb49-129">La racine de contenu par défaut est le dossier à partir duquel l’application est exécutée.</span><span class="sxs-lookup"><span data-stu-id="2eb49-129">The default content root is the folder from which the application is run.</span></span>

> [!NOTE]
> <span data-ttu-id="2eb49-130">Spécification de `Directory.GetCurrentDirectory` comme la racine du contenu servira de dossier racine du projet web racine du contenu de l’application lorsque l’application est démarrée à partir de ce dossier (par exemple, l’appel `dotnet run` à partir du dossier de projet web).</span><span class="sxs-lookup"><span data-stu-id="2eb49-130">Specifying `Directory.GetCurrentDirectory` as the content root will use the web project's root folder as the app's content root when the app is started from this folder (for example, calling `dotnet run` from the web project folder).</span></span> <span data-ttu-id="2eb49-131">Ceci est la valeur par défaut utilisée dans Visual Studio et `dotnet new` modèles.</span><span class="sxs-lookup"><span data-stu-id="2eb49-131">This is the default used in Visual Studio and `dotnet new` templates.</span></span>

<span data-ttu-id="2eb49-132">Pour utiliser IIS comme un proxy inverse, appelez `UseIISIntegration` dans le cadre de la création de l’ordinateur hôte.</span><span class="sxs-lookup"><span data-stu-id="2eb49-132">To use IIS as a reverse proxy, call `UseIISIntegration` as part of building the host.</span></span> 

<span data-ttu-id="2eb49-133">Notez que `UseIISIntegration` ne configure pas un *server*, comme `UseKestrel` est.</span><span class="sxs-lookup"><span data-stu-id="2eb49-133">Note that `UseIISIntegration` doesn't configure a *server*, like `UseKestrel` does.</span></span> <span data-ttu-id="2eb49-134">Pour utiliser IIS avec ASP.NET Core, vous devez spécifier les deux `UseKestrel` et `UseIISIntegration`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-134">To use IIS with ASP.NET Core, you must specify both `UseKestrel` and `UseIISIntegration`.</span></span> <span data-ttu-id="2eb49-135">`UseKestrel`crée le serveur web et héberge l’application.</span><span class="sxs-lookup"><span data-stu-id="2eb49-135">`UseKestrel` creates the web server and hosts the app.</span></span> <span data-ttu-id="2eb49-136">`UseIISIntegration`examine les variables d’environnement utilisées par IIS/IISExpress et configure les paramètres tels que le port d’écoute et les en-têtes à utiliser.</span><span class="sxs-lookup"><span data-stu-id="2eb49-136">`UseIISIntegration` examines environment variables used by IIS/IISExpress and configures settings such as the port to listen on and the headers to use.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="2eb49-137">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="2eb49-137">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="2eb49-138">Vous créez un hôte à l’aide d’une instance de `WebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-138">You create a host using an instance of `WebHostBuilder`.</span></span> <span data-ttu-id="2eb49-139">Cette opération s’effectue généralement dans le point d’entrée de votre application : `public static void Main` (qui, dans les modèles de projet se trouve dans un *Program.cs* fichier).</span><span class="sxs-lookup"><span data-stu-id="2eb49-139">This is typically done in your app's entry point: `public static void Main` (which in the project templates is located in a *Program.cs* file).</span></span> <span data-ttu-id="2eb49-140">Par défaut *Program.cs*, comme illustré ci-dessous, les appels `CreateDefaultbuilder` pour créer un ordinateur hôte :</span><span class="sxs-lookup"><span data-stu-id="2eb49-140">A typical *Program.cs*, shown below, calls `CreateDefaultbuilder` to build a host:</span></span>

<span data-ttu-id="2eb49-141">[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]</span><span class="sxs-lookup"><span data-stu-id="2eb49-141">[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]</span></span>

<span data-ttu-id="2eb49-142">`CreateDefaultbuilder`Crée une instance de `WebHostBuilder` pour générer l’hôte que les données du serveur pour l’application d’amorçage.</span><span class="sxs-lookup"><span data-stu-id="2eb49-142">`CreateDefaultbuilder` creates an instance of `WebHostBuilder` to build the host that bootstraps the server for the app.</span></span> <span data-ttu-id="2eb49-143">L’hôte requiert un [serveur qui implémente IServer](servers/index.md).</span><span class="sxs-lookup"><span data-stu-id="2eb49-143">The host requires a [server that implements IServer](servers/index.md).</span></span> <span data-ttu-id="2eb49-144">Serveurs intégrés sont [Kestrel](servers/kestrel.md) et [HTTP.sys](servers/httpsys.md); `CreateDefaultbuilder` utilisent Kestrel par défaut.</span><span class="sxs-lookup"><span data-stu-id="2eb49-144">The built-in servers are [Kestrel](servers/kestrel.md) and [HTTP.sys](servers/httpsys.md); `CreateDefaultbuilder` use Kestrel by default.</span></span>

<span data-ttu-id="2eb49-145">`CreateDefaultbuilder`exécute les tâches de configuration en plus de configurer Kestrel que le serveur web :</span><span class="sxs-lookup"><span data-stu-id="2eb49-145">`CreateDefaultbuilder` performs set-up tasks in addition to configuring Kestrel as the web server:</span></span>

* <span data-ttu-id="2eb49-146">Affecte à la racine de contenu `Directory.GetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-146">Sets the content root to `Directory.GetCurrentDirectory`.</span></span>
* <span data-ttu-id="2eb49-147">Configuration de la charge à partir de :</span><span class="sxs-lookup"><span data-stu-id="2eb49-147">Loads configuration from:</span></span>
  * <span data-ttu-id="2eb49-148">*appSettings.JSON*</span><span class="sxs-lookup"><span data-stu-id="2eb49-148">*appsettings.json*</span></span>
  * <span data-ttu-id="2eb49-149">*appSettings. \<EnvironmentName > .json*.</span><span class="sxs-lookup"><span data-stu-id="2eb49-149">*appsettings.\<EnvironmentName>.json*.</span></span>
  * <span data-ttu-id="2eb49-150">secrets d’utilisateur lorsque l’application s’exécute dans l’environnement de développement</span><span class="sxs-lookup"><span data-stu-id="2eb49-150">user secrets when the app runs in the Development environment</span></span>
  * <span data-ttu-id="2eb49-151">variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="2eb49-151">environment variables</span></span>
  * <span data-ttu-id="2eb49-152">arguments de ligne de commande fournis</span><span class="sxs-lookup"><span data-stu-id="2eb49-152">supplied command line args</span></span>
* <span data-ttu-id="2eb49-153">Configure la journalisation pour la sortie de console et de débogage, le filtrage des règles spécifiées dans une section de configuration de journalisation.</span><span class="sxs-lookup"><span data-stu-id="2eb49-153">Configures logging for console and debug output, with filtering rules specified in a Logging configuration section.</span></span>
* <span data-ttu-id="2eb49-154">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="2eb49-154">Enables IIS integration.</span></span>
* <span data-ttu-id="2eb49-155">Ajoute la page d’exception de développeur lorsque l’application s’exécute dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="2eb49-155">Adds the developer exception page when the app runs in the Development environment.</span></span>

<span data-ttu-id="2eb49-156">Le serveur *racine du contenu* détermine où il recherche les fichiers de contenu, tels que les fichiers de vue MVC.</span><span class="sxs-lookup"><span data-stu-id="2eb49-156">The server's *content root* determines where it searches for content files, like MVC View files.</span></span> <span data-ttu-id="2eb49-157">La racine de contenu par défaut est le dossier à partir duquel l’application est exécutée.</span><span class="sxs-lookup"><span data-stu-id="2eb49-157">The default content root is the folder from which the application is run.</span></span>

> [!NOTE]
> <span data-ttu-id="2eb49-158">Spécification de `Directory.GetCurrentDirectory` comme la racine du contenu servira de dossier racine du projet web racine du contenu de l’application lorsque l’application est démarrée à partir de ce dossier (par exemple, l’appel `dotnet run` à partir du dossier de projet web).</span><span class="sxs-lookup"><span data-stu-id="2eb49-158">Specifying `Directory.GetCurrentDirectory` as the content root will use the web project's root folder as the app's content root when the app is started from this folder (for example, calling `dotnet run` from the web project folder).</span></span> <span data-ttu-id="2eb49-159">Ceci est la valeur par défaut utilisée dans Visual Studio et `dotnet new` modèles.</span><span class="sxs-lookup"><span data-stu-id="2eb49-159">This is the default used in Visual Studio and `dotnet new` templates.</span></span>

<span data-ttu-id="2eb49-160">Lorsque vous utilisez IIS comme un proxy inverse, ASP.NET Core appelle automatiquement `UseIISIntegration` en tant que partie de la création de l’ordinateur hôte.</span><span class="sxs-lookup"><span data-stu-id="2eb49-160">When you use IIS as a reverse proxy, ASP.NET Core automatically calls `UseIISIntegration` as part of building the host.</span></span> <span data-ttu-id="2eb49-161">Pour plus d’informations, consultez [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="2eb49-161">For more information, see [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span>

<span data-ttu-id="2eb49-162">Notez que `UseIISIntegration` ne configure pas un *server*, comme `UseKestrel` est.</span><span class="sxs-lookup"><span data-stu-id="2eb49-162">Note that `UseIISIntegration` doesn't configure a *server*, like `UseKestrel` does.</span></span> <span data-ttu-id="2eb49-163">`UseKestrel`crée le serveur web et héberge l’application.</span><span class="sxs-lookup"><span data-stu-id="2eb49-163">`UseKestrel` creates the web server and hosts the app.</span></span> <span data-ttu-id="2eb49-164">`UseIISIntegration`examine les variables d’environnement utilisées par IIS/IISExpress et configure les paramètres tels que le port d’écoute et les en-têtes à utiliser.</span><span class="sxs-lookup"><span data-stu-id="2eb49-164">`UseIISIntegration` examines environment variables used by IIS/IISExpress and configures settings such as the port to listen on and the headers to use.</span></span>

---

<span data-ttu-id="2eb49-165">Une implémentation minimale de la configuration d’un ordinateur hôte (et une application ASP.NET Core) inclut uniquement un serveur et la configuration du pipeline de demande de l’application :</span><span class="sxs-lookup"><span data-stu-id="2eb49-165">A minimal implementation of configuring a host (and an ASP.NET Core app) would include just a server and configuration of the app's request pipeline:</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .Configure(app =>
    {
        app.Run(async (context) => await context.Response.WriteAsync("Hi!"));
    })
    .Build();

host.Run();
```

> [!NOTE]
> <span data-ttu-id="2eb49-166">Lorsque vous configurez un ordinateur hôte, vous pouvez fournir `Configure` et `ConfigureServices` méthodes, à la place ou en plus de spécifier un `Startup` classe (qui doit également définir ces méthodes - consultez [démarrage de l’Application](startup.md)).</span><span class="sxs-lookup"><span data-stu-id="2eb49-166">When setting up a host, you can provide `Configure` and `ConfigureServices` methods, instead of or in addition to specifying a `Startup` class (which must also define these methods - see [Application Startup](startup.md)).</span></span> <span data-ttu-id="2eb49-167">Appels multiples à `ConfigureServices` sont ajoutées à un autre ; les appels à `Configure` ou `UseStartup` remplaceront les paramètres précédents.</span><span class="sxs-lookup"><span data-stu-id="2eb49-167">Multiple calls to `ConfigureServices` will append to one another; calls to `Configure` or `UseStartup` will replace previous settings.</span></span>

## <a name="configuring-a-host"></a><span data-ttu-id="2eb49-168">Configuration d’un hôte</span><span class="sxs-lookup"><span data-stu-id="2eb49-168">Configuring a Host</span></span>

<span data-ttu-id="2eb49-169">Le `WebHostBuilder` fournit des méthodes permettant de définir la plupart des valeurs de configuration disponibles pour l’hôte, ce qui peut également être définie directement à l’aide de `UseSetting` et la clé associée.</span><span class="sxs-lookup"><span data-stu-id="2eb49-169">The `WebHostBuilder` provides methods for setting most of the available configuration values for the host, which can also be set directly using `UseSetting` and associated key.</span></span>

### <a name="host-configuration-values"></a><span data-ttu-id="2eb49-170">Valeurs de Configuration d’hôte</span><span class="sxs-lookup"><span data-stu-id="2eb49-170">Host Configuration Values</span></span>

<span data-ttu-id="2eb49-171">**Capturer les erreurs de démarrage**`bool`</span><span class="sxs-lookup"><span data-stu-id="2eb49-171">**Capture Startup Errors** `bool`</span></span>

<span data-ttu-id="2eb49-172">Clé : `captureStartupErrors`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-172">Key: `captureStartupErrors`.</span></span> <span data-ttu-id="2eb49-173">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-173">Defaults to `false`.</span></span> <span data-ttu-id="2eb49-174">Lorsque `false`, erreurs de résultat de démarrage de l’ordinateur hôte en cours de fermeture.</span><span class="sxs-lookup"><span data-stu-id="2eb49-174">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="2eb49-175">Lorsque `true`, l’hôte de capture toutes les exceptions à partir de la `Startup` classe et essayez de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="2eb49-175">When `true`, the host will capture any exceptions from the `Startup` class and attempt to start the server.</span></span> <span data-ttu-id="2eb49-176">Il affiche une page d’erreur (générique, ou plus, selon le paramètre d’erreurs détaillées, ci-dessous) pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="2eb49-176">It will display an error page (generic, or detailed, based on the Detailed Errors setting, below) for every request.</span></span> <span data-ttu-id="2eb49-177">À l’aide de la `CaptureStartupErrors` (méthode).</span><span class="sxs-lookup"><span data-stu-id="2eb49-177">Set using the `CaptureStartupErrors` method.</span></span>

<span data-ttu-id="2eb49-178">Remarque : Quand votre application s’exécute avec Kestrel et IIS, le comportement par défaut consiste à capturer les erreurs de démarrage.</span><span class="sxs-lookup"><span data-stu-id="2eb49-178">Note: When your app runs with Kestrel and IIS, the default behavior is to capture startup errors.</span></span> 

```csharp
new WebHostBuilder()
    .CaptureStartupErrors(true)
   ```

<span data-ttu-id="2eb49-179">**Racine du contenu**`string`</span><span class="sxs-lookup"><span data-stu-id="2eb49-179">**Content Root** `string`</span></span>

<span data-ttu-id="2eb49-180">Clé : `contentRoot`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-180">Key: `contentRoot`.</span></span> <span data-ttu-id="2eb49-181">Par défaut, le dossier dans lequel l’assembly d’application réside (pour Kestrel ; IIS utilisera la racine du projet web par défaut).</span><span class="sxs-lookup"><span data-stu-id="2eb49-181">Defaults to the folder where the application assembly resides (for Kestrel; IIS will use the web project root by default).</span></span> <span data-ttu-id="2eb49-182">Ce paramètre détermine où ASP.NET Core commence à rechercher les fichiers de contenu, tels que les vues MVC.</span><span class="sxs-lookup"><span data-stu-id="2eb49-182">This setting determines where ASP.NET Core will begin searching for content files, such as MVC Views.</span></span> <span data-ttu-id="2eb49-183">Également utilisé en tant que le chemin d’accès de base pour le [configuration de la racine Web](#web-root-setting).</span><span class="sxs-lookup"><span data-stu-id="2eb49-183">Also used as the base path for the [Web Root Setting](#web-root-setting).</span></span> <span data-ttu-id="2eb49-184">À l’aide de la `UseContentRoot` (méthode).</span><span class="sxs-lookup"><span data-stu-id="2eb49-184">Set using the `UseContentRoot` method.</span></span> <span data-ttu-id="2eb49-185">Chemin d’accès doit exister ou ordinateur hôte ne pourront pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="2eb49-185">Path must exist, or host will fail to start.</span></span>

```csharp
new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
   ```

<span data-ttu-id="2eb49-186">**Erreurs détaillées**`bool`</span><span class="sxs-lookup"><span data-stu-id="2eb49-186">**Detailed Errors** `bool`</span></span>

<span data-ttu-id="2eb49-187">Clé : `detailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-187">Key: `detailedErrors`.</span></span> <span data-ttu-id="2eb49-188">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-188">Defaults to `false`.</span></span> <span data-ttu-id="2eb49-189">Lorsque `true` (ou lorsque environnement a la valeur « Développement »), l’application affiche les détails des exceptions de démarrage, au lieu de simplement une page d’erreur générique.</span><span class="sxs-lookup"><span data-stu-id="2eb49-189">When `true` (or when Environment is set to "Development"), the app will display details of startup exceptions, instead of just a generic error page.</span></span> <span data-ttu-id="2eb49-190">À l’aide de `UseSetting`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-190">Set using `UseSetting`.</span></span>

```csharp
new WebHostBuilder()
    .UseSetting("detailedErrors", "true")
```

<span data-ttu-id="2eb49-191">Lorsque les erreurs détaillées a la valeur `false` et capturer les erreurs de démarrage est `true`, une page d’erreur générique s’affiche en réponse à chaque demande au serveur.</span><span class="sxs-lookup"><span data-stu-id="2eb49-191">When Detailed Errors is set to `false` and Capture Startup Errors is `true`, a generic error page is displayed in response to every request to the server.</span></span>

![Page d’erreur générique](hosting/_static/generic-error-page.png)

<span data-ttu-id="2eb49-193">Lorsque les erreurs détaillées a la valeur `true` et capturer les erreurs de démarrage est `true`, une page d’erreur détaillé s’affiche en réponse à chaque demande au serveur.</span><span class="sxs-lookup"><span data-stu-id="2eb49-193">When Detailed Errors is set to `true` and Capture Startup Errors is `true`, a detailed error page is displayed in response to every request to the server.</span></span>

![Page d’erreur détaillées](hosting/_static/detailed-error-page.png)

<span data-ttu-id="2eb49-195">**Environnement**`string`</span><span class="sxs-lookup"><span data-stu-id="2eb49-195">**Environment** `string`</span></span>

<span data-ttu-id="2eb49-196">Clé : `environment`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-196">Key: `environment`.</span></span> <span data-ttu-id="2eb49-197">La valeur par défaut est « Production ».</span><span class="sxs-lookup"><span data-stu-id="2eb49-197">Defaults to "Production".</span></span> <span data-ttu-id="2eb49-198">Peut être définie sur n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="2eb49-198">May be set to any value.</span></span> <span data-ttu-id="2eb49-199">Les valeurs définies par l’infrastructure incluent « Développement », « Étape intermédiaire » et « Production ».</span><span class="sxs-lookup"><span data-stu-id="2eb49-199">Framework-defined values include "Development", "Staging", and "Production".</span></span> <span data-ttu-id="2eb49-200">Valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="2eb49-200">Values are not case sensitive.</span></span> <span data-ttu-id="2eb49-201">Consultez [fonctionne avec plusieurs environnements](environments.md).</span><span class="sxs-lookup"><span data-stu-id="2eb49-201">See [Working with Multiple Environments](environments.md).</span></span> <span data-ttu-id="2eb49-202">À l’aide de la `UseEnvironment` (méthode).</span><span class="sxs-lookup"><span data-stu-id="2eb49-202">Set using the `UseEnvironment` method.</span></span>

```csharp
new WebHostBuilder()
    .UseEnvironment("Development")
```

> [!NOTE]
> <span data-ttu-id="2eb49-203">Par défaut, l’environnement est en lecture à partir de la `ASPNETCORE_ENVIRONMENT` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="2eb49-203">By default, the environment is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="2eb49-204">Lorsque vous utilisez Visual Studio, les variables d’environnement peuvent être définies dans le *launchSettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="2eb49-204">When using Visual Studio, environment variables may be set in the *launchSettings.json* file.</span></span>

<a id="server-urls"></a>

<span data-ttu-id="2eb49-205">**URL du serveur**`string`</span><span class="sxs-lookup"><span data-stu-id="2eb49-205">**Server URLs** `string`</span></span>

<span data-ttu-id="2eb49-206">Clé : `urls`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-206">Key: `urls`.</span></span> <span data-ttu-id="2eb49-207">Un point-virgule ( ;) la valeur séparées par des préfixes de la liste des URL à laquelle le serveur doit répondre.</span><span class="sxs-lookup"><span data-stu-id="2eb49-207">Set to a semicolon (;) separated list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="2eb49-208">Par exemple, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-208">For example, `http://localhost:123`.</span></span> <span data-ttu-id="2eb49-209">Le nom de domaine/de l’hôte peut être remplacé par «\*» pour indiquer le serveur doit écouter les demandes sur n’importe quelle adresse IP ou hôte à l’aide du port spécifié et le protocole (par exemple, `http://*:5000` ou `https://*:5001`).</span><span class="sxs-lookup"><span data-stu-id="2eb49-209">The domain/host name can be replaced with "\*" to indicate the server should listen to requests on any IP address or host using the specified port and protocol (for example, `http://*:5000` or `https://*:5001`).</span></span> <span data-ttu-id="2eb49-210">Le protocole (`http://` ou `https://`) doivent être incluses avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="2eb49-210">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="2eb49-211">Les préfixes sont interprétés par le serveur configuré ; formats pris en charge varie entre serveurs.</span><span class="sxs-lookup"><span data-stu-id="2eb49-211">The prefixes are interpreted by the configured server; supported formats will vary between servers.</span></span>

```csharp
new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="2eb49-212">Dans ASP.NET 2.0 de noyaux, Kestrel a sa propre API de configuration du point de terminaison et ne prend pas en charge `https://` dans le `urls` chaîne.</span><span class="sxs-lookup"><span data-stu-id="2eb49-212">In ASP.NET Core 2.0, Kestrel has its own endpoint configuration API and does not support `https://` in the `urls` string.</span></span> <span data-ttu-id="2eb49-213">Pour plus d’informations, consultez [présentation Kestrel](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="2eb49-213">For more information, see [Introduction to Kestrel](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span></span>

<span data-ttu-id="2eb49-214">**Assembly de démarrage**`string`</span><span class="sxs-lookup"><span data-stu-id="2eb49-214">**Startup Assembly** `string`</span></span>

<span data-ttu-id="2eb49-215">Clé : `startupAssembly`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-215">Key: `startupAssembly`.</span></span> <span data-ttu-id="2eb49-216">Détermine l’assembly à rechercher la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="2eb49-216">Determines the assembly to search for the `Startup` class.</span></span> <span data-ttu-id="2eb49-217">À l’aide de la `UseStartup` (méthode).</span><span class="sxs-lookup"><span data-stu-id="2eb49-217">Set using the `UseStartup` method.</span></span> <span data-ttu-id="2eb49-218">Peut faire référence à la place à l’aide du type spécifique `WebHostBuilder.UseStartup<StartupType>`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-218">May instead reference specific type using `WebHostBuilder.UseStartup<StartupType>`.</span></span> <span data-ttu-id="2eb49-219">Si plusieurs `UseStartup` méthodes sont appelées, le dernier est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="2eb49-219">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
```

<a name=web-root-setting></a>

<span data-ttu-id="2eb49-220">**Racine du site Web**`string`</span><span class="sxs-lookup"><span data-stu-id="2eb49-220">**Web Root** `string`</span></span>

<span data-ttu-id="2eb49-221">Clé : `webroot`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-221">Key: `webroot`.</span></span> <span data-ttu-id="2eb49-222">Si non spécifié à la valeur par défaut est `(Content Root Path)\wwwroot`, s’il existe.</span><span class="sxs-lookup"><span data-stu-id="2eb49-222">If not specified the default is `(Content Root Path)\wwwroot`, if it exists.</span></span> <span data-ttu-id="2eb49-223">Si ce chemin d’accès n’existe pas, un fournisseur de fichiers de l’absence d’opération est utilisé.</span><span class="sxs-lookup"><span data-stu-id="2eb49-223">If this path doesn't exist, then a no-op file provider is used.</span></span> <span data-ttu-id="2eb49-224">À l’aide de `UseWebRoot`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-224">Set using `UseWebRoot`.</span></span>

```csharp
new WebHostBuilder()
    .UseWebRoot("public")
```

### <a name="overriding-configuration"></a><span data-ttu-id="2eb49-225">Substitution de Configuration</span><span class="sxs-lookup"><span data-stu-id="2eb49-225">Overriding Configuration</span></span>

<span data-ttu-id="2eb49-226">Utilisez [Configuration](configuration.md) pour définir les valeurs de configuration à utiliser par l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2eb49-226">Use [Configuration](configuration.md) to set configuration values to be used by the host.</span></span> <span data-ttu-id="2eb49-227">Ces valeurs peuvent être remplacées par la suite.</span><span class="sxs-lookup"><span data-stu-id="2eb49-227">These values may be subsequently overridden.</span></span> <span data-ttu-id="2eb49-228">Cela est spécifié à l’aide de `UseConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-228">This is specified using `UseConfiguration`.</span></span>

```csharp
public static void Main(string[] args)
{
    var config = new ConfigurationBuilder()
        .AddJsonFile("hosting.json", optional: true)
        .AddCommandLine(args)
        .Build();

    var host = new WebHostBuilder()
        .UseConfiguration(config)
        .UseKestrel()
        .Configure(app =>
        {
            app.Run(async (context) => await context.Response.WriteAsync("Hi!"));
        })
        .Build();

    host.Run();
}
```

<span data-ttu-id="2eb49-229">Dans l’exemple ci-dessus, les arguments de ligne de commande peuvent être transmis dans pour configurer l’ordinateur hôte, ou les paramètres de configuration peuvent éventuellement être spécifiés dans un *hosting.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="2eb49-229">In the example above, command-line arguments may be passed in to configure the host, or configuration settings may optionally be specified in a *hosting.json* file.</span></span> <span data-ttu-id="2eb49-230">Pour spécifier l’hôte s’exécutent sur une URL particulière, vous pouvez transmettre la valeur souhaitée à partir d’une invite de commandes :</span><span class="sxs-lookup"><span data-stu-id="2eb49-230">To specify the host run on a particular URL, you could pass in the desired value from a command prompt:</span></span>

```console
dotnet run --urls "http://*:5000"
```

<span data-ttu-id="2eb49-231">Le `Run` méthode démarre l’application web et bloque le thread appelant jusqu'à ce que l’ordinateur hôte est arrêtée.</span><span class="sxs-lookup"><span data-stu-id="2eb49-231">The `Run` method starts the web app and blocks the calling thread until the host is shutdown.</span></span>

```csharp
host.Run();
```

<span data-ttu-id="2eb49-232">Vous pouvez exécuter l’hôte de manière non bloquante en appelant son `Start` méthode :</span><span class="sxs-lookup"><span data-stu-id="2eb49-232">You can run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="2eb49-233">Passez une liste d’URL pour le `Start` (méthode) et qu’il écoute sur les URL spécifiées :</span><span class="sxs-lookup"><span data-stu-id="2eb49-233">Pass a list of URLs to the `Start` method and it will listen on the URLs specified:</span></span>

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

<span data-ttu-id="2eb49-234">Les formats d’URL qui sont valides ici dépendent du serveur que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="2eb49-234">The URL formats that are valid here depend on the server you're using.</span></span> <span data-ttu-id="2eb49-235">Pour plus d’informations, consultez [URL de serveur](#server-urls) plus haut dans cet article.</span><span class="sxs-lookup"><span data-stu-id="2eb49-235">For more information, see [Server URLs](#server-urls) earlier in this article.</span></span>

> [!NOTE]
> <span data-ttu-id="2eb49-236">Le `UseConfiguration` méthode d’extension n’est pas actuellement capable d’analyser une section de configuration retournée par `GetSection` (par exemple, `.UseConfiguration(Configuration.GetSection("section"))`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-236">The `UseConfiguration` extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="2eb49-237">Le `GetSection` méthode filtre les clés de configuration à la section demandée, mais laisse le nom de la section sur les clés (par exemple, `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="2eb49-237">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="2eb49-238">Le `UseConfiguration` méthode attend les clés pour faire correspondre le `WebHostBuilder` clés (par exemple, `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="2eb49-238">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="2eb49-239">La présence d’un nom de la section sur les clés empêche des valeurs de la section à partir de la configuration de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2eb49-239">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="2eb49-240">Ce problème sera résolu dans une prochaine mise en production.</span><span class="sxs-lookup"><span data-stu-id="2eb49-240">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="2eb49-241">Pour plus d’informations et des solutions de contournement, consultez [en passant de la section de configuration dans WebHostBuilder.UseConfiguration utilise des clés complètes](https://github.com/aspnet/Hosting/issues/839).</span><span class="sxs-lookup"><span data-stu-id="2eb49-241">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>

### <a name="ordering-importance"></a><span data-ttu-id="2eb49-242">Ordre d’Importance</span><span class="sxs-lookup"><span data-stu-id="2eb49-242">Ordering Importance</span></span>

<span data-ttu-id="2eb49-243">`WebHostBuilder`les paramètres sont tout d’abord lus à partir de certaines variables d’environnement, si définie.</span><span class="sxs-lookup"><span data-stu-id="2eb49-243">`WebHostBuilder` settings are first read from certain environment variables, if set.</span></span> <span data-ttu-id="2eb49-244">Ces variables d’environnement doivent utiliser le format `ASPNETCORE_{configurationKey}`, par exemple définir les URL écoute sur le serveur par défaut, vous devez définir `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="2eb49-244">These environment variables must use the format `ASPNETCORE_{configurationKey}`, so for example to set the URLs the server will listen on by default, you would set `ASPNETCORE_URLS`.</span></span>

<span data-ttu-id="2eb49-245">Vous pouvez remplacer ces valeurs de variable d’environnement en spécifiant la configuration (à l’aide de `UseConfiguration`) ou en définissant la valeur de manière explicite (à l’aide de `UseUrls` par exemple).</span><span class="sxs-lookup"><span data-stu-id="2eb49-245">You can override any of these environment variable values by specifying configuration (using `UseConfiguration`) or by setting the value explicitly (using `UseUrls` for instance).</span></span> <span data-ttu-id="2eb49-246">L’hôte utilisera l’option qui définit la valeur en dernier.</span><span class="sxs-lookup"><span data-stu-id="2eb49-246">The host will use whichever option sets the value last.</span></span> <span data-ttu-id="2eb49-247">Pour cette raison, `UseIISIntegration` doivent apparaître après `UseUrls`, car elle remplace l’URL dynamique fourni par IIS.</span><span class="sxs-lookup"><span data-stu-id="2eb49-247">For this reason, `UseIISIntegration` must appear after `UseUrls`, because it replaces the URL with one dynamically provided by IIS.</span></span> <span data-ttu-id="2eb49-248">Si vous souhaitez par programmation la valeur de l’URL par défaut une valeur, mais lui permettent d’être substitué par une configuration, vous pourriez configurer l’ordinateur hôte comme suit :</span><span class="sxs-lookup"><span data-stu-id="2eb49-248">If you want to programmatically set the default URL to one value, but allow it to be overridden with configuration, you could configure the host as follows:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseUrls("http://*:1000") // default URL
    .UseConfiguration(config) // override from command line
    .UseKestrel()
    .Build();
```

## <a name="additional-resources"></a><span data-ttu-id="2eb49-249">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2eb49-249">Additional resources</span></span>

* [<span data-ttu-id="2eb49-250">Publier sur Windows à l’aide d’IIS</span><span class="sxs-lookup"><span data-stu-id="2eb49-250">Publish to Windows using IIS</span></span>](../publishing/iis.md)
* [<span data-ttu-id="2eb49-251">Publier sur Linux à l’aide de Nginx</span><span class="sxs-lookup"><span data-stu-id="2eb49-251">Publish to Linux using Nginx</span></span>](../publishing/linuxproduction.md)
* [<span data-ttu-id="2eb49-252">Publier sur Linux à l’aide d’Apache</span><span class="sxs-lookup"><span data-stu-id="2eb49-252">Publish to Linux using Apache</span></span>](../publishing/apache-proxy.md)
* [<span data-ttu-id="2eb49-253">Ordinateur hôte dans un Service Windows</span><span class="sxs-lookup"><span data-stu-id="2eb49-253">Host in a Windows Service</span></span>](xref:hosting/windows-service)


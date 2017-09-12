---
title: "Notions de base d’ASP.NET Core"
author: rick-anderson
description: "Cet article fournit une vue d’ensemble des concepts fondamentaux liés à la génération d’applications ASP.NET Core."
keywords: "ASP.NET Core,notions de base,vue d’ensemble"
ms.author: riande
manager: wpickett
ms.date: 08/18/2017
ms.topic: get-started-article
ms.assetid: a19b7836-63e4-44e8-8250-50d426dd1070
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/index
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5d8ca35b0e2e4b6e9b1ec745a3a7cf7c3983c461
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="aspnet-core-fundamentals-overview"></a><span data-ttu-id="9dc5b-104">Vue d’ensemble des notions de base d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9dc5b-104">ASP.NET Core fundamentals overview</span></span>

<span data-ttu-id="9dc5b-105">Une application ASP.NET Core est une application console qui crée un serveur web dans sa méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="9dc5b-105">An ASP.NET Core application is a console app that creates a web server in its `Main` method:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9dc5b-106">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9dc5b-106">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="9dc5b-107">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs)]</span><span class="sxs-lookup"><span data-stu-id="9dc5b-107">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs)]</span></span>

<span data-ttu-id="9dc5b-108">La méthode `Main` appelle `WebHost.CreateDefaultBuilder`, qui suit le modèle du générateur pour créer un hôte d’application web.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-108">The `Main` method invokes `WebHost.CreateDefaultBuilder`, which follows the builder pattern to create a web application host.</span></span> <span data-ttu-id="9dc5b-109">Le générateur a des méthodes qui définissent le serveur web (par exemple `UseKestrel`) et la classe de démarrage (`UseStartup`).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-109">The builder has methods that define the web server (for example, `UseKestrel`) and the startup class (`UseStartup`).</span></span> <span data-ttu-id="9dc5b-110">Dans l’exemple précédent, un serveur web [Kestrel](xref:fundamentals/servers/kestrel) est alloué automatiquement.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-110">In the preceding example, a [Kestrel](xref:fundamentals/servers/kestrel) web server is automatically allocated.</span></span> <span data-ttu-id="9dc5b-111">L’hôte web d’ASP.NET Core tente de s’exécuter sur IIS, s’il est disponible.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-111">ASP.NET Core's web host will attempt to run on IIS, if it is available.</span></span> <span data-ttu-id="9dc5b-112">D’autres serveurs web, tels que [HTTP.sys](xref:fundamentals/servers/httpsys), peuvent être utilisés via l’appel de la méthode d’extension appropriée.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-112">Other web servers, such as [HTTP.sys](xref:fundamentals/servers/httpsys), can be used by invoking the appropriate extension method.</span></span> <span data-ttu-id="9dc5b-113">`UseStartup` est expliqué de manière plus détaillée dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-113">`UseStartup` is explained further in the next section.</span></span>

<span data-ttu-id="9dc5b-114">`IWebHostBuilder`, le type de retour de l’appel de `WebHost.CreateDefaultBuilder`, fournit de nombreuses méthodes facultatives.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-114">`IWebHostBuilder`, the return type of the `WebHost.CreateDefaultBuilder` invocation, provides many optional methods.</span></span> <span data-ttu-id="9dc5b-115">Certaines de ces méthodes incluent `UseHttpSys` pour l’hébergement de l’application dans HTTP.sys, et `UseContentRoot` pour la spécification du répertoire de contenu racine.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-115">Some of these methods include `UseHttpSys` for hosting the application in HTTP.sys, and `UseContentRoot` for specifying the root content directory.</span></span> <span data-ttu-id="9dc5b-116">Les méthodes `Build` et `Run` génèrent l’objet `IWebHost`, qui héberge l’application et démarre l’écoute des requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-116">The `Build` and `Run` methods build the `IWebHost` object that will host the application and begin listening for HTTP requests.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9dc5b-117">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9dc5b-117">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="9dc5b-118">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs)]</span><span class="sxs-lookup"><span data-stu-id="9dc5b-118">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs)]</span></span>

<span data-ttu-id="9dc5b-119">La méthode `Main` utilise `WebHostBuilder`, qui suit le modèle du générateur pour créer un hôte d’application web.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-119">The `Main` method uses `WebHostBuilder`, which follows the builder pattern to create a web application host.</span></span> <span data-ttu-id="9dc5b-120">Le générateur a des méthodes qui définissent le serveur web (par exemple `UseKestrel`) et la classe de démarrage (`UseStartup`).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-120">The builder has methods that define the web server (for example, `UseKestrel`) and the startup class (`UseStartup`).</span></span> <span data-ttu-id="9dc5b-121">Dans l’exemple précédent, le serveur web [Kestrel](xref:fundamentals/servers/kestrel) est utilisé.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-121">In the preceding example, the [Kestrel](xref:fundamentals/servers/kestrel) web server is used.</span></span> <span data-ttu-id="9dc5b-122">D’autres serveurs web, tels que [WebListener](xref:fundamentals/servers/weblistener), peuvent être utilisés via l’appel de la méthode d’extension appropriée.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-122">Other web servers, such as [WebListener](xref:fundamentals/servers/weblistener), can be used by invoking the appropriate extension method.</span></span> <span data-ttu-id="9dc5b-123">`UseStartup` est expliqué de manière plus détaillée dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-123">`UseStartup` is explained further in the next section.</span></span>

<span data-ttu-id="9dc5b-124">`WebHostBuilder` fournit de nombreuses méthodes facultatives, notamment `UseIISIntegration` pour l’hébergement dans IIS et IIS Express, ainsi que `UseContentRoot` pour la spécification du répertoire de contenu racine.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-124">`WebHostBuilder` provides many optional methods, including `UseIISIntegration` for hosting in IIS and IIS Express, and `UseContentRoot` for specifying the root content directory.</span></span> <span data-ttu-id="9dc5b-125">Les méthodes `Build` et `Run` génèrent l’objet `IWebHost`, qui héberge l’application et démarre l’écoute des requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-125">The `Build` and `Run` methods build the `IWebHost` object that will host the application and begin listening for HTTP requests.</span></span>

---

## <a name="startup"></a><span data-ttu-id="9dc5b-126">Démarrage</span><span class="sxs-lookup"><span data-stu-id="9dc5b-126">Startup</span></span>

<span data-ttu-id="9dc5b-127">La méthode `UseStartup` sur `WebHostBuilder` spécifie la classe `Startup` pour votre application :</span><span class="sxs-lookup"><span data-stu-id="9dc5b-127">The `UseStartup` method on `WebHostBuilder` specifies the `Startup` class for your app:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9dc5b-128">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9dc5b-128">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="9dc5b-129">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs?highlight=10&range=6-17)]</span><span class="sxs-lookup"><span data-stu-id="9dc5b-129">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs?highlight=10&range=6-17)]</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9dc5b-130">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9dc5b-130">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="9dc5b-131">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs?highlight=7&range=6-17)]</span><span class="sxs-lookup"><span data-stu-id="9dc5b-131">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs?highlight=7&range=6-17)]</span></span>

---

<span data-ttu-id="9dc5b-132">La classe `Startup` est l’emplacement où vous définissez le pipeline de traitement des requêtes et où sont configurés les services nécessaires à l’application.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-132">The `Startup` class is where you define the request handling pipeline and where any services needed by the application are configured.</span></span> <span data-ttu-id="9dc5b-133">La classe `Startup` doit être publique et contenir les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9dc5b-133">The `Startup` class must be public and contain the following methods:</span></span>

```csharp
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app)
    {
    }
}
```

* <span data-ttu-id="9dc5b-134">`ConfigureServices` définit les [services](#services) utilisés par votre application (par exemple ASP.NET Core MVC, Entity Framework Core, Identity, etc.).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-134">`ConfigureServices` defines the [Services](#services) used by your application (such as ASP.NET Core MVC, Entity Framework Core, Identity, etc.).</span></span>

* <span data-ttu-id="9dc5b-135">`Configure` définit les [intergiciels (middleware)](xref:fundamentals/middleware) du pipeline de requêtes.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-135">`Configure` defines the [middleware](xref:fundamentals/middleware) in the request pipeline.</span></span>

<span data-ttu-id="9dc5b-136">Pour plus d’informations, consultez [Démarrage de l’application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-136">For more information, see [Application startup](xref:fundamentals/startup).</span></span>

## <a name="services"></a><span data-ttu-id="9dc5b-137">Services</span><span class="sxs-lookup"><span data-stu-id="9dc5b-137">Services</span></span>

<span data-ttu-id="9dc5b-138">Un service est un composant destiné à une consommation courante dans une application.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-138">A service is a component that is intended for common consumption in an application.</span></span> <span data-ttu-id="9dc5b-139">Les services sont accessibles via l’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-139">Services are made available through [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="9dc5b-140">ASP.NET Core inclut un conteneur IoC (inversion de contrôle) natif qui prend en charge l’[injection de constructeurs](xref:mvc/controllers/dependency-injection#constructor-injection) par défaut.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-140">ASP.NET Core includes a native inversion of control (IoC) container that supports [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) by default.</span></span> <span data-ttu-id="9dc5b-141">Vous pouvez remplacer le conteneur natif par le conteneur de votre choix.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-141">The native container can be replaced with your container of choice.</span></span> <span data-ttu-id="9dc5b-142">L’injection de dépendances n’offre pas seulement un couplage faible, elle permet également d’accéder aux services dans l’ensemble de votre application.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-142">In addition to its loose coupling benefit, DI makes services available throughout your application.</span></span> <span data-ttu-id="9dc5b-143">Par exemple, la [journalisation](xref:fundamentals/logging) est disponible dans l’ensemble de votre application.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-143">For example, [logging](xref:fundamentals/logging) is available throughout your application.</span></span>

<span data-ttu-id="9dc5b-144">Pour plus d’informations, consultez [Injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-144">For more information, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware"></a><span data-ttu-id="9dc5b-145">Intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="9dc5b-145">Middleware</span></span>

<span data-ttu-id="9dc5b-146">Dans ASP.NET Core, vous composez votre pipeline de requêtes à l’aide d’un [intergiciel (middleware)](xref:fundamentals/middleware).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-146">In ASP.NET Core, you compose your request pipeline using [Middleware](xref:fundamentals/middleware).</span></span> <span data-ttu-id="9dc5b-147">Un intergiciel (middleware) ASP.NET Core suit une logique asynchrone sur `HttpContext`, puis appelle l’intergiciel suivant de la séquence ou met fin directement à la requête.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-147">ASP.NET Core middleware performs asynchronous logic on an `HttpContext` and then either invokes the next middleware in the sequence or terminates the request directly.</span></span> <span data-ttu-id="9dc5b-148">Un composant intergiciel (middleware) appelé « XYZ » est ajouté via l’appel de la méthode d’extension `UseXYZ` dans la méthode `Configure`.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-148">A middleware component called "XYZ" is added by invoking a `UseXYZ` extension method in the `Configure` method.</span></span>

<span data-ttu-id="9dc5b-149">ASP.NET Core est livré avec un vaste ensemble d’intergiciels (middleware) intégrés :</span><span class="sxs-lookup"><span data-stu-id="9dc5b-149">ASP.NET Core comes with a rich set of built-in middleware:</span></span>

* [<span data-ttu-id="9dc5b-150">Fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="9dc5b-150">Static files</span></span>](xref:fundamentals/static-files)

* [<span data-ttu-id="9dc5b-151">Routage</span><span class="sxs-lookup"><span data-stu-id="9dc5b-151">Routing</span></span>](xref:fundamentals/routing)

* [<span data-ttu-id="9dc5b-152">Authentification</span><span class="sxs-lookup"><span data-stu-id="9dc5b-152">Authentication</span></span>](xref:security/authentication/index)

<span data-ttu-id="9dc5b-153">Vous pouvez aussi bien utiliser un intergiciel (middleware) [OWIN](http://owin.org) avec ASP.NET Core qu’écrire le vôtre en le personnalisant.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-153">You can use any [OWIN](http://owin.org)-based middleware with ASP.NET Core, and you can write your own custom middleware.</span></span>

<span data-ttu-id="9dc5b-154">Pour plus d’informations, consultez [Intergiciel (middleware)](xref:fundamentals/middleware) et [OWIN (Open Web Interface pour .NET)](xref:fundamentals/owin).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-154">For more information, see [Middleware](xref:fundamentals/middleware) and [Open Web Interface for .NET (OWIN)](xref:fundamentals/owin).</span></span>

## <a name="servers"></a><span data-ttu-id="9dc5b-155">Serveurs</span><span class="sxs-lookup"><span data-stu-id="9dc5b-155">Servers</span></span>

<span data-ttu-id="9dc5b-156">Le modèle d’hébergement ASP.NET Core n’écoute pas directement les requêtes. Il compte plutôt sur une implémentation d’un serveur HTTP pour transférer la requête à l’application.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-156">The ASP.NET Core hosting model does not directly listen for requests; rather, it relies on an HTTP server implementation to forward the request to the application.</span></span> <span data-ttu-id="9dc5b-157">La requête transférée est incluse dans un wrapper sous forme d’ensemble d’objets de fonctionnalités auxquels vous pouvez accéder via des interfaces.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-157">The forwarded request is wrapped as a set of feature objects that you can access through interfaces.</span></span> <span data-ttu-id="9dc5b-158">L’application compose cet ensemble dans `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-158">The application composes this set into an `HttpContext`.</span></span> <span data-ttu-id="9dc5b-159">ASP.NET Core inclut un serveur web multiplateforme géré, appelé [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-159">ASP.NET Core includes a managed, cross-platform web server, called [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="9dc5b-160">Kestrel s’exécute généralement derrière un serveur web de production comme [IIS](https://www.iis.net/) ou [nginx](http://nginx.org).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-160">Kestrel is typically run behind a production web server like [IIS](https://www.iis.net/) or [nginx](http://nginx.org).</span></span>

<span data-ttu-id="9dc5b-161">Pour plus d’informations, consultez [Serveurs](xref:fundamentals/servers/index) et [Hébergement](xref:fundamentals/hosting).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-161">For more information, see [Servers](xref:fundamentals/servers/index) and [Hosting](xref:fundamentals/hosting).</span></span>

## <a name="content-root"></a><span data-ttu-id="9dc5b-162">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="9dc5b-162">Content root</span></span>

<span data-ttu-id="9dc5b-163">La racine de contenu est le chemin de base de tout contenu utilisé par l’application, par exemple les vues, les [pages Razor](xref:mvc/razor-pages/index) et les composants statiques.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-163">The content root is the base path to any content used by the app, such as views, [Razor Pages](xref:mvc/razor-pages/index), and static assets.</span></span> <span data-ttu-id="9dc5b-164">Par défaut, la racine de contenu est identique au chemin de base de l’application pour l’exécutable qui héberge l’application.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-164">By default, the content root is the same as application base path for the executable hosting the application.</span></span> <span data-ttu-id="9dc5b-165">Vous pouvez spécifier un autre emplacement de racine de contenu avec `WebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-165">An alternative location for content root is specified with `WebHostBuilder`.</span></span>

## <a name="web-root"></a><span data-ttu-id="9dc5b-166">Racine web</span><span class="sxs-lookup"><span data-stu-id="9dc5b-166">Web root</span></span>

<span data-ttu-id="9dc5b-167">La racine web d’une application correspond au répertoire de projet qui contient les ressources publiques, statiques, telles que les fichiers CSS, JavaScript et image.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-167">The web root of an application is the directory in the project containing public, static resources like CSS, JavaScript, and image files.</span></span> <span data-ttu-id="9dc5b-168">Par défaut, l’intergiciel (middleware) des fichiers statiques traite uniquement les fichiers provenant du répertoire racine web et de ses sous-répertoires.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-168">By default, the static files middleware will only serve files from the web root directory and its sub-directories.</span></span> <span data-ttu-id="9dc5b-169">Pour plus d’informations, consultez [Utilisation de fichiers statiques](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-169">See [working with static files](xref:fundamentals/static-files) for more info.</span></span> <span data-ttu-id="9dc5b-170">Par défaut, le chemin de la racine web est */wwwroot*, mais vous pouvez spécifier un autre emplacement à l’aide de `WebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-170">The web root path defaults to */wwwroot*, but you can specify a different location using the `WebHostBuilder`.</span></span>

## <a name="configuration"></a><span data-ttu-id="9dc5b-171">Configuration</span><span class="sxs-lookup"><span data-stu-id="9dc5b-171">Configuration</span></span>

<span data-ttu-id="9dc5b-172">ASP.NET Core utilise un nouveau modèle de configuration pour prendre en charge les paires nom-valeur simples.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-172">ASP.NET Core uses a new configuration model for handling simple name-value pairs.</span></span> <span data-ttu-id="9dc5b-173">Le nouveau modèle de configuration n’est pas basé sur `System.Configuration` ou *web.config*. Il provient en fait d’un ensemble ordonné de fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-173">The new configuration model is not based on `System.Configuration` or *web.config*; rather, it pulls from an ordered set of configuration providers.</span></span> <span data-ttu-id="9dc5b-174">Les fournisseurs de configuration intégrés prennent en charge une grande variété de formats de fichiers (XML, JSON, INI), ainsi que des variables d’environnement pour activer la configuration basée sur l’environnement.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-174">The built-in configuration providers support a variety of file formats (XML, JSON, INI) and environment variables to enable environment-based configuration.</span></span> <span data-ttu-id="9dc5b-175">Vous pouvez également écrire vos propres fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-175">You can also write your own custom configuration providers.</span></span>

<span data-ttu-id="9dc5b-176">Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-176">For more information, see [Configuration](xref:fundamentals/configuration).</span></span>

## <a name="environments"></a><span data-ttu-id="9dc5b-177">Environnements</span><span class="sxs-lookup"><span data-stu-id="9dc5b-177">Environments</span></span>

<span data-ttu-id="9dc5b-178">Les environnements, tels que « Développement » et « Production », sont une notion de premier plan dans ASP.NET Core. Vous pouvez les définir à l’aide de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-178">Environments, like "Development" and "Production", are a first-class notion in ASP.NET Core and can be set using environment variables.</span></span>

<span data-ttu-id="9dc5b-179">Pour plus d’informations, consultez [Utilisation de plusieurs environnements](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-179">For more information, see [Working with Multiple Environments](xref:fundamentals/environments).</span></span>

## <a name="net-core-vs-net-framework-runtime"></a><span data-ttu-id="9dc5b-180">Runtime .NET Core et runtime .NET Framework</span><span class="sxs-lookup"><span data-stu-id="9dc5b-180">.NET Core vs. .NET Framework runtime</span></span>

<span data-ttu-id="9dc5b-181">Une application ASP.NET Core peut cibler le runtime .NET Core ou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="9dc5b-181">An ASP.NET Core application can target the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="9dc5b-182">Pour plus d’informations, consultez [Choix entre le .NET Core et le .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="9dc5b-182">For more information, see [Choosing between .NET Core and .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="additional-information"></a><span data-ttu-id="9dc5b-183">Informations supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9dc5b-183">Additional information</span></span>

<span data-ttu-id="9dc5b-184">Consultez également les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="9dc5b-184">See also the following topics:</span></span>

- [<span data-ttu-id="9dc5b-185">Gestion des erreurs</span><span class="sxs-lookup"><span data-stu-id="9dc5b-185">Error Handling</span></span>](xref:fundamentals/error-handling)
- [<span data-ttu-id="9dc5b-186">Fournisseurs de fichiers</span><span class="sxs-lookup"><span data-stu-id="9dc5b-186">File Providers</span></span>](xref:fundamentals/file-providers)
- [<span data-ttu-id="9dc5b-187">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="9dc5b-187">Globalization and localization</span></span>](xref:fundamentals/localization)
- [<span data-ttu-id="9dc5b-188">Journalisation</span><span class="sxs-lookup"><span data-stu-id="9dc5b-188">Logging</span></span>](xref:fundamentals/logging)
- [<span data-ttu-id="9dc5b-189">Gestion de l’état de l’application</span><span class="sxs-lookup"><span data-stu-id="9dc5b-189">Managing Application State</span></span>](xref:fundamentals/app-state)

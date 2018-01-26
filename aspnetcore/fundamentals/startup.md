---
title: "Démarrage de l’application dans ASP.NET Core"
author: ardalis
description: "Découvrez comment la classe de démarrage dans ASP.NET Core configure les services et le pipeline de demande de l’application."
ms.author: tdykstra
manager: wpickett
ms.custom: mvc
ms.date: 12/08/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/startup
ms.openlocfilehash: 81d76c39b7890e2d4ab86252cb0a343e3bb7359a
ms.sourcegitcommit: 83b5a4715fd25e4eb6f7c8427c0ef03850a7fa07
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="application-startup-in-aspnet-core"></a>Démarrage de l’application dans ASP.NET Core

Par [Steve Smith](https://ardalis.com), [Tom Dykstra](https://github.com/tdykstra), et [Luke Latham](https://github.com/guardrex)

La `Startup` classe configure les services et le pipeline de demande de l’application.

## <a name="the-startup-class"></a>Classe de démarrage.

Utilisation des applications ASP.NET Core un `Startup` (classe), qui est nommé `Startup` par convention. La `Startup` classe :

* Peut éventuellement inclure un [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) méthode permettant de configurer les services de l’application.
* Doit inclure un [configurer](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) méthode pour créer le pipeline de traitement de demande de l’application.

`ConfigureServices`et `Configure` sont appelés par le runtime au démarrage de l’application :

[!code-csharp[Main](startup/snapshot_sample/Startup1.cs)]

Spécifiez le `Startup` classe avec le [WebHostBuilderExtensions](/dotnet/api/Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions) [UseStartup&lt;TStartup&gt; ](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup#Microsoft_AspNetCore_Hosting_WebHostBuilderExtensions_UseStartup__1_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) méthode :

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=10)]

Le `Startup` constructeur de classe accepte les dépendances définies par l’hôte. Une utilisation courante de [injection de dépendance](xref:fundamentals/dependency-injection) dans la `Startup` classe consiste à injecter :

* [IHostingEnvironment](/dotnet/api/Microsoft.AspNetCore.Hosting.IHostingEnvironment) pour configurer les services à l’environnement.
* [IConfiguration](/dotnet/api/microsoft.extensions.configuration.iconfiguration) pour configurer l’application lors du démarrage.

[!code-csharp[Main](startup/snapshot_sample/Startup2.cs)]

Une alternative à l’injection `IHostingEnvironment` consiste à utiliser une approche basée sur les conventions. L’application peut définir distinct `Startup` classes pour différents environnements (par exemple, `StartupDevelopment`), et la classe de démarrage approprié est sélectionnée lors de l’exécution. La priorité de la classe dont suffixe de nom correspond à l’environnement actuel. Si l’application est exécutée dans l’environnement de développement et comprend à la fois un `Startup` classe et un `StartupDevelopment` (classe), la `StartupDevelopment` classe est utilisée. Pour plus d’informations, consultez [Utilisation de plusieurs environnements](xref:fundamentals/environments#startup-conventions).

Pour en savoir plus sur `WebHostBuilder`, consultez la [hébergement](xref:fundamentals/hosting) rubrique. Pour plus d’informations sur la gestion des erreurs lors du démarrage, consultez [la gestion des exceptions de démarrage](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>La méthode ConfigureServices

Le [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) méthode est :

* Facultatif.
* Appelée par l’hôte web avant du `Configure` méthode permettant de configurer les services de l’application.
* Où [les options de configuration](xref:fundamentals/configuration/index) sont définis par convention.

Ajout de services au conteneur de service les rend disponibles au sein de l’application et dans le `Configure` (méthode). Les services ne sont pas résolus via [injection de dépendance](xref:fundamentals/dependency-injection) ou à partir de [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices).

L’hôte web peut configurer certains services avant `Startup` méthodes sont appelées. Détails sont disponibles dans le [hébergement](xref:fundamentals/hosting) rubrique. 

Pour les fonctionnalités qui nécessitent le programme d’installation substantiel, il y a `Add[Service]` méthodes d’extension sur [IServiceCollection](/dotnet/api/Microsoft.Extensions.DependencyInjection.IServiceCollection). Une application web typique inscrit des services pour Entity Framework, d’identité et MVC :

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=4,7,11&start=40&end=55)]

## <a name="services-available-in-startup"></a>Services disponibles dans le démarrage

L’hôte web fournit des services qui sont disponibles pour le `Startup` constructeur de classe. L’application ajoute des services supplémentaires via `ConfigureServices`. Les services de l’hôte et l’application sont ensuite disponibles dans `Configure` et dans l’ensemble de l’application.

## <a name="the-configure-method"></a>Méthode Configure

Le [configurer](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) méthode est utilisée pour spécifier la manière dont l’application répond aux demandes HTTP. Le pipeline de requête est configuré en ajoutant [intergiciel (middleware)](xref:fundamentals/middleware) composants à une [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) instance. `IApplicationBuilder`est disponible pour le `Configure` (méthode), mais il n’est pas enregistré dans le conteneur de service. Hébergement crée un `IApplicationBuilder` et le passe directement à `Configure` ([source de référence](https://github.com/aspnet/Hosting/blob/release/2.0.0/src/Microsoft.AspNetCore.Hosting/Internal/WebHost.cs#L179-L192)).

Le [les modèles ASP.NET Core](/dotnet/core/tools/dotnet-new) configurer le pipeline avec prise en charge pour une page d’exception de développeur, [BrowserLink](http://vswebessentials.com/features/browserlink), pages d’erreurs, les fichiers statiques et ASP.NET MVC :

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Startup.cs?range=28-48&highlight=5,6,10,13,15)]

Chaque `Use` méthode d’extension ajoute un composant d’intergiciel (middleware) au pipeline de demande. Par exemple, le `UseMvc` ajoute de la méthode d’extension la [intergiciel (middleware) routage](xref:fundamentals/routing) au pipeline de demande et configure [MVC](xref:mvc/overview) en tant que gestionnaire par défaut.

Les services supplémentaires, tels que `IHostingEnvironment` et `ILoggerFactory`, peuvent également être spécifiées dans la signature de méthode. Si spécifié, les services supplémentaires sont injectées si elles sont disponibles.

Pour plus d’informations sur l’utilisation de `IApplicationBuilder`, consultez [intergiciel (middleware)](xref:fundamentals/middleware).

## <a name="convenience-methods"></a>Méthodes d’usage

[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder.configureservices) et [configurer](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) méthodes pratiques peuvent être utilisés au lieu de spécifier un `Startup` classe. Appels multiples à `ConfigureServices` ajouter à un autre. Appels multiples à `Configure` utiliser le dernier appel de méthode.

[!code-csharp[Main](startup/snapshot_sample/Program.cs?highlight=18,22)]

## <a name="startup-filters"></a>Filtres de démarrage

Utilisez [IStartupFilter](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) pour configurer un intergiciel (middleware) au début ou à la fin de l’application [configurer](#the-configure-method) pipeline d’intergiciel (middleware). `IStartupFilter`est utile pour s’assurer qu’un intergiciel (middleware) s’exécute avant ou après l’intergiciel (middleware) ajoutés par des bibliothèques au début ou à la fin du pipeline de traitement de demande de l’application.

`IStartupFilter`implémente une méthode unique, [configurer](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter.configure), qui reçoit et renvoie un `Action<IApplicationBuilder>`. Un [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) définit une classe pour configurer le pipeline de demande d’une application. Pour plus d’informations, consultez [création d’un pipeline de l’intergiciel (middleware) avec IApplicationBuilder](xref:fundamentals/middleware#creating-a-middleware-pipeline-with-iapplicationbuilder).

Chaque `IStartupFilter` implémente un ou plusieurs middlewares dans le pipeline de demande. Les filtres sont appelés dans l’ordre qu’ils ont été ajoutés au conteneur de service. Les filtres peuvent ajouter intergiciel (middleware) avant ou après le transfert de contrôle pour le filtre suivant, par conséquent, ils ajoutent au début ou à la fin du pipeline d’application.

Le [exemple d’application](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/startup/sample/) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample)) montre comment inscrire un intergiciel (middleware) avec `IStartupFilter`. L’exemple d’application inclut un intergiciel (middleware) qui définit une valeur d’options à partir d’un paramètre de chaîne de requête :

[!code-csharp[Main](startup/sample/RequestSetOptionsMiddleware.cs?name=snippet1)]

Le `RequestSetOptionsMiddleware` est configuré dans le `RequestSetOptionsStartupFilter` classe :

[!code-csharp[Main](startup/sample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

Le `IStartupFilter` est enregistré dans le conteneur de service dans `ConfigureServices`:

[!code-csharp[Main](startup/sample/Startup.cs?name=snippet1&highlight=3)]

Lorsqu’un paramètre de chaîne de requête pour `option` est fourni, l’intergiciel (middleware) traite l’affectation de la valeur avant de l’intergiciel (middleware) MVC affiche la réponse :

![Fenêtre de navigateur affichant la page d’Index rendue. La valeur de l’Option est rendue telle que 'D’intergiciel (middleware)' en fonction de la demande de la page avec la valeur de l’option est définie sur « D’intergiciel (middleware) » et le paramètre de chaîne de requête.](startup/_static/index.png)

Intergiciel (middleware) l’ordre d’exécution est définie par l’ordre de `IStartupFilter` inscriptions :

* Plusieurs `IStartupFilter` implémentations peuvent interagir avec les mêmes objets. Si l’ordre est important, ordre de leur `IStartupFilter` service inscriptions à l’ordre de leur middlewares doit s’exécuter.
* Les bibliothèques peuvent ajouter des intergiciels (middleware) avec un ou plusieurs `IStartupFilter` implémentations qui s’exécutent avant ou après l’autre intergiciel (middleware) application inscrite avec `IStartupFilter`. Pour appeler un `IStartupFilter` intergiciel (middleware) avant un intergiciel (middleware) ajouté à une bibliothèque `IStartupFilter`, placez l’inscription du service avant de la bibliothèque est ajoutée au conteneur de service. Pour appeler par la suite, placez l’inscription du service après avoir ajouté la bibliothèque.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Hébergement d’applications WPF](xref:fundamentals/hosting)
* [Utilisation de plusieurs environnements](xref:fundamentals/environments)
* [Intergiciel (middleware)](xref:fundamentals/middleware)
* [Journalisation](xref:fundamentals/logging/index)
* [Configuration](xref:fundamentals/configuration/index)
* [Classe de StartupLoader : FindStartupType (méthode) (source de référence)](https://github.com/aspnet/Hosting/blob/rel/2.0.0/src/Microsoft.AspNetCore.Hosting/Internal/StartupLoader.cs#L66-L116)

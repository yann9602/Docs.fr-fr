---
title: "Démarrage de l’application dans ASP.NET Core"
author: ardalis
description: "Explique la classe de démarrage dans ASP.NET Core."
keywords: "ASP.NET Core, démarrage, méthode Configure, ConfigureServices (méthode)"
ms.author: tdykstra
manager: wpickett
ms.date: 02/29/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/startup
ms.openlocfilehash: 69af91de6d2c48af58bc10a32d8857af18a41b6a
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="application-startup-in-aspnet-core"></a>Démarrage de l’application dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/) et [Tom Dykstra](https://github.com/tdykstra/)

La `Startup` classe configure les services et le pipeline de demande de l’application. 

## <a name="the-startup-class"></a>Classe de démarrage.

Les applications ASP.NET Core nécessitent un `Startup` classe. Par convention, le `Startup` classe est nommée « Démarrage ». Vous spécifiez le nom de classe de démarrage dans le `Main` du programme [WebHostBuilderExtensions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderextensions) [ `UseStartup<TStartup>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderextensions#Microsoft_AspNetCore_Hosting_WebHostBuilderExtensions_UseStartup__1_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) (méthode). Consultez [hébergement](xref:fundamentals/hosting) pour en savoir plus sur `WebHostBuilder`, qui s’exécute avant `Startup`.

Vous pouvez définir distinct `Startup` classes pour les différents environnements et appropriées, un est sélectionné lors de l’exécution. Si vous spécifiez `startupAssembly` dans les [WebHost configuration](https://docs.microsoft.com/aspnet/core/fundamentals/hosting?tabs=aspnetcore2x#configuring-a-host) ou options d’hébergement charge cet assembly de démarrage et recherchez un `Startup` ou `Startup[Environment]` type. La classe dont suffixe de nom correspond à l’environnement actuel est prioritaires, par conséquent, si l’application est exécutée dans le *développement* environnement et comprend à la fois un `Startup` et un `StartupDevelopment` (classe), la `StartupDevelopment` classe sera utilisé. Consultez [FindStartupType](https://github.com/aspnet/Hosting/blob/rel/1.1.0/src/Microsoft.AspNetCore.Hosting/Internal/StartupLoader.cs) dans `StartupLoader` et [fonctionne avec plusieurs environnements](environments.md#startup-conventions).

Vous pouvez également définir un fixe `Startup` classe qui sera utilisé, quelle que soit l’environnement en appelant `UseStartup<TStartup>`. Il s'agit de l'approche recommandée.

Le `Startup` constructeur de classe peut accepter des dépendances qui sont fournies via [injection de dépendance](xref:fundamentals/dependency-injection). Une approche courante consiste à utiliser `IHostingEnvironment` configurer [configuration](xref:fundamentals/configuration) sources.

Le `Startup` doit inclure un `Configure` (méthode) et peut éventuellement inclure un `ConfigureServices` méthode, qui sont appelées lorsque l’application démarre. La classe peut également inclure [versions spécifiques à l’environnement de ces méthodes](xref:fundamentals/environments#startup-conventions). `ConfigureServices`, le cas échéant, est appelée avant `Configure`.

En savoir plus sur [la gestion des exceptions au cours du démarrage de l’application](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>La méthode ConfigureServices

Le [ConfigureServices](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.startupbase#Microsoft_AspNetCore_Hosting_StartupBase_ConfigureServices_Microsoft_Extensions_DependencyInjection_IServiceCollection_) méthode est facultative ; mais si utilisée, elle est appelée avant la `Configure` méthode par l’hôte web. L’hôte web peut configurer certains services avant ``Startup`` méthodes sont appelées (voir [hébergement](xref:fundamentals/hosting)). Par convention, [les options de Configuration](xref:fundamentals/configuration) sont définies dans cette méthode.

Pour les fonctionnalités qui nécessitent le programme d’installation importante sont `Add[Service]` méthodes d’extension sur [IServiceCollection](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.dependencyinjection.iservicecollection). Cet exemple à partir du modèle de site web par défaut configure l’application pour utiliser les services pour Entity Framework, d’identité et MVC :

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=4,7,11&start=40&end=55)]

Ajout de services au conteneur de services les rend disponibles dans votre application via [injection de dépendance](xref:fundamentals/dependency-injection).

## <a name="services-available-in-startup"></a>Services disponibles dans le démarrage

Injection de dépendances ASP.NET Core fournit des services lors du démarrage de l’application. Vous pouvez demander ces services en incluant l’interface appropriée en tant que paramètre sur votre `Startup` constructeur de la classe ou son `Configure` (méthode). Le `ConfigureServices` méthode n’accepte qu’un `IServiceCollection` paramètre (mais n’importe quel service inscrit peut en être récupérés à partir de cette collection, des paramètres supplémentaires ne sont pas nécessaires).

Voici quelques-unes des services en général demandées par `Startup` méthodes :

* Dans le constructeur : `IHostingEnvironment`,`ILogger<Startup>`
* Dans `ConfigureServices`:`IServiceCollection`
* Dans `Configure`: `IApplicationBuilder`, `IHostingEnvironment`,`ILoggerFactory`

Tous les services ajoutés par le ``WebHostBuilder`` ``ConfigureServices`` méthode peut être demandée par le ``Startup`` constructeur de classe ou son ``Configure`` (méthode). Utilisez `WebHostBuilder` pour fournir les services que vous avez besoin au cours de `Startup` méthodes.

## <a name="the-configure-method"></a>Méthode Configure

Le `Configure` méthode est utilisée pour spécifier la façon dont l’application ASP.NET doit répondre aux demandes HTTP. Le pipeline de requête est configuré en ajoutant [intergiciel (middleware)](middleware.md) composants à une `IApplicationBuilder` instance qui est fournie par injection de dépendances.

Dans l’exemple suivant à partir du modèle de site web par défaut, plusieurs méthodes d’extension sont utilisés pour configurer le pipeline avec prise en charge pour [BrowserLink](http://vswebessentials.com/features/browserlink), pages d’erreurs, les fichiers statiques, ASP.NET MVC et identité.

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

Chaque `Use` méthode d’extension ajoute un [intergiciel (middleware)](xref:fundamentals/middleware) composant au pipeline de demande. Par exemple, le `UseMvc` ajoute de la méthode d’extension du [routage](routing.md) intergiciel (middleware) au pipeline de demande et configure [MVC](xref:mvc/overview) en tant que gestionnaire par défaut.

Pour plus d’informations sur l’utilisation de `IApplicationBuilder`, consultez [intergiciel (middleware)](xref:fundamentals/middleware).

Services supplémentaires, telles que `IHostingEnvironment` et `ILoggerFactory` peut également être spécifié dans la signature de méthode, auquel cas ces services seront [injecté](dependency-injection.md) si elles sont disponibles. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Utilisation de plusieurs environnements](xref:fundamentals/environments)
* [Intergiciel (middleware)](xref:fundamentals/middleware)
* [Journalisation](xref:fundamentals/logging)
* [Configuration](xref:fundamentals/configuration)

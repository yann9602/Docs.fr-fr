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
# <a name="aspnet-core-fundamentals-overview"></a>Vue d’ensemble des notions de base d’ASP.NET Core

Une application ASP.NET Core est une application console qui crée un serveur web dans sa méthode `Main` :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs)]

La méthode `Main` appelle `WebHost.CreateDefaultBuilder`, qui suit le modèle du générateur pour créer un hôte d’application web. Le générateur a des méthodes qui définissent le serveur web (par exemple `UseKestrel`) et la classe de démarrage (`UseStartup`). Dans l’exemple précédent, un serveur web [Kestrel](xref:fundamentals/servers/kestrel) est alloué automatiquement. L’hôte web d’ASP.NET Core tente de s’exécuter sur IIS, s’il est disponible. D’autres serveurs web, tels que [HTTP.sys](xref:fundamentals/servers/httpsys), peuvent être utilisés via l’appel de la méthode d’extension appropriée. `UseStartup` est expliqué de manière plus détaillée dans la section suivante.

`IWebHostBuilder`, le type de retour de l’appel de `WebHost.CreateDefaultBuilder`, fournit de nombreuses méthodes facultatives. Certaines de ces méthodes incluent `UseHttpSys` pour l’hébergement de l’application dans HTTP.sys, et `UseContentRoot` pour la spécification du répertoire de contenu racine. Les méthodes `Build` et `Run` génèrent l’objet `IWebHost`, qui héberge l’application et démarre l’écoute des requêtes HTTP.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs)]

La méthode `Main` utilise `WebHostBuilder`, qui suit le modèle du générateur pour créer un hôte d’application web. Le générateur a des méthodes qui définissent le serveur web (par exemple `UseKestrel`) et la classe de démarrage (`UseStartup`). Dans l’exemple précédent, le serveur web [Kestrel](xref:fundamentals/servers/kestrel) est utilisé. D’autres serveurs web, tels que [WebListener](xref:fundamentals/servers/weblistener), peuvent être utilisés via l’appel de la méthode d’extension appropriée. `UseStartup` est expliqué de manière plus détaillée dans la section suivante.

`WebHostBuilder` fournit de nombreuses méthodes facultatives, notamment `UseIISIntegration` pour l’hébergement dans IIS et IIS Express, ainsi que `UseContentRoot` pour la spécification du répertoire de contenu racine. Les méthodes `Build` et `Run` génèrent l’objet `IWebHost`, qui héberge l’application et démarre l’écoute des requêtes HTTP.

---

## <a name="startup"></a>Démarrage

La méthode `UseStartup` sur `WebHostBuilder` spécifie la classe `Startup` pour votre application :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs?highlight=10&range=6-17)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs?highlight=7&range=6-17)]

---

La classe `Startup` est l’emplacement où vous définissez le pipeline de traitement des requêtes et où sont configurés les services nécessaires à l’application. La classe `Startup` doit être publique et contenir les méthodes suivantes :

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

* `ConfigureServices` définit les [services](#services) utilisés par votre application (par exemple ASP.NET Core MVC, Entity Framework Core, Identity, etc.).

* `Configure` définit les [intergiciels (middleware)](xref:fundamentals/middleware) du pipeline de requêtes.

Pour plus d’informations, consultez [Démarrage de l’application](xref:fundamentals/startup).

## <a name="services"></a>Services

Un service est un composant destiné à une consommation courante dans une application. Les services sont accessibles via l’[injection de dépendances](xref:fundamentals/dependency-injection). ASP.NET Core inclut un conteneur IoC (inversion de contrôle) natif qui prend en charge l’[injection de constructeurs](xref:mvc/controllers/dependency-injection#constructor-injection) par défaut. Vous pouvez remplacer le conteneur natif par le conteneur de votre choix. L’injection de dépendances n’offre pas seulement un couplage faible, elle permet également d’accéder aux services dans l’ensemble de votre application. Par exemple, la [journalisation](xref:fundamentals/logging) est disponible dans l’ensemble de votre application.

Pour plus d’informations, consultez [Injection de dépendances](xref:fundamentals/dependency-injection).

## <a name="middleware"></a>Intergiciel (middleware)

Dans ASP.NET Core, vous composez votre pipeline de requêtes à l’aide d’un [intergiciel (middleware)](xref:fundamentals/middleware). Un intergiciel (middleware) ASP.NET Core suit une logique asynchrone sur `HttpContext`, puis appelle l’intergiciel suivant de la séquence ou met fin directement à la requête. Un composant intergiciel (middleware) appelé « XYZ » est ajouté via l’appel de la méthode d’extension `UseXYZ` dans la méthode `Configure`.

ASP.NET Core est livré avec un vaste ensemble d’intergiciels (middleware) intégrés :

* [Fichiers statiques](xref:fundamentals/static-files)

* [Routage](xref:fundamentals/routing)

* [Authentification](xref:security/authentication/index)

Vous pouvez aussi bien utiliser un intergiciel (middleware) [OWIN](http://owin.org) avec ASP.NET Core qu’écrire le vôtre en le personnalisant.

Pour plus d’informations, consultez [Intergiciel (middleware)](xref:fundamentals/middleware) et [OWIN (Open Web Interface pour .NET)](xref:fundamentals/owin).

## <a name="servers"></a>Serveurs

Le modèle d’hébergement ASP.NET Core n’écoute pas directement les requêtes. Il compte plutôt sur une implémentation d’un serveur HTTP pour transférer la requête à l’application. La requête transférée est incluse dans un wrapper sous forme d’ensemble d’objets de fonctionnalités auxquels vous pouvez accéder via des interfaces. L’application compose cet ensemble dans `HttpContext`. ASP.NET Core inclut un serveur web multiplateforme géré, appelé [Kestrel](xref:fundamentals/servers/kestrel). Kestrel s’exécute généralement derrière un serveur web de production comme [IIS](https://www.iis.net/) ou [nginx](http://nginx.org).

Pour plus d’informations, consultez [Serveurs](xref:fundamentals/servers/index) et [Hébergement](xref:fundamentals/hosting).

## <a name="content-root"></a>Racine de contenu

La racine de contenu est le chemin de base de tout contenu utilisé par l’application, par exemple les vues, les [pages Razor](xref:mvc/razor-pages/index) et les composants statiques. Par défaut, la racine de contenu est identique au chemin de base de l’application pour l’exécutable qui héberge l’application. Vous pouvez spécifier un autre emplacement de racine de contenu avec `WebHostBuilder`.

## <a name="web-root"></a>Racine web

La racine web d’une application correspond au répertoire de projet qui contient les ressources publiques, statiques, telles que les fichiers CSS, JavaScript et image. Par défaut, l’intergiciel (middleware) des fichiers statiques traite uniquement les fichiers provenant du répertoire racine web et de ses sous-répertoires. Pour plus d’informations, consultez [Utilisation de fichiers statiques](xref:fundamentals/static-files). Par défaut, le chemin de la racine web est */wwwroot*, mais vous pouvez spécifier un autre emplacement à l’aide de `WebHostBuilder`.

## <a name="configuration"></a>Configuration

ASP.NET Core utilise un nouveau modèle de configuration pour prendre en charge les paires nom-valeur simples. Le nouveau modèle de configuration n’est pas basé sur `System.Configuration` ou *web.config*. Il provient en fait d’un ensemble ordonné de fournisseurs de configuration. Les fournisseurs de configuration intégrés prennent en charge une grande variété de formats de fichiers (XML, JSON, INI), ainsi que des variables d’environnement pour activer la configuration basée sur l’environnement. Vous pouvez également écrire vos propres fournisseurs de configuration.

Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration).

## <a name="environments"></a>Environnements

Les environnements, tels que « Développement » et « Production », sont une notion de premier plan dans ASP.NET Core. Vous pouvez les définir à l’aide de variables d’environnement.

Pour plus d’informations, consultez [Utilisation de plusieurs environnements](xref:fundamentals/environments).

## <a name="net-core-vs-net-framework-runtime"></a>Runtime .NET Core et runtime .NET Framework

Une application ASP.NET Core peut cibler le runtime .NET Core ou .NET Framework. Pour plus d’informations, consultez [Choix entre le .NET Core et le .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).

## <a name="additional-information"></a>Informations supplémentaires

Consultez également les rubriques suivantes :

- [Gestion des erreurs](xref:fundamentals/error-handling)
- [Fournisseurs de fichiers](xref:fundamentals/file-providers)
- [Globalisation et localisation](xref:fundamentals/localization)
- [Journalisation](xref:fundamentals/logging)
- [Gestion de l’état de l’application](xref:fundamentals/app-state)

---
title: "Notions de base d’ASP.NET Core"
author: rick-anderson
description: "Découvrez les concepts de base permettant de créer des applications ASP.NET Core."
manager: wpickett
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 09/30/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: fundamentals/index
ms.openlocfilehash: 946ccc80915c5de60976a98cbbb253cb8dfacaca
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="aspnet-core-fundamentals"></a>Notions de base d’ASP.NET Core

Une application ASP.NET Core est une application console qui crée un serveur web dans sa méthode `Main` :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs)]

La méthode `Main` appelle `WebHost.CreateDefaultBuilder`, qui suit le modèle du générateur pour créer un hôte d’application web. Le générateur a des méthodes qui définissent le serveur web (par exemple `UseKestrel`) et la classe de démarrage (`UseStartup`). Dans l’exemple précédent, le serveur web [Kestrel](xref:fundamentals/servers/kestrel) est alloué automatiquement. L’hôte web d’ASP.NET Core tente de s’exécuter sur IIS, s’il est disponible. D’autres serveurs web, tels que [HTTP.sys](xref:fundamentals/servers/httpsys), peuvent être utilisés via l’appel de la méthode d’extension appropriée. `UseStartup` est expliqué de manière plus détaillée dans la section suivante.

`IWebHostBuilder`, le type de retour de l’appel de `WebHost.CreateDefaultBuilder`, fournit de nombreuses méthodes facultatives. Certaines de ces méthodes incluent `UseHttpSys` pour l’hébergement de l’application dans HTTP.sys et `UseContentRoot` pour la spécification du répertoire de contenu racine. Les méthodes `Build` et `Run` génèrent l’objet `IWebHost`, qui héberge l’application et démarre l’écoute des requêtes HTTP.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs)]

La méthode `Main` utilise `WebHostBuilder`, qui suit le modèle du générateur pour créer un hôte d’application web. Le générateur a des méthodes qui définissent le serveur web (par exemple `UseKestrel`) et la classe de démarrage (`UseStartup`). Dans l’exemple précédent, le serveur web [Kestrel](xref:fundamentals/servers/kestrel) est utilisé. D’autres serveurs web, tels que [WebListener](xref:fundamentals/servers/weblistener), peuvent être utilisés via l’appel de la méthode d’extension appropriée. `UseStartup` est expliqué de manière plus détaillée dans la section suivante.

`WebHostBuilder` fournit de nombreuses méthodes facultatives, notamment `UseIISIntegration` pour l’hébergement dans IIS et IIS Express et `UseContentRoot` pour la spécification du répertoire de contenu racine. Les méthodes `Build` et `Run` génèrent l’objet `IWebHost`, qui héberge l’application et démarre l’écoute des requêtes HTTP.

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
    // This method gets called by the runtime. Use this method
    // to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
    }

    // This method gets called by the runtime. Use this method
    // to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app)
    {
    }
}
```

`ConfigureServices` définit les [services](#dependency-injection-services) utilisés par votre application (par exemple, ASP.NET Core MVC, Entity Framework Core et Identity). `Configure` définit les [intergiciels (middleware)](xref:fundamentals/middleware) du pipeline de requêtes.

Pour plus d’informations, consultez [Démarrage de l’application](xref:fundamentals/startup).

## <a name="content-root"></a>Racine de contenu

La racine de contenu est le chemin de base de tout contenu utilisé par l’application, par exemple les vues, les [pages Razor](xref:mvc/razor-pages/index) et les composants statiques. Par défaut, la racine de contenu est identique au chemin de base de l’application pour l’exécutable qui héberge l’application.

## <a name="web-root"></a>Racine web

La racine web d’une application correspond au répertoire de projet qui contient les ressources publiques, statiques, telles que les fichiers CSS, JavaScript et image.

## <a name="dependency-injection-services"></a>Injection de dépendances (Services)

Un service est un composant destiné à une consommation courante dans une application. Les services sont accessibles via l’[injection de dépendances](xref:fundamentals/dependency-injection). ASP.NET Core inclut un conteneur IoC (**I**nversion **o**f **C**ontrol) natif qui prend en charge l’[injection de constructeurs](xref:mvc/controllers/dependency-injection#constructor-injection) par défaut. Vous pouvez remplacer le conteneur natif par défaut si vous le souhaitez. L’injection de dépendances n’offre pas seulement un couplage faible, elle permet également d’accéder aux services dans l’ensemble de votre application (par exemple, la [journalisation](xref:fundamentals/logging/index)).

Pour plus d’informations, consultez [Injection de dépendances](xref:fundamentals/dependency-injection).

## <a name="middleware"></a>Intergiciel (middleware)

Dans ASP.NET Core, vous composez votre pipeline de requêtes à l’aide d’un [intergiciel (middleware)](xref:fundamentals/middleware). Un intergiciel (middleware) ASP.NET Core suit une logique asynchrone sur `HttpContext`, puis appelle l’intergiciel suivant de la séquence ou met fin directement à la requête. Un composant intergiciel (middleware) appelé « XYZ » est ajouté via l’appel de la méthode d’extension `UseXYZ` dans la méthode `Configure`.

ASP.NET Core est livré avec un vaste ensemble d’intergiciels (middleware) intégrés :

* [Fichiers statiques](xref:fundamentals/static-files)
* [Routage](xref:fundamentals/routing)
* [Authentification](xref:security/authentication/index)
* [Intergiciel (middleware) de compression des réponses](xref:performance/response-compression)
* [Intergiciel de réécriture d’URL](xref:fundamentals/url-rewriting)

L’intergiciel (middleware) [OWIN](http://owin.org) est disponible pour les applications ASP.NET Core. Vous pouvez aussi écrire votre propre intergiciel personnalisé.

Pour plus d’informations, consultez [Intergiciel (middleware)](xref:fundamentals/middleware) et [OWIN (Open Web Interface pour .NET)](xref:fundamentals/owin).

## <a name="environments"></a>Environnements

Les environnements, tels que « Développement » et « Production », sont une notion de premier plan dans ASP.NET Core. Vous pouvez les définir à l’aide de variables d’environnement.

Pour plus d’informations, consultez [Utilisation de plusieurs environnements](xref:fundamentals/environments).

## <a name="configuration"></a>Configuration

ASP.NET Core utilise un modèle de configuration basé sur les paires nom-valeur. Le modèle de configuration n’est pas basé sur `System.Configuration` ou *web.config*. La configuration obtient les paramètres d’une sélection de fournisseurs de configuration. Les fournisseurs de configuration intégrés prennent en charge une grande variété de formats de fichiers (XML, JSON, INI), ainsi que des variables d’environnement pour activer la configuration basée sur l’environnement. Vous pouvez également écrire vos propres fournisseurs de configuration.

Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).

## <a name="logging"></a>Journalisation

ASP.NET Core prend en charge une API de journalisation qui fonctionne avec divers fournisseurs de journalisation. Les fournisseurs intégrés prennent en charge l’envoi de journaux à une ou plusieurs destinations. Des frameworks de journalisation tiers peuvent être utilisés.

[Journalisation](xref:fundamentals/logging/index)

## <a name="error-handling"></a>Gestion des erreurs

ASP.NET Core offre des fonctionnalités intégrées pour la gestion des erreurs dans les applications, notamment une page d’exceptions du développeur, des pages d’erreurs personnalisées, des pages de code d’état statique et la gestion des exceptions de démarrage.

Pour plus d’informations, consultez [Gestion des erreurs](xref:fundamentals/error-handling).

## <a name="routing"></a>Routage

ASP.NET Core offre des fonctionnalités pour router les requêtes d’application vers les gestionnaires de routage.

Pour plus d’informations, consultez [Routage](xref:fundamentals/routing).

## <a name="file-providers"></a>Fournisseurs de fichiers

ASP.NET Core fournit un accès au système de fichiers en utilisant des fournisseurs de fichiers qui offrent une interface commune permettant de travailler avec des fichiers sur plusieurs plateformes.

Pour plus d’informations, consultez [Fournisseurs de fichiers](xref:fundamentals/file-providers).

## <a name="static-files"></a>Fichiers statiques

L’intergiciel (middleware) de fichiers statiques prend en charge des fichiers statiques, tels que HTML, CSS, image et JavaScript.

Pour plus d’informations, consultez [Utilisation des fichiers statiques](xref:fundamentals/static-files).

## <a name="hosting"></a>Hébergement

Les applications ASP.NET Core configurent et lancent un *hôte*, qui est responsable de la gestion du démarrage et de la durée de vie des applications.

Pour plus d’informations, consultez [Hébergement](xref:fundamentals/hosting).

## <a name="session-and-application-state"></a>État de session et d’application

L’état de session est une fonctionnalité ASP.NET Core que vous pouvez utiliser pour enregistrer et stocker des données utilisateur pendant que l’utilisateur navigue dans votre application web.

Pour plus d’informations, consultez [État de session et d’application](xref:fundamentals/app-state).

## <a name="servers"></a>Serveurs

Le modèle d’hébergement ASP.NET Core n’écoute pas directement les requêtes. Le modèle d’hébergement s’appuie sur une implémentation de serveur HTTP pour transférer la requête à l’application. La requête transférée est enveloppée (wrap) dans un ensemble d’objets de fonctionnalités auxquels vous pouvez accéder via des interfaces. ASP.NET Core inclut un serveur web multiplateforme géré, appelé [Kestrel](xref:fundamentals/servers/kestrel). Kestrel s’exécute souvent derrière un serveur web de production comme [IIS](https://www.iis.net/) ou [Nginx](http://nginx.org). Kestrel peut être exécuté comme serveur edge.

Pour plus d’informations, consultez [Serveurs](xref:fundamentals/servers/index) et les rubriques suivantes :

* [Kestrel](xref:fundamentals/servers/kestrel)
* [Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module)
* [HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement [WebListener](xref:fundamentals/servers/weblistener))

## <a name="globalization-and-localization"></a>Globalisation et localisation

La création d’un site web multilingue avec ASP.NET Core vous permet d’atteindre un plus large public. ASP.NET Core offre des services et des intergiciels (middleware) de traduction dans différentes langues et cultures.

Pour plus d’informations, consultez [Internationalisation et traduction](xref:fundamentals/localization).

## <a name="request-features"></a>Fonctionnalités de requête

Les détails d’implémentation d’un serveur web relatifs aux requêtes HTTP et aux réponses sont définis dans les interfaces. Ces interfaces sont utilisées par les implémentations de serveur et les intergiciels (middleware) pour créer et modifier le pipeline d’hébergement de l’application.

Pour plus d’informations, consultez [Fonctionnalités de requête](xref:fundamentals/request-features).

## <a name="open-web-interface-for-net-owin"></a>OWIN (Open Web Interface pour .NET)

ASP.NET Core prend en charge l’interface Open Web pour .NET (OWIN). OWIN permet que les applications web soient dissociées des serveurs web.

Pour plus d’informations, consultez [Interface Open Web pour .NET (OWIN)](xref:fundamentals/owin).

## <a name="websockets"></a>WebSockets

[WebSocket](https://wikipedia.org/wiki/WebSocket) est un protocole qui autorise des canaux de communication persistants bidirectionnels sur les connexions TCP. Il est utilisé pour des applications comme le chat, les transactions boursières, les jeux et partout où vous voulez des fonctionnalités en temps réel dans une application web. ASP.NET Core prend en charge les fonctionnalités de socket web.

Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).

## <a name="microsoftaspnetcoreall-metapackage"></a>Métapackage Microsoft.AspNetCore.All

Le métapackage [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) pour ASP.NET Core comprend :

* Tous les packages pris en charge par l’équipe ASP.NET Core.
* Tous les packages pris en charge par Entity Framework Core. 
* Les dépendances internes et tierces utilisées par ASP.NET Core et Entity Framework Core.

Pour plus d’informations, consultez [Métapackage Microsoft.AspNetCore.All](xref:fundamentals/metapackage).

## <a name="net-core-vs-net-framework-runtime"></a>Runtime .NET Core et runtime .NET Framework

Une application ASP.NET Core peut cibler le runtime .NET Core ou .NET Framework.

Pour plus d’informations, consultez [Choix entre .NET Core et .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="choose-between-aspnet-core-and-aspnet"></a>Choisir entre ASP.NET Core et ASP.NET

Pour plus d’informations sur le choix entre ASP.NET Core et ASP.NET, consultez [Choisir entre ASP.NET Core et ASP.NET](xref:fundamentals/choose-between-aspnet-and-aspnetcore).

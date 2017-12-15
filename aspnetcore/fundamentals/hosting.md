---
title: "Hébergement dans ASP.NET Core"
author: guardrex
description: "En savoir plus sur l’hôte web dans ASP.NET Core, qui est responsable de la gestion de démarrage et la durée de vie des applications."
keywords: "ASP.NET Core, web hôte, IWebHost, WebHostBuilder, IHostingEnvironment, IApplicationLifetime"
ms.author: riande
manager: wpickett
ms.date: 09/21/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.openlocfilehash: dfec2a67112d40b528b97c847da3dda8ef1e63bd
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="hosting-in-aspnet-core"></a>Hébergement dans ASP.NET Core

Par [Luke Latham](https://github.com/guardrex)

Les applications ASP.NET Core configurent et lancent un *hôte*, qui est responsable de la gestion du démarrage et de la durée de vie des applications. Au minimum, l’hôte configure un serveur et un pipeline de traitement de la requête.

## <a name="setting-up-a-host"></a>Configuration d’un ordinateur hôte

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Créer un hôte à l’aide d’une instance de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder). Cette opération est généralement effectuée dans le point d’entrée de votre application, le `Main` (méthode). Dans les modèles de projet, `Main` se trouve dans *Program.cs*. Par défaut *Program.cs* appelle [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) pour lancer l’installation d’un ordinateur hôte :

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main)]

`CreateDefaultBuilder`effectue les tâches suivantes :

* Configure [Kestrel](servers/kestrel.md) que le serveur web. Pour les options par défaut Kestrel, consultez [le Kestrel options section d’implémentation du serveur web Kestrel ASP.NET Core](xref:fundamentals/servers/kestrel#kestrel-options).
* Affecte à la racine du contenu [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).
* Configuration facultative de charge à partir de :
  * *appSettings.JSON*.
  * *appSettings. {Environnement} .json*.
  * [Les secrets utilisateur](xref:security/app-secrets) lorsque l’application s’exécute le `Development` environnement.
  * Variables d'environnement.
  * Arguments de ligne de commande.
* Configure [journalisation](xref:fundamentals/logging/index) pour la sortie de console et de débogage avec [filtrage de journal](xref:fundamentals/logging/index#log-filtering) les règles spécifiées dans une section de configuration de journalisation d’un *appsettings.json* ou *appsettings. {Environnement} .json* fichier.
* En cas de retard IIS, Active [intégration des services Internet](xref:publishing/iis) en configurant le chemin d’accès de base et le port du serveur doit écouter lors de l’utilisation du [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module). Le module crée un proxy inverse entre IIS et Kestrel. Configure également l’application de [capturer les erreurs de démarrage](#capture-startup-errors). Pour les options par défaut d’IIS, consultez [IIS options de section de l’hôte ASP.NET Core sur Windows avec IIS](xref:publishing/iis#iis-options).

Le *racine du contenu* détermine où l’hôte recherche les fichiers de contenu, tels que les fichiers de vue MVC. La racine de contenu par défaut est [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory). Cela entraîne l’utilisation de dossier racine du projet web en tant que la racine de contenu lors de l’application est lancée à partir du dossier racine (par exemple, l’appel [dotnet exécuter](/dotnet/core/tools/dotnet-run) à partir du dossier du projet). Ceci est la valeur par défaut utilisée dans [Visual Studio](https://www.visualstudio.com/) et [dotnet nouveaux modèles](/dotnet/core/tools/dotnet-new).

Consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index) pour plus d’informations sur la configuration de l’application.

> [!NOTE]
> Comme alternative à l’aide de la méthode statique `CreateDefaultBuilder` méthode, la création d’un hôte à partir de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) est une approche pris en charge avec ASP.NET Core 2.x. Pour plus d’informations, consultez l’onglet ASP.NET Core 1.x.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Créer un hôte à l’aide d’une instance de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder). Cette opération est généralement effectuée dans le point d’entrée de votre application, le `Main` (méthode). Dans les modèles de projet, `Main` se trouve dans *Program.cs*. Les éléments suivants *Program.cs* montre comment utiliser `WebHostBuilder` pour générer l’hôte :

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs)]

`WebHostBuilder`nécessite un [serveur qui implémente IServer](servers/index.md). Serveurs intégrés sont [Kestrel](servers/kestrel.md) et [HTTP.sys](servers/httpsys.md) (avant la version d’ASP.NET 2.0 de noyaux, HTTP.sys a été appelé [WebListener](xref:fundamentals/servers/weblistener)). Dans cet exemple, le [méthode d’extension UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) Spécifie le serveur Kestrel.

Le *racine du contenu* détermine où l’hôte recherche les fichiers de contenu, tels que les fichiers de vue MVC. La racine de contenu par défaut fourni à `UseContentRoot` est [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1). Cela entraîne l’utilisation de dossier racine du projet web en tant que la racine de contenu lors de l’application est lancée à partir du dossier racine (par exemple, l’appel [dotnet exécuter](/dotnet/core/tools/dotnet-run) à partir du dossier du projet). Ceci est la valeur par défaut utilisée dans [Visual Studio](https://www.visualstudio.com/) et [dotnet nouveaux modèles](/dotnet/core/tools/dotnet-new).

Pour utiliser IIS comme un proxy inverse, appelez [UseIISIntegration](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) dans le cadre de la création de l’ordinateur hôte. `UseIISIntegration`ne configure pas un *server*, comme [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) est. `UseIISIntegration`Configure le chemin d’accès de base et le port que le serveur doit écouter lors de l’utilisation du [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) pour créer un proxy inverse entre Kestrel et IIS. Pour utiliser IIS avec ASP.NET Core, vous devez spécifier les deux `UseKestrel` et `UseIISIntegration`. `UseIISIntegration`Active uniquement lors de l’exécution derrière IIS ou IIS Express. Pour plus d’informations, consultez [Introduction à ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) et [référence de configuration ASP.NET Core Module](xref:hosting/aspnet-core-module).

Une implémentation minimale qui configure un ordinateur hôte (et une application ASP.NET Core) consiste à spécifier un serveur et la configuration du pipeline de demande de l’application :

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

Lorsque vous configurez un ordinateur hôte, vous pouvez fournir [configurer](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) et [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) méthodes. Si vous spécifiez un `Startup` (classe), il doit définir un `Configure` (méthode). Pour plus d’informations, consultez [démarrage de l’Application dans ASP.NET Core](startup.md). Appels multiples à `ConfigureServices` ajouter à un autre. Appels multiples à `Configure` ou `UseStartup` sur la `WebHostBuilder` remplacer les paramètres précédents.

## <a name="host-configuration-values"></a>Valeurs de configuration d’hôte

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) fournit des méthodes pour la définition de la plupart des valeurs de configuration disponibles pour l’hôte, qui peut également être définie directement avec [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) et la clé associée. Lors de la définition d’une valeur avec `UseSetting`, la valeur est définie sous forme de chaîne (entre guillemets) quel que soit le type.

### <a name="capture-startup-errors"></a>Capturer les erreurs de démarrage

Ce paramètre contrôle la capture des erreurs de démarrage.

**Clé**: captureStartupErrors  
**Type**: *bool* (`true` ou `1`)  
**Par défaut**: valeur par défaut est `false` , sauf si l’application s’exécute avec Kestrel derrière IIS, où la valeur par défaut est `true`.  
**À l’aide de**:`CaptureStartupErrors`

Lorsque `false`, erreurs de résultat de démarrage de l’ordinateur hôte en cours de fermeture. Lorsque `true`, capture des exceptions lors du démarrage de l’ordinateur hôte et tente de démarrer le serveur.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .CaptureStartupErrors(true)
    ...
```

---

### <a name="content-root"></a>Racine du contenu

Ce paramètre détermine où ASP.NET Core commence à rechercher les fichiers de contenu, tels que les vues MVC. 

**Clé**: contentRoot  
**Type**: *chaîne*  
**Par défaut**: le dossier où réside l’application par défaut.  
**À l’aide de**:`UseContentRoot`

La racine du contenu est également utilisée comme le chemin d’accès de base pour le [paramètre de la racine Web](#web-root). Si le chemin d’accès n’existe pas, l’hôte ne peut pas démarrer.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\mywebsite")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
    ...
```

---

### <a name="detailed-errors"></a>Erreurs détaillées

Détermine si les détails les erreurs doivent être capturés.

**Clé**: detailedErrors  
**Type**: *bool* (`true` ou `1`)  
**Par défaut**: false  
**À l’aide de**:`UseSetting`

Lorsque activé (ou lorsque la <a href="#environment">environnement</a> a la valeur `Development`), l’application capture les exceptions détaillées.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

---

### <a name="environment"></a>Environnement

Définit l’environnement de l’application.

**Clé**: environnement  
**Type**: *chaîne*  
**Par défaut**: Production  
**À l’aide de**:`UseEnvironment`

Vous pouvez définir le *environnement* n’importe quelle valeur. Les valeurs définies par l’infrastructure sont `Development`, `Staging`, et `Production`. Les valeurs ne sont pas respecter la casse. Par défaut, le *environnement* est lu depuis le `ASPNETCORE_ENVIRONMENT` variable d’environnement. Lorsque vous utilisez [Visual Studio](https://www.visualstudio.com/), variables d’environnement peuvent être définies dans le *launchSettings.json* fichier. Pour plus d’informations, consultez [Utilisation de plusieurs environnements](xref:fundamentals/environments).

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseEnvironment("Development")
    ...
```

---

### <a name="hosting-startup-assemblies"></a>Hébergement des assemblys de démarrage

Définit les assemblys de démarrage de l’application hôte.

**Clé**: hostingStartupAssemblies  
**Type**: *chaîne*  
**Par défaut**: une chaîne vide  
**À l’aide de**:`UseSetting`

Une chaîne délimitée par des points-virgules d’héberger des assemblys de démarrage à charger au démarrage. Cette fonctionnalité est une nouveauté dans ASP.NET 2.0 de base.

Bien que la valeur de configuration par défaut est une chaîne vide, les assemblys de démarrage hébergement toujours incluent assembly de l’application. Lorsque vous fournissez des assemblys de démarrage hébergement, elles sont ajoutées à l’assembly de l’application pour le chargement lorsque l’application génère ses services courants lors du démarrage.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Cette fonctionnalité n’est pas disponible dans ASP.NET Core 1.x.

---

### <a name="prefer-hosting-urls"></a>Préférez l’hébergement d’URL

Indique si l’ordinateur hôte doit écouter les URL configurées avec le `WebHostBuilder` au lieu de ceux configurés avec la `IServer` implémentation.

**Clé**: preferHostingUrls  
**Type**: *bool* (`true` ou `1`)  
**Par défaut**: true  
**À l’aide de**:`PreferHostingUrls`

Cette fonctionnalité est une nouveauté dans ASP.NET 2.0 de base.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Cette fonctionnalité n’est pas disponible dans ASP.NET Core 1.x.

---

### <a name="prevent-hosting-startup"></a>Empêcher le démarrage d’hébergement

Empêche le chargement automatique des assemblys de démarrage, y compris les assemblys de démarrage configurés par l’assembly de l’application d’hébergement d’hébergement. Consultez [ajouter des fonctionnalités de l’application à partir d’un assembly externe à l’aide de IHostingStartup](xref:hosting/ihostingstartup) pour plus d’informations.

**Clé**: preventHostingStartup  
**Type**: *bool* (`true` ou `1`)  
**Par défaut**: false  
**À l’aide de**:`UseSetting`

Cette fonctionnalité est une nouveauté dans ASP.NET 2.0 de base.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Cette fonctionnalité n’est pas disponible dans ASP.NET Core 1.x.

---

### <a name="server-urls"></a>URL du serveur

Indique les adresses IP ou les adresses d’hôte avec les ports et protocoles que le serveur doit écouter les demandes.

**Clé**: URL  
**Type**: *chaîne*  
**Par défaut**: http://localhost : 5000  
**À l’aide de**:`UseUrls`

La valeur séparées par des points-virgules ( ;) des préfixes de la liste des URL à laquelle le serveur doit répondre. Par exemple, `http://localhost:123`. Utilisez «\*» pour indiquer que le serveur doit écouter les demandes sur des adresses IP ou le nom d’hôte en utilisant le port spécifié et le protocole (par exemple, `http://*:5000`). Le protocole (`http://` ou `https://`) doivent être incluses avec chaque URL. Formats pris en charge varient entre les serveurs.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

Kestrel a sa propre API de configuration du point de terminaison. Pour plus d’informations, consultez [Implémentation du serveur web Kestrel dans ASP.NET Core](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

---

### <a name="shutdown-timeout"></a>Délai d’arrêt

Spécifie la quantité de temps à attendre avant l’arrêt hôte web.

**Clé**: shutdownTimeoutSeconds  
**Type**: *int*  
**Par défaut**: 5  
**À l’aide de**:`UseShutdownTimeout`

Bien que la clé accepte un *int* avec `UseSetting` (par exemple, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), la `UseShutdownTimeout` méthode d’extension prend un `TimeSpan`. Cette fonctionnalité est une nouveauté dans ASP.NET 2.0 de base.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Cette fonctionnalité n’est pas disponible dans ASP.NET Core 1.x.

---

### <a name="startup-assembly"></a>Assembly de démarrage

Détermine l’assembly à rechercher la `Startup` classe.

**Clé**: startupAssembly  
**Type**: *chaîne*  
**Par défaut**: assembly de l’application  
**À l’aide de**:`UseStartup`

Vous pouvez référencer l’assembly par nom (`string`) ou de type (`TStartup`). Si plusieurs `UseStartup` méthodes sont appelées, le dernier est prioritaire.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

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

### <a name="web-root"></a>Racine du site Web

Définit le chemin d’accès relatif aux ressources statique de l’application.

**Clé**: webroot  
**Type**: *chaîne*  
**Par défaut**: si non spécifié, la valeur par défaut est "(Content Root)/wwwroot », si le chemin d’accès existe. Si le chemin d’accès n’existe pas, un fournisseur de fichiers de l’absence d’opération est utilisé.  
**À l’aide de**:`UseWebRoot`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseWebRoot("public")
    ...
```

---

## <a name="overriding-configuration"></a>Substitution de configuration

Utilisez [Configuration](xref:fundamentals/configuration/index) pour configurer l’hôte. Dans l’exemple suivant, configuration de l’hôte est éventuellement spécifiée dans un *hosting.json* fichier. Toute configuration chargée à partir de la *hosting.json* fichier peut être remplacé par des arguments de ligne de commande. La configuration intégrée (dans `config`) est utilisé pour configurer l’ordinateur hôte avec `UseConfiguration`.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

*Hosting.JSON*:

```json
{
    urls: "http://*:5005"
}
```

Substitution de la configuration fournie par `UseUrls` avec *hosting.json* config du premier argument de ligne de commande config deuxième :

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

*Hosting.JSON*:

```json
{
    urls: "http://*:5005"
}
```

Substitution de la configuration fournie par `UseUrls` avec *hosting.json* config du premier argument de ligne de commande config deuxième :

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
> Le `UseConfiguration` méthode d’extension n’est pas actuellement capable d’analyser une section de configuration retournée par `GetSection` (par exemple, `.UseConfiguration(Configuration.GetSection("section"))`. Le `GetSection` méthode filtre les clés de configuration à la section demandée, mais laisse le nom de la section sur les clés (par exemple, `section:urls`, `section:environment`). Le `UseConfiguration` méthode attend les clés pour faire correspondre le `WebHostBuilder` clés (par exemple, `urls`, `environment`). La présence d’un nom de la section sur les clés empêche des valeurs de la section à partir de la configuration de l’hôte. Ce problème sera résolu dans une prochaine mise en production. Pour plus d’informations et des solutions de contournement, consultez [en passant de la section de configuration dans WebHostBuilder.UseConfiguration utilise des clés complètes](https://github.com/aspnet/Hosting/issues/839).

Pour spécifier l’hôte s’exécutent sur une URL particulière, vous pouvez transmettre la valeur souhaitée à partir d’une invite de commandes lors de l’exécution `dotnet run`. L’argument de ligne de commande remplace le `urls` valeur à partir de la *hosting.json* fichier et que le serveur écoute sur le port 8080 :

```console
dotnet run --urls "http://*:8080"
```

## <a name="ordering-importance"></a>Ordre d’importance

Parmi les `WebHostBuilder` paramètres sont tout d’abord lus à partir de variables d’environnement, si définie. Ces variables d’environnement utilisent le format `ASPNETCORE_{configurationKey}`. Pour définir les URL que le serveur écoute sur par défaut, vous définissez `ASPNETCORE_URLS`.

Vous pouvez remplacer ces valeurs de variable d’environnement en spécifiant la configuration (à l’aide de `UseConfiguration`) ou en définissant la valeur de manière explicite (à l’aide de `UseSetting` ou l’une des méthodes d’extension explicite, tel que `UseUrls`). L’hôte utilise l’option qui définit la valeur en dernier. Si vous souhaitez définir l’URL par défaut à une valeur par programmation, mais lui permettent d’être substitué par une configuration, vous pouvez utiliser la configuration de ligne de commande après avoir défini l’URL. Consultez [configuration de remplacement](#overriding-configuration).

## <a name="starting-the-host"></a>Démarrage de l’ordinateur hôte

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**Exécuter**

Le `Run` méthode démarre l’application web et bloque le thread appelant jusqu'à ce que l’ordinateur hôte est arrêtée :

```csharp
host.Run();
```

**Start**

Vous pouvez exécuter l’hôte de manière non bloquante en appelant son `Start` méthode :

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Si vous passez une liste d’URL pour le `Start` (méthode), il est à l’écoute sur les URL spécifiées :

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

Vous pouvez initialiser et démarrer un nouvel hôte à l’aide des valeurs par défaut préconfigurés de `CreateDefaultBuilder` à l’aide d’une méthode pratique statique. Ces méthodes de démarrer le serveur sans la sortie de console et avec [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) attendre un saut (Ctrl-C/SIGINT ou SIGTERM) :

**Démarrer (application RequestDelegate)**

Démarrer avec un `RequestDelegate`:

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Effectuer une demande dans le navigateur pour `http://localhost:5000` pour recevoir la réponse « Hello World ! » `WaitForShutdown`bloque jusqu'à ce qu’un saut (Ctrl-C/SIGINT ou SIGTERM) est émis. L’application s’affiche la `Console.WriteLine` message et attend d’une touche quitter.

**Démarrer (string url RequestDelegate application)**

Démarrer avec une URL et `RequestDelegate`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Produit le même résultat en tant que **début (application RequestDelegate)**, à l’exception de l’application répond à `http://localhost:8080`.

**Démarrer (Action<IRouteBuilder> routeBuilder)**

Utiliser une instance de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) à utiliser le routage intergiciel (middleware) :

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

Utilisez les demandes suivantes du navigateur avec l’exemple :

| Requête                                    | Réponse                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Hello, Martin !                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos dias, Catrina !                    |
| `http://localhost:5000/throw/ooops!`       | Lève une exception avec la chaîne « ooops ! » |
| `http://localhost:5000/throw`              | Lève une exception avec la chaîne « Uh oh ! » |
| `http://localhost:5000/Sante/Kevin`        | Santé, Kevin !                            |
| `http://localhost:5000`                    | Hello World!                             |

`WaitForShutdown`bloque jusqu'à ce qu’un saut (Ctrl-C/SIGINT ou SIGTERM) est émis. L’application s’affiche la `Console.WriteLine` message et attend d’une touche quitter.

**Démarrer (string url, Action<IRouteBuilder> routeBuilder)**

Utiliser une URL et une instance de `IRouteBuilder`:

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

Produit le même résultat en tant que **Démarrer (Action<IRouteBuilder> routeBuilder)**, à l’exception de l’application répond à `http://localhost:8080`.

**StartWith (Action<IApplicationBuilder> application)**

Fournissez un délégué pour configurer un `IApplicationBuilder`:

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

Effectuer une demande dans le navigateur pour `http://localhost:5000` pour recevoir la réponse « Hello World ! » `WaitForShutdown`bloque jusqu'à ce qu’un saut (Ctrl-C/SIGINT ou SIGTERM) est émis. L’application s’affiche la `Console.WriteLine` message et attend d’une touche quitter.

**StartWith (string url, Action<IApplicationBuilder> application)**

Fournir une URL et un délégué pour configurer un `IApplicationBuilder`:

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

Produit le même résultat en tant que **StartWith (Action<IApplicationBuilder> app)**, à l’exception de l’application répond à `http://localhost:8080`.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**Exécuter**

Le `Run` méthode démarre l’application web et bloque le thread appelant jusqu'à ce que l’ordinateur hôte est arrêtée :

```csharp
host.Run();
```

**Start**

Vous pouvez exécuter l’hôte de manière non bloquante en appelant son `Start` méthode :

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Si vous passez une liste d’URL pour le `Start` (méthode), il est à l’écoute sur les URL spécifiées :


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

## <a name="ihostingenvironment-interface"></a>Interface de IHostingEnvironment

Le [IHostingEnvironment interface](/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) fournit des informations sur l’environnement d’hébergement web de l’application. Vous pouvez utiliser [injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir le `IHostingEnvironment` afin d’utiliser ses propriétés et les méthodes d’extension :

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

Vous pouvez utiliser un [approche basée sur une convention](xref:fundamentals/environments#startup-conventions) pour configurer votre application au démarrage sur l’environnement. Ou bien, vous pouvez injecter la `IHostingEnvironment` dans les `Startup` constructeur pour une utilisation dans `ConfigureServices`:

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
> Outre la `IsDevelopment` méthode d’extension, `IHostingEnvironment` offre `IsStaging`, `IsProduction`, et `IsEnvironment(string environmentName)` méthodes. Consultez [fonctionne avec plusieurs environnements](xref:fundamentals/environments) pour plus d’informations.

Le `IHostingEnvironment` service peut également être injecté directement dans le `Configure` méthode de configuration de votre pipeline de traitement :

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

Vous pouvez injecter `IHostingEnvironment` dans les `Invoke` méthode lors de la création personnalisée [intergiciel (middleware)](xref:fundamentals/middleware#writing-middleware):

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

## <a name="iapplicationlifetime-interface"></a>Interface de IApplicationLifetime

Le [IApplicationLifetime interface](/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) vous permet d’effectuer les activités postérieur au démarrage et d’arrêt. Trois propriétés sur l’interface sont des jetons d’annulation que vous pouvez inscrire avec `Action` méthodes pour définir les événements de démarrage et d’arrêt. Il existe également un `StopApplication` (méthode).

| Jeton d’annulation    | Déclenché lors de le &#8230; |
| --------------------- | --------------------- |
| `ApplicationStarted`  | L’ordinateur hôte a entièrement démarré. |
| `ApplicationStopping` | L’hôte effectue un arrêt approprié. Les demandes peuvent être encore en cours. Blocs d’arrêt jusqu'à la fin de cet événement. |
| `ApplicationStopped`  | L’ordinateur hôte est la fin de l’arrêt approprié. Toutes les demandes doivent être complètement traitées. Blocs d’arrêt jusqu'à la fin de cet événement. |

| Méthode            | Action                                           |
| ----------------- | ------------------------------------------------ |
| `StopApplication` | Arrêt de demandes de l’application actuelle. |

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

## <a name="troubleshooting-systemargumentexception"></a>Résolution des problèmes de System.ArgumentException

**S’applique à ASP.NET Core 2.0 uniquement**

Si vous créez l’ordinateur hôte en injectant `IStartup` directement dans le conteneur d’injection de dépendance au lieu d’appeler `UseStartup` ou `Configure`, vous pouvez rencontrer l’erreur suivante : `Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided`.

Cela se produit car le [applicationName(ApplicationKey)](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) (l’assembly actuel) est requis pour analyser pour `HostingStartupAttributes`. Si vous avez injecter manuellement `IStartup` dans le conteneur d’injection de dépendance, ajoutez l’appel suivant à votre `WebHostBuilder` portant le nom de l’assembly spécifié :

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("applicationName", "<Assembly Name>")
    ...
```

Vous pouvez également ajouter un fantôme `Configure` à votre `WebHostBuilder`, qui affecte le `applicationName`(`ApplicationKey`) automatiquement :

```csharp
WebHost.CreateDefaultBuilder(args)
    .Configure(_ => { })
    ...
```

**Remarque**: cela est uniquement nécessaire avec la version de ASP.NET Core 2.0 et uniquement lorsque vous n’appelez pas `UseStartup` ou `Configure`.

Pour plus d’informations, consultez [annonces : Microsoft.Extensions.PlatformAbstractions a été supprimé (commentaire)](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) et [StartupInjection exemple](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Publier sur Windows à l’aide d’IIS](../publishing/iis.md)
* [Publier sur Linux à l’aide de Nginx](../publishing/linuxproduction.md)
* [Publier sur Linux à l’aide d’Apache](../publishing/apache-proxy.md)
* [Ordinateur hôte dans un Service Windows](xref:hosting/windows-service)

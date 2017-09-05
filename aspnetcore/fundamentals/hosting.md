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
# <a name="introduction-to-hosting-in-aspnet-core"></a>Introduction à l’hébergement dans ASP.NET Core

Par [Steve Smith](http://ardalis.com)

Pour exécuter une application ASP.NET Core, vous devez configurer et de lancer un ordinateur hôte à l’aide de `WebHostBuilder`.

## <a name="what-is-a-host"></a>Qu’est un ordinateur hôte ?

Les applications ASP.NET Core nécessitent un *hôte* dans lequel exécuter. Un ordinateur hôte doit implémenter la `IWebHost` interface qui expose des ensembles de fonctionnalités et les services, et un `Start` (méthode). L’ordinateur hôte est généralement créé à l’aide d’une instance d’un `WebHostBuilder`, ce qui génère et retourne un `WebHost` instance. Le `WebHost` fait référence au serveur qui va gérer les demandes. En savoir plus sur [serveurs](servers/index.md).

### <a name="what-is-the-difference-between-a-host-and-a-server"></a>Quelle est la différence entre un hôte et un serveur ?

L’hôte est responsable de la gestion de démarrage et la durée de vie des applications. Le serveur est chargé d’accepter les demandes HTTP. Partie de la responsabilité de l’ordinateur hôte inclut la vérification que des services de l’application et le serveur sont disponible et correctement configuré. Vous pouvez considérer l’hôte comme étant un wrapper autour du serveur. L’hôte est configuré pour utiliser un serveur particulier ; le serveur n’a pas connaissance de l’hôte.

## <a name="setting-up-a-host"></a>Configuration d’un ordinateur hôte

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Vous créez un hôte à l’aide d’une instance de `WebHostBuilder`. Cette opération s’effectue généralement dans le point d’entrée de votre application : `public static void Main` (qui, dans les modèles de projet se trouve dans un *Program.cs* fichier). Par défaut *Program.cs*, comme illustré ci-dessous, montre comment utiliser un `WebHostBuilder` pour créer un ordinateur hôte.

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=14,15,16,17,18,19,20,21)]

Le `WebHostBuilder` est responsable de la création de l’hôte qui sera amorcer le serveur pour l’application. `WebHostBuilder`nécessite de vous fournir un serveur qui implémente `IServer` (`UseKestrel` dans le code ci-dessus). `UseKestrel`Spécifie que le serveur Kestrel sera utilisé par l’application.

Le serveur *racine du contenu* détermine où il recherche les fichiers de contenu, tels que les fichiers de vue MVC. La racine de contenu par défaut est le dossier à partir duquel l’application est exécutée.

> [!NOTE]
> Spécification de `Directory.GetCurrentDirectory` comme la racine du contenu servira de dossier racine du projet web racine du contenu de l’application lorsque l’application est démarrée à partir de ce dossier (par exemple, l’appel `dotnet run` à partir du dossier de projet web). Ceci est la valeur par défaut utilisée dans Visual Studio et `dotnet new` modèles.

Pour utiliser IIS comme un proxy inverse, appelez `UseIISIntegration` dans le cadre de la création de l’ordinateur hôte. 

Notez que `UseIISIntegration` ne configure pas un *server*, comme `UseKestrel` est. Pour utiliser IIS avec ASP.NET Core, vous devez spécifier les deux `UseKestrel` et `UseIISIntegration`. `UseKestrel`crée le serveur web et héberge l’application. `UseIISIntegration`examine les variables d’environnement utilisées par IIS/IISExpress et configure les paramètres tels que le port d’écoute et les en-têtes à utiliser.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Vous créez un hôte à l’aide d’une instance de `WebHostBuilder`. Cette opération s’effectue généralement dans le point d’entrée de votre application : `public static void Main` (qui, dans les modèles de projet se trouve dans un *Program.cs* fichier). Par défaut *Program.cs*, comme illustré ci-dessous, les appels `CreateDefaultbuilder` pour créer un ordinateur hôte :

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

`CreateDefaultbuilder`Crée une instance de `WebHostBuilder` pour générer l’hôte que les données du serveur pour l’application d’amorçage. L’hôte requiert un [serveur qui implémente IServer](servers/index.md). Serveurs intégrés sont [Kestrel](servers/kestrel.md) et [HTTP.sys](servers/httpsys.md); `CreateDefaultbuilder` utilisent Kestrel par défaut.

`CreateDefaultbuilder`exécute les tâches de configuration en plus de configurer Kestrel que le serveur web :

* Affecte à la racine de contenu `Directory.GetCurrentDirectory`.
* Configuration de la charge à partir de :
  * *appSettings.JSON*
  * *appSettings. \<EnvironmentName > .json*.
  * secrets d’utilisateur lorsque l’application s’exécute dans l’environnement de développement
  * variables d'environnement
  * arguments de ligne de commande fournis
* Configure la journalisation pour la sortie de console et de débogage, le filtrage des règles spécifiées dans une section de configuration de journalisation.
* Permet l’intégration d’IIS.
* Ajoute la page d’exception de développeur lorsque l’application s’exécute dans l’environnement de développement.

Le serveur *racine du contenu* détermine où il recherche les fichiers de contenu, tels que les fichiers de vue MVC. La racine de contenu par défaut est le dossier à partir duquel l’application est exécutée.

> [!NOTE]
> Spécification de `Directory.GetCurrentDirectory` comme la racine du contenu servira de dossier racine du projet web racine du contenu de l’application lorsque l’application est démarrée à partir de ce dossier (par exemple, l’appel `dotnet run` à partir du dossier de projet web). Ceci est la valeur par défaut utilisée dans Visual Studio et `dotnet new` modèles.

Lorsque vous utilisez IIS comme un proxy inverse, ASP.NET Core appelle automatiquement `UseIISIntegration` en tant que partie de la création de l’ordinateur hôte. Pour plus d’informations, consultez [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).

Notez que `UseIISIntegration` ne configure pas un *server*, comme `UseKestrel` est. `UseKestrel`crée le serveur web et héberge l’application. `UseIISIntegration`examine les variables d’environnement utilisées par IIS/IISExpress et configure les paramètres tels que le port d’écoute et les en-têtes à utiliser.

---

Une implémentation minimale de la configuration d’un ordinateur hôte (et une application ASP.NET Core) inclut uniquement un serveur et la configuration du pipeline de demande de l’application :

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
> Lorsque vous configurez un ordinateur hôte, vous pouvez fournir `Configure` et `ConfigureServices` méthodes, à la place ou en plus de spécifier un `Startup` classe (qui doit également définir ces méthodes - consultez [démarrage de l’Application](startup.md)). Appels multiples à `ConfigureServices` sont ajoutées à un autre ; les appels à `Configure` ou `UseStartup` remplaceront les paramètres précédents.

## <a name="configuring-a-host"></a>Configuration d’un hôte

Le `WebHostBuilder` fournit des méthodes permettant de définir la plupart des valeurs de configuration disponibles pour l’hôte, ce qui peut également être définie directement à l’aide de `UseSetting` et la clé associée.

### <a name="host-configuration-values"></a>Valeurs de Configuration d’hôte

**Capturer les erreurs de démarrage**`bool`

Clé : `captureStartupErrors`. La valeur par défaut est `false`. Lorsque `false`, erreurs de résultat de démarrage de l’ordinateur hôte en cours de fermeture. Lorsque `true`, l’hôte de capture toutes les exceptions à partir de la `Startup` classe et essayez de démarrer le serveur. Il affiche une page d’erreur (générique, ou plus, selon le paramètre d’erreurs détaillées, ci-dessous) pour chaque demande. À l’aide de la `CaptureStartupErrors` (méthode).

Remarque : Quand votre application s’exécute avec Kestrel et IIS, le comportement par défaut consiste à capturer les erreurs de démarrage. 

```csharp
new WebHostBuilder()
    .CaptureStartupErrors(true)
   ```

**Racine du contenu**`string`

Clé : `contentRoot`. Par défaut, le dossier dans lequel l’assembly d’application réside (pour Kestrel ; IIS utilisera la racine du projet web par défaut). Ce paramètre détermine où ASP.NET Core commence à rechercher les fichiers de contenu, tels que les vues MVC. Également utilisé en tant que le chemin d’accès de base pour le [configuration de la racine Web](#web-root-setting). À l’aide de la `UseContentRoot` (méthode). Chemin d’accès doit exister ou ordinateur hôte ne pourront pas démarrer.

```csharp
new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
   ```

**Erreurs détaillées**`bool`

Clé : `detailedErrors`. La valeur par défaut est `false`. Lorsque `true` (ou lorsque environnement a la valeur « Développement »), l’application affiche les détails des exceptions de démarrage, au lieu de simplement une page d’erreur générique. À l’aide de `UseSetting`.

```csharp
new WebHostBuilder()
    .UseSetting("detailedErrors", "true")
```

Lorsque les erreurs détaillées a la valeur `false` et capturer les erreurs de démarrage est `true`, une page d’erreur générique s’affiche en réponse à chaque demande au serveur.

![Page d’erreur générique](hosting/_static/generic-error-page.png)

Lorsque les erreurs détaillées a la valeur `true` et capturer les erreurs de démarrage est `true`, une page d’erreur détaillé s’affiche en réponse à chaque demande au serveur.

![Page d’erreur détaillées](hosting/_static/detailed-error-page.png)

**Environnement**`string`

Clé : `environment`. La valeur par défaut est « Production ». Peut être définie sur n’importe quelle valeur. Les valeurs définies par l’infrastructure incluent « Développement », « Étape intermédiaire » et « Production ». Valeurs ne respectent pas la casse. Consultez [fonctionne avec plusieurs environnements](environments.md). À l’aide de la `UseEnvironment` (méthode).

```csharp
new WebHostBuilder()
    .UseEnvironment("Development")
```

> [!NOTE]
> Par défaut, l’environnement est en lecture à partir de la `ASPNETCORE_ENVIRONMENT` variable d’environnement. Lorsque vous utilisez Visual Studio, les variables d’environnement peuvent être définies dans le *launchSettings.json* fichier.

<a id="server-urls"></a>

**URL du serveur**`string`

Clé : `urls`. Un point-virgule ( ;) la valeur séparées par des préfixes de la liste des URL à laquelle le serveur doit répondre. Par exemple, `http://localhost:123`. Le nom de domaine/de l’hôte peut être remplacé par «\*» pour indiquer le serveur doit écouter les demandes sur n’importe quelle adresse IP ou hôte à l’aide du port spécifié et le protocole (par exemple, `http://*:5000` ou `https://*:5001`). Le protocole (`http://` ou `https://`) doivent être incluses avec chaque URL. Les préfixes sont interprétés par le serveur configuré ; formats pris en charge varie entre serveurs.

```csharp
new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

Dans ASP.NET 2.0 de noyaux, Kestrel a sa propre API de configuration du point de terminaison et ne prend pas en charge `https://` dans le `urls` chaîne. Pour plus d’informations, consultez [présentation Kestrel](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).

**Assembly de démarrage**`string`

Clé : `startupAssembly`. Détermine l’assembly à rechercher la `Startup` classe. À l’aide de la `UseStartup` (méthode). Peut faire référence à la place à l’aide du type spécifique `WebHostBuilder.UseStartup<StartupType>`. Si plusieurs `UseStartup` méthodes sont appelées, le dernier est prioritaire.

```csharp
new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
```

<a name=web-root-setting></a>

**Racine du site Web**`string`

Clé : `webroot`. Si non spécifié à la valeur par défaut est `(Content Root Path)\wwwroot`, s’il existe. Si ce chemin d’accès n’existe pas, un fournisseur de fichiers de l’absence d’opération est utilisé. À l’aide de `UseWebRoot`.

```csharp
new WebHostBuilder()
    .UseWebRoot("public")
```

### <a name="overriding-configuration"></a>Substitution de Configuration

Utilisez [Configuration](configuration.md) pour définir les valeurs de configuration à utiliser par l’hôte. Ces valeurs peuvent être remplacées par la suite. Cela est spécifié à l’aide de `UseConfiguration`.

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

Dans l’exemple ci-dessus, les arguments de ligne de commande peuvent être transmis dans pour configurer l’ordinateur hôte, ou les paramètres de configuration peuvent éventuellement être spécifiés dans un *hosting.json* fichier. Pour spécifier l’hôte s’exécutent sur une URL particulière, vous pouvez transmettre la valeur souhaitée à partir d’une invite de commandes :

```console
dotnet run --urls "http://*:5000"
```

Le `Run` méthode démarre l’application web et bloque le thread appelant jusqu'à ce que l’ordinateur hôte est arrêtée.

```csharp
host.Run();
```

Vous pouvez exécuter l’hôte de manière non bloquante en appelant son `Start` méthode :

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Passez une liste d’URL pour le `Start` (méthode) et qu’il écoute sur les URL spécifiées :

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

Les formats d’URL qui sont valides ici dépendent du serveur que vous utilisez. Pour plus d’informations, consultez [URL de serveur](#server-urls) plus haut dans cet article.

> [!NOTE]
> Le `UseConfiguration` méthode d’extension n’est pas actuellement capable d’analyser une section de configuration retournée par `GetSection` (par exemple, `.UseConfiguration(Configuration.GetSection("section"))`. Le `GetSection` méthode filtre les clés de configuration à la section demandée, mais laisse le nom de la section sur les clés (par exemple, `section:urls`, `section:environment`). Le `UseConfiguration` méthode attend les clés pour faire correspondre le `WebHostBuilder` clés (par exemple, `urls`, `environment`). La présence d’un nom de la section sur les clés empêche des valeurs de la section à partir de la configuration de l’hôte. Ce problème sera résolu dans une prochaine mise en production. Pour plus d’informations et des solutions de contournement, consultez [en passant de la section de configuration dans WebHostBuilder.UseConfiguration utilise des clés complètes](https://github.com/aspnet/Hosting/issues/839).

### <a name="ordering-importance"></a>Ordre d’Importance

`WebHostBuilder`les paramètres sont tout d’abord lus à partir de certaines variables d’environnement, si définie. Ces variables d’environnement doivent utiliser le format `ASPNETCORE_{configurationKey}`, par exemple définir les URL écoute sur le serveur par défaut, vous devez définir `ASPNETCORE_URLS`.

Vous pouvez remplacer ces valeurs de variable d’environnement en spécifiant la configuration (à l’aide de `UseConfiguration`) ou en définissant la valeur de manière explicite (à l’aide de `UseUrls` par exemple). L’hôte utilisera l’option qui définit la valeur en dernier. Pour cette raison, `UseIISIntegration` doivent apparaître après `UseUrls`, car elle remplace l’URL dynamique fourni par IIS. Si vous souhaitez par programmation la valeur de l’URL par défaut une valeur, mais lui permettent d’être substitué par une configuration, vous pourriez configurer l’ordinateur hôte comme suit :

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

## <a name="additional-resources"></a>Ressources supplémentaires

* [Publier sur Windows à l’aide d’IIS](../publishing/iis.md)
* [Publier sur Linux à l’aide de Nginx](../publishing/linuxproduction.md)
* [Publier sur Linux à l’aide d’Apache](../publishing/apache-proxy.md)
* [Ordinateur hôte dans un Service Windows](xref:hosting/windows-service)


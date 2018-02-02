---
title: "Ordinateur hôte dans un Service Windows"
author: tdykstra
description: "Découvrez comment héberger une application ASP.NET Core dans un Service Windows."
manager: wpickett
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/30/2018
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/windows-service
ms.openlocfilehash: c14a1f62bce4d06be3b8e6356f45cd5e330a0751
ms.sourcegitcommit: f2a11a89037471a77ad68a67533754b7bb8303e2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="host-an-aspnet-core-app-in-a-windows-service"></a>Héberger une application ASP.NET Core dans un Service Windows

Par [Tom Dykstra](https://github.com/tdykstra)

La méthode recommandée pour héberger une application ASP.NET Core sur Windows sans l’aide d’IIS est exécuté un [Service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications). Lorsqu’il est hébergé comme un Service Windows, l’application peut automatiquement démarrer après redémarrage et tombe en panne sans nécessiter une intervention humaine.

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/sample) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample)). Pour obtenir des instructions sur la façon d’exécuter l’exemple d’application, consultez l’exemple *README.md* fichier.

## <a name="prerequisites"></a>Prérequis

* L’application doit s’exécuter sur le runtime .NET Framework. Dans le *.csproj* de fichiers, spécifiez les valeurs appropriées pour [TargetFramework](/nuget/schema/target-frameworks) et [RuntimeIdentifier](/dotnet/articles/core/rid-catalog). Voici un exemple :

  [!code-xml[](windows-service/sample/AspNetCoreService.csproj?range=3-6)]

  Lorsque vous créez un projet dans Visual Studio, utilisez le **ASP.NET Core Application (.NET Framework)** modèle.

* Si l’application reçoit des demandes à partir d’Internet (pas uniquement à partir d’un réseau interne), elle doit utiliser le [HTTP.sys](xref:fundamentals/servers/httpsys) serveur web (anciennement [WebListener](xref:fundamentals/servers/weblistener) pour les applications ASP.NET Core 1.x) au lieu de [Kestrel](xref:fundamentals/servers/kestrel). IIS est recommandé d’utiliser comme un serveur proxy inverse avec Kestrel pour les déploiements de bord. Pour plus d’informations, consultez [Quand utiliser Kestrel avec un proxy inverse ?](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="getting-started"></a>Bien démarrer

Cette section explique les modifications minimales requises pour configurer un projet ASP.NET Core existant pour s’exécuter dans un service.

1. Installez le package NuGet [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices/).

1. Apportez les modifications suivantes dans `Program.Main`:
  
   * Appelez `host.RunAsService` au lieu de `host.Run`.
  
   * Si le code appelle `UseContentRoot`, utilisez un chemin d’accès à l’emplacement de publication au lieu de `Directory.GetCurrentDirectory()`.

   # <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   [!code-csharp[](windows-service/sample/Program.cs?name=ServiceOnly&highlight=3-4,7,12)]

   # <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   [!code-csharp[](windows-service/sample_snapshot/Program.cs?name=ServiceOnly&highlight=3-4,8,14)]

   ---

1. Publier l’application dans un dossier. Utilisez [dotnet publier](/dotnet/articles/core/tools/dotnet-publish) ou un [profil de publication de Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles) qui publie vers un dossier.

1. Testez en créant et en démarrant le service.

   Ouvrez une invite de commandes avec des privilèges d’administrateur pour utiliser le [sc.exe](https://technet.microsoft.com/library/bb490995) outil de ligne de commande pour créer et démarrer un service. Si le service se nomme MyService, publié sur `c:\svc`, et nommé AspNetCoreService, les commandes sont :

   ```console
   sc create MyService binPath="c:\svc\aspnetcoreservice.exe"
   sc start MyService
   ```

   Le `binPath` valeur est le chemin d’accès au fichier exécutable de l’application, qui inclut le nom du fichier exécutable.

   ![Fenêtre de console créer et démarrer l’exemple](windows-service/_static/create-start.png)

   Lors de la fin de ces commandes, recherchez le même chemin que lors de l’exécution en tant qu’application console (par défaut, `http://localhost:5000`) :

   ![En cours d’exécution dans un service](windows-service/_static/running-in-service.png)

## <a name="provide-a-way-to-run-outside-of-a-service"></a>Fournir un moyen d’exécuter en dehors d’un service

Il est plus facile tester et déboguer lors de l’exécution en dehors d’un service, il est habituel pour ajouter du code qui appelle `RunAsService` uniquement sous certaines conditions. Par exemple, l’application peut s’exécuter comme une application de console avec une `--console` argument de ligne de commande ou si le débogueur est attaché :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[](windows-service/sample/Program.cs?name=ServiceOrConsole)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[](windows-service/sample_snapshot/Program.cs?name=ServiceOrConsole)]

---

## <a name="handle-stopping-and-starting-events"></a>Gérer les événements de démarrage et arrêt

Pour gérer les `OnStarting`, `OnStarted`, et `OnStopping` événements, apporter les modifications supplémentaires suivantes :

1. Créez une classe qui dérive de `WebHostService`:

   [!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=NoLogging)]

1. Créer une méthode d’extension pour `IWebHost` qui transmet personnalisé `WebHostService` à `ServiceBase.Run`:

   [!code-csharp[](windows-service/sample/WebHostServiceExtensions.cs?name=ExtensionsClass)]

1. Dans `Program.Main`, appelez la nouvelle méthode d’extension, `RunAsCustomService`, au lieu de `RunAsService`:

   # <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   [!code-csharp[](windows-service/sample/Program.cs?name=HandleStopStart&highlight=24)]

   # <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   [!code-csharp[](windows-service/sample_snapshot/Program.cs?name=HandleStopStart&highlight=26)]

   ---

Si personnalisé `WebHostService` code nécessite un service à partir de l’injection de dépendances (par exemple, un journal), l’obtenir à partir de la `Services` propriété du `IWebHost`:

[!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=Logging&highlight=7)]

## <a name="acknowledgments"></a>Accusés de réception

Cet article a été écrite à l’aide de sources publiés :

* [Hébergement ASP.NET Core en tant que service Windows](https://stackoverflow.com/questions/37346383/hosting-asp-net-core-as-windows-service/37464074)
* [L’hébergement de base ASP.NET dans un Service Windows](https://dotnetthoughts.net/how-to-host-your-aspnet-core-in-a-windows-service/)

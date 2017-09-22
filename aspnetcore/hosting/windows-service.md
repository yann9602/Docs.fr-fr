---
title: "Ordinateur hôte dans un Service Windows"
author: tdykstra
description: "Découvrez comment héberger une application ASP.NET Core dans un Service Windows."
keywords: "ASP.NET Core, le service Windows, d’hébergement"
ms.author: tdykstra
manager: wpickett
ms.date: 03/30/2017
ms.topic: article
ms.assetid: d9a65066-d7cb-47df-b046-64629c4d2c6f
ms.technology: aspnet
ms.prod: aspnet-core
uid: hosting/windows-service
ms.openlocfilehash: 5b54c77ff9e019b1d550aa687923077a3e9ba5c2
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="host-an-aspnet-core-app-in-a-windows-service"></a>Héberger une application ASP.NET Core dans un Service Windows

Par [Tom Dykstra](https://github.com/tdykstra)

La méthode recommandée pour héberger une application ASP.NET Core sur Windows quand vous n’utilisez pas IIS consiste à exécuter dans un [Service Windows](https://docs.microsoft.com/dotnet/framework/windows-services/introduction-to-windows-service-applications). De cette façon qu’il peut démarrer automatiquement après les redémarrages et les blocages, sans attendre d’une personne pour se connecter.

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample) voir le [étapes](#next-steps) section pour obtenir des instructions sur la façon de l’exécuter.

## <a name="prerequisites"></a>Conditions préalables

* L’application doit s’exécuter sur le runtime .NET framework.  Dans le *.csproj* de fichiers, spécifiez les valeurs appropriées pour [TargetFramework](https://docs.microsoft.com/nuget/schema/target-frameworks) et [RuntimeIdentifier](https://docs.microsoft.com/dotnet/articles/core/rid-catalog). Voici un exemple :

  [!code-xml[](windows-service/sample/AspNetCoreService.csproj?range=3-6)]

  Lorsque vous créez un projet dans Visual Studio, utilisez le **ASP.NET Core Application (.NET Framework)** modèle.

* Si l’application obtiendra-t-elle les demandes à partir d’internet (pas uniquement à partir d’un réseau interne), elle doit utiliser le [WebListener](xref:fundamentals/servers/weblistener) serveur web au lieu de [Kestrel](xref:fundamentals/servers/kestrel).  Kestrel doit être utilisé avec IIS pour les déploiements de bord.  Pour plus d’informations, consultez [Quand utiliser Kestrel avec un proxy inverse ?](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="getting-started"></a>Bien démarrer

Cette section explique les modifications minimales requises pour configurer un projet ASP.NET Core existant pour s’exécuter dans un service.

* Installez le package NuGet [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices/).

* Apportez les modifications suivantes dans `Program.Main`:
  
  * Appelez `host.RunAsService` au lieu de `host.Run`.
  
  * Si votre code appelle `UseContentRoot`, utilisez un chemin d’accès à l’emplacement de publication au lieu de`Directory.GetCurrentDirectory()` 
  
  [!code-csharp[](windows-service/sample/Program.cs?name=ServiceOnly&highlight=3-4,8,14)]

* Publier l’application dans un dossier.

  Utilisez [dotnet publier](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish) ou un [profil de publication de Visual Studio](xref:publishing/web-publishing-vs) qui publie vers un dossier.

* Testez en créant et en démarrant le service.

  Ouvrez une fenêtre d’invite de commandes administrateur pour utiliser le [sc.exe](https://technet.microsoft.com/library/bb490995) outil de ligne de commande pour créer et démarrer un service.  
  
  Si vous le nommez votre service MyService, vous publiez votre application à `c:\svc`et l’application proprement dite est nommée AspNetCoreService, les commandes ressemble à ceci :

  ```console
  sc create MyService binPath="C:\Svc\AspNetCoreService.exe"
  sc start MyService
  ```
  Le `binPath` valeur est le chemin d’accès à l’exécutable de votre application, y compris le nom de fichier exécutable.

  ![Fenêtre de console créer et démarrer l’exemple](windows-service/_static/create-start.png)

  Lorsque ces commandes terminé, vous pouvez accéder à la même chemin d’accès lorsque vous exécutez en tant qu’une application console (par défaut, `http://localhost:5000`)

  ![En cours d’exécution dans un service](windows-service/_static/running-in-service.png)


## <a name="provide-a-way-to-run-outside-of-a-service"></a>Fournir un moyen d’exécuter en dehors d’un service

Il est plus facile tester et déboguer lors de l’exécution en dehors d’un service, il est habituel pour ajouter du code qui appelle `host.RunAsService` uniquement sous certaines conditions.  Par exemple, vous pouvez exécuter en tant qu’une application console si vous obtenez un `--console` argument de ligne de commande ou si le débogueur est attaché.

[!code-csharp[](windows-service/sample/Program.cs?name=ServiceOrConsole)]

## <a name="handle-stopping-and-starting-events"></a>Gérer les événements de démarrage et arrêt

Si vous souhaitez gérer `OnStarting`, `OnStarted`, et `OnStopping` événements, apporter les modifications supplémentaires suivantes :

* Créez une classe qui dérive de `WebHostService`.

  [!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=NoLogging)]

* Créer une méthode d’extension pour `IWebHost` qui transmet vos `WebHostService` à `ServiceBase.Run`.

  [!code-csharp[](windows-service/sample/WebHostServiceExtensions.cs?name=ExtensionsClass)]

* Dans `Program.Main` modification appeler la nouvelle méthode d’extension à la place de `host.RunAsService`.

  [!code-csharp[](windows-service/sample/Program.cs?name=HandleStopStart&highlight=26)]

Si votre personnalisé `WebHostService` code a besoin d’obtenir un service à partir de l’injection de dépendances (par exemple, un journal), vous pouvez l’obtenir à partir de la `Services` propriété du `IWebHost`.

[!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=Logging&highlight=7)]

## <a name="next-steps"></a>Étapes suivantes

Le [exemple d’application](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample) qui accompagne cette article est une application web MVC simple qui a été modifiée comme indiqué dans les précédents exemples de code.  Pour l’exécuter dans un service, procédez comme suit :

* Publier sur *c:\svc*.

* Ouvrez une fenêtre de l’administrateur.

* Entrez les commandes suivantes :

  ```console
  sc create MyService binPath="c:\svc\aspnetcoreservice.exe"
  sc start MyService
  ```

  * Dans un navigateur, accédez à http://localhost : 5000 pour vérifier qu’il s’exécute.

Si l’application ne démarre pas comme prévu lors de l’exécution dans un service, un moyen rapide pour rendre les messages d’erreur accessible est pour ajouter un module fournisseur d’informations telles que la [fournisseur du journal des événements Windows](xref:fundamentals/logging#eventlog).

## <a name="acknowledgments"></a>Accusés de réception

Cet article a été écrit à l’aide de sources qui ont été déjà publiés. Au plus tôt et plus utiles d'entre eux ont été ceux-ci :

* [Hébergement ASP.NET Core en tant que service Windows](https://stackoverflow.com/questions/37346383/hosting-asp-net-core-as-windows-service/37464074)
* [L’hébergement de base ASP.NET dans un Service Windows](https://dotnetthoughts.net/how-to-host-your-aspnet-core-in-a-windows-service/)

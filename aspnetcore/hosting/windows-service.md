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
ms.openlocfilehash: 1b3cdc18ded89ebdf7b7afa9f43af9669748eff4
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="host-an-aspnet-core-app-in-a-windows-service"></a><span data-ttu-id="d3162-104">Héberger une application ASP.NET Core dans un Service Windows</span><span class="sxs-lookup"><span data-stu-id="d3162-104">Host an ASP.NET Core app in a Windows Service</span></span>

<span data-ttu-id="d3162-105">Par [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="d3162-105">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="d3162-106">La méthode recommandée pour héberger une application ASP.NET Core sur Windows quand vous n’utilisez pas IIS consiste à exécuter dans un [Service Windows](https://msdn.microsoft.com/library/d56de412).</span><span class="sxs-lookup"><span data-stu-id="d3162-106">The recommended way to host an ASP.NET Core app on Windows when you don't use IIS is to run it in a [Windows Service](https://msdn.microsoft.com/library/d56de412).</span></span> <span data-ttu-id="d3162-107">De cette façon qu’il peut démarrer automatiquement après les redémarrages et les blocages, sans attendre d’une personne pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="d3162-107">That way it can automatically start after reboots and crashes, without waiting for someone to log in.</span></span>

<span data-ttu-id="d3162-108">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample) voir le [étapes](#next-steps) section pour obtenir des instructions sur la façon de l’exécuter.</span><span class="sxs-lookup"><span data-stu-id="d3162-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample) See the [Next Steps](#next-steps) section for instructions on how to run it.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d3162-109">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="d3162-109">Prerequisites</span></span>

* <span data-ttu-id="d3162-110">L’application doit s’exécuter sur le runtime .NET framework.</span><span class="sxs-lookup"><span data-stu-id="d3162-110">The app must run on the .NET framework runtime.</span></span>  <span data-ttu-id="d3162-111">Dans le *.csproj* de fichiers, spécifiez les valeurs appropriées pour [TargetFramework](https://docs.microsoft.com/nuget/schema/target-frameworks) et [RuntimeIdentifier](https://docs.microsoft.com/dotnet/articles/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d3162-111">In the *.csproj* file, specify appropriate values for [TargetFramework](https://docs.microsoft.com/nuget/schema/target-frameworks) and [RuntimeIdentifier](https://docs.microsoft.com/dotnet/articles/core/rid-catalog).</span></span> <span data-ttu-id="d3162-112">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="d3162-112">Here's an example:</span></span>

  [!code-xml[](windows-service/sample/AspNetCoreService.csproj?range=3-6)]

  <span data-ttu-id="d3162-113">Lorsque vous créez un projet dans Visual Studio, utilisez le **ASP.NET Core Application (.NET Framework)** modèle.</span><span class="sxs-lookup"><span data-stu-id="d3162-113">When creating a project in Visual Studio, use the **ASP.NET Core Application (.NET Framework)** template.</span></span>

* <span data-ttu-id="d3162-114">Si l’application obtiendra-t-elle les demandes à partir d’internet (pas uniquement à partir d’un réseau interne), elle doit utiliser le [WebListener](xref:fundamentals/servers/weblistener) serveur web au lieu de [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="d3162-114">If the app will get requests from the internet (not just from an internal network), it must use the [WebListener](xref:fundamentals/servers/weblistener) web server rather than [Kestrel](xref:fundamentals/servers/kestrel).</span></span>  <span data-ttu-id="d3162-115">Kestrel doit être utilisé avec IIS pour les déploiements de bord.</span><span class="sxs-lookup"><span data-stu-id="d3162-115">Kestrel must be used with IIS for edge deployments.</span></span>  <span data-ttu-id="d3162-116">Pour plus d’informations, consultez [quand utiliser Kestrel avec un proxy inverse](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="d3162-116">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="getting-started"></a><span data-ttu-id="d3162-117">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="d3162-117">Getting started</span></span>

<span data-ttu-id="d3162-118">Cette section explique les modifications minimales requises pour configurer un projet ASP.NET Core existant pour s’exécuter dans un service.</span><span class="sxs-lookup"><span data-stu-id="d3162-118">This section explains the minimum changes required to set up an existing ASP.NET Core project to run in a service.</span></span>

* <span data-ttu-id="d3162-119">Installez le package NuGet [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices/).</span><span class="sxs-lookup"><span data-stu-id="d3162-119">Install the NuGet package [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices/).</span></span>

* <span data-ttu-id="d3162-120">Apportez les modifications suivantes dans `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d3162-120">Make the following changes in `Program.Main`:</span></span>
  
  * <span data-ttu-id="d3162-121">Appelez `host.RunAsService` au lieu de `host.Run`.</span><span class="sxs-lookup"><span data-stu-id="d3162-121">Call `host.RunAsService` instead of `host.Run`.</span></span>
  
  * <span data-ttu-id="d3162-122">Si votre code appelle `UseContentRoot`, utilisez un chemin d’accès à l’emplacement de publication au lieu de`Directory.GetCurrentDirectory()`</span><span class="sxs-lookup"><span data-stu-id="d3162-122">If your code calls `UseContentRoot`, use a path to the publish location instead of `Directory.GetCurrentDirectory()`</span></span> 
  
  [!code-csharp[](windows-service/sample/Program.cs?name=ServiceOnly&highlight=3-4,8,14)]

* <span data-ttu-id="d3162-123">Publier l’application dans un dossier.</span><span class="sxs-lookup"><span data-stu-id="d3162-123">Publish the application to a folder.</span></span>

  <span data-ttu-id="d3162-124">Utilisez [dotnet publier](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish) ou un [profil de publication de Visual Studio](xref:publishing/web-publishing-vs) qui publie vers un dossier.</span><span class="sxs-lookup"><span data-stu-id="d3162-124">Use [dotnet publish](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish) or a [Visual Studio publish profile](xref:publishing/web-publishing-vs) that publishes to a folder.</span></span>

* <span data-ttu-id="d3162-125">Testez en créant et en démarrant le service.</span><span class="sxs-lookup"><span data-stu-id="d3162-125">Test by creating and starting the service.</span></span>

  <span data-ttu-id="d3162-126">Ouvrez une fenêtre d’invite de commandes administrateur pour utiliser le [sc.exe](https://technet.microsoft.com/library/bb490995) outil de ligne de commande pour créer et démarrer un service.</span><span class="sxs-lookup"><span data-stu-id="d3162-126">Open an administrator command prompt window to use the [sc.exe](https://technet.microsoft.com/library/bb490995) command-line tool to create and start a service.</span></span>  
  
  <span data-ttu-id="d3162-127">Si vous le nommez votre service MyService, vous publiez votre application à `c:\svc`et l’application proprement dite est nommée AspNetCoreService, les commandes ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="d3162-127">If you name your service MyService, you publish your app to `c:\svc`, and the app itself is named AspNetCoreService, the commands would look like this:</span></span>

  ```console
  sc create MyService binPath="C:\Svc\AspNetCoreService.exe"
  sc start MyService
  ```
  <span data-ttu-id="d3162-128">Le `binPath` valeur est le chemin d’accès à l’exécutable de votre application, y compris le nom de fichier exécutable.</span><span class="sxs-lookup"><span data-stu-id="d3162-128">The `binPath` value is the path to your app's executable, including the executable filename itself.</span></span>

  ![Fenêtre de console créer et démarrer l’exemple](windows-service/_static/create-start.png)

  <span data-ttu-id="d3162-130">Lorsque ces commandes terminé, vous pouvez accéder à la même chemin d’accès lorsque vous exécutez en tant qu’une application console (par défaut, `http://localhost:5000`)</span><span class="sxs-lookup"><span data-stu-id="d3162-130">When these commands finish, you can browse to the same path as when you run as a console app (by default, `http://localhost:5000`)</span></span>

  ![En cours d’exécution dans un service](windows-service/_static/running-in-service.png)


## <a name="provide-a-way-to-run-outside-of-a-service"></a><span data-ttu-id="d3162-132">Fournir un moyen d’exécuter en dehors d’un service</span><span class="sxs-lookup"><span data-stu-id="d3162-132">Provide a way to run outside of a service</span></span>

<span data-ttu-id="d3162-133">Il est plus facile tester et déboguer lors de l’exécution en dehors d’un service, il est habituel pour ajouter du code qui appelle `host.RunAsService` uniquement sous certaines conditions.</span><span class="sxs-lookup"><span data-stu-id="d3162-133">It's easier to test and debug when you're running outside of a service, so it's customary to add code that calls `host.RunAsService` only under certain conditions.</span></span>  <span data-ttu-id="d3162-134">Par exemple, vous pouvez exécuter en tant qu’une application console si vous obtenez un `--console` argument de ligne de commande ou si le débogueur est attaché.</span><span class="sxs-lookup"><span data-stu-id="d3162-134">For example, you could run as a console app if you get a `--console` command-line argument or if the debugger is attached.</span></span>

[!code-csharp[](windows-service/sample/Program.cs?name=ServiceOrConsole)]

## <a name="handle-stopping-and-starting-events"></a><span data-ttu-id="d3162-135">Gérer les événements de démarrage et arrêt</span><span class="sxs-lookup"><span data-stu-id="d3162-135">Handle stopping and starting events</span></span>

<span data-ttu-id="d3162-136">Si vous souhaitez gérer `OnStarting`, `OnStarted`, et `OnStopping` événements, apporter les modifications supplémentaires suivantes :</span><span class="sxs-lookup"><span data-stu-id="d3162-136">If you want to handle `OnStarting`, `OnStarted`, and `OnStopping` events, make the following additional changes:</span></span>

* <span data-ttu-id="d3162-137">Créez une classe qui dérive de `WebHostService`.</span><span class="sxs-lookup"><span data-stu-id="d3162-137">Create a class that derives from `WebHostService`.</span></span>

  [!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=NoLogging)]

* <span data-ttu-id="d3162-138">Créer une méthode d’extension pour `IWebHost` qui transmet vos `WebHostService` à `ServiceBase.Run`.</span><span class="sxs-lookup"><span data-stu-id="d3162-138">Create an extension method for `IWebHost` that passes your custom `WebHostService` to `ServiceBase.Run`.</span></span>

  [!code-csharp[](windows-service/sample/WebHostServiceExtensions.cs?name=ExtensionsClass)]

* <span data-ttu-id="d3162-139">Dans `Program.Main` modification appeler la nouvelle méthode d’extension à la place de `host.RunAsService`.</span><span class="sxs-lookup"><span data-stu-id="d3162-139">In `Program.Main` change call the new extension method instead of `host.RunAsService`.</span></span>

  [!code-csharp[](windows-service/sample/Program.cs?name=HandleStopStart&highlight=26)]

<span data-ttu-id="d3162-140">Si votre personnalisé `WebHostService` code a besoin d’obtenir un service à partir de l’injection de dépendances (par exemple, un journal), vous pouvez l’obtenir à partir de la `Services` propriété du `IWebHost`.</span><span class="sxs-lookup"><span data-stu-id="d3162-140">If your custom `WebHostService` code needs to get a service from dependency injection (such as a logger), you can get it from the `Services` property of `IWebHost`.</span></span>

[!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=Logging&highlight=7)]

## <a name="next-steps"></a><span data-ttu-id="d3162-141">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="d3162-141">Next steps</span></span>

<span data-ttu-id="d3162-142">Le [exemple d’application](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample) qui accompagne cette article est une application web MVC simple qui a été modifiée comme indiqué dans les précédents exemples de code.</span><span class="sxs-lookup"><span data-stu-id="d3162-142">The [sample application](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample) that accompanies this article is a simple MVC web app that has been modified as shown in preceding code examples.</span></span>  <span data-ttu-id="d3162-143">Pour l’exécuter dans un service, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="d3162-143">To run it in a service, do the following steps:</span></span>

* <span data-ttu-id="d3162-144">Publier sur *c:\svc*.</span><span class="sxs-lookup"><span data-stu-id="d3162-144">Publish to *c:\svc*.</span></span>

* <span data-ttu-id="d3162-145">Ouvrez une fenêtre de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="d3162-145">Open an administrator window.</span></span>

* <span data-ttu-id="d3162-146">Entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="d3162-146">Enter the following commands:</span></span>

  ```console
  sc create MyService binPath="c:\svc\aspnetcoreservice.exe"
  sc start MyService
  ```

  * <span data-ttu-id="d3162-147">Dans un navigateur, accédez à http://localhost : 5000 pour vérifier qu’il s’exécute.</span><span class="sxs-lookup"><span data-stu-id="d3162-147">In a browser, go to http://localhost:5000 to verify that it's running.</span></span>

<span data-ttu-id="d3162-148">Si l’application ne démarre pas comme prévu lors de l’exécution dans un service, un moyen rapide pour rendre les messages d’erreur accessible est pour ajouter un module fournisseur d’informations telles que la [fournisseur du journal des événements Windows](xref:fundamentals/logging#eventlog).</span><span class="sxs-lookup"><span data-stu-id="d3162-148">If the app doesn't start up as expected when running in a service, a quick way to make error messages accessible is to add a logging provider such as the [Windows EventLog provider](xref:fundamentals/logging#eventlog).</span></span>

## <a name="acknowledgments"></a><span data-ttu-id="d3162-149">Accusés de réception</span><span class="sxs-lookup"><span data-stu-id="d3162-149">Acknowledgments</span></span>

<span data-ttu-id="d3162-150">Cet article a été écrit à l’aide de sources qui ont été déjà publiés.</span><span class="sxs-lookup"><span data-stu-id="d3162-150">This article was written with the help of sources that were already published.</span></span> <span data-ttu-id="d3162-151">Au plus tôt et plus utiles d'entre eux ont été ceux-ci :</span><span class="sxs-lookup"><span data-stu-id="d3162-151">The earliest and most useful of them were these:</span></span>

* [<span data-ttu-id="d3162-152">Hébergement ASP.NET Core en tant que service Windows</span><span class="sxs-lookup"><span data-stu-id="d3162-152">Hosting ASP.NET Core as Windows service</span></span>](http://stackoverflow.com/questions/37346383/hosting-asp-net-core-as-windows-service/37464074#37464074)
* [<span data-ttu-id="d3162-153">L’hébergement de base ASP.NET dans un Service Windows</span><span class="sxs-lookup"><span data-stu-id="d3162-153">How to host your ASP.NET Core in a Windows Service</span></span>](http://dotnetthoughts.net/how-to-host-your-aspnet-core-in-a-windows-service/)

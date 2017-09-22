---
title: Journalisation dans ASP.NET Core
author: ardalis
description: "Présente les fonctionnalités de journalisation dans ASP.NET Core. Inclut une section pour chaque fournisseur de journalisation intégrés et des liens vers des fournisseurs tiers populaires."
keywords: ASP.NET Core, la journalisation, providers,Microsoft.Extensions.Logging,ILogger,ILoggerFactory,LogLevel,WithFilter,TraceSource,EventLog,EventSource,scopes de journalisation
ms.author: tdykstra
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ac27ac68-d76a-4f8e-b8ab-ea045803e5f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/logging
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca81f01fe1c5026514eafedf852b4bc8f3b6fd21
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-logging-in-aspnet-core"></a><span data-ttu-id="351b4-105">Introduction à la journalisation dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="351b4-105">Introduction to Logging in ASP.NET Core</span></span>

<span data-ttu-id="351b4-106">Par [Steve Smith](https://ardalis.com/) et [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="351b4-106">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="351b4-107">ASP.NET Core prend en charge une API de journalisation qui fonctionne avec une gamme de fournisseurs de journalisation.</span><span class="sxs-lookup"><span data-stu-id="351b4-107">ASP.NET Core supports a logging API that works with a variety of logging providers.</span></span> <span data-ttu-id="351b4-108">Les fournisseurs intégrés vous permettent d’envoyer les journaux à une ou plusieurs destinations, et vous pouvez incorporer dans une infrastructure de journalisation de l’application tierce.</span><span class="sxs-lookup"><span data-stu-id="351b4-108">Built-in providers let you send logs to one or more destinations, and you can plug in a third-party logging framework.</span></span> <span data-ttu-id="351b4-109">Cet article explique comment utiliser les API de journalisation intégrés et les fournisseurs dans votre code.</span><span class="sxs-lookup"><span data-stu-id="351b4-109">This article shows how to use the built-in logging API and providers in your code.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="351b4-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="351b4-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[<span data-ttu-id="351b4-111">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="351b4-111">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample2)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="351b4-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="351b4-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[<span data-ttu-id="351b4-113">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="351b4-113">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample)

---

## <a name="how-to-create-logs"></a><span data-ttu-id="351b4-114">Comment créer des journaux</span><span class="sxs-lookup"><span data-stu-id="351b4-114">How to create logs</span></span>

<span data-ttu-id="351b4-115">Pour créer des journaux, vous devez obtenir un `ILogger` de l’objet à partir de la [injection de dépendance](dependency-injection.md) conteneur :</span><span class="sxs-lookup"><span data-stu-id="351b4-115">To create logs, get an `ILogger` object from the [dependency injection](dependency-injection.md) container:</span></span>

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="351b4-116">Puis appelez les méthodes de journalisation sur cet objet de l’enregistreur d’événements :</span><span class="sxs-lookup"><span data-stu-id="351b4-116">Then call logging methods on that logger object:</span></span>

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="351b4-117">Cet exemple crée des journaux avec le `TodoController` classe en tant que le *catégorie*.</span><span class="sxs-lookup"><span data-stu-id="351b4-117">This example creates logs with the `TodoController` class as the *category*.</span></span>  <span data-ttu-id="351b4-118">Catégories sont expliquées [plus loin dans cet article](#log-category).</span><span class="sxs-lookup"><span data-stu-id="351b4-118">Categories are explained [later in this article](#log-category).</span></span>

<span data-ttu-id="351b4-119">ASP.NET Core ne fournit pas asynchrone des méthodes de journalisation, car la journalisation doit être donc rapide, il n’est pas important du coût d’utilisation d’async.</span><span class="sxs-lookup"><span data-stu-id="351b4-119">ASP.NET Core does not provide async logger methods because logging should be so fast that it isn't worth the cost of using async.</span></span> <span data-ttu-id="351b4-120">Si vous êtes dans une situation où qui n’est pas vrai, pensez à la manière de que vous connecter.</span><span class="sxs-lookup"><span data-stu-id="351b4-120">If you're in a situation where that's not true, consider changing the way you log.</span></span>  <span data-ttu-id="351b4-121">Si votre banque de données est lente, écrire les messages du journal dans un magasin rapide tout d’abord, puis les déplacer vers un magasin lent ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="351b4-121">If your data store is slow, write the log messages to a fast store first, then move them to a slow store later.</span></span> <span data-ttu-id="351b4-122">Par exemple, connectez-vous à une file d’attente qui est lues et conservé dans le stockage lent par un autre processus.</span><span class="sxs-lookup"><span data-stu-id="351b4-122">For example, log to a message queue that is read and persisted to slow storage by another process.</span></span>

## <a name="how-to-add-providers"></a><span data-ttu-id="351b4-123">Comment ajouter des fournisseurs</span><span class="sxs-lookup"><span data-stu-id="351b4-123">How to add providers</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="351b4-124">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="351b4-124">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="351b4-125">Un fournisseur de journalisation extrait les messages que vous créez avec un `ILogger` de l’objet et affiche les stocke.</span><span class="sxs-lookup"><span data-stu-id="351b4-125">A logging provider takes the messages that you create with an `ILogger` object and displays or stores them.</span></span> <span data-ttu-id="351b4-126">Par exemple, le fournisseur de la Console affiche des messages sur la console, et le fournisseur de services d’application Azure de les stocker dans le stockage blob Azure.</span><span class="sxs-lookup"><span data-stu-id="351b4-126">For example, the Console provider displays messages on the console, and the Azure App Service provider can store them in Azure blob storage.</span></span>

<span data-ttu-id="351b4-127">Pour utiliser un fournisseur, appelez du fournisseur `Add<ProviderName>` méthode d’extension dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="351b4-127">To use a provider, call the provider's `Add<ProviderName>` extension method in *Program.cs*:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=16,17)]

<span data-ttu-id="351b4-128">Le modèle de projet par défaut configure de la journalisation de la façon de vous voir dans le code précédent, mais le `ConfigureLogging` appel est effectué le `CreateDefaultBuilder` (méthode).</span><span class="sxs-lookup"><span data-stu-id="351b4-128">The default project template sets up logging the way you see it in the preceding code, but the `ConfigureLogging` call is done by the `CreateDefaultBuilder` method.</span></span> <span data-ttu-id="351b4-129">Voici le code *Program.cs* qui est créé par les modèles de projet :</span><span class="sxs-lookup"><span data-stu-id="351b4-129">Here's the code in *Program.cs* that is created by project templates:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_TemplateCode&highlight=7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="351b4-130">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="351b4-130">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="351b4-131">Un fournisseur de journalisation extrait les messages que vous créez avec un `ILogger` de l’objet et affiche les stocke.</span><span class="sxs-lookup"><span data-stu-id="351b4-131">A logging provider takes the messages that you create with an `ILogger` object and displays or stores them.</span></span> <span data-ttu-id="351b4-132">Par exemple, le fournisseur de la Console affiche des messages sur la console, et le fournisseur de services d’application Azure de les stocker dans le stockage blob Azure.</span><span class="sxs-lookup"><span data-stu-id="351b4-132">For example, the Console provider displays messages on the console, and the Azure App Service provider can store them in Azure blob storage.</span></span>

<span data-ttu-id="351b4-133">Pour utiliser un fournisseur, installez le package NuGet et appeler la méthode d’extension du fournisseur sur une instance de `ILoggerFactory`, comme illustré dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="351b4-133">To use a provider, install its NuGet package and call the provider's extension method on an instance of `ILoggerFactory`, as shown in the following example.</span></span>

[!code-csharp[](logging/sample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

<span data-ttu-id="351b4-134">ASP.NET Core [injection de dépendance](dependency-injection.md) (DI) fournit le `ILoggerFactory` instance.</span><span class="sxs-lookup"><span data-stu-id="351b4-134">ASP.NET Core [dependency injection](dependency-injection.md) (DI) provides the `ILoggerFactory` instance.</span></span> <span data-ttu-id="351b4-135">Le `AddConsole` et `AddDebug` méthodes d’extension sont définies dans le [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) et [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span><span class="sxs-lookup"><span data-stu-id="351b4-135">The `AddConsole` and `AddDebug` extension methods are defined in the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) and [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span></span> <span data-ttu-id="351b4-136">Chaque méthode d’extension appelle la `ILoggerFactory.AddProvider` méthode, en passant une instance du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="351b4-136">Each extension method calls the `ILoggerFactory.AddProvider` method, passing in an instance of the provider.</span></span> 

> [!NOTE]
> <span data-ttu-id="351b4-137">L’exemple d’application pour cet article ajoute des fournisseurs de journalisation dans le `Configure` méthode de la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="351b4-137">The sample application for this article adds logging providers in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="351b4-138">Si vous souhaitez obtenir la sortie du journal à partir du code qui s’exécute plus tôt, ajouter des fournisseurs de journalisation dans le `Startup` constructeur de classe à la place.</span><span class="sxs-lookup"><span data-stu-id="351b4-138">If you want to get log output from code that executes earlier, add logging providers in the `Startup` class constructor instead.</span></span> 

---

<span data-ttu-id="351b4-139">Vous trouverez des informations sur chacune d’elles [fournisseur de journalisation intégrés](#built-in-logging-providers) et des liens vers [modules fournisseurs d’informations de tiers](#third-party-logging-providers) plus loin dans l’article.</span><span class="sxs-lookup"><span data-stu-id="351b4-139">You'll find information about each [built-in logging provider](#built-in-logging-providers) and links to [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="351b4-140">Exemple de sortie de journalisation</span><span class="sxs-lookup"><span data-stu-id="351b4-140">Sample logging output</span></span>

<span data-ttu-id="351b4-141">Avec l’exemple de code indiqué dans la section précédente, vous verrez des journaux dans la console lorsque vous exécutez à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="351b4-141">With the sample code shown in the preceding section, you'll see logs in the console when you run from the command line.</span></span> <span data-ttu-id="351b4-142">Voici un exemple de sortie de la console :</span><span class="sxs-lookup"><span data-stu-id="351b4-142">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```
 
<span data-ttu-id="351b4-143">Ces journaux ont été créés en accédant à `http://localhost:5000/api/todo/0`, ce qui déclenche l’exécution des deux `ILogger` appels indiqués dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="351b4-143">These logs were created by going to `http://localhost:5000/api/todo/0`, which triggers execution of both `ILogger` calls shown in the preceding section.</span></span>

<span data-ttu-id="351b4-144">Voici un exemple des mêmes journaux telles qu’elles apparaissent dans la fenêtre de débogage lorsque vous exécutez l’exemple d’application dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="351b4-144">Here's an example of the same logs as they appear in the Debug window when you run the sample application in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404 
```

<span data-ttu-id="351b4-145">Les fichiers journaux qui ont été créés par le `ILogger` appels indiqués dans la section précédente commencent par « TodoApi.Controllers.TodoController ».</span><span class="sxs-lookup"><span data-stu-id="351b4-145">The logs that were created by the `ILogger` calls shown in the preceding section begin with "TodoApi.Controllers.TodoController".</span></span> <span data-ttu-id="351b4-146">Les fichiers journaux qui commencent par catégories de « Microsoft » sont à partir de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="351b4-146">The logs that begin with "Microsoft" categories are from ASP.NET Core.</span></span> <span data-ttu-id="351b4-147">ASP.NET Core lui-même et votre code d’application sont à l’aide de la même API de journalisation et les mêmes fournisseurs de journalisation.</span><span class="sxs-lookup"><span data-stu-id="351b4-147">ASP.NET Core itself and your application code are using the same logging API and the same logging providers.</span></span>

<span data-ttu-id="351b4-148">Le reste de cet article explique certains détails et les options de journalisation.</span><span class="sxs-lookup"><span data-stu-id="351b4-148">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="351b4-149">Packages NuGet</span><span class="sxs-lookup"><span data-stu-id="351b4-149">NuGet packages</span></span>

<span data-ttu-id="351b4-150">Le `ILogger` et `ILoggerFactory` sont des interfaces dans [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), et les implémentations par défaut pour les [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/).</span><span class="sxs-lookup"><span data-stu-id="351b4-150">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="351b4-151">Catégorie de journaux</span><span class="sxs-lookup"><span data-stu-id="351b4-151">Log category</span></span>

<span data-ttu-id="351b4-152">A *catégorie* est inclus dans chaque journal que vous créez.</span><span class="sxs-lookup"><span data-stu-id="351b4-152">A *category* is included with each log that you create.</span></span>  <span data-ttu-id="351b4-153">Vous spécifiez la catégorie lorsque vous créez un `ILogger` objet.</span><span class="sxs-lookup"><span data-stu-id="351b4-153">You specify the category when you create an `ILogger` object.</span></span> <span data-ttu-id="351b4-154">La catégorie peut être n’importe quelle chaîne, mais une convention est d’utiliser le nom qualifié complet de la classe à partir de laquelle les journaux sont écrits.</span><span class="sxs-lookup"><span data-stu-id="351b4-154">The category may be any string, but a convention is to use the fully qualified name of the class from which the logs are written.</span></span>  <span data-ttu-id="351b4-155">Par exemple : « TodoApi.Controllers.TodoController ».</span><span class="sxs-lookup"><span data-stu-id="351b4-155">For example: "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="351b4-156">Vous pouvez spécifier la catégorie sous forme de chaîne ou utiliser une méthode d’extension qui dérive de la catégorie du type.</span><span class="sxs-lookup"><span data-stu-id="351b4-156">You can specify the category as a string or use an extension method that derives the category from the type.</span></span> <span data-ttu-id="351b4-157">Pour spécifier la catégorie sous forme de chaîne, appelez `CreateLogger` sur une `ILoggerFactory` de l’instance, comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="351b4-157">To specify the category as a string, call `CreateLogger` on an `ILoggerFactory` instance, as shown below.</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="351b4-158">La plupart du temps, il sera plus facile à utiliser `ILogger<T>`, comme dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="351b4-158">Most of the time, it will be easier to use  `ILogger<T>`, as in the following example.</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="351b4-159">Cela équivaut à appeler la méthode `CreateLogger` avec le nom de type qualifié complet `T`.</span><span class="sxs-lookup"><span data-stu-id="351b4-159">This is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="351b4-160">Niveau du journal</span><span class="sxs-lookup"><span data-stu-id="351b4-160">Log level</span></span>

<span data-ttu-id="351b4-161">Chaque fois que vous écrivez un journal, spécifiez son [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="351b4-161">Each time you write a log, you specify its [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="351b4-162">Le niveau de journal indique le degré de gravité ou l’importance.</span><span class="sxs-lookup"><span data-stu-id="351b4-162">The log level indicates the degree of severity or importance.</span></span>  <span data-ttu-id="351b4-163">Par exemple, vous pouvez écrire un `Information` journal lorsqu’une méthode se termine normalement, un `Warning` lorsqu’une méthode retourne une erreur 404 code de retour et un journal `Error` ouvrir une session lorsque vous interceptez une exception inattendue.</span><span class="sxs-lookup"><span data-stu-id="351b4-163">For example, you might write an `Information` log when a method ends normally, a `Warning` log when a method returns a 404 return code, and an `Error` log when you catch an unexpected exception.</span></span>

<span data-ttu-id="351b4-164">Dans l’exemple de code suivant, les noms des méthodes (par exemple, `LogWarning`) spécifier le niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="351b4-164">In the following code example, the names of the methods (for example, `LogWarning`) specify the log level.</span></span>  <span data-ttu-id="351b4-165">Le premier paramètre est la [connecter l’ID d’événement](#log-event-id) (décrite plus loin dans cet article).</span><span class="sxs-lookup"><span data-stu-id="351b4-165">The first parameter is the [Log event ID](#log-event-id) (explained later in this article).</span></span>  <span data-ttu-id="351b4-166">Les paramètres restants de construire une chaîne de message de journal.</span><span class="sxs-lookup"><span data-stu-id="351b4-166">The remaining parameters construct a log message string.</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="351b4-167">Les méthodes de journal qui incluent le niveau dans le nom de la méthode sont [méthodes d’extension pour ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions).</span><span class="sxs-lookup"><span data-stu-id="351b4-167">Log methods that include the level in the method name are [extension methods for ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions).</span></span> <span data-ttu-id="351b4-168">En arrière-plan, ces méthodes appellent un `Log` méthode qui accepte un `LogLevel` paramètre.</span><span class="sxs-lookup"><span data-stu-id="351b4-168">Behind the scenes, these methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="351b4-169">Vous pouvez appeler la `Log` méthode directement au lieu de ces méthodes d’extension, mais la syntaxe est relativement complexe.</span><span class="sxs-lookup"><span data-stu-id="351b4-169">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="351b4-170">Pour plus d’informations, consultez la [ILogger interface](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) et [extensions de l’enregistreur de code source](https://github.com/aspnet/Logging/blob/master/src/Microsoft.Extensions.Logging.Abstractions/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="351b4-170">For more information, see the [ILogger interface](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) and the [logger extensions source code](https://github.com/aspnet/Logging/blob/master/src/Microsoft.Extensions.Logging.Abstractions/LoggerExtensions.cs).</span></span>

<span data-ttu-id="351b4-171">ASP.NET Core définit les éléments suivants [niveaux de journal](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel), ordonnés ici de gravité le plus élevée au moins.</span><span class="sxs-lookup"><span data-stu-id="351b4-171">ASP.NET Core defines the following [log levels](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel), ordered here from least to highest severity.</span></span>

* <span data-ttu-id="351b4-172">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="351b4-172">Trace = 0</span></span>

  <span data-ttu-id="351b4-173">Pour plus d’informations qui est utiles uniquement pour un développeur de débogage d’un problème.</span><span class="sxs-lookup"><span data-stu-id="351b4-173">For information that is valuable only to a developer debugging an issue.</span></span> <span data-ttu-id="351b4-174">Ces messages peuvent contenir des données d’application sensibles et ne doivent donc pas être activées dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="351b4-174">These messages may contain sensitive application data and so should not be enabled in a production environment.</span></span> <span data-ttu-id="351b4-175">*Désactivé par défaut.*</span><span class="sxs-lookup"><span data-stu-id="351b4-175">*Disabled by default.*</span></span> <span data-ttu-id="351b4-176">Exemple : `Credentials: {"User":"someuser", "Password":"P@ssword"}`</span><span class="sxs-lookup"><span data-stu-id="351b4-176">Example: `Credentials: {"User":"someuser", "Password":"P@ssword"}`</span></span>

* <span data-ttu-id="351b4-177">Déboguer = 1</span><span class="sxs-lookup"><span data-stu-id="351b4-177">Debug = 1</span></span>

  <span data-ttu-id="351b4-178">Pour plus d’informations ayant une utilité à court terme pendant le développement et le débogage.</span><span class="sxs-lookup"><span data-stu-id="351b4-178">For information that has short-term usefulness during development and debugging.</span></span> <span data-ttu-id="351b4-179">Exemple : `Entering method Configure with flag set to true.` vous généralement ne permettrait pas `Debug` niveau se connecte en production, sauf si vous dépannez, en raison du volume élevé de journaux.</span><span class="sxs-lookup"><span data-stu-id="351b4-179">Example: `Entering method Configure with flag set to true.` You typically would not enable `Debug` level logs in production unless you are troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="351b4-180">Informations = 2</span><span class="sxs-lookup"><span data-stu-id="351b4-180">Information = 2</span></span>

  <span data-ttu-id="351b4-181">Pour le suivi du flux général de l’application.</span><span class="sxs-lookup"><span data-stu-id="351b4-181">For tracking the general flow of the application.</span></span> <span data-ttu-id="351b4-182">Ces journaux ont généralement une valeur à long terme.</span><span class="sxs-lookup"><span data-stu-id="351b4-182">These logs typically have some long-term value.</span></span> <span data-ttu-id="351b4-183">Exemple : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="351b4-183">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="351b4-184">Avertissement = 3</span><span class="sxs-lookup"><span data-stu-id="351b4-184">Warning = 3</span></span>

  <span data-ttu-id="351b4-185">Pour les événements anormaux ou inattendues dans le flux d’application.</span><span class="sxs-lookup"><span data-stu-id="351b4-185">For abnormal or unexpected events in the application flow.</span></span> <span data-ttu-id="351b4-186">Il peut s’agir d’erreurs ou autres conditions qui ne provoquent pas l’arrêt de l’application, mais qui peut être nécessaire d’envisager.</span><span class="sxs-lookup"><span data-stu-id="351b4-186">These may include errors or other conditions that do not cause the application to stop, but which may need to be investigated.</span></span> <span data-ttu-id="351b4-187">Des exceptions sont un emplacement commun à utiliser le `Warning` niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="351b4-187">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="351b4-188">Exemple : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="351b4-188">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="351b4-189">Erreur = 4</span><span class="sxs-lookup"><span data-stu-id="351b4-189">Error = 4</span></span>

  <span data-ttu-id="351b4-190">Pour les erreurs et exceptions qui ne peuvent pas être gérées.</span><span class="sxs-lookup"><span data-stu-id="351b4-190">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="351b4-191">Ces messages indiquent un échec de l’activité actuelle ou l’opération (par exemple, la requête HTTP actuelle), pas un échec de l’application.</span><span class="sxs-lookup"><span data-stu-id="351b4-191">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an application-wide failure.</span></span> <span data-ttu-id="351b4-192">Exemple de message de journal :`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="351b4-192">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="351b4-193">Critique = 5</span><span class="sxs-lookup"><span data-stu-id="351b4-193">Critical = 5</span></span>

  <span data-ttu-id="351b4-194">Pour les défaillances qui nécessitent une attention immédiate.</span><span class="sxs-lookup"><span data-stu-id="351b4-194">For failures that require immediate attention.</span></span> <span data-ttu-id="351b4-195">Exemples : perte de données, espace disque insuffisant.</span><span class="sxs-lookup"><span data-stu-id="351b4-195">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="351b4-196">Vous pouvez utiliser le niveau de journal pour contrôler la quantité sortie du journal est écrit dans un support de stockage particulier ou fenêtre d’affichage.</span><span class="sxs-lookup"><span data-stu-id="351b4-196">You can use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="351b4-197">Par exemple, dans la production vous souhaiterez tous les journaux de `Information` niveau et vers le bas pour accéder à un magasin de données de volume et tous les journaux de `Warning` niveau et versions ultérieures pour accéder à un magasin de données de valeur.</span><span class="sxs-lookup"><span data-stu-id="351b4-197">For example, in production you might want all logs of `Information` level and lower to go to a volume data store, and all logs of `Warning` level and higher to go to a value data store.</span></span> <span data-ttu-id="351b4-198">Pendant le développement, vous pouvez normalement envoyer des journaux de `Warning` ou la gravité supérieure à la console.</span><span class="sxs-lookup"><span data-stu-id="351b4-198">During development, you might normally send logs of `Warning` or higher severity to the console.</span></span> <span data-ttu-id="351b4-199">Lorsque vous avez besoin résoudre les problèmes, vous pouvez alors ajouter `Debug` niveau.</span><span class="sxs-lookup"><span data-stu-id="351b4-199">Then when you need to troubleshoot, you can add `Debug` level.</span></span> <span data-ttu-id="351b4-200">Le [filtrage de journal](#log-filtering) section plus loin dans cet article explique comment contrôler les niveaux de journalisation d’un fournisseur gère.</span><span class="sxs-lookup"><span data-stu-id="351b4-200">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="351b4-201">L’infrastructure ASP.NET Core écrit `Debug` niveau des journaux des événements de framework.</span><span class="sxs-lookup"><span data-stu-id="351b4-201">The ASP.NET Core framework writes `Debug` level logs for framework events.</span></span> <span data-ttu-id="351b4-202">Les exemples de journal plus haut dans cet article exclus journaux ci-dessous `Information` niveau, par conséquent, aucune `Debug` niveau journaux ont été affichés.</span><span class="sxs-lookup"><span data-stu-id="351b4-202">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` level logs were shown.</span></span> <span data-ttu-id="351b4-203">Voici un exemple de journaux de la console si vous exécutez l’exemple d’application configuré pour afficher `Debug` et journaux plus élevées pour le fournisseur de la console.</span><span class="sxs-lookup"><span data-stu-id="351b4-203">Here's an example of console logs if you run the sample application configured to show `Debug` and higher logs for the console provider.</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="351b4-204">ID d’événement de journal</span><span class="sxs-lookup"><span data-stu-id="351b4-204">Log event ID</span></span>

<span data-ttu-id="351b4-205">Chaque fois que vous écrivez un journal, vous pouvez spécifier une *ID d’événement*.</span><span class="sxs-lookup"><span data-stu-id="351b4-205">Each time you write a log, you can specify an *event ID*.</span></span> <span data-ttu-id="351b4-206">L’exemple d’application pour cela, à l’aide de-définies localement `LoggingEvents` classe :</span><span class="sxs-lookup"><span data-stu-id="351b4-206">The sample app does this by using a locally-defined `LoggingEvents` class:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](logging/sample//Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="351b4-207">Un ID d’événement est une valeur entière qui vous permet d’associer un ensemble d’événements enregistrés avec l’autre.</span><span class="sxs-lookup"><span data-stu-id="351b4-207">An event ID is an integer value that you can use to associate a set of logged events with one another.</span></span> <span data-ttu-id="351b4-208">Par exemple, un journal pour l’ajout d’un élément dans un panier d’achat peut être l’ID d’événement 1000 et un journal d’exécution d’un fournisseur peut être l’événement ID 1001.</span><span class="sxs-lookup"><span data-stu-id="351b4-208">For instance, a log for adding an item to a shopping cart could be event ID 1000 and a log for completing a purchase could be event ID 1001.</span></span>

<span data-ttu-id="351b4-209">Dans la sortie de journalisation, l’ID d’événement peut-être être stocké dans un champ ou inclus dans le message texte, selon le fournisseur.</span><span class="sxs-lookup"><span data-stu-id="351b4-209">In logging output, the event ID may be stored in a field or included in the text message, depending on the provider.</span></span>  <span data-ttu-id="351b4-210">Le fournisseur de débogage n’affiche pas les ID d’événement, mais le fournisseur de console présente les crochets après la catégorie :</span><span class="sxs-lookup"><span data-stu-id="351b4-210">The Debug provider doesn't show event IDs, but the console provider shows them in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-format-string"></a><span data-ttu-id="351b4-211">Chaîne de format de message de journal</span><span class="sxs-lookup"><span data-stu-id="351b4-211">Log message format string</span></span>

<span data-ttu-id="351b4-212">Chaque fois que vous écrivez un journal, vous fournissez un message texte.</span><span class="sxs-lookup"><span data-stu-id="351b4-212">Each time you write a log, you provide a text message.</span></span> <span data-ttu-id="351b4-213">La chaîne de message peut contenir des espaces réservés nommés, dans l’argument qui sont placées les valeurs, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="351b4-213">The message string can contain named placeholders into which argument values are placed, as in the following example:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="351b4-214">L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour eux.</span><span class="sxs-lookup"><span data-stu-id="351b4-214">The order of placeholders, not their names, determines which parameters are used for them.</span></span> <span data-ttu-id="351b4-215">Par exemple, si vous avez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="351b4-215">For example, if you have the following code:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="351b4-216">Le message du journal résultante ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="351b4-216">The resulting log message would look like this:</span></span>

```
Parameter values: parm1, parm2
```

<span data-ttu-id="351b4-217">L’infrastructure de journalisation de message de cette façon à permettre aux fournisseurs de journalisation implémenter la mise en forme [journalisation sémantique, également appelée enregistrement structuré](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="351b4-217">The logging framework does message formatting in this way to make it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="351b4-218">Étant donné que les arguments eux-mêmes sont transmis au système de journalisation, pas seulement la chaîne de message mis en forme, les fournisseurs de journalisation peuvent stocker les valeurs de paramètre en tant que champs en plus de la chaîne de message.</span><span class="sxs-lookup"><span data-stu-id="351b4-218">Because the arguments themselves are passed to the logging system, not just the formatted message string, logging providers can store the parameter values as fields in addition to the message string.</span></span> <span data-ttu-id="351b4-219">Par exemple, si vous envoyez votre journal de sortie pour le stockage de Table Azure et votre appel de méthode enregistreur d’événements ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="351b4-219">For example, if you are directing your log output to Azure Table Storage, and your logger method call looks like this:</span></span>

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="351b4-220">Chaque entité de Table Azure peut avoir `ID` et `RequestTime` propriétés, qui seraient simplifient les requêtes sur les données du journal.</span><span class="sxs-lookup"><span data-stu-id="351b4-220">Each Azure Table entity could have `ID` and `RequestTime` properties, which would simplify queries on log data.</span></span> <span data-ttu-id="351b4-221">Vous pouvez rechercher tous les journaux dans un particulier `RequestTime` plage, sans avoir à analyser le délai d’expiration du message texte.</span><span class="sxs-lookup"><span data-stu-id="351b4-221">You could find all logs within a particular `RequestTime` range, without having to parse the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="351b4-222">Enregistrement des exceptions</span><span class="sxs-lookup"><span data-stu-id="351b4-222">Logging exceptions</span></span>

<span data-ttu-id="351b4-223">Les méthodes de l’enregistreur d’événements ont des surcharges qui vous permettent de passer d’une exception, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="351b4-223">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="351b4-224">Différents fournisseurs de gérer des informations sur l’exception de différentes façons.</span><span class="sxs-lookup"><span data-stu-id="351b4-224">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="351b4-225">Voici un exemple de sortie de fournisseur de débogage de code ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="351b4-225">Here's an example of Debug provider output from the code shown above.</span></span>

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="351b4-226">Filtrage de journal</span><span class="sxs-lookup"><span data-stu-id="351b4-226">Log filtering</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="351b4-227">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="351b4-227">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="351b4-228">Vous pouvez spécifier un niveau de journal minimale pour une catégorie et un fournisseur spécifique ou pour tous les fournisseurs ou toutes les catégories.</span><span class="sxs-lookup"><span data-stu-id="351b4-228">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span>  <span data-ttu-id="351b4-229">Tous les journaux sous le niveau minimal ne sont pas transmis à ce fournisseur, donc n’obtenir affichées ou stockées.</span><span class="sxs-lookup"><span data-stu-id="351b4-229">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span> 

<span data-ttu-id="351b4-230">Si vous souhaitez supprimer tous les journaux, vous pouvez spécifier `LogLevel.None` en tant que le niveau de journalisation minimale.</span><span class="sxs-lookup"><span data-stu-id="351b4-230">If you want to suppress all logs, you can specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="351b4-231">La valeur entière de `LogLevel.None` est 6, ce qui est supérieur à `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="351b4-231">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="351b4-232">**Créer des règles de filtre dans la configuration**</span><span class="sxs-lookup"><span data-stu-id="351b4-232">**Create filter rules in configuration**</span></span>

<span data-ttu-id="351b4-233">Les modèles de projet créent du code qui appelle `CreateDefaultBuilder` pour configurer la journalisation pour les fournisseurs de Console et de débogage.</span><span class="sxs-lookup"><span data-stu-id="351b4-233">The project templates create code that calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="351b4-234">Le `CreateDefaultBuilder` méthode définit également la journalisation pour rechercher la configuration dans un `Logging` section, à l’aide de code comme suit :</span><span class="sxs-lookup"><span data-stu-id="351b4-234">The `CreateDefaultBuilder` method also sets up logging to look for configuration in a `Logging` section, using code like the following:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=15)]

<span data-ttu-id="351b4-235">Les données de configuration spécifient des niveaux de journalisation minimale par fournisseur et par catégorie, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="351b4-235">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](logging/sample2/appsettings.json)]

<span data-ttu-id="351b4-236">Ce JSON crée les six règles de filtre, une pour le fournisseur de débogage, 4 pour le fournisseur de la Console et celle qui s’applique à tous les fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="351b4-236">This JSON creates six filter rules, one for the Debug provider, four for the Console provider, and one that applies to all providers.</span></span> <span data-ttu-id="351b4-237">Vous verrez plus tard comment une de ces règles est choisie pour chaque fournisseur lorsqu’un `ILogger` objet est créé.</span><span class="sxs-lookup"><span data-stu-id="351b4-237">You'll see later how just one of these rules is chosen for each provider when an `ILogger` object is created.</span></span>

<span data-ttu-id="351b4-238">**Règles de filtre dans le code**</span><span class="sxs-lookup"><span data-stu-id="351b4-238">**Filter rules in code**</span></span>

<span data-ttu-id="351b4-239">Vous pouvez enregistrer les règles de filtre dans le code, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="351b4-239">You can register filter rules in code, as shown in the following example:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="351b4-240">La seconde `AddFilter` Spécifie le fournisseur de débogage à l’aide de son nom de type.</span><span class="sxs-lookup"><span data-stu-id="351b4-240">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="351b4-241">Le premier `AddFilter` s’applique à tous les fournisseurs, car il ne spécifie pas un type de fournisseur.</span><span class="sxs-lookup"><span data-stu-id="351b4-241">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

<span data-ttu-id="351b4-242">**Fonctionnement des règles de filtrage sont appliquées.**</span><span class="sxs-lookup"><span data-stu-id="351b4-242">**How filtering rules are applied**</span></span>

<span data-ttu-id="351b4-243">Les données de configuration et le `AddFilter` code indiqué dans les exemples précédents créer les règles affichées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="351b4-243">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="351b4-244">Les six premiers proviennent de l’exemple de configuration et les deux derniers proviennent de l’exemple de code.</span><span class="sxs-lookup"><span data-stu-id="351b4-244">The first six come from the configuration example and the last two come from the code example.</span></span>

<span data-ttu-id="351b4-245">Nombre</span><span class="sxs-lookup"><span data-stu-id="351b4-245">Number</span></span>|<span data-ttu-id="351b4-246">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="351b4-246">Provider</span></span>|<span data-ttu-id="351b4-247">Catégories qui commencent par</span><span class="sxs-lookup"><span data-stu-id="351b4-247">Categories that begin with</span></span>|<span data-ttu-id="351b4-248">Niveau de journalisation minimale</span><span class="sxs-lookup"><span data-stu-id="351b4-248">Minimum log level</span></span>|
------|--------|--------------------------|-----------------|
<span data-ttu-id="351b4-249">1</span><span class="sxs-lookup"><span data-stu-id="351b4-249">1</span></span>|<span data-ttu-id="351b4-250">Déboguer</span><span class="sxs-lookup"><span data-stu-id="351b4-250">Debug</span></span>|<span data-ttu-id="351b4-251">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="351b4-251">All categories</span></span>|<span data-ttu-id="351b4-252">Information</span><span class="sxs-lookup"><span data-stu-id="351b4-252">Information</span></span>|
<span data-ttu-id="351b4-253">2</span><span class="sxs-lookup"><span data-stu-id="351b4-253">2</span></span>|<span data-ttu-id="351b4-254">Console</span><span class="sxs-lookup"><span data-stu-id="351b4-254">Console</span></span>|<span data-ttu-id="351b4-255">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="351b4-255">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span>|<span data-ttu-id="351b4-256">Warning</span><span class="sxs-lookup"><span data-stu-id="351b4-256">Warning</span></span>|
<span data-ttu-id="351b4-257">3</span><span class="sxs-lookup"><span data-stu-id="351b4-257">3</span></span>|<span data-ttu-id="351b4-258">Console</span><span class="sxs-lookup"><span data-stu-id="351b4-258">Console</span></span>|<span data-ttu-id="351b4-259">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="351b4-259">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>|<span data-ttu-id="351b4-260">Déboguer</span><span class="sxs-lookup"><span data-stu-id="351b4-260">Debug</span></span>|
<span data-ttu-id="351b4-261">4</span><span class="sxs-lookup"><span data-stu-id="351b4-261">4</span></span>|<span data-ttu-id="351b4-262">Console</span><span class="sxs-lookup"><span data-stu-id="351b4-262">Console</span></span>|<span data-ttu-id="351b4-263">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="351b4-263">Microsoft.AspNetCore.Mvc.Razor</span></span>|<span data-ttu-id="351b4-264">Erreur</span><span class="sxs-lookup"><span data-stu-id="351b4-264">Error</span></span>|
<span data-ttu-id="351b4-265">5</span><span class="sxs-lookup"><span data-stu-id="351b4-265">5</span></span>|<span data-ttu-id="351b4-266">Console</span><span class="sxs-lookup"><span data-stu-id="351b4-266">Console</span></span>|<span data-ttu-id="351b4-267">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="351b4-267">All categories</span></span>|<span data-ttu-id="351b4-268">Information</span><span class="sxs-lookup"><span data-stu-id="351b4-268">Information</span></span>|
<span data-ttu-id="351b4-269">6</span><span class="sxs-lookup"><span data-stu-id="351b4-269">6</span></span>|<span data-ttu-id="351b4-270">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="351b4-270">All providers</span></span>|<span data-ttu-id="351b4-271">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="351b4-271">All categories</span></span>|<span data-ttu-id="351b4-272">Déboguer</span><span class="sxs-lookup"><span data-stu-id="351b4-272">Debug</span></span>
<span data-ttu-id="351b4-273">7</span><span class="sxs-lookup"><span data-stu-id="351b4-273">7</span></span>|<span data-ttu-id="351b4-274">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="351b4-274">All providers</span></span>|<span data-ttu-id="351b4-275">Système</span><span class="sxs-lookup"><span data-stu-id="351b4-275">System</span></span>|<span data-ttu-id="351b4-276">Déboguer</span><span class="sxs-lookup"><span data-stu-id="351b4-276">Debug</span></span>
<span data-ttu-id="351b4-277">8</span><span class="sxs-lookup"><span data-stu-id="351b4-277">8</span></span>|<span data-ttu-id="351b4-278">Déboguer</span><span class="sxs-lookup"><span data-stu-id="351b4-278">Debug</span></span>|<span data-ttu-id="351b4-279">Microsoft</span><span class="sxs-lookup"><span data-stu-id="351b4-279">Microsoft</span></span>|<span data-ttu-id="351b4-280">Suivi</span><span class="sxs-lookup"><span data-stu-id="351b4-280">Trace</span></span>

<span data-ttu-id="351b4-281">Lorsque vous créez un `ILogger` objet à écrire des journaux, le `ILoggerFactory` objet sélectionne une règle unique par le fournisseur à appliquer à ce journal.</span><span class="sxs-lookup"><span data-stu-id="351b4-281">When you create an `ILogger` object to write logs with, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="351b4-282">Tous les messages rédigés par ce `ILogger` objet sont filtrées en fonction des règles sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="351b4-282">All messages written by that `ILogger` object are filtered based on the selected rules.</span></span> <span data-ttu-id="351b4-283">La règle la plus spécifique possible pour chaque paire de catégorie et le fournisseur est sélectionnée dans les règles disponibles.</span><span class="sxs-lookup"><span data-stu-id="351b4-283">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="351b4-284">L’algorithme suivant est utilisé pour chaque fournisseur lorsqu’un `ILogger` est créé pour une catégorie donnée :</span><span class="sxs-lookup"><span data-stu-id="351b4-284">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="351b4-285">Sélectionnez toutes les règles qui correspondent au fournisseur ou son alias.</span><span class="sxs-lookup"><span data-stu-id="351b4-285">Select all rules that match the provider or its alias.</span></span>  <span data-ttu-id="351b4-286">Si aucune n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.</span><span class="sxs-lookup"><span data-stu-id="351b4-286">If none are found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="351b4-287">À partir du résultat de l’étape précédente, sélectionnez des règles avec la plus longue correspondance de préfixe de catégorie.</span><span class="sxs-lookup"><span data-stu-id="351b4-287">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="351b4-288">Si aucune n’est trouvée, sélectionnez toutes les règles qui ne spécifient pas une catégorie.</span><span class="sxs-lookup"><span data-stu-id="351b4-288">If none are found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="351b4-289">Si plusieurs règles sont sélectionnées prendre le **dernière** une.</span><span class="sxs-lookup"><span data-stu-id="351b4-289">If multiple rules are selected take the **last** one.</span></span>
* <span data-ttu-id="351b4-290">Si aucune règle n’est sélectionné, utilisez `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="351b4-290">If no rules are selected, use `MinimumLevel`.</span></span>
 
<span data-ttu-id="351b4-291">Par exemple, supposons que vous disposez de la liste précédente des règles et que vous créez un `ILogger` objet pour la catégorie « Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine » :</span><span class="sxs-lookup"><span data-stu-id="351b4-291">For example, suppose you have the preceding list of rules and you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="351b4-292">Le fournisseur de débogage, les règles 1, 6 et 8 s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="351b4-292">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="351b4-293">Règle 8 est la plus spécifique, qui est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="351b4-293">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="351b4-294">Le fournisseur de la Console, les règles 3, 4, 5 et 6 s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="351b4-294">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="351b4-295">La règle 3 est plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="351b4-295">Rule 3 is most specific.</span></span>

<span data-ttu-id="351b4-296">Lorsque vous créez des journaux avec un `ILogger` pour la catégorie « Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine », les journaux de `Trace` niveau et ci-dessus passe auprès du fournisseur de débogage et les journaux de `Debug` niveau et ci-dessus passe au fournisseur de Console.</span><span class="sxs-lookup"><span data-stu-id="351b4-296">When you create logs with an `ILogger` for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine", logs of `Trace` level and above will go to the Debug provider, and logs of `Debug` level and above will go to the Console provider.</span></span>

<span data-ttu-id="351b4-297">**Alias de fournisseur**</span><span class="sxs-lookup"><span data-stu-id="351b4-297">**Provider aliases**</span></span>

<span data-ttu-id="351b4-298">Vous pouvez utiliser le nom de type pour spécifier un fournisseur de configuration, mais chaque fournisseur définit un plus court *alias* qui est plus facile à utiliser.</span><span class="sxs-lookup"><span data-stu-id="351b4-298">You can use the type name to specify a provider in configuration, but each provider defines a shorter *alias* that is easier to use.</span></span> <span data-ttu-id="351b4-299">Pour les fournisseurs intégrés, utilisez les alias suivants :</span><span class="sxs-lookup"><span data-stu-id="351b4-299">For the built-in providers, use the following aliases:</span></span>

- <span data-ttu-id="351b4-300">Console</span><span class="sxs-lookup"><span data-stu-id="351b4-300">Console</span></span>
- <span data-ttu-id="351b4-301">Déboguer</span><span class="sxs-lookup"><span data-stu-id="351b4-301">Debug</span></span>
- <span data-ttu-id="351b4-302">Journal des événements</span><span class="sxs-lookup"><span data-stu-id="351b4-302">EventLog</span></span>
- <span data-ttu-id="351b4-303">AzureAppServices</span><span class="sxs-lookup"><span data-stu-id="351b4-303">AzureAppServices</span></span>
- <span data-ttu-id="351b4-304">TraceSource</span><span class="sxs-lookup"><span data-stu-id="351b4-304">TraceSource</span></span>
- <span data-ttu-id="351b4-305">EventSource</span><span class="sxs-lookup"><span data-stu-id="351b4-305">EventSource</span></span>

<span data-ttu-id="351b4-306">**Niveau minimal de valeur par défaut**</span><span class="sxs-lookup"><span data-stu-id="351b4-306">**Default minimum level**</span></span>

<span data-ttu-id="351b4-307">Il existe un paramètre de niveau minimal qui entre en vigueur uniquement si aucune règle de configuration ou de code s’appliquent à une catégorie et un fournisseur donné.</span><span class="sxs-lookup"><span data-stu-id="351b4-307">There is a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="351b4-308">L’exemple suivant montre comment définir le niveau minimal :</span><span class="sxs-lookup"><span data-stu-id="351b4-308">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="351b4-309">Si vous ne définissez pas explicitement le niveau minimal, la valeur par défaut est `Information`, ce qui signifie que `Trace` et `Debug` journaux sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="351b4-309">IF you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

<span data-ttu-id="351b4-310">**Fonctions de filtrage**</span><span class="sxs-lookup"><span data-stu-id="351b4-310">**Filter functions**</span></span>

<span data-ttu-id="351b4-311">Vous pouvez écrire du code dans une fonction de filtre à appliquer les règles de filtrage.</span><span class="sxs-lookup"><span data-stu-id="351b4-311">You can write code in a filter function to apply filtering rules.</span></span> <span data-ttu-id="351b4-312">Une fonction de filtre est appelée pour tous les fournisseurs et les catégories qui n’ont pas de règles attribués par la configuration ou du code.</span><span class="sxs-lookup"><span data-stu-id="351b4-312">A filter function is invoked for all providers and categories that do not have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="351b4-313">Code de la fonction a accès à un type de fournisseur, catégorie et niveau de journal pour déterminer si un message doit être connecté.</span><span class="sxs-lookup"><span data-stu-id="351b4-313">Code in the function has access to the provider type, category, and log level to decide whether or not a message should be logged.</span></span> <span data-ttu-id="351b4-314">Exemple :</span><span class="sxs-lookup"><span data-stu-id="351b4-314">For example:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="351b4-315">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="351b4-315">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="351b4-316">Certains fournisseurs de journalisation vous permettent de spécifier quand des journaux doivent être écrites dans un support de stockage ou ignorés en fonction de la catégorie et le niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="351b4-316">Some logging providers let you specify when logs should be written to a storage medium or ignored based on log level and category.</span></span>

<span data-ttu-id="351b4-317">Le `AddConsole` et `AddDebug` les méthodes d’extension fournissent des surcharges qui vous permettent de passer dans les critères de filtrage.</span><span class="sxs-lookup"><span data-stu-id="351b4-317">The `AddConsole` and `AddDebug` extension methods provide overloads that let you pass in filtering criteria.</span></span> <span data-ttu-id="351b4-318">L’exemple de code suivant provoque le fournisseur de la console ignorer les journaux ci-dessous `Warning` de niveau, alors que le fournisseur de débogage ignore les journaux générés par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="351b4-318">The following sample code causes the console provider to ignore logs below `Warning` level, while the Debug provider ignores logs that the framework creates.</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

<span data-ttu-id="351b4-319">Le `AddEventLog` méthode a une surcharge qui prend un `EventLogSettings` instance, ce qui peut contenir une fonction de filtrage dans son `Filter` propriété.</span><span class="sxs-lookup"><span data-stu-id="351b4-319">The `AddEventLog` method has an overload that takes an `EventLogSettings` instance, which may contain a filtering function in its `Filter` property.</span></span> <span data-ttu-id="351b4-320">Le fournisseur de TraceSource ne fournit aucune de ces surcharges, étant donné que son niveau de journalisation et d’autres paramètres sont basés sur le `SourceSwitch` et `TraceListener` qu’il utilise.</span><span class="sxs-lookup"><span data-stu-id="351b4-320">The TraceSource provider does not provide any of those overloads, since its logging level and other parameters are based on the  `SourceSwitch` and `TraceListener` it uses.</span></span>

<span data-ttu-id="351b4-321">Vous pouvez définir des règles de filtrage pour tous les fournisseurs qui sont inscrits auprès d’un `ILoggerFactory` instance à l’aide de la `WithFilter` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="351b4-321">You can set filtering rules for all providers that are registered with an `ILoggerFactory` instance by using the `WithFilter` extension method.</span></span> <span data-ttu-id="351b4-322">L’exemple suivant limite journaux framework (catégorie commence par « Microsoft » ou « Système ») pour les avertissements tout en laissant le journal d’application au niveau de débogage.</span><span class="sxs-lookup"><span data-stu-id="351b4-322">The example below limits framework logs (category begins with "Microsoft" or "System") to warnings while letting the app log at debug level.</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

<span data-ttu-id="351b4-323">Si vous souhaitez utiliser le filtrage pour empêcher que tous les journaux en cours d’écriture pour une catégorie particulière, vous pouvez spécifier `LogLevel.None` en tant que le niveau de journalisation minimale pour cette catégorie.</span><span class="sxs-lookup"><span data-stu-id="351b4-323">If you want to use filtering to prevent all logs from being written for a particular category, you can specify `LogLevel.None` as the minimum log level for that category.</span></span> <span data-ttu-id="351b4-324">La valeur entière de `LogLevel.None` est 6, ce qui est supérieur à `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="351b4-324">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="351b4-325">Le `WithFilter` méthode d’extension est fournie par le [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) package NuGet.</span><span class="sxs-lookup"><span data-stu-id="351b4-325">The `WithFilter` extension method is provided by the [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet package.</span></span> <span data-ttu-id="351b4-326">La méthode retourne un nouveau `ILoggerFactory` instance qui permettra de filtrer les messages du journal transmis à tous les fournisseurs d’enregistreur d’événements enregistrés avec celui-ci.</span><span class="sxs-lookup"><span data-stu-id="351b4-326">The method returns a new `ILoggerFactory` instance that will filter the log messages passed to all logger providers registered with it.</span></span> <span data-ttu-id="351b4-327">Il n’affecte pas d’autres `ILoggerFactory` instances, y compris l’original `ILoggerFactory` instance.</span><span class="sxs-lookup"><span data-stu-id="351b4-327">It does not affect any other `ILoggerFactory` instances, including the original `ILoggerFactory` instance.</span></span>

---

## <a name="log-scopes"></a><span data-ttu-id="351b4-328">Étendues de journal</span><span class="sxs-lookup"><span data-stu-id="351b4-328">Log scopes</span></span>

<span data-ttu-id="351b4-329">Vous pouvez regrouper un ensemble d’opérations logiques dans un *étendue* pour pouvoir attacher les mêmes données pour chaque journal est créé dans le cadre de cet ensemble.</span><span class="sxs-lookup"><span data-stu-id="351b4-329">You can group a set of logical operations within a *scope* in order to attach the same data to each log that is created as part of that set.</span></span>  <span data-ttu-id="351b4-330">Par exemple, vous pourriez chaque journal créé dans le cadre du traitement d’une transaction pour inclure l’ID de transaction.</span><span class="sxs-lookup"><span data-stu-id="351b4-330">For example, you might want every log created as part of processing a transaction to include the transaction ID.</span></span>

<span data-ttu-id="351b4-331">Une étendue est un `IDisposable` type qui est retourné par la `ILogger.BeginScope<TState>` méthode et dure jusqu'à ce qu’elle est supprimée.</span><span class="sxs-lookup"><span data-stu-id="351b4-331">A scope is an `IDisposable` type that is returned by the `ILogger.BeginScope<TState>` method and lasts until it is disposed.</span></span> <span data-ttu-id="351b4-332">Vous utilisez une étendue en intégrant votre enregistreur d’événements appelle dans un `using` bloquer, comme illustré ici :</span><span class="sxs-lookup"><span data-stu-id="351b4-332">You use a scope by wrapping your logger calls in a `using` block, as shown here:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="351b4-333">Le code suivant permet d’étendues pour le fournisseur de la console :</span><span class="sxs-lookup"><span data-stu-id="351b4-333">The following code enables scopes for the console provider:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="351b4-334">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="351b4-334">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="351b4-335">Dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="351b4-335">In *Program.cs*:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_Scopes&highlight=4)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="351b4-336">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="351b4-336">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="351b4-337">Dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="351b4-337">In *Startup.cs*:</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_Scopes&highlight=6)]

---

<span data-ttu-id="351b4-338">Chaque message du journal inclut les informations incluses dans l’étendue :</span><span class="sxs-lookup"><span data-stu-id="351b4-338">Each log message includes the scoped information:</span></span>

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="351b4-339">Fournisseurs de journalisation intégrés</span><span class="sxs-lookup"><span data-stu-id="351b4-339">Built-in logging providers</span></span>

<span data-ttu-id="351b4-340">ASP.NET Core fourni les fournisseurs suivants :</span><span class="sxs-lookup"><span data-stu-id="351b4-340">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="351b4-341">Console</span><span class="sxs-lookup"><span data-stu-id="351b4-341">Console</span></span>](#console)
* [<span data-ttu-id="351b4-342">Déboguer</span><span class="sxs-lookup"><span data-stu-id="351b4-342">Debug</span></span>](#debug)
* [<span data-ttu-id="351b4-343">EventSource</span><span class="sxs-lookup"><span data-stu-id="351b4-343">EventSource</span></span>](#eventsource)
* [<span data-ttu-id="351b4-344">EventLog</span><span class="sxs-lookup"><span data-stu-id="351b4-344">EventLog</span></span>](#eventlog)
* [<span data-ttu-id="351b4-345">TraceSource</span><span class="sxs-lookup"><span data-stu-id="351b4-345">TraceSource</span></span>](#tracesource)
* [<span data-ttu-id="351b4-346">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="351b4-346">Azure App Service</span></span>](#appservice)

<a id="console"></a>
### <a name="the-console-provider"></a><span data-ttu-id="351b4-347">Le fournisseur de la console</span><span class="sxs-lookup"><span data-stu-id="351b4-347">The console provider</span></span>

<span data-ttu-id="351b4-348">Le [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package de fournisseur envoie la sortie de journal dans la console.</span><span class="sxs-lookup"><span data-stu-id="351b4-348">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="351b4-349">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="351b4-349">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddConsole()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="351b4-350">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="351b4-350">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddConsole()
```

<span data-ttu-id="351b4-351">[Les surcharges AddConsole](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.consoleloggerextensions) permettent de vous transmettez un un niveau de journalisation minimale, une fonction de filtre et une valeur booléenne qui indique si les étendues sont prises en charge.</span><span class="sxs-lookup"><span data-stu-id="351b4-351">[AddConsole overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.consoleloggerextensions) let you pass in an a minimum log level, a filter function, and a boolean that indicates whether scopes are supported.</span></span>  <span data-ttu-id="351b4-352">Une autre option consiste à passer dans un `IConfiguration` objet, que vous pouvez spécifier des niveaux d’enregistrement et de prise en charge des étendues.</span><span class="sxs-lookup"><span data-stu-id="351b4-352">Another option is to pass in an `IConfiguration` object, which can specify scopes support and logging levels.</span></span> 

<span data-ttu-id="351b4-353">Si vous envisagez de la console de fournisseur pour une utilisation en production, n’oubliez pas qu’il a un impact significatif sur les performances.</span><span class="sxs-lookup"><span data-stu-id="351b4-353">If you are considering the console provider for use in production, be aware that it has a significant impact on performance.</span></span>

<span data-ttu-id="351b4-354">Lorsque vous créez un nouveau projet dans Visual Studio, le `AddConsole` méthode ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="351b4-354">When you create a new project in Visual Studio, the `AddConsole` method looks like this:</span></span>

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

<span data-ttu-id="351b4-355">Ce code fait référence à la `Logging` section de la *appSettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="351b4-355">This code refers to the `Logging` section of the *appSettings.json* file:</span></span>

[!code-json[](logging/sample//appsettings.json)]

<span data-ttu-id="351b4-356">Les paramètres des journaux de framework limite aux avertissements tout en autorisant l’application pour se connecter au niveau du débogage, comme expliqué dans la [filtrage de journal](#log-filtering) section.</span><span class="sxs-lookup"><span data-stu-id="351b4-356">The settings shown limit framework logs to warnings while allowing the app to log at debug level, as explained in the [Log filtering](#log-filtering) section.</span></span> <span data-ttu-id="351b4-357">Pour plus d’informations, consultez [Configuration](configuration.md).</span><span class="sxs-lookup"><span data-stu-id="351b4-357">For more information, see [Configuration](configuration.md).</span></span>

---

<a id="debug"></a>
### <a name="the-debug-provider"></a><span data-ttu-id="351b4-358">Le fournisseur de débogage</span><span class="sxs-lookup"><span data-stu-id="351b4-358">The Debug provider</span></span>

<span data-ttu-id="351b4-359">Le [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package de fournisseur écrit la sortie du journal à l’aide de la [System.Diagnostics.Debug](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.debug#System_Diagnostics_Debug) classe (`Debug.WriteLine` les appels de méthode).</span><span class="sxs-lookup"><span data-stu-id="351b4-359">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.debug#System_Diagnostics_Debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="351b4-360">Sur Linux, ce fournisseur écrit des journaux sur */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="351b4-360">On Linux, this provider writes logs to */var/log/message*.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="351b4-361">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="351b4-361">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddDebug()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="351b4-362">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="351b4-362">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddDebug()
```

<span data-ttu-id="351b4-363">[Les surcharges AddDebug](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.debugloggerfactoryextensions) permettent de vous transmettez un niveau de journal minimale ou une fonction de filtre.</span><span class="sxs-lookup"><span data-stu-id="351b4-363">[AddDebug overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.debugloggerfactoryextensions) let you pass in a minimum log level or a filter function.</span></span>

---

<a id="eventsource"></a>
### <a name="the-eventsource-provider"></a><span data-ttu-id="351b4-364">Le fournisseur de source d’événement</span><span class="sxs-lookup"><span data-stu-id="351b4-364">The EventSource provider</span></span>

<span data-ttu-id="351b4-365">Pour les applications qui ciblent ASP.NET Core 1.1.0 ou une version ultérieure, le [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) package de fournisseur peut implémenter le suivi d’événements.</span><span class="sxs-lookup"><span data-stu-id="351b4-365">For apps that target ASP.NET Core 1.1.0 or higher, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="351b4-366">Sous Windows, il utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="351b4-366">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="351b4-367">Le fournisseur est inter-plateformes, mais il en existe aucun événement collecte et l’affichage des outils pour Linux ou macOS.</span><span class="sxs-lookup"><span data-stu-id="351b4-367">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="351b4-368">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="351b4-368">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddEventSourceLogger()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="351b4-369">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="351b4-369">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventSourceLogger()
```

---

<span data-ttu-id="351b4-370">Un bon moyen de collecter et d’afficher les journaux consiste à utiliser le [PerfView utilitaire](https://www.microsoft.com/download/details.aspx?id=28567).</span><span class="sxs-lookup"><span data-stu-id="351b4-370">A good way to collect and view logs is to use the [PerfView utility](https://www.microsoft.com/download/details.aspx?id=28567).</span></span> <span data-ttu-id="351b4-371">Il existe d’autres outils pour l’affichage des journaux ETW, mais PerfView fournit la meilleure expérience pour travailler avec les événements ETW émis par ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="351b4-371">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET.</span></span> 

<span data-ttu-id="351b4-372">Pour configurer PerfView pour collecter les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la **des fournisseurs supplémentaires** liste.</span><span class="sxs-lookup"><span data-stu-id="351b4-372">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="351b4-373">(Ne pas manquer de l’astérisque au début de la chaîne).</span><span class="sxs-lookup"><span data-stu-id="351b4-373">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview des fournisseurs supplémentaires](logging/_static/perfview-additional-providers.png)

<span data-ttu-id="351b4-375">Capture des événements sur Nano Server requiert une installation supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="351b4-375">Capturing events on Nano Server requires some additional setup:</span></span>

* <span data-ttu-id="351b4-376">Se connecter à la communication à distance PowerShell à Nano Server :</span><span class="sxs-lookup"><span data-stu-id="351b4-376">Connect PowerShell remoting to the Nano Server:</span></span>

  ```powershell
  Enter-PSSession [name]
  ```

* <span data-ttu-id="351b4-377">Créer une session ETW :</span><span class="sxs-lookup"><span data-stu-id="351b4-377">Create an ETW session:</span></span>

  ```powershell
  New-EtwTraceSession -Name "MyAppTrace" -LocalFilePath C:\trace.etl
  ```

* <span data-ttu-id="351b4-378">Ajouter des fournisseurs ETW pour [CLR](https://docs.microsoft.com/dotnet/framework/performance/clr-etw-providers), ASP.NET Core et autres en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="351b4-378">Add ETW providers for [CLR](https://docs.microsoft.com/dotnet/framework/performance/clr-etw-providers), ASP.NET Core, and others as needed.</span></span> <span data-ttu-id="351b4-379">Le fournisseur ASP.NET Core GUID est `3ac73b97-af73-50e9-0822-5da4367920d0`.</span><span class="sxs-lookup"><span data-stu-id="351b4-379">The ASP.NET Core provider GUID is `3ac73b97-af73-50e9-0822-5da4367920d0`.</span></span> 

  ```powershell
  Add-EtwTraceProvider -Guid "{e13c0d23-ccbc-4e12-931b-d9cc2eee27e4}" -SessionName MyAppTrace
  Add-EtwTraceProvider -Guid "{3ac73b97-af73-50e9-0822-5da4367920d0}" -SessionName MyAppTrace
  ```

* <span data-ttu-id="351b4-380">Exécuter le site et faire autant d’actions que vous voulez des informations de traçage pour.</span><span class="sxs-lookup"><span data-stu-id="351b4-380">Run the site and do whatever actions you want tracing information for.</span></span>

* <span data-ttu-id="351b4-381">Arrêter la session de suivi est terminée :</span><span class="sxs-lookup"><span data-stu-id="351b4-381">Stop the tracing session when you're finished:</span></span>

  ```powershell
  Stop-EtwTraceSession -Name "MyAppTrace"
  ```

<span data-ttu-id="351b4-382">Résultant *C:\trace.etl* fichier peut être analysé avec PerfView comme sur les autres éditions de Windows.</span><span class="sxs-lookup"><span data-stu-id="351b4-382">The resulting *C:\trace.etl* file can be analyzed with PerfView as on other editions of Windows.</span></span>

<a id="eventlog"></a>
### <a name="the-windows-eventlog-provider"></a><span data-ttu-id="351b4-383">Le fournisseur du journal des événements Windows</span><span class="sxs-lookup"><span data-stu-id="351b4-383">The Windows EventLog provider</span></span>

<span data-ttu-id="351b4-384">Le [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) package de fournisseur envoie la sortie de journal dans le journal des événements Windows.</span><span class="sxs-lookup"><span data-stu-id="351b4-384">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="351b4-385">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="351b4-385">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddEventLog()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="351b4-386">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="351b4-386">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventLog()
```

<span data-ttu-id="351b4-387">[Les surcharges de méthode AddEventLog](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.eventloggerfactoryextensions) permettent de vous transmettez `EventLogSettings` ou un niveau de journalisation minimale.</span><span class="sxs-lookup"><span data-stu-id="351b4-387">[AddEventLog overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.eventloggerfactoryextensions) let you pass in `EventLogSettings` or a minimum log level.</span></span>

---

<a id="tracesource"></a>
### <a name="the-tracesource-provider"></a><span data-ttu-id="351b4-388">Le fournisseur de TraceSource</span><span class="sxs-lookup"><span data-stu-id="351b4-388">The TraceSource provider</span></span>

<span data-ttu-id="351b4-389">Le [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) fournisseur package utilise le [System.Diagnostics.TraceSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracesource) bibliothèques et les fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="351b4-389">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the [System.Diagnostics.TraceSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracesource) libraries and providers.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="351b4-390">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="351b4-390">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddTraceSource(sourceSwitchName);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="351b4-391">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="351b4-391">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

---

<span data-ttu-id="351b4-392">[Les surcharges AddTraceSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.tracesourcefactoryextensions) permettent de vous transmettez un changement de source et un écouteur de suivi.</span><span class="sxs-lookup"><span data-stu-id="351b4-392">[AddTraceSource overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.tracesourcefactoryextensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="351b4-393">Pour utiliser ce fournisseur, une application doit s’exécuter sur le .NET Framework (plutôt que du .NET Core).</span><span class="sxs-lookup"><span data-stu-id="351b4-393">To use this provider, an application has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="351b4-394">Le permet de fournisseur pour router des messages à une variété de [écouteurs](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/trace-listeners), telles que la [TextWriterTraceListener](https://docs.microsoft.com/dotnet/api/system.diagnostics.textwritertracelistenerr) utilisé dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="351b4-394">The provider lets you route messages to a variety of [listeners](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/trace-listeners), such as the [TextWriterTraceListener](https://docs.microsoft.com/dotnet/api/system.diagnostics.textwritertracelistenerr) used in the sample application.</span></span>

<span data-ttu-id="351b4-395">L’exemple suivant configure un `TraceSource` fournisseur qui enregistre des `Warning` et des messages plus élevées dans la fenêtre de console.</span><span class="sxs-lookup"><span data-stu-id="351b4-395">The following example configures a `TraceSource` provider that logs `Warning` and higher messages to the console window.</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

<a id="appservice"></a>
### <a name="the-azure-app-service-provider"></a><span data-ttu-id="351b4-396">Le fournisseur de services d’application Azure</span><span class="sxs-lookup"><span data-stu-id="351b4-396">The Azure App Service provider</span></span>

<span data-ttu-id="351b4-397">Le [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) package de fournisseur écrit des journaux dans des fichiers texte dans le système de fichiers d’une application de Service d’applications Azure et en [stockage d’objets blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure.</span><span class="sxs-lookup"><span data-stu-id="351b4-397">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span> <span data-ttu-id="351b4-398">Le fournisseur est disponible uniquement pour les applications qui ciblent ASP.NET Core 1.1.0 ou ultérieure.</span><span class="sxs-lookup"><span data-stu-id="351b4-398">The provider is available only for apps that target ASP.NET Core 1.1.0 or higher.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="351b4-399">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="351b4-399">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

> [!NOTE]
> <span data-ttu-id="351b4-400">ASP.NET Core 2.0 est en version préliminaire.</span><span class="sxs-lookup"><span data-stu-id="351b4-400">ASP.NET Core 2.0 is in preview.</span></span>  <span data-ttu-id="351b4-401">Les applications créées avec la dernière version de l’aperçu peut ne pas fonctionner lors du déploiement vers Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="351b4-401">Apps created with the latest preview release may not run when deployed to Azure App Service.</span></span> <span data-ttu-id="351b4-402">Quand ASP.NET Core 2.0 est publiée, Azure App Service s’exécutera 2.0 applications et le Service d’application Azure fournisseur fonctionne comme indiqué ici.</span><span class="sxs-lookup"><span data-stu-id="351b4-402">When ASP.NET Core 2.0 is released, Azure App Service will run 2.0 apps, and the Azure App Service provider will work as indicated here.</span></span>

<span data-ttu-id="351b4-403">Vous n’êtes pas obligé d’installer le package du fournisseur ou appelez le `AddAzureWebAppDiagnostics` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="351b4-403">You don't have to install the provider package or call the `AddAzureWebAppDiagnostics` extension method.</span></span>  <span data-ttu-id="351b4-404">Le fournisseur est automatiquement disponible pour votre application lorsque vous déployez l’application sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="351b4-404">The provider is automatically available to your app when you deploy the app to Azure App Service.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="351b4-405">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="351b4-405">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="351b4-406">Un `AddAzureWebAppDiagnostics` surcharge vous permet de transmettre dans [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs), avec laquelle vous pouvez remplacer les paramètres par défaut tels que le modèle de sortie de journalisation, nom d’objet blob et limite de taille de fichier.</span><span class="sxs-lookup"><span data-stu-id="351b4-406">An `AddAzureWebAppDiagnostics` overload lets you pass in [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs), with which you can override default settings such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="351b4-407">(*Modèle sortie* est une chaîne de format de message qui est appliquée à tous les journaux sur celui que vous fournissez lorsque vous appelez un `ILogger` méthode.)</span><span class="sxs-lookup"><span data-stu-id="351b4-407">(*Output template* is a message format string that is applied to all logs, on top of the one that you provide when you call an `ILogger` method.)</span></span>  

---

<span data-ttu-id="351b4-408">Lorsque vous déployez une application de Service d’applications, votre application respecte les paramètres dans le [journaux de Diagnostic](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/#enablediag) section de la **du Service d’applications** page du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="351b4-408">When you deploy to an App Service app, your application honors the settings in the [Diagnostic Logs](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="351b4-409">Lorsque vous modifiez ces paramètres, les modifications prennent effet immédiatement sans redémarrer l’application ou de redéployer le code à ce dernier.</span><span class="sxs-lookup"><span data-stu-id="351b4-409">When you change those settings, the changes take effect immediately without requiring that you restart the app or redeploy code to it.</span></span> 

![Paramètres de journalisation d’Azure](logging/_static/azure-logging-settings.png)

<span data-ttu-id="351b4-411">L’emplacement par défaut pour les fichiers journaux est le *D:\\domestique\\LogFiles\\Application* dossier et le nom de fichier par défaut est *yyyymmdd.txt de diagnostics*.</span><span class="sxs-lookup"><span data-stu-id="351b4-411">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="351b4-412">La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal par défaut des fichiers conservés est 2.</span><span class="sxs-lookup"><span data-stu-id="351b4-412">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="351b4-413">Le nom d’objet blob par défaut est *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="351b4-413">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span> <span data-ttu-id="351b4-414">Pour plus d’informations sur le comportement par défaut, consultez [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs).</span><span class="sxs-lookup"><span data-stu-id="351b4-414">For more information about default behavior, see [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs).</span></span>

<span data-ttu-id="351b4-415">Le fournisseur fonctionne uniquement lorsque votre projet s’exécute dans l’environnement Windows Azure.</span><span class="sxs-lookup"><span data-stu-id="351b4-415">The provider only works when your project runs in the Azure environment.</span></span>  <span data-ttu-id="351b4-416">Il n’a aucun effet lorsque vous exécutez localement &mdash; il n’écrit pas dans les fichiers locaux ou de stockage de développement local pour les objets BLOB.</span><span class="sxs-lookup"><span data-stu-id="351b4-416">It has no effect when you run locally &mdash; it does not write to local files or local development storage for blobs.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="351b4-417">Modules fournisseurs d’informations de tiers</span><span class="sxs-lookup"><span data-stu-id="351b4-417">Third-party logging providers</span></span>

<span data-ttu-id="351b4-418">Voici certaines infrastructures de journalisation de l’application tierce qui fonctionnent avec ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="351b4-418">Here are some third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="351b4-419">[ELMAH.IO](https://github.com/elmahio/Elmah.Io.Extensions.Logging) -fournisseur pour le service Elmah.Io</span><span class="sxs-lookup"><span data-stu-id="351b4-419">[elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging) - provider for the Elmah.Io service</span></span>

* <span data-ttu-id="351b4-420">[JSNLog](http://jsnlog.com) -enregistre les exceptions JavaScript et autres événements côté client dans votre journal côté serveur.</span><span class="sxs-lookup"><span data-stu-id="351b4-420">[JSNLog](http://jsnlog.com) - logs JavaScript exceptions and other client-side events in your server-side log.</span></span>

* <span data-ttu-id="351b4-421">[Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) -fournisseur pour le service Loggr</span><span class="sxs-lookup"><span data-stu-id="351b4-421">[Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) - provider for the Loggr service</span></span>

* <span data-ttu-id="351b4-422">[NLog](https://github.com/NLog/NLog.Extensions.Logging) -fournisseur pour la bibliothèque NLog</span><span class="sxs-lookup"><span data-stu-id="351b4-422">[NLog](https://github.com/NLog/NLog.Extensions.Logging) - provider for the NLog library</span></span>

* <span data-ttu-id="351b4-423">[Serilog](https://github.com/serilog/serilog-extensions-logging) -fournisseur pour la bibliothèque Serilog</span><span class="sxs-lookup"><span data-stu-id="351b4-423">[Serilog](https://github.com/serilog/serilog-extensions-logging) - provider for the Serilog library</span></span>

<span data-ttu-id="351b4-424">Certaines infrastructures tierces faire [journalisation sémantique, également appelée enregistrement structuré](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="351b4-424">Some third-party frameworks can do [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="351b4-425">À l’aide d’une infrastructure tierce est semblable à celle des fournisseurs intégrés : ajouter un package NuGet à votre projet et appeler une méthode d’extension sur `ILoggerFactory`.</span><span class="sxs-lookup"><span data-stu-id="351b4-425">Using a third-party framework is similar to using one of the built-in providers: add a NuGet package to your project and call an extension method on `ILoggerFactory`.</span></span> <span data-ttu-id="351b4-426">Pour plus d’informations, consultez la documentation de chaque framework.</span><span class="sxs-lookup"><span data-stu-id="351b4-426">For more information, see each framework's documentation.</span></span>

<span data-ttu-id="351b4-427">Vous pouvez créer vos propres fournisseurs personnalisés, pour prendre en charge d’autres infrastructures de journalisation ou vos propres exigences de journalisation.</span><span class="sxs-lookup"><span data-stu-id="351b4-427">You can create your own custom providers as well, to support other logging frameworks or your own logging requirements.</span></span>

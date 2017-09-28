---
title: "Développement d’applications ASP.NET Core à l’aide de dotnet watch"
author: rick-anderson
description: Montre comment utiliser dotnet watch.
keywords: "ASP.NET Core, à l’aide de dotnet watch"
ms.author: riande
manager: wpickett
ms.date: 03/09/2017
ms.topic: article
ms.assetid: 563ffb3f-d369-4aa5-bf0a-7300b4e7832c
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/dotnet-watch
ms.openlocfilehash: 6a8943619e6174dbcd3d901b7bb736ba5d3af95d
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a><span data-ttu-id="90d3e-104">Développement d’applications ASP.NET Core à l’aide de dotnet watch</span><span class="sxs-lookup"><span data-stu-id="90d3e-104">Developing ASP.NET Core apps using dotnet watch</span></span>


<span data-ttu-id="90d3e-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="90d3e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="90d3e-106">`dotnet watch` est un outil qui exécute une commande `dotnet` quand des fichiers sources changent.</span><span class="sxs-lookup"><span data-stu-id="90d3e-106">`dotnet watch` is a tool that runs a `dotnet` command when source files change.</span></span> <span data-ttu-id="90d3e-107">Par exemple, une modification de fichier peut déclencher la compilation, tests ou le déploiement.</span><span class="sxs-lookup"><span data-stu-id="90d3e-107">For example, a file change can trigger compilation, tests, or deployment.</span></span>

<span data-ttu-id="90d3e-108">Dans ce didacticiel, nous utilisons une application API web existante avec deux points de terminaison : un qui retourne une somme et un qui retourne un produit.</span><span class="sxs-lookup"><span data-stu-id="90d3e-108">In this tutorial, we use an existing Web API app with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="90d3e-109">La méthode product contient un bogue que nous allons résoudre dans le cadre de ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="90d3e-109">The product method contains a bug that we'll fix as part of this tutorial.</span></span>

<span data-ttu-id="90d3e-110">Téléchargez l’[application exemple](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="90d3e-110">Download the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="90d3e-111">Il contient deux projets, `WebApp` (une application web) et `WebAppTests` (des tests unitaires pour l’application web).</span><span class="sxs-lookup"><span data-stu-id="90d3e-111">It contains two projects, `WebApp` (a web app) and `WebAppTests` (unit tests for the web app).</span></span>

<span data-ttu-id="90d3e-112">Dans une console, accédez au dossier WebApp et exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="90d3e-112">In a console, navigate to the WebApp folder and run the following commands:</span></span>

- `dotnet restore`
- `dotnet run`

<span data-ttu-id="90d3e-113">La sortie de la console affiche des messages semblables à ce qui suit (indiquant que l’application est en cours d’exécution et en attente de requêtes) :</span><span class="sxs-lookup"><span data-stu-id="90d3e-113">The console output will show messages similar to the following (indicating that the app is running and waiting for requests):</span></span>

```console
$ dotnet run
Hosting environment: Production
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="90d3e-114">Dans un navigateur web, accédez à `http://localhost:5000/api/math/sum?a=4&b=5`. Le résultat `9` doit s’afficher.</span><span class="sxs-lookup"><span data-stu-id="90d3e-114">In a web browser, navigate to `http://localhost:5000/api/math/sum?a=4&b=5`, you should see the result `9`.</span></span>

<span data-ttu-id="90d3e-115">Accédez à l’API du produit (`http://localhost:5000/api/math/product?a=4&b=5`). Elle retourne `9`, et non pas `20` comme prévu.</span><span class="sxs-lookup"><span data-stu-id="90d3e-115">Navigate to the product API (`http://localhost:5000/api/math/product?a=4&b=5`), it returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="90d3e-116">Nous allons corriger cela ultérieurement dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="90d3e-116">We'll fix that later in the tutorial.</span></span>

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="90d3e-117">Ajouter `dotnet watch` à un projet</span><span class="sxs-lookup"><span data-stu-id="90d3e-117">Add `dotnet watch` to a project</span></span>

- <span data-ttu-id="90d3e-118">Ajoutez `Microsoft.DotNet.Watcher.Tools` au fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="90d3e-118">Add `Microsoft.DotNet.Watcher.Tools` to the *.csproj* file:</span></span>
 ```xml
 <ItemGroup>
   <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
 </ItemGroup> 
 ```

- <span data-ttu-id="90d3e-119">Exécutez `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="90d3e-119">Run `dotnet restore`.</span></span>

## <a name="running-dotnet-commands-using-dotnet-watch"></a><span data-ttu-id="90d3e-120">Exécution de commandes `dotnet` à l’aide de `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="90d3e-120">Running `dotnet` commands using `dotnet watch`</span></span>

<span data-ttu-id="90d3e-121">Il est possible d’exécuter n’importe quelle commande `dotnet` avec `dotnet watch` ; par exemple :</span><span class="sxs-lookup"><span data-stu-id="90d3e-121">Any `dotnet` command can be run with `dotnet watch`, for example:</span></span>

| <span data-ttu-id="90d3e-122">Commande</span><span class="sxs-lookup"><span data-stu-id="90d3e-122">Command</span></span> | <span data-ttu-id="90d3e-123">Commande avec watch</span><span class="sxs-lookup"><span data-stu-id="90d3e-123">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="90d3e-124">dotnet run</span><span class="sxs-lookup"><span data-stu-id="90d3e-124">dotnet run</span></span> | <span data-ttu-id="90d3e-125">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="90d3e-125">dotnet watch run</span></span> |
| <span data-ttu-id="90d3e-126">dotnet run -f net451</span><span class="sxs-lookup"><span data-stu-id="90d3e-126">dotnet run -f net451</span></span> | <span data-ttu-id="90d3e-127">dotnet watch run -f net451</span><span class="sxs-lookup"><span data-stu-id="90d3e-127">dotnet watch run -f net451</span></span> |
| <span data-ttu-id="90d3e-128">dotnet run -f net451 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="90d3e-128">dotnet run -f net451 -- --arg1</span></span> | <span data-ttu-id="90d3e-129">dotnet watch run -f net451 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="90d3e-129">dotnet watch run -f net451 -- --arg1</span></span> |
| <span data-ttu-id="90d3e-130">dotnet test</span><span class="sxs-lookup"><span data-stu-id="90d3e-130">dotnet test</span></span> | <span data-ttu-id="90d3e-131">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="90d3e-131">dotnet watch test</span></span> |

<span data-ttu-id="90d3e-132">Exécutez `dotnet watch run` dans le dossier `WebApp`.</span><span class="sxs-lookup"><span data-stu-id="90d3e-132">Run `dotnet watch run` in the `WebApp` folder.</span></span> <span data-ttu-id="90d3e-133">La sortie de la console indique alors que `watch` a démarré.</span><span class="sxs-lookup"><span data-stu-id="90d3e-133">The console output will indicate `watch` has started.</span></span>

## <a name="making-changes-with-dotnet-watch"></a><span data-ttu-id="90d3e-134">Apport de modifications avec `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="90d3e-134">Making changes with `dotnet watch`</span></span>

<span data-ttu-id="90d3e-135">Vérifiez que `dotnet watch` est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="90d3e-135">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="90d3e-136">Corrigez le bogue présent dans la méthode `Product` de `MathController` afin qu’elle retourne le produit et non la somme.</span><span class="sxs-lookup"><span data-stu-id="90d3e-136">Fix the bug in the `Product` method of the `MathController` so it returns the product and not the sum.</span></span>

```csharp
public static int Product(int a, int b)
{
  return a * b;
} 
```

<span data-ttu-id="90d3e-137">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="90d3e-137">Save the file.</span></span> <span data-ttu-id="90d3e-138">La sortie de la console affiche des messages indiquant que `dotnet watch` a détecté un changement de fichier et redémarré de l’application.</span><span class="sxs-lookup"><span data-stu-id="90d3e-138">The console output will show messages indicating that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="90d3e-139">Vérifiez que `http://localhost:5000/api/math/product?a=4&b=5` retourne le résultat correct.</span><span class="sxs-lookup"><span data-stu-id="90d3e-139">Verify `http://localhost:5000/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="running-tests-using-dotnet-watch"></a><span data-ttu-id="90d3e-140">Exécution de tests à l’aide de `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="90d3e-140">Running tests using `dotnet watch`</span></span>

- <span data-ttu-id="90d3e-141">Modifiez la méthode `Product` de `MathController` pour qu’elle retourne à nouveau la somme, puis enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="90d3e-141">Change the `Product` method of the `MathController` back to returning the sum and save the file.</span></span>
- <span data-ttu-id="90d3e-142">Dans une fenêtre Commande, accédez au dossier `WebAppTests`.</span><span class="sxs-lookup"><span data-stu-id="90d3e-142">In a command window, naviagate to the `WebAppTests` folder.</span></span>
- <span data-ttu-id="90d3e-143">Exécutez `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="90d3e-143">Run `dotnet restore`</span></span>
- <span data-ttu-id="90d3e-144">Exécutez `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="90d3e-144">Run `dotnet watch test`.</span></span> <span data-ttu-id="90d3e-145">Une sortie s’affiche, indiquant qu’un test a échoué et que l’observateur est en attente de modifications de fichier :</span><span class="sxs-lookup"><span data-stu-id="90d3e-145">You see output indicating that a test failed and that watcher is waiting for file changes:</span></span>

 ```console
 Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
 Test Run Failed.
  ```
- <span data-ttu-id="90d3e-146">Corrigez le code de la méthode `Product` afin qu’elle retourne le produit.</span><span class="sxs-lookup"><span data-stu-id="90d3e-146">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="90d3e-147">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="90d3e-147">Save the file.</span></span>

<span data-ttu-id="90d3e-148">`dotnet watch` détecte le changement de fichier et réexécute les tests.</span><span class="sxs-lookup"><span data-stu-id="90d3e-148">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="90d3e-149">La sortie de la console affiche alors les tests réussis.</span><span class="sxs-lookup"><span data-stu-id="90d3e-149">The console output will show the tests passed.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="90d3e-150">dotnet-watch dans GitHub</span><span class="sxs-lookup"><span data-stu-id="90d3e-150">dotnet-watch in GitHub</span></span>

<span data-ttu-id="90d3e-151">dotnet-watch fait partie du [dépôt DotNetTools](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools) GitHub.</span><span class="sxs-lookup"><span data-stu-id="90d3e-151">dotnet-watch is part of the GitHub [DotNetTools repository](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools).</span></span>

<span data-ttu-id="90d3e-152">La [section MSBuild](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild) du fichier [Lisez-moi dotnet-watch](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) explique comment configurer dotnet-watch à partir du fichier projet MSBuild surveillé.</span><span class="sxs-lookup"><span data-stu-id="90d3e-152">The [MSBuild section](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild) of the [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) explains how dotnet-watch can be configured from the MSBuild project file being watched.</span></span> <span data-ttu-id="90d3e-153">Le fichier [Lisez-moi dotnet-watch](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) contient des informations sur dotnet-watch non traitées dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="90d3e-153">The [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) contains information on dotnet-watch not covered in this tutorial.</span></span>

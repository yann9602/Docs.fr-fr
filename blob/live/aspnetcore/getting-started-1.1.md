---
title: "Bien démarrer avec ASP.NET Core 1.1"
author: rick-anderson
description: "Didacticiel rapide qui crée et exécute une application Hello World simple à l’aide d’ASP.NET Core 1.1."
keywords: "ASP.NET Core,didacticiel,bien démarrer"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.assetid: 73543e9d-d9d5-47d6-9664-17a9beea6cd3
ms.technology: aspnet
ms.prod: asp.net-core
uid: getting-started-1.1
ms.openlocfilehash: e8fd9ef60ebc1cff6ca0e03000ea50eebff0a9f9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="getting-started-with-aspnet-core-11"></a><span data-ttu-id="87b54-104">Bien démarrer avec ASP.NET Core 1.1</span><span class="sxs-lookup"><span data-stu-id="87b54-104">Getting Started with ASP.NET Core 1.1</span></span>

> [!NOTE]
> <span data-ttu-id="87b54-105">Les instructions suivantes concernent ASP.NET Core 1.1.</span><span class="sxs-lookup"><span data-stu-id="87b54-105">These instructions are for ASP.NET Core 1.1.</span></span> <span data-ttu-id="87b54-106">Si vous recherchez la dernière version,</span><span class="sxs-lookup"><span data-stu-id="87b54-106">Looking for the latest version?</span></span> <span data-ttu-id="87b54-107">consultez [la version actuelle de ce didacticiel](xref:getting-started).</span><span class="sxs-lookup"><span data-stu-id="87b54-107">See [the current version of this tutorial](xref:getting-started).</span></span>

1. <span data-ttu-id="87b54-108">Installez le **programme d’installation du SDK** .NET Core pour le SDK 1.0.4 à partir de la [page des téléchargements du SDK 1.0.4 .NET Core 1.0.5 & 1.1.2](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.0.5-download.md).</span><span class="sxs-lookup"><span data-stu-id="87b54-108">Install the .NET Core **SDK Installer** for SDK 1.0.4 from the [.NET Core 1.0.5 & 1.1.2 SDK 1.0.4 downloads page](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.0.5-download.md).</span></span>

2. <span data-ttu-id="87b54-109">Créez un dossier pour un nouveau projet .NET Core.</span><span class="sxs-lookup"><span data-stu-id="87b54-109">Create a folder for a new .NET Core project.</span></span>

   <span data-ttu-id="87b54-110">Sur macOS et Linux, ouvrez une fenêtre de terminal.</span><span class="sxs-lookup"><span data-stu-id="87b54-110">On macOS and Linux, open a terminal window.</span></span> <span data-ttu-id="87b54-111">Sur Windows, ouvrez une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="87b54-111">On Windows, open a command prompt.</span></span>

   ```terminal
   mkdir aspnetcoreapp
   cd aspnetcoreapp
   ```

2. <span data-ttu-id="87b54-112">Si vous avez installé une version ultérieure du SDK sur votre ordinateur, créez un fichier *global.json* pour sélectionner le SDK 1.0.4.</span><span class="sxs-lookup"><span data-stu-id="87b54-112">If you have installed a later SDK version on your machine, create a *global.json* file to select the 1.0.4 SDK.</span></span>

   ```json
   {
     "sdk": { "version": "1.0.4" }
   }
   ```

2. <span data-ttu-id="87b54-113">Créez un projet .NET Core.</span><span class="sxs-lookup"><span data-stu-id="87b54-113">Create a new .NET Core project.</span></span>

   ```terminal
   dotnet new web
   ```
   
3.  <span data-ttu-id="87b54-114">Restaurez les packages.</span><span class="sxs-lookup"><span data-stu-id="87b54-114">Restore the packages.</span></span>

    ```terminal
    dotnet restore
    ```

4. <span data-ttu-id="87b54-115">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="87b54-115">Run the app.</span></span>

   <span data-ttu-id="87b54-116">La commande `dotnet run` commence par générer l’application, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="87b54-116">The `dotnet run` command builds the app first if needed.</span></span>

   ```terminal
   dotnet run
   ```

5. <span data-ttu-id="87b54-117">Accédez à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="87b54-117">Browse to `http://localhost:5000`</span></span>

<!-- H3 to avoid a single-entry internal TOC -->
### <a name="next-steps"></a><span data-ttu-id="87b54-118">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="87b54-118">Next steps</span></span>

<span data-ttu-id="87b54-119">Pour consulter des didacticiels permettant de bien démarrer, consultez [Didacticiels ASP.NET Core](tutorials/index.md).</span><span class="sxs-lookup"><span data-stu-id="87b54-119">For getting-started tutorials, see [ASP.NET Core Tutorials](tutorials/index.md)</span></span>

<span data-ttu-id="87b54-120">Pour obtenir une introduction aux concepts et à l’architecture d’ASP.NET Core, consultez [Introduction à ASP.NET Core](index.md) et [Notions de base d’ASP.NET Core](fundamentals/index.md).</span><span class="sxs-lookup"><span data-stu-id="87b54-120">For an introduction to ASP.NET Core concepts and architecture, see [ASP.NET Core Introduction](index.md) and [ASP.NET Core Fundamentals](fundamentals/index.md).</span></span>

<span data-ttu-id="87b54-121">Une application ASP.NET Core peut utiliser la bibliothèque de classes de base et le runtime .NET Core ou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="87b54-121">An ASP.NET Core app can use the .NET Core or .NET Framework Base Class Library and runtime.</span></span> <span data-ttu-id="87b54-122">Pour plus d’informations, consultez [Choix entre .NET Core et .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="87b54-122">For more information, see [Choosing between .NET Core and .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span></span>

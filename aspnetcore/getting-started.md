---
title: "Bien démarrer avec ASP.NET Core 2.0"
author: rick-anderson
description: "Didacticiel rapide qui crée et exécute une application Hello World simple à l’aide d’ASP.NET Core."
keywords: "ASP.NET Core,didacticiel,bien démarrer"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.assetid: 73543e9d-d9d5-47d6-9664-17a9beea6cd3
ms.technology: aspnet
ms.prod: asp.net-core
uid: getting-started
ms.openlocfilehash: 3399df3958093da9117b013736b1cb370fd6beb2
ms.sourcegitcommit: 297ee5d2f3b3b24eb8a2c4a25195c9e2973cb91b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2017
---
# <a name="getting-started-with-aspnet-core"></a><span data-ttu-id="84dd3-104">Bien démarrer avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84dd3-104">Getting Started with ASP.NET Core</span></span>

> [!NOTE]
> <span data-ttu-id="84dd3-105">Les instructions suivantes concernent la dernière version d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="84dd3-105">These instructions are for the latest version of ASP.NET Core.</span></span> <span data-ttu-id="84dd3-106">Si vous souhaitez bien démarrer avec une version précédente,</span><span class="sxs-lookup"><span data-stu-id="84dd3-106">Looking to get started with an earlier version?</span></span> <span data-ttu-id="84dd3-107">consultez [la version 1.1 de ce didacticiel](xref:getting-started-1.1).</span><span class="sxs-lookup"><span data-stu-id="84dd3-107">See [the 1.1 version of this tutorial](xref:getting-started-1.1).</span></span>

1. <span data-ttu-id="84dd3-108">Installez [.NET Core](https://microsoft.com/net/core/).</span><span class="sxs-lookup"><span data-stu-id="84dd3-108">Install [.NET Core](https://microsoft.com/net/core/).</span></span>

2. <span data-ttu-id="84dd3-109">Créez un projet .NET Core.</span><span class="sxs-lookup"><span data-stu-id="84dd3-109">Create a new .NET Core project.</span></span>

   <span data-ttu-id="84dd3-110">Sur macOS et Linux, ouvrez une fenêtre de terminal.</span><span class="sxs-lookup"><span data-stu-id="84dd3-110">On macOS and Linux, open a terminal window.</span></span> <span data-ttu-id="84dd3-111">Sur Windows, ouvrez une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="84dd3-111">On Windows, open a command prompt.</span></span>

   ```terminal
   mkdir aspnetcoreapp
   cd aspnetcoreapp
   dotnet new web
   ```
    
4. <span data-ttu-id="84dd3-112">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="84dd3-112">Run the app.</span></span>

   <span data-ttu-id="84dd3-113">La commande `dotnet run` commence par générer l’application, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="84dd3-113">The `dotnet run` command builds the app first if needed.</span></span>

   ```terminal
   dotnet run
   ```

5. <span data-ttu-id="84dd3-114">Accédez à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="84dd3-114">Browse to `http://localhost:5000`</span></span>

### <a name="next-steps"></a><span data-ttu-id="84dd3-115">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="84dd3-115">Next steps</span></span>

<span data-ttu-id="84dd3-116">Pour consulter des didacticiels permettant de bien démarrer, consultez [Didacticiels ASP.NET Core](tutorials/index.md).</span><span class="sxs-lookup"><span data-stu-id="84dd3-116">For getting-started tutorials, see [ASP.NET Core Tutorials](tutorials/index.md)</span></span>

<span data-ttu-id="84dd3-117">Pour obtenir une introduction aux concepts et à l’architecture d’ASP.NET Core, consultez [Introduction à ASP.NET Core](index.md) et [Notions de base d’ASP.NET Core](fundamentals/index.md).</span><span class="sxs-lookup"><span data-stu-id="84dd3-117">For an introduction to ASP.NET Core concepts and architecture, see [ASP.NET Core Introduction](index.md) and [ASP.NET Core Fundamentals](fundamentals/index.md).</span></span>

<span data-ttu-id="84dd3-118">Une application ASP.NET Core peut utiliser la bibliothèque de classes de base et le runtime .NET Core ou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="84dd3-118">An ASP.NET Core app can use the .NET Core or .NET Framework Base Class Library and runtime.</span></span> <span data-ttu-id="84dd3-119">Pour plus d’informations, consultez [Choix entre .NET Core et .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="84dd3-119">For more information, see [Choosing between .NET Core and .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span></span>

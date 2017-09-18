---
title: "Bien démarrer avec ASP.NET Core 2.0"
author: rick-anderson
description: "Didacticiel rapide qui crée et exécute une application Hello World simple à l’aide d’ASP.NET Core."
keywords: "ASP.NET Core,didacticiel,bien démarrer"
ms.author: riande
manager: wpickett
ms.date: 08/30/2017
ms.topic: get-started-article
ms.assetid: 73543e9d-d9d5-47d6-9664-17a9beea6cd3
ms.technology: aspnet
ms.prod: asp.net-core
uid: getting-started
ms.openlocfilehash: f7852f0dddb0585089f5ccd8f4c865f5b87b049b
ms.sourcegitcommit: fb518f856f31fe53c09196a13309eacb85b37a22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2017
---
# <a name="getting-started-with-aspnet-core"></a><span data-ttu-id="6f5c1-104">Bien démarrer avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f5c1-104">Getting Started with ASP.NET Core</span></span>

> [!NOTE]
> <span data-ttu-id="6f5c1-105">Les instructions suivantes concernent la dernière version d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f5c1-105">These instructions are for the latest version of ASP.NET Core.</span></span> <span data-ttu-id="6f5c1-106">Si vous souhaitez bien démarrer avec une version précédente,</span><span class="sxs-lookup"><span data-stu-id="6f5c1-106">Looking to get started with an earlier version?</span></span> <span data-ttu-id="6f5c1-107">consultez [la version 1.1 de ce didacticiel](xref:getting-started-1.1).</span><span class="sxs-lookup"><span data-stu-id="6f5c1-107">See [the 1.1 version of this tutorial](xref:getting-started-1.1).</span></span>

1. <span data-ttu-id="6f5c1-108">Installez [.NET Core](https://microsoft.com/net/core/).</span><span class="sxs-lookup"><span data-stu-id="6f5c1-108">Install [.NET Core](https://microsoft.com/net/core/).</span></span>

2. <span data-ttu-id="6f5c1-109">Créez un projet .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f5c1-109">Create a new .NET Core project.</span></span>

   <span data-ttu-id="6f5c1-110">Sur macOS et Linux, ouvrez une fenêtre de terminal.</span><span class="sxs-lookup"><span data-stu-id="6f5c1-110">On macOS and Linux, open a terminal window.</span></span> <span data-ttu-id="6f5c1-111">Sur Windows, ouvrez une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="6f5c1-111">On Windows, open a command prompt.</span></span>

    ```terminal
    dotnet new razor -o aspnetcoreapp
    ```
    
4. <span data-ttu-id="6f5c1-112">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="6f5c1-112">Run the app.</span></span>

    <span data-ttu-id="6f5c1-113">Exécutez les commandes suivantes pour exécuter l’application :</span><span class="sxs-lookup"><span data-stu-id="6f5c1-113">Use the following commands to run the app:</span></span>

    ```terminal
    cd aspnetcoreapp
    dotnet run
    ```

5. <span data-ttu-id="6f5c1-114">Accédez à [http://localhost:5000](http://localhost:5000)</span><span class="sxs-lookup"><span data-stu-id="6f5c1-114">Browse to [http://localhost:5000](http://localhost:5000)</span></span>

6. <span data-ttu-id="6f5c1-115">Ouvrez *Pages/About.cshtml*, puis modifiez la page de façon à afficher le message « Hello, world!</span><span class="sxs-lookup"><span data-stu-id="6f5c1-115">Open *Pages/About.cshtml* and modify the page to display the message "Hello, world!</span></span> <span data-ttu-id="6f5c1-116">The time on the server is @DateTime.Now » :</span><span class="sxs-lookup"><span data-stu-id="6f5c1-116">The time on the server is @DateTime.Now":</span></span>

    <span data-ttu-id="6f5c1-117">[!code-html[Main](getting-started/sample/getting-started/about.cshtml?highlight=9&range=1-9)]</span><span class="sxs-lookup"><span data-stu-id="6f5c1-117">[!code-html[Main](getting-started/sample/getting-started/about.cshtml?highlight=9&range=1-9)]</span></span>

7. <span data-ttu-id="6f5c1-118">Accédez à [http://localhost:5000/About](http://localhost:5000/About), puis vérifiez les modifications.</span><span class="sxs-lookup"><span data-stu-id="6f5c1-118">Browse to [http://localhost:5000/About](http://localhost:5000/About) and verify the changes.</span></span>

### <a name="next-steps"></a><span data-ttu-id="6f5c1-119">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="6f5c1-119">Next steps</span></span>

<span data-ttu-id="6f5c1-120">Pour consulter des didacticiels permettant de bien démarrer, consultez [Didacticiels ASP.NET Core](tutorials/index.md).</span><span class="sxs-lookup"><span data-stu-id="6f5c1-120">For getting-started tutorials, see [ASP.NET Core Tutorials](tutorials/index.md)</span></span>

<span data-ttu-id="6f5c1-121">Pour obtenir une introduction aux concepts et à l’architecture d’ASP.NET Core, consultez [Introduction à ASP.NET Core](index.md) et [Notions de base d’ASP.NET Core](fundamentals/index.md).</span><span class="sxs-lookup"><span data-stu-id="6f5c1-121">For an introduction to ASP.NET Core concepts and architecture, see [ASP.NET Core Introduction](index.md) and [ASP.NET Core Fundamentals](fundamentals/index.md).</span></span>

<span data-ttu-id="6f5c1-122">Une application ASP.NET Core peut utiliser la bibliothèque de classes de base et le runtime .NET Core ou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="6f5c1-122">An ASP.NET Core app can use the .NET Core or .NET Framework Base Class Library and runtime.</span></span> <span data-ttu-id="6f5c1-123">Pour plus d’informations, consultez [Choix entre .NET Core et .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="6f5c1-123">For more information, see [Choosing between .NET Core and .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span></span>

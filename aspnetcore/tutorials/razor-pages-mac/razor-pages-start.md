---
title: "Bien démarrer avec des pages Razor dans ASP.NET Core sur Mac"
author: rick-anderson
description: "Bien démarrer avec des pages Razor dans ASP.NET Core à l’aide de Visual Studio pour Mac"
keywords: "ASP.NET Core, pages Razor, génération de modèles automatique, Entity Framework Core, EF, EF Core, base de données, mac, macOS, Visual Studio pour Mac"
ms.author: riande
manager: wpickett
ms.date: 7/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages-mac/razor-pages-start
ms.openlocfilehash: caadc3fcb3bb71abe0773aed4f6ff60a043e3a02
ms.sourcegitcommit: d9ec19e5452af83648074db5d96c0a0f4f9e7f9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2017
---
# <a name="getting-started-with-razor-pages-in-aspnet-core-with-visual-studio-for-mac"></a><span data-ttu-id="e59ba-104">Bien démarrer avec des pages Razor dans ASP.NET Core avec Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="e59ba-104">Getting started with Razor Pages in ASP.NET Core with Visual Studio for Mac</span></span>

<span data-ttu-id="e59ba-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e59ba-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e59ba-106">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web de pages Razor dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e59ba-106">This tutorial teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span> <span data-ttu-id="e59ba-107">Nous vous recommandons d’effectuer l’étape [Présentation des pages Razor](xref:mvc/razor-pages/index) avant de commencer ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="e59ba-107">We recommend you complete [Introduction to Razor Pages](xref:mvc/razor-pages/index) before starting this tutorial.</span></span> <span data-ttu-id="e59ba-108">L’utilisation de pages Razor est la méthode recommandée pour générer l’IU d’applications web dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e59ba-108">Razor Pages is the recommended way to build UI for web applications in ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e59ba-109">Prérequis</span><span class="sxs-lookup"><span data-stu-id="e59ba-109">Prerequisites</span></span>

<span data-ttu-id="e59ba-110">Installez les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="e59ba-110">Install the following:</span></span>

* <span data-ttu-id="e59ba-111">[Kit .NET Core 2.0.0 SDK](https://dot.net/core) ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="e59ba-111">[.NET Core 2.0.0 SDK](https://dot.net/core) or later</span></span>
* [<span data-ttu-id="e59ba-112">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="e59ba-112">Visual Studio for Mac</span></span>](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-a-razor-web-app"></a><span data-ttu-id="e59ba-113">Créer une application web Razor</span><span class="sxs-lookup"><span data-stu-id="e59ba-113">Create a Razor web app</span></span>

<span data-ttu-id="e59ba-114">À partir d’un terminal, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="e59ba-114">From a terminal, run the following commands:</span></span>

```console
dotnet new razor -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

<span data-ttu-id="e59ba-115">Les commandes précédentes utilisent le [CLI .NET Core](https://docs.microsoft.com/dotnet/core/tools/dotnet) pour créer et exécuter un projet de pages Razor.</span><span class="sxs-lookup"><span data-stu-id="e59ba-115">The preceding commands use the [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/dotnet) to create and run a Razor Pages project.</span></span> <span data-ttu-id="e59ba-116">Ouvrez un navigateur à l’adresse http://localhost:5000 pour afficher l’application.</span><span class="sxs-lookup"><span data-stu-id="e59ba-116">Open a browser to http://localhost:5000 to view the application.</span></span>

![Page d’accueil ou page d’index](../razor-pages/razor-pages-start/_static/home.png)

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

## <a name="open-the-project"></a><span data-ttu-id="e59ba-118">Ouvrir le projet</span><span class="sxs-lookup"><span data-stu-id="e59ba-118">Open the project</span></span>

<span data-ttu-id="e59ba-119">Appuyez sur Ctrl+C pour arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="e59ba-119">Press Ctrl+C to shut down the application.</span></span>

<span data-ttu-id="e59ba-120">Dans Visual Studio, sélectionnez **Fichier > Ouvrir**, puis sélectionnez le fichier *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="e59ba-120">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="e59ba-121">Lancer l’application</span><span class="sxs-lookup"><span data-stu-id="e59ba-121">Launch the app</span></span>

<span data-ttu-id="e59ba-122">Dans Visual Studio, sélectionnez **Exécuter > Exécuter sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="e59ba-122">In Visual Studio, select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="e59ba-123">Visual Studio démarre [IIS Express](http://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview), lance un navigateur, puis accède à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e59ba-123">Visual Studio starts [IIS Express](http://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview), launches a browser, and navigates to `http://localhost:5000`.</span></span>

<span data-ttu-id="e59ba-124">Dans le prochain didacticiel, nous allons ajouter un modèle au projet.</span><span class="sxs-lookup"><span data-stu-id="e59ba-124">In the next tutorial, we add a model to the project.</span></span>

>[!div class="step-by-step"]
[<span data-ttu-id="e59ba-125">Suivant : Ajout d’un modèle</span><span class="sxs-lookup"><span data-stu-id="e59ba-125">Next: Adding a model</span></span>](xref:tutorials/razor-pages-mac/model)

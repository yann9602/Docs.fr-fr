---
title: "Bien démarrer avec ASP.NET Core MVC et Visual Studio pour Mac"
author: rick-anderson
description: "Bien démarrer avec ASP.NET Core MVC et Visual Studio"
keywords: ASP.NET Core, MVC, Visual Studio pour Mac, Entity Framework
ms.author: riande
manager: wpickett
ms.date: 8/23/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-mac/start-mvc
ms.openlocfilehash: 21f115eec924d5e4b21ad78398c8cbd99e02a0a8
ms.sourcegitcommit: 216dfac27542f10a79274a9ce60dc449e888ed20
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="getting-started-with-aspnet-core-mvc-and-visual-studio-for-mac"></a><span data-ttu-id="75ae9-104">Bien démarrer avec ASP.NET Core MVC et Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="75ae9-104">Getting started with ASP.NET Core MVC and Visual Studio for Mac</span></span>

<span data-ttu-id="75ae9-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="75ae9-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="75ae9-106">Ce didacticiel présente les principes de base de la génération d’une application web ASP.NET Core MVC à l’aide de [Visual Studio pour Mac](https://www.visualstudio.com/vs/visual-studio-mac/).</span><span class="sxs-lookup"><span data-stu-id="75ae9-106">This tutorial teaches you the basics of building an ASP.NET Core MVC web app using [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/).</span></span> 

[!INCLUDE[consider RP](../../includes/razor.md)]

<span data-ttu-id="75ae9-107">Il existe trois versions de ce didacticiel :</span><span class="sxs-lookup"><span data-stu-id="75ae9-107">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="75ae9-108">macOS : [Créer une application ASP.NET Core MVC avec Visual Studio pour Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="75ae9-108">macOS: [Build an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="75ae9-109">Windows : [Créer une application ASP.NET Core MVC avec Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="75ae9-109">Windows: [Build an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="75ae9-110">Linux, macOS et Windows : [Créer une application ASP.NET Core MVC avec Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="75ae9-110">Linux, macOS, and Windows: [Build an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="75ae9-111">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="75ae9-111">Prerequisites</span></span>

<span data-ttu-id="75ae9-112">Ce didacticiel nécessite le [SDK .NET Core 2.0.0](https://www.microsoft.com/net/core) ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="75ae9-112">This tutorial requires the [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later.</span></span>

<span data-ttu-id="75ae9-113">Installez les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="75ae9-113">Install the following:</span></span>

- <span data-ttu-id="75ae9-114">[SDK .NET Core 2.0.0 ](https://www.microsoft.com/net/core) ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="75ae9-114">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later</span></span>
- [<span data-ttu-id="75ae9-115">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="75ae9-115">Visual Studio for Mac</span></span>](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-a-web-app"></a><span data-ttu-id="75ae9-116">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="75ae9-116">Create a web app</span></span>

<span data-ttu-id="75ae9-117">Dans Visual Studio, sélectionnez **Fichier > Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="75ae9-117">From Visual Studio, select **File > New Solution**.</span></span>

![macOS - Nouvelle solution](../first-web-api-mac/_static/sln.png)

<span data-ttu-id="75ae9-119">Sélectionnez **Application .NET Core > ASP.NET Core > Application web > Suivant**.</span><span class="sxs-lookup"><span data-stu-id="75ae9-119">Select **.NET Core App >  ASP.NET Core > Web App > Next**.</span></span>

![macOS - Boîte de dialogue Nouveau projet](start-mvc/1.png)

<span data-ttu-id="75ae9-121">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="75ae9-121">Name the project **MvcMovie**, and then select **Create**.</span></span>

![macOS - Boîte de dialogue Nouveau projet](start-mvc/2.png)

### <a name="launch-the-app"></a><span data-ttu-id="75ae9-123">Lancer l’application</span><span class="sxs-lookup"><span data-stu-id="75ae9-123">Launch the app</span></span>

<span data-ttu-id="75ae9-124">Dans Visual Studio, sélectionnez **Exécuter > Exécuter sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="75ae9-124">In Visual Studio, select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="75ae9-125">Visual Studio démarre [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi au hasard.</span><span class="sxs-lookup"><span data-stu-id="75ae9-125">Visual Studio starts [Kestrel](xref:fundamentals/servers/index#kestrel), launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

![Navigateur avec le nouveau projet](start-mvc/b1.png)

* <span data-ttu-id="75ae9-127">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="75ae9-127">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="75ae9-128">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="75ae9-128">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="75ae9-129">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="75ae9-129">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="75ae9-130">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="75ae9-130">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="75ae9-131">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="75ae9-131">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="75ae9-132">Le modèle par défaut donne des liens **Accueil, À propos** et **Contact**.</span><span class="sxs-lookup"><span data-stu-id="75ae9-132">The default template gives you **Home, About** and **Contact** links.</span></span> <span data-ttu-id="75ae9-133">L’image de navigateur ci-dessus ne montre pas ces liens.</span><span class="sxs-lookup"><span data-stu-id="75ae9-133">The browser image above doesn't show these links.</span></span> <span data-ttu-id="75ae9-134">En fonction de la taille de votre navigateur, vous devrez peut-être cliquer sur l’icône de navigation pour les afficher.</span><span class="sxs-lookup"><span data-stu-id="75ae9-134">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![Navigateur avec le nouveau projet](start-mvc/b2.png)

<span data-ttu-id="75ae9-136">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="75ae9-136">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

>[!div class="step-by-step"]
[<span data-ttu-id="75ae9-137">Next</span><span class="sxs-lookup"><span data-stu-id="75ae9-137">Next</span></span>](adding-controller.md)  

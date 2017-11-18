---
title: Choisir entre ASP.NET et ASP.NET Core
author: rick-anderson
description: "Découvrez comment choisir entre ASP.NET et ASP.NET Core."
keywords: "ASP.NET Core,notions de base,vue d’ensemble"
ms.author: riande
manager: wpickett
ms.date: 09/30/2017
ms.topic: article
ms.assetid: f0d17abf-3c69-413e-87fc-30780805e33f
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 875064bd3437acc4e2a53220e19e86431d8c159b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="choose-between-aspnet-and-aspnet-core"></a><span data-ttu-id="d8350-104">Choisir entre ASP.NET et ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8350-104">Choose between ASP.NET and ASP.NET Core</span></span> 

<span data-ttu-id="d8350-105">Quelle que soit l’application web à créer, ASP.NET a une solution pour vous : depuis les applications web d’entreprise ciblant Windows Server aux microservices ciblant des conteneurs Linux, et tout ce qui se trouve entre les deux.</span><span class="sxs-lookup"><span data-stu-id="d8350-105">No matter the web application you are creating, ASP.NET has a solution for you: from enterprise web applications targeting Windows Server, to small microservices targeting Linux containers, and everything in between.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="d8350-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8350-106">ASP.NET Core</span></span>

<span data-ttu-id="d8350-107">ASP.NET Core est un framework open source et multiplateforme pour générer des applications web cloud modernes sur Windows, macOS ou Linux.</span><span class="sxs-lookup"><span data-stu-id="d8350-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web applications on Windows, macOS, or Linux.</span></span>

## <a name="aspnet"></a><span data-ttu-id="d8350-108">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="d8350-108">ASP.NET</span></span>

<span data-ttu-id="d8350-109">ASP.NET est un framework mature qui offre tous les services nécessaires pour générer sur Windows des applications web d’entreprise basées sur des serveurs.</span><span class="sxs-lookup"><span data-stu-id="d8350-109">ASP.NET is a mature framework that provides all the services needed to build enterprise-class, server-based web applications on Windows.</span></span>

## <a name="which-one-is-right-for-me"></a><span data-ttu-id="d8350-110">Laquelle des deux est adaptée à mes besoins ?</span><span class="sxs-lookup"><span data-stu-id="d8350-110">Which one is right for me?</span></span>

| <span data-ttu-id="d8350-111">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8350-111">ASP.NET Core</span></span> | <span data-ttu-id="d8350-112">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="d8350-112">ASP.NET</span></span> |
|---|---|
|<span data-ttu-id="d8350-113">Générer pour Windows, macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="d8350-113">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="d8350-114">Générer pour Windows</span><span class="sxs-lookup"><span data-stu-id="d8350-114">Build for Windows</span></span>|
|<span data-ttu-id="d8350-115">Les [pages Razor](xref:mvc/razor-pages/index) constituent l’approche recommandée pour créer une interface utilisateur web avec ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="d8350-115">[Razor Pages](xref:mvc/razor-pages/index) is the recommended approach to create a Web UI with ASP.NET Core 2.0.</span></span> <span data-ttu-id="d8350-116">Consultez aussi [MVC](xref:mvc/overview) et [API web](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="d8350-116">See also [MVC](xref:mvc/overview) and [Web API](xref:tutorials/first-web-api)</span></span>|<span data-ttu-id="d8350-117">Utiliser [Web Forms](https://docs.microsoft.com/aspnet/web-forms), [SignalR](https://docs.microsoft.com/aspnet/signalr), [MVC](https://docs.microsoft.com/aspnet/mvc), [API Web](https://docs.microsoft.com/aspnet/web-api/), ou [Pages Web](https://docs.microsoft.com/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="d8350-117">Use [Web Forms](https://docs.microsoft.com/aspnet/web-forms), [SignalR](https://docs.microsoft.com/aspnet/signalr), [MVC](https://docs.microsoft.com/aspnet/mvc), [Web API](https://docs.microsoft.com/aspnet/web-api/), or [Web Pages](https://docs.microsoft.com/aspnet/web-pages)</span></span>|
|<span data-ttu-id="d8350-118">Plusieurs versions par machine</span><span class="sxs-lookup"><span data-stu-id="d8350-118">Multiple versions per machine</span></span>|<span data-ttu-id="d8350-119">Une seule version par machine</span><span class="sxs-lookup"><span data-stu-id="d8350-119">One version per machine</span></span>|
|<span data-ttu-id="d8350-120">Développer avec Visual Studio, [Visual Studio pour Mac](https://www.visualstudio.com/vs/visual-studio-mac/) ou [Visual Studio Code](https://code.visualstudio.com/) en utilisant C# ou F#</span><span class="sxs-lookup"><span data-stu-id="d8350-120">Develop with Visual Studio, [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="d8350-121">Développer avec Visual Studio en utilisant C#, VB ou F#</span><span class="sxs-lookup"><span data-stu-id="d8350-121">Develop with Visual Studio using C#, VB, or F#</span></span>|
|<span data-ttu-id="d8350-122">Performances supérieures à celles d’ASP.NET</span><span class="sxs-lookup"><span data-stu-id="d8350-122">Higher performance than ASP.NET</span></span>|<span data-ttu-id="d8350-123">Bonnes performances</span><span class="sxs-lookup"><span data-stu-id="d8350-123">Good performance</span></span>|
|[<span data-ttu-id="d8350-124">Choisir le runtime .NET Framework ou .NET Core</span><span class="sxs-lookup"><span data-stu-id="d8350-124">Choose .NET Framework or .NET Core runtime</span></span>](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)|<span data-ttu-id="d8350-125">Utiliser le runtime .NET Framework</span><span class="sxs-lookup"><span data-stu-id="d8350-125">Use .NET Framework runtime</span></span>|

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="d8350-126">Scénarios ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8350-126">ASP.NET Core scenarios</span></span>

<!-- update link to Razor Pages mvc movie series when done -->
* <span data-ttu-id="d8350-127">Les [pages Razor](xref:mvc/razor-pages/index) constituent l’approche recommandée pour créer une interface utilisateur web avec ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="d8350-127">[Razor Pages](xref:mvc/razor-pages/index) is the recommended approach to create a Web UI with ASP.NET Core 2.0.</span></span>
* [<span data-ttu-id="d8350-128">Sites web</span><span class="sxs-lookup"><span data-stu-id="d8350-128">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="d8350-129">API</span><span class="sxs-lookup"><span data-stu-id="d8350-129">APIs</span></span>](xref:tutorials/first-web-api)

## <a name="aspnet-scenarios"></a><span data-ttu-id="d8350-130">Scénarios ASP.NET</span><span class="sxs-lookup"><span data-stu-id="d8350-130">ASP.NET scenarios</span></span>

* [<span data-ttu-id="d8350-131">Sites web</span><span class="sxs-lookup"><span data-stu-id="d8350-131">Websites</span></span>](https://docs.microsoft.com/aspnet/mvc)
* [<span data-ttu-id="d8350-132">API</span><span class="sxs-lookup"><span data-stu-id="d8350-132">APIs</span></span>](https://docs.microsoft.com/aspnet/web-api)
* [<span data-ttu-id="d8350-133">Temps réel</span><span class="sxs-lookup"><span data-stu-id="d8350-133">Real-time</span></span>](https://docs.microsoft.com/aspnet/signalr)

## <a name="resources"></a><span data-ttu-id="d8350-134">Ressources</span><span class="sxs-lookup"><span data-stu-id="d8350-134">Resources</span></span>

* [<span data-ttu-id="d8350-135">Présentation d’ASP.NET</span><span class="sxs-lookup"><span data-stu-id="d8350-135">Introduction to ASP.NET</span></span>](https://docs.microsoft.com/aspnet/overview)
* [<span data-ttu-id="d8350-136">Présentation d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8350-136">Introduction to ASP.NET Core</span></span>](xref:index)

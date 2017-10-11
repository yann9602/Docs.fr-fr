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
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2017
---
# <a name="choose-between-aspnet-and-aspnet-core"></a><span data-ttu-id="28487-104">Choisir entre ASP.NET et ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28487-104">Choose between ASP.NET and ASP.NET Core</span></span> 

<span data-ttu-id="28487-105">Quelle que soit l’application web à créer, ASP.NET a une solution pour vous : depuis les applications web d’entreprise ciblant Windows Server aux microservices ciblant des conteneurs Linux, et tout ce qui se trouve entre les deux.</span><span class="sxs-lookup"><span data-stu-id="28487-105">No matter the web application you are creating, ASP.NET has a solution for you: from enterprise web applications targeting Windows Server, to small microservices targeting Linux containers, and everything in between.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="28487-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28487-106">ASP.NET Core</span></span>

<span data-ttu-id="28487-107">ASP.NET Core est un framework open source et multiplateforme pour générer des applications web cloud modernes sur Windows, macOS ou Linux.</span><span class="sxs-lookup"><span data-stu-id="28487-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web applications on Windows, macOS, or Linux.</span></span>

## <a name="aspnet"></a><span data-ttu-id="28487-108">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="28487-108">ASP.NET</span></span>

<span data-ttu-id="28487-109">ASP.NET est un framework mature qui offre tous les services nécessaires pour générer sur Windows des applications web d’entreprise basées sur des serveurs.</span><span class="sxs-lookup"><span data-stu-id="28487-109">ASP.NET is a mature framework that provides all the services needed to build enterprise-class, server-based web applications on Windows.</span></span>

## <a name="which-one-is-right-for-me"></a><span data-ttu-id="28487-110">Laquelle des deux est adaptée à mes besoins ?</span><span class="sxs-lookup"><span data-stu-id="28487-110">Which one is right for me?</span></span>

| <span data-ttu-id="28487-111">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28487-111">ASP.NET Core</span></span> | <span data-ttu-id="28487-112">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="28487-112">ASP.NET</span></span> |
|---|---|
|<span data-ttu-id="28487-113">Générer pour Windows, macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="28487-113">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="28487-114">Générer pour Windows</span><span class="sxs-lookup"><span data-stu-id="28487-114">Build for Windows</span></span>|
|<span data-ttu-id="28487-115">Les [pages Razor](xref:mvc/razor-pages/index) constituent l’approche recommandée pour créer une interface utilisateur web avec ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="28487-115">[Razor Pages](xref:mvc/razor-pages/index) is the recommended approach to create a Web UI with ASP.NET Core 2.0.</span></span> <span data-ttu-id="28487-116">Consultez aussi [MVC](xref:mvc/overview) et [API web](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="28487-116">See also [MVC](xref:mvc/overview) and [Web API](xref:tutorials/first-web-api)</span></span>|<span data-ttu-id="28487-117">Utiliser [Web Forms](https://docs.microsoft.com/aspnet/web-forms), [SignalR](https://docs.microsoft.com/aspnet/signalr), [MVC](https://docs.microsoft.com/aspnet/mvc), [API Web](https://docs.microsoft.com/aspnet/web-api/), ou [Pages Web](https://docs.microsoft.com/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="28487-117">Use [Web Forms](https://docs.microsoft.com/aspnet/web-forms), [SignalR](https://docs.microsoft.com/aspnet/signalr), [MVC](https://docs.microsoft.com/aspnet/mvc), [Web API](https://docs.microsoft.com/aspnet/web-api/), or [Web Pages](https://docs.microsoft.com/aspnet/web-pages)</span></span>|
|<span data-ttu-id="28487-118">Plusieurs versions par machine</span><span class="sxs-lookup"><span data-stu-id="28487-118">Multiple versions per machine</span></span>|<span data-ttu-id="28487-119">Une seule version par machine</span><span class="sxs-lookup"><span data-stu-id="28487-119">One version per machine</span></span>|
|<span data-ttu-id="28487-120">Développer avec Visual Studio, [Visual Studio pour Mac](https://www.visualstudio.com/vs/visual-studio-mac/) ou [Visual Studio Code](https://code.visualstudio.com/) en utilisant C# ou F#</span><span class="sxs-lookup"><span data-stu-id="28487-120">Develop with Visual Studio, [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="28487-121">Développer avec Visual Studio en utilisant C#, VB ou F#</span><span class="sxs-lookup"><span data-stu-id="28487-121">Develop with Visual Studio using C#, VB, or F#</span></span>|
|<span data-ttu-id="28487-122">Performances supérieures à celles d’ASP.NET</span><span class="sxs-lookup"><span data-stu-id="28487-122">Higher performance than ASP.NET</span></span>|<span data-ttu-id="28487-123">Bonnes performances</span><span class="sxs-lookup"><span data-stu-id="28487-123">Good performance</span></span>|
|[<span data-ttu-id="28487-124">Choisir le runtime .NET Framework ou .NET Core</span><span class="sxs-lookup"><span data-stu-id="28487-124">Choose .NET Framework or .NET Core runtime</span></span>](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)|<span data-ttu-id="28487-125">Utiliser le runtime .NET Framework</span><span class="sxs-lookup"><span data-stu-id="28487-125">Use .NET Framework runtime</span></span>|

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="28487-126">Scénarios ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28487-126">ASP.NET Core scenarios</span></span>

<!-- update link to Razor Pages mvc movie series when done -->
* <span data-ttu-id="28487-127">Les [pages Razor](xref:mvc/razor-pages/index) constituent l’approche recommandée pour créer une interface utilisateur web avec ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="28487-127">[Razor Pages](xref:mvc/razor-pages/index) is the recommended approach to create a Web UI with ASP.NET Core 2.0.</span></span>
* [<span data-ttu-id="28487-128">Sites web</span><span class="sxs-lookup"><span data-stu-id="28487-128">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="28487-129">API</span><span class="sxs-lookup"><span data-stu-id="28487-129">APIs</span></span>](xref:tutorials/first-web-api)

## <a name="aspnet-scenarios"></a><span data-ttu-id="28487-130">Scénarios ASP.NET</span><span class="sxs-lookup"><span data-stu-id="28487-130">ASP.NET scenarios</span></span>

* [<span data-ttu-id="28487-131">Sites web</span><span class="sxs-lookup"><span data-stu-id="28487-131">Websites</span></span>](https://docs.microsoft.com/aspnet/mvc)
* [<span data-ttu-id="28487-132">API</span><span class="sxs-lookup"><span data-stu-id="28487-132">APIs</span></span>](https://docs.microsoft.com/aspnet/web-api)
* [<span data-ttu-id="28487-133">Temps réel</span><span class="sxs-lookup"><span data-stu-id="28487-133">Real-time</span></span>](https://docs.microsoft.com/aspnet/signalr)

## <a name="resources"></a><span data-ttu-id="28487-134">Ressources</span><span class="sxs-lookup"><span data-stu-id="28487-134">Resources</span></span>

* [<span data-ttu-id="28487-135">Présentation d’ASP.NET</span><span class="sxs-lookup"><span data-stu-id="28487-135">Introduction to ASP.NET</span></span>](https://docs.microsoft.com/aspnet/overview)
* [<span data-ttu-id="28487-136">Présentation d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28487-136">Introduction to ASP.NET Core</span></span>](xref:index)

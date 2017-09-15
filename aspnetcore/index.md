---
title: "Présentation d’ASP.NET Core"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: index
ms.openlocfilehash: d8516643393cdf9175adc78ab98420dc1dc5d1d9
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="19370-103">Présentation d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19370-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="19370-104">Par [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="19370-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="19370-105">ASP.NET Core est un framework multiplateforme à hautes performances et [open source](https://github.com/aspnet/home) pour créer des applications cloud modernes et connectées à Internet.</span><span class="sxs-lookup"><span data-stu-id="19370-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="19370-106">Avec ASP.NET Core, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="19370-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="19370-107">Créer des applications et des services web, des applications IoT et des back-ends mobiles.</span><span class="sxs-lookup"><span data-stu-id="19370-107">Build web apps and services, IoT apps, and mobile backends.</span></span>
* <span data-ttu-id="19370-108">Utilisez vos outils de développement préférés sur Windows, macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="19370-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="19370-109">Déployer sur le cloud ou localement</span><span class="sxs-lookup"><span data-stu-id="19370-109">Deploy to the cloud or on-premises</span></span>
* <span data-ttu-id="19370-110">Exécuter sur [.NET Core ou .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="19370-110">Run on [.NET Core or .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-use-aspnet-core"></a><span data-ttu-id="19370-111">Pourquoi utiliser ASP.NET Core ?</span><span class="sxs-lookup"><span data-stu-id="19370-111">Why use ASP.NET Core?</span></span>

<span data-ttu-id="19370-112">Des millions de développeurs ont utilisé ASP.NET (et continuer de l’utiliser) pour créer des applications web.</span><span class="sxs-lookup"><span data-stu-id="19370-112">Millions of developers have used ASP.NET (and continue to use it) to create web apps.</span></span> <span data-ttu-id="19370-113">ASP.NET Core est une reconception d’ASP.NET, avec des modifications d’architecture qui aboutissent à un framework plus léger et modulaire.</span><span class="sxs-lookup"><span data-stu-id="19370-113">ASP.NET Core is a redesign of ASP.NET, with architectural changes that result in a leaner and modular framework.</span></span>

<span data-ttu-id="19370-114">ASP.NET Core offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="19370-114">ASP.NET Core provides the following benefits:</span></span>

* <span data-ttu-id="19370-115">Un scénario unifié pour créer une interface utilisateur web et des API web.</span><span class="sxs-lookup"><span data-stu-id="19370-115">A unified story for building web UI and web APIs.</span></span>
* <span data-ttu-id="19370-116">Intégration de [frameworks modernes côté client](xref:client-side/index) et de workflows de développement.</span><span class="sxs-lookup"><span data-stu-id="19370-116">Integration of [modern client-side frameworks](xref:client-side/index) and development workflows.</span></span>
* <span data-ttu-id="19370-117">Un [système de configuration](xref:fundamentals/configuration) prêt pour le cloud et basé sur les environnements.</span><span class="sxs-lookup"><span data-stu-id="19370-117">A cloud-ready, environment-based [configuration system](xref:fundamentals/configuration).</span></span>
* <span data-ttu-id="19370-118">[Injection de dépendances](xref:fundamentals/dependency-injection) intégrée.</span><span class="sxs-lookup"><span data-stu-id="19370-118">Built-in [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="19370-119">Un pipeline des requêtes HTTP léger, à hautes performances et modulaire.</span><span class="sxs-lookup"><span data-stu-id="19370-119">A lightweight, high-performance, and modular HTTP request pipeline.</span></span>
* <span data-ttu-id="19370-120">Capacité d’hébergement sur IIS ou d’auto-hébergement dans votre propre processus.</span><span class="sxs-lookup"><span data-stu-id="19370-120">Ability to host on IIS or self-host in your own process.</span></span>
* <span data-ttu-id="19370-121">Peut s’exécuter sur [.NET Core](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server), qui prend en charge une gestion réelle des versions des applications côte à côte.</span><span class="sxs-lookup"><span data-stu-id="19370-121">Can run on [.NET Core](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server), which supports true side-by-side app versioning.</span></span>
* <span data-ttu-id="19370-122">Outils qui simplifient le développement web moderne.</span><span class="sxs-lookup"><span data-stu-id="19370-122">Tooling that simplifies modern web development.</span></span>
* <span data-ttu-id="19370-123">Capacité à générer et à exécuter sur Windows, macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="19370-123">Ability to build and run on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="19370-124">Open source et centré sur la communauté.</span><span class="sxs-lookup"><span data-stu-id="19370-124">Open-source and community-focused.</span></span>

<span data-ttu-id="19370-125">ASP.NET Core est fourni entièrement sous forme de packages [NuGet](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="19370-125">ASP.NET Core ships entirely as [NuGet](https://www.nuget.org/) packages.</span></span> <span data-ttu-id="19370-126">Ceci vous permet d’optimiser votre application en y incluant seulement les packages NuGet dont vous avez besoin.</span><span class="sxs-lookup"><span data-stu-id="19370-126">This allows you to optimize your app to include just the NuGet packages you need.</span></span> <span data-ttu-id="19370-127">Les avantages des applications avec une surface d’exposition plus petite sont une sécurité accrue, une maintenance réduite et des performances améliorées.</span><span class="sxs-lookup"><span data-stu-id="19370-127">The benefits of a smaller app surface area include tighter security, reduced servicing, and improved performance.</span></span>

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="19370-128">Créer des API web et une interface utilisateur web en utilisant le modèle MVC d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19370-128">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="19370-129">Le modèle MVC d’ASP.NET Core fournit des fonctionnalités qui vous aident à créer des [API web](xref:tutorials/index#building-web-apis) et des [applications web](xref:tutorials/index#building-web-applications) :</span><span class="sxs-lookup"><span data-stu-id="19370-129">ASP.NET Core MVC provides features that help you build [web APIs](xref:tutorials/index#building-web-apis) and [web apps](xref:tutorials/index#building-web-applications):</span></span>

* <span data-ttu-id="19370-130">Le [modèle MVC (Modèle-Vue-Contrôleur)](xref:mvc/overview) permet de rendre vos API web et vos applications web [testables](testing/index.md).</span><span class="sxs-lookup"><span data-stu-id="19370-130">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps [testable](testing/index.md).</span></span>
* <span data-ttu-id="19370-131">[Razor Pages](xref:mvc/razor-pages/index) (nouveauté dans 2.0) est un modèle de programmation basé sur les pages qui rend plus facile et plus productive la création d’une interface utilisateur web.</span><span class="sxs-lookup"><span data-stu-id="19370-131">[Razor Pages](xref:mvc/razor-pages/index) (new in 2.0) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="19370-132">La [syntaxe Razor](xref:mvc/views/razor) fournit un langage efficace pour [Razor Pages](xref:mvc/razor-pages/index) et les [vues MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="19370-132">[Razor syntax](xref:mvc/views/razor) provides a productive language for [Razor Pages](xref:mvc/razor-pages/index) and [MVC Views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="19370-133">Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les fichiers Razor.</span><span class="sxs-lookup"><span data-stu-id="19370-133">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="19370-134">La prise en charge intégrée de [plusieurs formats de données et de la négociation de contenu](mvc/models/formatting.md) permet à vos API web d’être utilisées par un large éventail de clients, notamment des navigateurs et des appareils mobiles.</span><span class="sxs-lookup"><span data-stu-id="19370-134">Built-in support for [multiple data formats and content negotiation](mvc/models/formatting.md) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="19370-135">La [liaison de modèle](xref:mvc/models/model-binding) mappe automatiquement les données des requêtes HTTP aux paramètres des méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="19370-135">[Model Binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="19370-136">La [validation de modèle](xref:mvc/models/validation) effectue automatiquement la validation côté client et côté serveur.</span><span class="sxs-lookup"><span data-stu-id="19370-136">[Model Validation](xref:mvc/models/validation) automatically performs client and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="19370-137">Développement côté client</span><span class="sxs-lookup"><span data-stu-id="19370-137">Client-side development</span></span>

<span data-ttu-id="19370-138">ASP.NET Core est conçu pour s’intégrer parfaitement à différents frameworks côté client, notamment [AngularJS](xref:client-side/angular), [KnockoutJS](xref:client-side/knockout) et [Bootstrap](xref:client-side/bootstrap).</span><span class="sxs-lookup"><span data-stu-id="19370-138">ASP.NET Core is designed to integrate seamlessly with a variety of client-side frameworks, including [AngularJS](xref:client-side/angular), [KnockoutJS](xref:client-side/knockout), and [Bootstrap](xref:client-side/bootstrap).</span></span> <span data-ttu-id="19370-139">Pour plus d’informations, consultez [Développement côté client](client-side/index.md).</span><span class="sxs-lookup"><span data-stu-id="19370-139">See [Client-side development](client-side/index.md) for more details.</span></span>

## <a name="next-steps"></a><span data-ttu-id="19370-140">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="19370-140">Next steps</span></span>

<span data-ttu-id="19370-141">Pour plus d'informations, reportez-vous aux ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="19370-141">For more information, see the following resources:</span></span>

* [<span data-ttu-id="19370-142">Didacticiels ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19370-142">ASP.NET Core tutorials</span></span>](xref:tutorials/index)
* [<span data-ttu-id="19370-143">Notions de base d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19370-143">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="19370-144">[Le point hebdomadaire de la communauté ASP.NET](https://live.asp.net/) couvre l’avancement et les plans de l’équipe, et comprend de nouveaux blogs et des logiciels de tiers.</span><span class="sxs-lookup"><span data-stu-id="19370-144">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans and features new blogs and third-party software.</span></span>

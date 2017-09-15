---
title: "Utilisation des données dans ASP.NET Core"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a8fb7eb7-e0e5-4394-84f3-1f1dbe0ba2ef
ms.technology: aspnet
ms.prod: asp.net-core
ms.openlocfilehash: 3566127476289ae085a9161132b103638bc9b068
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="working-with-data-in-aspnet-core"></a><span data-ttu-id="bca51-103">Utilisation des données dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bca51-103">Working with Data in ASP.NET Core</span></span> 

*   [<span data-ttu-id="bca51-104">Bien démarrer avec ASP.NET Core et Entity Framework Core à l’aide de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bca51-104">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](ef-mvc/index.md)
    *   [<span data-ttu-id="bca51-105">Prise en main</span><span class="sxs-lookup"><span data-stu-id="bca51-105">Getting started</span></span>](ef-mvc/intro.md)
    *   [<span data-ttu-id="bca51-106">Opérations de création, de lecture, de mise à jour et de suppression</span><span class="sxs-lookup"><span data-stu-id="bca51-106">Create, Read, Update, and Delete operations</span></span>](ef-mvc/crud.md)
    *   [<span data-ttu-id="bca51-107">Tri, filtrage, pagination et regroupement</span><span class="sxs-lookup"><span data-stu-id="bca51-107">Sorting, filtering, paging, and grouping</span></span>](ef-mvc/sort-filter-page.md)
    *   [<span data-ttu-id="bca51-108">Migrations</span><span class="sxs-lookup"><span data-stu-id="bca51-108">Migrations</span></span>](ef-mvc/migrations.md)
    *   [<span data-ttu-id="bca51-109">Création d’un modèle de données complexe</span><span class="sxs-lookup"><span data-stu-id="bca51-109">Creating a complex data model</span></span>](ef-mvc/complex-data-model.md)
    *   [<span data-ttu-id="bca51-110">Lecture de données associées</span><span class="sxs-lookup"><span data-stu-id="bca51-110">Reading related data</span></span>](ef-mvc/read-related-data.md)
    *   [<span data-ttu-id="bca51-111">Mise à jour de données associées</span><span class="sxs-lookup"><span data-stu-id="bca51-111">Updating related data</span></span>](ef-mvc/update-related-data.md)
    *   [<span data-ttu-id="bca51-112">Gestion des conflits d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="bca51-112">Handling concurrency conflicts</span></span>](ef-mvc/concurrency.md)
    *   [<span data-ttu-id="bca51-113">Héritage</span><span class="sxs-lookup"><span data-stu-id="bca51-113">Inheritance</span></span>](ef-mvc/inheritance.md)
    *   [<span data-ttu-id="bca51-114">Rubriques avancées</span><span class="sxs-lookup"><span data-stu-id="bca51-114">Advanced topics</span></span>](ef-mvc/advanced.md)
* <span data-ttu-id="bca51-115">[ASP.NET Core avec EF Core - nouvelle base de données](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) (site de documentation d’Entity Framework Core)</span><span class="sxs-lookup"><span data-stu-id="bca51-115">[ASP.NET Core with EF Core - new database](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) (Entity Framework Core documentation site)</span></span>
* <span data-ttu-id="bca51-116">[ASP.NET Core avec EF Core - base de données existante](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db) (site de documentation d’Entity Framework Core)</span><span class="sxs-lookup"><span data-stu-id="bca51-116">[ASP.NET Core with EF Core - existing database](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db) (Entity Framework Core documentation site)</span></span>
*   [<span data-ttu-id="bca51-117">Bien démarrer avec ASP.NET Core et Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="bca51-117">Getting Started with ASP.NET Core and Entity Framework 6</span></span>](entity-framework-6.md)
*   [<span data-ttu-id="bca51-118">Stockage Azure</span><span class="sxs-lookup"><span data-stu-id="bca51-118">Azure Storage</span></span>](azure-storage/index.md)
    *   [<span data-ttu-id="bca51-119">Ajout de Stockage Azure à l’aide des services connectés de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bca51-119">Adding Azure Storage by Using Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-azure-tools-connected-services-storage/)
    *   [<span data-ttu-id="bca51-120">Prendre en main Stockage Blob Azure et les services connectés de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bca51-120">Get Started with Azure Blob storage and Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
    *   [<span data-ttu-id="bca51-121">Bien démarrer avec Stockage File d’attente et les services connectés de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bca51-121">Get Started with Queue Storage and Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
    *   [<span data-ttu-id="bca51-122">Mise en route avec Stockage Table Azure et les services connectés de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bca51-122">How to Get Started with Azure Table Storage and Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

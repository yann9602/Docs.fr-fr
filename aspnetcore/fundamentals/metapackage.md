---
title: "Metapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.x et versions ultérieures"
author: Rick-Anderson
description: "Le metapackage Microsoft.AspNetCore.All inclut tous les packages ASP.NET Core et Entity Framework Core, ainsi que leurs dépendances."
keywords: ASP.NET Core,NuGet,package,Microsoft.AspNetCore.All,metapackage
ms.author: riande
manager: wpickett
ms.date: 09/20/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/metapackage
ms.openlocfilehash: 23a07867874eb534c75c4e7b3be00c4a376f8a8b
ms.sourcegitcommit: 4e45fd4e3f1374cd51cc931cee93c9d72631d9fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2017
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a><span data-ttu-id="076c4-104">Metapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="076c4-104">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x</span></span>

<span data-ttu-id="076c4-105">Cette fonctionnalité nécessite ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="076c4-105">This feature requires ASP.NET Core 2.x.</span></span>

<span data-ttu-id="076c4-106">Le [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) metapackage pour ASP.NET Core inclut :</span><span class="sxs-lookup"><span data-stu-id="076c4-106">The [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) metapackage for ASP.NET Core includes:</span></span>

* <span data-ttu-id="076c4-107">Tous les packages pris en charge par l’équipe ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="076c4-107">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="076c4-108">Tous les packages par l’Entity Framework Core pris en charge.</span><span class="sxs-lookup"><span data-stu-id="076c4-108">All supported packages by the Entity Framework Core.</span></span> 
* <span data-ttu-id="076c4-109">Dépendances internes et 3 rd-party utilisés par ASP.NET Core et Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="076c4-109">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span> 

<span data-ttu-id="076c4-110">Toutes les fonctionnalités ASP.NET Core 2.x et Entity Framework Core 2.x sont inclus dans le `Microsoft.AspNetCore.All` package.</span><span class="sxs-lookup"><span data-stu-id="076c4-110">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="076c4-111">Les modèles de projet par défaut utilisent ce package.</span><span class="sxs-lookup"><span data-stu-id="076c4-111">The default project templates use this package.</span></span>

<span data-ttu-id="076c4-112">Le numéro de version de la `Microsoft.AspNetCore.All` metapackage représente la version de ASP.NET Core et Entity Framework Core (aligné avec la version de .NET Core).</span><span class="sxs-lookup"><span data-stu-id="076c4-112">The version number of the `Microsoft.AspNetCore.All` metapackage represents the ASP.NET Core version and Entity Framework Core version (aligned with the .NET Core version).</span></span>

<span data-ttu-id="076c4-113">Les applications qui utilisent la `Microsoft.AspNetCore.All` metapackage tirer parti automatiquement de la [magasin du Runtime .NET Core](https://docs.microsoft.com/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="076c4-113">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the [.NET Core Runtime Store](https://docs.microsoft.com/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="076c4-114">Le magasin de Runtime contient tous les composants runtime nécessaires à l’exécution des applications 2.x ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="076c4-114">The Runtime Store contains all the runtime assets needed to run ASP.NET Core 2.x applications.</span></span> <span data-ttu-id="076c4-115">Lorsque vous utilisez la `Microsoft.AspNetCore.All` metapackage, **aucun** des ressources depuis les packages ASP.NET Core NuGet référencés sont déployés avec l’application &mdash; le magasin du Runtime .NET Core contient ces ressources.</span><span class="sxs-lookup"><span data-stu-id="076c4-115">When you use the `Microsoft.AspNetCore.All` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application &mdash; the .NET Core Runtime Store contains these assets.</span></span> <span data-ttu-id="076c4-116">Les éléments multimédias dans le magasin d’exécution sont précompilés pour améliorer les temps de démarrage d’application.</span><span class="sxs-lookup"><span data-stu-id="076c4-116">The assets in the Runtime Store are precompiled to improve application startup time.</span></span>

<span data-ttu-id="076c4-117">Vous pouvez utiliser le processus de suppression du package à supprimer les packages que vous n’utilisez pas.</span><span class="sxs-lookup"><span data-stu-id="076c4-117">You can use the package trimming process to remove packages that you don't use.</span></span> <span data-ttu-id="076c4-118">Packages découpés sont exclus de la sortie de l’application publiée.</span><span class="sxs-lookup"><span data-stu-id="076c4-118">Trimmed packages are excluded in published application output.</span></span>

<span data-ttu-id="076c4-119">Les éléments suivants *.csproj* références de fichiers le `Microsoft.AspNetCore.All` metapackage pour ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="076c4-119">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]

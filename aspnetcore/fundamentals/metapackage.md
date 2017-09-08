---
title: "Metapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.x et versions ultérieures"
author: Rick-Anderson
description: Microsoft.AspNetCore.All metapackage inclut tous les packages.
keywords: "ASP.NET Core, NuGet, créez un package, Microsoft.AspNetCore.All, metapackage"
ms.author: riande
manager: wpickett
ms.date: 07/16/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/metapackage
ms.openlocfilehash: 255438a4ce36ce4978f8c8ee298388a25ac00d17
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a><span data-ttu-id="bdd35-104">Metapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bdd35-104">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x</span></span>

<span data-ttu-id="bdd35-105">Cette fonctionnalité nécessite ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="bdd35-105">This feature requires ASP.NET Core 2.x.</span></span>

<span data-ttu-id="bdd35-106">Le [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) metapackage pour ASP.NET Core inclut :</span><span class="sxs-lookup"><span data-stu-id="bdd35-106">The [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) metapackage for ASP.NET Core includes:</span></span>

* <span data-ttu-id="bdd35-107">Tous les packages pris en charge par l’équipe ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd35-107">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="bdd35-108">Tous les packages par l’Entity Framework Core pris en charge.</span><span class="sxs-lookup"><span data-stu-id="bdd35-108">All supported packages by the Entity Framework Core.</span></span> 
* <span data-ttu-id="bdd35-109">Dépendances internes et 3 rd-party utilisés par ASP.NET Core et Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="bdd35-109">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span> 

<span data-ttu-id="bdd35-110">Toutes les fonctionnalités ASP.NET Core 2.x et Entity Framework Core 2.x sont inclus dans le `Microsoft.AspNetCore.All` package.</span><span class="sxs-lookup"><span data-stu-id="bdd35-110">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="bdd35-111">Les modèles de projet par défaut utilisent ce package.</span><span class="sxs-lookup"><span data-stu-id="bdd35-111">The default project templates use this package.</span></span>

<span data-ttu-id="bdd35-112">Le numéro de version de la `Microsoft.AspNetCore.All` metapackage représente la version de ASP.NET Core et Entity Framework Core (aligné avec la version de .NET Core).</span><span class="sxs-lookup"><span data-stu-id="bdd35-112">The version number of the `Microsoft.AspNetCore.All` metapackage represents the ASP.NET Core version and Entity Framework Core version (aligned with the .NET Core version).</span></span>

<span data-ttu-id="bdd35-113">Les applications qui utilisent le `Microsoft.AspNetCore.All` metapackage tirer parti automatiquement de la banque de Runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd35-113">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the .NET Core Runtime Store.</span></span> <span data-ttu-id="bdd35-114">Le magasin de Runtime contient tous les composants runtime nécessaires à l’exécution des applications 2.x ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd35-114">The Runtime Store contains all the runtime assets needed to run ASP.NET Core 2.x applications.</span></span> <span data-ttu-id="bdd35-115">Lorsque vous utilisez la `Microsoft.AspNetCore.All` metapackage, **aucun** des ressources depuis les packages ASP.NET Core NuGet référencés sont déployés avec l’application &mdash; le magasin du Runtime .NET Core contient ces ressources.</span><span class="sxs-lookup"><span data-stu-id="bdd35-115">When you use the `Microsoft.AspNetCore.All` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application &mdash; the .NET Core Runtime Store contains these assets.</span></span> <span data-ttu-id="bdd35-116"><!-- todo add link to Runtime store -->Les éléments multimédias dans le magasin d’exécution sont précompilés pour améliorer les temps de démarrage d’application.</span><span class="sxs-lookup"><span data-stu-id="bdd35-116"><!-- todo add link to Runtime store --> The assets in the Runtime Store are precompiled to improve application startup time.</span></span>

<span data-ttu-id="bdd35-117">Vous pouvez utiliser le processus de suppression du package à supprimer les packages que vous n’utilisez pas.</span><span class="sxs-lookup"><span data-stu-id="bdd35-117">You can use the package trimming process to remove packages that you don't use.</span></span> <span data-ttu-id="bdd35-118">Packages découpés sont exclus de la sortie de l’application publiée.</span><span class="sxs-lookup"><span data-stu-id="bdd35-118">Trimmed packages are excluded in published application output.</span></span>

<span data-ttu-id="bdd35-119">Les éléments suivants *.csproj* références de fichiers le `Microsoft.AspNetCore.All` metapackage pour ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="bdd35-119">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

<span data-ttu-id="bdd35-120">[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]</span><span class="sxs-lookup"><span data-stu-id="bdd35-120">[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]</span></span>

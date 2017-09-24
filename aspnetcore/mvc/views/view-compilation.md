---
title: "La précompilation et compilation de vue razor"
author: rick-anderson
description: "Un document de référence expliquant comment activer la compilation de vue MVC Razor et précompilation dans les applications ASP.NET Core."
keywords: "Compilation de vue Razor, Razor antérieur à la compilation, la précompilation de Razor ASP.NET Core"
ms.author: riande
manager: wpickett
ms.date: 08/16/2017
ms.topic: article
ms.assetid: ab4705b7-1638-1638-bc97-ea7f292fe92a
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-compilation
ms.openlocfilehash: bfee2e5e8f71c99465be79589a77f0e173097b23
ms.sourcegitcommit: 8005eb4051e568d88ee58d48424f39916052e6e2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2017
---
# <a name="razor-view-compilation-and-precompilation-in-aspnet-core"></a><span data-ttu-id="3a199-104">Compilation de vue Razor et précompilation dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a199-104">Razor view compilation and precompilation in ASP.NET Core</span></span>

<span data-ttu-id="3a199-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3a199-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3a199-106">Vues Razor sont compilées lors de l’exécution lorsque la vue est appelée.</span><span class="sxs-lookup"><span data-stu-id="3a199-106">Razor views are compiled at runtime when the view is invoked.</span></span> <span data-ttu-id="3a199-107">ASP.NET Core 1.1.0 et une valeur supérieure peut éventuellement compiler vues Razor et les déployer avec l’application &mdash; processus s’appelé la précompilation.</span><span class="sxs-lookup"><span data-stu-id="3a199-107">ASP.NET Core 1.1.0 and higher can optionally compile Razor views and deploy them with the app &mdash; a process known as precompilation.</span></span> <span data-ttu-id="3a199-108">Les modèles de projet ASP.NET Core 2.x activent la précompilation par défaut.</span><span class="sxs-lookup"><span data-stu-id="3a199-108">The ASP.NET Core 2.x project templates enable precompilation by default.</span></span>

> [!NOTE]
> <span data-ttu-id="3a199-109">La précompilation de vue Razor est actuellement pas disponible lorsque vous effectuez un [déploiement autonome (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) dans ASP.NET 2.0 de base.</span><span class="sxs-lookup"><span data-stu-id="3a199-109">Razor view precompilation is currently unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span> <span data-ttu-id="3a199-110">La fonctionnalité sera disponible pour les dimensions à variation lente lorsque 2.1 relâche.</span><span class="sxs-lookup"><span data-stu-id="3a199-110">The feature will be available for SCDs when 2.1 releases.</span></span> <span data-ttu-id="3a199-111">Pour plus d’informations, consultez [la compilation de la vue échoue lors de la compilation croisée pour Linux sur Windows](https://github.com/aspnet/MvcPrecompilation/issues/102).</span><span class="sxs-lookup"><span data-stu-id="3a199-111">For more information, see [View compilation fails when cross-compiling for Linux on Windows](https://github.com/aspnet/MvcPrecompilation/issues/102).</span></span>

<span data-ttu-id="3a199-112">Considérations relatives à la précompilation :</span><span class="sxs-lookup"><span data-stu-id="3a199-112">Precompilation considerations:</span></span>

* <span data-ttu-id="3a199-113">Résultats de la précompilation des vues dans un plus petit groupe de publié et le démarrage plus rapide.</span><span class="sxs-lookup"><span data-stu-id="3a199-113">Precompiling views results in a smaller published bundle and faster startup time.</span></span>
* <span data-ttu-id="3a199-114">Vous ne pouvez pas modifier les fichiers Razor précompiler de vues.</span><span class="sxs-lookup"><span data-stu-id="3a199-114">You can't edit Razor files after you precompile views.</span></span> <span data-ttu-id="3a199-115">Les vues modifiés sera présents dans l’application publiée.</span><span class="sxs-lookup"><span data-stu-id="3a199-115">The edited views won't be present in the published bundle.</span></span> 

<span data-ttu-id="3a199-116">Pour déployer des vues précompilés :</span><span class="sxs-lookup"><span data-stu-id="3a199-116">To deploy precompiled views:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="3a199-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="3a199-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="3a199-118">Si votre projet cible .NET Framework, inclure une référence de package à `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`:</span><span class="sxs-lookup"><span data-stu-id="3a199-118">If your project targets .NET Framework, include a package reference to `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.ViewCompilation" Version="2.0.0" PrivateAssets="All" />
```

<span data-ttu-id="3a199-119">Si votre projet cible .NET Core, aucune modification n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="3a199-119">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="3a199-120">Les modèles de projet ASP.NET Core 2.x implicitement la valeur `MvcRazorCompileOnPublish` à `true` par défaut, ce qui signifie que ce nœud peut être supprimé en toute sécurité le *.csproj* fichier.</span><span class="sxs-lookup"><span data-stu-id="3a199-120">The ASP.NET Core 2.x project templates implicitly set `MvcRazorCompileOnPublish` to `true` by default, which means this node can be safely removed from the *.csproj* file.</span></span> <span data-ttu-id="3a199-121">Si vous préférez être explicite, il n’existe aucun risque dans le paramètre de la `MvcRazorCompileOnPublish` propriété `true`.</span><span class="sxs-lookup"><span data-stu-id="3a199-121">If you prefer to be explicit, there's no harm in setting the `MvcRazorCompileOnPublish` property to `true`.</span></span> <span data-ttu-id="3a199-122">Les éléments suivants *.csproj* exemple met en surbrillance ce paramètre :</span><span class="sxs-lookup"><span data-stu-id="3a199-122">The following *.csproj* sample highlights this setting:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="3a199-123">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="3a199-123">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="3a199-124">Définissez `MvcRazorCompileOnPublish` à `true`et inclure une référence de package à `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`.</span><span class="sxs-lookup"><span data-stu-id="3a199-124">Set `MvcRazorCompileOnPublish` to `true`, and include a package reference to `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`.</span></span> <span data-ttu-id="3a199-125">Les éléments suivants *.csproj* exemple met en évidence ces paramètres :</span><span class="sxs-lookup"><span data-stu-id="3a199-125">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]

---

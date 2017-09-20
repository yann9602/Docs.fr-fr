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
ms.openlocfilehash: 0cb61315916d1b38f7cab3339e150c446fb69d98
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2017
---
# <a name="razor-view-compilation-and-precompilation-in-aspnet-core"></a><span data-ttu-id="d7242-104">Compilation de vue Razor et précompilation dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7242-104">Razor view compilation and precompilation in ASP.NET Core</span></span>

<span data-ttu-id="d7242-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d7242-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d7242-106">Vues Razor sont compilées lors de l’exécution lorsque la vue est appelée.</span><span class="sxs-lookup"><span data-stu-id="d7242-106">Razor views are compiled at runtime when the view is invoked.</span></span> <span data-ttu-id="d7242-107">ASP.NET Core 1.1.0 et une valeur supérieure peut éventuellement compiler vues Razor et les déployer avec l’application &mdash; processus s’appelé la précompilation.</span><span class="sxs-lookup"><span data-stu-id="d7242-107">ASP.NET Core 1.1.0 and higher can optionally compile Razor views and deploy them with the app &mdash; a process known as precompilation.</span></span> <span data-ttu-id="d7242-108">Les modèles de projet ASP.NET Core 2.x activent la précompilation par défaut.</span><span class="sxs-lookup"><span data-stu-id="d7242-108">The ASP.NET Core 2.x project templates enable precompilation by default.</span></span>

> [!NOTE]
> <span data-ttu-id="d7242-109">La précompilation de vue Razor n’est pas disponible lorsque vous effectuez un [Self-Contained déploiement](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dans les versions de ASP.NET Core 2.0.0 et les versions antérieures.</span><span class="sxs-lookup"><span data-stu-id="d7242-109">Razor view precompilation is unavailable when doing a [Self-Contained Deployment](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core versions 2.0.0 and earlier.</span></span>

<span data-ttu-id="d7242-110">Considérations relatives à la précompilation :</span><span class="sxs-lookup"><span data-stu-id="d7242-110">Precompilation considerations:</span></span>

* <span data-ttu-id="d7242-111">Résultats de la précompilation des vues dans un plus petit groupe de publié et le démarrage plus rapide.</span><span class="sxs-lookup"><span data-stu-id="d7242-111">Precompiling views results in a smaller published bundle and faster startup time.</span></span>
* <span data-ttu-id="d7242-112">Vous ne pouvez pas modifier les fichiers Razor précompiler de vues.</span><span class="sxs-lookup"><span data-stu-id="d7242-112">You can't edit Razor files after you precompile views.</span></span> <span data-ttu-id="d7242-113">Les vues modifiés sera présents dans l’application publiée.</span><span class="sxs-lookup"><span data-stu-id="d7242-113">The edited views won't be present in the published bundle.</span></span> 

<span data-ttu-id="d7242-114">Pour déployer des vues précompilés :</span><span class="sxs-lookup"><span data-stu-id="d7242-114">To deploy precompiled views:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="d7242-115">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d7242-115">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="d7242-116">Si votre projet cible .NET Framework, inclure une référence de package à `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`:</span><span class="sxs-lookup"><span data-stu-id="d7242-116">If your project targets .NET Framework, include a package reference to `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.ViewCompilation" Version="2.0.0" PrivateAssets="All" />
```

<span data-ttu-id="d7242-117">Si votre projet cible .NET Core, aucune modification n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="d7242-117">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="d7242-118">Les modèles de projet ASP.NET Core 2.x implicitement la valeur `MvcRazorCompileOnPublish` à `true` par défaut, ce qui signifie que ce nœud peut être supprimé en toute sécurité le *.csproj* fichier.</span><span class="sxs-lookup"><span data-stu-id="d7242-118">The ASP.NET Core 2.x project templates implicitly set `MvcRazorCompileOnPublish` to `true` by default, which means this node can be safely removed from the *.csproj* file.</span></span> <span data-ttu-id="d7242-119">Si vous préférez être explicite, il n’existe aucun risque dans le paramètre de la `MvcRazorCompileOnPublish` propriété `true`.</span><span class="sxs-lookup"><span data-stu-id="d7242-119">If you prefer to be explicit, there's no harm in setting the `MvcRazorCompileOnPublish` property to `true`.</span></span> <span data-ttu-id="d7242-120">Les éléments suivants *.csproj* exemple met en surbrillance ce paramètre :</span><span class="sxs-lookup"><span data-stu-id="d7242-120">The following *.csproj* sample highlights this setting:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="d7242-121">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="d7242-121">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="d7242-122">Définissez `MvcRazorCompileOnPublish` à `true`et inclure une référence de package à `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`.</span><span class="sxs-lookup"><span data-stu-id="d7242-122">Set `MvcRazorCompileOnPublish` to `true`, and include a package reference to `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`.</span></span> <span data-ttu-id="d7242-123">Les éléments suivants *.csproj* exemple met en évidence ces paramètres :</span><span class="sxs-lookup"><span data-stu-id="d7242-123">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]

---

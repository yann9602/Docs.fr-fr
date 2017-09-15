---
title: "Migration d’ASP.NET Core 1.x vers la version 2.0"
author: scottaddie
description: "Cet article présente les prérequis et la plupart des étapes courantes nécessaires pour la migration d’un projet ASP.NET Core 1.x vers ASP.NET Core 2.0."
keywords: ASP.NET Core, migration
ms.author: scaddie
manager: wpickett
ms.date: 08/01/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/1x-to-2x/index
ms.openlocfilehash: db277ee6b079eab973a565983d6661bf95fce2e3
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="migrating-from-aspnet-core-1x-to-aspnet-core-20"></a><span data-ttu-id="57d86-104">Migration d’ASP.NET Core 1.x vers ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="57d86-104">Migrating from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>

<span data-ttu-id="57d86-105">Par [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="57d86-105">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="57d86-106">Dans cet article, nous allons vous montrer comment mettre à jour un projet ASP.NET Core 1.x existant vers ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="57d86-106">In this article, we'll walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="57d86-107">La migration de votre application vers ASP.NET Core 2.0 vous permet de tirer parti de [nombreuses nouvelles fonctionnalités et améliorations des performances](https://docs.microsoft.com/aspnet/core/aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="57d86-107">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](https://docs.microsoft.com/aspnet/core/aspnetcore-2.0).</span></span> 

<span data-ttu-id="57d86-108">Les applications ASP.NET Core 1.x existantes sont basées sur des modèles de projet propres à la version.</span><span class="sxs-lookup"><span data-stu-id="57d86-108">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="57d86-109">Les modèles de projet et le code de démarrage qu’ils contiennent évoluent en même temps que le framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57d86-109">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="57d86-110">Outre le framework ASP.NET Core, vous devez aussi mettre à jour le code de votre application.</span><span class="sxs-lookup"><span data-stu-id="57d86-110">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="57d86-111">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="57d86-111">Prerequisites</span></span>
<span data-ttu-id="57d86-112">Consultez [Bien démarrer avec ASP.NET Core](xref:getting-started).</span><span class="sxs-lookup"><span data-stu-id="57d86-112">Please see [Getting Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="57d86-113">Mettre à jour le Moniker du Framework cible</span><span class="sxs-lookup"><span data-stu-id="57d86-113">Update Target Framework Moniker (TFM)</span></span>
<span data-ttu-id="57d86-114">Les projets ciblant .NET Core doivent utiliser le [Moniker du Framework cible](/dotnet/standard/frameworks#referring-to-frameworks) d’une version supérieure ou égale à .NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="57d86-114">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks#referring-to-frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="57d86-115">Recherchez le nœud `<TargetFramework>` dans le fichier *.csproj* et remplacez son texte interne par `netcoreapp2.0` :</span><span class="sxs-lookup"><span data-stu-id="57d86-115">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

<span data-ttu-id="57d86-116">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=3)]</span><span class="sxs-lookup"><span data-stu-id="57d86-116">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=3)]</span></span>

<span data-ttu-id="57d86-117">Les projets ciblant le .NET Framework doivent utiliser le Moniker du Framework cible d’une version supérieure ou égale à .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="57d86-117">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="57d86-118">Recherchez le nœud `<TargetFramework>` dans le fichier *.csproj* et remplacez son texte interne par `net461` :</span><span class="sxs-lookup"><span data-stu-id="57d86-118">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

<span data-ttu-id="57d86-119">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]</span><span class="sxs-lookup"><span data-stu-id="57d86-119">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]</span></span>

> [!NOTE]
> <span data-ttu-id="57d86-120">.NET Core 2.0 offre une surface d’exposition beaucoup plus grande que .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="57d86-120">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="57d86-121">Si vous ciblez le .NET Framework uniquement car il manque des API dans .NET Core 1.x, le ciblage de .NET Core 2.0 fonctionnera sans doute.</span><span class="sxs-lookup"><span data-stu-id="57d86-121">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="57d86-122">Mettre à jour la version du SDK .NET Core dans global.json</span><span class="sxs-lookup"><span data-stu-id="57d86-122">Update .NET Core SDK version in global.json</span></span>
<span data-ttu-id="57d86-123">Si votre solution s’appuie sur un fichier [*global.json*](https://docs.microsoft.com/dotnet/core/tools/global-json) pour cibler une version spécifique du SDK .NET Core, mettez à jour sa propriété `version` de façon à utiliser la version 2.0 installée sur votre ordinateur :</span><span class="sxs-lookup"><span data-stu-id="57d86-123">If your solution relies upon a [*global.json*](https://docs.microsoft.com/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

<span data-ttu-id="57d86-124">[!code-json[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/global.json?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="57d86-124">[!code-json[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/global.json?highlight=3)]</span></span>

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="57d86-125">Mettre à jour les références de package</span><span class="sxs-lookup"><span data-stu-id="57d86-125">Update package references</span></span>
<span data-ttu-id="57d86-126">Le fichier *.csproj* dans un projet 1.x répertorie chaque package NuGet utilisé par le projet.</span><span class="sxs-lookup"><span data-stu-id="57d86-126">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="57d86-127">Dans un projet ASP.NET Core 2.0 ciblant .NET Core 2.0, une seule référence de [métapackage](xref:fundamentals/metapackage) dans le fichier *.csproj* remplace la collection de packages :</span><span class="sxs-lookup"><span data-stu-id="57d86-127">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

<span data-ttu-id="57d86-128">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=9-11)]</span><span class="sxs-lookup"><span data-stu-id="57d86-128">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=9-11)]</span></span>

<span data-ttu-id="57d86-129">Toutes les fonctionnalités d’ASP.NET Core 2.0 et d’Entity Framework Core 2.0 sont incluses dans le métapackage.</span><span class="sxs-lookup"><span data-stu-id="57d86-129">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="57d86-130">Les projets ASP.NET Core 2.0 ciblant le .NET Framework doivent continuer à référencer des packages NuGet spécifiques.</span><span class="sxs-lookup"><span data-stu-id="57d86-130">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="57d86-131">Mettez à jour l’attribut `Version` de chaque nœud `<PackageReference />` vers la version 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="57d86-131">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="57d86-132">Par exemple, voici la liste des nœuds `<PackageReference />` utilisés dans un projet ASP.NET Core 2.0 standard qui cible le .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="57d86-132">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

<span data-ttu-id="57d86-133">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]</span><span class="sxs-lookup"><span data-stu-id="57d86-133">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]</span></span>

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="57d86-134">Mettre à jour les outils CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="57d86-134">Update .NET Core CLI tools</span></span>
<span data-ttu-id="57d86-135">Dans le fichier *.csproj*, mettez à jour l’attribut `Version` de chaque nœud `<DotNetCliToolReference />` vers la version 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="57d86-135">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="57d86-136">Par exemple, voici la liste des outils CLI utilisés dans un projet ASP.NET Core 2.0 standard qui cible .NET Core 2.0 :</span><span class="sxs-lookup"><span data-stu-id="57d86-136">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

<span data-ttu-id="57d86-137">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=13-17)]</span><span class="sxs-lookup"><span data-stu-id="57d86-137">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=13-17)]</span></span>

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="57d86-138">Renommer la propriété PackageTargetFallback</span><span class="sxs-lookup"><span data-stu-id="57d86-138">Rename Package Target Fallback property</span></span>
<span data-ttu-id="57d86-139">Le fichier *.csproj* d’un projet 1.x utilise un nœud et une variable `PackageTargetFallback` :</span><span class="sxs-lookup"><span data-stu-id="57d86-139">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

<span data-ttu-id="57d86-140">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=5)]</span><span class="sxs-lookup"><span data-stu-id="57d86-140">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=5)]</span></span>

<span data-ttu-id="57d86-141">Renommez le nœud et la variable `AssetTargetFallback` :</span><span class="sxs-lookup"><span data-stu-id="57d86-141">Rename both the node and variable to `AssetTargetFallback`:</span></span>

<span data-ttu-id="57d86-142">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=5)]</span><span class="sxs-lookup"><span data-stu-id="57d86-142">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=5)]</span></span>

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="57d86-143">Mettre à jour la méthode Main dans Program.cs</span><span class="sxs-lookup"><span data-stu-id="57d86-143">Update Main method in Program.cs</span></span>
<span data-ttu-id="57d86-144">Dans les projets 1.x, la méthode `Main` de *Program.cs* ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="57d86-144">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

<span data-ttu-id="57d86-145">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]</span><span class="sxs-lookup"><span data-stu-id="57d86-145">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]</span></span>

<span data-ttu-id="57d86-146">Dans les projets 2.0, la méthode `Main` de *Program.cs* a été simplifiée :</span><span class="sxs-lookup"><span data-stu-id="57d86-146">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

<span data-ttu-id="57d86-147">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Program.cs?highlight=8-11)]</span><span class="sxs-lookup"><span data-stu-id="57d86-147">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Program.cs?highlight=8-11)]</span></span>

<span data-ttu-id="57d86-148">L’adoption de ce nouveau modèle 2.0 est vivement recommandée, et elle est obligatoire pour que des fonctionnalités de produit telles que les [migrations Entity Framework Core](xref:data/ef-mvc/migrations) fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="57d86-148">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="57d86-149">Par exemple, l’exécution de `Update-Database` à partir de la fenêtre de la Console du Gestionnaire de Package ou de `dotnet ef database update` à partir de la ligne de commande (sur des projets convertis vers ASP.NET Core 2.0) génère l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="57d86-149">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="view-compilation"></a>

## <a name="review-your-razor-view-compilation-setting"></a><span data-ttu-id="57d86-150">Passer en revue votre paramètre de compilation de vue Razor</span><span class="sxs-lookup"><span data-stu-id="57d86-150">Review your Razor View Compilation setting</span></span>
<span data-ttu-id="57d86-151">La vitesse de démarrage d’application et la taille des bundles publiés ont une importance capitale pour vous.</span><span class="sxs-lookup"><span data-stu-id="57d86-151">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="57d86-152">Pour ces raisons, la [compilation de vue Razor](xref:mvc/views/view-compilation) est activée par défaut dans ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="57d86-152">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="57d86-153">L’affectation de la valeur true à la propriété `MvcRazorCompileOnPublish` n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="57d86-153">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="57d86-154">Vous pouvez supprimer la propriété du fichier *.csproj*, sauf si vous désactivez la compilation des vues.</span><span class="sxs-lookup"><span data-stu-id="57d86-154">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="57d86-155">Quand vous ciblez le .NET Framework, vous devez toujours référencer explicitement le package NuGet [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) dans votre fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="57d86-155">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

<span data-ttu-id="57d86-156">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]</span><span class="sxs-lookup"><span data-stu-id="57d86-156">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]</span></span>

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="57d86-157">S’appuyer sur les fonctionnalités de mise en évidence d’Application Insights</span><span class="sxs-lookup"><span data-stu-id="57d86-157">Rely on Application Insights "Light-Up" features</span></span>
<span data-ttu-id="57d86-158">La configuration sans effort de l’instrumentation des performances d’application est importante.</span><span class="sxs-lookup"><span data-stu-id="57d86-158">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="57d86-159">Vous pouvez maintenant vous appuyer sur les nouvelles fonctionnalités de mise en évidence [d’Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) disponibles dans les outils Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="57d86-159">You can now rely on the new [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="57d86-160">Par défaut, les projets ASP.NET Core 1.1 créés dans Visual Studio 2017 ajoutaient Application Insights.</span><span class="sxs-lookup"><span data-stu-id="57d86-160">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="57d86-161">Si vous n’utilisez pas le SDK Application Insights directement, en dehors de *Program.cs* et *Startup.cs*, effectuez les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="57d86-161">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs*, follow these steps:</span></span>

1. <span data-ttu-id="57d86-162">Supprimez le nœud `<PackageReference />` suivant du fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="57d86-162">Remove the following `<PackageReference />` node from the *.csproj* file:</span></span>
    
    <span data-ttu-id="57d86-163">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=10)]</span><span class="sxs-lookup"><span data-stu-id="57d86-163">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=10)]</span></span>

2. <span data-ttu-id="57d86-164">Supprimez l’appel de méthode d’extension `UseApplicationInsights` de *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="57d86-164">Remove the `UseApplicationInsights` extension method invocation from *Program.cs*:</span></span>

    <span data-ttu-id="57d86-165">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]</span><span class="sxs-lookup"><span data-stu-id="57d86-165">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]</span></span>

3. <span data-ttu-id="57d86-166">Supprimez l’appel d’API côté client Application Insights du fichier *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="57d86-166">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="57d86-167">Il comprend les deux lignes de code suivantes :</span><span class="sxs-lookup"><span data-stu-id="57d86-167">It comprises the following two lines of code:</span></span>

    <span data-ttu-id="57d86-168">[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Views/Shared/_Layout.cshtml?range=1,19)]</span><span class="sxs-lookup"><span data-stu-id="57d86-168">[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Views/Shared/_Layout.cshtml?range=1,19)]</span></span>

<span data-ttu-id="57d86-169">Si vous utilisez le SDK Application Insights directement, continuez à le faire.</span><span class="sxs-lookup"><span data-stu-id="57d86-169">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="57d86-170">La version 2.0 du [métapackage](xref:fundamentals/metapackage) inclut la dernière version d’Application Insights. Par conséquent, une erreur de passage à une version antérieure de package s’affiche si vous référencez une version antérieure.</span><span class="sxs-lookup"><span data-stu-id="57d86-170">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authentication--identity-improvements"></a><span data-ttu-id="57d86-171">Adopter les améliorations d’authentification / identité</span><span class="sxs-lookup"><span data-stu-id="57d86-171">Adopt Authentication / Identity Improvements</span></span>
<span data-ttu-id="57d86-172">ASP.NET Core 2.0 offre un nouveau modèle d’authentification, et plusieurs modifications importantes ont été apportées à ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="57d86-172">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="57d86-173">Si vous avez créé votre projet avec l’option Comptes d’utilisateur individuels activée, ou si vous avez ajouté manuellement l’authentification ou Identity, consultez [Migrating Authentication and Identity to ASP.NET Core 2.0 (Migration de l’authentification et de l’identité vers ASP.NET Core 2.0)](xref:migration/1x-to-2x/identity-2x).</span><span class="sxs-lookup"><span data-stu-id="57d86-173">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrating Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="57d86-174">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="57d86-174">Additional Resources</span></span>
- [<span data-ttu-id="57d86-175">Changements importants dans ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="57d86-175">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)

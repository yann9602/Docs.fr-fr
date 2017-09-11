---
title: "Créer des profils de publication pour Visual Studio et MSBuild"
author: rick-anderson
description: "Explique ce qu’est la publication web dans Visual Studio."
keywords: "ASP.NET Core, publication web, publication, msbuild, déploiement web, dotnet publish, Visual Studio 2017"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 0377a02d-8fda-47a5-929a-24a16e1d2c93
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/web-publishing-vs
ms.openlocfilehash: 872e8c99734a0913770281d9d87b1e30c250ab0a
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2017
---
# <a name="create-publish-profiles-for-visual-studio-and-msbuild-to-deploy-aspnet-core-apps"></a><span data-ttu-id="704d1-104">Créer des profils de publication pour Visual Studio et MSBuild afin de déployer des applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="704d1-104">Create publish profiles for Visual Studio and MSBuild, to deploy ASP.NET Core apps</span></span>

<span data-ttu-id="704d1-105">De [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="704d1-105">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="704d1-106">Cet article est axé sur l’utilisation de Visual Studio 2017 pour créer des profils de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-106">This article focuses on using Visual Studio 2017 to create publish profiles.</span></span> <span data-ttu-id="704d1-107">Les profils de publication créées avec Visual Studio peuvent être exécutés à partir de MSBuild et Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="704d1-107">The publish profiles created with Visual Studio can be run from MSBuild and Visual Studio 2017.</span></span>

<span data-ttu-id="704d1-108">Le fichier *.csproj* suivant a été créé avec la commande `dotnet new mvc` :</span><span class="sxs-lookup"><span data-stu-id="704d1-108">The following *.csproj* file was created with the command `dotnet new mvc`:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="704d1-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="704d1-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
  </ItemGroup>

  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
  </ItemGroup>

</Project>
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="704d1-110">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="704d1-110">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp1.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore" Version="1.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.Mvc" Version="1.1.2" />
    <PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="1.1.1" />
  </ItemGroup>

</Project>
```

---

<span data-ttu-id="704d1-111">L’attribut `Sdk` dans l’élément `<Project>` (sur la première ligne) du balisage ci-dessus effectue les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="704d1-111">The `Sdk` attribute in the `<Project>` element (in the first line) of the markup above does the following:</span></span>

* <span data-ttu-id="704d1-112">Importation du fichier `props` à partir de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* au début.</span><span class="sxs-lookup"><span data-stu-id="704d1-112">Imports the `props` file from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* at the beginning.</span></span>
* <span data-ttu-id="704d1-113">Importation du fichier targets à partir de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* à la fin.</span><span class="sxs-lookup"><span data-stu-id="704d1-113">Imports the targets file from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* at the end.</span></span>

<span data-ttu-id="704d1-114">L’emplacement par défaut de `MSBuildSDKsPath` (avec Visual Studio 2017 Enterprise) est le dossier *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks*.</span><span class="sxs-lookup"><span data-stu-id="704d1-114">The default location for `MSBuildSDKsPath` (with Visual Studio 2017 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="704d1-115">`Microsoft.NET.Sdk.Web` dépend de :</span><span class="sxs-lookup"><span data-stu-id="704d1-115">`Microsoft.NET.Sdk.Web` depends on:</span></span>

* <span data-ttu-id="704d1-116">*Microsoft.NET.Sdk.Web.ProjectSystem*</span><span class="sxs-lookup"><span data-stu-id="704d1-116">*Microsoft.NET.Sdk.Web.ProjectSystem*</span></span>
* <span data-ttu-id="704d1-117">*Microsoft.NET.Sdk.Publish*</span><span class="sxs-lookup"><span data-stu-id="704d1-117">*Microsoft.NET.Sdk.Publish*</span></span>

<span data-ttu-id="704d1-118">Ce qui provoque l’importation des fichiers `props` et `targets` suivants :</span><span class="sxs-lookup"><span data-stu-id="704d1-118">Which causes the following `props` and `targets` to be imported:</span></span>

* <span data-ttu-id="704d1-119">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props</span><span class="sxs-lookup"><span data-stu-id="704d1-119">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props</span></span>
* <span data-ttu-id="704d1-120">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets</span><span class="sxs-lookup"><span data-stu-id="704d1-120">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets</span></span>
* <span data-ttu-id="704d1-121">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props</span><span class="sxs-lookup"><span data-stu-id="704d1-121">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props</span></span>
* <span data-ttu-id="704d1-122">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets</span><span class="sxs-lookup"><span data-stu-id="704d1-122">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets</span></span>

<span data-ttu-id="704d1-123">Les cibles de publication réimporteront l’ensemble approprié de cibles en fonction de la méthode de publication utilisée.</span><span class="sxs-lookup"><span data-stu-id="704d1-123">Publish targets will again import the right set of targets based on the publish method used.</span></span>

<span data-ttu-id="704d1-124">Quand MSBuild ou Visual Studio charge un projet, les actions principales suivantes sont effectuées :</span><span class="sxs-lookup"><span data-stu-id="704d1-124">When MSBuild or Visual Studio loads a project, the following high level actions are performed:</span></span>

* <span data-ttu-id="704d1-125">Génération du projet</span><span class="sxs-lookup"><span data-stu-id="704d1-125">Build project</span></span>
* <span data-ttu-id="704d1-126">Calcul des fichiers à publier</span><span class="sxs-lookup"><span data-stu-id="704d1-126">Compute files to publish</span></span>
* <span data-ttu-id="704d1-127">Publication des fichiers sur la destination</span><span class="sxs-lookup"><span data-stu-id="704d1-127">Publish files to destination</span></span>

### <a name="compute-project-items"></a><span data-ttu-id="704d1-128">Calcul des éléments du projet</span><span class="sxs-lookup"><span data-stu-id="704d1-128">Compute project items</span></span>

<span data-ttu-id="704d1-129">Quand le projet est chargé, les éléments du projet (fichiers) sont calculés.</span><span class="sxs-lookup"><span data-stu-id="704d1-129">When the project is loaded, the project items (files) are computed.</span></span> <span data-ttu-id="704d1-130">L’attribut `item type` détermine comment le fichier est traité.</span><span class="sxs-lookup"><span data-stu-id="704d1-130">The `item type` attribute determines how the file is processed.</span></span> <span data-ttu-id="704d1-131">Par défaut, les fichiers *.cs* sont inclus dans la liste d’éléments `Compile`.</span><span class="sxs-lookup"><span data-stu-id="704d1-131">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="704d1-132">Les fichiers dans la liste d’éléments `Compile` sont compilés.</span><span class="sxs-lookup"><span data-stu-id="704d1-132">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="704d1-133">La liste d’éléments `Content` contient des fichiers qui seront publiés en plus des sorties de génération.</span><span class="sxs-lookup"><span data-stu-id="704d1-133">The `Content` item list contains files that will be published in addition to the build outputs.</span></span> <span data-ttu-id="704d1-134">Par défaut, les fichiers qui correspondent au modèle wwwroot/** sont inclus dans l’élément `Content`.</span><span class="sxs-lookup"><span data-stu-id="704d1-134">By default, files matching the pattern wwwroot/** will be included in the `Content` item.</span></span> <span data-ttu-id="704d1-135">[wwwroot/** est un modèle d’utilisation des caractères génériques](https://gruntjs.com/configuring-tasks#globbing-patterns) qui spécifie tous les fichiers dans le dossier *wwwroot* **et** les sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="704d1-135">[wwwroot/** is a globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) that specifies all files in the *wwwroot* folder **and** subfolders.</span></span> <span data-ttu-id="704d1-136">Si vous devez ajouter explicitement un fichier à la liste de publication, vous pouvez l’ajouter directement au fichier *.csproj* comme indiqué dans [Inclusion de fichiers](#including-files).</span><span class="sxs-lookup"><span data-stu-id="704d1-136">If you need to explicitly add a file to the publish list you can add the file directly in the *.csproj* file as shown in [Including Files](#including-files).</span></span>

<span data-ttu-id="704d1-137">Quand vous sélectionnez le bouton **Publier** dans Visual Studio ou quand vous publiez à partir de la ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="704d1-137">When you select the **Publish** button in Visual Studio or when you publish from command line:</span></span>

- <span data-ttu-id="704d1-138">Les éléments/propriétés sont calculés (il s’agit des fichiers nécessaires à la génération).</span><span class="sxs-lookup"><span data-stu-id="704d1-138">The properties/items are computed (the files that are needed to build).</span></span>
- <span data-ttu-id="704d1-139">Visual Studio uniquement : les packages NuGet sont restaurés.</span><span class="sxs-lookup"><span data-stu-id="704d1-139">Visual Studio only: NuGet packages are restored.</span></span>  <span data-ttu-id="704d1-140">(La restauration doit être explicite par l’utilisateur sur l’interface CLI.)</span><span class="sxs-lookup"><span data-stu-id="704d1-140">(Restore needs to be explicit by the user on the CLI.)</span></span>
- <span data-ttu-id="704d1-141">Le projet est généré.</span><span class="sxs-lookup"><span data-stu-id="704d1-141">The project builds.</span></span>
- <span data-ttu-id="704d1-142">Les éléments de publication sont calculés (il s’agit des fichiers nécessaires à la publication).</span><span class="sxs-lookup"><span data-stu-id="704d1-142">The publish items are computed (the files that are needed to publish).</span></span>
- <span data-ttu-id="704d1-143">Le projet est publié.</span><span class="sxs-lookup"><span data-stu-id="704d1-143">The project is published.</span></span> <span data-ttu-id="704d1-144">(Les fichiers calculés sont copiés sur la destination de publication.)</span><span class="sxs-lookup"><span data-stu-id="704d1-144">(The computed files are copied to the publish destination.)</span></span>

## <a name="simple-command-line-publishing"></a><span data-ttu-id="704d1-145">Publication simple sur une ligne de commande</span><span class="sxs-lookup"><span data-stu-id="704d1-145">Simple command line publishing</span></span>

<span data-ttu-id="704d1-146">La procédure décrite dans cette section fonctionne sur toutes les plateformes .NET Core prises en charge et ne nécessite pas Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="704d1-146">This section works on all .NET Core supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="704d1-147">Dans les exemples ci-dessous, la commande `dotnet publish` est exécutée à partir du répertoire du projet (qui contient le fichier *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="704d1-147">In the samples below, the `dotnet publish` command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="704d1-148">Si vous n’êtes pas dans le dossier du projet, vous pouvez transmettre explicitement le chemin du fichier projet.</span><span class="sxs-lookup"><span data-stu-id="704d1-148">If you're not in the project folder, you can explicitly pass in the project file path.</span></span> <span data-ttu-id="704d1-149">Exemple :</span><span class="sxs-lookup"><span data-stu-id="704d1-149">For example:</span></span>

```console
dotnet publish  c:/webs/web1
```

<span data-ttu-id="704d1-150">Exécutez les commandes suivantes pour créer et publier une application web :</span><span class="sxs-lookup"><span data-stu-id="704d1-150">Run the following commands to create and publish a web app:</span></span>

```console
dotnet new mvc
dotnet restore
dotnet publish
```

<span data-ttu-id="704d1-151">`dotnet publish` produit une sortie semblable à la suivante :</span><span class="sxs-lookup"><span data-stu-id="704d1-151">The `dotnet publish` produces output similar to the following:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.1.548.43366
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp1.1\Web1.dll
```

<span data-ttu-id="704d1-152">Le dossier de publication par défaut est `bin\$(Configuration)\netcoreapp<version>\publish`.</span><span class="sxs-lookup"><span data-stu-id="704d1-152">The default publish folder is `bin\$(Configuration)\netcoreapp<version>\publish`.</span></span> <span data-ttu-id="704d1-153">La valeur par défaut de `$(Configuration)` est Debug.</span><span class="sxs-lookup"><span data-stu-id="704d1-153">The default for `$(Configuration)` is Debug.</span></span> <span data-ttu-id="704d1-154">Dans l’exemple ci-dessus, le `<TargetFramework>` est `netcoreapp1.1`.</span><span class="sxs-lookup"><span data-stu-id="704d1-154">In the sample above, the `<TargetFramework>` is `netcoreapp1.1`.</span></span> <span data-ttu-id="704d1-155">Le chemin réel dans l’exemple ci-dessus est *bin\Debug\netcoreapp1.1\publish*.</span><span class="sxs-lookup"><span data-stu-id="704d1-155">The actual path in the sample above is *bin\Debug\netcoreapp1.1\publish*.</span></span>

<span data-ttu-id="704d1-156">`dotnet publish -h` affiche des informations d’aide relatives à la publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-156">`dotnet publish -h` displays help information for publish.</span></span>

<span data-ttu-id="704d1-157">La commande suivante spécifie une build `Release` et le répertoire de publication :</span><span class="sxs-lookup"><span data-stu-id="704d1-157">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:/MyWebs/test
```

<span data-ttu-id="704d1-158">La commande `dotnet publish` appelle `MSBuild`, qui appelle la cible de `Publish`.</span><span class="sxs-lookup"><span data-stu-id="704d1-158">The `dotnet publish` command calls `MSBuild` which invokes the `Publish` target.</span></span> <span data-ttu-id="704d1-159">Les paramètres passés à `dotnet publish` sont passés à `MSBuild`.</span><span class="sxs-lookup"><span data-stu-id="704d1-159">Any parameters passed to `dotnet publish` are passed to `MSBuild`.</span></span> <span data-ttu-id="704d1-160">Le paramètre `-c` est mappé à la propriété MSBuild `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="704d1-160">The `-c` parameter maps to the `Configuration` MSBuild property.</span></span> <span data-ttu-id="704d1-161">Le paramètre `-o` est mappé à `OutputPath`.</span><span class="sxs-lookup"><span data-stu-id="704d1-161">The `-o` parameter maps to `OutputPath`.</span></span>

<span data-ttu-id="704d1-162">Vous pouvez passer des propriétés MSBuild à l’aide de l’un des formats suivants :</span><span class="sxs-lookup"><span data-stu-id="704d1-162">You can pass MSBuild properties using either of the following formats:</span></span>

- ` p:<NAME>=<VALUE>`
- `/p:<NAME>=<VALUE>`

<span data-ttu-id="704d1-163">La commande suivante publie une build `Release` sur un partage réseau :</span><span class="sxs-lookup"><span data-stu-id="704d1-163">The following command publishes a `Release` build to a network share:</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="704d1-164">Le partage réseau est spécifié avec des barres obliques (*//r8/*) et fonctionne sur toutes les plateformes .NET Core prises en charge.</span><span class="sxs-lookup"><span data-stu-id="704d1-164">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

<span data-ttu-id="704d1-165">Vérifiez que l’application publiée pour le déploiement n’est pas en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="704d1-165">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="704d1-166">Les fichiers dans le dossier *publish* sont verrouillés quand l’application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="704d1-166">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="704d1-167">Le déploiement ne peut pas se produire car les fichiers verrouillés ne peuvent pas être copiés.</span><span class="sxs-lookup"><span data-stu-id="704d1-167">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="704d1-168">Profils de publication</span><span class="sxs-lookup"><span data-stu-id="704d1-168">Publish profiles</span></span>

<span data-ttu-id="704d1-169">Cette section utilise Visual Studio 2017 et versions ultérieures pour créer des profils de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-169">This section uses Visual Studio 2017 and higher to create publishing profiles.</span></span> <span data-ttu-id="704d1-170">Une fois les profils créés, vous pouvez publier à partir de Visual Studio ou de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="704d1-170">Once created, you can publish from Visual Studio or the command line.</span></span>

<span data-ttu-id="704d1-171">Les profils de publication peuvent simplifier le processus de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-171">Publish profiles can simplify the publishing process.</span></span> <span data-ttu-id="704d1-172">Vous pouvez avoir plusieurs profils de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-172">You can have multiple publish profiles.</span></span> <span data-ttu-id="704d1-173">Pour créer un profil de publication dans Visual Studio, cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions et sélectionnez **Publier**.</span><span class="sxs-lookup"><span data-stu-id="704d1-173">To create a publish profile in Visual Studio, right click on the project in Solution Explore and select **Publish**.</span></span> <span data-ttu-id="704d1-174">Vous pouvez également sélectionner **Publier \<nom_projet>** dans le menu Générer.</span><span class="sxs-lookup"><span data-stu-id="704d1-174">Alternatively, you can select **Publish     \<project name>** from the build menu.</span></span> <span data-ttu-id="704d1-175">L’onglet **Publier** de la page de capacités d’application s’affiche.</span><span class="sxs-lookup"><span data-stu-id="704d1-175">The **Publish** tab of the application capacities page is displayed.</span></span> <span data-ttu-id="704d1-176">Si le projet ne contient pas de profil de publication, la page suivante s’affiche :</span><span class="sxs-lookup"><span data-stu-id="704d1-176">If the project doesn't contain a publish profile, the following page is displayed:</span></span>

![L’onglet **Publier** de la page de capacités d’application montrant Azure, IIS, FTB, Dossier avec Azure sélectionné.](web-publishing-vs/_static/az.png)

<span data-ttu-id="704d1-179">Quand **Dossier** est sélectionné, le bouton **Publier** crée un dossier de profil de publication et le publie.</span><span class="sxs-lookup"><span data-stu-id="704d1-179">When **Folder** is selected, the **Publish** button creates a folder publish profile and publishes.</span></span>

![L’onglet **Publier** de la page de capacités d’application montrant Azure, IIS, FTB, Dossier](web-publishing-vs/_static/pub1.png)

<span data-ttu-id="704d1-181">Une fois qu’un profil de publication a été créé, l’onglet **Publier** change et vous sélectionnez **Créer un profil** pour créer un profil.</span><span class="sxs-lookup"><span data-stu-id="704d1-181">Once a publish profile is created, the **Publish** tab changes, and you select **Create new profile** to create a new profile.</span></span>

![L’onglet **Publier** de la page de capacités d’application montrant FolderProfile (créé lors de la dernière étape) et le lien Créer un profil](web-publishing-vs/_static/create_new.png)

<span data-ttu-id="704d1-183">L’Assistant Publication prend en charge les cibles de publication suivantes :</span><span class="sxs-lookup"><span data-stu-id="704d1-183">The Publish wizard supports the following publish targets:</span></span>

- <span data-ttu-id="704d1-184">Microsoft Azure App Service</span><span class="sxs-lookup"><span data-stu-id="704d1-184">Microsoft Azure App Service</span></span>
- <span data-ttu-id="704d1-185">IIS, FTP, etc. (pour n’importe quel serveur web)</span><span class="sxs-lookup"><span data-stu-id="704d1-185">IIS, FTP, etc (for any web server)</span></span>
- <span data-ttu-id="704d1-186">Dossier</span><span class="sxs-lookup"><span data-stu-id="704d1-186">Folder</span></span>
- <span data-ttu-id="704d1-187">Importer un profil (vous permet d’importer un profil)</span><span class="sxs-lookup"><span data-stu-id="704d1-187">Import profile (allows you to import a profile).</span></span>
- <span data-ttu-id="704d1-188">Machines virtuelles Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="704d1-188">Microsoft Azure Virtual Machines</span></span>

<span data-ttu-id="704d1-189">Pour plus d’informations, consultez [Quelles options de publication choisir ?](https://docs.microsoft.com/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="704d1-189">See [What publishing options are right for me?](https://docs.microsoft.com/visualstudio/ide/not-in-toc/web-publish-options) for more information.</span></span>

<span data-ttu-id="704d1-190">Quand vous créez un profil de publication avec Visual Studio, un fichier MSBuild *Properties/PublishProfiles/\<nom_publication>.pubxml* est créé.</span><span class="sxs-lookup"><span data-stu-id="704d1-190">When you create a publish profile with Visual Studio, a *Properties/PublishProfiles/\<publish name>.pubxml* MSBuild file is created.</span></span> <span data-ttu-id="704d1-191">Ce fichier *.pubxml* est un fichier MSBuild qui contient des paramètres de configuration de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-191">This *.pubxml* file is a MSBuild file and contains publish configuration settings.</span></span> <span data-ttu-id="704d1-192">Vous pouvez modifier ce fichier pour personnaliser le processus de génération et de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-192">You can change this file to customize the build and publish process.</span></span> <span data-ttu-id="704d1-193">Ce fichier est lu par le processus de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-193">This file is read by the publishing process.</span></span> <span data-ttu-id="704d1-194">`<LastUsedBuildConfiguration>` est spéciale, car il s’agit d’une propriété globale qui ne doit être dans aucun fichier importé dans la build.</span><span class="sxs-lookup"><span data-stu-id="704d1-194">`<LastUsedBuildConfiguration>` is special because it’s a global property and shouldn’t be in any file that’s imported in the build.</span></span> <span data-ttu-id="704d1-195">Pour plus d’informations, consultez [MSBuild: how to set the configuration property (Comment définir la propriété de configuration)](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="704d1-195">See [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) for more info.</span></span>
<span data-ttu-id="704d1-196">Le fichier *.pubxml* ne doit pas être archivé dans le contrôle de code source, car il dépend du fichier *.user*.</span><span class="sxs-lookup"><span data-stu-id="704d1-196">The *.pubxml* file should not be checked into source control because it depends on the *.user* file.</span></span> <span data-ttu-id="704d1-197">Le fichier *.user* ne doit jamais être archivé dans le contrôle de code source, car il peut contenir des informations sensibles et il est valide uniquement pour un utilisateur et un ordinateur.</span><span class="sxs-lookup"><span data-stu-id="704d1-197">The *.user* file should never be checked into source control because it can contain sensitive information and it's only valid for one user and machine.</span></span>

<span data-ttu-id="704d1-198">Les informations sensibles (telles que le mot de passe de publication) sont chiffrées pour chaque utilisateur/ordinateur et stockées dans le fichier *Properties/PublishProfiles/\<nom_publication>.pubxml.user*.</span><span class="sxs-lookup"><span data-stu-id="704d1-198">Sensitive information (like the publish password) is encrypted on a per user/machine level and stored in the *Properties/PublishProfiles/\<publish name>.pubxml.user* file.</span></span> <span data-ttu-id="704d1-199">Ce fichier pouvant contenir des informations sensibles, il ne doit **pas** être archivé dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="704d1-199">Because this file can contain sensitive information, it should **not** be checked into source control.</span></span>

<span data-ttu-id="704d1-200">Pour obtenir une vue d’ensemble expliquant comment publier une application web sur ASP.NET Core, consultez [Publication et déploiement](index.md).</span><span class="sxs-lookup"><span data-stu-id="704d1-200">For an overview of how to publish a web app on ASP.NET Core see [Publishing and Deployment](index.md).</span></span> <span data-ttu-id="704d1-201">[Publication et déploiement](index.md) est un projet open source accessible à l’adresse https://github.com/aspnet/websdk.</span><span class="sxs-lookup"><span data-stu-id="704d1-201">[Publishing and Deployment](index.md) is an open source project at https://github.com/aspnet/websdk.</span></span>

<span data-ttu-id="704d1-202">Actuellement, `dotnet publish` n’offre pas la possibilité d’utiliser des profils de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-202">Currently `dotnet publish` doesn’t have the ability to use publish profiles.</span></span> <span data-ttu-id="704d1-203">Pour utiliser des profils de publication, utilisez `dotnet build`.</span><span class="sxs-lookup"><span data-stu-id="704d1-203">To use publish profiles, use `dotnet build`.</span></span> <span data-ttu-id="704d1-204">`dotnet build` appelle MSBuild sur le projet.</span><span class="sxs-lookup"><span data-stu-id="704d1-204">`dotnet build` invokes MSBuild on the project.</span></span> <span data-ttu-id="704d1-205">Vous pouvez également appeler `msbuild` directement.</span><span class="sxs-lookup"><span data-stu-id="704d1-205">Alternatively, call `msbuild` directly.</span></span>

<span data-ttu-id="704d1-206">Définissez les propriétés MSBuild suivantes lors de l’utilisation d’un profil de publication :</span><span class="sxs-lookup"><span data-stu-id="704d1-206">Set the following MSBuild properties when using a publish profile:</span></span>

- `DeployOnBuild=true`
- `PublishProfile=<Publish profile name>`

<span data-ttu-id="704d1-207">Par exemple, lors de la publication avec un profil nommé *FolderProfile* vous pouvez exécuter l’une des commandes ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="704d1-207">For example, when publishing with a profile named *FolderProfile* you can execute either of the commands below.</span></span>

- `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
- `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="704d1-208">Quand vous appelez la commande `dotnet build`, elle appelle `msbuild` pour exécuter le processus de génération et de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-208">When you invoke `dotnet build` it will call `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="704d1-209">Appeler `dotnet build` ou `msbuild` revient essentiellement au même quand vous passez un profil de dossier.</span><span class="sxs-lookup"><span data-stu-id="704d1-209">Calling `dotnet build` or `msbuild` is essentially equivalent when you pass in a folder profile.</span></span> <span data-ttu-id="704d1-210">Quand vous appelez MSBuild directement sur Windows, vous obtenez la version du .NET Framework de MSBuild.</span><span class="sxs-lookup"><span data-stu-id="704d1-210">When calling MSBuild directly on Windows you get the .NET Framework version of MSBuild.</span></span>  <span data-ttu-id="704d1-211">MSDeploy est actuellement limité aux ordinateurs Windows pour la publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-211">MSDeploy is currently limited to Windows machines for publishing.</span></span> <span data-ttu-id="704d1-212">L’appel de `dotnet build` sur un profil autre qu’un profil de dossier entraîne l’appel de MSBuild, et MSBuild utilise MSDeploy sur les profils autres que les dossiers de profil.</span><span class="sxs-lookup"><span data-stu-id="704d1-212">Calling `dotnet build` on a non-folder profile invokes MSBuild, and MSBuild uses MSDeploy on non-folder profiles.</span></span> <span data-ttu-id="704d1-213">L’appel de `dotnet build` sur un profil autre qu’un profil de dossier entraîne l’appel de MSBuild (à l’aide de MSDeploy) et échoue (même en cas d’exécution sur une plateforme Windows).</span><span class="sxs-lookup"><span data-stu-id="704d1-213">Calling `dotnet build` on a non-folder profile invokes MSBuild (using MSDeploy) and results in a failure (even when running on a Windows platform).</span></span> <span data-ttu-id="704d1-214">Pour publier avec un profil autre qu’un profil de dossier, appelez MSBuild directement.</span><span class="sxs-lookup"><span data-stu-id="704d1-214">To publish with a non-folder profile, call MSBuild directly.</span></span>

<span data-ttu-id="704d1-215">Le profil de publication de dossier suivant a été créé avec Visual Studio et publie sur un partage réseau :</span><span class="sxs-lookup"><span data-stu-id="704d1-215">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

<span data-ttu-id="704d1-216">[!code-xml[Main](web-publishing-vs/sample/FolderProfile.pubxml?highlight=5,9,11,18)]</span><span class="sxs-lookup"><span data-stu-id="704d1-216">[!code-xml[Main](web-publishing-vs/sample/FolderProfile.pubxml?highlight=5,9,11,18)]</span></span>

<span data-ttu-id="704d1-217">Notez que `<LastUsedBuildConfiguration>` a la valeur `Release`.</span><span class="sxs-lookup"><span data-stu-id="704d1-217">Note `<LastUsedBuildConfiguration>` is set to `Release`.</span></span>  <span data-ttu-id="704d1-218">Lors de la publication à partir de Visual Studio, la propriété de configuration `<LastUsedBuildConfiguration>` prend la valeur en vigueur lors du démarrage du processus de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-218">When publishing from Visual Studio, the `<LastUsedBuildConfiguration>` configuration property value is set using the value when the publish process is started.</span></span> <span data-ttu-id="704d1-219">La propriété de configuration `<LastUsedBuildConfiguration>` est spéciale et ne doit pas être substituée dans un fichier MSBuild importé.</span><span class="sxs-lookup"><span data-stu-id="704d1-219">The `<LastUsedBuildConfiguration>` configuration property is special and shouldn’t be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="704d1-220">Vous pouvez substituer cette propriété à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="704d1-220">You can override this property from the command line.</span></span> <span data-ttu-id="704d1-221">Exemple :</span><span class="sxs-lookup"><span data-stu-id="704d1-221">For example:</span></span>

`dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="704d1-222">À l’aide de MSBuild :</span><span class="sxs-lookup"><span data-stu-id="704d1-222">Using MSBuild:</span></span>

`msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="704d1-223">Publier sur un point de terminaison MSDeploy à partir de la ligne de commande</span><span class="sxs-lookup"><span data-stu-id="704d1-223">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="704d1-224">Comme mentionné précédemment, vous pouvez publier à l’aide de la commande `dotnet publish` ou `msbuild`.</span><span class="sxs-lookup"><span data-stu-id="704d1-224">As previously mentioned, you can publish using `dotnet publish` or the `msbuild` command.</span></span> <span data-ttu-id="704d1-225">`dotnet publish` s’exécute dans le contexte de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="704d1-225">`dotnet publish` runs in the context of .NET Core.</span></span> <span data-ttu-id="704d1-226">`msbuild` a besoin du .NET framework et est donc limitée aux environnements Windows.</span><span class="sxs-lookup"><span data-stu-id="704d1-226">`msbuild` requires .NET framework, and is therefore limited to Windows environments.</span></span>

<span data-ttu-id="704d1-227">Pour publier avec MSDeploy, le plus simple est d’abord de créer un profil de publication dans Visual Studio 2017, puis d’utiliser le profil à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="704d1-227">The easiest way to publish with MSDeploy is to first create a publish profile in Visual Studio 2017 and use the profile from the command line.</span></span>

<span data-ttu-id="704d1-228">Dans l’exemple suivant, j’ai créé une application web ASP.NET Core (à l’aide de `dotnet new mvc`) et ajouté un profil de publication Azure avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="704d1-228">In the following sample, I created an ASP.NET Core web app ( using `dotnet new mvc`) and added an Azure publish profile with Visual Studio.</span></span>

<span data-ttu-id="704d1-229">Vous exécutez `msbuild` à partir d’une **invite de commandes développeur pour VS 2017**.</span><span class="sxs-lookup"><span data-stu-id="704d1-229">You run `msbuild` from a **Developer Command Prompt for VS 2017**.</span></span> <span data-ttu-id="704d1-230">L’invite de commandes développeur aura le bon *msbuild.exe* dans son chemin et définira certaines variables MSBuild.</span><span class="sxs-lookup"><span data-stu-id="704d1-230">The Developer Command Prompt will have the correct *msbuild.exe* in its path and set some MSBuild variables.</span></span>

<span data-ttu-id="704d1-231">MSBuild utilise la syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="704d1-231">MSBuild uses the following syntax:</span></span>

`msbuild <path-to-project-file> /p:DeployOnBuild=true /p:PublishProfile=<Publish Profile>  /p:Username=<USERNAME> /p:Password=<PASSWORD>`

<span data-ttu-id="704d1-232">Vous pouvez obtenir le `Password` à partir du fichier *\<nom_publication>.PublishSettings*.</span><span class="sxs-lookup"><span data-stu-id="704d1-232">You can get the `Password` from the *\<Publish name>.PublishSettings* file.</span></span> <span data-ttu-id="704d1-233">Vous pouvez télécharger le fichier *.PublishSettings* à partir des emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="704d1-233">You can download the *.PublishSettings* file from:</span></span>

- <span data-ttu-id="704d1-234">Explorateur de solutions : cliquez avec le bouton droit sur l’application web et sélectionnez **Télécharger le profil de publication**.</span><span class="sxs-lookup"><span data-stu-id="704d1-234">Solution Explorer: Right click on the Web App and select **Download Publish Profile**.</span></span>
- <span data-ttu-id="704d1-235">Portail de gestion Azure : sélectionnez **Obtenir le profil de publication** à partir du panneau Application web.</span><span class="sxs-lookup"><span data-stu-id="704d1-235">The Azure Management Portal: Select **Get publish profile** from the  Web App blade.</span></span>

<span data-ttu-id="704d1-236">`Username` figure dans le profil de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-236">`Username` can be found in the publish profile.</span></span>

<span data-ttu-id="704d1-237">L’exemple suivant utilise le profil de publication « Web11112 – Web Deploy » :</span><span class="sxs-lookup"><span data-stu-id="704d1-237">The following sample uses the "Web11112 - Web Deploy" publish profile:</span></span>

```console
msbuild "C:\Webs\Web1\Web1.csproj" /p:DeployOnBuild=true
 /p:PublishProfile="Web11112 - Web Deploy"  /p:Username="$Web11112"
 /p:Password="<password removed>"
```

## <a name="excluding-files"></a><span data-ttu-id="704d1-238">Exclusion de fichiers</span><span class="sxs-lookup"><span data-stu-id="704d1-238">Excluding files</span></span>

<span data-ttu-id="704d1-239">Lors de la publication d’applications web ASP.NET Core, les artefacts de build et le contenu du dossier *wwwroot* sont inclus.</span><span class="sxs-lookup"><span data-stu-id="704d1-239">When publishing ASP.NET Core web apps, the build artifacts and contents of the *wwwroot* folder are included.</span></span> <span data-ttu-id="704d1-240">`msbuild` prend en charge les [modèles d’utilisation des caractères génériques](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="704d1-240">`msbuild` supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="704d1-241">Par exemple, le balisage d’éléments `<Content>` suivant exclut tous les fichiers texte (*.txt*) du dossier *wwwroot/content* et tous ses sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="704d1-241">For example, the following `<Content>` element markup will exclude all text (*.txt*) files from the *wwwroot/content* folder and all its subfolders.</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="704d1-242">Vous pouvez ajouter le balisage ci-dessus à un profil de publication ou au fichier *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="704d1-242">The markup above can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="704d1-243">En cas d’ajout au fichier *.csproj*, la règle est ajoutée à tous les profils de publication dans le projet.</span><span class="sxs-lookup"><span data-stu-id="704d1-243">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="704d1-244">Le balisage d’éléments `<MsDeploySkipRules>` suivant exclut tous les fichiers du dossier *wwwroot/content* :</span><span class="sxs-lookup"><span data-stu-id="704d1-244">The following `<MsDeploySkipRules>` element markup exludes all files from the *wwwroot/content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="704d1-245">`<MsDeploySkipRules>` ne supprime pas les cibles *skip* du site de déploiement.</span><span class="sxs-lookup"><span data-stu-id="704d1-245">`<MsDeploySkipRules>`  will not delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="704d1-246">Les dossiers et fichiers ciblés par `<Content>` sont supprimés du site de déploiement.</span><span class="sxs-lookup"><span data-stu-id="704d1-246">`<Content>` targeted files and folders will be deleted from the deployment site.</span></span> <span data-ttu-id="704d1-247">Par exemple, supposez que vous avez déployé une application web avec les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="704d1-247">For example, suppose you had deployed a web app with the following files:</span></span>

- <span data-ttu-id="704d1-248">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="704d1-248">*Views/Home/About1.cshtml*</span></span>
- <span data-ttu-id="704d1-249">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="704d1-249">*Views/Home/About2.cshtml*</span></span>
- <span data-ttu-id="704d1-250">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="704d1-250">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="704d1-251">Si vous avez ajouté le balisage `<MsDeploySkipRules>` suivant, ces fichiers ne sont pas supprimés sur le site de déploiement.</span><span class="sxs-lookup"><span data-stu-id="704d1-251">If you added the following `<MsDeploySkipRules>` markup, those files would not be deleted on the deployment site.</span></span>

``` xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="704d1-252">Le balisage `<MsDeploySkipRules>` ci-dessus empêche le déploiement des fichiers *ignorés*, mais ne supprime pas ces fichiers une fois qu’ils sont déployés.</span><span class="sxs-lookup"><span data-stu-id="704d1-252">The `<MsDeploySkipRules>` markup shown above prevents the *skipped* files from being depoyed, but will not delete those files once they are deployed.</span></span>

<span data-ttu-id="704d1-253">Le balisage `<Content>` suivant supprime les fichiers ciblés sur le site de déploiement :</span><span class="sxs-lookup"><span data-stu-id="704d1-253">The following `<Content>` markup would delete the targeted files at the deployment site:</span></span>

``` xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="704d1-254">L’utilisation du déploiement à partir de la ligne de commande avec le balisage `<Content>` ci-dessus génère une sortie semblable à la suivante :</span><span class="sxs-lookup"><span data-stu-id="704d1-254">Using command line deployment with the `<Content>` markup above would result in output similar to the following:</span></span>

``` console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="including-files"></a><span data-ttu-id="704d1-255">Inclusion de fichiers</span><span class="sxs-lookup"><span data-stu-id="704d1-255">Including files</span></span>

<span data-ttu-id="704d1-256">Vous pouvez utiliser le balisage suivant pour inclure un dossier *images* situé en dehors du répertoire de projet dans le dossier *wwwroot/images* du site de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-256">The following markup can be used to include an *images* folder outside the project directory to the *wwwroot/images* folder of the publish site.</span></span>

``` xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="704d1-257">Vous pouvez ajouter le balisage au fichier *.csproj* ou au profil de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-257">The markup can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="704d1-258">Si vous l’ajoutez au fichier *csproj*, il est inclus dans chaque profil de publication du projet.</span><span class="sxs-lookup"><span data-stu-id="704d1-258">If it's added to the *.csproj* file, it will be included in each publish profile in the project.</span></span>

<span data-ttu-id="704d1-259">Le balisage mis en surbrillance ci-dessous montre comment :</span><span class="sxs-lookup"><span data-stu-id="704d1-259">The following highlighted markup shows how to:</span></span>

* <span data-ttu-id="704d1-260">Copier un fichier situé en dehors du projet dans le dossier *wwwroot*</span><span class="sxs-lookup"><span data-stu-id="704d1-260">Copy a file from outside the project into the *wwwroot* folder.</span></span>
* <span data-ttu-id="704d1-261">Exclure le dossier *wwwroot\Content*</span><span class="sxs-lookup"><span data-stu-id="704d1-261">Exclude the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="704d1-262">Exclure *Views\Home\About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="704d1-262">Exclude *Views\Home\About2.cshtml*.</span></span>

<span data-ttu-id="704d1-263">[!code-xml[Main](web-publishing-vs/sample/FolderProfile2.pubxml?highlight=21-29)]</span><span class="sxs-lookup"><span data-stu-id="704d1-263">[!code-xml[Main](web-publishing-vs/sample/FolderProfile2.pubxml?highlight=21-29)]</span></span>

<span data-ttu-id="704d1-264">Pour obtenir d’autres exemples de déploiement, consultez le fichier [Lisez-moi webSDK](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="704d1-264">See the [WebSDK Readme](https://github.com/aspnet/websdk) for more deployment samples.</span></span>

### <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="704d1-265">Exécuter une cible avant ou après la publication</span><span class="sxs-lookup"><span data-stu-id="704d1-265">Run a target before or after publishing</span></span>

<span data-ttu-id="704d1-266">Vous pouvez utiliser les cibles intégrées `BeforePublish` et `AfterPublish` pour exécuter une cible avant ou après la cible de publication.</span><span class="sxs-lookup"><span data-stu-id="704d1-266">The builtin `BeforePublish` and `AfterPublish` targets can be used to execute a target before or after the publish target.</span></span> <span data-ttu-id="704d1-267">Vous pouvez ajouter le balisage suivant au profil de publication pour enregistrer les messages dans la sortie de console avant et après la publication :</span><span class="sxs-lookup"><span data-stu-id="704d1-267">The following markup can be added to the publish profile to log messages to the console output before and after publishing:</span></span>

``` xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="the-kudu-service"></a><span data-ttu-id="704d1-268">Le service Kudu</span><span class="sxs-lookup"><span data-stu-id="704d1-268">The Kudu service</span></span>

<span data-ttu-id="704d1-269">Pour afficher les fichiers sur votre application web Azure, utilisez le [service Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="704d1-269">To view the files on your Azure Web App, use the [kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="704d1-270">Ajoutez le jeton `scm` au nom de votre application web.</span><span class="sxs-lookup"><span data-stu-id="704d1-270">Append the `scm` token to the name or your Web App.</span></span> <span data-ttu-id="704d1-271">Exemple :</span><span class="sxs-lookup"><span data-stu-id="704d1-271">For example:</span></span>

| <span data-ttu-id="704d1-272">URL</span><span class="sxs-lookup"><span data-stu-id="704d1-272">URL</span></span>               | <span data-ttu-id="704d1-273">Résultat</span><span class="sxs-lookup"><span data-stu-id="704d1-273">Result</span></span>|
| ----------------- | ------------ |
| `http://mysite.azurewebsites.net/` | <span data-ttu-id="704d1-274">Application web</span><span class="sxs-lookup"><span data-stu-id="704d1-274">Web App</span></span> |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="704d1-275">Service Kudu</span><span class="sxs-lookup"><span data-stu-id="704d1-275">Kudu sevice</span></span> |

<span data-ttu-id="704d1-276">Sélectionnez l’élément de menu [Console de débogage](https://github.com/projectkudu/kudu/wiki/Kudu-console) pour afficher/modifier/supprimer/ajouter des fichiers.</span><span class="sxs-lookup"><span data-stu-id="704d1-276">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view/edit/delete/add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="704d1-277">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="704d1-277">Additional resources</span></span>

- <span data-ttu-id="704d1-278">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (msdeploy) simplifie le déploiement des applications web et des sites web sur les serveurs IIS.</span><span class="sxs-lookup"><span data-stu-id="704d1-278">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy)  (msdeploy) simplifies deployment of Web applications and Web sites to IIS servers.</span></span>

- <span data-ttu-id="704d1-279">[https://github.com/ASPNET/websdk](https://github.com/aspnet/websdk/issues) : soumettez vos problèmes et demandez des fonctionnalités pour le déploiement.</span><span class="sxs-lookup"><span data-stu-id="704d1-279">[https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>

---
title: "Ajout d’un modèle à une application de pages Razor avec Visual Studio pour Mac"
author: rick-anderson
description: "Ajout d’un modèle à une application de pages Razor dans ASP.NET Core à l’aide de Visual Studio pour Mac"
keywords: "ASP.NET Core,pages Razor,Razor,MVC,modèle"
ms.author: riande
manager: wpickett
ms.date: 8/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages-vsc/model
ms.openlocfilehash: 39b069f8c81ca9460abc33b32b0bcc27118939cb
ms.sourcegitcommit: d9ec19e5452af83648074db5d96c0a0f4f9e7f9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2017
---
# <a name="adding-a-model-to-a-razor-pages-app-in-aspnet-core-with-visual-studio-for-mac"></a><span data-ttu-id="2d389-104">Ajout d’un modèle à une application de pages Razor dans ASP.NET Core avec Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2d389-104">Adding a model to a Razor Pages app in ASP.NET Core with Visual Studio for Mac</span></span>

[!INCLUDE[model1](../../includes/RP/model1.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="2d389-105">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="2d389-105">Add a data model</span></span>

* <span data-ttu-id="2d389-106">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="2d389-106">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2d389-107">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="2d389-107">Add a class to the *Models* folder named *Movie.cs*.</span></span>
* <span data-ttu-id="2d389-108">Ajoutez le code suivant au fichier *Models/Movie.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d389-108">Add the following code to the *Models/Movie.cs* file:</span></span>

[!INCLUDE[model 2](../../includes/RP/model2.md)]
[!INCLUDE[model 2a](../../includes/RP/model2a.md)]

<span data-ttu-id="2d389-109">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices2&highlight=3-6)]</span><span class="sxs-lookup"><span data-stu-id="2d389-109">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices2&highlight=3-6)]</span></span>

<span data-ttu-id="2d389-110">Générez le projet pour vérifier qu’il ne comporte aucune erreur.</span><span class="sxs-lookup"><span data-stu-id="2d389-110">Build the project to verify you don't have any errors.</span></span>

### <a name="entity-framework-core-nuget-packages-for-migrations"></a><span data-ttu-id="2d389-111">Packages NuGet Entity Framework Core pour les migrations</span><span class="sxs-lookup"><span data-stu-id="2d389-111">Entity Framework Core NuGet packages for migrations</span></span>

<span data-ttu-id="2d389-112">Les outils EF de l’interface de ligne de commande (CLI) sont fournis dans [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span><span class="sxs-lookup"><span data-stu-id="2d389-112">The EF tools for the command-line interface (CLI) are provided in [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span></span> <span data-ttu-id="2d389-113">Pour installer ce package, ajoutez-le à la collection `DotNetCliToolReference` dans le fichier *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2d389-113">To install this package, add it to the `DotNetCliToolReference` collection in the *.csproj* file.</span></span> <span data-ttu-id="2d389-114">**Remarque :** Vous devez installer ce package en modifiant le fichier *.csproj*. Vous ne pouvez pas utiliser la commande `install-package` ou le GUI (interface graphique utilisateur) du Gestionnaire de package.</span><span class="sxs-lookup"><span data-stu-id="2d389-114">**Note:** You have to install this package by editing the *.csproj* file; you can't use the `install-package` command or the package manager GUI.</span></span>

<span data-ttu-id="2d389-115">Modifiez le fichier *RazorPagesMovie.csproj* :</span><span class="sxs-lookup"><span data-stu-id="2d389-115">Edit the *RazorPagesMovie.csproj* file:</span></span>

* <span data-ttu-id="2d389-116">Sélectionnez **Fichier > Ouvrir un fichier**, puis sélectionnez le fichier *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2d389-116">Select **File > Open File**, and then select the *RazorPagesMovie.csproj* file.</span></span>
* <span data-ttu-id="2d389-117">Ajoutez `<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />` en surbrillance dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2d389-117">Add `<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />` as highlighted in the following code:</span></span>

<span data-ttu-id="2d389-118">[!code-xml[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/RazorPagesMovie.cli.csproj?highlight=10)] [!INCLUDE[model 3](../../includes/RP/model3.md)]</span><span class="sxs-lookup"><span data-stu-id="2d389-118">[!code-xml[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/RazorPagesMovie.cli.csproj?highlight=10)] [!INCLUDE[model 3](../../includes/RP/model3.md)]</span></span>

<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a><span data-ttu-id="2d389-119">Effectuer la génération de modèles automatique pour le modèle Movie</span><span class="sxs-lookup"><span data-stu-id="2d389-119">Scaffold the Movie model</span></span>

* <span data-ttu-id="2d389-120">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="2d389-120">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="2d389-121">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2d389-121">Run the following command:</span></span>

<span data-ttu-id="2d389-122">**Remarque : Exécutez la commande suivante sur Windows. Pour macOS et Linux, consultez la commande suivante**</span><span class="sxs-lookup"><span data-stu-id="2d389-122">**Note: Run the following command on Windows. For MacOS and Linux, see the next command**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2d389-123">Sur macOS et Linux, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2d389-123">On MacOS and Linux, run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<span data-ttu-id="2d389-124">Si vous obtenez l’erreur :</span><span class="sxs-lookup"><span data-stu-id="2d389-124">If you get the error:</span></span>
  ```
  The process cannot access the file 
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll' 
  because it is being used by another process.
  ```

<span data-ttu-id="2d389-125">Quittez Visual Studio et réexécutez la commande.</span><span class="sxs-lookup"><span data-stu-id="2d389-125">Exit Visual Studio and run the command again.</span></span>

[!INCLUDE[model 4](../../includes/RP/model4.md)]<span data-ttu-id="2d389-126"> Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="2d389-126"> The next tutorial explains the files created by scaffolding.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="2d389-127">[Précédent : Bien démarrer](xref:tutorials/razor-pages-vsc/razor-pages-start)
[Suivant : Pages Razor obtenues par génération de modèles automatique](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2d389-127">[Previous: Getting Started](xref:tutorials/razor-pages-vsc/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

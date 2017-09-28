---
title: "Ajout d’un modèle à une application de pages Razor avec Visual Studio pour Mac"
author: rick-anderson
description: "Ajout d’un modèle à une application de pages Razor dans ASP.NET Core à l’aide de Visual Studio pour Mac"
keywords: "ASP.NET Core,pages Razor,Razor,MVC,modèle"
ms.author: riande
manager: wpickett
ms.date: 08/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages-vsc/model
ms.openlocfilehash: 445c13542bd4a7561eb6aa509b1aabe47cb45468
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2017
---
# <a name="adding-a-model-to-a-razor-pages-app-in-aspnet-core-with-visual-studio-for-mac"></a>Ajout d’un modèle à une application de pages Razor dans ASP.NET Core avec Visual Studio pour Mac

[!INCLUDE[model1](../../includes/RP/model1.md)]

## <a name="add-a-data-model"></a>Ajouter un modèle de données

* Ajoutez un dossier nommé *Models*.
* Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.
* Ajoutez le code suivant au fichier *Models/Movie.cs* :

[!INCLUDE[model 2](../../includes/RP/model2.md)]
[!INCLUDE[model 2a](../../includes/RP/model2a.md)]

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices2&highlight=3-6)]

Générez le projet pour vérifier qu’il ne comporte aucune erreur.

### <a name="entity-framework-core-nuget-packages-for-migrations"></a>Packages NuGet Entity Framework Core pour les migrations

Les outils EF de l’interface de ligne de commande (CLI) sont fournis dans [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet). Pour installer ce package, ajoutez-le à la collection `DotNetCliToolReference` dans le fichier *.csproj*. **Remarque :** Vous devez installer ce package en modifiant le fichier *.csproj*. Vous ne pouvez pas utiliser la commande `install-package` ou le GUI (interface graphique utilisateur) du Gestionnaire de package.

Modifiez le fichier *RazorPagesMovie.csproj* :

* Sélectionnez **Fichier > Ouvrir un fichier**, puis sélectionnez le fichier *RazorPagesMovie.csproj*.
* Ajoutez `<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />` en surbrillance dans le code suivant :

[!code-xml[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/RazorPagesMovie.cli.csproj?highlight=10)]
[!INCLUDE[model 3](../../includes/RP/model3.md)]

<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a>Effectuer la génération de modèles automatique pour le modèle Movie

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).
* Exécutez la commande suivante :

**Remarque : Exécutez la commande suivante sur Windows. Pour macOS et Linux, consultez la commande suivante**

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* Sur macOS et Linux, exécutez la commande suivante :

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

Si vous obtenez l’erreur :
  ```
  The process cannot access the file 
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll' 
  because it is being used by another process.
  ```

Quittez Visual Studio et réexécutez la commande.

[!INCLUDE[model 4](../../includes/RP/model4.md)] Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.

>[!div class="step-by-step"]
[Précédent : Bien démarrer](xref:tutorials/razor-pages-vsc/razor-pages-start)
[Suivant : Pages Razor obtenues par génération de modèles automatique](xref:tutorials/razor-pages/page)

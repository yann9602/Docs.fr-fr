---
title: "Ajout d’un modèle à une application de pages Razor avec Visual Studio pour Mac"
author: rick-anderson
description: "Ajout d’un modèle à une application de pages Razor dans ASP.NET Core à l’aide de Visual Studio pour Mac"
manager: wpickett
ms.author: riande
ms.date: 08/27/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages-vsc/model
ms.openlocfilehash: 9600392b47fb8b1dded06faefaff1bf87d67af4e
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="adding-a-model-to-a-razor-pages-app-in-aspnet-core-with-visual-studio-code"></a>Ajout d’un modèle à une application de pages Razor dans ASP.NET Core avec Visual Studio Code

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

* Sélectionnez **Fichier** > **Ouvrir un fichier**, puis sélectionnez le fichier *RazorPagesMovie.csproj*.
* Ajoutez la référence d’outil de `Microsoft.EntityFrameworkCore.Tools.DotNet` au deuxième **\<ItemGroup>** :

[!code-xml[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_cli_sample/RazorPagesMovie/RazorPagesMovie.cli.csproj)]

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
[Suivant : Pages Razor obtenues par génération de modèles automatique](xref:tutorials/razor-pages-vsc/page)

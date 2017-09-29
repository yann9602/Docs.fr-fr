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
uid: tutorials/razor-pages-mac/model
ms.openlocfilehash: 648ecd3a782fa489b727982ce5f7a2087539bf38
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="adding-a-model-to-a-razor-pages-app-in-aspnet-core-with-visual-studio-for-mac"></a>Ajout d’un modèle à une application de pages Razor dans ASP.NET Core avec Visual Studio pour Mac

[!INCLUDE[model1](../../includes/RP/model1.md)]

## <a name="add-a-data-model"></a>Ajouter un modèle de données

* Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **RazorPagesMovie**, puis sélectionnez **Ajouter** > **Nouveau dossier**. Nommez le dossier *Models*.
* Cliquez avec le bouton droit sur le dossier *Models*, puis sélectionnez **Ajouter** > **Nouveau fichier**.
* Dans la boîte de dialogue **Nouveau fichier** :

  * Dans le volet gauche, sélectionnez **Général**.
  * Dans le volet central, sélectionnez **Classe vide**.
  * Nommez la classe **Movie**, puis sélectionnez **Nouveau**.

[!INCLUDE[model 2](../../includes/RP/model2.md)]
[!INCLUDE[model 2a](../../includes/RP/model2a.md)]

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices2&highlight=3-6)]

Cliquez avec le bouton droit sur une ligne ondulée rouge, par exemple `MovieContext` à la ligne `services.AddDbContext<MovieContext>(options =>`. Sélectionnez **Correctif rapide > using RazorPagesMovie.Models;**. Visual Studio ajoute l’instruction using.

Générez le projet pour vérifier qu’il ne comporte aucune erreur.

![Créer une page](model/red.png)

### <a name="entity-framework-core-nuget-packages-for-migrations"></a>Packages NuGet Entity Framework Core pour les migrations

Les outils EF de l’interface de ligne de commande (CLI) sont fournis dans [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet). Pour installer ce package, ajoutez-le à la collection `DotNetCliToolReference` dans le fichier *.csproj*. **Remarque :** Vous devez installer ce package en modifiant le fichier *.csproj*. Vous ne pouvez pas utiliser la commande `install-package` ou le GUI (interface graphique utilisateur) du Gestionnaire de package.

Pour modifier un fichier *.csproj* :

* Sélectionnez **Fichier** > **Ouvrir**, puis sélectionnez le fichier *.csproj*.
* Sélectionnez **Options**.
* Affectez à **Ouvrir avec** la valeur **Éditeur de code source**.

![Modifier le fichier csproj](model/csproj.png)

Ajoutez la référence d’outil de `Microsoft.EntityFrameworkCore.Tools.DotNet` au deuxième **\<ItemGroup>** :

[!code-xml[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_cli_sample/RazorPagesMovie/RazorPagesMovie.cli.csproj?range=12-16&highlight=4)]

[!INCLUDE[model3](../../includes/RP/model3.md)]
[!INCLUDE[model 4x](../../includes/RP/model4x.md)]

[!INCLUDE[model 4 exit](../../includes/RP/model4exit.md)]

[!INCLUDE[model 4](../../includes/RP/model4.md)]

### <a name="add-the-pagesmovies-files-to-the-project"></a>Ajouter les fichiers Pages/Movies au projet

* Dans Visual Studio, cliquez avec le bouton droit sur le dossier *Pages*, puis sélectionnez **Ajouter > Ajouter un dossier existant**.
* Sélectionnez le dossier *Movies*.
* Dans la boîte de dialogue *Choisir les fichiers à inclure dans le projet*, sélectionnez **Inclure tout**.

Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.

>[!div class="step-by-step"]
[Précédent : Bien démarrer](xref:tutorials/razor-pages-mac/razor-pages-start)
[Suivant : Pages Razor obtenues par génération de modèles automatique](xref:tutorials/razor-pages/page)

---
title: "Ajout d’un modèle à une application de pages Razor dans ASP.NET Core"
author: rick-anderson
description: "Ajout d’un modèle à une application de pages Razor dans ASP.NET Core"
keywords: ASP.NET Core,pages Razor,Razor,MVC
ms.author: riande
manager: wpickett
ms.date: 7/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/modelz
ms.openlocfilehash: 1a08ecf1ee12fa0860cb6a18c1a63eaff2ddfbed
ms.sourcegitcommit: d9ec19e5452af83648074db5d96c0a0f4f9e7f9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2017
---
# <a name="adding-a-model-to-a-razor-pages-app"></a>Ajout d’un modèle à une application de pages Razor

[!INCLUDE[model1](../../includes/RP/model1.md)]

## <a name="add-a-data-model"></a>Ajouter un modèle de données

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **RazorPagesMovie** > **Ajouter** > **Nouveau dossier**. Nommez le dossier *Models*.

Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**. Nommez la classe **Movie**, puis ajoutez les propriétés suivantes :

[!INCLUDE[model 2](../../includes/RP/model2.md)]

<a name="cs"></a>
### <a name="add-a-database-connection-string"></a>Ajouter une chaîne de connexion de base de données

Ajoutez une chaîne de connexion au fichier *appsettings.json*.

[!code-json[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a>Inscrire le contexte de base de données

Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans le fichier *Startup.cs*.

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-6)]

Générez le projet pour vérifier qu’il ne comporte aucune erreur.

<a name="pmc"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a>Ajouter un outil de génération de modèles automatique et effectuer la migration initiale

Dans cette section, effectuez les tâches suivantes à l’aide de la console du gestionnaire de package :

* Ajoutez le package de génération de code web Visual Studio. Ce package est nécessaire à l’exécution du moteur de génération de modèles automatique.
* Ajoutez une migration initiale.
* Mettez à jour la base de données avec la migration initiale.

Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.

  ![Menu de la console du gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design -Version 2.0.0
Add-Migration Initial
Update-Database
```

La commande `Install-Package` installe les outils nécessaires à l’exécution du moteur de génération de modèles automatique.

La commande `Add-Migration` génère le code nécessaire à la création du schéma de base de données initial. Le schéma est basé sur le modèle spécifié dans le fichier `DbContext` (dans *Models/MovieContext.cs*). L’argument `Initial` est utilisé pour nommer les migrations. Vous pouvez utiliser n’importe quel nom, mais par convention, choisissez un nom qui décrit la migration. Pour plus d’informations, consultez [Présentation des migrations](xref:data/ef-mvc/migrations#introduction-to-migrations).

La commande `Update-Database` exécute la méthode `Up` dans le fichier *Migrations/\<horodatage>_InitialCreate.cs*, ce qui entraîne la création de la base de données.

[!INCLUDE[model 4windows](../../includes/RP/model4Win.md)]

[!INCLUDE[model 4](../../includes/RP/model4.md)]

Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.

>[!div class="step-by-step"]
[Précédent : Bien démarrer](xref:tutorials/razor-pages/razor-pages-start)
[Suivant : Pages Razor obtenues par génération de modèles automatique](xref:tutorials/razor-pages/page)    
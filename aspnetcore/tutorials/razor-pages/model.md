---
title: "Ajout d’un modèle à une application de pages Razor dans ASP.NET Core"
author: rick-anderson
description: "Ajout d’un modèle à une application de pages Razor dans ASP.NET Core"
manager: wpickett
ms.author: riande
ms.date: 07/27/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages/model
ms.openlocfilehash: 0ce7693bfdc37d930488304b329dbcd533a5ec1d
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="adding-a-model-to-a-razor-pages-app"></a>Ajout d’un modèle à une application de pages Razor

[!INCLUDE[model1](../../includes/RP/model1.md)]

## <a name="add-a-data-model"></a>Ajouter un modèle de données

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **RazorPagesMovie** > **Ajouter** > **Nouveau dossier**. Nommez le dossier *Models*.

Cliquez avec le bouton droit sur le dossier *Models*. Sélectionnez **Ajouter** > **Classe**. Nommez la classe **Movie**, puis ajoutez les propriétés suivantes :

[!INCLUDE[model 2](../../includes/RP/model2.md)]

<a name="cs"></a>
### <a name="add-a-database-connection-string"></a>Ajouter une chaîne de connexion de base de données

Ajoutez une chaîne de connexion au fichier *appsettings.json*.

[!code-json[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a>Inscrire le contexte de base de données

Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans le fichier *Startup.cs*.

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-5,7-9)]

Générez le projet pour vérifier qu’il ne comporte aucune erreur.

<a name="pmc"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a>Ajouter un outil de génération de modèles automatique et effectuer la migration initiale

Dans cette section, effectuez les tâches suivantes à l’aide de la console du gestionnaire de package :

* Ajoutez le package de génération de code web Visual Studio. Ce package est nécessaire à l’exécution du moteur de génération de modèles automatique.
* Ajoutez une migration initiale.
* Mettez à jour la base de données avec la migration initiale.

Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package**.

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
Add-Migration Initial
Update-Database
```

La commande `Install-Package` installe les outils nécessaires à l’exécution du moteur de génération de modèles automatique.

La commande `Add-Migration` génère le code nécessaire à la création du schéma de base de données initial. Le schéma est basé sur le modèle spécifié dans le fichier `DbContext` (dans *Models/MovieContext.cs*). L’argument `Initial` est utilisé pour nommer les migrations. Vous pouvez utiliser n’importe quel nom, mais par convention, choisissez un nom qui décrit la migration. Pour plus d’informations, consultez [Présentation des migrations](xref:data/ef-mvc/migrations#introduction-to-migrations).

La commande `Update-Database` exécute la méthode `Up` dans le fichier *Migrations/\<horodatage>_InitialCreate.cs*, ce qui entraîne la création de la base de données.

[!INCLUDE[model 4windows](../../includes/RP/model4Win.md)]

[!INCLUDE[model 4](../../includes/RP/model4tbl.md)]

<a name="test"></a>
### <a name="test-the-app"></a>Tester l’application

* Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).
* Testez le lien **Créer**.

 ![Créer une page](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* Testez les liens **Modifier**, **Détails** et **Supprimer**.

Si vous obtenez une exception SQL, vérifiez que vous avez exécuté les migrations et mis à jour la base de données :

Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.

>[!div class="step-by-step"]
[Précédent : Bien démarrer](xref:tutorials/razor-pages/razor-pages-start)
[Suivant : Pages Razor obtenues par génération de modèles automatique](xref:tutorials/razor-pages/page)    

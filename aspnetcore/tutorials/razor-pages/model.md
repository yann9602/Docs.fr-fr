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
# <a name="adding-a-model-to-a-razor-pages-app"></a><span data-ttu-id="c536c-104">Ajout d’un modèle à une application de pages Razor</span><span class="sxs-lookup"><span data-stu-id="c536c-104">Adding a model to a Razor Pages app</span></span>

[!INCLUDE[model1](../../includes/RP/model1.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="c536c-105">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="c536c-105">Add a data model</span></span>

<span data-ttu-id="c536c-106">Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **RazorPagesMovie** > **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="c536c-106">In Solution Explorer, right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="c536c-107">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="c536c-107">Name the folder *Models*.</span></span>

<span data-ttu-id="c536c-108">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="c536c-108">Right click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="c536c-109">Nommez la classe **Movie**, puis ajoutez les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="c536c-109">Name the class **Movie** and add the following properties:</span></span>

[!INCLUDE[model 2](../../includes/RP/model2.md)]

<a name="cs"></a>
### <a name="add-a-database-connection-string"></a><span data-ttu-id="c536c-110">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="c536c-110">Add a database connection string</span></span>

<span data-ttu-id="c536c-111">Ajoutez une chaîne de connexion au fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c536c-111">Add a connection string to the *appsettings.json* file.</span></span>

<span data-ttu-id="c536c-112">[!code-json[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]</span><span class="sxs-lookup"><span data-stu-id="c536c-112">[!code-json[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]</span></span>

<a name="reg"></a>
###  <a name="register-the-database-context"></a><span data-ttu-id="c536c-113">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="c536c-113">Register the database context</span></span>

<span data-ttu-id="c536c-114">Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans le fichier *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="c536c-114">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in the *Startup.cs* file.</span></span>

<span data-ttu-id="c536c-115">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-6)]</span><span class="sxs-lookup"><span data-stu-id="c536c-115">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-6)]</span></span>

<span data-ttu-id="c536c-116">Générez le projet pour vérifier qu’il ne comporte aucune erreur.</span><span class="sxs-lookup"><span data-stu-id="c536c-116">Build the project to verify you don't have any errors.</span></span>

<a name="pmc"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a><span data-ttu-id="c536c-117">Ajouter un outil de génération de modèles automatique et effectuer la migration initiale</span><span class="sxs-lookup"><span data-stu-id="c536c-117">Add scaffold tooling and perform initial migration</span></span>

<span data-ttu-id="c536c-118">Dans cette section, effectuez les tâches suivantes à l’aide de la console du gestionnaire de package :</span><span class="sxs-lookup"><span data-stu-id="c536c-118">In this section, you use the Package Manager Console (PMC) to:</span></span>

* <span data-ttu-id="c536c-119">Ajoutez le package de génération de code web Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c536c-119">Add the Visual Studio web code generation package.</span></span> <span data-ttu-id="c536c-120">Ce package est nécessaire à l’exécution du moteur de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="c536c-120">This package is required to run the scaffolding engine.</span></span>
* <span data-ttu-id="c536c-121">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="c536c-121">Add an initial migration.</span></span>
* <span data-ttu-id="c536c-122">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="c536c-122">Update the database with the initial migration.</span></span>

<span data-ttu-id="c536c-123">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="c536c-123">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![Menu de la console du gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="c536c-125">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="c536c-125">In the PMC, enter the following commands:</span></span>

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design -Version 2.0.0
Add-Migration Initial
Update-Database
```

<span data-ttu-id="c536c-126">La commande `Install-Package` installe les outils nécessaires à l’exécution du moteur de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="c536c-126">The `Install-Package` command installs the tooling required to run the scaffolding engine.</span></span>

<span data-ttu-id="c536c-127">La commande `Add-Migration` génère le code nécessaire à la création du schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="c536c-127">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="c536c-128">Le schéma est basé sur le modèle spécifié dans le fichier `DbContext` (dans *Models/MovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="c536c-128">The schema is based on the model specified in the `DbContext` (In the *Models/MovieContext.cs* file).</span></span> <span data-ttu-id="c536c-129">L’argument `Initial` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="c536c-129">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="c536c-130">Vous pouvez utiliser n’importe quel nom, mais par convention, choisissez un nom qui décrit la migration.</span><span class="sxs-lookup"><span data-stu-id="c536c-130">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="c536c-131">Pour plus d’informations, consultez [Présentation des migrations](xref:data/ef-mvc/migrations#introduction-to-migrations).</span><span class="sxs-lookup"><span data-stu-id="c536c-131">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="c536c-132">La commande `Update-Database` exécute la méthode `Up` dans le fichier *Migrations/\<horodatage>_InitialCreate.cs*, ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="c536c-132">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE[model 4windows](../../includes/RP/model4Win.md)]

[!INCLUDE[model 4](../../includes/RP/model4.md)]

<span data-ttu-id="c536c-133">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="c536c-133">The next tutorial explains the files created by scaffolding.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="c536c-134">[Précédent : Bien démarrer](xref:tutorials/razor-pages/razor-pages-start)
[Suivant : Pages Razor obtenues par génération de modèles automatique](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="c536c-134">[Previous: Getting Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>    
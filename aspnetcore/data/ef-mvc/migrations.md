---
title: "Cœur de ASP.NET MVC avec EF Core - Migrations - 4 10"
author: tdykstra
description: "Dans ce didacticiel, vous démarrez à l’aide de la fonctionnalité de migrations EF principales pour la gestion des modifications du modèle de données dans une application ASP.NET MVC de base."
keywords: Migrations de ASP.NET Core, Entity Framework Core,
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 81f6c9c2-a819-4f3a-97a4-4b0503b56c26
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/migrations
ms.openlocfilehash: 4d81099d1ab97a8a49d96657153a54aa96dd6bf8
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2017
---
# <a name="migrations---ef-core-with-aspnet-core-mvc-tutorial-4-of-10"></a><span data-ttu-id="2d01e-104">Migrations - Core EF avec le didacticiel d’ASP.NET MVC de base (4 sur 10)</span><span class="sxs-lookup"><span data-stu-id="2d01e-104">Migrations - EF Core with ASP.NET Core MVC tutorial (4 of 10)</span></span>

<span data-ttu-id="2d01e-105">Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2d01e-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2d01e-106">L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2d01e-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="2d01e-107">Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).</span><span class="sxs-lookup"><span data-stu-id="2d01e-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="2d01e-108">Dans ce didacticiel, vous démarrez à l’aide de la fonctionnalité de migrations EF principales pour la gestion des modifications du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-108">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="2d01e-109">Dans les didacticiels suivants, vous allez ajouter des migrations plus que vous modifiez le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-109">In later tutorials, you'll add more migrations as you change the data model.</span></span>

## <a name="introduction-to-migrations"></a><span data-ttu-id="2d01e-110">Introduction aux migrations</span><span class="sxs-lookup"><span data-stu-id="2d01e-110">Introduction to migrations</span></span>

<span data-ttu-id="2d01e-111">Lorsque vous développez une application, votre modèle de données change fréquemment et chaque fois que les modifications apportées au modèle, il est désynchronisé avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-111">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="2d01e-112">Vous avez démarré ces didacticiels en configurant l’Entity Framework pour créer la base de données si elle n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="2d01e-112">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="2d01e-113">Ensuite, chaque fois que vous modifiez le modèle de données : ajoutez, supprimez, ou modifiez des classes d’entité ou modifiez votre classe DbContext--vous pouvez supprimer la base de données et EF crée un nouveau qui correspond au modèle et l’alimente avec les données de test.</span><span class="sxs-lookup"><span data-stu-id="2d01e-113">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="2d01e-114">Cette méthode de synchronisation de la base de données avec le modèle de données fonctionne bien jusqu'à ce que vous déployez l’application en production.</span><span class="sxs-lookup"><span data-stu-id="2d01e-114">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="2d01e-115">Lorsque l’application est en cours d’exécution en production, qu'il stocke généralement les données que vous souhaitez conserver, et vous ne voulez pas perdre tous les éléments chaque fois que vous apportez une modification telles que l’ajout d’une nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="2d01e-115">When the application is running in production it is usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="2d01e-116">La fonctionnalité EF Core Migrations résout ce problème en activant EF mettre à jour le schéma de base de données au lieu de créer une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-116">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

## <a name="entity-framework-core-nuget-packages-for-migrations"></a><span data-ttu-id="2d01e-117">Entity Framework Core NuGet packages pour les migrations</span><span class="sxs-lookup"><span data-stu-id="2d01e-117">Entity Framework Core NuGet packages for migrations</span></span>

<span data-ttu-id="2d01e-118">Pour utiliser des migrations, vous pouvez utiliser la **Package Manager Console** (PMC) ou de l’interface de ligne de commande (CLI).</span><span class="sxs-lookup"><span data-stu-id="2d01e-118">To work with migrations, you can use the **Package Manager Console** (PMC) or the command-line interface (CLI).</span></span>  <span data-ttu-id="2d01e-119">Ces didacticiels montrent comment utiliser les commandes CLI.</span><span class="sxs-lookup"><span data-stu-id="2d01e-119">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="2d01e-120">Plus d’informations sur la CFP est à [la fin de ce didacticiel](#pmc).</span><span class="sxs-lookup"><span data-stu-id="2d01e-120">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

<span data-ttu-id="2d01e-121">Les outils EF de l’interface de ligne de commande (CLI) sont fournis dans [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span><span class="sxs-lookup"><span data-stu-id="2d01e-121">The EF tools for the command-line interface (CLI) are provided in [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span></span> <span data-ttu-id="2d01e-122">Pour installer ce package, ajoutez-la à la `DotNetCliToolReference` collection dans le *.csproj* de fichiers, comme indiqué.</span><span class="sxs-lookup"><span data-stu-id="2d01e-122">To install this package, add it to the `DotNetCliToolReference` collection in the *.csproj* file, as shown.</span></span> <span data-ttu-id="2d01e-123">**Remarque :** vous devez installer ce package en modifiant le *.csproj* de fichiers ; vous ne pouvez pas utiliser le `install-package` commande ou l’interface utilisateur graphique du Gestionnaire de package.</span><span class="sxs-lookup"><span data-stu-id="2d01e-123">**Note:** You have to install this package by editing the *.csproj* file; you can't use the `install-package` command or the package manager GUI.</span></span> <span data-ttu-id="2d01e-124">Vous pouvez modifier le *.csproj* fichier en cliquant sur le nom du projet dans **l’Explorateur de solutions** et en sélectionnant **ContosoUniversity.csproj de modifier**.</span><span class="sxs-lookup"><span data-stu-id="2d01e-124">You can edit the *.csproj* file by right-clicking the project name in **Solution Explorer** and selecting **Edit ContosoUniversity.csproj**.</span></span>

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?range=12-15&highlight=2)]
  
<span data-ttu-id="2d01e-125">(Dans cet exemple, les numéros de version étaient en cours lors de l’écriture de ce didacticiel.)</span><span class="sxs-lookup"><span data-stu-id="2d01e-125">(The version numbers in this example were current when the tutorial was written.)</span></span> 

## <a name="change-the-connection-string"></a><span data-ttu-id="2d01e-126">Modifier la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="2d01e-126">Change the connection string</span></span>

<span data-ttu-id="2d01e-127">Dans le *appsettings.json* , modifiez le nom de la base de données dans la chaîne de connexion à ContosoUniversity2 ou un autre nom que vous avez utilisé sur l’ordinateur que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="2d01e-127">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

<span data-ttu-id="2d01e-128">[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]</span><span class="sxs-lookup"><span data-stu-id="2d01e-128">[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]</span></span>

<span data-ttu-id="2d01e-129">Cette modification affecte le projet afin que la première migration créera une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-129">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="2d01e-130">Ce n’est pas requis pour la prise en main de migrations, mais vous le verrez plus tard pourquoi il est judicieux.</span><span class="sxs-lookup"><span data-stu-id="2d01e-130">This isn't required for getting started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="2d01e-131">Comme alternative à la modification du nom de la base de données, vous pouvez supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-131">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="2d01e-132">Utilisez **l’Explorateur d’objets SQL Server** (SSOX) ou le `database drop` commande CLI :</span><span class="sxs-lookup"><span data-stu-id="2d01e-132">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
> ```console
> dotnet ef database drop
> ```
> <span data-ttu-id="2d01e-133">La section suivante explique comment exécuter des commandes CLI.</span><span class="sxs-lookup"><span data-stu-id="2d01e-133">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="2d01e-134">Créer une migration initiale</span><span class="sxs-lookup"><span data-stu-id="2d01e-134">Create an initial migration</span></span>

<span data-ttu-id="2d01e-135">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="2d01e-135">Save your changes and build the project.</span></span> <span data-ttu-id="2d01e-136">Ensuite, ouvrez une fenêtre de commande et accédez au dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="2d01e-136">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="2d01e-137">Voici un moyen rapide pour ce faire :</span><span class="sxs-lookup"><span data-stu-id="2d01e-137">Here's a quick way to do that:</span></span>

* <span data-ttu-id="2d01e-138">Dans **l’Explorateur de solutions**, cliquez sur le projet et choisissez **ouvert dans l’Explorateur de fichiers** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="2d01e-138">In **Solution Explorer**, right-click the project and choose **Open in File Explorer** from the context menu.</span></span>

  ![Ouvrir dans l’élément de menu de l’Explorateur de fichiers](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="2d01e-140">Entrez « cmd » dans la barre d’adresses et appuyez sur ENTRÉE.</span><span class="sxs-lookup"><span data-stu-id="2d01e-140">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Fenêtre de commande ouverte](migrations/_static/open-command-window.png)

<span data-ttu-id="2d01e-142">Dans la fenêtre de commande, entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2d01e-142">Enter the following command in the command window:</span></span>

```console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="2d01e-143">Vous voyez la sortie comme suit dans la fenêtre de commande :</span><span class="sxs-lookup"><span data-stu-id="2d01e-143">You see output like the following in the command window:</span></span>

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="2d01e-144">Si vous voyez un message d’erreur *aucun exécutable ne trouvé correspondant commande « dotnet-ef »*, consultez [ce billet de blog](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) pour le dépannage de l’aide.</span><span class="sxs-lookup"><span data-stu-id="2d01e-144">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="2d01e-145">Si vous voyez un message d’erreur «*accéder au fichier... ContosoUniversity.dll, car il est utilisé par un autre processus.* », recherchez l’icône IIS Express dans la barre des tâches Windows, faites un clic droit, puis cliquez sur **ContosoUniversity > Stop Site**.</span><span class="sxs-lookup"><span data-stu-id="2d01e-145">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="2d01e-146">Examiner le haut et vers le bas de méthodes</span><span class="sxs-lookup"><span data-stu-id="2d01e-146">Examine the Up and Down methods</span></span>

<span data-ttu-id="2d01e-147">Lorsque vous avez exécuté le `migrations add` EF généré le code qui crée la base de données à partir de zéro de la commande.</span><span class="sxs-lookup"><span data-stu-id="2d01e-147">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="2d01e-148">Ce code se trouve dans le *Migrations* dossier, dans le fichier nommé  *\<timestamp > _InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="2d01e-148">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="2d01e-149">Le `Up` méthode de la `InitialCreate` classe crée les tables de base de données qui correspondent aux jeux d’entités de modèle de données, et le `Down` méthode les supprime, comme indiqué dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="2d01e-149">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

<span data-ttu-id="2d01e-150">[!code-csharp[Main](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]</span><span class="sxs-lookup"><span data-stu-id="2d01e-150">[!code-csharp[Main](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]</span></span>

<span data-ttu-id="2d01e-151">Appels de migrations le `Up` méthode pour implémenter les modifications de modèle de données pour une migration.</span><span class="sxs-lookup"><span data-stu-id="2d01e-151">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="2d01e-152">Lorsque vous entrez une commande pour restaurer la mise à jour, les appels de Migrations le `Down` (méthode).</span><span class="sxs-lookup"><span data-stu-id="2d01e-152">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="2d01e-153">Ce code est pour la migration initiale qui a été créée lorsque vous avez entré la `migrations add InitialCreate` commande.</span><span class="sxs-lookup"><span data-stu-id="2d01e-153">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="2d01e-154">Le paramètre de nom de la migration (« InitialCreate » dans l’exemple) est utilisé pour le nom de fichier et peut être tout ce que vous voulez.</span><span class="sxs-lookup"><span data-stu-id="2d01e-154">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="2d01e-155">Il est conseillé de choisir un mot ou une expression qui résume ce qui est effectué dans la migration.</span><span class="sxs-lookup"><span data-stu-id="2d01e-155">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="2d01e-156">Par exemple, vous pouvez nommer une migration ultérieure « AddDepartmentTable ».</span><span class="sxs-lookup"><span data-stu-id="2d01e-156">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="2d01e-157">Si vous avez créé la migration initiale lors de la base de données existe déjà, le code de création de base de données est généré, mais ne doit pas nécessairement exécuter, car la base de données correspond déjà le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-157">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="2d01e-158">Lorsque vous déployez l’application vers un autre environnement dans lequel la base de données n’existe pas encore, ce code sera exécuté pour créer votre base de données, il est donc judicieux de tester au préalable.</span><span class="sxs-lookup"><span data-stu-id="2d01e-158">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="2d01e-159">C’est pourquoi vous avez modifié le nom de la base de données dans la chaîne de connexion précédemment--afin que les migrations peuvent créer un nouveau à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="2d01e-159">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="examine-the-data-model-snapshot"></a><span data-ttu-id="2d01e-160">Examinez l’instantané de modèle de données</span><span class="sxs-lookup"><span data-stu-id="2d01e-160">Examine the data model snapshot</span></span>

<span data-ttu-id="2d01e-161">Migrations crée également un *instantané* du schéma de base de données en cours dans *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="2d01e-161">Migrations also creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="2d01e-162">Ce code ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="2d01e-162">Here's what that code looks like:</span></span>

<span data-ttu-id="2d01e-163">[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]</span><span class="sxs-lookup"><span data-stu-id="2d01e-163">[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]</span></span>

<span data-ttu-id="2d01e-164">Étant donné que le schéma de base de données actuelle est représenté dans le code, EF Core ne doit pas interagir avec la base de données pour créer des migrations.</span><span class="sxs-lookup"><span data-stu-id="2d01e-164">Because the current database schema is represented in code, EF Core doesn't have to interact with the database to create migrations.</span></span> <span data-ttu-id="2d01e-165">Lorsque vous ajoutez une migration, EF détermine ce qui a changé en comparant le modèle de données pour le fichier d’instantané.</span><span class="sxs-lookup"><span data-stu-id="2d01e-165">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span> <span data-ttu-id="2d01e-166">EF interagit avec la base de données uniquement lorsqu’il a mettre à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-166">EF interacts with the database only when it has to update the database.</span></span> 

<span data-ttu-id="2d01e-167">Le fichier d’instantané doit être synchronisée avec les migrations qui créent, vous ne pouvez pas supprimer une migration uniquement en supprimant le fichier nommé  *\<timestamp > _\<migrationname > .cs*.</span><span class="sxs-lookup"><span data-stu-id="2d01e-167">The snapshot file has to be kept in sync with the migrations that create it, so you can't remove a migration just by deleting the file named  *\<timestamp>_\<migrationname>.cs*.</span></span> <span data-ttu-id="2d01e-168">Si vous supprimez ce fichier, les migrations restantes sera synchronisées avec le fichier d’instantané de base de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-168">If you delete that file, the remaining migrations will be out of sync with the database snapshot file.</span></span> <span data-ttu-id="2d01e-169">Pour supprimer la dernière migration que vous avez ajouté, utilisez la [supprimer des migrations d’ef dotnet](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) commande.</span><span class="sxs-lookup"><span data-stu-id="2d01e-169">To delete the last migration that you added, use the [dotnet ef migrations remove](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command.</span></span>

## <a name="apply-the-migration-to-the-database"></a><span data-ttu-id="2d01e-170">Appliquer la migration vers la base de données</span><span class="sxs-lookup"><span data-stu-id="2d01e-170">Apply the migration to the database</span></span>

<span data-ttu-id="2d01e-171">Dans la fenêtre de commande, entrez la commande suivante pour créer la base de données et les tables qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="2d01e-171">In the command window, enter the following command to create the database and tables in it.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="2d01e-172">La sortie de la commande est similaire à la `migrations add` de commande, à ceci près que vous consultez les journaux des commandes SQL qui configurer la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-172">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="2d01e-173">La plupart des journaux est omise dans l’exemple de sortie suivant.</span><span class="sxs-lookup"><span data-stu-id="2d01e-173">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="2d01e-174">Si vous préférez ne pas voir ce niveau de détail dans les messages de journal, vous pouvez modifier les niveaux de journal dans le *appsettings. Development.JSON* fichier.</span><span class="sxs-lookup"><span data-stu-id="2d01e-174">If you prefer not to see this level of detail in log messages, you can change the log levels in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="2d01e-175">Pour plus d’informations, consultez [Introduction à la journalisation](xref:fundamentals/logging).</span><span class="sxs-lookup"><span data-stu-id="2d01e-175">For more information, see [Introduction to logging](xref:fundamentals/logging).</span></span>

```text
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (467ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (20ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20170816151242_InitialCreate', N'2.0.0-rtm-26452');
Done.
```

<span data-ttu-id="2d01e-176">Utilisez **l’Explorateur d’objets SQL Server** pour inspecter la base de données comme vous le faisiez dans le premier didacticiel.</span><span class="sxs-lookup"><span data-stu-id="2d01e-176">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="2d01e-177">Vous remarquerez que l’ajout d’une table __EFMigrationsHistory qui conserve les migrations ont été appliquées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-177">You'll notice the addition of an __EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="2d01e-178">Afficher les données de cette table, et vous verrez une ligne pour la première migration.</span><span class="sxs-lookup"><span data-stu-id="2d01e-178">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="2d01e-179">(Le dernier journal dans l’exemple de sortie CLI précédent montre l’instruction INSERT qui crée cette ligne.)</span><span class="sxs-lookup"><span data-stu-id="2d01e-179">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="2d01e-180">Exécutez l’application pour vérifier que tout fonctionne toujours les mêmes qu’avant.</span><span class="sxs-lookup"><span data-stu-id="2d01e-180">Run the application to verify that everything still works the same as before.</span></span>

![Page d’Index les étudiants](migrations/_static/students-index.png)

<a id="pmc"></a>
## <a name="command-line-interface-cli-vs-package-manager-console-pmc"></a><span data-ttu-id="2d01e-182">Par rapport à l’interface de ligne de commande (CLI) Console du Gestionnaire de package (PMC)</span><span class="sxs-lookup"><span data-stu-id="2d01e-182">Command-line interface (CLI) vs. Package Manager Console (PMC)</span></span>

<span data-ttu-id="2d01e-183">L’outillage EF pour la gestion des migrations ne sont disponible à partir de commandes .NET Core CLI ou d’applets de commande PowerShell dans Visual Studio **Package Manager Console** les fenêtre (PMC).</span><span class="sxs-lookup"><span data-stu-id="2d01e-183">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="2d01e-184">Ce didacticiel montre comment utiliser l’interface CLI, mais vous pouvez utiliser le PMC si vous préférez.</span><span class="sxs-lookup"><span data-stu-id="2d01e-184">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="2d01e-185">Les commandes EF pour les commandes PMC se trouvent dans le [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span><span class="sxs-lookup"><span data-stu-id="2d01e-185">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="2d01e-186">Ce package est déjà inclus dans le [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, sans que vous ayez à installer.</span><span class="sxs-lookup"><span data-stu-id="2d01e-186">This package is already included in the [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, so you don't have to install it.</span></span>

<span data-ttu-id="2d01e-187">**Important :** ce n’est pas le même package que celui que vous installez pour l’interface CLI en modifiant le *.csproj* fichier.</span><span class="sxs-lookup"><span data-stu-id="2d01e-187">**Important:** This is not the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="2d01e-188">Le nom de celle-ci se termine dans `Tools`, contrairement au nom de package CLI qui se termine par `Tools.DotNet`.</span><span class="sxs-lookup"><span data-stu-id="2d01e-188">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="2d01e-189">Pour plus d’informations sur les commandes CLI, consultez [.NET Core CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="2d01e-189">For more information about the CLI commands, see [.NET Core CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet).</span></span> 

<span data-ttu-id="2d01e-190">Pour plus d’informations sur les commandes PMC, consultez [Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="2d01e-190">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="summary"></a><span data-ttu-id="2d01e-191">Résumé</span><span class="sxs-lookup"><span data-stu-id="2d01e-191">Summary</span></span>

<span data-ttu-id="2d01e-192">Dans ce didacticiel, vous avez vu comment créer et appliquer votre première migration.</span><span class="sxs-lookup"><span data-stu-id="2d01e-192">In this tutorial, you've seen how to create and apply your first migration.</span></span> <span data-ttu-id="2d01e-193">Dans l’étape suivante du didacticiel, vous pourrez commencer examinant rubriques plus avancées en développant le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="2d01e-193">In the next tutorial, you'll begin looking at more advanced topics by expanding the data model.</span></span> <span data-ttu-id="2d01e-194">Tout au long du processus, vous allez créer et appliquer des migrations supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="2d01e-194">Along the way you'll create and apply additional migrations.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="2d01e-195">[Précédent](sort-filter-page.md)
[Suivant](complex-data-model.md)</span><span class="sxs-lookup"><span data-stu-id="2d01e-195">[Previous](sort-filter-page.md)
[Next](complex-data-model.md)</span></span>  

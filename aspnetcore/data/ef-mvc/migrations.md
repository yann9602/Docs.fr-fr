---
title: "Cœur de ASP.NET MVC avec EF Core - Migrations - 4 10"
author: tdykstra
description: "Dans ce didacticiel, vous démarrez à l’aide de la fonctionnalité de migrations EF principales pour la gestion des modifications du modèle de données dans une application ASP.NET MVC de base."
keywords: ASP.NET Core, Entity Framework Core, migrations
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 81f6c9c2-a819-4f3a-97a4-4b0503b56c26
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/migrations
ms.openlocfilehash: 20b05801ac666feef29fd05dd3e4738b1bd50b86
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="migrations---ef-core-with-aspnet-core-mvc-tutorial-4-of-10"></a>Migrations - Core EF avec le didacticiel d’ASP.NET MVC de base (4 sur 10)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).

Dans ce didacticiel, vous démarrez à l’aide de la fonctionnalité de migrations EF principales pour la gestion des modifications du modèle de données. Dans les didacticiels suivants, vous allez ajouter des migrations plus que vous modifiez le modèle de données.

## <a name="introduction-to-migrations"></a>Introduction aux migrations

Lorsque vous développez une application, votre modèle de données change fréquemment et chaque fois que les modifications apportées au modèle, il est désynchronisé avec la base de données. Vous avez démarré ces didacticiels en configurant l’Entity Framework pour créer la base de données si elle n’existe pas. Ensuite, chaque fois que vous modifiez le modèle de données : ajoutez, supprimez, ou modifiez des classes d’entité ou modifiez votre classe DbContext--vous pouvez supprimer la base de données et EF crée un nouveau qui correspond au modèle et l’alimente avec les données de test.

Cette méthode de synchronisation de la base de données avec le modèle de données fonctionne bien jusqu'à ce que vous déployez l’application en production. Lorsque l’application est en cours d’exécution en production, qu'il stocke généralement les données que vous souhaitez conserver, et vous ne voulez pas perdre tous les éléments chaque fois que vous apportez une modification telles que l’ajout d’une nouvelle colonne. La fonctionnalité EF Core Migrations résout ce problème en activant EF mettre à jour le schéma de base de données au lieu de créer une nouvelle base de données.

## <a name="entity-framework-core-nuget-packages-for-migrations"></a>Packages NuGet Entity Framework Core pour les migrations

Pour utiliser des migrations, vous pouvez utiliser la **Package Manager Console** (PMC) ou de l’interface de ligne de commande (CLI).  Ces didacticiels montrent comment utiliser les commandes CLI. Plus d’informations sur la CFP est à [la fin de ce didacticiel](#pmc).

Les outils EF de l’interface de ligne de commande (CLI) sont fournis dans [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet). Pour installer ce package, ajoutez-la à la `DotNetCliToolReference` collection dans le *.csproj* de fichiers, comme indiqué. **Remarque :** Vous devez installer ce package en modifiant le fichier *.csproj*. Vous ne pouvez pas utiliser la commande `install-package` ou le GUI (interface graphique utilisateur) du Gestionnaire de package. Vous pouvez modifier le *.csproj* fichier en cliquant sur le nom du projet dans **l’Explorateur de solutions** et en sélectionnant **ContosoUniversity.csproj de modifier**.

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?range=12-15&highlight=2)]
  
(Dans cet exemple, les numéros de version étaient en cours lors de l’écriture de ce didacticiel.) 

## <a name="change-the-connection-string"></a>Modifier la chaîne de connexion

Dans le *appsettings.json* , modifiez le nom de la base de données dans la chaîne de connexion à ContosoUniversity2 ou un autre nom que vous avez utilisé sur l’ordinateur que vous utilisez.

[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]

Cette modification affecte le projet afin que la première migration créera une nouvelle base de données. Ce n’est pas requis pour la prise en main de migrations, mais vous le verrez plus tard pourquoi il est judicieux.

> [!NOTE]
> Comme alternative à la modification du nom de la base de données, vous pouvez supprimer la base de données. Utilisez **l’Explorateur d’objets SQL Server** (SSOX) ou le `database drop` commande CLI :
> ```console
> dotnet ef database drop
> ```
> La section suivante explique comment exécuter des commandes CLI.

## <a name="create-an-initial-migration"></a>Créer une migration initiale

Enregistrez vos modifications et générez le projet. Ensuite, ouvrez une fenêtre de commande et accédez au dossier du projet. Voici un moyen rapide pour ce faire :

* Dans **l’Explorateur de solutions**, cliquez sur le projet et choisissez **ouvert dans l’Explorateur de fichiers** dans le menu contextuel.

  ![Ouvrir dans l’élément de menu de l’Explorateur de fichiers](migrations/_static/open-in-file-explorer.png)

* Entrez « cmd » dans la barre d’adresses et appuyez sur ENTRÉE.

  ![Fenêtre de commande ouverte](migrations/_static/open-command-window.png)

Entrez la commande suivante dans la fenêtre Commande :

```console
dotnet ef migrations add InitialCreate
```

Vous voyez la sortie comme suit dans la fenêtre de commande :

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> Si vous voyez un message d’erreur *aucun exécutable ne trouvé correspondant commande « dotnet-ef »*, consultez [ce billet de blog](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) pour le dépannage de l’aide.

Si vous voyez un message d’erreur «*accéder au fichier... ContosoUniversity.dll, car il est utilisé par un autre processus.* », recherchez l’icône IIS Express dans la barre des tâches Windows, faites un clic droit, puis cliquez sur **ContosoUniversity > Stop Site**.

## <a name="examine-the-up-and-down-methods"></a>Examiner le haut et vers le bas de méthodes

Lorsque vous avez exécuté le `migrations add` EF généré le code qui crée la base de données à partir de zéro de la commande. Ce code se trouve dans le *Migrations* dossier, dans le fichier nommé  *\<timestamp > _InitialCreate.cs*. Le `Up` méthode de la `InitialCreate` classe crée les tables de base de données qui correspondent aux jeux d’entités de modèle de données, et le `Down` méthode les supprime, comme indiqué dans l’exemple suivant.

[!code-csharp[Main](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Appels de migrations le `Up` méthode pour implémenter les modifications de modèle de données pour une migration. Lorsque vous entrez une commande pour restaurer la mise à jour, les appels de Migrations le `Down` (méthode).

Ce code est pour la migration initiale qui a été créée lorsque vous avez entré la `migrations add InitialCreate` commande. Le paramètre de nom de la migration (« InitialCreate » dans l’exemple) est utilisé pour le nom de fichier et peut être tout ce que vous voulez. Il est conseillé de choisir un mot ou une expression qui résume ce qui est effectué dans la migration. Par exemple, vous pouvez nommer une migration ultérieure « AddDepartmentTable ».

Si vous avez créé la migration initiale lors de la base de données existe déjà, le code de création de base de données est généré, mais ne doit pas nécessairement exécuter, car la base de données correspond déjà le modèle de données. Lorsque vous déployez l’application vers un autre environnement dans lequel la base de données n’existe pas encore, ce code sera exécuté pour créer votre base de données, il est donc judicieux de tester au préalable. C’est pourquoi vous avez modifié le nom de la base de données dans la chaîne de connexion précédemment--afin que les migrations peuvent créer un nouveau à partir de zéro.

## <a name="examine-the-data-model-snapshot"></a>Examinez l’instantané de modèle de données

Migrations crée également un *instantané* du schéma de base de données en cours dans *Migrations/SchoolContextModelSnapshot.cs*. Ce code ressemble à ceci :

[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]

Étant donné que le schéma de base de données actuelle est représenté dans le code, EF Core ne doit pas interagir avec la base de données pour créer des migrations. Lorsque vous ajoutez une migration, EF détermine ce qui a changé en comparant le modèle de données pour le fichier d’instantané. EF interagit avec la base de données uniquement lorsqu’il a mettre à jour la base de données. 

Le fichier d’instantané doit être synchronisée avec les migrations qui créent, vous ne pouvez pas supprimer une migration uniquement en supprimant le fichier nommé  *\<timestamp > _\<migrationname > .cs*. Si vous supprimez ce fichier, les migrations restantes sera synchronisées avec le fichier d’instantané de base de données. Pour supprimer la dernière migration que vous avez ajouté, utilisez la [supprimer des migrations d’ef dotnet](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) commande.

## <a name="apply-the-migration-to-the-database"></a>Appliquer la migration vers la base de données

Dans la fenêtre de commande, entrez la commande suivante pour créer la base de données et les tables qu’il contient.

```console
dotnet ef database update
```

La sortie de la commande est similaire à la `migrations add` de commande, à ceci près que vous consultez les journaux des commandes SQL qui configurer la base de données. La plupart des journaux est omise dans l’exemple de sortie suivant. Si vous préférez ne pas voir ce niveau de détail dans les messages de journal, vous pouvez modifier le niveau de journal dans le *appsettings. Development.JSON* fichier. Pour plus d’informations, consultez [Introduction à la journalisation](xref:fundamentals/logging/index).

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

Utilisez **l’Explorateur d’objets SQL Server** pour inspecter la base de données comme vous le faisiez dans le premier didacticiel.  Vous remarquerez que l’ajout d’une table __EFMigrationsHistory qui conserve les migrations ont été appliquées à la base de données. Afficher les données de cette table, et vous verrez une ligne pour la première migration. (Le dernier journal dans l’exemple de sortie CLI précédent montre l’instruction INSERT qui crée cette ligne.)

Exécutez l’application pour vérifier que tout fonctionne toujours les mêmes qu’avant.

![Page d’Index les étudiants](migrations/_static/students-index.png)

<a id="pmc"></a>
## <a name="command-line-interface-cli-vs-package-manager-console-pmc"></a>Par rapport à l’interface de ligne de commande (CLI) Console du Gestionnaire de package (PMC)

L’outillage EF pour la gestion des migrations ne sont disponible à partir de commandes .NET Core CLI ou d’applets de commande PowerShell dans Visual Studio **Package Manager Console** les fenêtre (PMC). Ce didacticiel montre comment utiliser l’interface CLI, mais vous pouvez utiliser le PMC si vous préférez.

Les commandes EF pour les commandes PMC se trouvent dans le [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package. Ce package est déjà inclus dans le [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, sans que vous ayez à installer.

**Important :** ce n’est pas le même package que celui que vous installez pour l’interface CLI en modifiant le *.csproj* fichier. Le nom de celle-ci se termine dans `Tools`, contrairement au nom de package CLI qui se termine par `Tools.DotNet`.

Pour plus d’informations sur les commandes CLI, consultez [.NET Core CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet). 

Pour plus d’informations sur les commandes PMC, consultez [Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell).

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez vu comment créer et appliquer votre première migration. Dans l’étape suivante du didacticiel, vous pourrez commencer examinant rubriques plus avancées en développant le modèle de données. Tout au long du processus, vous allez créer et appliquer des migrations supplémentaires.

>[!div class="step-by-step"]
[Précédent](sort-filter-page.md)
[Suivant](complex-data-model.md)  

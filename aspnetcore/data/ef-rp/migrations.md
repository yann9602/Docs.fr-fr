---
title: Pages Razor avec EF Core - Migrations - 4 de 8
author: rick-anderson
description: "Dans ce didacticiel, vous démarrez à l’aide de la fonctionnalité de migrations EF principales pour la gestion des modifications du modèle de données dans une application ASP.NET MVC de base."
ms.author: riande
manager: wpickett
ms.date: 10/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/migrations
ms.openlocfilehash: 7b0a3f73efd1d30b903b3258bea2082792eb6e8c
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="migrations---ef-core-with-razor-pages-tutorial-4-of-8"></a>Migrations - Core EF avec le didacticiel de Pages Razor (4 sur 8)

Par [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), et [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

Dans ce didacticiel, la fonctionnalité de migrations EF principales pour la gestion des modifications du modèle de données est utilisée.

Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez le [application terminée pour cette étape](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).

Lorsqu’une application est développée, le modèle de données modifications fréquemment. Chaque fois que les modifications apportées au modèle, le modèle est désynchronisé avec la base de données. Ce didacticiel est démarré par la configuration d’Entity Framework pour créer la base de données si elle n’existe pas. Chaque fois que les données de modifications sur le modèle :

* La base de données est supprimée.
* EF crée un nouveau qui correspond au modèle.
* L’application est basée sur la base de données avec des données de test.

Cette approche pour conserver la base de données synchronisée avec le modèle de données fonctionne bien jusqu'à ce que vous déployez l’application en production. Lorsque l’application est en cours d’exécution en production, il est généralement stocker des données qui doivent être géré. L’application ne peut pas commencer par un test de base de données chaque fois qu’une modification est effectuée (par exemple, l’ajout d’une nouvelle colonne). La fonctionnalité EF Core Migrations résout ce problème en activant Core EF mettre à jour le schéma de base de données au lieu de créer une nouvelle base de données.

Au lieu de supprimer et recréer la base de données lors de la modification de modèle de données, les migrations met à jour le schéma et conserve les données existantes.

## <a name="entity-framework-core-nuget-packages-for-migrations"></a>Packages NuGet Entity Framework Core pour les migrations

Pour utiliser des migrations, utilisez le **Package Manager Console** (PMC) ou de l’interface de ligne de commande (CLI). Ces didacticiels montrent comment utiliser les commandes CLI. Plus d’informations sur la CFP est à [la fin de ce didacticiel](#pmc).

Les outils EF principaux pour l’interface de ligne de commande (CLI) sont fournis dans [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet). Pour installer ce package, ajoutez-la à la `DotNetCliToolReference` collection dans le *.csproj* de fichiers, comme indiqué. **Remarque :** ce package doit être installé en modifiant le *.csproj* fichier. Le`install-package` commande ou l’interface utilisateur graphique du Gestionnaire de package ne peut pas être utilisé pour installer ce package. Modifier la *.csproj* fichier en cliquant sur le nom du projet dans **l’Explorateur de solutions** et en sélectionnant **ContosoUniversity.csproj de modifier**.

Le balisage suivant illustre la mise à jour *.csproj* fichier avec les outils EF Core CLI mis en surbrillance :

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?highlight=12)]
  
Les numéros de version dans l’exemple précédent étaient en cours lorsque le didacticiel a été écrit. Utilisez la même version pour les outils EF Core CLI trouvés dans les autres packages.

## <a name="change-the-connection-string"></a>Modifier la chaîne de connexion

Dans le *appsettings.json* , modifiez le nom de la base de données dans la chaîne de connexion à ContosoUniversity2.

[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]

La modification du nom de la base de données dans la chaîne de connexion entraîne la première migration créer une nouvelle base de données. Une nouvelle base de données est créée, car il portant ce nom n’existe pas. Modification de la chaîne de connexion n’est pas requise pour la prise en main des migrations.

Une alternative à la modification du nom de la base de données supprime la base de données. Utilisez **l’Explorateur d’objets SQL Server** (SSOX) ou le `database drop` commande CLI :

 ```console
 dotnet ef database drop
 ```

La section suivante explique comment exécuter des commandes CLI.

## <a name="create-an-initial-migration"></a>Créer une migration initiale

Générez le projet.

Ouvrez une fenêtre de commande et accédez au dossier du projet. Le dossier du projet contient le *Startup.cs* fichier.

Dans la fenêtre de commande, entrez ce qui suit :

```console
dotnet ef migrations add InitialCreate
```

La fenêtre de commande affiche des informations similaires à ce qui suit :

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

Si la migration échoue avec le message «*accéder au fichier... ContosoUniversity.dll, car il est utilisé par un autre processus.* " s’affiche :

* Arrêter IIS Express.

   * Quittez et redémarrez Visual Studio, ou
   * Rechercher l’icône IIS Express dans la barre d’état du système Windows.
   * Cliquez sur l’icône IIS Express, puis cliquez sur **ContosoUniversity > arrêter un Site**.

Si le message d’erreur « Échec de la génération. » s’affiche, exécutez à nouveau la commande. Si vous obtenez cette erreur, laissez une note au bas de ce didacticiel.

### <a name="examine-the-up-and-down-methods"></a>Examiner le haut et vers le bas de méthodes

La commande EF Core `migrations add` généré le code pour créer la base de données à partir de. Ce code de migrations se trouve dans le *Migrations\<timestamp > _InitialCreate.cs* fichier. Le `Up` méthode de la `InitialCreate` classe crée les tables de base de données qui correspondent aux jeux d’entités de modèle données. Le `Down` méthode les supprime, comme indiqué dans l’exemple suivant :

[!code-csharp[Main](intro/samples/cu/Migrations/20171026010210_InitialCreate.cs?range=8-24,77-)]

Appels de migrations le `Up` méthode pour implémenter les modifications de modèle de données pour une migration. Lorsque vous entrez une commande pour restaurer la mise à jour, les appels de migrations le `Down` (méthode).

Le code précédent est pour la migration initiale. Ce code a été créé lorsque le `migrations add InitialCreate` commande a été exécutée. Le paramètre de nom de la migration (« InitialCreate » dans l’exemple) est utilisé pour le nom de fichier. Le nom de la migration peut être n’importe quel nom de fichier valide. Il est conseillé de choisir un mot ou une expression qui résume ce qui est effectué dans la migration. Par exemple, une migration à l’ajout d’un tableau de service peut être appelée « AddDepartmentTable ».

Si la migration initiale est créée et la base de données s’arrête :

* Le code de création de base de données est généré.
* Le code de création de base de données n’a pas besoin de s’exécuter, car la base de données correspond déjà le modèle de données. Si le code de création de la base de données est exécuté, il n’apporte aucune modification car la base de données correspond déjà le modèle de données.

Lorsque l’application est déployée sur un nouvel environnement, vous devez exécuter le code de création de base de données pour créer la base de données.

Précédemment, la chaîne de connexion a été modifiée pour utiliser un nouveau nom pour la base de données. La base de données spécifié n’existe pas, donc migrations crée la base de données.

### <a name="examine-the-data-model-snapshot"></a>Examinez l’instantané de modèle de données

Migrations crée un *instantané* du schéma de base de données en cours dans *Migrations/SchoolContextModelSnapshot.cs*:

[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]

Étant donné que le schéma de base de données actuels est représenté dans le code, EF Core ne doit pas interagir avec la base de données pour créer des migrations. Lorsque vous ajoutez une migration, EF Core détermine ce qui a changé en comparant le modèle de données pour le fichier d’instantané. EF Core interagit avec la base de données uniquement lorsqu’il a mettre à jour la base de données.

Le fichier d’instantané doit être synchronisé avec les migrations qui l’a créée. Une migration ne peut pas être supprimée en supprimant le fichier nommé  *\<timestamp > _\<migrationname > .cs*. Si ce fichier est supprimé, les migrations restantes sont synchronisées avec le fichier de capture instantanée de base de données. Pour supprimer la dernière migration ajoutée, utilisez la [supprimer des migrations d’ef dotnet](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) commande.

## <a name="remove-ensurecreated"></a>Supprimer EnsureCreated

Pour le développement anticipée, le `EnsureCreated` commande a été utilisée. Dans ce didacticiel, les migrations est utilisé. `EnsureCreated`présente les limitations suivantes :

* Ignore les migrations et crée la base de données et le schéma.
* Ne crée pas une table de migration.
* Peut *pas* être utilisé avec des migrations.
* Est conçu pour prototypage rapide ou essai où la base de données est supprimée et recréée fréquemment.

Supprimez la ligne suivante à partir de `DbInitializer`:

```csharp
context.Database.EnsureCreated();
```

## <a name="apply-the-migration-to-the-db-in-development"></a>Appliquer la migration vers la base de données en cours de développement

Dans la fenêtre de commande, entrez la commande suivante pour créer la base de données et des tables.

```console
dotnet ef database update
```

Remarque : Si le `update` commande renvoie l’erreur « Échec de la Build. » :

* Réexécutez la commande.
* Si elle échoue de nouveau, quittez Visual Studio, puis exécutez le `update` commande.
* Laisser un message au bas de la page.

La sortie de la commande est similaire à la `migrations add` sortie de commande. Dans la commande précédente, les journaux pour les commandes SQL qui configurer la base de données sont affichés. La plupart des journaux est omise dans la sortie suivante :

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

Pour réduire le niveau de détail dans les messages de journal, peut modifier les niveaux de journal dans le *appsettings. Development.JSON* fichier. Pour plus d’informations, consultez [Introduction à la journalisation](xref:fundamentals/logging/index).

Utilisez **l’Explorateur d’objets SQL Server** pour inspecter la base de données. Notez l’ajout d’un `__EFMigrationsHistory` table. Le `__EFMigrationsHistory` table effectue le suivi des migrations ont été appliquées à la base de données. Afficher les données dans le `__EFMigrationsHistory` table, il affiche une ligne pour la première migration. Le dernier journal dans l’exemple de sortie CLI précédent montre l’instruction INSERT qui crée cette ligne.

Exécutez l’application et vérifier que tout fonctionne.

## <a name="appling-migrations-in-production"></a>Migrations d’application en production

Nous vous recommandons d’applications de production doivent **pas** appeler [Database.Migrate](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) au démarrage de l’application. `Migrate`ne doit pas être appelée à partir d’une application dans la batterie de serveurs. Par exemple, si l’application a été cloud déployé avec montée en puissance parallèle (plusieurs instances de l’application sont en cours d’exécution).

Migration de base de données doit être effectuée dans le cadre du déploiement et d’une façon contrôlée. Méthodes de migration de base de données de production sont les suivantes :

* L’utilisation de migrations pour créer des scripts SQL et à l’aide de scripts SQL dans le déploiement.
* En cours d’exécution `dotnet ef database update` à partir d’un environnement contrôlé.

EF Core utilise le `__MigrationsHistory` table pour voir si les migrations doivent s’exécuter. Si la base de données est à jour, aucune migration n’est exécutée.

<a id="pmc"></a>
## <a name="command-line-interface-cli-vs-package-manager-console-pmc"></a>Par rapport à l’interface de ligne de commande (CLI) Console du Gestionnaire de package (PMC)

Le cœur d’EF pour les outils de gestion des migrations est disponible à partir de :

* Commandes CLI de base .NET.
* Les applets de commande PowerShell dans Visual Studio **Package Manager Console** les fenêtre (PMC).

Ce didacticiel montre comment utiliser l’interface CLI, certains développeurs préfèrent à l’aide de la PMC.

Les commandes de base de EF pour PMC se trouvent dans le [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package. Ce package est inclus dans le [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, sans que vous ayez à installer.

**Important :** cela n’est pas le même package que celui que vous installez pour l’interface CLI en modifiant le *.csproj* fichier. Le nom de celle-ci se termine dans `Tools`, contrairement au nom de package CLI qui se termine par `Tools.DotNet`.

Pour plus d’informations sur les commandes CLI, consultez [.NET Core CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet).

Pour plus d’informations sur les commandes PMC, consultez [Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell).

## <a name="troubleshooting"></a>Résolution des problèmes

Téléchargez le [application terminée pour cette étape](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).

L’application génère l’exception suivante :

```text
`SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Solution : exécutez`dotnet ef database update`

Si le `update` commande renvoie l’erreur « Échec de la Build. » :

* Réexécutez la commande.
* Laisser un message au bas de la page.

>[!div class="step-by-step"]
[Précédent](xref:data/ef-rp/sort-filter-page)
[Suivant](xref:data/ef-rp/complex-data-model)

---
title: "ASP.NET Core MVC avec EF Core - avancée - 10 sur 10"
author: tdykstra
description: "Ce didacticiel présente des rubriques qui sont utiles à connaître lorsque vous dépassent les principes de base du développement d’applications web ASP.NET qui utilisent Entity Framework Core."
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/advanced
ms.openlocfilehash: b1d1cb8710595acd72bd5a0786bf1b1fed8b1d79
ms.sourcegitcommit: 09b342b45e7372ba9ebf17f35eee331e5a08fb26
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2018
---
# <a name="advanced-topics---ef-core-with-aspnet-core-mvc-tutorial-10-of-10"></a>Rubriques avancées - Core EF avec le didacticiel d’ASP.NET MVC de base (10 10)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).

Dans le didacticiel précédent, vous implémenté l’héritage table par hiérarchie. Ce didacticiel présente des rubriques qui sont utiles à connaître lorsque vous dépassent les principes de base du développement d’applications web ASP.NET Core qui utilisent Entity Framework Core.

## <a name="raw-sql-queries"></a>Requêtes SQL brut

Un des avantages de l’utilisation d’Entity Framework est qu’elle évite de lier votre code trop près d’une méthode particulière du stockage des données. Cela en générant des requêtes et commandes SQL pour vous, ce qui vous évite de les écrire. Mais il existe des scénarios exceptionnelles lorsque vous avez besoin exécuter des requêtes SQL spécifiques que vous avez créé manuellement. Pour ces scénarios, l’API de premier Code Entity Framework comprend les méthodes qui vous permettent de transmettre des commandes SQL directement à la base de données. Vous disposez des options suivantes dans EF Core 1.0 :

* Utilisez la `DbSet.FromSql` méthode pour les requêtes qui retournent des types d’entités. Les objets retournés doivent être du type attendu par le `DbSet` objet et elles sont suivies automatiquement par le contexte de base de données, sauf si vous [désactiver le suivi des](crud.md#no-tracking-queries).

* Utilisez le `Database.ExecuteSqlCommand` pour les commandes de requête non.

Si vous avez besoin exécuter une requête qui retourne des types qui ne sont pas des entités, vous pouvez utiliser ADO.NET avec la connexion de base de données fournie par EF. Les données renvoyées n’est pas suivies par le contexte de la base de données, même si vous utilisez cette méthode pour récupérer des types d’entités.

Comme étant toujours true lorsque vous exécutez des commandes SQL dans une application web, vous devez prendre des précautions pour protéger votre site contre les attaques par injection SQL. Une manière de procéder consiste à utiliser des requêtes paramétrables pour vous assurer que les chaînes soumis par une page web ne peut pas être interprétées comme des commandes SQL. Dans ce didacticiel, vous utiliserez des requêtes paramétrables lors de l’intégration de l’entrée d’utilisateur dans une requête.

## <a name="call-a-query-that-returns-entities"></a>Appeler une requête qui retourne des entités

Le `DbSet<TEntity>` classe fournit une méthode que vous pouvez utiliser pour exécuter une requête qui retourne une entité de type `TEntity`. Pour voir comment cela fonctionne vous allez modifier le code dans la `Details` méthode du contrôleur de service.

Dans *DepartmentsController.cs*, dans le `Details` (méthode), remplacez le code qui Récupère un service avec un `FromSql` appel de méthode, comme indiqué dans le code en surbrillance suivant :

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10,13)]

Pour vérifier que le nouveau code fonctionne correctement, sélectionnez le **départements** onglet, puis **détails** pour un des services.

![Informations relatives au service](advanced/_static/department-details.png)

## <a name="call-a-query-that-returns-other-types"></a>Appeler une requête qui retourne les autres types

Précédemment, vous avez créé une grille des statistiques de student pour la page à propos montrant le nombre d’élèves pour chaque date d’inscription. Vous avez obtenu les données du jeu d’entités étudiants (`_context.Students`) et utilisé LINQ pour projeter les résultats dans une liste de `EnrollmentDateGroup` afficher les objets de modèle. Supposons que vous souhaitez écrire le code SQL elle-même plutôt qu’à l’aide de LINQ. Pour que vous avez besoin exécuter une requête SQL, qui retourne une valeur autre que les objets d’entité. Dans EF Core 1.0, une faire consiste à écrire du code ADO.NET et d’obtenir la connexion de base de données à partir de EF.

Dans *HomeController.cs*, remplacez le `About` méthode avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Ajouter un à l’aide de déclaration :

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Exécutez l’application et accédez à la page à propos de. Il affiche les mêmes données qu’auparavant.

![Sur la page](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Appeler une requête de mise à jour

Supposons que les administrateurs de Contoso University effectuer des modifications globales dans la base de données, telles que la modification du nombre de crédits pour chaque cours. Si l’université comporte un grand nombre des cours, il est inefficace pour les extraire sous la forme d’entités et les modifier individuellement. Dans cette section, vous allez implémenter une page web qui permet à l’utilisateur spécifier un facteur permettant de modifier le nombre de crédits pour tous les cours, et vous devez apporter la modification en exécutant une instruction SQL UPDATE. La page web doit ressembler à l’illustration suivante :

![Page des crédits du cours de mise à jour](advanced/_static/update-credits.png)

Dans *CoursesContoller.cs*, ajouter des méthodes de UpdateCourseCredits pour HttpGet et HttpPost :

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Lorsque le contrôleur traite une demande de HttpGet, rien n’est retourné dans `ViewData["RowsAffected"]`, et la vue affiche une zone de texte vide et un bouton d’envoi, comme indiqué dans l’illustration précédente.

Lorsque le **mise à jour** bouton, la méthode HttpPost est appelée et multiplicateur a la valeur entrée dans la zone de texte. Le code exécute ensuite l’instruction SQL qui met à jour des cours et retourne le nombre de lignes affectées à la vue dans `ViewData`. Lorsque la vue obtient un `RowsAffected` valeur, elle affiche le nombre de lignes mises à jour.

Dans **l’Explorateur de solutions**, avec le bouton droit le *vues/cours* dossier, puis cliquez sur **Ajouter > nouvel élément**.

Dans le **ajouter un nouvel élément** boîte de dialogue, cliquez sur **ASP.NET** sous **installé** dans le volet gauche, cliquez sur **Page de vue MVC**et le nom de la nouvelle vue  *UpdateCourseCredits.cshtml*.

Dans *Views/Courses/UpdateCourseCredits.cshtml*, remplacez le code de modèle par le code suivant :

[!code-html[Main](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

Exécuter la `UpdateCourseCredits` méthode en sélectionnant le **cours** onglet, puis en ajoutant « / UpdateCourseCredits » à la fin de l’URL dans la barre d’adresses du navigateur (par exemple : `http://localhost:5813/Courses/UpdateCourseCredits`). Entrez un nombre dans la zone de texte :

![Page des crédits du cours de mise à jour](advanced/_static/update-credits.png)

Cliquez sur **Mettre à jour**. Vous voyez le nombre de lignes affectées :

![Mise à jour cours crédits page lignes affectées](advanced/_static/update-credits-rows-affected.png)

Cliquez sur **retour à la liste** pour afficher la liste des cours avec la modification du numéro de crédits.

Notez que le code de production assureriez qui met à jour toujours des données valides. Le code simplifié indiqué ici peut multipliez le nombre de crédits suffisamment de numéros supérieurs à 5. (Le `Credits` propriété a un `[Range(0, 5)]` attribut.) La requête de mise à jour fonctionne, mais les données non valides peuvent provoquer des résultats inattendus dans d’autres parties du système qui supposent que le nombre de crédits est inférieur ou égal à 5.

Pour plus d’informations sur les requêtes SQL brutes, consultez [des requêtes SQL brutes](https://docs.microsoft.com/ef/core/querying/raw-sql).

## <a name="examine-sql-sent-to-the-database"></a>Examinez SQL envoyée à la base de données

Il est parfois utile de pouvoir voir les requêtes SQL réelles qui sont envoyées à la base de données. Fonctionnalité de journalisation intégrés pour ASP.NET Core est utilisée automatiquement par EF Core d’écrire des journaux qui contiennent le code SQL pour les requêtes et les mises à jour. Dans cette section, vous verrez des exemples de journalisation SQL.

Ouvrez *StudentsController.cs* et dans le `Details` méthode définie un point d’arrêt sur la `if (student == null)` instruction.

Exécuter l’application en mode débogage et accédez à la page des détails pour un étudiant.

Accédez à la **sortie** de sortie de fenêtre affichant le débogage et que la requête :

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

Vous remarquerez que quelque chose ici qui peut vous surprendre : l’instruction SQL sélectionne des lignes jusqu'à 2 (`TOP(2)`) à partir de la table Person. Le `SingleOrDefaultAsync` méthode ne résout pas 1 ligne sur le serveur. Voici pourquoi :

* Si la requête retourne plusieurs lignes, la méthode retourne la valeur null.
* Pour déterminer si la requête retourne plusieurs lignes, EF a besoin de vérifier si elle retourne au moins 2.

Notez que vous ne devez utiliser le mode débogage et s’arrêter à un point d’arrêt pour obtenir la sortie de journalisation dans le **sortie** fenêtre. Il est simplement un moyen pratique pour arrêter la journalisation au niveau où que vous souhaitez consulter la sortie. Si vous ne le faire, l’enregistrement se poursuit et vous devez revenir en arrière pour rechercher les parties qui que vous intéresse.

## <a name="repository-and-unit-of-work-patterns"></a>Espace de stockage et une unité de travail modèles

De nombreux développeurs écrire du code pour implémenter le référentiel et une unité de travail modèles comme un wrapper autour du code qui fonctionne avec Entity Framework. Ces modèles sont destinés à créer une couche d’abstraction entre la couche d’accès aux données et la couche de logique métier d’une application. Implémentation de ces modèles peut permettre d’isoler votre application des modifications dans le magasin de données et peuvent faciliter le test unitaire automatisé ou développement piloté par test (TDD). Toutefois, l’écriture de code supplémentaire pour implémenter ces modèles n’est pas toujours le meilleur choix pour les applications qui utilisent EF, pour plusieurs raisons :

* La classe de contexte EF lui-même isole votre code à partir du code spécifique au magasin de données.

* La classe de contexte EF peut agir comme une classe de l’unité de travail de base de données mises à jour de procéder à l’aide de EF.

* EF inclut des fonctionnalités pour l’implémentation de TDD sans écrire de code de référentiel.

Pour plus d’informations sur la façon d’implémenter le référentiel et une unité de travail des modèles, consultez [la version d’Entity Framework 5 de cette série de didacticiels](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).

Entity Framework Core implémente un fournisseur de base de données en mémoire qui peut être utilisé pour le test. Pour plus d’informations, consultez [test avec InMemory](https://docs.microsoft.com/ef/core/miscellaneous/testing/in-memory).

## <a name="automatic-change-detection"></a>Détection des modifications automatique

Entity Framework détermine la manière dont une entité a changé (et par conséquent les mises à jour doivent être envoyées à la base de données) en comparant les valeurs en cours d’une entité avec les valeurs d’origine. Les valeurs d’origine sont stockées lorsque l’entité est interrogée ou attachée. Certaines des méthodes qui provoquent la détection des modifications automatique sont les suivants :

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Entries

Si vous effectuez le suivi d’un grand nombre d’entités et que vous appelez l’une de ces méthodes plusieurs fois dans une boucle, vous pouvez obtenir des améliorations significatives des performances en activant temporairement désactiver la détection de modification automatique à l’aide du `ChangeTracker.AutoDetectChangesEnabled` propriété. Exemple :

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="entity-framework-core-source-code-and-development-plans"></a>Entity Framework Core source code et le développement des plans

La source de l’Entity Framework Core est à [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore). Le référentiel EF Core contient les builds nocturnes, suivi des problèmes, spécifications des fonctionnalités, notes, de la réunion de conception et [la feuille de route pour le développement futur](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap). Vous pouvez de fichiers ou rechercher des bogues et contribuer.

Bien que le code source est ouvert, Entity Framework Core est entièrement pris en charge comme un produit Microsoft. L’équipe Microsoft Entity Framework conserve le contrôle sur lequel les contributions sont acceptées et teste toutes les modifications du code pour garantir la qualité de chaque version.

## <a name="reverse-engineer-from-existing-database"></a>L’ingénierie à rebours à partir de la base de données existante

Pour rétroconcevoir un modèle de données, y compris les classes d’entité à partir de la base de données existante, utilisez la [scaffold-dbcontext](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) commande. Consultez le [didacticiel de mise en route](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db).

<a id="dynamic-linq"></a>
## <a name="use-dynamic-linq-to-simplify-sort-selection-code"></a>Permet de simplifier le code de sélection de tri LINQ dynamique

Le [troisième didacticiel de cette série](sort-filter-page.md) montre comment écrire du code LINQ en codage en dur des noms de colonnes dans un `switch` instruction. Avec deux colonnes sélectionnables, cela fonctionne correctement, mais si vous avez de nombreuses colonnes le code pourrait verbose. Pour résoudre ce problème, vous pouvez utiliser la `EF.Property` méthode pour spécifier le nom de la propriété sous forme de chaîne. Pour tester cette approche, vous devez remplacer le `Index` méthode dans le `StudentsController` par le code suivant.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="next-steps"></a>Étapes suivantes

Cette étape termine cette série de didacticiels sur l’utilisation de l’Entity Framework Core dans une application ASP.NET MVC.

Pour plus d’informations sur EF Core, consultez la [documentation d’Entity Framework Core](https://docs.microsoft.com/ef/core). Un livre est également disponible : [Entity Framework Core en Action](https://www.manning.com/books/entity-framework-core-in-action).

Pour plus d’informations sur la façon de déployer une application web, consultez [hôte et déployer](xref:host-and-deploy/index).

Pour plus d’informations sur les autres rubriques relatives à ASP.NET MVC de base, telles que l’authentification et l’autorisation, consultez le [documentation d’ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

## <a name="acknowledgments"></a>Accusés de réception

Tom Dykstra et Rick Anderson (twitter @RickAndMSFT) a écrit ce didacticiel. Rowan Miller, Diego Vega et autres membres de l’équipe d’Entity Framework assistée avec les révisions du code et a aidé à déboguer les problèmes posés par pendant que nous avons écrire du code pour les didacticiels.

## <a name="common-errors"></a>Erreurs courantes  

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity.dll utilisé par un autre processus

Message d’erreur :

> Impossible d’ouvrir '... bin\Debug\netcoreapp1.0\ContosoUniversity.dll' en écriture--' le processus ne peut pas accéder au fichier '... \bin\Debug\netcoreapp1.0\ContosoUniversity.dll', car il est utilisé par un autre processus.

Solution :

Arrêter le site dans IIS Express. Accédez à la barre des tâches Windows, recherchez IIS Express et cliquez sur son icône, sélectionnez le site de l’Université de Contoso, puis cliquez sur **arrêter un Site**.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Migration structurée sans code de haut et bas de méthodes

Cause possible :

Les commandes CLI d’EF n’automatiquement fermer et enregistrer des fichiers de code. Si vous avez apporté des modifications lorsque vous exécutez le `migrations add` EF ne trouve pas les modifications de la commande.

Solution :

Exécutez le `migrations remove` de commande, enregistrez vos modifications du code et réexécuter le `migrations add` commande.

### <a name="errors-while-running-database-update"></a>Erreurs lors de la mise à jour de base de données en cours d’exécution

Il est possible d’obtenir d’autres erreurs lorsque des modifications de schéma dans une base de données qui comporte déjà des données. Si vous obtenez des erreurs de migration que vous ne pouvez pas résoudre, vous pouvez modifier le nom de la base de données dans la chaîne de connexion ou supprimer la base de données. Avec une base de données, il n’existe pas de données à migrer, et la commande de mise à jour de la base de données est beaucoup plus susceptible de se terminer sans erreur.

L’approche la plus simple consiste à renommer la base de données *appsettings.json*. La prochaine fois que vous exécutez `database update`, une base de données sera créée.

Pour supprimer une base de données SSOX, avec le bouton droit de la base de données, cliquez sur **supprimer**, puis, dans le **supprimer la base de données** boîte de dialogue Sélectionnez **fermer les connexions existantes** sur  **OK**.

Pour supprimer une base de données à l’aide de l’interface CLI, exécutez le `database drop` commande CLI :

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>Instance SQL Server recherche d’erreur

Message d’erreur :

> Une erreur liée au réseau ou d’instance spécifique s’est produite lors de l’établissement d’une connexion à SQL Server. Le serveur est introuvable ou n’est pas accessible. Vérifiez que le nom de l’instance est correct et que SQL Server est configuré pour autoriser les connexions distantes. (fournisseur : Interfaces réseau SQL, erreur : 26 - erreur serveur/de l’Instance spécifiée de localisation)

Solution :

Vérifiez la chaîne de connexion. Si vous avez supprimé manuellement des fichiers de la base de données, modifiez le nom de la base de données dans la chaîne de construction pour recommencer avec une nouvelle base de données.

>[!div class="step-by-step"]
[Précédent](inheritance.md)

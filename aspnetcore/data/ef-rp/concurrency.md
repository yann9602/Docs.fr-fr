---
title: "Pages Razor avec EF Core - d’accès concurrentiel - 8 de 8"
author: rick-anderson
description: "Ce didacticiel montre comment gérer les conflits lorsque plusieurs utilisateurs mettre à jour la même entité en même temps."
keywords: "Concurrence d’accès ASP.NET Core, Entity Framework Core,"
ms.author: riande
manager: wpickett
ms.date: 11/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/concurrency
ms.openlocfilehash: 841c638b2cacaab7970f2b173fee488972957b63
ms.sourcegitcommit: 2d23ea501e0213bbacf65298acf1c8bd17209540
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
en-us /

# <a name="handling-concurrency-conflicts---ef-core-with-razor-pages-8-of-8"></a>La gestion des conflits d’accès concurrentiel - Core EF avec les Pages Razor (8 sur 8)

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), et [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

Ce didacticiel montre comment gérer les conflits lorsque plusieurs utilisateurs mettre à jour une entité simultanément (simultanément). Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez le [application terminée pour cette étape](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part8).

## <a name="concurrency-conflicts"></a>Conflits d’accès concurrentiel

Un conflit de concurrence se produit lorsque :

* Un utilisateur accède à la page de modification pour une entité.
* Un autre utilisateur met à jour la même entité avant la première modification utilisateur est écrit dans la base de données.

Si la détection d’accès concurrentiel n’est pas activée, lorsque des mises à jour simultanées se produisent :

* La dernière mise à jour est prioritaire. Autrement dit, les dernières valeurs de mise à jour sont enregistrés dans la base de données.
* La première mises à jour en cours sont perdues.

### <a name="optimistic-concurrency"></a>Accès concurrentiel optimiste

L’accès concurrentiel optimiste permet de conflits d’accès concurrentiel se produire et puis réagit en conséquence s’ils ne. Par exemple, Jane visite de la page de modification de service et modifie l’allocation de réserve pour le service en anglais à partir de $350,000.00 à 0,00 $.

![La modification de budget à 0](concurrency/_static/change-budget.png)

Avant de Jane clique sur **enregistrer**, John consulte la même page et modifie le champ Date de début à partir de 1/9/2007, à 1/9/2013.

![La modification de la date de début pour 2013](concurrency/_static/change-date.png)

Jane clique sur **enregistrer** première et voit sa change lorsque le navigateur affiche la page d’Index.

![Allocation de réserve modifiée à zéro](concurrency/_static/budget-zero.png)

John clique sur **enregistrer** sur une page d’édition qui affiche toujours un budget de $350,000.00. Que se passe-t-il ensuite est déterminé par la façon dont vous gérez les conflits d’accès concurrentiel.

L’accès concurrentiel optimiste inclut les options suivantes :

* Vous pouvez effectuer le suivi des dont un utilisateur a modifié la propriété et mettre à jour uniquement les colonnes correspondantes dans la base de données.

 Dans le scénario, aucune donnée n’a été perdue. Des propriétés différentes ont été mis à jour par les deux utilisateurs. La prochaine fois qu’un utilisateur parcourt le service en anglais, il voit les modifications à la fois de John et Jane. Cette méthode de mise à jour peut réduire le nombre de conflits qui peuvent entraîner une perte de données. Cette approche : * ne peut pas éviter une perte de données si des modifications concurrentes sont apportées à la même propriété.
        * N’est généralement pas pratique dans une application web. Elle nécessite la gestion de l’état significatif pour effectuer le suivi d’extraites de toutes les valeurs et les nouvelles valeurs. Maintenance de grandes quantités d’état peut affecter les performances de l’application.
        * Peut augmenter la complexité des applications par rapport à la détection de concurrence sur une entité.

* Vous pouvez laisser les modifications de John écrase les modifications de Jeanne.

 La prochaine fois que quelqu'un accède le service en anglais, il voit le 1/9/2013 et la valeur de $350,000.00 extraite. Cette approche est appelée un *Client Wins* ou *dernier dans Wins* scénario. (Toutes les valeurs à partir du client sont prioritaires sur les nouveautés dans le magasin de données). Si vous ne le faites pas de codage pour la gestion d’accès concurrentiel, priorité au Client se produit automatiquement.

* Vous pouvez empêcher la modification de Jean à partir de la mise à jour dans la base de données. En règle générale, l’application serait : * affiche un message d’erreur.
        * Afficher l’état actuel des données.
        * Autoriser l’utilisateur à réappliquer les modifications.

 Cela s’appelle un *magasin Wins* scénario. (Les valeurs du magasin de données sont prioritaires sur les valeurs soumis par le client). Implémenter le scénario de magasin Wins dans ce didacticiel. Cette méthode garantit qu’aucune modification n’est remplacées sans que l’utilisateur est averti.

## <a name="handling-concurrency"></a>Gestion d’accès concurrentiel 

Quand une propriété est configurée comme un [jeton d’accès concurrentiel](https://docs.microsoft.com/en-us/ef/core/modeling/concurrency):

* EF Core vérifie que propriété n’a pas été modifiée après que qu’elle a été extraite. La vérification se produit lorsque [SaveChanges](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) ou [SaveChangesAsync](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) est appelée.
* Si la propriété a été modifiée après qu’elle a été extraite, un [DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) est levée. 

Le modèle de données et de la base de données doit être configuré pour prendre en charge de lever `DbUpdateConcurrencyException`.

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Détection des conflits d’accès concurrentiel sur une propriété

Conflits d’accès concurrentiel peuvent être détectées au niveau de la propriété avec le [ConcurrencyCheck](https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribut. L’attribut peut être appliqué à plusieurs propriétés sur le modèle. Pour plus d’informations, consultez [Annotations de données-ConcurrencyCheck](https://docs.microsoft.com/en-us/ef/core/modeling/concurrency#data-annotations).

Le `[ConcurrencyCheck]` attribut n’est pas utilisé dans ce didacticiel.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Détection des conflits d’accès concurrentiel sur une ligne

Pour détecter les conflits d’accès concurrentiel, une [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) le suivi de colonne est ajouté au modèle.  `rowversion` :

* SQL Server est spécifique. Autres bases de données peut ne pas fournissent une fonctionnalité similaire.
* Permet de déterminer qu’une entité n’a pas été modifiée depuis qu’elle a été extraite à partir de la base de données. 

La base de données génère une liste séquentielle `rowversion` nombre qui est incrémentée chaque fois que la ligne est mise à jour. Dans un `Update` ou `Delete` commande, le `Where` clause inclut la valeur extraite de `rowversion`. Si la ligne mise à jour a changé :

 * `rowversion`ne correspond pas à la valeur extraite.
 * Le `Update` ou `Delete` commandes ne trouve pas une ligne, car le `Where` clause inclut l’extraction `rowversion`.
 * A `DbUpdateConcurrencyException` est levée.

Dans EF Core, lorsque aucune ligne n’a été mis à jour par une `Update` ou `Delete` de commande, une exception d’accès concurrentiel est levée.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Ajouter une propriété de suivi à l’entité du service

Dans *Models/Department.cs*, ajouter une propriété de suivi nommée RowVersion :

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Le [Timestamp](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribut spécifie que cette colonne est incluse dans le `Where` clause de `Update` et `Delete` commandes. L’attribut est appelé `Timestamp` , car les versions précédentes de SQL Server utilisaient SQL `timestamp` type de données avant l’instruction SQL `rowversion` type remplacé.

L’API fluent peut également spécifier la propriété de suivi :

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Le code suivant montre une partie de T-SQL générée par EF Core lorsque le nom du service est mis à jour :

[!code-sql[](intro/samples/sql.txt?highlight=2-3)]

L’exemple précédent en surbrillance montre le code le `WHERE` clause contenant `RowVersion`. Si la base de données `RowVersion` n’est pas égal le `RowVersion` paramètre (`@p2`), aucune ligne n’est mis à jour.

Le code en surbrillance suivant montre le code T-SQL qui vérifie qu’une seule ligne a été mis à jour :

[!code-sql[](intro/samples/sql.txt?highlight=4-6)]

[@@ROWCOUNT ](https://docs.microsoft.com/en-us/sql/t-sql/functions/rowcount-transact-sql) retourne le nombre de lignes affectées par la dernière instruction. Absence lignes sont mises à jour, EF Core lève une `DbUpdateConcurrencyException`.

Vous pouvez voir que le cœur de EF T-SQL génère dans la fenêtre Sortie de Visual Studio.

### <a name="update-the-db"></a>Mise à jour de la base de données

Ajout de la `RowVersion` propriété modifie le modèle de base de données, ce qui requiert une migration.

Générez le projet. Dans une fenêtre de commande, entrez ce qui suit :

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

Les commandes précédentes :

* Ajoute le *Migrations / {temps stamp}_RowVersion.cs* fichier de migration.
* Les mises à jour le *Migrations/SchoolContextModelSnapshot.cs* fichier. La mise à jour ajoute le code en surbrillance suivant à la `BuildModel` méthode :

[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot2.cs?name=snippet&highlight=14-16)]

* Exécute des migrations pour mettre à jour la base de données.

<a name="scaffold"></a>
## <a name="scaffold-the-departments-model"></a>Structure du modèle de services

* Quittez Visual Studio.
* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).
* Exécutez la commande suivante :

 ```console
dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
 ```

Les structures de commande précédent la `Department` modèle. Ouvrez le projet dans Visual Studio.

Générez le projet. La build génère des erreurs comme suit :

`1>Pages/Departments/Index.cshtml.cs(26,37,26,43): error CS1061: 'SchoolContext' does not
 contain a definition for 'Department' and no extension method 'Department' accepting a first
 argument of type 'SchoolContext' could be found (are you missing a using directive or
 an assembly reference?)`

 Modifier globalement `_context.Department` à `_context.Departments` (autrement dit, ajoutez un « s » pour `Department`). 7 occurrences sont trouvées et mis à jour.

### <a name="update-the-departments-index-page"></a>Mise à jour de la page d’Index de services

Le moteur de génération de modèles automatique créé un `RowVersion` colonne pour la page d’Index, mais ce champ ne doit pas être affichée. Dans ce didacticiel, le dernier octet de la `RowVersion` s’affiche pour aider à comprendre la concurrence. Le dernier octet n’est pas garanti pour être unique. Dans une application réelle n’aurait aucun affichage `RowVersion` ou le dernier octet de `RowVersion`.

Mise à jour de la page d’Index :

* Avec les services de remplacer l’Index.
* Remplacez le balisage contenant `RowVersion` avec le dernier octet de `RowVersion`.
* Remplacez FirstMidName par nom complet.

Le balisage suivant montre la page de mise à jour :

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Mettre à jour le modèle de page de modification

Mise à jour *pages\departments\edit.cshtml.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Pour détecter un problème d’accès concurrentiel, le [OriginalValue](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) est mis à jour avec la `rowVersion` la valeur de l’entité qu’elle a été extraite. EF Core génère une commande SQL UPDATE avec une clause WHERE contenant la version d’origine `RowVersion` valeur. Si aucune ligne n’est affectées par la commande de mise à jour (aucune ligne n’ont d’origine `RowVersion` valeur), un `DbUpdateConcurrencyException` exception est levée.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-)]

Dans le code précédent, `Department.RowVersion` est la valeur lorsque l’entité a été extraite. `OriginalValue`est la valeur de la base de données lorsque `FirstOrDefaultAsync` a été appelée dans cette méthode.

Le code suivant obtient les valeurs de client (les valeurs publiées dans cette méthode) et les valeurs de la base de données :

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Le code suivant ajoute un message d’erreur personnalisé pour chaque colonne dont la base de données les valeurs différentes à partir de ce qui a été validé dans `OnPostAsync`:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Les éléments suivants mis en surbrillance le code définit la `RowVersion` valeur à la nouvelle valeur récupérée à partir de la base de données. La prochaine fois que l’utilisateur clique sur **enregistrer**, seules les erreurs d’accès concurrentiel qui se produisent dans la mesure où le dernier affichage de la page de modification est interceptée.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

Le `ModelState.Remove` instruction est requise car `ModelState` a l’ancien `RowVersion` valeur. Dans la Page Razor, le `ModelState` valeur pour un champ est prioritaire sur les valeurs de propriété de modèle lorsque les deux sont présents.

## <a name="update-the-edit-page"></a>Mise à jour de la page de modification

Mise à jour *Pages/Departments/Edit.cshtml* par le balisage suivant :

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Le balisage précédent :

* Les mises à jour le `page` de `@page` à `@page "{id:int}"`.
* Ajoute une version de ligne masquée. `RowVersion`doit être ajouté pour la publication lie la valeur.
* Affiche le dernier octet de `RowVersion` à des fins de débogage.
* Remplace `ViewData` avec le fortement typé `InstructorNameSL`.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Conflits d’accès concurrentiel de test avec la page de modification

Ouvrez deux instances de navigateurs de modification sur le service en anglais :

* Exécutez l’application et sélectionnez Services.
* Avec le bouton droit le **modifier** lien hypertexte pour le service en anglais, puis sélectionnez **ouvrir dans un nouvel onglet**.
* Dans le premier onglet, cliquez sur le **modifier** lien hypertexte pour le service en anglais.

Les onglets de deux navigateur affichent les mêmes informations.

Modifier le nom dans le premier onglet de navigateur, puis cliquez sur **enregistrer**.

![Édition de service page 1 après modification](concurrency/_static/edit-after-change-1.png)

Le navigateur affiche la page d’Index de la valeur modifiée et d’un indicateur de mise à jour rowVersion. Notez l’indicateur rowVersion mis à jour, il est affiché sur la deuxième publication dans l’autre onglet.

Modifier un champ différent dans le deuxième onglet du navigateur.

![Modification du service page 2 après la modification](concurrency/_static/edit-after-change-2.png)

Cliquez sur **Enregistrer**. Vous consultez les messages d’erreur pour tous les champs ne correspondent pas aux valeurs de la base de données :

![Message d’erreur service modifier page](concurrency/_static/edit-error.png)

Cette fenêtre de navigateur ne souhaitez pas modifier le champ nom. Copiez et collez la valeur actuelle (langues) dans le champ nom. Appuyez sur TAB. La validation côté client supprime le message d’erreur.

![Message d’erreur service modifier page](concurrency/_static/cv.png)

Cliquez sur **enregistrer** à nouveau. La valeur que vous avez entré dans le deuxième onglet navigateur est enregistrée. Vous voyez les valeurs enregistrées dans la page d’Index.

## <a name="update-the-delete-page"></a>Mise à jour de la page de suppression

Mettre à jour le modèle de page de suppression par le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

La page de suppression détecte les conflits d’accès concurrentiel lorsque l’entité a été modifiée après que qu’elle a été extraite. `Department.RowVersion`est la version de ligne lorsque l’entité a été extraite. Lorsque EF Core crée la commande SQL DELETE, il inclut une clause WHERE avec `RowVersion`. Si les résultats de la commande SQL DELETE dans des lignes nulles affectés :

* Le `RowVersion` dans le SQL DELETE commande ne correspond pas à `RowVersion` dans la base de données.
* Une exception DbUpdateConcurrencyException est levée.
* `OnGetAsync`est appelée avec le `concurrencyError`.

### <a name="update-the-delete-page"></a>Mise à jour de la page de suppression

Mise à jour *Pages/Departments/Delete.cshtml* avec le code suivant :

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,36,51)]


Le balisage précédent apporte les modifications suivantes :

* Les mises à jour le `page` de `@page` à `@page "{id:int}"`.
* Ajoute un message d’erreur.
* Remplace FirstMidName FullName dans le **administrateur** champ.
* Modifications `RowVersion` pour afficher le dernier octet.
* Ajoute une version de ligne masquée. `RowVersion`doit être ajouté pour la publication lie la valeur.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Conflits d’accès concurrentiel de test avec la page de suppression

Créer un service de test.

Ouvrez deux instances de navigateurs de suppression sur le service de test :

* Exécutez l’application et sélectionnez Services.
* Avec le bouton droit le **supprimer** lien hypertexte pour le service de test, puis sélectionnez **ouvrir dans un nouvel onglet**.
* Cliquez sur le **modifier** lien hypertexte pour le service de test.

Les onglets de deux navigateur affichent les mêmes informations.

Modifier l’allocation de réserve dans le premier onglet de navigateur, cliquez sur **enregistrer**.

Le navigateur affiche la page d’Index de la valeur modifiée et d’un indicateur de mise à jour rowVersion. Notez l’indicateur rowVersion mis à jour, il est affiché sur la deuxième publication dans l’autre onglet.

Supprimer le service de test à partir du deuxième onglet. Une erreur d’accès concurrentiel est s’affichent avec les valeurs actuelles à partir de la base de données. En cliquant sur **supprimer** supprime l’entité, sauf si `RowVersion` a été updated.department a été supprimé.

Consultez [héritage](xref:data/ef-mvc/inheritance) comment hériter d’un modèle de données.

### <a name="additional-resources"></a>Ressources supplémentaires

* [Jetons d’accès concurrentiel dans EF Core](https://docs.microsoft.com/en-us/ef/core/modeling/concurrency)
* [Gestion d’accès concurrentiel dans EF Core](https://docs.microsoft.com/en-us/ef/core/saving/concurrency)

>[!div class="step-by-step"]
[Précédent](xref:data/ef-rp/update-related-data)

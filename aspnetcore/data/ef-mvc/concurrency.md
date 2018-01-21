---
title: "Cœur de ASP.NET MVC avec EF Core - d’accès concurrentiel - 8 de 10"
author: tdykstra
description: "Ce didacticiel montre comment gérer les conflits lorsque plusieurs utilisateurs mettre à jour la même entité en même temps."
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 69ffafc7f92cda75c001fe1098275766063113fb
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="handling-concurrency-conflicts---ef-core-with-aspnet-core-mvc-tutorial-8-of-10"></a>La gestion des conflits d’accès concurrentiel - Core EF avec le didacticiel d’ASP.NET MVC de base (8 sur 10)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).

Dans les didacticiels antérieures, vous avez appris comment mettre à jour des données. Ce didacticiel montre comment gérer les conflits lorsque plusieurs utilisateurs mettre à jour la même entité en même temps.

Vous allez créer des pages web qui utilisent l’entité du service et de gérer les erreurs d’accès concurrentiel. Les illustrations suivantes montrent les pages modifier et supprimer, y compris des messages qui sont affichent si un conflit de concurrence se produit.

![Page de modification du service](concurrency/_static/edit-error.png)

![Page de suppression de service](concurrency/_static/delete-error.png)

## <a name="concurrency-conflicts"></a>Conflits d’accès concurrentiel

Un conflit de concurrence se produit lorsqu’un utilisateur affiche les données d’une entité afin de la pour modifier, puis un autre utilisateur met à jour les données de la même entité avant la première modification utilisateur est écrit dans la base de données. Si vous n’activez pas la détection de ces conflits, la personne qui met à jour la base de données remplace dernier les modifications de l’autre utilisateur. Dans de nombreuses applications, ce risque est acceptable : s’il existe quelques utilisateurs ou les mises à jour, ou si n’est pas réellement critique si des modifications sont remplacées, le coût de la programmation d’accès concurrentiel peut-être dépasser l’avantage. Dans ce cas, vous n’êtes pas obligé de configurer l’application pour gérer les conflits d’accès concurrentiel.

### <a name="pessimistic-concurrency-locking"></a>L’accès simultané pessimiste (verrouillage)

Si votre application doit-elle éviter la perte accidentelle de données dans les scénarios d’accès concurrentiel, une manière de procéder consiste à utiliser des verrous de base de données. Il s’agit d’accès concurrentiel pessimiste. Par exemple, avant de lire une ligne à partir d’une base de données, vous demandez un verrou de lecture seule ou pour l’accès de mise à jour. Si vous verrouillez une ligne pour l’accès de mise à jour, aucun autre utilisateur n’est autorisés pour le verrouillage de la ligne pour en lecture seule ou mettre à jour de l’accès, car ils obtenez une copie des données sont en cours de modification. Si vous verrouillez une ligne pour l’accès en lecture seule, d’autres peuvent également le verrouiller pour l’accès en lecture seule, mais pas pour la mise à jour.

Gestion des verrous présente des inconvénients. Il peut être complexe au programme. Il nécessite des ressources de gestion de base de données importantes, et cela peut provoquer des problèmes de performances en tant que le nombre d’utilisateurs d’une application augmente. Pour ces raisons, pas tous les systèmes de gestion de base de données prend en charge l’accès simultané pessimiste. Entity Framework Core ne fournit aucune prise en charge intégrée pour elle et ce didacticiel ne vous montre comment l’implémenter.

### <a name="optimistic-concurrency"></a>Accès concurrentiel optimiste

L’alternative à l’accès concurrentiel pessimiste est l’accès concurrentiel optimiste. L’accès concurrentiel optimiste signifie autoriser les conflits d’accès concurrentiel se produire et puis réagir correctement dans le cas. Par exemple, Jane visite de la page Modifier le service et modifie la quantité d’allocation de réserve pour le service en anglais à partir de $350,000.00 à 0,00 $.

![La modification de budget à 0](concurrency/_static/change-budget.png)

Avant de Jane clique sur **enregistrer**, John consulte la même page et modifie le champ Date de début à partir de 1/9/2007, à 1/9/2013.

![La modification de la date de début pour 2013](concurrency/_static/change-date.png)

Jane clique sur **enregistrer** première et voit sa modification lorsque le navigateur revient à la page d’Index.

![Allocation de réserve modifiée à zéro](concurrency/_static/budget-zero.png)

John clique ensuite sur **enregistrer** sur une page d’édition qui affiche toujours un budget de $350,000.00. Que se passe-t-il ensuite est déterminé par la façon dont vous gérez les conflits d’accès concurrentiel.

Certaines de ces options sont les suivantes :

* Vous pouvez effectuer le suivi des dont un utilisateur a modifié la propriété et mettre à jour uniquement les colonnes correspondantes dans la base de données.

     Dans l’exemple de scénario, aucune donnée n’a été perdue, car des propriétés différentes ont été mis à jour par les deux utilisateurs. La prochaine fois qu’un utilisateur parcourt le service en anglais, il voit les modifications à la fois de John et Jane : une date de début de 1/9/2013 et un budget de zéro dollars. Cette méthode de mise à jour peut réduire le nombre de conflits qui peuvent entraîner une perte de données, mais il ne peut pas éviter la perte de données si des modifications concurrentes sont apportées à la même propriété d’une entité. Si Entity Framework fonctionne de cette façon dépend de la façon dont vous implémentez votre code de mise à jour. Il est souvent pratique dans une application web, car elle peut requérir que vous conservez des grandes quantités d’état afin d’effectuer le suivi de toutes les valeurs de propriété d’origine d’une entité, ainsi que les nouvelles valeurs. Maintenance de grandes quantités d’état peut affecter les performances de l’application, car il nécessite des ressources serveur ou doit être inclus dans la page web elle-même (par exemple, dans les champs masqués) ou dans un cookie.

* Vous pouvez laisser les modifications de John écrase les modifications de Jeanne.

     La prochaine fois que quelqu'un accède le service en anglais, il voit le 1/9/2013 et la valeur de $350,000.00 restaurée. Cela s’appelle un *Client Wins* ou *dernier dans Wins* scénario. (Toutes les valeurs à partir du client sont prioritaires sur les nouveautés dans le magasin de données). Comme indiqué dans l’introduction de cette section, si vous ne le faites pas de codage pour la gestion d’accès concurrentiel, cela se produit automatiquement.

* Vous pouvez empêcher la modification de Jean à partir de la mise à jour dans la base de données.

     En règle générale, vous afficher un message d’erreur, lui afficher l’état actuel des données et lui permettre d’appliquer ses modifications s’il veut toujours rendre. Cela s’appelle un *magasin Wins* scénario. (Les valeurs du magasin de données sont prioritaires sur les valeurs soumis par le client). Vous allez implémenter le scénario de magasin Wins dans ce didacticiel. Cette méthode garantit qu’aucune modification n’est remplacées sans que l’utilisateur est averti que se passe-t-il.

### <a name="detecting-concurrency-conflicts"></a>Détection des conflits d’accès concurrentiel

Vous pouvez résoudre les conflits en gérant `DbConcurrencyException` Entity Framework lève les exceptions. Pour savoir quand ces exceptions de lever, Entity Framework doit être en mesure de détecter les conflits. Par conséquent, vous devez configurer la base de données et le modèle de données en conséquence. Certaines options pour l’activation de la détection de conflit sont les suivantes :

* Dans la table de base de données, incluez une colonne de suivi qui peut être utilisée pour déterminer quand une ligne a été modifiée. Vous pouvez ensuite configurer l’Entity Framework pour inclure cette colonne dont la clause de mise à jour de SQL ou des commandes Delete.

     Le type de données de la colonne de suivi est généralement `rowversion`. Le `rowversion` valeur est un numéro séquentiel qui est incrémentée chaque fois que la ligne est mise à jour. Dans une commande Update ou Delete, Where clause inclut la valeur d’origine de la colonne de suivi (la version de ligne d’origine). Si la ligne mise à jour a été modifiée par un autre utilisateur, la valeur de la `rowversion` colonne est différente de la valeur d’origine, afin que l’instruction Update ou Delete ne peut pas trouver la ligne à mettre à jour en raison de l’emplacement où clause. Lors de la recherche d’Entity Framework qu’aucune ligne n’ont été mis à jour par la mise à jour ou la suppression de commandes (autrement dit, lorsque le nombre de lignes affectées est égal à zéro), il qui interprète comme un conflit d’accès concurrentiel.

* Configurer l’Entity Framework pour inclure les valeurs d’origine de chaque colonne dans la table dont la clause de commandes Update et Delete.

     Comme dans la première option, si n’importe où dans la ligne a changé depuis la ligne a été lu tout d’abord, Where clause ne retourne pas une ligne à mettre à jour, lequel Entity Framework interprète comme un conflit d’accès concurrentiel. Pour les tables de base de données qui ont beaucoup de colonnes, cette approche peut entraîner de très grande taille où les clauses et peut nécessiter que vous avez de grandes quantités d’état. Comme indiqué précédemment, en conservant de grandes quantités d’état peut affecter les performances de l’application. Par conséquent, cette approche n’est généralement pas recommandée, et il n’est pas la méthode utilisée dans ce didacticiel.

     Si vous ne souhaitez pas implémenter cette approche en matière d’accès concurrentiel, vous devez marquer toutes les propriétés de clé non primaire de l’entité que vous souhaitez effectuer le suivi de la concurrence d’accès en ajoutant le `ConcurrencyCheck` leur attribut. Cette modification permet à Entity Framework inclure toutes les colonnes dans la clause SQL Where des instructions Update et Delete.

Dans le reste de ce didacticiel, vous ajouterez un `rowversion` suivi des modifications de propriété à l’entité de service, créer un contrôleur et des vues et de test pour vérifier que tout fonctionne correctement.

## <a name="add-a-tracking-property-to-the-department-entity"></a>Ajouter une propriété de suivi à l’entité du service

Dans *Models/Department.cs*, ajouter une propriété de suivi nommée RowVersion :

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Le `Timestamp` attribut spécifie que cette colonne sera incluse dont la clause de mise à jour et supprimer des commandes envoyées à la base de données. L’attribut est appelé `Timestamp` , car les versions précédentes de SQL Server utilisaient SQL `timestamp` type de données avant l’instruction SQL `rowversion` remplacé. Le type .NET de `rowversion` est un tableau d’octets.

Si vous préférez utiliser l’API fluent, vous pouvez utiliser la `IsConcurrencyToken` (méthode) (dans *Data/SchoolContext.cs*) pour spécifier la propriété de suivi, comme indiqué dans l’exemple suivant :

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

En ajoutant une propriété, vous avez modifié le modèle de base de données, vous devez effectuer une autre migration.

Enregistrer vos modifications et générez le projet, puis entrez les commandes suivantes dans la fenêtre de commande :

```console
dotnet ef migrations add RowVersion
```

```console
dotnet ef database update
```

## <a name="create-a-departments-controller-and-views"></a>Créer un contrôleur de services et des vues

Structurez un contrôleur de services et des vues, comme vous l’avez fait précédemment pour les étudiants, les cours et les formateurs.

![Service de la vue de structure](concurrency/_static/add-departments-controller.png)

Dans le *DepartmentsController.cs* , modifiez les quatre occurrences de « FirstMidName » à « FullName » afin que les listes déroulantes d’administrateur service contiendra le nom complet de l’enseignant plutôt que simplement le nom de famille.

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-the-departments-index-view"></a>Mise à jour de la vue de l’Index de services

Le moteur de génération de modèles automatique a créé une colonne RowVersion dans la vue Index, mais ce champ ne doit pas être affiché.

Remplacez le code dans *Views/Departments/Index.cshtml* avec le code suivant.

[!code-html[Main](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

Cela modifie l’en-tête pour les « Services », supprime la colonne timestamp et montre le nom complet au lieu du prénom de l’administrateur.

## <a name="update-the-edit-methods-in-the-departments-controller"></a>Mettre à jour les méthodes de modification dans le contrôleur de services

Dans les deux le HttpGet `Edit` (méthode) et le `Details` (méthode), ajouter `AsNoTracking`. Dans le HttpGet `Edit` (méthode), ajoutez un chargement hâtif pour l’administrateur.

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading&highlight=2,3)]

Remplacez le code existant pour le HttpPost `Edit` méthode avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

Le code commence par la tentative de lecture du service de mise à jour. Si le `SingleOrDefaultAsync` méthode retourne la valeur null, le service a été supprimé par un autre utilisateur. Dans ce cas, le code utilise les valeurs de formulaire publiées pour créer une entité de service afin que la page de modification peut être actualisée avec un message d’erreur. En guise d’alternative, vous n’avez pas à recréer l’entité du service si vous affichez un message d’erreur sans réaffichage les champs de service.

La vue stocke l’original `RowVersion` valeur dans un champ masqué et cette méthode reçoit cette valeur dans la `rowVersion` paramètre. Avant d’appeler `SaveChanges`, vous devez faire que d’origine `RowVersion` valeur de propriété dans le `OriginalValues` collection pour l’entité.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Lorsque Entity Framework crée une commande de mise à jour de SQL, cette commande inclut une clause WHERE qui recherche une ligne contenant la version d’origine `RowVersion` valeur. Si aucune ligne n’est affectées par la commande de mise à jour (aucune ligne n’ont d’origine `RowVersion` valeur), Entity Framework lève un `DbUpdateConcurrencyException` exception.

Le code dans le bloc catch pour cette exception Obtient l’entité du service affectée qui a les valeurs mises à jour à partir de la `Entries` propriété sur l’objet exception.

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

Le `Entries` collection possède simplement un `EntityEntry` objet.  Vous pouvez utiliser cet objet pour obtenir les nouvelles valeurs entrées par l’utilisateur et les valeurs actuelles de la base de données.

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

Le code ajoute un message d’erreur personnalisé pour chaque colonne qui a des valeurs de base de données différentes à partir de ce que l’utilisateur entré dans le menu Edition de page (seul un champ est affiché ici par souci de concision).

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Enfin, le code définit les `RowVersion` valeur de la `departmentToUpdate` à la nouvelle valeur récupérée à partir de la base de données. Cette nouvelle `RowVersion` valeur est stockée dans le champ masqué lorsque la modification de page s’affiche de nouveau et la prochaine fois que l’utilisateur clique sur **enregistrer**, seules les erreurs d’accès concurrentiel qui se produisent dans la mesure où l’actualiser de la page de modification est interceptée.

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

Le `ModelState.Remove` instruction est requise car `ModelState` a l’ancien `RowVersion` valeur. Dans la vue, le `ModelState` valeur pour un champ est prioritaire sur les valeurs de propriété de modèle lorsque les deux sont présents.

## <a name="update-the-department-edit-view"></a>Mettre à jour la vue Modifier le service

Dans *Views/Departments/Edit.cshtml*, apportez les modifications suivantes :

* Ajouter un champ masqué pour enregistrer le `RowVersion` valeur de propriété, le champ masqué qui suit immédiatement la `DepartmentID` propriété.

* Ajouter une option « Sélectionner un administrateur » à la liste déroulante.

[!code-html[Main](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts-in-the-edit-page"></a>Tester les conflits d’accès concurrentiel dans la page de modification

Exécutez l’application et accédez à la page d’Index de services. Avec le bouton droit le **modifier** lien hypertexte pour le service en anglais, puis sélectionnez **ouvrir dans un nouvel onglet**, puis cliquez sur le **modifier** lien hypertexte pour le service en anglais. Les onglets de deux navigateur affichent désormais les mêmes informations.

Modifier un champ dans le premier onglet de navigateur, cliquez sur **enregistrer**.

![Édition de service page 1 après modification](concurrency/_static/edit-after-change-1.png)

Le navigateur affiche la page d’Index de la valeur modifiée.

Modifier un champ dans le deuxième onglet du navigateur.

![Modification du service page 2 après la modification](concurrency/_static/edit-after-change-2.png)

Cliquez sur **Enregistrer**. Vous voyez un message d’erreur :

![Message d’erreur service modifier page](concurrency/_static/edit-error.png)

Cliquez sur **enregistrer** à nouveau. La valeur que vous avez entré dans le deuxième onglet navigateur est enregistrée. Vous voyez les valeurs enregistrées lorsque la page d’Index s’affiche.

## <a name="update-the-delete-page"></a>Mise à jour de la page de suppression

Pour la page de suppression, Entity Framework détecte les conflits d’accès concurrentiel causés par quelqu'un d’autre modification du service d’une manière similaire. Lorsque le HttpGet `Delete` méthode affiche la vue de confirmation, la vue inclut la version d’origine `RowVersion` valeur dans un champ masqué. Que valeur est ensuite disponible pour le HttpPost `Delete` méthode qui est appelée lorsque l’utilisateur confirme la suppression. Lorsque Entity Framework crée la commande SQL DELETE, il inclut une clause WHERE avec la version d’origine `RowVersion` valeur. Si les résultats de la commande dans des lignes nulles affectés (c'est-à-dire la ligne a été modifiée après l’affichage de la page de confirmation de suppression), une exception d’accès concurrentiel est levée et le HttpGet `Delete` méthode est appelée avec un indicateur d’erreur sur « True » pour réafficher le page de confirmation avec un message d’erreur. Il est également possible qu’aucune ligne ont été affectés, car la ligne a été supprimée par un autre utilisateur, afin que dans ce cas, aucun message d’erreur n’est affiché.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Mettre à jour les méthodes de suppression dans le contrôleur de services

Dans *DepartmentsController.cs*, remplacez le HttpGet `Delete` méthode avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

La méthode accepte un paramètre facultatif qui indique si la page est en cours affiche de nouveau après une erreur d’accès concurrentiel. Si cet indicateur a la valeur true et que le service spécifié n’est plus existe, il a été supprimé par un autre utilisateur. Dans ce cas, le code redirige vers la page d’Index.  Si cet indicateur a la valeur true et que le service n’existe pas, il a été modifié par un autre utilisateur. Dans ce cas, le code envoie un message d’erreur à la vue à l’aide de `ViewData`.  

Remplacez le code dans le HttpPost `Delete` (méthode) (nommé `DeleteConfirmed`) avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

Dans le code de modèle généré automatiquement qui vient d’être remplacé, cette méthode acceptées uniquement un ID d’enregistrement :


```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Vous avez modifié ce paramètre dans une instance d’entité de service créée par le classeur de modèles. Cela donne accès EF à la valeur de propriété RowVersion en plus de la clé d’enregistrement.

```csharp
public async Task<IActionResult> Delete(Department department)
```

Vous avez également modifié le nom de la méthode d’action à partir de `DeleteConfirmed` à `Delete`. Le code de modèle généré automatiquement utilisé le nom `DeleteConfirmed` pour permettre à la méthode HttpPost une signature unique. (Le CLR a besoin des méthodes surchargées pour avoir des paramètres de l’autre méthode.) Maintenant que les signatures sont uniques, vous pouvez coller avec la convention MVC et utiliser le même nom pour les méthodes de suppression HttpPost et HttpGet.

Si le service a déjà été supprimé, le `AnyAsync` méthode retourne la valeur false et l’application simplement revient à la méthode de l’Index.

Si une erreur d’accès concurrentiel est interceptée, le code affiche la page de confirmation de suppression de nouveau et fournit un indicateur qui indique qu’il doit afficher un message d’erreur d’accès concurrentiel.

### <a name="update-the-delete-view"></a>Mettre à jour la vue Delete

Dans *Views/Departments/Delete.cshtml*, remplacez le code de modèle généré automatiquement par le code suivant qui ajoute un champ de message d’erreur et les champs masqués pour les propriétés de DepartmentID et RowVersion. Les modifications sont mises en surbrillance.

[!code-html[Main](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

Il apporte les modifications suivantes :

* Ajoute un message d’erreur entre la `h2` et `h3` des en-têtes.

* Remplace FirstMidName FullName dans le **administrateur** champ.

* Supprime le champ RowVersion.

* Ajoute un champ masqué pour le `RowVersion` propriété.

Exécutez l’application et accédez à la page d’Index de services. Avec le bouton droit le **supprimer** lien hypertexte pour le service en anglais, puis sélectionnez **ouvrir dans un nouvel onglet**, puis, dans le premier onglet, cliquez sur le **modifier** lien hypertexte pour le service en anglais.

Dans la première fenêtre, modifiez l’une des valeurs, puis cliquez sur **enregistrer**:

![Page de modification du service après modification avant la suppression](concurrency/_static/edit-after-change-for-delete.png)

Dans le deuxième onglet, cliquez sur **supprimer**. Vous voyez le message d’erreur d’accès concurrentiel et les valeurs de service sont actualisés avec ce qui est actuellement dans la base de données.

![Page de confirmation de suppression de service avec erreur d’accès concurrentiel](concurrency/_static/delete-error.png)

Si vous cliquez sur **supprimer** là encore, vous êtes redirigé vers la page d’Index, ce qui indique que le service a été supprimé.

## <a name="update-details-and-create-views"></a>Mettre à jour les détails et de créer des vues

Vous pouvez éventuellement nettoyer le code de modèle généré automatiquement dans les détails et créer des vues.

Remplacez le code dans *Views/Departments/Details.cshtml* à supprimer la colonne timestamp, puis affichez le nom complet de l’administrateur.

[!code-html[Main](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Remplacez le code dans *Views/Departments/Create.cshtml* pour ajouter une option de sélection à la liste déroulante.

[!code-html[Main](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="summary"></a>Récapitulatif

Cette étape termine l’introduction à la gestion des conflits d’accès concurrentiel. Pour plus d’informations sur la façon de gérer l’accès concurrentiel dans EF Core, consultez [conflits d’accès concurrentiel](https://docs.microsoft.com/ef/core/saving/concurrency). Le didacticiel suivant montre comment implémenter l’héritage table par hiérarchie pour les entités Instructor et Student.

>[!div class="step-by-step"]
[Précédent](update-related-data.md)
[Suivant](inheritance.md)  

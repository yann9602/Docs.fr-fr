---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
title: "Gestion d’accès concurrentiel avec Entity Framework 6 dans une Application ASP.NET MVC 5 (10 12) | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/08/2014
ms.topic: article
ms.assetid: be0c098a-1fb2-457e-b815-ddca601afc65
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: e7b79503a1d297d40c6824f8b2b7bbc1f42b9fca
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="handling-concurrency-with-the-entity-framework-6-in-an-aspnet-mvc-5-application-10-of-12"></a>Gestion d’accès concurrentiel avec Entity Framework 6 dans une Application ASP.NET MVC 5 (10 12)
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) ou [télécharger le PDF](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio 2013. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).


Dans les didacticiels antérieures, vous avez appris comment mettre à jour des données. Ce didacticiel montre comment gérer les conflits lorsque plusieurs utilisateurs mettre à jour la même entité en même temps.

Vous allez modifier les pages web qui fonctionnent avec le `Department` entité afin qu’ils gèrent les erreurs d’accès concurrentiel. Les illustrations suivantes montrent les pages Index et de suppression, y compris des messages qui sont affichent si un conflit de concurrence se produit.

![Department_Index_page_before_edits](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

## <a name="concurrency-conflicts"></a>Conflits d’accès concurrentiel

Un conflit de concurrence se produit lorsqu’un utilisateur affiche les données d’une entité afin de la pour modifier, puis un autre utilisateur met à jour les données de la même entité avant la première modification utilisateur est écrit dans la base de données. Si vous n’activez pas la détection de ces conflits, la personne qui met à jour la base de données remplace dernier les modifications de l’autre utilisateur. Dans de nombreuses applications, ce risque est acceptable : s’il existe quelques utilisateurs ou les mises à jour, ou si n’est pas réellement critique si des modifications sont remplacées, le coût de la programmation d’accès concurrentiel peut-être dépasser l’avantage. Dans ce cas, vous n’êtes pas obligé de configurer l’application pour gérer les conflits d’accès concurrentiel.

### <a name="pessimistic-concurrency-locking"></a>L’accès simultané pessimiste (verrouillage)

Si votre application doit-elle éviter la perte accidentelle de données dans les scénarios d’accès concurrentiel, une manière de procéder consiste à utiliser des verrous de base de données. Il s’agit *d’accès concurrentiel pessimiste*. Par exemple, avant de lire une ligne à partir d’une base de données, vous demandez un verrou de lecture seule ou pour l’accès de mise à jour. Si vous verrouillez une ligne pour l’accès de mise à jour, aucun autre utilisateur n’est autorisés pour le verrouillage de la ligne pour en lecture seule ou mettre à jour de l’accès, car ils obtenez une copie des données sont en cours de modification. Si vous verrouillez une ligne pour l’accès en lecture seule, d’autres peuvent également le verrouiller pour l’accès en lecture seule, mais pas pour la mise à jour.

Gestion des verrous présente des inconvénients. Il peut être complexe au programme. Il nécessite des ressources de gestion de base de données importantes, et cela peut provoquer des problèmes de performances en tant que le nombre d’utilisateurs d’une application augmente. Pour ces raisons, pas tous les systèmes de gestion de base de données prend en charge l’accès simultané pessimiste. Entity Framework ne fournit aucune prise en charge intégrée pour celle-ci, et ce didacticiel ne vous montre comment l’implémenter.

### <a name="optimistic-concurrency"></a>Accès concurrentiel optimiste

L’alternative à l’accès concurrentiel pessimiste est *d’accès concurrentiel optimiste*. L’accès concurrentiel optimiste signifie autoriser les conflits d’accès concurrentiel se produire et puis réagir correctement dans le cas. Par exemple, John exécute la page Modifier les services, des modifications du **Budget** montant pour le service en anglais à partir de $350,000.00 à 0,00 $.

![Changing_English_dept_budget_to_100000](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

Avant de Jean clique sur **enregistrer**, Jane exécute la même page et les mêmes modifications le **Date de début** au champ à partir du 1/9/2007 8/8/2013.

![Changing_English_dept_start_date_to_1999](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

John clique sur **enregistrer** premier et voit sa modification lorsque le navigateur revient à la page d’Index, Jane puis clique sur **enregistrer**. Que se passe-t-il ensuite est déterminé par la façon dont vous gérez les conflits d’accès concurrentiel. Certaines de ces options sont les suivantes :

- Vous pouvez effectuer le suivi des dont un utilisateur a modifié la propriété et mettre à jour uniquement les colonnes correspondantes dans la base de données. Dans l’exemple de scénario, aucune donnée n’a été perdue, car des propriétés différentes ont été mis à jour par les deux utilisateurs. La prochaine fois que quelqu'un accède le service en anglais, il voit les modifications de John et Jane : une date de début de 8/8/2013 et qu’une allocation de réserve de zéro dollars.

    Cette méthode de mise à jour peut réduire le nombre de conflits qui peuvent entraîner une perte de données, mais il ne peut pas éviter la perte de données si des modifications concurrentes sont apportées à la même propriété d’une entité. Si Entity Framework fonctionne de cette façon dépend de la façon dont vous implémentez votre code de mise à jour. Il est souvent pratique dans une application web, car elle peut requérir que vous conservez des grandes quantités d’état afin d’effectuer le suivi de toutes les valeurs de propriété d’origine d’une entité, ainsi que les nouvelles valeurs. Maintenance de grandes quantités d’état peut affecter les performances de l’application, car il nécessite des ressources serveur ou doit être inclus dans la page web elle-même (par exemple, dans les champs masqués) ou dans un cookie.
- Vous pouvez laisser les modifications de Jane écrase les modifications de John. La prochaine fois que quelqu'un accède le service en anglais, il voit 8/8/2013 et la valeur de $350,000.00 restaurée. Cela s’appelle un *Client Wins* ou *dernier dans Wins* scénario. (Toutes les valeurs à partir du client sont prioritaires sur les nouveautés dans le magasin de données). Comme indiqué dans l’introduction de cette section, si vous ne le faites pas de codage pour la gestion d’accès concurrentiel, cela se produit automatiquement.
- Vous pouvez empêcher la modification de Jeanne à partir de la mise à jour dans la base de données. En règle générale, vous afficher un message d’erreur, elle indique l’état actuel des données et lui permet de réappliquer les modifications si elle souhaite que pour les rendre encore. Cela s’appelle un *magasin Wins* scénario. (Les valeurs du magasin de données sont prioritaires sur les valeurs soumis par le client). Vous allez implémenter le scénario de magasin Wins dans ce didacticiel. Cette méthode garantit qu’aucune modification n’est remplacées sans que l’utilisateur est averti que se passe-t-il.

### <a name="detecting-concurrency-conflicts"></a>Détection des conflits d’accès concurrentiel

Vous pouvez résoudre les conflits en gérant [OptimisticConcurrencyException](https://msdn.microsoft.com/en-us/library/system.data.optimisticconcurrencyexception.aspx) Entity Framework lève les exceptions. Pour savoir quand ces exceptions de lever, Entity Framework doit être en mesure de détecter les conflits. Par conséquent, vous devez configurer la base de données et le modèle de données en conséquence. Certaines options pour l’activation de la détection de conflit sont les suivantes :

- Dans la table de base de données, incluez une colonne de suivi qui peut être utilisée pour déterminer quand une ligne a été modifiée. Vous pouvez ensuite configurer l’Entity Framework pour inclure cette colonne dans la `Where` clause SQL `Update` ou `Delete` commandes.

    Le type de données de la colonne de suivi est généralement [rowversion](https://msdn.microsoft.com/en-us/library/ms182776(v=sql.110).aspx). Le [rowversion](https://msdn.microsoft.com/en-us/library/ms182776(v=sql.110).aspx) valeur est un numéro séquentiel qui est incrémentée chaque fois que la ligne est mise à jour. Dans un `Update` ou `Delete` commande, le `Where` clause inclut la valeur d’origine de la colonne de suivi (la version de ligne d’origine). Si la ligne mise à jour a été modifiée par un autre utilisateur, la valeur de la `rowversion` colonne est différente de la valeur d’origine, afin que la `Update` ou `Delete` instruction ne peut pas trouver la ligne mise à jour en raison de le `Where` clause. Lorsque l’Entity Framework détecte qu’aucune ligne n’a été mis à jour par le `Update` ou `Delete` de commandes (autrement dit, lorsque le nombre de lignes affectées est égal à zéro), il qui interprète comme un conflit d’accès concurrentiel.
- Configurer l’Entity Framework pour inclure les valeurs d’origine de chaque colonne dans la table dans le `Where` clause de `Update` et `Delete` commandes.

    Comme dans la première option, si n’importe où dans la ligne a changé depuis la ligne a été lu tout d’abord, le `Where` clause ne retourne pas une ligne à mettre à jour, lequel Entity Framework interprète comme un conflit d’accès concurrentiel. Pour les tables de base de données qui ont beaucoup de colonnes, cette approche peut entraîner de très grande `Where` clauses et peut nécessiter que vous avez de grandes quantités d’état. Comme indiqué précédemment, en conservant de grandes quantités d’état peut affecter les performances de l’application. Par conséquent, cette approche n’est généralement pas recommandée, et il n’est pas la méthode utilisée dans ce didacticiel.

    Si vous ne souhaitez pas implémenter cette approche en matière d’accès concurrentiel, vous devez marquer toutes les propriétés de clé non primaire de l’entité que vous souhaitez effectuer le suivi de la concurrence d’accès en ajoutant le [ConcurrencyCheck](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.concurrencycheckattribute.aspx) leur attribut. Que modification permet à Entity Framework inclure toutes les colonnes dans l’instruction SQL `WHERE` clause de `UPDATE` instructions.

Dans le reste de ce didacticiel, vous ajouterez un [rowversion](https://msdn.microsoft.com/en-us/library/ms182776(v=sql.110).aspx) le suivi des propriétés pour le `Department` entité, créer un contrôleur et des vues et de test pour vérifier que tout fonctionne correctement.

## <a name="add-an-optimistic-concurrency-property-to-the-department-entity"></a>Ajouter une propriété de l’accès concurrentiel optimiste à l’entité du service

Dans *Models\Department.cs*, ajouter une propriété de suivi nommée `RowVersion`:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=20-22)]

Le [Timestamp](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.timestampattribute.aspx) attribut spécifie que cette colonne est incluse dans le `Where` clause de `Update` et `Delete` commandes envoyées à la base de données. L’attribut est appelé [Timestamp](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.timestampattribute.aspx) , car les versions précédentes de SQL Server utilisaient SQL [timestamp](https://msdn.microsoft.com/en-us/library/ms182776(v=SQL.90).aspx) type de données avant l’instruction SQL [rowversion](https://msdn.microsoft.com/en-us/library/ms182776(v=sql.110).aspx) remplacé. Le type .net de [rowversion](https://msdn.microsoft.com/en-us/library/ms182776(v=sql.110).aspx) est un tableau d’octets.

Si vous préférez utiliser l’API fluent, vous pouvez utiliser la [IsConcurrencyToken](https://msdn.microsoft.com/en-us/library/gg679501(v=VS.103).aspx) méthode pour spécifier la propriété de suivi, comme indiqué dans l’exemple suivant :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

En ajoutant une propriété, vous avez modifié le modèle de base de données, vous devez effectuer une autre migration. Dans Package Manager Console (PMC), entrez les commandes suivantes :

[!code-console[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cmd)]

## <a name="modify-the-department-controller"></a>Modifier le contrôleur de service

Dans *Controllers\DepartmentController.cs*, ajoutez un `using` instruction :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

Dans le *DepartmentController.cs* , modifiez les quatre occurrences de « LastName » à « FullName » afin que les listes déroulantes d’administrateur service contiendra le nom complet de l’enseignant plutôt que simplement le nom de famille.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=1)]

Remplacez le code existant pour le `HttpPost` `Edit` méthode avec le code suivant :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

Si le `FindAsync` méthode retourne la valeur null, le service a été supprimé par un autre utilisateur. Le code utilise les valeurs de formulaire publiées pour créer une entité de service afin que la page de modification peut être actualisée avec un message d’erreur. En guise d’alternative, vous n’avez pas à recréer l’entité du service si vous affichez un message d’erreur sans réaffichage les champs de service.

La vue stocke l’original `RowVersion` reçoit la valeur dans un champ masqué et la méthode dans le `rowVersion` paramètre. Avant d’appeler `SaveChanges`, vous devez faire que d’origine `RowVersion` valeur de propriété dans le `OriginalValues` collection pour l’entité. Lorsque Entity Framework crée ensuite un SQL `UPDATE` de commande, que la commande doit contenir un `WHERE` clause qui recherche une ligne contenant la version d’origine `RowVersion` valeur.

Si aucune ligne n’est affecté par le `UPDATE` commande (aucune ligne n’ont d’origine `RowVersion` valeur), Entity Framework lève un `DbUpdateConcurrencyException` exception et le code dans le `catch` bloc Obtient l’élément affecté `Department` entité à partir de l’exception objet.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

Cet objet a les nouvelles valeurs entrées par l’utilisateur dans son `Entity` propriété et vous pouvez obtenir les valeurs lues à partir de la base de données en appelant le `GetDatabaseValues` (méthode).

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

Le `GetDatabaseValues` méthode retourne null si quelqu'un a supprimé la ligne de la base de données ; dans le cas contraire, vous devez effectuer un cast de l’objet retourné à la `Department` classe afin d’accéder à la `Department` propriétés. (Étant donné que vous déjà activée pour la suppression, `databaseEntry` serait null uniquement si le service a été supprimé après `FindAsync` s’exécute et avant `SaveChanges` s’exécute.)

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

Ensuite, le code ajoute un message d’erreur personnalisé pour chaque colonne qui a des valeurs de la base de données différents de ce que l’utilisateur a entré dans la page Modifier :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

Un message d’erreur plu explique que s’est-il passé et ce qu’il faut faire :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

Enfin, le code définit les `RowVersion` valeur de la `Department` de récupérer l’objet à la nouvelle valeur à partir de la base de données. Cette nouvelle `RowVersion` valeur est stockée dans le champ masqué lorsque la modification de page s’affiche de nouveau et la prochaine fois que l’utilisateur clique sur **enregistrer**, seules les erreurs d’accès concurrentiel qui se produisent dans la mesure où l’actualiser de la page de modification est interceptée.

Dans *Views\Department\Edit.cshtml*, ajoutez un champ masqué pour enregistrer le `RowVersion` valeur de propriété, qui suit immédiatement le champ masqué pour le `DepartmentID` propriété :

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=18)]

## <a name="testing-optimistic-concurrency-handling"></a>Test de gestion d’accès concurrentiel optimiste

Exécuter le site, puis cliquez sur **services**:

![Department_Index_page_before_edits](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)

Bouton droit sur le **modifier** lien hypertexte pour le service en anglais, puis sélectionnez **ouvert dans un nouvel onglet,** puis cliquez sur le **modifier** lien hypertexte pour le service en anglais. Les deux onglets affichent les mêmes informations.

![Department_Edit_page_before_changes](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

Modifier un champ dans le premier onglet de navigateur, cliquez sur **enregistrer**.

![Department_Edit_page_1_after_change](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

Le navigateur affiche la page d’Index de la valeur modifiée.

![Departments_Index_page_after_first_budget_edit](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image8.png)

Modifier un champ dans le deuxième onglet du navigateur, cliquez sur **enregistrer**.

![Department_Edit_page_2_after_change](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

Cliquez sur **enregistrer** dans le deuxième onglet du navigateur. Vous voyez un message d’erreur :

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

Cliquez sur **enregistrer** à nouveau. La valeur que vous avez entré dans le deuxième onglet navigateur est enregistrée avec la valeur d’origine des données que vous avez modifié dans le navigateur en premier. Vous voyez les valeurs enregistrées lorsque la page d’Index s’affiche.

![Department_Index_page_with_change_from_second_browser](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image11.png)

## <a name="updating-the-delete-page"></a>Mise à jour de la Page de suppression

Pour la page de suppression, Entity Framework détecte les conflits d’accès concurrentiel causés par quelqu'un d’autre modification du service d’une manière similaire. Lorsque le `HttpGet` `Delete` méthode affiche la vue de confirmation, la vue inclut la version d’origine `RowVersion` valeur dans un champ masqué. Que valeur est ensuite disponible pour le `HttpPost` `Delete` méthode qui est appelée lorsque l’utilisateur confirme la suppression. Lorsque Entity Framework crée l’instruction SQL `DELETE` de commande, elle inclut un `WHERE` clause avec la version d’origine `RowVersion` valeur. Si les résultats de la commande dans des lignes nulles affectés (c'est-à-dire la ligne a été modifiée après l’affichage de la page de confirmation de suppression), une exception d’accès concurrentiel est levée et le `HttpGet Delete` méthode est appelée avec un indicateur d’erreur défini sur `true` pour réafficher le page de confirmation avec un message d’erreur. Il est également possible qu’aucune ligne ont été affectés, car la ligne a été supprimée par un autre utilisateur, afin que dans ce cas d’un message d’erreur différent est affiché.

Dans *DepartmentController.cs*, remplacez le `HttpGet` `Delete` méthode avec le code suivant :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

La méthode accepte un paramètre facultatif qui indique si la page est en cours affiche de nouveau après une erreur d’accès concurrentiel. Si cet indicateur est `true`, un message d’erreur est envoyé à la vue en utilisant un `ViewBag` propriété.

Remplacez le code dans le `HttpPost` `Delete` (méthode) (nommé `DeleteConfirmed`) avec le code suivant :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs)]

Dans le code de modèle généré automatiquement qui vient d’être remplacé, cette méthode acceptées uniquement un ID d’enregistrement :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs)]

Vous avez modifié ce paramètre pour un `Department` instance d’entité créé par le classeur de modèles. Cela vous permet d’accéder à la `RowVersion` valeur de propriété en plus de la clé d’enregistrement.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs)]

Vous avez également modifié le nom de la méthode d’action à partir de `DeleteConfirmed` à `Delete`. Le nom de code modèle généré automatiquement le `HttpPost` `Delete` méthode `DeleteConfirmed` afin de donner la `HttpPost` une signature unique (méthode). (Le CLR a besoin des méthodes surchargées pour avoir des paramètres de l’autre méthode.) Maintenant que les signatures sont uniques, vous pouvez coller avec la convention MVC et utiliser le même nom pour le `HttpPost` et `HttpGet` supprimer les méthodes.

Si une erreur d’accès concurrentiel est interceptée, le code affiche la page de confirmation de suppression de nouveau et fournit un indicateur qui indique qu’il doit afficher un message d’erreur d’accès concurrentiel.

Dans *Views\Department\Delete.cshtml*, remplacez le code de modèle généré automatiquement par le code suivant qui ajoute un champ de message d’erreur et les champs masqués pour les propriétés de DepartmentID et RowVersion. Les modifications sont mises en surbrillance.

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml?highlight=9-10,21,52-54)]

Ce code ajoute un message d’erreur entre la `h2` et `h3` des en-têtes :

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cshtml)]

Il remplace `LastName` avec `FullName` dans le `Administrator` champ :

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml)]

Enfin, il ajoute les champs masqués de la `DepartmentID` et `RowVersion` propriétés une fois que la `Html.BeginForm` instruction :

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cshtml)]

Exécutez la page d’Index de services. Bouton droit sur le **supprimer** lien hypertexte pour le service en anglais, puis sélectionnez **ouvert dans un nouvel onglet,** puis, dans le premier onglet, cliquez sur le **modifier** lien hypertexte pour le service en anglais.

Dans la première fenêtre, modifiez l’une des valeurs, puis cliquez sur **enregistrer** :

![Department_Edit_page_after_change_before_delete](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image12.png)

La page d’Index confirme la modification.

![Departments_Index_page_after_budget_edit_before_delete](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image13.png)

Dans le deuxième onglet, cliquez sur **supprimer**.

![Department_Delete_confirmation_page_before_concurrency_error](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image14.png)

Vous voyez le message d’erreur d’accès concurrentiel et les valeurs de service sont actualisés avec ce qui est actuellement dans la base de données.

![Department_Delete_confirmation_page_with_concurrency_error](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

Si vous cliquez sur **supprimer** là encore, vous êtes redirigé vers la page d’Index, ce qui indique que le service a été supprimé.

## <a name="summary"></a>Résumé

Cette étape termine l’introduction à la gestion des conflits d’accès concurrentiel. Pour plus d’informations sur les autres méthodes pour gérer les différents scénarios de concurrence, consultez [des modèles d’accès concurrentiel optimiste](https://msdn.microsoft.com/en-us/data/jj592904) et [utilisation des valeurs de propriété](https://msdn.microsoft.com/en-us/data/jj592677) sur MSDN. Le didacticiel suivant montre comment implémenter l’héritage table par hiérarchie pour le `Instructor` et `Student` entités.

Vous trouverez des liens vers d’autres ressources Entity Framework dans le [ASP.NET Data Access - ressources recommandées](../../../../whitepapers/aspnet-data-access-content-map.md).

>[!div class="step-by-step"]
[Précédent](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application.md)
[Suivant](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)

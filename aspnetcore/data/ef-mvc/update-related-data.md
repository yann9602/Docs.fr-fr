---
title: "Données - 7 sur 10 inhérentes à cœur d’ASP.NET MVC avec EF Core - mise à jour"
author: tdykstra
description: "Dans ce didacticiel vous allez mettre à jour les données associées en mettant à jour les propriétés de navigation et les champs de clé étrangère."
keywords: "ASP.NET Core, Entity Framework Core, les données associées, jointures"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 67bd162b-bfb7-4750-9e7f-705228b5288c
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 981a099630008eaf11599b17c4d4d5d6e86b8b90
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2017
---
# <a name="updating-related-data---ef-core-with-aspnet-core-mvc-tutorial-7-of-10"></a>Mise à jour des données connexes - Core EF avec le didacticiel d’ASP.NET MVC de base (7 sur 10)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).

Dans le didacticiel précédent, vous avez affiché les données associées ; Dans ce didacticiel vous allez mettre à jour les données associées en mettant à jour les propriétés de navigation et les champs de clé étrangère.

Les illustrations suivantes montrent certaines des pages que vous allez utiliser.

![Page de modification du cours](update-related-data/_static/course-edit.png)

![Page de modification du formateur](update-related-data/_static/instructor-edit-courses.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a>Personnaliser le créer et modifier des Pages pour les cours

Lorsqu’une nouvelle entité de cours est créée, il doit avoir une relation à un service existant. Pour faciliter ce processus, le code de modèle généré automatiquement inclut des méthodes de contrôleur et de créer et modifier des vues qui incluent une liste déroulante pour sélectionner le service. Les jeux de liste déroulante, le `Course.DepartmentID` une propriété de clé étrangère, et c’est tout Entity Framework a besoin pour charger le `Department` propriété de navigation avec l’entité de service appropriée. Vous allez utiliser le code de modèle généré automatiquement, mais modifier légèrement pour ajouter la gestion des erreurs et de trier la liste déroulante.

Dans *CoursesController.cs*, supprimez les quatre méthodes de créer et modifier et remplacez-les par le code suivant :

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

Après le `Edit` HttpPost (méthode), créez une méthode qui charge les informations de service pour obtenir la liste déroulante.

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

Le `PopulateDepartmentsDropDownList` Obtient une liste de tous les départements sont triés par nom de méthode, crée un `SelectList` collection pour obtenir la liste déroulante et passe la collection à la vue dans `ViewBag`. La méthode accepte le paramètre facultatif `selectedDepartment` paramètre qui permet au code appelant spécifier l’élément est sélectionné lors du rendu de la liste déroulante. La vue passe le nom « DepartmentID » pour le `<select>` d’assistance de balise et l’application d’assistance puis sache qu’il peut pour rechercher dans le `ViewBag` de l’objet pour un `SelectList` nommé « DepartmentID ».

Le HttpGet `Create` les appels de méthode le `PopulateDepartmentsDropDownList` méthode sans définir l’élément sélectionné, car de formation le service n’est pas établi encore :

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

Le HttpGet `Edit` méthode définit l’élément sélectionné, en fonction de l’ID du service qui est déjà affecté au cours en cours de modification :

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Pour les deux méthodes HttpPost `Create` et `Edit` également inclure du code qui définit l’élément sélectionné quand ils réafficher la page après une erreur. Cela garantit que lorsque la page s’affiche de nouveau pour afficher le message d’erreur, quel que soit le service a été sélectionné reste sélectionné.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Ajouter. AsNoTracking pour plus d’informations et de méthodes de suppression

Pour optimiser les performances des détails du cours et supprimer des pages, ajouter `AsNoTracking` appelle le `Details` et HttpGet `Delete` méthodes.

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Modifier les vues de cours

Dans *Views/Courses/Create.cshtml*, ajouter une option « Sélectionner département » pour le **service** déroulante liste, de modifier la légende de **DepartmentID** à ** Service**et ajouter un message de validation.

[!code-html[Main](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

Dans *Views/Courses/Edit.cshtml*, apporter la même modification pour le champ de service que vous venez de faire dans *Create.cshtml*.

Également dans *Views/Courses/Edit.cshtml*, ajoutez un champ de numéro de cours avant la **titre** champ. Étant donné que le nombre de cours est la clé primaire, il est affiché, mais il ne peut pas être modifié.

[!code-html[Main](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

Il existe déjà un champ masqué (`<input type="hidden">`) pour le nombre de cours dans la vue Edition. Ajout d’un `<label>` application d’assistance de balise n’élimine la nécessité pour le champ masqué, car elle n’entraîne pas le nombre de cours à inclure dans les données publiées lorsque l’utilisateur clique sur **enregistrer** sur la **modifier** page.

Dans *Views/Courses/Delete.cshtml*, ajoutez un champ du numéro de cours en haut et modifier l’ID de service pour le nom du service.

[!code-html[Main](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

Dans *Views/Courses/Details.cshtml*, apporter la même modification que vous venez de *Delete.cshtml*.

### <a name="test-the-course-pages"></a>Tester les pages de cours

Exécuter l’application, sélectionnez le **cours** , cliquez sur **créer un nouveau**et entrez les données d’un nouveau cours :

![Page de création de cours](update-related-data/_static/course-create.png)

Cliquez sur **Créer**. La page d’Index de cours s’affiche avec le cours de nouveau ajouté à la liste. Le nom du service dans la liste de page d’Index provient de la propriété de navigation, indiquant que la relation a été établie correctement.

Cliquez sur **modifier** sur un cours dans la page d’Index de cours.

![Page de modification du cours](update-related-data/_static/course-edit.png)

Modifier les données sur la page, cliquez sur **enregistrer**. La page d’Index de cours s’affiche avec les données de cours mis à jour.

## <a name="add-an-edit-page-for-instructors"></a>Ajouter une Page de modification pour les formateurs

Lorsque vous modifiez un enregistrement du formateur, que vous souhaitez être en mesure de mettre à jour l’attribution du formateur office. Entité Instructor a une relation un-à-zéro-ou-un avec l’entité OfficeAssignment, ce qui signifie que votre code doit gérer les situations suivantes :

* Si l’utilisateur efface l’attribution d’office et il avait une valeur, supprimez l’entité OfficeAssignment.

* Si l’utilisateur entre une valeur de l’attribution d’office et il a été initialement vide, créez une nouvelle entité OfficeAssignment.

* Si l’utilisateur modifie la valeur d’une assignation d’office, modifiez la valeur dans une entité OfficeAssignment existante.

### <a name="update-the-instructors-controller"></a>Mettre à jour le contrôleur instructeurs

Dans *InstructorsController.cs*, modifiez le code dans le HttpGet `Edit` méthode afin qu’elle charge de l’entité Instructor `OfficeAssignment` propriété de navigation et les appels `AsNoTracking`:

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

Remplacez le HttpPost `Edit` méthode avec le code suivant pour gérer les mises à jour de l’attribution d’office :

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Le code effectue les opérations suivantes :

-  Modifie le nom de la méthode `EditPost` , car la signature est désormais le même que le HttpGet `Edit` (méthode) (le `ActionName` attribut spécifie que le `/Edit/` URL est toujours utilisé).

-  Obtient l’entité Instructor actuelle à partir de la base de données à l’aide pour le chargement hâtif le `OfficeAssignment` propriété de navigation. Il est identique à ce que vous l’avez fait dans le HttpGet `Edit` (méthode).

-  Met à jour l’entité Instructor récupérée avec des valeurs dans le classeur de modèles. Le `TryUpdateModel` surcharge vous permet à la liste blanche les propriétés que vous souhaitez inclure. Cela empêche la validation excessive, comme expliqué dans la [deuxième didacticiel](crud.md).

    <!-- Snippets do not play well with <ul> [!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```
    
-   Si l’emplacement du bureau est vide, définit la propriété Instructor.OfficeAssignment NULL afin que la ligne correspondante dans la table OfficeAssignment est supprimée.

    <!-- Snippets do not play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- Enregistre les modifications dans la base de données.

### <a name="update-the-instructor-edit-view"></a>Mettre à jour la vue Modifier le formateur

Dans *Views/Instructors/Edit.cshtml*, ajouter un nouveau champ pour la modification de l’emplacement du bureau, à la fin avant du **enregistrer** bouton :

[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Exécuter l’application, sélectionnez le **instructeurs** onglet, puis cliquez sur **modifier** sur un formateur. Modifier la **bureaux** et cliquez sur **enregistrer**.

![Page de modification du formateur](update-related-data/_static/instructor-edit-office.png)

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Ajouter des affectations de cours à la page Modifier le formateur

Instructeurs enseigner à n’importe quel nombre de cours. Maintenant, vous allez améliorer la page Modifier le formateur en ajoutant la possibilité de modifier les affectations de cours à l’aide d’un groupe de cases à cocher, comme indiqué dans la capture d’écran suivante :

![Page de modification du formateur en cours](update-related-data/_static/instructor-edit-courses.png)

La relation entre les entités en cours et le formateur est plusieurs-à-plusieurs. Pour ajouter et supprimer des relations, vous ajoutez et supprimez des entités vers et depuis le jeu d’entités CourseAssignments jointure.

Formateur de l’interface utilisateur qui permet de modifier les cours est assigné à est un groupe de cases à cocher. Une case à cocher pour chaque cours dans la base de données s’affiche, et celles qui le formateur est actuellement attribué aux sont sélectionnés. L’utilisateur peut sélectionner ou désactivez les cases à cocher pour modifier les affectations de cours. Si le nombre de cours ont été beaucoup plus important, vous souhaiterez probablement utiliser une autre méthode de présentation des données dans la vue, mais que vous utiliseriez la même méthode de manipulation d’une entité de jointure pour créer ou supprimer des relations.

### <a name="update-the-instructors-controller"></a>Mettre à jour le contrôleur instructeurs

Pour fournir des données à la vue pour obtenir la liste de cases à cocher, vous allez utiliser une classe de modèle de vue.

Créer *AssignedCourseData.cs* dans les *SchoolViewModels* dossier et remplacez le code existant par le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

Dans *InstructorsController.cs*, remplacez le HttpGet `Edit` méthode avec le code suivant. Les modifications sont mises en surbrillance.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Le code ajoute un chargement hâtif pour le `Courses` propriété de navigation et appelle la nouvelle `PopulateAssignedCourseData` méthode pour fournir des informations du tableau de case à cocher à l’aide de la `AssignedCourseData` afficher la classe de modèle.

Le code dans la `PopulateAssignedCourseData` méthode lit toutes les entités de cours pour charger une liste de cours à l’aide de la classe de modèle de vue. Pour chaque cours, le code vérifie l’existence de cours dans le formateur `Courses` propriété de navigation. Pour créer l’efficacité des recherches lors de la vérification si un cours est affecté pour le formateur, les cours affectés pour le formateur sont placés dans un `HashSet` collection. Le `Assigned` est définie sur true pour les cours le formateur est assigné à. La vue utilise cette propriété pour déterminer les zones doivent être affichées en tant que vérification sélectionné. Enfin, la liste est passée à la vue dans `ViewData`.

Ensuite, ajoutez le code qui est exécuté lorsque l’utilisateur clique sur **enregistrer**. Remplacez le `EditPost` méthode avec les éléments suivants de code et ajoutez une nouvelle méthode qui met à jour le `Courses` propriété de navigation d’entité Instructor.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

La signature de méthode diffère maintenant le HttpGet `Edit` méthode, de sorte que si le nom de la méthode change de `EditPost` à `Edit`.

Étant donné que la vue ne dispose d’une collection d’entités de cours, le classeur de modèles ne peut pas mettre à jour automatiquement le `CourseAssignments` propriété de navigation. Au lieu d’utiliser le classeur de modèles pour mettre à jour le `CourseAssignments` propriété de navigation, c’est dans la nouvelle `UpdateInstructorCourses` (méthode). Par conséquent, vous devez exclure le `CourseAssignments` propriété à partir de la liaison de modèle. Cela ne nécessite aucune modification pour le code qui appelle `TryUpdateModel` , car vous utilisez la surcharge de la création de listes autorisées et `CourseAssignments` n’est pas dans la liste d’inclusion.

Si aucune vérification de zones ont été sélectionnés, le code dans `UpdateInstructorCourses` initialise le `CourseAssignments` propriété de navigation avec une collection vide et retourne :

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Ensuite, le code effectue une itération sur tous les cours dans la base de données et vérifie chaque cours par rapport à celles actuellement affectées au formateur par rapport à ceux qui ont été sélectionnés dans la vue. Pour faciliter les recherches efficaces, les deux collections ce dernier sont stockées dans `HashSet` objets.

Si la case à cocher pour un cours a été sélectionnée mais que le cours n’est pas dans le `Instructor.CourseAssignments` propriété de navigation, le cours est ajouté à la collection dans la propriété de navigation.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Si la case à cocher pour un cours n’a pas été activée, mais le cours se trouve dans le `Instructor.CourseAssignments` propriété de navigation, le cours est supprimée de la propriété de navigation.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Mettre à jour les vues de formateur

Dans *Views/Instructors/Edit.cshtml*, ajouter un **cours** champ avec un tableau de cases à cocher en ajoutant le code suivant immédiatement après le code le `div` éléments pour le **Office ** champ et avant la `div` , élément pour les **enregistrer** bouton.

<a id="notepad"></a>
> [!NOTE] 
> Lorsque vous collez le code dans Visual Studio, les sauts de ligne changera d’une manière qui interrompt le code.  Appuyez une fois sur Ctrl + Z pour annuler la mise en forme automatique.  Cela permet de résoudre les sauts de ligne afin qu’elles apparaîtront comme vous le voyez ici. La mise en retrait ne doit pas nécessairement être parfait, mais la `@</tr><tr>`, `@:<td>`, `@:</td>`, et `@:</tr>` lignes doivent être chacun sur une ligne unique comme illustré, ou vous obtiendrez une erreur d’exécution. Avec le bloc de code nouveau sélectionné, appuyez sur Tab trois fois pour aligner le nouveau code avec le code existant.

[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Ce code crée une table HTML qui possède trois colonnes. Dans chaque colonne est une case à cocher suivie d’une légende qui est constitué par le numéro et le titre. Toutes les cases à cocher ont le même nom (« selectedCourses »), qui informe le classeur de modèles, ils doivent être traités en tant que groupe. L’attribut de valeur de chaque case à cocher est défini à la valeur de `CourseID`. Lorsque la page est publiée, le classeur de modèles passe un tableau au contrôleur qui se compose de la `CourseID` valeurs pour seulement les cases à cocher qui sont sélectionnées.

Lorsque les cases à cocher sont restitués initialement, ceux qui sont pour les cours affectés pour le formateur archivées attributs, qui sélectionne les (affiche les archivés).

Exécuter l’application, sélectionnez le **instructeurs** onglet, puis cliquez sur **modifier** sur un formateur pour voir les **modifier** page.

![Page de modification du formateur en cours](update-related-data/_static/instructor-edit-courses.png)

Modifier des attributions de cours et cliquez sur Enregistrer. Les modifications que vous apportez sont répercutées sur la page d’Index.

> [!NOTE] 
> L’approche adoptée ici pour modifier les données de cours formateur fonctionne bien quand un nombre limité de cours. Pour les collections qui sont beaucoup plus volumineux, une interface utilisateur différente et une autre méthode de mise à jour serait nécessaires.

## <a name="update-the-delete-page"></a>Mise à jour de la page de suppression

Dans *InstructorsController.cs*, supprimez le `DeleteConfirmed` méthode et insérer le code suivant à la place.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Ce code rend les modifications suivantes :

* Eager fait de chargement pour le `CourseAssignments` propriété de navigation.  Vous devez inclure ou EF ne sont pas informés sur connexes `CourseAssignment` entités et ne sont pas les supprimer.  Pour éviter d’avoir à les lire ici vous pouvez configurer suppression en cascade dans la base de données.

* Si le formateur à supprimer est attribué en tant qu’administrateur d’un service, supprime l’attribution de formateur de ces services.

## <a name="add-office-location-and-courses-to-the-create-page"></a>Ajouter des bureaux et les cours à la page Créer

Dans *InstructorsController.cs*, supprimez le HttpGet et HttpPost `Create` méthodes, puis ajoutez le code suivant à leur place :

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Ce code est similaire à ce que vous avez vu pour la `Edit` méthodes à l’exception qui initialement aucun cours sont sélectionnés. Le HttpGet `Create` les appels de méthode le `PopulateAssignedCourseData` méthode pas, car il peut y avoir des cours sélectionnés mais, dans commande pour fournir une collection vide pour le `foreach` boucle dans la vue (sinon l’afficher le code lève une exception de référence null).

HttpPost `Create` méthode ajoute chaque cours sélectionné pour le `CourseAssignments` propriété de navigation avant qu’il vérifie les erreurs de validation et ajoute le nouveau formateur pour la base de données. Cours sont ajoutés même s’il existe des erreurs de modèle afin que lorsque des erreurs de modèle (par exemple, l’utilisateur indexé une date non valide), et la page s’affiche de nouveau avec un message d’erreur, toutes les sélections de cours qui ont été apportées sont automatiquement restaurées.

Notez que pour pouvoir ajouter des cours à la `CourseAssignments` propriété de navigation que vous avez pour initialiser la propriété comme une collection vide :

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Comme alternative à cette opération dans le code du contrôleur, vous pouvez l’effectuer dans le modèle de formateur en modifiant l’accesseur Get de propriété pour créer automatiquement la collection si elle n’existe pas, comme indiqué dans l’exemple suivant :

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

Si vous modifiez le `CourseAssignments` propriété de cette façon, vous pouvez supprimer le code d’initialisation explicite de propriété dans le contrôleur.

Dans *Views/Instructor/Create.cshtml*, ajoutez une zone de texte emplacement office et cochez les cases pour les cours avant le bouton Envoyer. Comme dans le cas de la page de modification, [corriger la mise en forme si Visual Studio remet en forme le code lorsque vous le collez](#notepad).

[!code-html[Main](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Testez en exécutant l’application et la création d’un formateur. 

## <a name="handling-transactions"></a>La gestion des Transactions

Comme expliqué dans la [didacticiel CRUD](crud.md), Entity Framework implémente implicitement des transactions. Pour les scénarios où vous avez besoin de plus contrôler--par exemple, si vous souhaitez inclure des opérations effectuées en dehors d’Entity Framework dans une transaction, consultez [Transactions](https://docs.microsoft.com/ef/core/saving/transactions).

## <a name="summary"></a>Résumé

Vous avez maintenant terminé l’introduction à l’utilisation des données associées. Dans l’étape suivante du didacticiel, vous verrez comment gérer les conflits d’accès concurrentiel.

>[!div class="step-by-step"]
[Précédent](read-related-data.md)
[Suivant](concurrency.md)  

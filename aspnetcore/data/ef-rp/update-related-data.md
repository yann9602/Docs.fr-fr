---
title: "Les Pages Razor avec EF Core - mettre à jour les données connexes - 7 de 8"
author: rick-anderson
description: "Dans ce didacticiel vous allez mettre à jour les données associées en mettant à jour les propriétés de navigation et les champs de clé étrangère."
ms.author: riande
manager: wpickett
ms.date: 11/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 236589d0202a7f30f1e1a9d69902000fd9a2dd71
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="updating-related-data---ef-core-razor-pages-7-of-8"></a>Mise à jour des données connexes - Pages Razor de noyaux EF (7 sur 8)

Par [Tom Dykstra](https://github.com/tdykstra), et [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

Ce didacticiel illustre la mise à jour de données associées. Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez le [application terminée pour cette étape](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part7).

Les illustrations suivantes montrent certaines pages terminées.

![Page de modification du cours](update-related-data/_static/course-edit.png)
![formateur modifier la page](update-related-data/_static/instructor-edit-courses.png)

Examinez et testez les pages de cours créer et modifier. Créer un nouveau cours. Le service est activée par sa clé primaire (entier), pas son nom. Modifiez le nouveau cours. Lorsque vous avez terminé le test, supprimez le cours de nouveau.

## <a name="create-a-base-class-to-share-common-code"></a>Créer une classe de base pour partager le code commun

Les pages cours/Create et cours/modifier besoin d’une liste de noms de service. Créer le *Pages/Courses/DepartmentNamePageModel.cshtml.cs* classe de base pour les pages créer et modifier :

[!code-csharp[Main](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Le code précédent crée un [SelectList](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) pour contenir la liste des noms de service. Si `selectedDepartment` est spécifié, ce service est sélectionné dans le `SelectList`.

Les classes de modèle de page de créer et modifier doit dériver `DepartmentNamePageModel`.

## <a name="customize-the-courses-pages"></a>Personnaliser les Pages de cours

Lorsqu’une nouvelle entité de cours est créée, il doit avoir une relation à un service existant. Pour ajouter un service lors de la création d’un cours, la classe de base pour créer et modifier contient une liste déroulante pour sélectionner le service. Les jeux de liste déroulante, le `Course.DepartmentID` propriété de clé étrangère (FK). EF Core utilise le `Course.DepartmentID` FK pour charger le `Department` propriété de navigation.

![Créer des cours](update-related-data/_static/ddl.png)

Mettre à jour le modèle de la page Créer avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-)]

Le code précédent :

* Dérive de `DepartmentNamePageModel`.
* Utilise `TryUpdateModelAsync` pour empêcher [sur-validation](xref:data/ef-rp/crud#overposting).
* Remplace `ViewData["DepartmentID"]` avec `DepartmentNameSL` (à partir de la classe de base).

`ViewData["DepartmentID"]`est remplacé par fortement typé `DepartmentNameSL`. Modèles fortement typés sont plutôt que faiblement typé. Pour plus d’informations, consultez [faiblement typé (ViewData et ViewBag) de données](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Mise à jour de la page de création de cours

Mise à jour *Pages/Courses/Create.cshtml* par le balisage suivant :

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Le balisage précédent apporte les modifications suivantes :

* Modifier la légende de **DepartmentID** à **service**.
* Remplace `"ViewBag.DepartmentID"` avec `DepartmentNameSL` (à partir de la classe de base).
* Ajoute l’option « Sélectionner département ». Cette modification rend « Sélectionnez département » plutôt que le premier service.
* Ajoute un message de validation lorsque le service n’est pas sélectionné.

La Page Razor utilise le [sélectionner une assistance de balise](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Tester la page de création. La page Créer affiche le nom du service plutôt que l’ID de service.

### <a name="update-the-courses-edit-page"></a>Mettre à jour de la page Modifier le cours.

Mettre à jour le modèle de page de modification avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-)]

Les modifications sont semblables à celles effectuées dans le modèle de page de création. Dans le code précédent, `PopulateDepartmentsDropDownList` transmet l’ID de service, ce qui permet de sélectionner le département spécifié dans la liste déroulante.

Mise à jour *Pages/Courses/Edit.cshtml* par le balisage suivant :

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Le balisage précédent apporte les modifications suivantes :

* Affiche l’identificateur du cours. En général la clé primaire (PK) d’une entité n’est pas affichée. Clés primaires sont généralement sans signification pour les utilisateurs. Dans ce cas, la clé est le nombre de cours.
* Modifier la légende de **DepartmentID** à **service**.
* Remplace `"ViewBag.DepartmentID"` avec `DepartmentNameSL` (à partir de la classe de base).
* Ajoute l’option « Sélectionner département ». Cette modification rend « Sélectionnez département » plutôt que le premier service.
* Ajoute un message de validation lorsque le service n’est pas sélectionné.

La page contient un champ masqué (`<input type="hidden">`) pour le nombre de cours. Ajout d’un `<label>` d’assistance avec la balise `asp-for="Course.CourseID"` n’élimine la nécessité pour le champ masqué. `<input type="hidden">`est requis pour le nombre de cours à inclure dans les données publiées lorsque l’utilisateur clique sur **enregistrer**.

Tester le code mis à jour. Créer, modifier et supprimer un cours.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Ajouter AsNoTracking les détails et supprimer des modèles de page

[AsNoTracking](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) peut améliorer les performances lorsque le suivi n’est pas nécessaire. Ajouter `AsNoTracking` au modèle de page de suppression et de détails. Le code suivant montre le modèle mis à jour de la page Supprimer :

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Mise à jour la `OnGetAsync` méthode dans le *Pages/Courses/Details.cshtml.cs* fichier :

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Modifier les pages Delete et détails

Mise à jour de la page Supprimer un Razor avec le balisage suivant :

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Apporter les mêmes modifications à la page de détails.

### <a name="test-the-course-pages"></a>Tester les pages de cours

Test de créer, modifier, détails et supprimer.

## <a name="update-the-instructor-pages"></a>Mettre à jour les pages de formateur

Les sections suivantes mettre à jour les pages de formateur.

### <a name="add-office-location"></a>Ajouter l’emplacement du bureau

Lorsque vous modifiez un enregistrement du formateur, vous pouvez mettre à jour l’attribution du formateur office. Le `Instructor` entité a une relation un-à-zéro-ou-un avec le `OfficeAssignment` entité. Le code de formateur doit gérer :

* Si l’utilisateur efface l’attribution d’office, supprimez le `OfficeAssignment` entité.
* Si l’utilisateur entre une attribution et il était vide, créez un nouveau `OfficeAssignment` entité.
* Si l’utilisateur modifie l’affectation d’office, mettre à jour le `OfficeAssignment` entité.

Mettre à jour le modèle de page instructeurs modifier avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-)]

Le code précédent :

- Obtient les valeurs combinées `Instructor` entité à partir de la base de données à l’aide d’un chargement hâtif pour le `OfficeAssignment` propriété de navigation.
- Met à jour récupérées `Instructor` entité avec les valeurs de classeur de modèles. `TryUpdateModel`empêche [sur-validation](xref:data/ef-rp/crud#overposting).
- Si l’emplacement du bureau est vide, définit `Instructor.OfficeAssignment` avec la valeur null. Lorsque `Instructor.OfficeAssignment` est null, la ligne correspondante dans la `OfficeAssignment` table est supprimée.

### <a name="update-the-instructor-edit-page"></a>Mettre à jour de la page Modifier le formateur

Mise à jour *Pages/Instructors/Edit.cshtml* avec l’emplacement du bureau :

[!code-cshtml[Main](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Vérifiez que vous pouvez modifier un emplacement office de formateurs.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Ajouter des affectations de cours à la page Modifier le formateur

Instructeurs enseigner à n’importe quel nombre de cours. Dans cette section, vous ajoutez la possibilité de modifier les affectations de cours. L’illustration suivante montre le formateur de mise à jour de page de modification :

![Page de modification du formateur en cours](update-related-data/_static/instructor-edit-courses.png)

`Course`et `Instructor` a une relation plusieurs-à-plusieurs. Pour ajouter et supprimer des relations, vous ajoutez et supprimez des entités à partir de la `CourseAssignments` joindre le jeu d’entités.

Cases à cocher Activer les modifications aux cours à que formateur est assigné. Une case à cocher s’affiche pour chaque cours dans la base de données. Cours auquel appartient le formateur sont vérifiées. L’utilisateur peut sélectionner ou désactivez les cases à cocher pour modifier les affectations de cours. Si le nombre de cours ont été nettement supérieur :

* Vous devrez sans doute utiliser une interface utilisateur différente pour afficher les cours.
* La méthode de manipulation d’une entité de jointure pour créer ou supprimer des relations ne changera.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Ajouter des classes pour prendre en charge créer et modifier des pages de formateur

Créer *SchoolViewModels/AssignedCourseData.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

La `AssignedCourseData` classe contient des données pour créer les cases à cocher pour les cours attribués par un formateur.

Créer le *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* classe de base :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

Le `InstructorCoursesPageModel` est la classe de base utilisée pour la modifier et créer des modèles de page. `PopulateAssignedCourseData`lit tous les `Course` entités pour remplir `AssignedCourseDataList`. Pour chaque cours, le code définit la `CourseID`, titre et s’il faut ou non le formateur est affecté au cours. A [HashSet](https://docs.microsoft.com/dotnet/api/system.collections.generic.hashset-1) est utilisé pour créer des recherches efficaces.

### <a name="instructors-edit-page-model"></a>Modifier le modèle de page instructeurs

Mettre à jour le modèle de page de modification de formateur par le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-)]

Le code précédent gère les changements d’affectation d’office.

Mettre à jour la vue Razor formateur :

[!code-cshtml[Main](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Lorsque vous collez le code dans Visual Studio, les sauts de ligne sont modifiées d’une manière qui interrompt le code. Appuyez une fois sur Ctrl + Z pour annuler la mise en forme automatique. CTRL + Z résout les sauts de ligne afin qu’elles apparaîtront comme vous le voyez ici. La mise en retrait ne doit pas nécessairement être parfait, mais la `@</tr><tr>`, `@:<td>`, `@:</td>`, et `@:</tr>` lignes doivent chacune être sur une seule ligne comme indiqué. Avec le bloc de code nouveau sélectionné, appuyez sur Tab trois fois pour aligner le nouveau code avec le code existant. Voter pour ou vérifier l’état de ce bogue [ce lien](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Le code précédent crée une table HTML qui possède trois colonnes. Chaque colonne possède une case à cocher et une légende contenant le numéro et le titre. Toutes les cases à cocher ont le même nom (« selectedCourses »). En utilisant le même nom informe le classeur de modèles pour les traiter en tant que groupe. L’attribut value de chaque case à cocher a la valeur `CourseID`. Lorsque la page est publiée, le classeur de modèles passe un tableau comprenant le `CourseID` valeurs pour les cases à cocher sont sélectionnées.

Lorsque les cases à cocher sont restitués initialement, cours affectés pour le formateur archivées par attributs.

Exécutez l’application et tester la page de modification des instructeurs mis à jour. Modifier des attributions de cours. Les modifications sont répercutées sur la page d’Index.

Remarque : L’approche adoptée ici pour modifier les données de cours formateur fonctionne bien quand un nombre limité de cours. Pour les collections qui sont beaucoup plus volumineux, une interface utilisateur différente et une autre méthode de mise à jour serait plus utile et plus efficace.

### <a name="update-the-instructors-create-page"></a>Mise à jour de la page de création de formateurs

Mettre à jour le modèle de page de création de formateur par le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Le code précédent est similaire à la *Pages/Instructors/Edit.cshtml.cs* code.

Mise à jour de la page de création de Razor formateur par le balisage suivant :

[!code-cshtml[Main](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Tester la page de création de formateur.

## <a name="update-the-delete-page"></a>Mise à jour de la page de suppression

Mettre à jour le modèle de page de suppression par le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-)]

Le code précédent apporte les modifications suivantes :

* Utilise un chargement hâtif pour le `CourseAssignments` propriété de navigation. `CourseAssignments`doit être inclus ou ils ne sont pas supprimés lorsque le formateur est supprimé. Pour éviter d’avoir à les lire, configurez la suppression en cascade dans la base de données.

* Si le formateur à supprimer est attribué en tant qu’administrateur d’un service, supprime l’attribution de formateur de ces services.

>[!div class="step-by-step"]
[Précédent](xref:data/ef-rp/read-related-data)
[Suivant](xref:data/ef-rp/concurrency)

---
uid: mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
title: "Mise à jour des données associées avec Entity Framework dans une Application ASP.NET MVC (partie 6 sur 10) | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/30/2013
ms.topic: article
ms.assetid: 7871dc05-2750-470f-8b4c-3a52511949bc
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 2ca76364a2e9a71dc92644bd579345ae3c304a69
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="updating-related-data-with-the-entity-framework-in-an-aspnet-mvc-application-6-of-10"></a>Mise à jour des données associées avec Entity Framework dans une Application ASP.NET MVC (partie 6 sur 10)
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/Getting-Started-with-dd0e2ed8)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio 2012. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md). Vous pouvez démarrer la série de didacticiels à partir du début ou [télécharger un projet de démarrage pour ce chapitre](building-the-ef5-mvc4-chapter-downloads.md) et Démarrer ici.
> 
> > [!NOTE] 
> > 
> > Si vous rencontrez un problème que vous ne pouvez pas résoudre, [télécharger le chapitre terminé](building-the-ef5-mvc4-chapter-downloads.md) et essayez de reproduire le problème. En règle générale, vous trouverez la solution au problème en comparant votre code pour le code terminé. Pour certaines erreurs courantes et comment les résoudre, consultez [erreurs et des solutions de contournement.](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#errors)


Dans le didacticiel précédent, vous avez affiché les données associées ; Dans ce didacticiel, vous allez mettre à jour les données associées. Pour la plupart des relations, cela est possible en mettant à jour les champs clés étrangères. Pour les relations plusieurs-à-plusieurs, Entity Framework n’expose la table de jointure directement, donc vous devez explicitement ajouter et supprimer des entités vers et depuis les propriétés de navigation.

Les illustrations suivantes montrent les pages que vous allez utiliser.

![Course_create_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Instructor_edit_page_with_courses](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a>Personnaliser le créer et modifier des Pages pour les cours

Lorsqu’une nouvelle entité de cours est créée, il doit avoir une relation à un service existant. Pour faciliter ce processus, le code de modèle généré automatiquement inclut des méthodes de contrôleur et de créer et modifier des vues qui incluent une liste déroulante pour sélectionner le service. Les jeux de liste déroulante, le `Course.DepartmentID` une propriété de clé étrangère, et c’est tout Entity Framework a besoin pour charger le `Department` propriété de navigation avec approprié `Department` entité. Vous allez utiliser le code de modèle généré automatiquement, mais modifier légèrement pour ajouter la gestion des erreurs et de trier la liste déroulante.

Dans *CourseController.cs*, supprimez les quatre `Edit` et `Create` méthodes et les remplacer par le code suivant :

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=3,10,13-14,21-27,34,41,44-45,52-58,62-68)]

Le `PopulateDepartmentsDropDownList` Obtient une liste de tous les départements sont triés par nom de méthode, crée un `SelectList` collection pour obtenir la liste déroulante et passe la collection à l’affichage dans un `ViewBag` propriété. La méthode accepte le paramètre facultatif `selectedDepartment` paramètre qui permet au code appelant spécifier l’élément est sélectionné lors du rendu de la liste déroulante. La vue passe le nom du `DepartmentID` à [le `DropDownList` assistance](../working-with-the-dropdownlist-box-and-jquery/using-the-dropdownlist-helper-with-aspnet-mvc.md), et l’application d’assistance puis sache qu’il peut pour rechercher dans le `ViewBag` de l’objet pour un `SelectList` nommé `DepartmentID`.

Le `HttpGet` `Create` les appels de méthode le `PopulateDepartmentsDropDownList` méthode sans définir l’élément sélectionné, car de formation le service n’est pas établi encore :

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Le `HttpGet` `Edit` méthode définit l’élément sélectionné, en fonction de l’ID du service qui est déjà affecté au cours en cours de modification :

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

Le `HttpPost` méthodes pour les deux `Create` et `Edit` également inclure du code qui définit l’élément sélectionné quand ils réafficher la page après une erreur :

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

Ce code garantit que lorsque la page s’affiche de nouveau pour afficher le message d’erreur, quel que soit le service a été sélectionné reste sélectionné.

Dans *Views\Course\Create.cshtml*, ajoutez le code en surbrillance pour créer un nouveau champ de numéro de cours avant la **titre** champ. Comme expliqué dans un didacticiel antérieur, les champs de clé primaire ne sont pas structurés par défaut, mais cette clé primaire n’est significative, donc vous souhaitez que l’utilisateur à entrer la valeur de clé.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml?highlight=17-23)]

Dans *Views\Course\Edit.cshtml*, *Views\Course\Delete.cshtml*, et *Views\Course\Details.cshtml*, ajoutez un champ de numéro de cours avant la **titre**  champ. Comme il est la clé primaire, il est affiché, mais il ne peut pas être modifié.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml)]

Exécutez le **créer** page (afficher la page d’Index de cours et cliquez sur **créer un nouveau**), puis entrez les données de formation :

![Course_create_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

Cliquez sur **Créer**. La page d’Index de cours s’affiche avec le cours de nouveau ajouté à la liste. Le nom du service dans la liste de page d’Index provient de la propriété de navigation, indiquant que la relation a été établie correctement.

![Course_Index_page_showing_new_course](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

Exécutez le **modifier** page (afficher la page d’Index de cours et cliquez sur **modifier** sur un cours).

![Course_edit_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)

Modifier les données sur la page, cliquez sur **enregistrer**. La page d’Index de cours s’affiche avec les données de cours mis à jour.

## <a name="adding-an-edit-page-for-instructors"></a>Ajout d’une Page de modification pour les formateurs

Lorsque vous modifiez un enregistrement du formateur, que vous souhaitez être en mesure de mettre à jour l’attribution du formateur office. Le `Instructor` entité a une relation un-à-zéro-ou-un avec le `OfficeAssignment` entité, ce qui signifie que vous devez gérer les situations suivantes :

- Si l’utilisateur efface l’attribution d’office et il avait une valeur, vous devez supprimer et le `OfficeAssignment` entité.
- Si l’utilisateur entre une valeur d’assignation office et il a été initialement vide, vous devez créer un nouveau `OfficeAssignment` entité.
- Si l’utilisateur modifie la valeur d’une assignation d’office, vous devez modifier la valeur dans un existant `OfficeAssignment` entité.

Ouvrez *InstructorController.cs* et examinez le `HttpGet` `Edit` méthode :

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

Le code de modèle généré automatiquement ici n’est pas ce que vous souhaitez. Il configure des données d’une liste déroulante, mais vous ce dont vous avez besoin est une zone de texte. Remplacez cette méthode par le code suivant :

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

Ce code supprime le `ViewBag` instruction et ajoute un chargement hâtif associé au `OfficeAssignment` entité. Impossible d’effectuer un chargement hâtif avec la `Find` (méthode), donc la `Where` et `Single` méthodes sont utilisées à la place pour sélectionner le formateur.

Remplacez le `HttpPost` `Edit` méthode avec le code suivant. qui gère les mises à jour de l’attribution d’office :

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

Le code effectue les opérations suivantes :

- Obtient les valeurs combinées `Instructor` entité à partir de la base de données à l’aide d’un chargement hâtif pour le `OfficeAssignment` propriété de navigation. Il est identique à ce que vous avez fait le `HttpGet` `Edit` (méthode).
- Met à jour récupérées `Instructor` entité avec les valeurs de classeur de modèles. Le [TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.108).aspx) vous permet de surcharge utilisée *liste blanche* les propriétés que vous souhaitez inclure. Cela empêche la validation excessive, comme expliqué dans [le deuxième didacticiel](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md).

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]
- Si l’emplacement du bureau est vide, définit le `Instructor.OfficeAssignment` propriété NULL afin que la ligne correspondante dans la `OfficeAssignment` table va être supprimée.

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]
- Enregistre les modifications dans la base de données.

Dans *Views\Instructor\Edit.cshtml*, après le `div` éléments pour le **Date d’embauche** champ, ajouter un nouveau champ pour la modification de l’emplacement du bureau :

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml)]

Exécution de la page (sélectionnez le **instructeurs** onglet, puis cliquez sur **modifier** sur un formateur). Modifier la **bureaux** et cliquez sur **enregistrer**.

![Changing_the_office_location](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

## <a name="adding-course-assignments-to-the-instructor-edit-page"></a>Page Modifier les affectations de cours Ajout pour le formateur

Instructeurs enseigner à n’importe quel nombre de cours. Maintenant, vous allez améliorer la page Modifier le formateur en ajoutant la possibilité de modifier les affectations de cours à l’aide d’un groupe de cases à cocher, comme indiqué dans la capture d’écran suivante :

![Instructor_edit_page_with_courses](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

La relation entre la `Course` et `Instructor` entités est plusieurs-à-plusieurs, ce qui signifie que vous n’avez pas un accès direct à la table de jointure. Au lieu de cela, vous allez ajouter et supprimer des entités vers et depuis le `Instructor.Courses` propriété de navigation.

Formateur de l’interface utilisateur qui permet de modifier les cours est assigné à est un groupe de cases à cocher. Une case à cocher pour chaque cours dans la base de données s’affiche, et celles qui le formateur est actuellement attribué aux sont sélectionnés. L’utilisateur peut sélectionner ou désactivez les cases à cocher pour modifier les affectations de cours. Si le nombre de cours ont été beaucoup plus important, vous souhaiterez probablement utiliser une autre méthode de présentation des données dans la vue, mais que vous utiliseriez la même méthode de manipulation des propriétés de navigation pour pouvoir créer ou supprimer des relations.

Pour fournir des données à la vue pour obtenir la liste de cases à cocher, vous allez utiliser une classe de modèle de vue. Créer *AssignedCourseData.cs* dans les *ViewModel* dossier et remplacez le code existant par le code suivant :

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

Dans *InstructorController.cs*, remplacez le `HttpGet` `Edit` méthode avec le code suivant. Les modifications sont mises en surbrillance.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs?highlight=5,8,12-27)]

Le code ajoute un chargement hâtif pour le `Courses` propriété de navigation et appelle la nouvelle `PopulateAssignedCourseData` méthode pour fournir des informations du tableau de case à cocher à l’aide de la `AssignedCourseData` afficher la classe de modèle.

Le code dans le `PopulateAssignedCourseData` méthode lit à travers toutes les `Course` entités pour charger une liste de cours à l’aide de l’affichage de classe de modèle. Pour chaque cours, le code vérifie l’existence de cours dans le formateur `Courses` propriété de navigation. Pour créer l’efficacité des recherches lors de la vérification si un cours est affecté pour le formateur, les cours affectés pour le formateur sont placés dans un [HashSet](https://msdn.microsoft.com/library/bb359438.aspx) collection. Le `Assigned` est définie sur `true` pour les cours le formateur est affecté. La vue utilise cette propriété pour déterminer les zones doivent être affichées en tant que vérification sélectionné. Enfin, la liste est passée à l’affichage dans un `ViewBag` propriété.

Ensuite, ajoutez le code qui est exécuté lorsque l’utilisateur clique sur **enregistrer**. Remplacez le `HttpPost` `Edit` méthode avec le code suivant, qui appelle une méthode qui met à jour la `Courses` propriété de navigation de la `Instructor` entité. Les modifications sont mises en surbrillance.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs?highlight=3,7,20,33,37-65)]

Puisque la vue n’a pas une collection de `Course` entités, le binder de modèle ne peut pas mettre à jour automatiquement le `Courses` propriété de navigation. Au lieu d’utiliser le classeur de modèles pour mettre à jour la propriété de navigation du cours, vous allez faire dans la nouvelle `UpdateInstructorCourses` (méthode). Par conséquent, vous devez exclure le `Courses` propriété à partir de la liaison de modèle. Cela ne nécessite aucune modification pour le code qui appelle [TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.98).aspx) , car vous utilisez le *liste approuvées* de surcharge et `Courses` n’est pas dans la liste d’inclusion.

Si aucune vérification de zones ont été sélectionnés, le code dans `UpdateInstructorCourses` initialise le `Courses` propriété de navigation avec une collection vide :

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs)]

Ensuite, le code effectue une itération sur tous les cours dans la base de données et vérifie chaque cours par rapport à celles actuellement affectées au formateur par rapport à ceux qui ont été sélectionnés dans la vue. Pour faciliter les recherches efficaces, les deux collections ce dernier sont stockées dans `HashSet` objets.

Si la case à cocher pour un cours a été sélectionnée mais que le cours n’est pas dans le `Instructor.Courses` propriété de navigation, le cours est ajouté à la collection dans la propriété de navigation.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cs)]

Si la case à cocher pour un cours n’a pas été activée, mais le cours se trouve dans le `Instructor.Courses` propriété de navigation, le cours est supprimée de la propriété de navigation.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

Dans *Views\Instructor\Edit.cshtml*, ajoutez un **cours** champ avec un tableau de cases à cocher en ajoutant le code suivant mis en surbrillance code immédiatement après le `div` éléments pour le `OfficeAssignment` champ :

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml?highlight=51-73)]

Ce code crée une table HTML qui possède trois colonnes. Dans chaque colonne est une case à cocher suivie d’une légende qui est constitué par le numéro et le titre. Toutes les cases à cocher ont le même nom (« selectedCourses »), qui informe le classeur de modèles, ils doivent être traités en tant que groupe. Le `value` attribut de chaque case à cocher est défini à la valeur de `CourseID.` lors de la validation de la page, le classeur de modèles passe un tableau au contrôleur qui se compose de la `CourseID` valeurs pour seulement les cases à cocher qui sont sélectionnées.

Lorsque les cases à cocher sont restitués initialement, ceux qui sont pour les cours affectés pour le formateur ont `checked` attributs, qui sélectionne les (affiche les archivés).

Après avoir modifié les affectations des cours, que vous souhaitez être en mesure de vérifier les modifications lorsque le site revient à la `Index` page. Par conséquent, vous devez ajouter une colonne à la table dans la page. Dans ce cas vous n’avez pas besoin d’utiliser le `ViewBag` de l’objet, car les informations que vous voulez afficher soient déjà la `Courses` propriété de navigation de la `Instructor` entité que vous passez à la page que le modèle.

Dans *Views\Instructor\Index.cshtml*, ajoutez un **cours** titre qui suit immédiatement la **Office** titre, comme indiqué dans l’exemple suivant :

[!code-html[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.html?highlight=7)]

Ajoutez ensuite une nouvelle cellule de détail qui suit immédiatement la cellule de détail d’emplacement office :

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cshtml?highlight=19,50-57)]

Exécutez le **formateur Index** page pour consulter les cours affectés à chaque formateur :

![Instructor_index_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image8.png)

Cliquez sur **modifier** sur un formateur pour afficher la page de modification.

![Instructor_edit_page_with_courses](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

Modifier des attributions de cours et cliquez sur **enregistrer**. Les modifications que vous apportez sont répercutées sur la page d’Index.

 Remarque : L’approche adoptée pour modifier les données de cours formateur fonctionne bien quand un nombre limité de cours. Pour les collections qui sont beaucoup plus volumineux, une interface utilisateur différente et une autre méthode de mise à jour serait nécessaires.  
 

## <a name="update-the-delete-method"></a>Mise à jour de la méthode de suppression

Modifiez le code dans la méthode HttpPost Delete afin de l’enregistrement de l’attribution d’office (le cas échéant) est supprimé lorsque le formateur est supprimé :

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs?highlight=6,10)]


Si vous essayez de supprimer un formateur qui est attribué à un service en tant qu’administrateur, vous obtenez une erreur d’intégrité référentielle. Consultez [la version actuelle de ce didacticiel](../../getting-started/getting-started-with-ef-using-mvc/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) pour du code supplémentaire qui va supprimer automatiquement le formateur à partir de n’importe quel service où le formateur est affecté en tant qu’administrateur.

## <a name="summary"></a>Récapitulatif

Vous avez maintenant terminé cette introduction à l’utilisation des données associées. Jusqu'à présent dans ces didacticiels, vous avez effectué une plage complète des opérations, mais vous n’avez pas encore traitées avec des problèmes d’accès concurrentiel. Le didacticiel suivant sera Présentez le sujet de l’accès concurrentiel, expliquent les options pour la traiter et ajouter la gestion au code CRUD que vous avez déjà écrit pour un type d’entité concurrentielle.

Vous trouverez des liens vers d’autres ressources Entity Framework, à la fin de [le dernier didacticiel de cette série](advanced-entity-framework-scenarios-for-an-mvc-web-application.md).

>[!div class="step-by-step"]
[Précédent](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)
[Suivant](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)

---
title: "Core ASP.NET MVC avec EF Core - lire les données associées - 6 sur 10"
author: tdykstra
description: "Dans ce didacticiel, vous allez lire et afficher les données associées, autrement dit, les données Entity Framework charge dans les propriétés de navigation."
keywords: "ASP.NET Core, Entity Framework Core, les données associées, jointures"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 71fec30f-8ea7-4ca8-96e3-d2e26c5be44e
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 37613d974fdf1766b187cdd05efc926ecc6a351b
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="reading-related-data---ef-core-with-aspnet-core-mvc-tutorial-6-of-10"></a>Lecture liés de données - Core EF avec le didacticiel ASP.NET Core MVC (partie 6 sur 10)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).

Dans le didacticiel précédent, vous terminé le modèle de données School. Dans ce didacticiel, vous allez lire et afficher les données associées, autrement dit, les données Entity Framework charge dans les propriétés de navigation.

Les illustrations suivantes montrent les pages que vous allez utiliser.

![Page d’Index de cours](read-related-data/_static/courses-index.png)

![Page d’Index instructeurs](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Chargement hâtif, explicit et différé de données connexes

Voici plusieurs façons qu’un logiciel de mappage relationnel objet (ORM) comme Entity Framework peut charger des données connexes dans les propriétés de navigation d’une entité :

* Chargement hâtif. Lors de la lecture de l’entité, les données associées sont récupérées en même temps. Cela entraîne généralement une requête de jointure unique qui extrait toutes les données que nécessaire. Vous spécifiez un chargement hâtif dans Entity Framework Core à l’aide de la `Include` et `ThenInclude` méthodes.

  ![Exemple de chargement hâtif](read-related-data/_static/eager-loading.png)

  Vous pouvez récupérer des données dans des requêtes distinctes et EF » résout « les propriétés de navigation.  Autrement dit, EF ajoute automatiquement les entités récupérées séparément auquel ils appartiennent dans les propriétés de navigation d’entités précédemment récupérées. Pour la requête qui Récupère les données associées, vous pouvez utiliser la `Load` méthode au lieu d’une méthode renvoyant une liste ou un objet, tel que `ToList` ou `Single`.

  ![Exemple de requêtes distinctes](read-related-data/_static/separate-queries.png)

* Chargement explicite. Lors de la lecture de l’entité est tout d’abord, les données associées n’est pas récupérées. Vous écrivez un programme qui Récupère les données associées si elle est nécessaire. Comme dans le cas de chargement hâtif avec des requêtes distinctes, explicite le chargement des résultats dans plusieurs requêtes envoyées à la base de données. La différence est que, avec le chargement explicite, le code spécifie les propriétés de navigation doivent être chargées. Dans Entity Framework Core 1.1, vous pouvez utiliser la `Load` méthode pour effectuer le chargement explicite. Exemple :

  ![Exemple de chargement explicite](read-related-data/_static/explicit-loading.png)

* Chargement différé. Lors de la lecture de l’entité est tout d’abord, les données associées n’est pas récupérées. Toutefois, la première fois que vous tentez d’accéder à une propriété de navigation, les données requises pour cette propriété de navigation sont automatiquement récupérées. Une requête est envoyée à la base de données chaque fois que vous essayez d’obtenir des données à partir d’une propriété de navigation pour la première fois. Entity Framework Core 1.0 ne prend pas en charge le chargement différé.

### <a name="performance-considerations"></a>Considérations sur les performances

Si vous savez que vous avez besoin des données associées pour toutes les entités récupérées, le chargement hâtif souvent offre des performances optimales, car une seule requête envoyée à la base de données est généralement plus efficace que les requêtes distinctes pour chaque entité récupérée. Par exemple, supposons que chaque service possède dix cours. Chargement hâtif de toutes les données connexes entraînerait simplement une requête unique (jointure) et un seul aller-retour à la base de données. Une requête distincte pour les cours pour chaque service entraînerait onze les allers-retours vers la base de données. Les allers-retours supplémentaires à la base de données sont particulièrement nuits aux performances lors de la latence est élevée.

En revanche, dans certains scénarios, des requêtes distinctes est plus efficace. Chargement hâtif de toutes les données connexes dans une requête peut entraîner une jointure très complexe à générer, lequel SQL Server ne peut pas traiter efficacement. Ou, si vous avez besoin d’accéder aux propriétés de navigation d’une entité uniquement pour un sous-ensemble d’un jeu d’entités que vous traitez, des requêtes distinctes peuvent être plus performantes, car un chargement hâtif de tous les éléments en amont permet de récupérer plus de données que vous avez besoin. Si les performances sont critiques, il est préférable de tester les performances les deux méthodes pour effectuer le meilleur choix.

## <a name="create-a-courses-page-that-displays-department-name"></a>Créer une page de cours qui affiche le nom du service

L’entité de cours inclut une propriété de navigation qui contient l’entité de service du service auquel appartient le cours. Pour afficher le nom du service affecté dans une liste de cours, vous devez obtenir la propriété de nom de l’entité du service qui se trouve dans le `Course.Department` propriété de navigation.

Créer un contrôleur nommé CoursesController pour le type d’entité de cours, utilisant les mêmes options pour le **contrôleur MVC avec vues, utilisant Entity Framework** scaffolder que vous avez utilisé précédemment pour le contrôleur d’étudiants, comme indiqué dans les illustration suivante :

![Ajouter un contrôleur de cours](read-related-data/_static/add-courses-controller.png)

Ouvrez *CoursesController.cs* et examinez le `Index` (méthode). La génération de modèles automatique a spécifié un chargement hâtif pour le `Department` propriété de navigation à l’aide de la `Include` (méthode).

Remplacez le `Index` méthode avec le code suivant qui utilise un nom plus approprié pour le `IQueryable` qui retourne des entités de cours (`courses` au lieu de `schoolContext`) :

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

Ouvrez *Views/Courses/Index.cshtml* et remplacez le code de modèle par le code suivant. Les modifications sont mises en surbrillance :

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Vous avez effectué les modifications suivantes au code de modèle généré automatiquement :

* Modifié le titre de l’Index au cours.

* Ajouter un **nombre** colonne qui affiche le `CourseID` valeur de propriété. Par défaut, les clés primaires ne sont pas structurés, car ceux-ci ne sont généralement pas de sens pour les utilisateurs finaux. Toutefois, dans ce cas, la clé primaire est significative et que vous souhaitez afficher.

* Modifié le **service** colonne pour afficher le nom du service. Le code affiche le `Name` propriété de l’entité de service qui est chargée dans le `Department` propriété de navigation :

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Exécutez l’application et sélectionnez le **cours** onglet pour afficher la liste avec les noms de service.

![Page d’Index de cours](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Créer une page de formateurs qui affiche des cours et des inscriptions

Dans cette section, vous allez créer un contrôleur et une vue pour l’entité Instructor afin d’afficher la page de formateurs :

![Page d’Index instructeurs](read-related-data/_static/instructors-index.png)

Cette page lit et affiche les données connexes comme suit :

* La liste des instructeurs affiche les données connexes à partir de l’entité OfficeAssignment. Les entités du formateur et OfficeAssignment sont dans une relation un-à-zéro-ou-un. Vous allez utiliser un chargement hâtif des entités OfficeAssignment. Comme expliqué précédemment, le chargement hâtif est généralement plus efficace lorsque vous avez besoin des données associées pour toutes les lignes extraites de la table primaire. Dans ce cas, vous souhaitez afficher les affectations d’office pour tous les formateurs affichées.

* Lorsque l’utilisateur sélectionne un formateur, les entités de cours associées sont affichent. Les entités du formateur et de cours sont dans une relation plusieurs-à-plusieurs. Vous allez utiliser un chargement hâtif pour les entités de cours et de leurs entités de service associées. Dans ce cas, les requêtes distinctes peuvent être plus efficaces, car vous avez besoin de cours uniquement pour l’enseignant sélectionné. Toutefois, cet exemple montre comment utiliser un chargement hâtif pour les propriétés de navigation au sein des entités qui sont elles-mêmes des propriétés de navigation.

* Lorsque l’utilisateur sélectionne un cours, les données associées dans le jeu d’entités inscriptions s’affiche. Les entités de l’inscription et le cours sont dans une relation un-à-plusieurs. Vous allez utiliser des requêtes distinctes pour les entités de l’inscription et de leurs entités étudiant associées.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Créer un modèle d’affichage pour l’affichage de l’Index de formateur

La page de formateurs affiche les données de trois tables différentes. Par conséquent, vous allez créer un modèle d’affichage qui comprend trois propriétés, chaque contenant les données pour l’une des tables.

Dans le *SchoolViewModels* dossier, créez *InstructorIndexData.cs* et remplacez le code existant par le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Créer le contrôleur du formateur et des vues

Créer un contrôleur de formateurs avec des actions de lecture/écriture EF comme indiqué dans l’illustration suivante :

![Ajouter un contrôleur de formateurs](read-related-data/_static/add-instructors-controller.png)

Ouvrez *InstructorsController.cs* et ajouter une à l’aide de l’instruction pour l’espace de noms ViewModel :

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Remplacez la méthode d’Index par le code suivant pour effectuer un chargement hâtif des données associées et le placer dans le modèle d’affichage.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

La méthode accepte les données d’itinéraire facultatif (`id`) et un paramètre de chaîne de requête (`courseID`) qui fournissent les valeurs d’ID de l’enseignant sélectionné et le cours sélectionné. Les paramètres sont fournis par le **sélectionnez** des liens hypertexte dans la page.

Le code commence par la création d’une instance du modèle de vue et donne la liste des formateurs. Le code spécifie un chargement hâtif pour le `Instructor.OfficeAssignment` et `Instructor.CourseAssignments` propriétés de navigation. Dans le `CourseAssignments` propriété, le `Course` propriété est chargé et dans ce, le `Enrollments` et `Department` propriétés sont chargés et dans chaque `Enrollment` entité le `Student` propriété est chargée.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Étant donné que la vue nécessite toujours l’entité OfficeAssignment, il est plus efficace d’extraire que dans la même requête. Les entités de cours sont requises lorsque le formateur est sélectionné dans la page web, par conséquent, une seule requête est meilleure que plusieurs requêtes uniquement si la page s’affiche plus souvent avec un cours sélectionné que sans.

Le code se répète `CourseAssignments` et `Course` , car vous avez besoin de deux propriétés de `Course`. La première chaîne de `ThenInclude` appelle Obtient `CourseAssignment.Course`, `Course.Enrollments`, et `Enrollment.Student`.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

À ce stade dans le code, un autre `ThenInclude` serait pour les propriétés de navigation de `Student`, ce qui vous n’avez pas besoin. Contrairement à l’appel `Include` recommence avec `Instructor` propriétés, donc vous devez parcourir la chaîne à nouveau, cette fois en spécifiant `Course.Department` au lieu de `Course.Enrollments`.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

Le code suivant s’exécute lorsqu’un formateur a été sélectionné. Le formateur sélectionné est récupéré à partir de la liste des formateurs dans le modèle d’affichage. Le modèle d’affichage `Courses` propriété est ensuite chargée avec les entités de cours à partir de ce formateur `CourseAssignments` propriété de navigation.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

Le `Where` méthode retourne une collection, mais dans ce cas les critères passé à cette méthode entraînent uniquement une seule entité Instructor qui est retournée. Le `Single` méthode convertit la collection en une seule entité Instructor, ce qui vous permet d’accéder à cette entité `CourseAssignments` propriété. Le `CourseAssignments` propriété contient `CourseAssignment` entités, à partir de laquelle vous souhaitez que le connexes `Course` entités.

Vous utilisez la `Single` méthode sur une collection lorsque vous savez que la collection aura qu’un seul élément. La seule méthode lève une exception si la collection passée à ce dernier est vide ou s’il existe plusieurs éléments. Une alternative est `SingleOrDefault`, qui retourne une valeur par défaut (null dans ce cas) si la collection est vide. Toutefois, dans ce cas encore aboutirait à une exception (tente de trouver un `Courses` propriété sur une référence null), et le message d’exception indique moins clairement la cause du problème. Lorsque vous appelez le `Single` (méthode), vous pouvez également passer dont la condition au lieu d’appeler le `Where` méthode séparément :

```csharp
.Single(i => i.ID == id.Value)
```

À la place de :

```csharp
.Where(I => i.ID == id.Value).Single()
```

Ensuite, si un cours a été sélectionné, le cours sélectionné sont récupéré à partir de la liste des cours dans le modèle d’affichage. De puis le modèle d’affichage `Enrollments` propriété est chargée avec les entités de l’inscription à partir de ce cours `Enrollments` propriété de navigation.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Modifier l’affichage de l’Index de formateur

Dans *Views/Instructors/Index.cshtml*, remplacez le code de modèle par le code suivant. Les modifications sont mises en surbrillance.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

Vous avez effectué les modifications suivantes au code existant :

* Modifié de la classe de modèle à `InstructorIndexData`.

* Modifié le titre de la page à partir de **Index** à **instructeurs**.

* Ajouter un **Office** colonne affiche `item.OfficeAssignment.Location` uniquement si `item.OfficeAssignment` n’est pas null. (Car il s’agit d’une relation un-à-zéro-ou-un, il peut être une entité OfficeAssignment associée.)

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Ajouter un **cours** colonne qui affiche les cours dispensés par chaque formateur. Consultez [explicite Transition ligne avec `@:` ](xref:mvc/views/razor#explicit-line-transition-with-label) pour plus d’informations sur cette syntaxe razor.

* Code ajouté qui ajoute dynamiquement `class="success"` à la `tr` élément de l’enseignant sélectionné. Cela définit la couleur d’arrière-plan de la ligne sélectionnée à l’aide d’une classe d’amorçage.

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Ajouter un nouveau lien hypertexte étiqueté **sélectionnez** immédiatement avant les autres liens dans chaque ligne, ce qui entraîne des ID du formateur sélectionné à envoyer à la `Index` (méthode).

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Exécutez l’application et sélectionnez le **instructeurs** onglet. La page affiche la propriété d’emplacement des entités OfficeAssignment connexes et une cellule de table vide lorsqu’il n’existe aucune entité OfficeAssignment associée.

![Page d’Index instructeurs qu'aucun élément sélectionné](read-related-data/_static/instructors-index-no-selection.png)

Dans le *Views/Instructors/Index.cshtml* fichier, après la fermeture de table élément (à la fin du fichier), ajoutez le code suivant. Ce code affiche une liste de cours liés à un formateur lorsqu’un formateur est sélectionné.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

Ce code lit le `Courses` propriété du modèle de la vue pour afficher une liste de cours. Il fournit également un **sélectionnez** lien hypertexte qui envoie l’ID du cours sélectionné pour le `Index` méthode d’action.

Actualisez la page et sélectionner un formateur. Vous voyez à présent une grille qui affiche les cours affectés à l’enseignant sélectionné, et pour chaque cours, vous voyez le nom du service affecté.

![Formateur de page d’Index instructeurs sélectionné](read-related-data/_static/instructors-index-instructor-selected.png)

Après le bloc de code que vous venez d’ajouter, ajoutez le code suivant. Cela affiche une liste des étudiants qui sont inscrits dans un cours lorsque ce cours est sélectionné.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Ce code lit la propriété d’inscriptions du modèle de vue pour afficher une liste d’étudiants inscrits dans le cours.

Actualisez la page à nouveau et sélectionnez un formateur. Sélectionnez ensuite un cours pour afficher la liste des étudiants inscrits et leurs catégories.

![Formateur de page d’Index instructeurs et cours sélectionné](read-related-data/_static/instructors-index.png)

## <a name="explicit-loading"></a>Chargement explicite

Lorsque vous avez récupéré la liste des formateurs dans *InstructorsController.cs*, vous avez spécifié un chargement hâtif pour le `CourseAssignments` propriété de navigation.

Supposons que vous attendiez les utilisateurs que vous souhaitez rarement voir les inscriptions dans un formateur sélectionné et le cours. Dans ce cas, vous souhaiterez charger les données d’inscription uniquement si elle est demandée. Pour voir un exemple de procédure à suivre lors du chargement explicit, remplacez le `Index` méthode avec le code suivant, qui supprime un chargement hâtif pour les inscriptions et charge cette propriété explicitement. Les modifications de code sont mis en surbrillance.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

Le nouveau code supprime les *ThenInclude* méthode appelle pour l’inscription à partir du code qui extrait les entités de formateur. Si le formateur et cours sont sélectionnées, le code en surbrillance récupère les entités de l’inscription pour le cours sélectionné et étudiant des entités pour chaque inscription.

Exécutez que l’application, accédez à la page d’Index de formateurs maintenant et vous ne verrez aucune différence de ce qui est affiché dans la page, même si vous avez modifié la façon dont les données sont récupérées.

## <a name="summary"></a>Résumé

Vous avez maintenant utilisé chargement hâtif avec une requête et avec plusieurs requêtes pour lire les données connexes dans les propriétés de navigation. Dans l’étape suivante du didacticiel, vous allez apprendre à mettre à jour les données associées.

>[!div class="step-by-step"]
>[Précédent](complex-data-model.md)
>[Suivant](update-related-data.md)  

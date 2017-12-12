---
title: "Pages Razor avec EF Core - lire les données associées - 6 de 8"
author: rick-anderson
description: "Dans ce didacticiel vous lire et afficher les données associées, autrement dit, les données Entity Framework charge dans les propriétés de navigation."
keywords: "ASP.NET Core, Entity Framework Core, les données associées, jointures"
ms.author: riande
manager: wpickett
ms.date: 11/05/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/read-related-data
ms.openlocfilehash: ba9b17ecdcb605d39117d03230b1db37e8e4d0dd
ms.sourcegitcommit: 05e798c9bac7b9e9983599afb227ef393905d023
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="reading-related-data---ef-core-with-razor-pages-6-of-8"></a>Lecture liés de données - Core EF avec les Pages Razor (6 de 8)

Par [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), et [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

Dans ce didacticiel, les données associées sont lues et affichées. Les données associées sont données EF Core charge dans les propriétés de navigation.

Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez le [application terminée pour cette étape](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part6-related).

Les illustrations suivantes montrent les pages terminées pour ce didacticiel :

![Page d’Index de cours](read-related-data/_static/courses-index.png)

![Page d’Index instructeurs](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Chargement hâtif, explicit et différé de données connexes

Il existe plusieurs façons que EF Core chargez des données connexes dans les propriétés de navigation d’une entité :

* [Chargement hâtif](https://docs.microsoft.com/ef/core/querying/related-data#eager-loading). Chargement hâtif est lorsqu’une requête pour un type d’entité charge également des entités associées. Lors de la lecture de l’entité, ses données associées sont récupérées. Cela entraîne généralement une requête de jointure unique qui extrait toutes les données que nécessaire. EF Core émet plusieurs requêtes pour certains types de chargement hâtif. Émission de requêtes multiples peut être plus efficace qu’était le cas pour certaines requêtes de EF6 où il a une seule requête. Chargement hâtif est spécifié avec la `Include` et `ThenInclude` méthodes.

 ![Exemple de chargement hâtif](read-related-data/_static/eager-loading.png)
 
 Chargement hâtif envoie plusieurs requêtes lorsqu’une collection de nvavigation est incluse :

 * Une requête pour la requête principale 
 * Une requête pour chaque collection de « session » dans l’arborescence de la charge.

* Séparer les requêtes avec `Load`: les données peuvent être récupérées dans des requêtes distinctes et EF Core » résout « les propriétés de navigation. signifie « correctifs des » que EF Core remplit automatiquement les propriétés de navigation. Séparer les requêtes avec `Load` est plus explicite de chargement chargement hâtif.

 ![Exemple de requêtes distinctes](read-related-data/_static/separate-queries.png)

 Remarque : EF Core résout automatiquement les propriétés de navigation pour toutes les autres entités qui ont été précédemment chargées dans l’instance de contexte. Même si les données pour une propriété de navigation seront *pas* explicitement inclus, la propriété peut toujours être remplie si certaines ou toutes les entités connexes ont été précédemment chargées.

* [Chargement explicite](https://docs.microsoft.com/ef/core/querying/related-data#explicit-loading). Lors de la lecture de l’entité est tout d’abord, les données associées n’est pas récupérées. Code doit être écrit pour récupérer les données associées lorsque cela est nécessaire. Chargement explicite avec des requêtes distinctes entraîne plusieurs requêtes envoyées à la base de données. Avec le chargement explicite, le code spécifie les propriétés de navigation doivent être chargées. Utilisez la `Load` méthode pour effectuer le chargement explicite. Exemple :

 ![Exemple de chargement explicite](read-related-data/_static/explicit-loading.png)

* [Chargement différé](https://docs.microsoft.com/ef/core/querying/related-data#lazy-loading). [EF Core ne prend pas en charge le chargement tardif](https://github.com/aspnet/EntityFrameworkCore/issues/3797). Lors de la lecture de l’entité est tout d’abord, les données associées n’est pas récupérées. La première fois qu’une propriété de navigation est accessible, les données requises pour cette propriété de navigation sont automatiquement récupérées. Une requête est envoyée à la base de données chaque fois qu’une propriété de navigation est accessible pour la première fois.

* Le `Select` opérateur charge uniquement les données connexes nécessaires.

## <a name="create-a-courses-page-that-displays-department-name"></a>Créer une page de cours qui affiche le nom du service

L’entité de cours inclut une propriété de navigation qui contient le `Department` entité. Le `Department` entité contient le service auquel appartient le cours.

Pour afficher le nom du service affecté dans une liste de cours :

* Obtenir le `Name` propriété à partir de la `Department` entité.
* Le `Department` provient de l’entité la `Course.Department` propriété de navigation.

![ourse. Service](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>
### <a name="scaffold-the-course-model"></a>Structure du modèle de cours

* Quittez Visual Studio.
* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).
* Exécutez la commande suivante :

 ```console
dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
 ```

Les structures de commande précédent la `Course` modèle. Ouvrez le projet dans Visual Studio.

Générez le projet. La build génère des erreurs comme suit :

`1>Pages/Courses/Index.cshtml.cs(26,37,26,43): error CS1061: 'SchoolContext' does not
 contain a definition for 'Course' and no extension method 'Course' accepting a first
 argument of type 'SchoolContext' could be found (are you missing a using directive or
 an assembly reference?)`

 Modifier globalement `_context.Course` à `_context.Courses` (autrement dit, ajoutez un « s » pour `Course`). 7 occurrences sont trouvées et mis à jour.

Ouvrez *Pages/Courses/Index.cshtml.cs* et examinez le `OnGetAsync` (méthode). Le moteur de génération de modèles automatique spécifié pour un chargement hâtif le `Department` propriété de navigation. Le `Include` méthode spécifie un chargement hâtif.

Exécutez l’application et sélectionnez le **cours** lien. La colonne Service affiche le `DepartmentID`, ce qui n’est pas utile.

Mettez à jour la méthode `OnGetAsync` avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Le code précédent ajoute `AsNoTracking`. `AsNoTracking`améliore les performances, car les entités retournées ne sont pas suivies. Les entités ne sont pas suivies, car ils ne sont pas mis à jour dans le contexte actuel.

Mise à jour *Views/Courses/Index.cshtml* avec le balisage mis en surbrillance suivant :

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Les modifications suivantes ont été apportées au code de modèle généré automatiquement :

* Modifié le titre de l’Index au cours.
* Ajouter un **nombre** colonne qui affiche le `CourseID` valeur de propriété. Par défaut, les clés primaires ne sont pas structurés, car ceux-ci ne sont généralement pas de sens pour les utilisateurs finaux. Toutefois, dans ce cas la clé primaire est significative.
* Modifié le **service** colonne pour afficher le nom du service. Le code affiche le `Name` propriété de la `Department` entité qui est chargée dans le `Department` propriété de navigation :

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Exécutez l’application et sélectionnez le **cours** onglet pour afficher la liste avec les noms de service.

![Page d’Index de cours](read-related-data/_static/courses-index.png)

<a name="select"></a>
### <a name="loading-related-data-with-select"></a>Chargement liés de données avec Select

Le `OnGetAsync` méthode charge les données associées avec la `Include` méthode :

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

Le `Select` opérateur charge uniquement les données connexes nécessaires. Pour les éléments uniques, comme le `Department.Name` qu’il utilise une jointure interne SQL. Pour les regroupements qu’il utilise une autre base de données access, mais le.`Include` opérateur sur des collections.

Le code suivant charge les données associées avec la `Select` méthode :

[!code-csharp[Main](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

Le `CourseViewModel`:

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Consultez [IndexSelect.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) et [IndexSelect.cshtml.cs](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) pour obtenir un exemple complet.

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Créer une page de formateurs qui affiche des cours et des inscriptions

Dans cette section, la page instructeurs est créée.

<a name="IP"></a>
![Page d’Index instructeurs](read-related-data/_static/instructors-index.png)

Cette page lit et affiche les données connexes comme suit :

* La liste des instructeurs affiche les données connexes à partir de la `OfficeAssignment` entité (Office dans l’image précédente). Le `Instructor` et `OfficeAssignment` sont des entités dans une relation un-à-zéro-ou-un. Chargement hâtif est utilisé pour le `OfficeAssignment` entités. Chargement hâtif est généralement plus efficace lorsque les données connexes doivent être affichée. Dans ce cas, les affectations d’office pour les formateurs sont affichées.
* Lorsque l’utilisateur sélectionne un formateur (Harui dans l’image précédente), lié `Course` les entités sont affichées. Le `Instructor` et `Course` sont des entités dans une relation plusieurs-à-plusieurs. Eager de chargement pour le `Course` leur sont associées et les entités `Department` entités est utilisé. Dans ce cas, les requêtes distinctes peuvent être plus efficaces, car seuls les cours pour l’enseignant sélectionné sont nécessaires. Cet exemple montre comment utiliser un chargement hâtif pour les propriétés de navigation dans les entités qui se trouvent dans les propriétés de navigation.
* Lorsque l’utilisateur sélectionne un cours (chimie dans l’image précédente), les données à partir d’inhérentes à la `Enrollments` entité est affichée. Dans l’image précédente, la qualité et étudiant s’affichent. Le `Course` et `Enrollment` sont des entités dans une relation un-à-plusieurs.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Créer un modèle d’affichage pour l’affichage de l’Index de formateur

La page de formateurs affiche les données de trois tables différentes. Un modèle d’affichage est créé qui inclut les trois entités représentant les trois tables.

Dans le *SchoolViewModels* dossier, créez *InstructorIndexData.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Structure du modèle de formateur

* Quittez Visual Studio.
* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).
* Exécutez la commande suivante :

 ```console
dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
 ```

Les structures de commande précédent la `Instructor` modèle. Ouvrez le projet dans Visual Studio.

Générez le projet. La build génère des erreurs.

Modifier globalement `_context.Instructor` à `_context.Instructors` (autrement dit, ajoutez un « s » pour `Instructor`). 7 occurrences sont trouvées et mis à jour.

Exécuter l’application et accédez à la page de formateurs.

Remplacez *Pages/Instructors/Index.cshtml.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,20-)]

Le `OnGetAsync` méthode accepte les données d’itinéraire facultatif pour l’ID de l’enseignant sélectionné.

Examinez la requête sur le *Pages/Instructors/Index.cshtml* page :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

La requête comporte deux inclut :

* `OfficeAssignment`: Affichée dans le [instructeurs vue](#IP).
* `CourseAssignments`: Ce qui permet de bénéficier des dispensés.


### <a name="update-the-instructors-index-page"></a>Mise à jour de la page d’Index instructeurs

Mise à jour *Pages/Instructors/Index.cshtml* par le balisage suivant :

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Le balisage précédent apporte les modifications suivantes :

* Les mises à jour le `page` de `@page` à `@page "{id:int?}"`. `"{id:int?}"`est un modèle d’itinéraire. Le modèle d’itinéraire modifie les chaînes de requête entière dans l’URL pour les données d’itinéraire. Par exemple, en cliquant sur le **sélectionnez** lien un formateur lorsque la directive de page génère une URL comme suit :

    `http://localhost:1234/Instructors?id=2`

    Lorsque la directive de page est `@page "{id:int?}"`, l’URL précédente est :

    `http://localhost:1234/Instructors/2`

* Titre de la page est **instructeurs**.
* Ajouter un **Office** colonne affiche `item.OfficeAssignment.Location` uniquement si `item.OfficeAssignment` n’est pas null. Il s’agit d’une relation un-à-zéro-ou-un, il peuvent ne pas être une entité OfficeAssignment associée.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Ajouter un **cours** colonne qui affiche les cours dispensés par chaque formateur. Consultez [explicite Transition ligne avec `@:` ](xref:mvc/views/razor#explicit-line-transition-with-) pour plus d’informations sur cette syntaxe razor.

* Code ajouté qui ajoute dynamiquement `class="success"` à la `tr` élément de l’enseignant sélectionné. Cela définit la couleur d’arrière-plan de la ligne sélectionnée à l’aide d’une classe d’amorçage.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Ajouter un nouveau lien hypertexte étiqueté **sélectionnez**. Ce lien envoie l’ID du formateur sélectionné à la `Index` méthode et définit une couleur d’arrière-plan.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Exécutez l’application et sélectionnez le **instructeurs** onglet. La page affiche le `Location` (office) de la relation `OfficeAssignment` entité. Si OfficeAssignment' est null, une cellule de table vide s’affiche.

![Page d’Index instructeurs qu'aucun élément sélectionné](read-related-data/_static/instructors-index-no-selection.png)

Cliquez sur le **sélectionnez** lien. Les modifications de style de ligne.

### <a name="add-courses-taught-by-selected-instructor"></a>Ajouter dispensés par un formateur sélectionné

Mise à jour la `OnGetAsync` méthode dans *Pages/Instructors/Index.cshtml.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-)]

Examinez la requête de mise à jour :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

La requête précédente ajoute le `Department` entités.

Le code suivant s’exécute lorsqu’un formateur est sélectionné (`id != null`). Le formateur sélectionné est récupéré à partir de la liste des formateurs dans le modèle d’affichage. Le modèle d’affichage `Courses` propriété est chargée avec le `Course` entités à partir de ce formateur `CourseAssignments` propriété de navigation.

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

Le `Where` méthode retourne une collection. Dans l’exemple précédent `Where` (méthode), un seul `Instructor` est retournée. Le `Single` méthode convertit la collection en une seule `Instructor` entité. Le `Instructor` entité fournit l’accès à la `CourseAssignments` propriété. `CourseAssignments`fournit l’accès à la `Course` entités.

![Formateur à cours m:M](complex-data-model/_static/courseassignment.png)

Le `Single` méthode est utilisée sur une collection lorsque la collection comporte un seul élément. Le `Single` méthode lève une exception si la collection est vide ou s’il existe plusieurs éléments. Une alternative est `SingleOrDefault`, qui retourne une valeur par défaut (null dans ce cas) si la collection est vide. À l’aide de `SingleOrDefault` sur une collection vide :

* Une exception se produit (tente de trouver un `Courses` propriété sur une référence null).
* Le message d’exception indique moins clairement la cause du problème.

Le code suivant remplit le modèle d’affichage `Enrollments` propriété lorsqu’un cours est sélectionné :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

Ajoutez le balisage suivant à la fin de la *Pages/Courses/Index.cshtml* Page Razor :

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-)]

La balise précédente affiche une liste de cours liés à un formateur lorsqu’un formateur est sélectionné.

Tester l’application. Cliquez sur un **sélectionnez** lien dans la page de formateurs.

![Formateur de page d’Index instructeurs sélectionné](read-related-data/_static/instructors-index-instructor-selected.png)

### <a name="show-student-data"></a>Afficher les données de l’étudiant

Dans cette section, l’application est mise à jour pour afficher les données de l’étudiant pour le cours sélectionné.

Mettre à jour de la requête dans le `OnGetAsync` méthode dans *Pages/Instructors/Index.cshtml.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Mise à jour *Pages/Instructors/Index.cshtml*. Ajoutez le balisage suivant à la fin du fichier :

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

La balise précédente affiche une liste des étudiants qui sont inscrits dans le cours sélectionné.

Actualisez la page et sélectionner un formateur. Sélectionnez un cours pour afficher la liste des étudiants inscrits et leurs catégories.

![Formateur de page d’Index instructeurs et cours sélectionné](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>À l’aide d’unique

Le `Single` méthode peut passer le `Where` condition au lieu d’appeler le `Where` méthode séparément :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21,28-29)]

L’exemple précédent `Single` approche ne présente aucun avantage principal à l’aide `Where`. Certains développeurs préfèrent les `Single` approche de style.

## <a name="explicit-loading"></a>Chargement explicite

Le code actuel spécifie un chargement hâtif pour `Enrollments` et `Students`:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Supposons que les utilisateurs souhaitent rarement voir les inscriptions dans un cours. Dans ce cas, une optimisation serait pour charger uniquement les données d’inscription si elle est demandée. Dans cette section, le `OnGetAsync` est mis à jour pour utiliser le chargement explicite de `Enrollments` et `Students`.

Mise à jour le `OnGetAsync` par le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Le code précédent supprime le *ThenInclude* des appels de méthode pour les données d’inscription et les étudiants. Si un cours est sélectionné, le code en surbrillance récupère :

* Le `Enrollment` entités pour le cours sélectionné.
* Le `Student` entités pour chaque `Enrollment`.

Notez l’exemple précédent commentaire de code `.AsNoTracking()`. Propriétés de navigation peuvent uniquement être chargées explicitement pour les entités de suivi.

Tester l’application. Du point de vue des utilisateurs, l’application se comporte comme la version précédente.

Le didacticiel suivant montre comment mettre à jour les données associées.

>[!div class="step-by-step"]
>[Précédent](xref:data/ef-rp/complex-data-model)
>[Suivant](xref:data/ef-rp/update-related-data)
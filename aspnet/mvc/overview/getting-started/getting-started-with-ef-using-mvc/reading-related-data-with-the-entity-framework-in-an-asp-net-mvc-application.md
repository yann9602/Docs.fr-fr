---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
title: "Lors de la lecture des données associées avec Entity Framework dans une Application ASP.NET MVC | Documents Microsoft"
author: tdykstra
description: /AJAX/Tutorials/Using-AJAX-Control-Toolkit-Controls-and-Control-Extenders-VB
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/07/2014
ms.topic: article
ms.assetid: 18cdd896-8ed9-4547-b143-114711e3eafb
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 1f4912bb3113a8f9cdae4211e055a7e317ab2aff
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="reading-related-data-with-the-entity-framework-in-an-aspnet-mvc-application"></a>Les données avec Entity Framework dans une Application ASP.NET MVC inhérentes à la lecture
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) ou [télécharger le PDF](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio 2013. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).


Dans le didacticiel précédent vous terminé le modèle de données School. Dans ce didacticiel, vous allez lire et afficher les données associées, autrement dit, les données Entity Framework charge dans les propriétés de navigation.

Les illustrations suivantes montrent les pages que vous allez utiliser.

![](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Instructors_index_page_with_instructor_and_course_selected](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

## <a name="lazy-eager-and-explicit-loading-of-related-data"></a>Chargement différé, Eager et Explicit de données connexes

Il existe plusieurs façons qu’Entity Framework peut charger des données connexes dans les propriétés de navigation d’une entité :

- *Chargement différé*. Lors de la lecture de l’entité est tout d’abord, les données associées n’est pas récupérées. Toutefois, la première fois que vous tentez d’accéder à une propriété de navigation, les données requises pour cette propriété de navigation sont automatiquement récupérées. Cela entraîne plusieurs requêtes envoyées à la base de données : une pour l’entité elle-même et chaque fois que les données pour l’entité associées doivent être récupérées. La `DbContext` classe permet le chargement différé par défaut. 

    ![Lazy_loading_example](https://asp.net/media/2577850/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Lazy_loading_example_2c44eabb-5fd3-485a-837d-8e3d053f2c0c.png)
- *Chargement hâtif*. Lors de la lecture de l’entité, les données associées sont récupérées en même temps. Cela entraîne généralement une requête de jointure unique qui extrait toutes les données que nécessaire. Vous spécifiez un chargement hâtif à l’aide de la `Include` (méthode).

    ![Eager_loading_example](https://asp.net/media/2577856/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Eager_loading_example_33f907ff-f0b0-4057-8e75-05a8cacac807.png)
- *Chargement explicite*. Cela revient à chargement différé, à ceci près que vous explicitement de récupérer les données associées dans le code ; Il ne se produit automatiquement lorsque vous accédez à une propriété de navigation. Vous chargez manuellement les données associées en obtenant l’entrée du gestionnaire objet état pour une entité et l’appel du [Collection.Load](https://msdn.microsoft.com/en-us/library/gg696220(v=vs.103).aspx) méthode pour les collections ou les [Reference.Load](https://msdn.microsoft.com/en-us/library/gg679166(v=vs.103).aspx) méthode pour les propriétés qui contiennent un entité unique. (Dans l’exemple suivant, si vous souhaitez charger la propriété de navigation administrateur, vous devez remplacer `Collection(x => x.Courses)` avec `Reference(x => x.Administrator)`.) En règle générale, vous pouvez utiliser le chargement explicite uniquement lorsque vous avez activé la chargement désactivé différé.

    ![Explicit_loading_example](https://asp.net/media/2577862/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Explicit_loading_example_79d8c368-6d82-426f-be9a-2b443644ab15.png)

Car ils ne récupèrent pas immédiatement les valeurs de propriété, le chargement tardif et chargement explicite sont également tous deux appelés *chargement différé*.

### <a name="performance-considerations"></a>Considérations sur les performances

Si vous savez que vous avez besoin des données associées pour toutes les entités récupérées, le chargement hâtif souvent offre des performances optimales, car une seule requête envoyée à la base de données est généralement plus efficace que les requêtes distinctes pour chaque entité récupérée. Par exemple, dans les exemples ci-dessus, supposons que chaque service possède dix cours associés. L’exemple de chargement hâtif entraînerait simplement une requête unique (jointure) et un seul aller-retour à la base de données. Le chargement différé et des exemples de chargement explicite à la fois entraînerait onze requêtes et onze les allers-retours vers la base de données. Les allers-retours supplémentaires à la base de données sont particulièrement nuits aux performances lors de la latence est élevée.

En revanche, dans certains scénarios de chargement différé est plus efficace. Chargement hâtif peut entraîner une jointure très complexe à générer, lequel SQL Server ne peut pas traiter efficacement. Ou, si vous avez besoin d’accéder aux propriétés de navigation d’une entité uniquement pour un sous-ensemble d’un jeu d’entités que vous traitez, le chargement tardif peut-être être améliorées, car le chargement hâtif permet de récupérer plus de données que vous avez besoin. Si les performances sont critiques, il est préférable de tester les performances les deux méthodes pour effectuer le meilleur choix.

Chargement différé peut masquer le code qui provoque des problèmes de performances. Par exemple, le code qui ne spécifie pas de chargement hâtif ou explicit, mais traite un volume élevé d’entités et utilise plusieurs propriétés de navigation dans chaque itération peut être très inefficace (en raison des nombreux allers-retours vers la base de données). Une application qui effectue correctement dans le développement à l’aide d’un serveur SQL local peut avoir des problèmes de performances lorsque déplacé vers la base de données SQL Azure en raison de la latence accrue et le chargement différé. Les requêtes de base de données avec une charge réaliste de tests de profilage vous aidera à déterminer si le chargement différé est approprié. Pour plus d’informations, consultez [Démystification des stratégies Entity Framework : chargement des données connexes](https://msdn.microsoft.com/en-us/magazine/hh205756.aspx) et [à l’aide d’Entity Framework pour réduire la latence du réseau à SQL Azure](https://msdn.microsoft.com/en-us/magazine/gg309181.aspx).

### <a name="disable-lazy-loading-before-serialization"></a>Désactiver le chargement tardif avant la sérialisation

Si vous laissez du chargement différé activé pendant la sérialisation, vous pouvez retrouver l’interrogation de données beaucoup plus que prévu. En règle générale, la sérialisation fonctionne en accédant à chaque propriété sur une instance d’un type. Accès à la propriété déclenche le chargement différé, et ces entités chargées différées sont sérialisées. Le processus de sérialisation accède ensuite à chaque propriété des entités chargées en différé, ce qui peut entraîner davantage de sérialisation et de chargement différé. Pour empêcher cette réaction en chaîne pertes, activez différée chargement désactivé avant que vous sérialisez une entité.

Sérialisation peut également être compliquée par les classes proxy qui utilise d’Entity Framework, comme expliqué dans la [didacticiel de scénarios avancés](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#proxies).

Une façon d’éviter les problèmes de sérialisation consiste à sérialiser des objets de transfert de données (DTO) au lieu d’objets d’entité, comme indiqué dans le [à l’aide des API Web avec Entity Framework](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-5.md) didacticiel.

Si vous n’utilisez pas DTO, vous pouvez désactiver le chargement différé et éviter les problèmes de proxy par [la désactivation de la création du proxy](https://msdn.microsoft.com/en-US/data/jj592886.aspx).

Voici un autre [comment désactiver le chargement différé](https://msdn.microsoft.com/en-US/data/jj574232):

- Pour les propriétés de navigation spécifiques, omettez le `virtual` mot clé lorsque vous déclarez la propriété.
- Pour toutes les propriétés de navigation, définissez `LazyLoadingEnabled` à `false`, placez le code suivant dans le constructeur de votre classe de contexte : 

    [!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

## <a name="create-a-courses-page-that-displays-department-name"></a>Créer une Page de cours ce nom affiche service

Le `Course` entité inclut une propriété de navigation qui contient le `Department` entité du service auquel appartient le cours. Pour afficher le nom du service affecté dans une liste de cours, vous devez obtenir le `Name` propriété à partir de la `Department` entité qui se trouve dans le `Course.Department` propriété de navigation.

Créer un contrôleur nommé `CourseController` (pas CoursesController) pour le `Course` type d’entité, utilisant les mêmes options pour le **contrôleur MVC 5 avec vues, utilisant Entity Framework** scaffolder que vous avez utilisé précédemment pour le `Student` contrôleur, comme indiqué dans l’illustration suivante :

![Add_Controller_dialog_box_for_Course_controller](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

Ouvrez *Controllers\CourseController.cs* et examinez le `Index` méthode :

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

La génération de modèles automatique a spécifié un chargement hâtif pour le `Department` propriété de navigation à l’aide de la `Include` (méthode).

Ouvrez *Views\Course\Index.cshtml* et remplacez le code de modèle par le code suivant. Les modifications sont mises en surbrillance :

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cshtml?highlight=4,7,14-16,23-25,31-33,40-42)]

Vous avez effectué les modifications suivantes au code de modèle généré automatiquement :

- Modifié le titre de **Index** à **cours**.
- Ajouter un **nombre** colonne qui affiche le `CourseID` valeur de propriété. Par défaut, les clés primaires ne sont pas structurés, car ceux-ci ne sont généralement pas de sens pour les utilisateurs finaux. Toutefois, dans ce cas, la clé primaire est significative et que vous souhaitez afficher.
- Déplacer le **service** colonne vers la droite et modifié son titre. Le scaffolder correctement choisissez d’afficher le `Name` propriété à partir de la `Department` entité, mais ici dans l’en-tête de colonne doit être la page du cours **service** plutôt que **nom**.

Notez que pour la colonne service, le modèle généré automatiquement du code affiche le `Name` propriété de la `Department` entité qui est chargée dans le `Department` propriété de navigation :

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=2)]

Exécution de la page (sélectionnez le **cours** onglet sur la page d’accueil Contoso University) pour afficher la liste avec les noms de service.

![Courses_index_page_with_department_names](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Créer une Page de formateurs qui affiche des cours et des inscriptions

Dans cette section, vous allez créer un contrôleur et afficher pour le `Instructor` entité afin d’afficher la page de formateurs :

![Instructors_index_page_with_instructor_and_course_selected](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)

Cette page lit et affiche les données connexes comme suit :

- La liste des instructeurs affiche les données connexes à partir de la `OfficeAssignment` entité. Le `Instructor` et `OfficeAssignment` sont des entités dans une relation un-à-zéro-ou-un. Vous allez utiliser un chargement hâtif pour le `OfficeAssignment` entités. Comme expliqué précédemment, le chargement hâtif est généralement plus efficace lorsque vous avez besoin des données associées pour toutes les lignes extraites de la table primaire. Dans ce cas, vous souhaitez afficher les affectations d’office pour tous les formateurs affichées.
- Lorsque l’utilisateur sélectionne un formateur lié `Course` les entités sont affichées. Le `Instructor` et `Course` sont des entités dans une relation plusieurs-à-plusieurs. Vous allez utiliser un chargement hâtif pour le `Course` leur sont associées et les entités `Department` entités. Dans ce cas, le chargement tardif peut être plus efficace, car vous avez besoin de cours uniquement pour l’enseignant sélectionné. Toutefois, cet exemple montre comment utiliser un chargement hâtif pour les propriétés de navigation au sein des entités qui sont elles-mêmes des propriétés de navigation.
- Lorsque l’utilisateur sélectionne un cours, liés des données à partir de la `Enrollments` jeu d’entités s’affiche. Le `Course` et `Enrollment` sont des entités dans une relation un-à-plusieurs. Vous ajouterez le chargement explicite pour `Enrollment` leur sont associées et les entités `Student` entités. (Chargement explicite n’est pas nécessaire, car le chargement différé est activé, mais cet exemple montre comment effectuer le chargement explicite.)

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Créer un modèle d’affichage de la vue d’Index formateur

La page instructeurs montre trois tables différentes. Par conséquent, vous allez créer un modèle d’affichage qui comprend trois propriétés, chaque contenant les données pour l’une des tables.

Dans le *ViewModel* dossier, créez *InstructorIndexData.cs* et remplacez le code existant par le code suivant :

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Créer le contrôleur du formateur et des vues

Créer un `InstructorController` (pas InstructorsController) contrôleur avec actions de lecture/écriture EF comme indiqué dans l’illustration suivante :

![Add_Controller_dialog_box_for_Instructor_controller](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

Ouvrez *Controllers\InstructorController.cs* et ajoutez un `using` instruction pour le `ViewModels` espace de noms :

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

Le code de modèle généré automatiquement dans le `Index` méthode spécifie un chargement hâtif uniquement pour le `OfficeAssignment` propriété de navigation :

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

Remplacez le `Index` méthode avec le code suivant à la charge supplémentaire des données liées et le placer dans le modèle d’affichage :

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

La méthode accepte les données d’itinéraire facultatif (`id`) et un paramètre de chaîne de requête (`courseID`) qui fournissent les valeurs d’ID de l’enseignant sélectionné et le cours sélectionné et passe toutes les données nécessaires à la vue. Les paramètres sont fournis par le **sélectionnez** des liens hypertexte dans la page.

Le code commence par la création d’une instance du modèle de vue et donne la liste des formateurs. Le code spécifie un chargement hâtif pour le `Instructor.OfficeAssignment` et `Instructor.Courses` propriété de navigation.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs?highlight=3-4)]

La seconde `Include` méthode charge de cours et pour chaque cours qui est chargé, il ne chargement hâtif pour le `Course.Department` propriété de navigation.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

Comme mentionné précédemment, chargement hâtif n’est pas obligatoire, mais est effectué pour améliorer les performances. Étant donné que la vue nécessite toujours le `OfficeAssignment` entité, il est plus efficace d’extraire que dans la même requête. `Course`les entités sont requises lorsqu’un formateur est sélectionné dans la page web, chargement hâtif est meilleur que le chargement différé uniquement si la page s’affiche plus souvent avec un cours sélectionné que sans.

Si un ID de formateur a été sélectionné, le formateur sélectionné est récupéré à partir de la liste des formateurs dans le modèle d’affichage. Le modèle d’affichage `Courses` propriété est ensuite chargée avec le `Course` entités à partir de ce formateur `Courses` propriété de navigation.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

Le `Where` méthode retourne une collection, mais dans ce cas les critères passé à ce résultat de la méthode dans une seule `Instructor` entité qui est retournée. Le `Single` méthode convertit la collection en une seule `Instructor` entité, qui vous permet d’accéder à cette entité `Courses` propriété.

Vous utilisez la [unique](https://msdn.microsoft.com/en-us/library/system.linq.enumerable.single.aspx) méthode sur une collection lorsque vous savez que la collection aura qu’un seul élément. Le `Single` méthode lève une exception si la collection passée à ce dernier est vide ou s’il existe plusieurs éléments. Une alternative est [SingleOrDefault](https://msdn.microsoft.com/en-us/library/bb342451.aspx), qui retourne une valeur par défaut (`null` dans ce cas) si la collection est vide. Toutefois, dans ce cas encore aboutirait à une exception (tente de trouver un `Courses` propriété sur un `null` référence), et le message d’exception indique moins clairement la cause du problème. Lorsque vous appelez le `Single` (méthode), vous pouvez également transmettre le `Where` condition au lieu d’appeler le `Where` méthode séparément :

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cs)]

À la place de :

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

Ensuite, si un cours a été sélectionné, le cours sélectionné sont récupéré à partir de la liste des cours dans le modèle d’affichage. De puis le modèle d’affichage `Enrollments` propriété est chargée avec le `Enrollment` entités à partir de ce cours `Enrollments` propriété de navigation.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs)]

### <a name="modify-the-instructor-index-view"></a>Modifier l’affichage des Index formateur

Dans *Views\Instructor\Index.cshtml*, remplacez le code de modèle par le code suivant. Les modifications sont mises en surbrillance :

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cshtml?highlight=1,4,14-18,21,23-28,38-43,45)]

Vous avez effectué les modifications suivantes au code existant :

- Modifié de la classe de modèle à `InstructorIndexData`.
- Modifié le titre de la page à partir de **Index** à **instructeurs**.
- Ajouter un **Office** colonne affiche `item.OfficeAssignment.Location` uniquement si `item.OfficeAssignment` n’est pas null. (Il s’agit d’une relation un-à-zéro-ou-un, il peuvent ne pas être un `OfficeAssignment` entity.) 

    [!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cshtml)]
- Code ajouté qui ajoute dynamiquement `class="success"` à la `tr` élément de l’enseignant sélectionné. Cela définit la couleur d’arrière-plan de la ligne sélectionnée à l’aide un [Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap) classe. 

    [!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml)]
- Ajouté un nouveau `ActionLink` intitulé **sélectionnez** immédiatement avant les autres liens dans chaque ligne, ce qui entraîne l’ID de formateur sélectionné être envoyés à la `Index` (méthode).

Exécutez l’application et sélectionnez le **instructeurs** onglet. La page affiche le `Location` propriété connexes `OfficeAssignment` entités et une table vide de la cellule lorsqu’il est non liée `OfficeAssignment` entité.

![Instructors_index_page_with_nothing_selected](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

Dans le *Views\Instructor\Index.cshtml* fichier, après la fermeture `table` élément (à la fin du fichier), ajoutez le code suivant. Ce code affiche une liste de cours liés à un formateur lorsqu’un formateur est sélectionné.

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cshtml)]

Ce code lit le `Courses` propriété du modèle de la vue pour afficher une liste de cours. Il fournit également un `Select` lien hypertexte qui envoie l’ID du cours sélectionné pour le `Index` méthode d’action.

Exécutez la page et sélectionner un formateur. Vous voyez à présent une grille qui affiche les cours affectés à l’enseignant sélectionné, et pour chaque cours, vous voyez le nom du service affecté.

![Instructors_index_page_with_instructor_selected](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image8.png)

Après le bloc de code que vous venez d’ajouter, ajoutez le code suivant. Cela affiche une liste des étudiants qui sont inscrits dans un cours lorsque ce cours est sélectionné.

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml)]

Ce code lit le `Enrollments` propriété du modèle de la vue pour afficher une liste d’étudiants inscrits dans le cours.

Exécutez la page et sélectionner un formateur. Sélectionnez ensuite un cours pour afficher la liste des étudiants inscrits et leurs catégories.

![Instructors_index_page_with_instructor_and_course_selected](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

### <a name="adding-explicit-loading"></a>Ajout de chargement explicite

Ouvrez *InstructorController.cs* et examiner comment les `Index` méthode obtient la liste des inscriptions pour un cours sélectionné :

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs)]

Lorsque vous avez extrait la liste des instructeurs, vous avez spécifié un chargement hâtif pour le `Courses` propriété de navigation et pour le `Department` propriété de chaque cours. Vous placez le `Courses` la collection dans le modèle d’affichage, et vous accédez à présent le `Enrollments` propriété de navigation d’une entité de la collection. Étant donné que vous n’avez pas spécifié un chargement hâtif pour le `Course.Enrollments` propriété de navigation, les données de cette propriété s’affiche dans la page à la suite de chargement différé.

Si vous avez désactivé le chargement différé sans modifier le code de toute autre manière, le `Enrollments` propriété serait null, quelle que soit l’inscriptions combien disposions de cours. Dans ce cas, charger le `Enrollments` propriété, vous devez spécifier un chargement hâtif ou chargement explicite. Vous avez déjà vu comment effectuer un chargement hâtif. Pour voir un exemple de chargement explicite, remplacez le `Index` méthode avec le code suivant, qui charge explicitement le `Enrollments` propriété. Le code de modification sont mises en surbrillance.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cs?highlight=20-31)]

Après avoir obtenu le `Course` entité, le nouveau code charge explicitement de ce cours `Enrollments` propriété de navigation :

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs)]

Ensuite, il charge explicitement chaque `Enrollment` associée d’entité du `Student` entité :

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cs)]

Notez que vous utilisez le `Collection` méthode pour charger une propriété de collection, mais pour une propriété qui contient uniquement une entité, vous utilisez la `Reference` (méthode).

Exécuter maintenant de la page d’Index du formateur et vous ne verrez aucune différence de ce qui est affiché dans la page, même si vous avez modifié la façon dont les données sont récupérées.

## <a name="summary"></a>Résumé

Vous avez maintenant utilisé toutes les trois façons (lazy eager et explicites) pour charger des données connexes dans les propriétés de navigation. Dans l’étape suivante du didacticiel, vous allez apprendre à mettre à jour les données associées.

Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer. Vous pouvez également demander de nouvelles rubriques à [afficher Me comment avec le Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

Vous trouverez des liens vers d’autres ressources Entity Framework dans le [ASP.NET Data Access - ressources recommandées](../../../../whitepapers/aspnet-data-access-content-map.md).

>[!div class="step-by-step"]
[Précédent](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)
[Suivant](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

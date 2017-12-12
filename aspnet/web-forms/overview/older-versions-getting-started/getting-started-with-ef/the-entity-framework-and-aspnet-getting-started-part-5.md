---
uid: web-forms/overview/older-versions-getting-started/getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-5
title: "Mise en route avec base de données Entity Framework 4.0 tout d’abord et 4 d’ASP.NET Web Forms - partie 5 | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications Web Forms ASP.NET à l’aide d’Entity Framework. L’exemple d’application est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/03/2010
ms.topic: article
ms.assetid: 24ad4379-3fb2-44dc-ba59-85fe0ffcb2ae
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-5
msc.type: authoredcontent
ms.openlocfilehash: 7200899d54585cd09e0a648e3aaaf839db2649e0
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="getting-started-with-entity-framework-40-database-first-and-aspnet-4-web-forms---part-5"></a>Mise en route avec base de données Entity Framework 4.0 tout d’abord et 4 les Web Forms ASP.NET - partie 5
====================
Par [Tom Dykstra](https://github.com/tdykstra)

> L’exemple d’application web Contoso University montre comment créer des applications Web Forms ASP.NET à l’aide de l’Entity Framework 4.0 et Visual Studio 2010. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](the-entity-framework-and-aspnet-getting-started-part-1.md)


## <a name="working-with-related-data-continued"></a>Utilisation des données associées, suite

Dans le didacticiel précédent, vous avez commencé à utiliser le `EntityDataSource` contrôle pour travailler avec les données associées. Affiche plusieurs niveaux de hiérarchie et de modification de données dans les propriétés de navigation. Dans ce didacticiel, vous allez continuer à travailler avec les données associées en ajoutant et supprimant des relations et en ajoutant une nouvelle entité qui a une relation à une entité existante.

Vous allez créer une page qui ajoute des cours qui sont affectés à des services. Les services existent déjà, et lorsque vous créez un nouveau cours, en même temps, vous devez établir une relation entre elle et d’un service existant.

[![Image02](the-entity-framework-and-aspnet-getting-started-part-5/_static/image2.png)](the-entity-framework-and-aspnet-getting-started-part-5/_static/image1.png)

Vous allez également créer une page qui fonctionne avec une relation plusieurs-à-plusieurs en assignant un formateur à un cours (ajout d’une relation entre deux entités que vous sélectionnez) ou la suppression d’un formateur à partir d’un cours (suppression d’une relation entre deux entités que vous avez Sélectionnez). Dans la base de données, l’ajout d’une relation entre un formateur et un cours entraîne une nouvelle ligne est ajoutée à la `CourseInstructor` table association ; la suppression d’une relation implique la suppression d’une ligne à partir de la `CourseInstructor` table association. Toutefois, cela dans Entity Framework en définissant les propriétés de navigation, sans faire référence à la `CourseInstructor` table explicitement.

[![Image01](the-entity-framework-and-aspnet-getting-started-part-5/_static/image4.png)](the-entity-framework-and-aspnet-getting-started-part-5/_static/image3.png)

## <a name="adding-an-entity-with-a-relationship-to-an-existing-entity"></a>Ajout d’une entité avec une relation à une entité existante

Créer une nouvelle page web nommée *CoursesAdd.aspx* qui utilise le *Site.Master* page maître et ajoutez le balisage suivant à la `Content` contrôle nommé `Content2`:

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-5/samples/sample1.aspx)]

Cette balise crée un `EntityDataSource` contrôle qui sélectionne des cours, qui permet d’insérer, et qui spécifie un gestionnaire pour le `Inserting` événement. Vous allez utiliser le Gestionnaire de mise à jour la `Department` propriété de navigation quand un nouveau `Course` entité est créée.

Le balisage crée également un `DetailsView` contrôle à utiliser pour ajouter de nouveaux `Course` entités. Le balisage utilise des champs liés pour `Course` propriétés de l’entité. Vous devez entrer le `CourseID` valeur, car il n’est pas un champ ID généré par le système. Au lieu de cela, il est un nombre de cours qui doit être spécifié manuellement lorsque le cours est créé.

Vous utilisez un champ de modèle pour le `Department` propriété de navigation, car les propriétés de navigation ne peut pas être utilisées avec `BoundField` contrôles. Le champ modèle fournit une liste déroulante pour sélectionner le service. La liste déroulante est liée à la `Departments` jeu à l’aide d’entités `Eval` plutôt que `Bind`, à nouveau, car il est impossible de lier directement afin de les mettre à jour des propriétés de navigation. Vous spécifiez un gestionnaire pour le `DropDownList` du contrôle `Init` événement afin que vous pouvez stocker une référence au contrôle pour une utilisation par le code qui met à jour le `DepartmentID` clé étrangère.

Dans *CoursesAdd.aspx.cs* juste après la déclaration de classe partielle, ajoutez un champ de classe pour contenir une référence à la `DepartmentsDropDownList` contrôle :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-5/samples/sample2.cs)]

Ajoutez un gestionnaire pour le `DepartmentsDropDownList` du contrôle `Init` événement afin que vous pouvez stocker une référence au contrôle. Cela vous permet d’obtenir la valeur de l’utilisateur a entré et l’utiliser pour mettre à jour le `DepartmentID` valeur de la `Course` entité.

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-5/samples/sample3.cs)]

Ajoutez un gestionnaire pour le `DetailsView` du contrôle `Inserting` événement :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-5/samples/sample4.cs)]

Lorsque l’utilisateur clique sur `Insert`, le `Inserting` événement est déclenché avant le nouvel enregistrement est inséré. Obtient par le code dans le Gestionnaire de la `DepartmentID` à partir de la `DropDownList` de contrôle et l’utilise pour définir la valeur qui sera utilisée pour le `DepartmentID` propriété de la `Course` entité.

Entity Framework s’occupe de l’ajout de ce cours pour le `Courses` propriété de navigation associé au `Department` entité. Il ajoute également le service pour le `Department` propriété de navigation de la `Course` entité.

Exécutez la page.

[![Image02](the-entity-framework-and-aspnet-getting-started-part-5/_static/image6.png)](the-entity-framework-and-aspnet-getting-started-part-5/_static/image5.png)

Entrez un ID, un titre, un nombre de crédits, sélectionnez un service, puis cliquez sur **insérer**.

Exécutez le *Courses.aspx* page et sélectionner le même service pour afficher le nouveau cours.

[![Image03](the-entity-framework-and-aspnet-getting-started-part-5/_static/image8.png)](the-entity-framework-and-aspnet-getting-started-part-5/_static/image7.png)

## <a name="working-with-many-to-many-relationships"></a>Utilisation des relations plusieurs-à-plusieurs

La relation entre la `Courses` jeu d’entités et les `People` jeu d’entités est une relation plusieurs-à-plusieurs. A `Course` entité a une propriété de navigation nommée `People` qui peut contenir zéro, un ou plusieurs liées `Person` entités (représentant des instructeurs affectés à animer ce cours). Et un `Person` entité a une propriété de navigation nommée `Courses` qui peut contenir zéro, un ou plusieurs liées `Course` entités (représentant des cours ce formateur est attribué à apprendre). Un formateur peut couvrir plusieurs cours, et un cours peut être dirigée par des instructeurs plusieurs. Dans cette section de la procédure pas à pas, vous allez ajouter et supprimer des relations entre `Person` et `Course` entités en mettant à jour les propriétés de navigation des entités associées.

Créer une nouvelle page web nommée *InstructorsCourses.aspx* qui utilise le *Site.Master* page maître et ajoutez le balisage suivant à la `Content` contrôle nommé `Content2`:

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-5/samples/sample5.aspx)]

Cette balise crée un `EntityDataSource` contrôle qui Récupère le nom et `PersonID` de `Person` entités pour les formateurs. A `DropDrownList` contrôle est lié à la `EntityDataSource` contrôle. Le `DropDownList` contrôle spécifie un gestionnaire pour le `DataBound` événement. Vous allez utiliser ce gestionnaire aux databind les deux listes déroulantes, qui affichent des cours.

Le balisage crée également le groupe de contrôles à utiliser pour l’affectation d’un cours pour le formateur sélectionné suivant :

- A `DropDownList` contrôle permettant de sélectionner un cours à affecter. Ce contrôle contiendra les cours ne sont pas actuellement attribués à l’enseignant sélectionné.
- A `Button` pour lancer l’affectation.
- A `Label` contrôle pour afficher un message d’erreur si l’affectation échoue.

Enfin, le balisage crée également un groupe de contrôles à utiliser pour la suppression d’un cours de l’enseignant sélectionné.

Dans *InstructorsCourses.aspx.cs*, ajouter une à l’aide de déclaration :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-5/samples/sample6.cs)]

Ajoutez une méthode pour remplir les deux listes déroulantes qui affichent des cours :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-5/samples/sample7.cs)]

Ce code obtient tous les cours à partir de la `Courses` entité définie et obtient les cours à partir de la `Courses` propriété de navigation de la `Person` entité pour l’enseignant sélectionné. Ensuite, il détermine les cours sont affectés à ce formateur et remplit les listes déroulantes en conséquence.

Ajoutez un gestionnaire pour le `Assign` du bouton `Click` événement :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-5/samples/sample8.cs)]

Ce code obtient le `Person` Obtient de l’entité pour l’enseignant sélectionné, le `Course` entité pour le cours sélectionné et ajoute le cours sélectionné pour le `Courses` propriété de navigation de l’enseignant `Person` entité. Ensuite, il enregistre les modifications dans la base de données et remplit à nouveau les listes déroulantes pour les résultats peuvent être consultés immédiatement.

Ajoutez un gestionnaire pour le `Remove` du bouton `Click` événement :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-5/samples/sample9.cs)]

Ce code obtient le `Person` Obtient de l’entité pour l’enseignant sélectionné, le `Course` entité pour le cours sélectionné et supprime le cours sélectionné à partir de la `Person` l’entité `Courses` propriété de navigation. Ensuite, il enregistre les modifications dans la base de données et remplit à nouveau les listes déroulantes pour les résultats peuvent être consultés immédiatement.

Ajoutez du code pour le `Page_Load` méthode qui permet de s’assurer les messages d’erreur n’est pas visible quand il n’existe aucune erreur de rapport et d’ajouter des gestionnaires pour le `DataBound` et `SelectedIndexChanged` les événements de la liste déroulante de formateurs pour remplir les listes déroulantes de cours :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-5/samples/sample10.cs)]

Exécutez la page.

[![Image01](the-entity-framework-and-aspnet-getting-started-part-5/_static/image10.png)](the-entity-framework-and-aspnet-getting-started-part-5/_static/image9.png)

Sélectionnez un formateur. Le **affecter un cours** liste déroulante affiche les cours le formateur n’animer, et le **supprimer un cours** liste déroulante affiche les cours le formateur est déjà assigné à. Dans le **affecter un cours** section, sélectionnez un cours, puis sur **affecter**. Le cours se déplace vers le **supprimer un cours** liste déroulante. Sélectionnez un cours dans le **supprimer un cours** et cliquez sur **supprimer***.* Le cours se déplace vers le **affecter un cours** liste déroulante.

Vous savez maintenant certains autres façons de travailler avec les données associées. Dans ce didacticiel, vous allez apprendre comment utiliser l’héritage dans le modèle de données pour améliorer la facilité de maintenance de votre application.

>[!div class="step-by-step"]
[Précédent](the-entity-framework-and-aspnet-getting-started-part-4.md)
[Suivant](the-entity-framework-and-aspnet-getting-started-part-6.md)

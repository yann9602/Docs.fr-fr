---
uid: web-forms/overview/older-versions-getting-started/getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-6
title: "Mise en route avec base de données Entity Framework 4.0 tout d’abord et 4 d’ASP.NET Web Forms - partie 6 | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications Web Forms ASP.NET à l’aide d’Entity Framework. L’exemple d’application est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/03/2010
ms.topic: article
ms.assetid: 994a5496-c648-4830-b03c-55bb43f325d2
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-6
msc.type: authoredcontent
ms.openlocfilehash: 164c2002a119420555d2c7065c5a79a5f433a725
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="getting-started-with-entity-framework-40-database-first-and-aspnet-4-web-forms---part-6"></a>Mise en route avec base de données Entity Framework 4.0 tout d’abord et 4 les Web Forms ASP.NET - partie 6
====================
Par [Tom Dykstra](https://github.com/tdykstra)

> L’exemple d’application web Contoso University montre comment créer des applications Web Forms ASP.NET à l’aide de l’Entity Framework 4.0 et Visual Studio 2010. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](the-entity-framework-and-aspnet-getting-started-part-1.md)


## <a name="implementing-table-per-hierarchy-inheritance"></a>Implémentation de l'héritage TPH (table par hiérarchie)

Dans le didacticiel précédent vous avez travaillé avec des données connexes en ajoutant et supprimant des relations et en ajoutant une nouvelle entité ayant une relation à une entité existante. Ce didacticiel vous indiquera comment implémenter l’héritage dans le modèle de données.

Dans la programmation orientée objet, vous pouvez utiliser l’héritage pour le rendre plus facile de travailler avec les classes connexes. Par exemple, vous pouvez créer `Instructor` et `Student` classes qui dérivent d’un `Person` classe de base. Vous pouvez créer les mêmes types de structures d’héritage entre les entités dans Entity Framework.

Dans cette partie du didacticiel, vous ne sont pas créer de nouvelles pages web. Au lieu de cela, vous allez ajouter des entités dérivées pour le modèle de données et modifier des pages existantes pour utiliser les nouvelles entités.

## <a name="table-per-hierarchy-versus-table-per-type-inheritance"></a>Table par hiérarchie et héritage Table par Type

Une base de données peut stocker plus d’informations sur les objets connexes dans une table ou dans plusieurs tables. Par exemple, dans le `School` base de données, le `Person` table inclut des informations sur les étudiants et instructeurs dans une table unique. Certaines colonnes s’appliquent uniquement aux instructeurs (`HireDate`), certains uniquement pour les étudiants (`EnrollmentDate`), tandis que d’autres à la fois (`LastName`, `FirstName`).

[![image11](the-entity-framework-and-aspnet-getting-started-part-6/_static/image2.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image1.png)

Vous pouvez configurer l’Entity Framework pour créer `Instructor` et `Student` entités qui héritent de la `Person` entité. Ce modèle de la génération d’une structure d’héritage d’entité à partir d’une table de base de données unique est appelé *table par hiérarchie* l’héritage (TPH).

Pour les cours, le `School` base de données utilise un modèle différent. Cours en ligne et les formations sur site sont stockées dans des tables distinctes, chacune d’elles a une clé étrangère qui pointe vers le `Course` table. Informations communes aux deux types de cours sont stockées uniquement dans le `Course` table.

[![image12](the-entity-framework-and-aspnet-getting-started-part-6/_static/image4.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image3.png)

Vous pouvez configurer le modèle de données Entity Framework afin que `OnlineCourse` et `OnsiteCourse` entités héritent le `Course` entité. Ce modèle de la génération d’une structure d’héritage d’entité à partir des tables distinctes pour chaque type, chaque table distincte, faire référence à une table qui stocke les données communes à tous les types, est appelé *table par type* l’héritage (TPT).

Modèles d’héritage TPH généralement offrent de meilleures performances dans Entity Framework que les modèles d’héritage TPT, étant donné que les modèles TPT peuvent entraîner des requêtes de jointure complexe. Cette procédure pas à pas montre comment implémenter l’héritage TPH. Vous devez le faire en effectuant les étapes suivantes :

- Créer `Instructor` et `Student` types d’entités qui dérivent de `Person`.
- Propriétés de déplacement qui se rapportent aux entités dérivées à partir de la `Person` entité aux entités dérivées.
- Définir des contraintes sur les propriétés dans les types dérivés.
- Rendre le `Person` entité une entité abstraite.
- Carte chaque dérivée d’entité à la `Person` table avec une condition qui spécifie la façon de déterminer si un `Person` représente ce type dérivé de la ligne.

## <a name="adding-instructor-and-student-entities"></a>Ajout d’entités Instructor et Student

Ouvrez le *SchoolModel.edmx* de fichiers, cliquez sur une zone libre dans le concepteur, sélectionnez **ajouter**, puis sélectionnez **entité***.*

[![Image01](the-entity-framework-and-aspnet-getting-started-part-6/_static/image6.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image5.png)

Dans le **ajouter une entité** boîte de dialogue, nom de l’entité `Instructor` et définir son **type de Base** option `Person`.

[![image02](the-entity-framework-and-aspnet-getting-started-part-6/_static/image8.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image7.png)

Cliquez sur **OK**. Le concepteur crée un `Instructor` entité qui dérive de la `Person` entité. La nouvelle entité n’a pas encore de toutes les propriétés.

[![image03](the-entity-framework-and-aspnet-getting-started-part-6/_static/image10.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image9.png)

Répétez la procédure pour créer un `Student` entité dérive également de `Person`.

Uniquement les formateurs ont des dates d’embauche, donc vous devez déplacer cette propriété à partir de la `Person` entité à la `Instructor` entité. Dans le `Person` entité, avec le bouton droit le `HireDate` propriété et cliquez sur **couper**. Avec le bouton droit puis **propriétés** dans les `Instructor` entité et cliquez sur **coller**.

[![image04](the-entity-framework-and-aspnet-getting-started-part-6/_static/image12.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image11.png)

La date d’embauche un `Instructor` entité ne peut pas être null. Avec le bouton droit le `HireDate` propriété, cliquez sur **propriétés**, puis, dans le **propriétés** modification de la fenêtre `Nullable` à `False`.

[![image05](the-entity-framework-and-aspnet-getting-started-part-6/_static/image14.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image13.png)

Répétez la procédure pour déplacer le `EnrollmentDate` propriété à partir de la `Person` entité à la `Student` entité. Assurez-vous que vous définissez également `Nullable` à `False` pour le `EnrollmentDate` propriété.

Maintenant qu’un `Person` entité dispose uniquement des propriétés qui sont communes aux `Instructor` et `Student` entités (à l’exception de propriétés de navigation, qui vous déplacez pas), l’entité utilisable uniquement comme entité de base dans la structure d’héritage. Par conséquent, vous devez vous assurer qu’elle n’est jamais traitée comme une entité indépendante. Avec le bouton droit le `Person` entité, sélectionnez **propriétés**, puis, dans le **propriétés** fenêtre Modifier la valeur de la **abstraite** propriété  **True**.

[![image06](the-entity-framework-and-aspnet-getting-started-part-6/_static/image16.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image15.png)

## <a name="mapping-instructor-and-student-entities-to-the-person-table"></a>Mappage des entités Instructor et Student pour la Table Person

Maintenant vous devez indiquer la façon de faire la distinction entre Entity Framework `Instructor` et `Student` entités dans la base de données.

Avec le bouton droit le `Instructor` entité et sélectionnez **mappage de Table**. Dans le **détails de mappage** fenêtre, cliquez sur **ajouter une Table ou vue** et sélectionnez **personne**.

[![image07](the-entity-framework-and-aspnet-getting-started-part-6/_static/image18.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image17.png)

Cliquez sur **ajouter une Condition**, puis sélectionnez **HireDate**.

[![image09](the-entity-framework-and-aspnet-getting-started-part-6/_static/image20.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image19.png)

Modification **opérateur** à **est** et **valeur / propriété** à **non Null**.

[![image10](the-entity-framework-and-aspnet-getting-started-part-6/_static/image22.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image21.png)

Répétez la procédure pour le `Students` entité, en spécifiant que cette entité est mappée à la `Person` table lorsque la `EnrollmentDate` colonne n’est pas null. Ensuite, enregistrez et fermez le modèle de données.

Générez le projet afin de créer de nouvelles entités en tant que classes et les rendre disponibles dans le concepteur.

## <a name="using-the-instructor-and-student-entities"></a>À l’aide du formateur et des entités d’étudiant

Lorsque vous avez créé les pages web qui fonctionnent avec des données student et instructor, vous lié aux données à le `Person` du jeu d’entités, et vous avez filtré sur la `HireDate` ou `EnrollmentDate` propriété pour restreindre les données retournées pour les étudiants ou les formateurs. Toutefois, maintenant lorsque vous liez chaque contrôle de source de données pour le `Person` du jeu d’entités, vous pouvez spécifier que seuls `Student` ou `Instructor` types d’entité doivent être sélectionnées. Étant donné que l’Entity Framework sait comment différencier les étudiants et les formateurs dans le `Person` jeu d’entités, vous pouvez supprimer le `Where` paramètres de propriété que vous avez entré manuellement pour ce faire.

Dans le concepteur Visual Studio, vous pouvez spécifier le type de l’entité une `EntityDataSource` contrôle doit sélectionner dans la **EntityTypeFilter** zone de liste déroulante de la `Configure Data Source` Assistant, comme indiqué dans l’exemple suivant.

[![image13](the-entity-framework-and-aspnet-getting-started-part-6/_static/image24.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image23.png)

Puis, dans le **propriétés** fenêtre vous pouvez de supprimer `Where` valeurs de la clause qui ne sont plus nécessaires, comme indiqué dans l’exemple suivant.

[![image14](the-entity-framework-and-aspnet-getting-started-part-6/_static/image26.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image25.png)

Toutefois, étant donné que vous avez modifié le balisage de `EntityDataSource` contrôles à utiliser le `ContextTypeName` attribut, vous ne pouvez pas exécuter le **configurer la Source de données** Assistant sur `EntityDataSource` contrôles que vous avez déjà créé. Par conséquent, vous allez apporter les modifications requises en modifiant le balisage au lieu de cela.

Ouvrez le *Students.aspx* page. Dans le `StudentsEntityDataSource` contrôler, supprimez le `Where` d’attribut et d’ajouter un `EntityTypeFilter="Student"` attribut. Le balisage ressemblera maintenant l’exemple suivant :

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample1.aspx)]

Définition de la `EntityTypeFilter` attribut garantit que les `EntityDataSource` contrôle sélectionne uniquement le type d’entité spécifié. Si vous souhaitez récupérer les deux `Student` et `Instructor` types d’entité, vous ne serez pas définir cet attribut. (Vous avez la possibilité de récupérer plusieurs types d’entités avec une `EntityDataSource` contrôle uniquement si vous utilisez le contrôle d’accès aux données en lecture seule. Si vous utilisez un `EntityDataSource` contrôler pour insérer, mettre à jour ou supprimer des entités et si elle est liée à l’ensemble d’entités peut contenir plusieurs types, vous ne pouvez travailler avec un type d’entité, et vous devez définir cet attribut.)

Répétez la procédure pour le `SearchEntityDataSource` contrôler, sauf supprimer uniquement la partie de la `Where` attribut sélectionne `Student` entités au lieu de supprimer complètement de la propriété. La balise d’ouverture du contrôle maintenant ressemblera à l’exemple suivant :

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample2.aspx)]

Exécutez la page pour vérifier qu’elle fonctionne toujours comme auparavant.

[![image15](the-entity-framework-and-aspnet-getting-started-part-6/_static/image28.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image27.png)

Mettre à jour les pages suivantes que vous avez créé dans les didacticiels antérieures afin qu’ils utilisent le nouveau `Student` et `Instructor` entités à la place de `Person` entités, puis les exécuter pour vérifier qu’elles fonctionnent comme avant :

- Dans *StudentsAdd.aspx*, ajoutez `EntityTypeFilter="Student"` à la `StudentsEntityDataSource` contrôle. Le balisage ressemblera maintenant l’exemple suivant : 

    [!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample3.aspx)]

    [![image16](the-entity-framework-and-aspnet-getting-started-part-6/_static/image30.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image29.png)
- Dans *About.aspx*, ajoutez `EntityTypeFilter="Student"` à la `StudentStatisticsEntityDataSource` contrôler et supprimer `Where="it.EnrollmentDate is not null"`. Le balisage ressemblera maintenant l’exemple suivant : 

    [!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample4.aspx)]

    [![image17](the-entity-framework-and-aspnet-getting-started-part-6/_static/image32.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image31.png)
- Dans *Instructors.aspx* et *InstructorsCourses.aspx*, ajoutez `EntityTypeFilter="Instructor"` à la `InstructorsEntityDataSource` contrôler et supprimer `Where="it.HireDate is not null"`. Le balisage dans *Instructors.aspx* ressemble maintenant à l’exemple suivant : 

    [!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample5.aspx)]

    [![image18](the-entity-framework-and-aspnet-getting-started-part-6/_static/image34.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image33.png)

    Le balisage dans *InstructorsCourses.aspx* ressemble maintenant à l’exemple suivant :

    [!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample6.aspx)]

    [![Image19](the-entity-framework-and-aspnet-getting-started-part-6/_static/image36.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image35.png)

À la suite de ces modifications, vous avez amélioré la facilité de maintenance de l’application Contoso University de plusieurs façons. Vous avez déplacé la logique de sélection et validation hors de la couche d’interface utilisateur (*.aspx* balisage) et la partie intégrante de la couche d’accès aux données. Cela permet d’isoler votre code d’application des modifications que vous pouvez apporter à l’avenir pour le schéma de base de données ou le modèle de données. Par exemple, vous pouvez décider que les étudiants peuvent être engagés comme aides de professeurs et par conséquent, obtenez une date d’embauche. Vous pouvez ensuite ajouter une nouvelle propriété pour différencier les étudiants instructeurs et mettre à jour le modèle de données. Aucun code dans l’application web ne doit remplacer, sauf lorsque vous souhaitez afficher une date d’embauche pour les étudiants. Un autre avantage de l’ajout de `Instructor` et `Student` entités est que votre code est plus facilement compréhensible que lorsqu’il agit de `Person` les objets qui ont été réellement les étudiants ou les formateurs.

Vous avez maintenant vu un moyen d’implémenter un modèle d’héritage dans Entity Framework. Dans ce didacticiel, vous allez apprendre comment utiliser des procédures stockées afin de mieux contrôler la façon dont l’Entity Framework accède à la base de données.

>[!div class="step-by-step"]
[Précédent](the-entity-framework-and-aspnet-getting-started-part-5.md)
[Suivant](the-entity-framework-and-aspnet-getting-started-part-7.md)

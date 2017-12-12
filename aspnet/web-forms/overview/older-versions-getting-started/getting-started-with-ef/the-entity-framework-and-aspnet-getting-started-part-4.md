---
uid: web-forms/overview/older-versions-getting-started/getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-4
title: "Mise en route avec base de données Entity Framework 4.0 tout d’abord et 4 d’ASP.NET Web Forms - partie 4 | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications Web Forms ASP.NET à l’aide d’Entity Framework. L’exemple d’application est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/03/2010
ms.topic: article
ms.assetid: ceb9e60f-957c-4d25-9331-cc527de96a33
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-4
msc.type: authoredcontent
ms.openlocfilehash: 06d129384fc78db21ad1b9224781deab6a0e91a5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="getting-started-with-entity-framework-40-database-first-and-aspnet-4-web-forms---part-4"></a>Mise en route avec base de données Entity Framework 4.0 tout d’abord et 4 les Web Forms ASP.NET - partie 4
====================
Par [Tom Dykstra](https://github.com/tdykstra)

> L’exemple d’application web Contoso University montre comment créer des applications Web Forms ASP.NET à l’aide de l’Entity Framework 4.0 et Visual Studio 2010. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](the-entity-framework-and-aspnet-getting-started-part-1.md)


## <a name="working-with-related-data"></a>Utilisation des données connexes

Dans le didacticiel précédent, vous avez utilisé le `EntityDataSource` contrôle à filtrer, trier et regrouper les données. Dans ce didacticiel, vous allez afficher et mettre à jour les données associées.

Vous allez créer la page de formateurs qui affiche une liste de formateurs. Lorsque vous sélectionnez un formateur, vous voyez une liste de dispensés par ce formateur. Lorsque vous sélectionnez un cours, vous voyez plus d’informations sur le cours et une liste d’étudiants inscrits dans le cours. Vous pouvez modifier le nom de l’enseignant, date d’embauche et affectation d’office. L’attribution d’office est un jeu d’entités distinctes auxquels vous accédez via une propriété de navigation.

Vous pouvez lier des données de référence à des données de détail dans le balisage ou dans le code. Dans cette partie du didacticiel, vous allez utiliser les deux méthodes.

[![Image01](the-entity-framework-and-aspnet-getting-started-part-4/_static/image2.png)](the-entity-framework-and-aspnet-getting-started-part-4/_static/image1.png)

## <a name="displaying-and-updating-related-entities-in-a-gridview-control"></a>Affichage et mise à jour des entités associées dans un contrôle GridView

Créer une nouvelle page web nommée *Instructors.aspx* qui utilise le *Site.Master* page maître et ajoutez le balisage suivant à la `Content` contrôle nommé `Content2`:

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample1.aspx)]

Cette balise crée un `EntityDataSource` contrôle qui sélectionne des formateurs et Active les mises à jour. Le `div` élément configure le balisage pour restituer sur la gauche afin que vous pouvez ajouter une colonne à droite plus tard.

Entre le `EntityDataSource` balisage et la clôture `</div>` , ajoutez le balisage suivant crée un `GridView` contrôle et un `Label` contrôle que vous allez utiliser pour les messages d’erreur :

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample2.aspx)]

Cela `GridView` contrôle permet la sélection de ligne, met en surbrillance la ligne sélectionnée avec une couleur d’arrière-plan en gris clair et spécifie les gestionnaires (sur lequel vous allez créer une version ultérieure) pour le `SelectedIndexChanged` et `Updating` les événements. Il spécifie également `PersonID` pour le `DataKeyNames` propriété, afin que la valeur de clé de la ligne sélectionnée peut être passée à un autre contrôle que vous ajouterez ultérieurement.

La dernière colonne contient attribution du formateur, qui est stockée dans une propriété de navigation de la `Person` entité car elle provient d’une entité associée. Notez que la `EditItemTemplate` élément spécifie `Eval` au lieu de `Bind`, car le `GridView` contrôle ne peut pas lier directement aux propriétés de navigation afin de les mettre à jour. Vous allez mettre à jour l’attribution d’office dans le code. Pour ce faire, vous aurez besoin d’une référence à la `TextBox` contrôle et que vous allez obtenir et l’enregistrer dans le `TextBox` du contrôle `Init` événement.

Suivant le `GridView` contrôle est un `Label` contrôle qui est utilisé pour les messages d’erreur. Du contrôle `Visible` propriété est `false`, et l’état d’affichage est désactivé, afin que l’étiquette s’affiche uniquement lorsque code rend visible en réponse à une erreur.

Ouvrez le *Instructors.aspx.cs* de fichiers et ajoutez le code suivant `using` instruction :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample3.cs)]

Ajoutez un champ de classe privée immédiatement après la déclaration de nom de classe partielle pour contenir une référence à la zone de texte de l’attribution d’office.

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample4.cs)]

Ajouter un stub pour le `SelectedIndexChanged` Gestionnaire d’événements que vous ajouterez du code pour une date ultérieure. Également ajouter un gestionnaire pour l’attribution d’office `TextBox` du contrôle `Init` événement afin que vous pouvez stocker une référence à la `TextBox` contrôle. Cette référence permet d’obtenir la valeur de l’utilisateur a entré pour mettre à jour de l’entité associée à la propriété de navigation.

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample5.cs)]

Vous allez utiliser le `GridView` du contrôle `Updating` événement pour mettre à jour le `Location` propriété associé au `OfficeAssignment` entité. Ajoutez le gestionnaire suivant pour le `Updating` événement :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample6.cs)]

Ce code s’exécute lorsque l’utilisateur clique sur **mise à jour** dans un `GridView` ligne. Le code utilise LINQ to Entities pour récupérer le `OfficeAssignment` entité associé actuel `Person` entité, à l’aide de la `PersonID` de la ligne sélectionnée à partir de l’argument d’événement.

Le code effectue ensuite une des actions suivantes en fonction de la valeur dans la `InstructorOfficeTextBox` contrôle :

- Si la zone de texte a la valeur et n’est pas `OfficeAssignment` entité à mettre à jour, il en crée un.
- Si la zone de texte a la valeur et il existe un `OfficeAssignment` entité, il met à jour le `Location` valeur de propriété.
- Si la zone de texte est vide et une `OfficeAssignment` entité existe, il supprime l’entité.

Après cela, il enregistre les modifications à la base de données. Si une exception se produit, il affiche un message d’erreur.

Exécutez la page.

[![Image02](the-entity-framework-and-aspnet-getting-started-part-4/_static/image4.png)](the-entity-framework-and-aspnet-getting-started-part-4/_static/image3.png)

Cliquez sur **modifier** et changent de tous les champs aux zones de texte.

[![Image03](the-entity-framework-and-aspnet-getting-started-part-4/_static/image6.png)](the-entity-framework-and-aspnet-getting-started-part-4/_static/image5.png)

Modifier un de ces valeurs, y compris **attribution**. Cliquez sur **mise à jour** et vous verrez les modifications soient répercutées dans la liste.

## <a name="displaying-related-entities-in-a-separate-control"></a>Affichage des entités associées dans un contrôle distinct

Chaque formateur peut animer un ou plusieurs cours, vous allez ajouter un `EntityDataSource` contrôle et un `GridView` contrôle de liste les cours associés avec le formateur est sélectionné dans les instructeurs `GridView` contrôle. Pour créer un titre et le `EntityDataSource` pour les entités de cours, ajoutez le balisage suivant entre le message d’erreur `Label` contrôle et la fermeture `</div>` balise :

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample7.aspx)]

Le `Where` paramètre contient la valeur de la `PersonID` du formateur dont la ligne est sélectionnée dans le `InstructorsGridView` contrôle. Le `Where` propriété contient une commande de sous-sélection qui obtient toutes les `Person` entités à partir d’un `Course` l’entité `People` propriété de navigation et sélectionne le `Course` uniquement si d’entité parmi les associés`Person`sélectionné contient des entités `PersonID` valeur.

Pour créer le `GridView` . de contrôle, ajoutez le balisage suivant immédiatement après le `CoursesEntityDataSource` contrôle (avant la fermeture `</div>` balise) :

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample8.aspx)]

Car aucun cours ne seront affichera si aucun formateur n’est sélectionnée, un `EmptyDataTemplate` élément est inclus.

Exécutez la page.

[![Image04](the-entity-framework-and-aspnet-getting-started-part-4/_static/image8.png)](the-entity-framework-and-aspnet-getting-started-part-4/_static/image7.png)

Sélectionnez un formateur qui a un ou plusieurs cours affectés et l’ou les cours apparaissent dans la liste. (Remarque : bien que le schéma de base de données autorise plusieurs cours, données de test fournies avec la base de données aucun formateur est constitué de plusieurs cours. Vous pouvez ajouter des cours à la base de données vous-même à l’aide de la **l’Explorateur de serveurs** fenêtre ou le *CoursesAdd.aspx* page que vous allez ajouter dans un prochain didacticiel.)

[![Image05](the-entity-framework-and-aspnet-getting-started-part-4/_static/image10.png)](the-entity-framework-and-aspnet-getting-started-part-4/_static/image9.png)

Le `CoursesGridView` contrôle affiche uniquement certains champs de cours. Pour afficher tous les détails pour un cours, vous allez utiliser un `DetailsView` contrôle au cours de l’utilisateur sélectionne. Dans *Instructors.aspx*, ajoutez le balisage suivant après la fermeture `</div>` balise (Assurez-vous que vous placez ce balisage **après** balise de l’élément div de fermeture, pas avant) :

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample9.aspx)]

Cette balise crée un `EntityDataSource` contrôle qui est lié à la `Courses` jeu d’entités. Le `Where` propriété sélectionne un cours à l’aide de la `CourseID` la valeur de la ligne sélectionnée dans les cours `GridView` contrôle. Spécifie un gestionnaire pour le `Selected` événement, que vous allez utiliser ultérieurement pour l’affichage des notes des étudiants, qui est un autre niveau inférieur dans la hiérarchie.

Dans *Instructors.aspx.cs*, créer le stub suivant pour le `CourseDetailsEntityDataSource_Selected` (méthode). (Vous devez remplir ce stub plus loin dans ce didacticiel, pour l’instant, vous avez besoin pour que la page est compilé et exécuté.)

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample10.cs)]

Exécutez la page.

[![Image06](the-entity-framework-and-aspnet-getting-started-part-4/_static/image12.png)](the-entity-framework-and-aspnet-getting-started-part-4/_static/image11.png)

Au départ ne sont aucun détail de cours car aucun cours n’est sélectionné. Sélectionnez un formateur qui a un cours affecté, puis un cours pour afficher les détails.

[![Image07](the-entity-framework-and-aspnet-getting-started-part-4/_static/image14.png)](the-entity-framework-and-aspnet-getting-started-part-4/_static/image13.png)

## <a name="using-the-entitydatasource-selected-event-to-display-related-data"></a>À l’aide du contrôle EntityDataSource » « événement sélectionné pour afficher les données associées

Enfin, vous souhaitez afficher tous les participants inscrits et leurs niveaux pour le cours sélectionné. Pour ce faire, vous allez utiliser le `Selected` l’événement de la `EntityDataSource` contrôle lié au cours `DetailsView`.

Dans *Instructors.aspx*, ajoutez le balisage suivant après le `DetailsView` contrôle :

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample11.aspx)]

Cette balise crée un `ListView` contrôle qui affiche une liste d’étudiants et leurs niveaux pour le cours sélectionné. Aucune source de données n’est spécifié, car vous allez databind contrôle dans le code. Le `EmptyDataTemplate` élément fournit un message à afficher lorsque aucun cours n’est sélectionné, dans ce cas, il n’existe aucun élève à afficher. Le `LayoutTemplate` élément crée un tableau HTML pour afficher la liste et le `ItemTemplate` spécifie les colonnes à afficher. L’ID et la note de l’étudiant sont issues de la `StudentGrade` entité et le nom de l’étudiant provient de la `Person` entité Entity Framework rend disponible dans le `Person` propriété de navigation de la `StudentGrade` entité.

Dans *Instructors.aspx.cs*, remplacez l’extraite `CourseDetailsEntityDataSource_Selected` méthode avec le code suivant :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample12.cs)]

L’argument d’événement pour cet événement fournit les données sélectionnées sous la forme d’une collection, ce qui aura zéro si rien n’est sélectionné ou un élément si un `Course` entité est sélectionnée. Si un `Course` entité est sélectionnée, le code utilise la `First` méthode pour convertir la collection un objet unique. Elle obtient ensuite `StudentGrade` entités à partir de la propriété de navigation, les convertit en une collection et lie le `GradesListView` contrôle à la collection.

Cela est suffisant pour afficher grades, mais vous souhaitez vous assurer que le message dans le modèle de données vide est affiché la première fois que la page s’affiche, et chaque fois qu’un cours n’est pas sélectionné. Pour ce faire, créez la méthode suivante, que vous allez appeler à partir de deux emplacements :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample13.cs)]

Appelez cette nouvelle méthode à partir de la `Page_Load` méthode pour afficher la données vides modèle la première fois la page s’affiche. Et l’appeler à partir la `InstructorsGridView_SelectedIndexChanged` méthode étant donné que cet événement est déclenché lorsqu’un formateur est sélectionné, ce qui signifie que les nouveaux cours chargés dans les cours `GridView` contrôle et aucun n’est encore sélectionné. Voici les deux appels :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample14.cs)]

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-4/samples/sample15.cs)]

Exécutez la page.

[![Image08](the-entity-framework-and-aspnet-getting-started-part-4/_static/image16.png)](the-entity-framework-and-aspnet-getting-started-part-4/_static/image15.png)

Sélectionnez un formateur qui a un cours affecté, puis le cours.

[![Image09](the-entity-framework-and-aspnet-getting-started-part-4/_static/image18.png)](the-entity-framework-and-aspnet-getting-started-part-4/_static/image17.png)

Vous savez maintenant quelques manières d’utiliser des données connexes. Dans ce didacticiel, vous allez apprendre à ajouter des relations entre des entités existantes, la suppression des relations et comment ajouter une nouvelle entité qui a une relation à une entité existante.

>[!div class="step-by-step"]
[Précédent](the-entity-framework-and-aspnet-getting-started-part-3.md)
[Suivant](the-entity-framework-and-aspnet-getting-started-part-5.md)

---
uid: web-forms/overview/older-versions-getting-started/continuing-with-ef/using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering
title: "À l’aide d’Entity Framework 4.0 et du contrôle ObjectDataSource, partie 3 : tri et de filtrage | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels s’appuie sur l’application web de l’université Contoso qui est créée par la prise en main de la série de didacticiels Entity Framework 4.0. JE..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/26/2011
ms.topic: article
ms.assetid: 2990bd10-590d-43d5-9529-6b503ce5455d
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/continuing-with-ef/using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering
msc.type: authoredcontent
ms.openlocfilehash: 4accd3381a66bde1f87f0dc7dd95beeb54fcc6a2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-the-entity-framework-40-and-the-objectdatasource-control-part-3-sorting-and-filtering"></a>À l’aide d’Entity Framework 4.0 et du contrôle ObjectDataSource, partie 3 : tri et filtrage
====================
Par [Tom Dykstra](https://github.com/tdykstra)

> Cette série de didacticiels s’appuie sur l’application web de l’université Contoso qui est créée par le [mise en route avec l’Entity Framework 4.0](https://asp.net/entity-framework/tutorials#Getting%20Started) série de didacticiels. Si vous n’avez pas terminé les didacticiels antérieures, comme point de départ pour ce didacticiel vous pouvez [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-97f8ee9a) que vous devriez avoir créé. Vous pouvez également [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-6c7197aa) qui est créé par la série de didacticiels terminée. Si vous avez des questions sur les didacticiels, vous pouvez les valider pour le [forum de ASP.NET Entity Framework](https://forums.asp.net/1227.aspx).


Dans le didacticiel précédent, vous avez implémenté le modèle de référentiel dans une application multicouche web qui utilise Entity Framework et le `ObjectDataSource` contrôle. Ce didacticiel montre comment effectuer le tri et de filtrage et de gérer les scénarios maître / détail. Vous allez ajouter les améliorations suivantes apportées à la *Departments.aspx* page :

- Une zone de texte pour permettre aux utilisateurs de sélectionner les services par nom.
- Une liste de cours pour chaque département, qui est indiqué dans la grille.
- La possibilité de trier en cliquant sur les en-têtes de colonne.

[![Image01](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/_static/image2.png)](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/_static/image1.png)

## <a name="adding-the-ability-to-sort-gridview-columns"></a>Outre la possibilité de trier les colonnes GridView

Ouvrez le *Departments.aspx* page et ajoutez un `SortParameterName="sortExpression"` attribut le `ObjectDataSource` contrôle nommé `DepartmentsObjectDataSource`. (Plus tard, vous allez créer un `GetDepartments` méthode qui accepte un paramètre nommé `sortExpression`.) Le balisage de la balise d’ouverture du contrôle ressemble maintenant à l’exemple suivant.

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample1.aspx)]

Ajouter le `AllowSorting="true"` d’attribut à la balise d’ouverture de la `GridView` contrôle. Le balisage de la balise d’ouverture du contrôle ressemble maintenant à l’exemple suivant.

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample2.aspx)]

Dans *Departments.aspx.cs*, définir l’ordre de tri par défaut en appelant le `GridView` du contrôle `Sort` méthode à partir de la `Page_Load` méthode :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample3.cs)]

Vous pouvez ajouter du code trie ou les filtres dans la classe de logique métier ou de la classe de référentiel. Si vous le faites dans la classe de logique métier, tri ou filtrage du travail est effectué une fois que les données sont récupérées à partir de la base de données, car l’utilisation de la classe de logique métier avec un `IEnumerable` objet retourné par l’espace de stockage. Si vous ajoutez triée et filtrée de code de la classe de l’espace de stockage et de vous faire avant d’une expression LINQ ou de requête d’objet a été converti en un `IEnumerable` de l’objet, vos commandes sont transmises à la base de données pour le traitement, qui est généralement plus efficace. Dans ce didacticiel, vous allez implémenter le tri et le filtrage d’une manière qui provoque le traitement doit être fait par la base de données, autrement dit, dans le référentiel.

Pour ajouter la fonctionnalité de tri, vous devez ajouter une nouvelle méthode à l’interface de référentiel et les classes du référentiel, ainsi que pour la classe de logique métier. Dans le *ISchoolRepository.cs* , ajoutez un nouveau `GetDepartments` méthode qui accepte un `sortExpression` paramètre qui permet de trier la liste des services qui est retournée :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample4.cs)]

Le `sortExpression` paramètre spécifie la colonne de tri et le sens du tri.

Ajouter du code pour la nouvelle méthode le *SchoolRepository.cs* fichier :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample5.cs)]

Modifier existants sans paramètre `GetDepartments` méthode à appeler la nouvelle méthode :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample6.cs)]

Dans le projet de test, ajoutez la nouvelle méthode suivante à *MockSchoolRepository.cs*:

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample7.cs)]

Si vous souhaitez créer des tests unitaires qui dépendaient de cette méthode qui retourne une liste triée, vous devez trier la liste avant de le renvoyer. Vous n’allez pas créer les tests comme cela dans ce didacticiel, afin que la méthode puisse retourner simplement la liste non triée de services.

Dans le *SchoolBL.cs* , ajoutez la nouvelle méthode suivante à la classe de logique métier :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample8.cs)]

Ce code passe le paramètre de tri à la méthode de référentiel.

Exécutez le *Departments.aspx* page.

[![Image02](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/_static/image4.png)](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/_static/image3.png)

Vous pouvez maintenant cliquer sur n’importe quel en-tête de colonne pour trier par colonne. Si la colonne est déjà triée, en cliquant sur l’en-tête inverse le sens de tri.

## <a name="adding-a-search-box"></a>Ajout d’une zone de recherche

Dans cette section, vous allez ajouter une zone de texte de recherche, liez-le à la `ObjectDataSource` contrôler à l’aide d’un paramètre de contrôle et ajoutez une méthode à la classe de logique métier pour prendre en charge le filtrage.

Ouvrez le *Departments.aspx* page et ajoutez le balisage suivant entre le titre et la première `ObjectDataSource` contrôle :

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample9.aspx)]

Dans le `ObjectDataSource` contrôle nommé `DepartmentsObjectDataSource`, procédez comme suit :

- Ajouter un `SelectParameters` , élément pour un paramètre nommé `nameSearchString` qui obtient la valeur entrée dans le `SearchTextBox` contrôle.
- Modifier la `SelectMethod` valeur d’attribut `GetDepartmentsByName`. (Vous allez créer cette méthode ultérieurement.)

Le balisage de la `ObjectDataSource` contrôle ressemble maintenant à l’exemple suivant :

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample10.aspx)]

Dans *ISchoolRepository.cs*, ajoutez un `GetDepartmentsByName` méthode qui accepte à la fois `sortExpression` et `nameSearchString` paramètres :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample11.cs)]

Dans *SchoolRepository.cs*, ajoutez la nouvelle méthode suivante :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample12.cs)]

Ce code utilise un `Where` (méthode) pour sélectionner les éléments qui contiennent la chaîne de recherche. Si la chaîne de recherche est vide, tous les enregistrements seront sélectionnés. Notez que lorsque vous spécifiez la méthode appelle ensemble dans une instruction comme celle-ci (`Include`, puis `OrderBy`, puis `Where`), la `Where` méthode doit toujours être le dernier.

Modifier existants `GetDepartments` méthode qui accepte un `sortExpression` paramètre pour appeler la nouvelle méthode :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample13.cs)]

Dans *MockSchoolRepository.cs* dans le projet de test, ajoutez la nouvelle méthode suivante :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample14.cs)]

Dans *SchoolBL.cs*, ajoutez la nouvelle méthode suivante :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample15.cs)]

Exécutez le *Departments.aspx* page et entrez une chaîne de recherche pour vous assurer que la logique de sélection fonctionne. Laissez la zone de texte vide et effectuez une recherche pour vous assurer que tous les enregistrements sont retournés.

[![Image03](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/_static/image6.png)](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/_static/image5.png)

## <a name="adding-a-details-column-for-each-grid-row"></a>Ajout d’une colonne de détails pour chaque ligne de grille

Ensuite, vous souhaitez afficher tous les cours pour chaque département, affiché dans la cellule de la grille de droite. Pour ce faire, vous allez utiliser une liste imbriquée `GridView` contrôle et lier les données à des données à partir de la `Courses` propriété de navigation de la `Department` entité.

Ouvrez *Departments.aspx* et dans le balisage de la `GridView` contrôler, spécifiez un gestionnaire pour le `RowDataBound` événement. Le balisage de la balise d’ouverture du contrôle ressemble maintenant à l’exemple suivant.

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample16.aspx)]

Ajouter un nouveau `TemplateField` élément après le `Administrator` champ de modèle :

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample17.aspx)]

Cette balise crée une liste imbriquée `GridView` contrôle qui affiche le numéro et le titre d’une liste de cours. Il ne spécifie pas une source de données, car vous allez databind dans le code dans le `RowDataBound` gestionnaire.

Ouvrez *Departments.aspx.cs* et ajoutez le gestionnaire suivant pour le `RowDataBound` événement :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample18.cs)]

Ce code obtient le `Department` convertit d’entité à partir des arguments d’événement, le `Courses` propriété de navigation pour un `List` collection et établit l’imbriquée `GridView` à la collection.

Ouvrez le *SchoolRepository.cs* fichier et spécifiez un chargement hâtif pour le `Courses` propriété de navigation en appelant le `Include` méthode dans la requête d’objet que vous créez dans le `GetDepartmentsByName` (méthode). Le `return` instruction dans le `GetDepartmentsByName` méthode ressemble maintenant à l’exemple suivant.

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/samples/sample19.cs)]

Exécutez la page. Outre le tri et filtrage que vous avez ajouté précédemment, le contrôle GridView affiche maintenant les détails du cours imbriquée pour chaque service.

[![Image01](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/_static/image8.png)](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering/_static/image7.png)

Cette étape termine l’introduction aux scénarios de tri, filtrage et maître / détail. Dans l’étape suivante du didacticiel, vous allez apprendre à gérer l’accès concurrentiel.

>[!div class="step-by-step"]
[Précédent](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests.md)
[Suivant](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application.md)

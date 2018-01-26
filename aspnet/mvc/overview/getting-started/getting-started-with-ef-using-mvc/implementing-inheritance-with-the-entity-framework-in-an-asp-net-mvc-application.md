---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
title: "Implémentation de l’héritage avec Entity Framework 6 dans une Application ASP.NET MVC 5 (11 12) | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/07/2014
ms.topic: article
ms.assetid: 08834147-77ec-454a-bb7a-d931d2a40dab
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 118233338112a71216b909b1dabed2333bfa235e
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="implementing-inheritance-with-the-entity-framework-6-in-an-aspnet-mvc-5-application-11-of-12"></a>Implémentation de l’héritage avec Entity Framework 6 dans une Application ASP.NET MVC 5 (11 12)
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) ou [télécharger le PDF](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio 2013. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).


Dans le didacticiel précédent vous gérée des exceptions d’accès concurrentiel. Ce didacticiel vous indiquera comment implémenter l’héritage dans le modèle de données.

Dans la programmation orientée objet, vous pouvez utiliser [héritage](http://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) pour faciliter la [réutilisation du code](http://en.wikipedia.org/wiki/Code_reuse). Dans ce didacticiel, vous allez modifier le `Instructor` et `Student` afin qu’ils dérivent des classes un `Person` classe qui contient les propriétés de base `LastName` qui sont communs aux instructeurs et les étudiants. Vous ne seront pas ajouter ou modifier des pages web, mais vous allez modifier la partie du code et ces modifications sont répercutées automatiquement dans la base de données.

## <a name="options-for-mapping-inheritance-to-database-tables"></a>Options pour le mappage d’héritage pour les tables de base de données

Le `Instructor` et `Student` des classes dans le `School` modèle de données possèdent plusieurs propriétés qui sont identiques :

![Student_and_Instructor_classes](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

Supposons que vous souhaitez éliminer le code redondant pour les propriétés qui sont partagées par les `Instructor` et `Student` entités. Ou vous souhaitez écrire un service qui peut mettre en forme les noms sans soins si le nom provient d’un formateur ou un étudiant. Vous pouvez créer un `Person` classe qui contient uniquement les propriétés partagées de base, puis apportez les `Instructor` et `Student` entités héritent de cette classe de base, comme indiqué dans l’illustration suivante :

![Student_and_Instructor_classes_deriving_from_Person_class](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

Il existe plusieurs façons que cette structure d’héritage peut être représentée dans la base de données. Vous pouvez avoir un `Person` table qui consacrée des informations sur les étudiants et instructeurs dans une table unique. Certaines colonnes pourraient s’appliquent uniquement aux instructeurs (`HireDate`), certains uniquement pour les étudiants (`EnrollmentDate`), certaines pour les deux (`LastName`, `FirstName`). En règle générale, vous devriez un *discriminateur* colonne afin d’indiquer le type de chaque ligne représente. Par exemple, la colonne de discriminateur peut-être « Formateur » pour les enseignants et « Étudiant » pour les étudiants.

![Table-per-hierarchy_example](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

Ce modèle de la génération d’une structure d’héritage d’entité à partir d’une table de base de données unique est appelé *table par hiérarchie* l’héritage (TPH).

Une alternative consiste à rendre la base de données ressemble plus à la structure d’héritage. Par exemple, vous pouvez avoir seulement les champs de nom dans la `Person` table et avez distinct `Instructor` et `Student` tables avec des champs de date.

![Table-per-type_inheritance](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

Ce modèle de configuration d’une table de base de données pour chaque classe d’entité est appelé *table par type* l’héritage (TPT).

Encore une autre option consiste à mapper tous les types non abstraits à des tables individuelles. Toutes les propriétés d’une classe, y compris les propriétés héritées, mappent aux colonnes de la table correspondante. Ce modèle est appelé l’héritage de Table-par classe concrète (TPC). Si vous avez implémenté l’héritage TPC pour le `Person`, `Student`, et `Instructor` classes comme indiqué précédemment, le `Student` et `Instructor` tables ressemble pas différents après l’implémentation de l’héritage de leur.

TPC et modèles d’héritage TPH généralement offrent de meilleures performances dans Entity Framework que les modèles d’héritage TPT, étant donné que les modèles TPT peuvent entraîner des requêtes de jointure complexe.

Ce didacticiel montre comment implémenter l’héritage TPH. TPH étant le modèle d’héritage dans Entity Framework, il vous est de créer un `Person` de classe, de modifier le `Instructor` et `Student` comme classes de dérivation `Person`, ajouter la nouvelle classe par le `DbContext`et créer un migration. (Pour plus d’informations sur la façon d’implémenter les autres modèles d’héritage, consultez [mappage de l’héritage Table par Type (TPT)](https://msdn.microsoft.com/data/jj591617#2.5) et [mappage de l’héritage de classe concrète-par-Table (TPC)](https://msdn.microsoft.com/data/jj591617#2.6) sur le site MSDN Entity Framework documentation sur.)

## <a name="create-the-person-class"></a>Créer la classe de personne

Dans le *modèles* dossier, créez *Person.cs* et remplacez le code de modèle par le code suivant :

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

## <a name="make-student-and-instructor-classes-inherit-from-person"></a>Faites en sorte que les classes de Student et Instructor hérite de personne

Dans *Instructor.cs*, dériver le `Instructor` classe à partir de la `Person` classe et supprimez les champs de clé et le nom. Le code doit ressembler à l’exemple suivant :

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Apporter des modifications similaires à *Student.cs*. La `Student` classe doit ressembler à l’exemple suivant :

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

## <a name="add-the-person-entity-type-to-the-model"></a>Ajouter le Type d’entité personne au modèle

Dans *SchoolContext.cs*, ajoutez un `DbSet` propriété pour la `Person` type d’entité :

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

C’est tout ce qui a besoin d’Entity Framework pour configurer l’héritage table par hiérarchie. Comme vous le verrez, lorsque la base de données est mise à jour, il aura un `Person` de table à la place de la `Student` et `Instructor` tables.

## <a name="create-and-update-a-migrations-file"></a>Créer et mettre à jour un fichier de migration

Dans Package Manager Console (PMC), entrez la commande suivante :

`Add-Migration Inheritance`

Exécuter le `Update-Database` dans PMC. La commande échoue à ce stade parce que nous avons des données existantes migrations ne sait pas comment gérer. Vous obtenez un message d’erreur tel que le suivant :

> *Impossible de supprimer l’objet ' dbo. Formateur ', car elle est référencée par une contrainte FOREIGN KEY.*


Ouvrez *Migrations\&lt ; horodatage&gt;\_Inheritance.cs* et remplacez le `Up` méthode avec le code suivant :

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

Ce code prend en charge les tâches de mise à jour de base de données suivantes :

- Supprime les contraintes de clé étrangère et les index qui pointent vers la table d’étudiants.
- Renomme la table de formateurs en tant que personne et apporte les modifications nécessaires pour stocker les données de l’étudiant :

    - Ajoute EnrollmentDate nullable pour les étudiants.
    - Ajoute la colonne de discriminateur pour indiquer si une ligne est pour un étudiant ou un formateur.
    - Rend HireDate nullable étant donné que les lignes de l’étudiant n’ont des dates d’embauche.
    - Ajoute un champ temporaire qui sera utilisé pour mettre à jour les clés étrangères qui pointent vers les étudiants. Lorsque vous copiez des étudiants dans la table Person ils obtenez de nouvelles valeurs de clé primaires.
- Copie des données à partir de la table de l’étudiant dans la table Person. Cela provoque des étudiants obtenir attribué les nouvelles valeurs de clé primaires.
- Résout des valeurs de clés étrangères qui pointent vers les étudiants.
- Crée de nouveau les contraintes de clé étrangère et des index, désormais de les utiliser pour la table Person.

(Si vous aviez utilisé des GUID au lieu de l’entier en tant que le type de clé primaire, les valeurs de clé primaire étudiant n’ont pas à modifier, et plusieurs de ces étapes a été omises).

Exécutez le `update-database` réexécutez la commande.

(Dans un système de production vous fera modifications correspondantes apportées à la méthode vers le bas dans le cas où vous deviez jamais qui permet de revenir à la version précédente de la base de données. Pour ce didacticiel vous n’utiliserez pas la méthode vers le bas.)

> [!NOTE]
> Il est possible d’obtenir d’autres erreurs lors de la migration des données et apporter des modifications de schéma. Si vous obtenez des erreurs de migration vous ne peut pas résoudre, vous pouvez continuer avec le didacticiel en modifiant la chaîne de connexion dans le *Web.config* de fichiers ou en supprimant la base de données. L’approche la plus simple consiste à renommer la base de données dans le *Web.config* fichier. Par exemple, modifiez le nom de la base de données à ContosoUniversity2 comme indiqué dans l’exemple suivant :
> 
> [!code-xml[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.xml?highlight=2)]
> 
> Avec une base de données, il n’existe pas de données à migrer et le `update-database` commande est beaucoup plus susceptible de se terminer sans erreur. Pour obtenir des instructions sur la suppression de la base de données, consultez [le déplacement d’une base de données à partir de Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/). Si vous adoptez cette approche pour pouvoir continuer avec le didacticiel, ignorez l’étape de déploiement à la fin de ce didacticiel ou déployer un nouveau site et de la base de données. Si vous déployez une mise à jour sur le même site que vous avez été déploiement déjà, EF obtiendrez la même erreur lorsqu’elle s’exécute automatiquement les migrations. Si vous souhaitez corriger une erreur de migration, cette ressource est un des forums d’Entity Framework ou StackOverflow.com.


## <a name="testing"></a>Test

Exécuter le site et essayez différentes pages. Tout fonctionne comme auparavant.

Dans **l’Explorateur de serveurs** développez **données Connections\SchoolContext** , puis **Tables**, et vous constatez que la **étudiant** et **Formateur** tables ont été remplacés par un **personne** table. Développez le **personne** table et que vous consultez dont toutes les colonnes utilisées dans les **Student** et **formateur** tables.

![Server_Explorer_showing_Person_table](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)

Avec le bouton droit de la table Person, puis cliquez sur **afficher les données de Table** pour afficher la colonne de discriminateur.

![](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

Le diagramme suivant illustre la structure de la base de données School :

![School_database_diagram](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

## <a name="deploy-to-azure"></a>Déployer vers Azure

Cette section, vous devez avoir terminé le paramètre facultatif **déploiement de l’application dans Azure** section [partie 3, tri, filtrage et la pagination](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md) de cette série de didacticiels. Si vous aviez des erreurs de migrations résolus en supprimant la base de données dans votre projet local, ignorez cette étape ; ou créer un nouveau site et la base de données et les déployer vers le nouvel environnement.

1. Dans Visual Studio, cliquez sur le projet dans **l’Explorateur de solutions** et sélectionnez **publier** dans le menu contextuel.  
  
    ![Publier dans le menu contextuel du projet](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image8.png)
2. Cliquez sur **Publier**.  
  
    ![publish](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)  
  
 L’application Web s’ouvre dans votre navigateur par défaut.
3. Tester l’application pour vérifier qu’il fonctionne.

    La première fois que vous exécutez une page qui accède à la base de données, Entity Framework exécute toutes les migrations `Up` méthodes nécessaires à la mise à jour avec le modèle de données actuel de la base de données.

## <a name="summary"></a>Récapitulatif

Vous avez implémenté l’héritage table par hiérarchie pour le `Person`, `Student`, et `Instructor` classes. Pour plus d’informations sur cette modification et autres structures de l’héritage, consultez [modèle d’héritage TPT](https://msdn.microsoft.com/data/jj618293) et [modèle d’héritage TPH](https://msdn.microsoft.com/data/jj618292) sur MSDN. Dans l’étape suivante du didacticiel, vous allez apprendre à gérer une variété de scénarios de Entity Framework relativement avancés.

Vous trouverez des liens vers d’autres ressources Entity Framework dans le [ASP.NET Data Access - ressources recommandées](../../../../whitepapers/aspnet-data-access-content-map.md).

>[!div class="step-by-step"]
[Précédent](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
[Suivant](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)

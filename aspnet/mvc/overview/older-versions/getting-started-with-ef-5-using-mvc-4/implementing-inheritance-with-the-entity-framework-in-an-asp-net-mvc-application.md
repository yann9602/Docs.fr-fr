---
uid: mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
title: "Implémentation de l’héritage avec Entity Framework dans une Application ASP.NET MVC (8 sur 10) | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/30/2013
ms.topic: article
ms.assetid: a5c3feff-5335-4cdd-a97d-f7a8785c2494
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 54e46c6f996b6fe86a227c851562e61678b02780
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="implementing-inheritance-with-the-entity-framework-in-an-aspnet-mvc-application-8-of-10"></a>Implémentation de l’héritage avec Entity Framework dans une Application ASP.NET MVC (8 sur 10)
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/Getting-Started-with-dd0e2ed8)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio 2012. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md). Vous pouvez démarrer la série de didacticiels à partir du début ou [télécharger un projet de démarrage pour ce chapitre](building-the-ef5-mvc4-chapter-downloads.md) et Démarrer ici.
> 
> > [!NOTE] 
> > 
> > Si vous rencontrez un problème que vous ne pouvez pas résoudre, [télécharger le chapitre terminé](building-the-ef5-mvc4-chapter-downloads.md) et essayez de reproduire le problème. En règle générale, vous trouverez la solution au problème en comparant votre code pour le code terminé. Pour certaines erreurs courantes et comment les résoudre, consultez [erreurs et des solutions de contournement.](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#errors)


Dans le didacticiel précédent vous gérée des exceptions d’accès concurrentiel. Ce didacticiel vous indiquera comment implémenter l’héritage dans le modèle de données.

Dans la programmation orientée objet, vous pouvez utiliser l’héritage d’éliminer le code redondant. Dans ce didacticiel, vous allez modifier le `Instructor` et `Student` afin qu’ils dérivent des classes un `Person` classe qui contient les propriétés de base `LastName` qui sont communs aux instructeurs et les étudiants. Vous ne seront pas ajouter ou modifier des pages web, mais vous allez modifier la partie du code et ces modifications sont répercutées automatiquement dans la base de données.

## <a name="table-per-hierarchy-versus-table-per-type-inheritance"></a>Table par hiérarchie et héritage Table par Type

Dans la programmation orientée objet, vous pouvez utiliser l’héritage pour le rendre plus facile de travailler avec les classes connexes. Par exemple, le `Instructor` et `Student` des classes dans le `School` modèle de données partagent plusieurs propriétés, ce qui entraîne le code redondant :

![Student_and_Instructor_classes](https://asp.net/media/2578113/Windows-Live-Writer_58f5a93579b2_CC7B_Student_and_Instructor_classes_e7a32f99-8bc4-48ce-aeaf-216a18071a8b.png)

Supposons que vous souhaitez éliminer le code redondant pour les propriétés qui sont partagées par les `Instructor` et `Student` entités. Vous pouvez créer un `Person` classe qui contient uniquement les propriétés partagées de base, puis apportez les `Instructor` et `Student` entités héritent de cette classe de base, comme indiqué dans l’illustration suivante :

![Student_and_Instructor_classes_deriving_from_Person_class](https://asp.net/media/2578119/Windows-Live-Writer_58f5a93579b2_CC7B_Student_and_Instructor_classes_deriving_from_Person_class_671d708c-cbb8-454a-a8f8-c2d99439acd9.png)

Il existe plusieurs façons que cette structure d’héritage peut être représentée dans la base de données. Vous pouvez avoir un `Person` table qui consacrée des informations sur les étudiants et instructeurs dans une table unique. Certaines colonnes pourraient s’appliquent uniquement aux instructeurs (`HireDate`), certains uniquement pour les étudiants (`EnrollmentDate`), certaines pour les deux (`LastName`, `FirstName`). En règle générale, vous devriez un *discriminateur* colonne afin d’indiquer le type de chaque ligne représente. Par exemple, la colonne de discriminateur peut-être « Formateur » pour les enseignants et « Étudiant » pour les étudiants.

![Table-par-hierarchy_example](https://asp.net/media/2578125/Windows-Live-Writer_58f5a93579b2_CC7B_Table-per-hierarchy_example_244067cd-b451-4e9b-9595-793b9afca505.png)

Ce modèle de la génération d’une structure d’héritage d’entité à partir d’une table de base de données unique est appelé *table par hiérarchie* l’héritage (TPH).

Une alternative consiste à rendre la base de données ressemble plus à la structure d’héritage. Par exemple, vous pouvez avoir seulement les champs de nom dans la `Person` table et avez distinct `Instructor` et `Student` tables avec des champs de date.

![Table-par-type_inheritance](https://asp.net/media/2578131/Windows-Live-Writer_58f5a93579b2_CC7B_Table-per-type_inheritance.png)

Ce modèle de configuration d’une table de base de données pour chaque classe d’entité est appelé *table par type* l’héritage (TPT).

Modèles d’héritage TPH généralement offrent de meilleures performances dans Entity Framework que les modèles d’héritage TPT, étant donné que les modèles TPT peuvent entraîner des requêtes de jointure complexe. Ce didacticiel montre comment implémenter l’héritage TPH. Vous devez le faire en effectuant les étapes suivantes :

- Créer un `Person` de classe et de modifier le `Instructor` et `Student` comme classes de dérivation `Person`.
- Ajoutez le code de mappage de modèle à base de données à la classe de contexte de base de données.
- Modification `InstructorID` et `StudentID` références tout au long du projet pour `PersonID`.

## <a name="creating-the-person-class"></a>Création de la classe de personne

 Remarque : Vous ne pourrez compiler le projet après avoir créé les classes ci-dessous jusqu'à ce que vous mettez à jour les contrôleurs qui utilise ces classes. 

Dans le *modèles* dossier, créez *Person.cs* et remplacez le code de modèle par le code suivant :

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

Dans *Instructor.cs*, dériver le `Instructor` classe à partir de la `Person` classe et supprimez les champs de clé et le nom. Le code doit ressembler à l’exemple suivant :

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Apporter des modifications similaires à *Student.cs*. La `Student` classe doit ressembler à l’exemple suivant :

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

## <a name="adding-the-person-entity-type-to-the-model"></a>Ajout du Type d’entité Person au modèle

Dans *SchoolContext.cs*, ajoutez un `DbSet` propriété pour la `Person` type d’entité :

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

C’est tout ce qui a besoin d’Entity Framework pour configurer l’héritage table par hiérarchie. Comme vous le verrez, lorsque la base de données est recréé, il aura un `Person` de table à la place de la `Student` et `Instructor` tables.

## <a name="changing-instructorid-and-studentid-to-personid"></a>La modification InstructorID et StudentID à PersonID

Dans *SchoolContext.cs*, dans l’instruction de mappage en cours de formation, modifiez `MapRightKey("InstructorID")` à `MapRightKey("PersonID")`:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=4)]

Cette modification n’est pas nécessaire ; Il modifie uniquement le nom de la colonne InstructorID dans la table de jointure plusieurs-à-plusieurs. Si vous avez laissé le nom que InstructorID, l’application serait fonctionne toujours correctement. Voici le *SchoolContext.cs*:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs?highlight=15,24)]

Ensuite, vous devez modifier `InstructorID` à `PersonID` et `StudentID` à `PersonID` tout au long du projet ***sauf*** dans les fichiers horodatés les migrations dans le *Migrations* dossier. Pour ce faire, vous allez trouver et ouvrir uniquement les fichiers qui doivent être modifiées, puis effectuer une modification globale sur les fichiers ouverts. Le seul fichier dans le *Migrations* dossier, vous devez modifier est *Migrations\Configuration.cs.*

1. > [!IMPORTANT]
 > Commencez par fermer tous les fichiers ouverts dans Visual Studio.
2. Cliquez sur **rechercher et remplacer--rechercher tous les fichiers** dans les **modifier** menu et recherchez tous les fichiers dans le projet contenant `InstructorID`.  
  
    ![](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)
3. Ouvrez chaque fichier dans le **résultats de la recherche** fenêtre ***sauf*** le &lt;horodatage&gt;*\_.cs* des fichiers de migration dans le *Migrations* dossier, en double-cliquant sur une ligne pour chaque fichier.  
  
    ![](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)
4. Ouvrez le **remplacer dans les fichiers** boîte de dialogue et la modification **Regarder dans** à **tous les Documents ouverts**.
5. Utilisez le **remplacer dans les fichiers** boîte de dialogue pour modifier toutes les `InstructorID` à`PersonID.`  
  
    ![](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)
6. Rechercher tous les fichiers dans le projet contenant `StudentID`.
7. Ouvrez chaque fichier dans le **résultats de la recherche** fenêtre ***sauf*** le &lt;horodatage&gt;*\_\*.cs* les fichiers de migration dans le *Migrations* dossier, en double-cliquant sur une ligne pour chaque fichier.  
  
    ![](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)
8. Ouvrez le **remplacer dans les fichiers** boîte de dialogue et la modification **Regarder dans** à **tous les Documents ouverts**.
9. Utilisez le **remplacer dans les fichiers** boîte de dialogue pour modifier toutes les `StudentID` à `PersonID`.   
  
    ![](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)
10. Générez le projet.

(Notez que cela présente un *inconvénient* de la `classnameID` modèle de dénomination des clés primaires. Si vous l’aviez nommée sans le préfixe le nom de classe, ID de clés primaires *aucune* renommer seraient nécessaire maintenant.)

## <a name="create-and-update-a-migrations-file"></a>Créer et mettre à jour un fichier de migration

Dans Package Manager Console (PMC), entrez la commande suivante :

`Add-Migration Inheritance`

Exécuter le `Update-Database` dans PMC. La commande échoue à ce stade parce que nous avons des données existantes migrations ne sait pas comment gérer. Vous obtenez l’erreur suivante :

*L’instruction ALTER TABLE est en conflit avec la contrainte de clé étrangère « FK\_dbo. Service\_dbo. Personne\_PersonID ». Le conflit s’est produite dans la base de données « ContosoUniversity », table « dbo. Personne », colonne « PersonID ».*

Ouvrez *Migrations\&lt ; horodatage&gt;\_Inheritance.cs* et remplacez le `Up` méthode avec le code suivant :

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=25,29-40)]

Exécutez le `update-database` réexécutez la commande.

> [!NOTE]
> Il est possible d’obtenir d’autres erreurs lors de la migration des données et apporter des modifications de schéma. Si vous obtenez des erreurs de migration vous ne peut pas résoudre, vous pouvez continuer avec le didacticiel en modifiant la chaîne de connexion dans le *Web.config* fichier ou la suppression de la base de données. L’approche la plus simple consiste à renommer la base de données dans le *Web.config* fichier. Par exemple, modifier le nom de la base de données pour CU\_de test comme indiqué dans l’exemple suivant :
> 
> [!code-xml[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.xml?highlight=1-2)]
> 
> Avec une base de données, il n’existe pas de données à migrer et le `update-database` commande est beaucoup plus susceptible de se terminer sans erreur. Pour obtenir des instructions sur la suppression de la base de données, consultez [le déplacement d’une base de données à partir de Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/). Si vous adoptez cette approche pour pouvoir continuer avec le didacticiel, ignorez l’étape de déploiement à la fin de ce didacticiel, étant donné que le site déployé obtiendriez la même erreur lorsqu’elle s’exécute automatiquement les migrations. Si vous souhaitez corriger une erreur de migration, cette ressource est un des forums d’Entity Framework ou StackOverflow.com.


## <a name="testing"></a>Test

Exécuter le site et essayez différentes pages. Tout fonctionne comme auparavant.

Dans **l’Explorateur de serveurs** développez **SchoolContext** , puis **Tables**, et vous constatez que la **Student** et **formateur**  tables ont été remplacés par un **personne** table. Développez le **personne** table et que vous consultez dont toutes les colonnes utilisées dans les **Student** et **formateur** tables.

![Server_Explorer_showing_Person_table](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

Avec le bouton droit de la table Person, puis cliquez sur **afficher les données de Table** pour afficher la colonne de discriminateur.

![](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

Le diagramme suivant illustre la structure de la base de données School :

![School_database_diagram](https://asp.net/media/2578143/Windows-Live-Writer_58f5a93579b2_CC7B_School_database_diagram_6350a801-7199-413f-bbac-4a2009ed19d7.png)

## <a name="summary"></a>Résumé

L’héritage table par hiérarchie a maintenant été implémentée pour le `Person`, `Student`, et `Instructor` classes. Pour plus d’informations sur cette modification et autres structures de l’héritage, consultez [stratégies de mappage d’héritage](https://weblogs.asp.net/manavi/archive/2010/12/24/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-1-table-per-hierarchy-tph.aspx) sur le blog de Morteza Manavi. Dans l’étape suivante du didacticiel, vous verrez quelques méthodes permettant d’implémenter le référentiel et une unité de travail des modèles.

Vous trouverez des liens vers d’autres ressources Entity Framework dans le [ASP.NET Data Access Content Map](../../../../whitepapers/aspnet-data-access-content-map.md).

>[!div class="step-by-step"]
[Précédent](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
[Suivant](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application.md)

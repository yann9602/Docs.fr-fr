---
title: "Cœur de ASP.NET MVC avec EF Core - héritage - 9, 10"
author: tdykstra
description: "Ce didacticiel vous indiquera comment implémenter l’héritage dans le modèle de données, à l’aide d’Entity Framework Core dans une application ASP.NET Core."
keywords: "Héritage ASP.NET Core, Entity Framework Core,"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 41dc0db7-6f17-453e-aba6-633430609c74
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 6102b426cb5aff78fedb9389df229cd8100e4f36
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2017
---
# <a name="inheritance---ef-core-with-aspnet-core-mvc-tutorial-9-of-10"></a>Héritage - Core EF avec le didacticiel d’ASP.NET MVC de base (9 sur 10)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).

Dans le didacticiel précédent, vous avez géré les exceptions d’accès concurrentiel. Ce didacticiel vous indiquera comment implémenter l’héritage dans le modèle de données.

Dans la programmation orientée objet, vous pouvez utiliser l’héritage pour faciliter la réutilisation de code. Dans ce didacticiel, vous allez modifier le `Instructor` et `Student` afin qu’ils dérivent des classes un `Person` classe qui contient les propriétés de base `LastName` qui sont communs aux instructeurs et les étudiants. Vous ne seront pas ajouter ou modifier des pages web, mais vous allez modifier la partie du code et ces modifications sont répercutées automatiquement dans la base de données.

## <a name="options-for-mapping-inheritance-to-database-tables"></a>Options pour le mappage d’héritage pour les tables de base de données

Le `Instructor` et `Student` classes dans le modèle de données School ont plusieurs propriétés qui sont identiques :

![Classes de Student et Instructor](inheritance/_static/no-inheritance.png)

Supposons que vous souhaitez éliminer le code redondant pour les propriétés qui sont partagées par les `Instructor` et `Student` entités. Ou vous souhaitez écrire un service qui peut mettre en forme les noms sans soins si le nom provient d’un formateur ou un étudiant. Vous pouvez créer un `Person` classe de base qui contient uniquement les propriétés partagées, puis apportez les `Instructor` et `Student` classes héritent de cette classe de base, comme indiqué dans l’illustration suivante :

![Classes de Student et Instructor dérivant de la classe Person](inheritance/_static/inheritance.png)

Il existe plusieurs façons que cette structure d’héritage peut être représentée dans la base de données. Vous pourriez avoir une table de la personne qui inclut des informations sur les étudiants et instructeurs dans une table unique. Certaines des colonnes impossible applique uniquement aux instructeurs (HireDate), certains uniquement pour les étudiants (EnrollmentDate), certains pour les deux (nom, prénom). En règle générale, vous devriez pour indiquer le type de chaque ligne représente une colonne de discriminateur. Par exemple, la colonne de discriminateur peut-être « Formateur » pour les enseignants et « Étudiant » pour les étudiants.

![Exemple de table par hiérarchie](inheritance/_static/tph.png)

Ce modèle de la génération d’une structure d’héritage d’entité à partir d’une table de base de données unique est appelé l’héritage table par hiérarchie (TPH).

Une alternative consiste à rendre la base de données ressemble plus à la structure d’héritage. Par exemple, vous pourriez uniquement les champs nom de la table Person et ont des tables distinctes Instructor et Student avec les champs de date.

![Héritage TPT (table par type)](inheritance/_static/tpt.png)

Ce modèle de configuration d’une table de base de données pour chaque classe d’entité est appelé table par héritage de type (TPT).

Encore une autre option consiste à mapper tous les types non abstraits à des tables individuelles. Toutes les propriétés d’une classe, y compris les propriétés héritées, mappent aux colonnes de la table correspondante. Ce modèle est appelé l’héritage de Table-par classe concrète (TPC). Si vous avez implémenté l’héritage pour les classes de personne, Student et Instructor TPC comme indiqué précédemment, les tables Student et Instructor ne ressemble pas différents après l’implémentation de l’héritage de leur.

Les modèles d’héritage TPC et TPH remettre généralement meilleures performances que les modèles d’héritage TPT, étant donné que les modèles TPT peuvent entraîner des requêtes de jointure complexe.

Ce didacticiel montre comment implémenter l’héritage TPH. TPH est un modèle d’héritage uniquement qui prend en charge de l’Entity Framework Core.  Ce que vous allez faire est de créer un `Person` de classe, de modifier le `Instructor` et `Student` comme classes de dérivation `Person`, ajouter la nouvelle classe par le `DbContext`et créer une migration.

> [!TIP] 
> Pensez à enregistrer une copie du projet avant d’apporter les modifications suivantes.  Si vous rencontrez des problèmes et devez recommencer, il sera alors plus facile de démarrer à partir du projet enregistré au lieu d’inverser les opérations effectuées pour ce didacticiel ou en accédant au début de la série entière.

## <a name="create-the-person-class"></a>Créer la classe de personne

Dans le dossier de modèles, créer Person.cs et remplacez le code de modèle par le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Person.cs)]

## <a name="make-student-and-instructor-classes-inherit-from-person"></a>Faites en sorte que les classes de Student et Instructor hérite de personne

Dans *Instructor.cs*, dérivez la classe de formateur de la classe de personne et supprimer les champs de clé et le nom. Le code doit ressembler à l’exemple suivant :

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

Apporter les mêmes modifications dans *Student.cs*.

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-the-person-entity-type-to-the-data-model"></a>Ajouter le type d’entité personne au modèle de données

Ajouter le type d’entité personne *SchoolContext.cs*. Les nouvelles lignes sont mises en surbrillance.

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

C’est tout ce qui a besoin d’Entity Framework pour configurer l’héritage table par hiérarchie. Comme vous le verrez, lorsque la base de données est mise à jour, il aura une table de la personne à la place les tables Student et formateur.

## <a name="create-and-customize-migration-code"></a>Créer et personnaliser le code de la migration

Enregistrez vos modifications et générez le projet. Ouvrez la fenêtre de commande dans le dossier du projet, puis entrez la commande suivante :

```console
dotnet ef migrations add Inheritance
```

N’exécutez pas le `database update` commande encore. Cette commande entraîne de perte de données, car il supprime la table de formateurs et les renommer la table étudiant à personne. Vous devez fournir un code personnalisé pour préserver les données existantes.

Ouvrez *Migrations /\<timestamp > _Inheritance.cs* et remplacez le `Up` méthode avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

Ce code prend en charge les tâches de mise à jour de base de données suivantes :

* Supprime les contraintes de clé étrangère et les index qui pointent vers la table d’étudiants.

* Renomme la table de formateurs en tant que personne et apporte les modifications nécessaires pour stocker les données de l’étudiant :

* Ajoute EnrollmentDate nullable pour les étudiants.

* Ajoute la colonne de discriminateur pour indiquer si une ligne est pour un étudiant ou un formateur.

* Rend HireDate nullable étant donné que les lignes de l’étudiant n’ont des dates d’embauche.

* Ajoute un champ temporaire qui sera utilisé pour mettre à jour les clés étrangères qui pointent vers les étudiants. Lorsque vous copiez des étudiants dans la table Person ils obtenez de nouvelles valeurs de clé primaires.

* Copie des données à partir de la table de l’étudiant dans la table Person. Cela provoque des étudiants obtenir attribué les nouvelles valeurs de clé primaires.

* Résout des valeurs de clés étrangères qui pointent vers les étudiants.

* Crée de nouveau les contraintes de clé étrangère et des index, désormais de les utiliser pour la table Person.

(Si vous aviez utilisé des GUID au lieu de l’entier en tant que le type de clé primaire, les valeurs de clé primaire étudiant n’ont pas à modifier, et plusieurs de ces étapes a été omises).

Exécutez le `database update` commande :

```console
dotnet ef database update
```

(Dans un système de production, vous effectueriez modifications correspondantes apportées à la `Down` méthode dans les cas, vous deviez jamais qui permet de revenir à la version précédente de la base de données. Pour ce didacticiel, vous n’utiliserez pas la `Down` méthode.)

> [!NOTE] 
> Il est possible d’obtenir d’autres erreurs lorsque des modifications de schéma dans une base de données qui comporte déjà des données. Si vous obtenez des erreurs de migration que vous ne pouvez pas résoudre, vous pouvez modifier le nom de la base de données dans la chaîne de connexion ou supprimer la base de données. Avec une base de données, il n’existe pas de données à migrer, et la commande de base de données de mise à jour est plus susceptible de se terminer sans erreur. Pour supprimer la base de données, utilisez SSOX ou exécutez le `database drop` commande CLI.

## <a name="test-with-inheritance-implemented"></a>Tester avec héritage implémentée

Exécutez l’application et essayez de différentes pages. Tout fonctionne comme auparavant.

Dans **l’Explorateur d’objets SQL Server**, développez **les connexions de données/SchoolContext** , puis **Tables**, et vous voyez que les tables Student et Instructor ont été remplacés par un Table Person. Ouvrez le Concepteur de tables Person et vous voyez qu’il possède toutes les colonnes utilisées dans les tables Student et formateur.

![Table Person dans SSOX](inheritance/_static/ssox-person-table.png)

Avec le bouton droit de la table Person, puis cliquez sur **afficher les données de Table** pour afficher la colonne de discriminateur.

![Table Person dans SSOX - données de la table](inheritance/_static/ssox-person-data.png)

## <a name="summary"></a>Résumé

Vous avez implémenté l’héritage table par hiérarchie pour le `Person`, `Student`, et `Instructor` classes. Pour plus d’informations sur l’héritage dans Entity Framework Core, consultez [héritage](https://docs.microsoft.com/ef/core/modeling/inheritance). Dans l’étape suivante du didacticiel, vous allez apprendre à gérer une variété de scénarios de Entity Framework relativement avancés.

>[!div class="step-by-step"]
[Précédent](concurrency.md)
[Suivant](advanced.md)  

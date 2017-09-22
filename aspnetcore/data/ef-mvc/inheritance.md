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
ms.openlocfilehash: 10bde121dac3bdbbf0e55f2d146d91dea0f0210f
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="inheritance---ef-core-with-aspnet-core-mvc-tutorial-9-of-10"></a><span data-ttu-id="36f0c-104">Héritage - Core EF avec le didacticiel d’ASP.NET MVC de base (9 sur 10)</span><span class="sxs-lookup"><span data-stu-id="36f0c-104">Inheritance - EF Core with ASP.NET Core MVC tutorial (9 of 10)</span></span>

<span data-ttu-id="36f0c-105">Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="36f0c-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="36f0c-106">L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="36f0c-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="36f0c-107">Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).</span><span class="sxs-lookup"><span data-stu-id="36f0c-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="36f0c-108">Dans le didacticiel précédent, vous avez géré les exceptions d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="36f0c-108">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="36f0c-109">Ce didacticiel vous indiquera comment implémenter l’héritage dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="36f0c-109">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="36f0c-110">Dans la programmation orientée objet, vous pouvez utiliser l’héritage pour faciliter la réutilisation de code.</span><span class="sxs-lookup"><span data-stu-id="36f0c-110">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="36f0c-111">Dans ce didacticiel, vous allez modifier le `Instructor` et `Student` afin qu’ils dérivent des classes un `Person` classe qui contient les propriétés de base `LastName` qui sont communs aux instructeurs et les étudiants.</span><span class="sxs-lookup"><span data-stu-id="36f0c-111">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="36f0c-112">Vous ne seront pas ajouter ou modifier des pages web, mais vous allez modifier la partie du code et ces modifications sont répercutées automatiquement dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="36f0c-112">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

## <a name="options-for-mapping-inheritance-to-database-tables"></a><span data-ttu-id="36f0c-113">Options pour le mappage d’héritage pour les tables de base de données</span><span class="sxs-lookup"><span data-stu-id="36f0c-113">Options for mapping inheritance to database tables</span></span>

<span data-ttu-id="36f0c-114">Le `Instructor` et `Student` classes dans le modèle de données School ont plusieurs propriétés qui sont identiques :</span><span class="sxs-lookup"><span data-stu-id="36f0c-114">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Classes de Student et Instructor](inheritance/_static/no-inheritance.png)

<span data-ttu-id="36f0c-116">Supposons que vous souhaitez éliminer le code redondant pour les propriétés qui sont partagées par les `Instructor` et `Student` entités.</span><span class="sxs-lookup"><span data-stu-id="36f0c-116">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="36f0c-117">Ou vous souhaitez écrire un service qui peut mettre en forme les noms sans soins si le nom provient d’un formateur ou un étudiant.</span><span class="sxs-lookup"><span data-stu-id="36f0c-117">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="36f0c-118">Vous pouvez créer un `Person` classe de base qui contient uniquement les propriétés partagées, puis apportez les `Instructor` et `Student` classes héritent de cette classe de base, comme indiqué dans l’illustration suivante :</span><span class="sxs-lookup"><span data-stu-id="36f0c-118">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Classes de Student et Instructor dérivant de la classe Person](inheritance/_static/inheritance.png)

<span data-ttu-id="36f0c-120">Il existe plusieurs façons que cette structure d’héritage peut être représentée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="36f0c-120">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="36f0c-121">Vous pourriez avoir une table de la personne qui inclut des informations sur les étudiants et instructeurs dans une table unique.</span><span class="sxs-lookup"><span data-stu-id="36f0c-121">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="36f0c-122">Certaines des colonnes impossible applique uniquement aux instructeurs (HireDate), certains uniquement pour les étudiants (EnrollmentDate), certains pour les deux (nom, prénom).</span><span class="sxs-lookup"><span data-stu-id="36f0c-122">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="36f0c-123">En règle générale, vous devriez pour indiquer le type de chaque ligne représente une colonne de discriminateur.</span><span class="sxs-lookup"><span data-stu-id="36f0c-123">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="36f0c-124">Par exemple, la colonne de discriminateur peut-être « Formateur » pour les enseignants et « Étudiant » pour les étudiants.</span><span class="sxs-lookup"><span data-stu-id="36f0c-124">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Exemple de table par hiérarchie](inheritance/_static/tph.png)

<span data-ttu-id="36f0c-126">Ce modèle de la génération d’une structure d’héritage d’entité à partir d’une table de base de données unique est appelé l’héritage table par hiérarchie (TPH).</span><span class="sxs-lookup"><span data-stu-id="36f0c-126">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="36f0c-127">Une alternative consiste à rendre la base de données ressemble plus à la structure d’héritage.</span><span class="sxs-lookup"><span data-stu-id="36f0c-127">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="36f0c-128">Par exemple, vous pourriez uniquement les champs nom de la table Person et ont des tables distinctes Instructor et Student avec les champs de date.</span><span class="sxs-lookup"><span data-stu-id="36f0c-128">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Héritage TPT (table par type)](inheritance/_static/tpt.png)

<span data-ttu-id="36f0c-130">Ce modèle de configuration d’une table de base de données pour chaque classe d’entité est appelé table par héritage de type (TPT).</span><span class="sxs-lookup"><span data-stu-id="36f0c-130">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="36f0c-131">Encore une autre option consiste à mapper tous les types non abstraits à des tables individuelles.</span><span class="sxs-lookup"><span data-stu-id="36f0c-131">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="36f0c-132">Toutes les propriétés d’une classe, y compris les propriétés héritées, mappent aux colonnes de la table correspondante.</span><span class="sxs-lookup"><span data-stu-id="36f0c-132">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="36f0c-133">Ce modèle est appelé l’héritage de Table-par classe concrète (TPC).</span><span class="sxs-lookup"><span data-stu-id="36f0c-133">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="36f0c-134">Si vous avez implémenté l’héritage pour les classes de personne, Student et Instructor TPC comme indiqué précédemment, les tables Student et Instructor ne ressemble pas différents après l’implémentation de l’héritage de leur.</span><span class="sxs-lookup"><span data-stu-id="36f0c-134">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="36f0c-135">Les modèles d’héritage TPC et TPH remettre généralement meilleures performances que les modèles d’héritage TPT, étant donné que les modèles TPT peuvent entraîner des requêtes de jointure complexe.</span><span class="sxs-lookup"><span data-stu-id="36f0c-135">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="36f0c-136">Ce didacticiel montre comment implémenter l’héritage TPH.</span><span class="sxs-lookup"><span data-stu-id="36f0c-136">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="36f0c-137">TPH est un modèle d’héritage uniquement qui prend en charge de l’Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="36f0c-137">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="36f0c-138">Ce que vous allez faire est de créer un `Person` de classe, de modifier le `Instructor` et `Student` comme classes de dérivation `Person`, ajouter la nouvelle classe par le `DbContext`et créer une migration.</span><span class="sxs-lookup"><span data-stu-id="36f0c-138">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP] 
> <span data-ttu-id="36f0c-139">Pensez à enregistrer une copie du projet avant d’apporter les modifications suivantes.</span><span class="sxs-lookup"><span data-stu-id="36f0c-139">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="36f0c-140">Si vous rencontrez des problèmes et devez recommencer, il sera alors plus facile de démarrer à partir du projet enregistré au lieu d’inverser les opérations effectuées pour ce didacticiel ou en accédant au début de la série entière.</span><span class="sxs-lookup"><span data-stu-id="36f0c-140">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="36f0c-141">Créer la classe de personne</span><span class="sxs-lookup"><span data-stu-id="36f0c-141">Create the Person class</span></span>

<span data-ttu-id="36f0c-142">Dans le dossier de modèles, créer Person.cs et remplacez le code de modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="36f0c-142">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Person.cs)]

## <a name="make-student-and-instructor-classes-inherit-from-person"></a><span data-ttu-id="36f0c-143">Faites en sorte que les classes de Student et Instructor hérite de personne</span><span class="sxs-lookup"><span data-stu-id="36f0c-143">Make Student and Instructor classes inherit from Person</span></span>

<span data-ttu-id="36f0c-144">Dans *Instructor.cs*, dérivez la classe de formateur de la classe de personne et supprimer les champs de clé et le nom.</span><span class="sxs-lookup"><span data-stu-id="36f0c-144">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="36f0c-145">Le code doit ressembler à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="36f0c-145">The code will look like the following example:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="36f0c-146">Apporter les mêmes modifications dans *Student.cs*.</span><span class="sxs-lookup"><span data-stu-id="36f0c-146">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-the-person-entity-type-to-the-data-model"></a><span data-ttu-id="36f0c-147">Ajouter le type d’entité personne au modèle de données</span><span class="sxs-lookup"><span data-stu-id="36f0c-147">Add the Person entity type to the data model</span></span>

<span data-ttu-id="36f0c-148">Ajouter le type d’entité personne *SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="36f0c-148">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="36f0c-149">Les nouvelles lignes sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="36f0c-149">The new lines are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="36f0c-150">C’est tout ce qui a besoin d’Entity Framework pour configurer l’héritage table par hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="36f0c-150">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="36f0c-151">Comme vous le verrez, lorsque la base de données est mise à jour, il aura une table de la personne à la place les tables Student et formateur.</span><span class="sxs-lookup"><span data-stu-id="36f0c-151">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-customize-migration-code"></a><span data-ttu-id="36f0c-152">Créer et personnaliser le code de la migration</span><span class="sxs-lookup"><span data-stu-id="36f0c-152">Create and customize migration code</span></span>

<span data-ttu-id="36f0c-153">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="36f0c-153">Save your changes and build the project.</span></span> <span data-ttu-id="36f0c-154">Ouvrez la fenêtre de commande dans le dossier du projet, puis entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="36f0c-154">Then open the command window in the project folder and enter the following command:</span></span>

```console
dotnet ef migrations add Inheritance
```

<span data-ttu-id="36f0c-155">N’exécutez pas le `database update` commande encore.</span><span class="sxs-lookup"><span data-stu-id="36f0c-155">Don't run the `database update` command yet.</span></span> <span data-ttu-id="36f0c-156">Cette commande entraîne de perte de données, car il supprime la table de formateurs et les renommer la table étudiant à personne.</span><span class="sxs-lookup"><span data-stu-id="36f0c-156">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="36f0c-157">Vous devez fournir un code personnalisé pour préserver les données existantes.</span><span class="sxs-lookup"><span data-stu-id="36f0c-157">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="36f0c-158">Ouvrez *Migrations /\<timestamp > _Inheritance.cs* et remplacez le `Up` méthode avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="36f0c-158">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="36f0c-159">Ce code prend en charge les tâches de mise à jour de base de données suivantes :</span><span class="sxs-lookup"><span data-stu-id="36f0c-159">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="36f0c-160">Supprime les contraintes de clé étrangère et les index qui pointent vers la table d’étudiants.</span><span class="sxs-lookup"><span data-stu-id="36f0c-160">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="36f0c-161">Renomme la table de formateurs en tant que personne et apporte les modifications nécessaires pour stocker les données de l’étudiant :</span><span class="sxs-lookup"><span data-stu-id="36f0c-161">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="36f0c-162">Ajoute EnrollmentDate nullable pour les étudiants.</span><span class="sxs-lookup"><span data-stu-id="36f0c-162">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="36f0c-163">Ajoute la colonne de discriminateur pour indiquer si une ligne est pour un étudiant ou un formateur.</span><span class="sxs-lookup"><span data-stu-id="36f0c-163">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="36f0c-164">Rend HireDate nullable étant donné que les lignes de l’étudiant n’ont des dates d’embauche.</span><span class="sxs-lookup"><span data-stu-id="36f0c-164">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="36f0c-165">Ajoute un champ temporaire qui sera utilisé pour mettre à jour les clés étrangères qui pointent vers les étudiants.</span><span class="sxs-lookup"><span data-stu-id="36f0c-165">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="36f0c-166">Lorsque vous copiez des étudiants dans la table Person ils obtenez de nouvelles valeurs de clé primaires.</span><span class="sxs-lookup"><span data-stu-id="36f0c-166">When you copy students into the Person table they'll get new primary key values.</span></span>

* <span data-ttu-id="36f0c-167">Copie des données à partir de la table de l’étudiant dans la table Person.</span><span class="sxs-lookup"><span data-stu-id="36f0c-167">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="36f0c-168">Cela provoque des étudiants obtenir attribué les nouvelles valeurs de clé primaires.</span><span class="sxs-lookup"><span data-stu-id="36f0c-168">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="36f0c-169">Résout des valeurs de clés étrangères qui pointent vers les étudiants.</span><span class="sxs-lookup"><span data-stu-id="36f0c-169">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="36f0c-170">Crée de nouveau les contraintes de clé étrangère et des index, désormais de les utiliser pour la table Person.</span><span class="sxs-lookup"><span data-stu-id="36f0c-170">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="36f0c-171">(Si vous aviez utilisé des GUID au lieu de l’entier en tant que le type de clé primaire, les valeurs de clé primaire étudiant n’ont pas à modifier, et plusieurs de ces étapes a été omises).</span><span class="sxs-lookup"><span data-stu-id="36f0c-171">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="36f0c-172">Exécutez le `database update` commande :</span><span class="sxs-lookup"><span data-stu-id="36f0c-172">Run the `database update` command:</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="36f0c-173">(Dans un système de production, vous effectueriez modifications correspondantes apportées à la `Down` méthode dans les cas, vous deviez jamais qui permet de revenir à la version précédente de la base de données.</span><span class="sxs-lookup"><span data-stu-id="36f0c-173">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="36f0c-174">Pour ce didacticiel, vous n’utiliserez pas la `Down` méthode.)</span><span class="sxs-lookup"><span data-stu-id="36f0c-174">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE] 
> <span data-ttu-id="36f0c-175">Il est possible d’obtenir d’autres erreurs lorsque des modifications de schéma dans une base de données qui comporte déjà des données.</span><span class="sxs-lookup"><span data-stu-id="36f0c-175">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="36f0c-176">Si vous obtenez des erreurs de migration que vous ne pouvez pas résoudre, vous pouvez modifier le nom de la base de données dans la chaîne de connexion ou supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="36f0c-176">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="36f0c-177">Avec une base de données, il n’existe pas de données à migrer, et la commande de base de données de mise à jour est plus susceptible de se terminer sans erreur.</span><span class="sxs-lookup"><span data-stu-id="36f0c-177">With a new database, there is no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="36f0c-178">Pour supprimer la base de données, utilisez SSOX ou exécutez le `database drop` commande CLI.</span><span class="sxs-lookup"><span data-stu-id="36f0c-178">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-with-inheritance-implemented"></a><span data-ttu-id="36f0c-179">Tester avec héritage implémentée</span><span class="sxs-lookup"><span data-stu-id="36f0c-179">Test with inheritance implemented</span></span>

<span data-ttu-id="36f0c-180">Exécutez l’application et essayez de différentes pages.</span><span class="sxs-lookup"><span data-stu-id="36f0c-180">Run the app and try various pages.</span></span> <span data-ttu-id="36f0c-181">Tout fonctionne comme auparavant.</span><span class="sxs-lookup"><span data-stu-id="36f0c-181">Everything works the same as it did before.</span></span>

<span data-ttu-id="36f0c-182">Dans **l’Explorateur d’objets SQL Server**, développez **les connexions de données/SchoolContext** , puis **Tables**, et vous voyez que les tables Student et Instructor ont été remplacés par un Table Person.</span><span class="sxs-lookup"><span data-stu-id="36f0c-182">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="36f0c-183">Ouvrez le Concepteur de tables Person et vous voyez qu’il possède toutes les colonnes utilisées dans les tables Student et formateur.</span><span class="sxs-lookup"><span data-stu-id="36f0c-183">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Table Person dans SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="36f0c-185">Avec le bouton droit de la table Person, puis cliquez sur **afficher les données de Table** pour afficher la colonne de discriminateur.</span><span class="sxs-lookup"><span data-stu-id="36f0c-185">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Table Person dans SSOX - données de la table](inheritance/_static/ssox-person-data.png)

## <a name="summary"></a><span data-ttu-id="36f0c-187">Résumé</span><span class="sxs-lookup"><span data-stu-id="36f0c-187">Summary</span></span>

<span data-ttu-id="36f0c-188">Vous avez implémenté l’héritage table par hiérarchie pour le `Person`, `Student`, et `Instructor` classes.</span><span class="sxs-lookup"><span data-stu-id="36f0c-188">You've implemented table-per-hierarchy inheritance for the `Person`, `Student`, and `Instructor` classes.</span></span> <span data-ttu-id="36f0c-189">Pour plus d’informations sur l’héritage dans Entity Framework Core, consultez [héritage](https://docs.microsoft.com/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="36f0c-189">For more information about inheritance in Entity Framework Core, see [Inheritance](https://docs.microsoft.com/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="36f0c-190">Dans l’étape suivante du didacticiel, vous allez apprendre à gérer une variété de scénarios de Entity Framework relativement avancés.</span><span class="sxs-lookup"><span data-stu-id="36f0c-190">In the next tutorial you'll see how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="36f0c-191">[Précédent](concurrency.md)
[Suivant](advanced.md)</span><span class="sxs-lookup"><span data-stu-id="36f0c-191">[Previous](concurrency.md)
[Next](advanced.md)</span></span>  

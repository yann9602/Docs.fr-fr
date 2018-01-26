---
title: "Cœur de ASP.NET MVC avec Entity Framework Core - didacticiel 1 sur 10"
author: tdykstra
description: 
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/intro
ms.openlocfilehash: c30556368ba24fb38cf3347dd49f171b5246514c
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="getting-started-with-aspnet-core-mvc-and-entity-framework-core-using-visual-studio-1-of-10"></a>Mise en route avec ASP.NET MVC de base et d’Entity Framework Core, à l’aide de Visual Studio (1 / 10)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Une version de Pages Razor de ce didacticiel est disponible [ici](xref:data/ef-rp/intro). La version des pages Razor est plus facile à suivre et couvre plus de fonctionnalités Entity Framework. Nous vous recommandons de suivre les [version Pages Razor de ce didacticiel](xref:data/ef-rp/intro).

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET Core 2.0 MVC à l’aide d’Entity Framework (EF) 2.0 et Visual Studio 2017.

L’exemple d’application est un site web pour une université fictif de Contoso. Il inclut des fonctionnalités telles que leur admission d’étudiant, la création de cours et les affectations de formateur. Il s’agit de la première d’une série de didacticiels qui expliquent comment générer l’exemple d’application Contoso University à partir de zéro.

[Télécharger ou afficher l’application terminée.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

EF Core 2.0 est la dernière version de EF mais ne dispose pas encore toutes les fonctionnalités EF 6.x. Pour plus d’informations sur comment choisir entre EF 6.x et EF Core, consultez [EF Core vs. EF6.x](https://docs.microsoft.com/ef/efcore-and-ef6/). Si vous choisissez EF 6.x, consultez [la version précédente de cette série de didacticiels](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

> [!NOTE]
> * Pour la version 1.1 de base ASP.NET de ce didacticiel, consultez le [version VS 2017 mise à jour 2 de ce didacticiel au format PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/data/ef-mvc/intro/_static/efmvc1.1.pdf).
> * Pour obtenir la version Visual Studio 2015 de ce didacticiel, consultez la [version VS 2015 de la documentation ASP.NET Core au format PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/common/_static/aspnet-core-project-json.pdf).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous rencontrez un problème que vous ne pouvez pas résoudre, vous trouverez généralement la solution en comparant votre code pour le [projet achevé](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final). Pour obtenir la liste des erreurs courantes et comment les résoudre, consultez [la section de dépannage du didacticiel dernière dans la série](advanced.md#common-errors). Si vous ne trouvez pas ce dont vous avez besoin il, vous pouvez publier une question dans StackOverflow.com pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP] 
> Il s’agit d’une série de 10 didacticiels, chacun d’eux s’appuie sur les opérations réalisées dans les didacticiels antérieures. Pensez à enregistrer une copie du projet après chaque didacticiel réussite. Si vous rencontrez des problèmes, vous pouvez ensuite démarrer sur à partir du didacticiel précédent, au lieu de revenir au début de l’ensemble de la série.

## <a name="the-contoso-university-web-application"></a>L’application web de Contoso University

L’application que vous créez dans ces didacticiels est un site web de l’université simple.

Les utilisateurs peuvent afficher et mettre à jour des étudiants, les cours et les informations de formateur. Voici quelques exemples d’écrans que vous allez créer.

![Page d’Index les étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

Le style de l’interface utilisateur de ce site a été conservé proche de ce qui est généré par les modèles prédéfinis, afin de pouvoir le didacticiel concentrer principalement sur l’utilisation d’Entity Framework.

## <a name="create-an-aspnet-core-mvc-web-application"></a>Créer une application de web ASP.NET MVC de base

Ouvrez Visual Studio et créez un nouveau projet de web ASP.NET Core c# nommé « ContosoUniversity ».

* À partir de la **fichier** menu, sélectionnez **Nouveau > projet**.

* Dans le volet gauche, sélectionnez **installé > Visual c# > Web**.

* Sélectionnez le modèle de projet **Application web ASP.NET Core**.

* Entrez **ContosoUniversity** en tant que le nom et cliquez sur **OK**.

  ![Boîte de dialogue Nouveau projet](intro/_static/new-project.png)

* Attendez que la **nouvelle Application ASP.NET Core Web (.NET Core)** boîte de dialogue

* Sélectionnez **ASP.NET Core 2.0** et **l’Application Web (Model-View-Controller)** modèle.

  **Remarque :** ce didacticiel nécessite ASP.NET 2.0 de noyaux et Core EF 2.0 ou version ultérieure, assurez-vous que **ASP.NET Core 1.1** n’est pas sélectionnée.

* Assurez-vous que **authentification** a la valeur **aucune authentification**.

* Cliquez sur **OK**.

  ![Boîte de dialogue Nouveau projet ASP.NET](intro/_static/new-aspnet.png)

## <a name="set-up-the-site-style"></a>Définir le style de site

Quelques modifications configurera le menu de site, la disposition et la page d’accueil.

Ouvrez *Views/Shared/_Layout.cshtml* et apportez les modifications suivantes :

* Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ». Il existe trois occurrences.

* Ajouter des entrées de menu pour **étudiants**, **cours**, **instructeurs**, et **départements**et supprimer la **Contact** entrée de menu.

Les modifications sont mises en surbrillance.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,30,36-39,48)]

Dans *Views/Home/Index.cshtml*, remplacez le contenu du fichier par le code suivant pour remplacer le texte sur ASP.NET et MVC avec le texte de cette application :

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Appuyez sur CTRL + F5 pour exécuter le projet ou choisissez **Déboguer > Démarrer sans débogage** à partir du menu. Vous consultez la page d’accueil avec onglets pour les pages que vous créez dans ces didacticiels.

![Page d’accueil de l’université Contoso](intro/_static/home-page.png)

## <a name="entity-framework-core-nuget-packages"></a>Entity Framework Core NuGet packages

Pour ajouter la prise en charge EF Core à un projet, installez le fournisseur de base de données que vous souhaitez cibler. Ce didacticiel utilise SQL Server, et le package de fournisseur est [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Ce package est inclus dans le [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, sans que vous ayez à installer.

Ce package et ses dépendances (`Microsoft.EntityFrameworkCore` et `Microsoft.EntityFrameworkCore.Relational`) fournissent la prise en charge du runtime pour EF. Vous allez ajouter un package d’outils plus loin, dans le [Migrations](migrations.md) didacticiel. 

Pour plus d’informations sur les autres fournisseurs de base de données qui sont disponibles pour Entity Framework Core, consultez [fournisseurs de base de données](https://docs.microsoft.com/ef/core/providers/).

## <a name="create-the-data-model"></a>Créer le modèle de données

Vous allez ensuite créer des classes d’entité pour l’application Contoso University. Vous devez commencer par les trois entités suivantes.

![Diagramme de modèle de données de l’étudiant de l’inscription de cours](intro/_static/data-model-diagram.png)

Il existe une relation un-à-plusieurs entre `Student` et `Enrollment` entités, et il existe une relation un-à-plusieurs entre `Course` et `Enrollment` entités. En d’autres termes, un étudiant peut être inscrit dans n’importe quel nombre de cours et un cours peut avoir n’importe quel nombre d’élèves inscrits.

Dans les sections suivantes, vous allez créer une classe pour chacune de ces entités.

### <a name="the-student-entity"></a>L’entité Student

![Diagramme de l’entité étudiant](intro/_static/student-entity.png)

Dans le *modèles* dossier, créez un fichier de classe nommé *Student.cs* et remplacez le code de modèle par le code suivant.

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

Le `ID` propriété deviendra la colonne de clé primaire de la table de base de données qui correspond à cette classe. Par défaut, Entity Framework interprète une propriété nommée `ID` ou `classnameID` comme clé primaire.

Le `Enrollments` est une propriété de navigation. Propriétés de navigation contiennent d’autres entités qui sont associées à cette entité. Dans ce cas, le `Enrollments` propriété d’un `Student entity` contiendra tous les `Enrollment` entités qui sont associées à cette `Student` entité. En d’autres termes, si une ligne donnée d’étudiant dans la base de données a deux d’inscription des lignes liées (lignes qui contiennent la valeur de clé primaire qui student dans leur colonne StudentID de clé étrangère), qui `Student` l’entité `Enrollments` celles contiendra la propriété de navigation deux `Enrollment` entités.

Si une propriété de navigation peut contenir plusieurs entités (par exemple, les relations plusieurs-à-plusieurs ou un-à-plusieurs), son type doit être une liste dans laquelle les entrées peuvent être ajoutées, supprimées et mis à jour, telles que `ICollection<T>`. Vous pouvez spécifier `ICollection<T>` ou un type tel que `List<T>` ou `HashSet<T>`. Si vous spécifiez `ICollection<T>`, EF crée un `HashSet<T>` collection par défaut.

### <a name="the-enrollment-entity"></a>L’entité de l’inscription

![Diagramme de l’entité d’inscription](intro/_static/enrollment-entity.png)

Dans le *modèles* dossier, créez *Enrollment.cs* et remplacez le code existant par le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

Le `EnrollmentID` propriété sera la clé primaire ; cette entité utilise le `classnameID` de modèle au lieu de `ID` par lui-même en tant que vous l’avez vu dans la `Student` entité. En général vous choisissez un modèle et utilisez-le dans votre modèle de données. Ici, la variante illustre que vous pouvez utiliser un modèle. Dans un [didacticiel ultérieur](inheritance.md), vous verrez comment à l’aide de code sans classname facilite l’implémentation de l’héritage dans le modèle de données.

Le `Grade` propriété est un `enum`. Le point d’interrogation après la `Grade` déclaration de type indique que le `Grade` propriété est nullable. Un niveau qui a la valeur null est différent de zéro une note : null signifie une note n’est pas connu ou n’a pas encore été affectée.

Le `StudentID` propriété est une clé étrangère, et la propriété de navigation correspondante est `Student`. Un `Enrollment` entité est associée à un `Student` entité, donc la propriété peut contenir uniquement un seul `Student` entité (contrairement à la `Student.Enrollments` propriété de navigation, vous avez vu précédemment, qui peut contenir plusieurs `Enrollment` entités).

Le `CourseID` propriété est une clé étrangère, et la propriété de navigation correspondante est `Course`. Un `Enrollment` entité est associée à un `Course` entité.

Entity Framework interprète une propriété comme une propriété de clé étrangère s’il est nommé `<navigation property name><primary key property name>` (par exemple, `StudentID` pour le `Student` propriété de navigation depuis le `Student` la clé primaire de l’entité est `ID`). Propriétés de clé étrangère peuvent aussi être nommées simplement `<primary key property name>` (par exemple, `CourseID` depuis le `Course` la clé primaire de l’entité est `CourseID`).

### <a name="the-course-entity"></a>L’entité de cours

![Diagramme d’entité de cours](intro/_static/course-entity.png)

Dans le *modèles* dossier, créez *Course.cs* et remplacez le code existant par le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

Le `Enrollments` est une propriété de navigation. A `Course` entité peut être associée à un nombre quelconque de `Enrollment` entités.

Nous contenterons de dire plus le `DatabaseGenerated` d’attribut dans un [didacticiel ultérieur](complex-data-model.md) dans cette série. En fait, cet attribut vous permet d’entrer la clé primaire pour le cours plutôt que d’avoir à la base de données de sa génération.

## <a name="create-the-database-context"></a>Créer le contexte de base de données

La classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données spécifiée est la classe de contexte de base de données. Vous créez cette classe en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`. Dans votre code, vous spécifiez les entités qui sont incluses dans le modèle de données. Vous pouvez également personnaliser le comportement de certaines Entity Framework. Dans ce projet, la classe est nommée `SchoolContext`.

Dans le dossier du projet, créez un dossier nommé *données*.

Dans le *données* dossier créer un nouveau fichier de classe nommé *SchoolContext.cs*et remplacez le code de modèle par le code suivant :

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Ce code crée un `DbSet` propriété pour chaque jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.

Vous pouvez avez omis le `DbSet<Enrollment>` et `DbSet<Course>` instructions et il seraient fonctionnent de la même. Entity Framework inclut les implicitement, car le `Student` références d’entité le `Enrollment` entité et la `Enrollment` références d’entité le `Course` entité.

Lorsque la base de données est créé, EF crée des tables qui ont des noms identique à la `DbSet` les noms de propriété. Les noms de propriété pour les collections sont généralement pluriel (étudiants plutôt qu’étudiant), mais les développeurs sont en désaccord sur si les noms de table doivent être pluralisés ou non. Pour ces didacticiels, vous allez remplacer le comportement par défaut en spécifiant les noms de table unique dans le DbContext. Pour ce faire, ajoutez le code en surbrillance suivant après la dernière propriété DbSet.

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a>Enregistrer le contexte avec l’injection de dépendance

ASP.NET Core implémente [injection de dépendance](../../fundamentals/dependency-injection.md) par défaut. Services (par exemple, le contexte de base de données EF) sont enregistrés avec l’injection de dépendance au cours du démarrage de l’application. Composants qui requièrent ces services (tels que les contrôleurs MVC) sont fournis à ces services via les paramètres du constructeur. Vous verrez le code du constructeur contrôleur qui obtient une instance de contexte plus loin dans ce didacticiel.

Pour inscrire `SchoolContext` en tant que service, ouvrez *Startup.cs*et ajoutez les lignes en surbrillance vers le `ConfigureServices` (méthode).

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

Le nom de la chaîne de connexion est passé dans le contexte en appelant une méthode sur un `DbContextOptionsBuilder` objet. Pour le développement local, le [système de configuration ASP.NET Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir de la *appsettings.json* fichier.

Ajouter `using` instructions pour `ContosoUniversity.Data` et `Microsoft.EntityFrameworkCore` espaces de noms, puis générez le projet.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Ouvrez le *appsettings.json* et ajoutez une chaîne de connexion comme indiqué dans l’exemple suivant.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

La chaîne de connexion spécifie une base de données de la base de données SQL Server locale. LocalDB est une version légère de SQL Server Express Database Engine et est conçue pour le développement d’applications, pas les fins de production. LocalDB démarre à la demande et s’exécute en mode utilisateur, il n’existe aucune configuration complexe. Par défaut, LocalDB crée *.mdf* dans les fichiers de base de données la `C:/Users/<user>` active.

## <a name="add-code-to-initialize-the-database-with-test-data"></a>Ajoutez du code pour initialiser la base de données de test

Entity Framework crée une base de données vide pour vous. Dans cette section, vous écrivez une méthode qui est appelée après que la base de données est créée pour la remplir avec des données de test.

Vous allez utiliser le `EnsureCreated` méthode pour créer automatiquement la base de données. Dans un [didacticiel ultérieur](migrations.md) vous allez apprendre à gérer les modifications apportées au modèle à l’aide de Migrations Code First pour modifier le schéma de base de données au lieu de devoir supprimer et recréer la base de données.

Dans le *données* dossier, créez un nouveau fichier de classe nommé *DbInitializer.cs* et remplacez le code de modèle par le code suivant, ce qui entraîne une base de données doit être créé si nécessaire, et les données dans le nouvel de test de charge base de données.

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Le code vérifie s’il existe des étudiants dans la base de données, et dans le cas contraire, il suppose que la base de données est nouveau et doit être exécutée avec les données de test. Il charge les données de test dans les tableaux plutôt que `List<T>` collections pour optimiser les performances.

Dans *Program.cs*, modifiez le `Main` méthode effectuer les opérations suivantes sur le démarrage de l’application :

* Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendance.
* Appelez la méthode de valeur initiale, en lui passant le contexte.
* Supprimer le contexte lors de la méthode de la valeur initiale est terminée.

[!code-csharp[Main](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Ajouter `using` instructions :

[!code-csharp[Main](intro/samples/cu/Program.cs?name=snippet_Usings)]

Dans les didacticiels plus anciens, vous pouvez voir un code similaire dans le `Configure` méthode dans *Startup.cs*. Nous vous recommandons d’utiliser le `Configure` méthode uniquement pour définir le pipeline de demande. Code de démarrage d’application appartient à la `Main` (méthode).

La première fois que vous exécutez l’application, la base de données sera être désormais créée et amorcée avec les données de test. Chaque fois que vous modifiez votre modèle de données, vous pouvez supprimer la base de données, mettre à jour votre méthode de valeur initiale et repartir avec une nouvelle base de données de la même façon. Dans les didacticiels suivants, vous allez apprendre à modifier la base de données lorsque le modèle de données change, sans supprimer et recréer.

## <a name="create-a-controller-and-views"></a>Créer un contrôleur et des vues

Ensuite, vous utiliserez le moteur de génération de modèles automatique dans Visual Studio pour ajouter un contrôleur MVC et les vues utilisant EF pour interroger et enregistrer des données.

La création automatique de méthodes d’action CRUD et de vues est appelée à la génération de modèles automatique. La structure diffère de la génération de code dans la mesure où le code de modèle généré automatiquement est un point de départ que vous pouvez modifier pour l’adapter à vos besoins, tandis qu’en général, vous ne modifiez pas de code généré. Lorsque vous avez besoin personnaliser le code généré, vous utilisez des classes partielles ou vous régénérez le code en cas de changements.

* Avec le bouton droit le **contrôleurs** dossier **l’Explorateur de solutions** et sélectionnez **Ajouter > nouvel élément structuré**.

Si la boîte de dialogue **Ajouter des dépendances MVC** apparaît :

* [Effectuez la mise à jour de Visual Studio vers la dernière version](https://www.visualstudio.com/downloads/). Les versions de Visual Studio antérieures à 15.5 affichent cette boîte de dialogue.
* Si vous ne pouvez pas effectuer la mise à jour, sélectionnez **ADD**, puis suivez à nouveau les étapes pour ajouter un contrôleur.

* Dans le **ajouter une vue de structure** boîte de dialogue :

  * Sélectionnez **contrôleur MVC avec des vues, utilisant Entity Framework**.

  * Cliquez sur **Ajouter**.

* Dans le **ajouter un contrôleur** boîte de dialogue :

  * Dans **classe de modèle** sélectionnez **étudiant**.

  * Dans **classe de contexte de données** sélectionnez **SchoolContext**.

  * Acceptez la valeur par défaut **StudentsController** comme nom.

  * Cliquez sur **Ajouter**.

  ![Une vue de structure étudiant](intro/_static/scaffold-student.png)

  Lorsque vous cliquez sur **ajouter**, le moteur de génération de modèles automatique de Visual Studio crée un *StudentsController.cs* de fichiers et un ensemble de vues (*.cshtml* fichiers) qui fonctionnent avec le contrôleur.

(Le moteur de génération de modèles automatique peut également créer le contexte de base de données pour vous si vous ne le créer manuellement tout d’abord comme vous l’avez fait précédemment pour ce didacticiel. Vous pouvez spécifier une nouvelle classe de contexte dans le **ajouter un contrôleur** en cliquant sur le signe plus à droite de la boîte de **classe de contexte de données**.  Visual Studio crée ensuite votre `DbContext` classe ainsi que le contrôleur et les vues.)

Vous remarquerez que le contrôleur prend un `SchoolContext` comme paramètre de constructeur.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

Injection de dépendances ASP.NET s’occupe du passage d’une instance de `SchoolContext` dans le contrôleur. Vous avez configuré dans le *Startup.cs* fichier précédemment.

Le contrôleur contient un `Index` méthode d’action, qui affiche tous les étudiants dans la base de données. La méthode obtient une liste d’étudiants de l’entité étudiants définie en lisant la `Students` propriété de l’instance de contexte de base de données :

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Vous allez en savoir plus sur les éléments de programmation asynchrones dans ce code plus loin dans le didacticiel.

Le *Views/Students/Index.cshtml* affiche cette liste dans une table :

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Appuyez sur CTRL + F5 pour exécuter le projet ou choisissez **Déboguer > Démarrer sans débogage** à partir du menu.

Cliquez sur l’onglet d’étudiants pour afficher les données de test qui le `DbInitializer.Initialize` méthode inséré. Selon comment étroit votre fenêtre de navigateur, vous verrez la `Student` lien onglet en haut de la page, ou vous devrez cliquer sur l’icône de navigation dans le coin supérieur droit pour afficher le lien.

![Page d’accueil Contoso University étroit](intro/_static/home-page-narrow.png)

![Page d’Index les étudiants](intro/_static/students-index.png)

## <a name="view-the-database"></a>Afficher la base de données

Lorsque vous avez démarré l’application, le `DbInitializer.Initialize` les appels de méthode `EnsureCreated`. EF vu qu’il n’y a aucune base de données, et par conséquent, il créé un, puis le reste de la `Initialize` code de la méthode rempli la base de données. Vous pouvez utiliser **l’Explorateur d’objets SQL Server** (SSOX) pour afficher la base de données dans Visual Studio.

Fermez le navigateur.

Si la fenêtre SSOX n’est pas déjà ouverte, sélectionnez-le dans le **vue** menu dans Visual Studio.

Dans SSOX, cliquez sur **(localdb) \MSSQLLocalDB > bases de données**, puis cliquez sur l’entrée pour le nom de la base de données qui se trouve dans la chaîne de connexion dans votre *appsettings.json* fichier.

Développez le **Tables** nœud pour afficher les tables de votre base de données.

![Tables de SSOX](intro/_static/ssox-tables.png)

Avec le bouton droit le **Student** de table et cliquez sur **des données d’affichage** pour afficher les colonnes qui ont été créés et les lignes qui ont été insérées dans la table.

![Table étudiant dans SSOX](intro/_static/ssox-student-table.png)

Le *.mdf* et *.ldf* les fichiers de base de données se trouvent dans le *C:\Users\<votre_nom_utilisateur >* dossier.

Étant donné que vous appelez `EnsureCreated` dans la méthode d’initialiseur qui s’exécute sur le démarrage de l’application, vous pouvez maintenant apporter une modification apportée à la `Student` classe, supprimer la base de données, exécutez à nouveau l’application et la base de données est automatiquement recréée pour correspondre à votre modification. Par exemple, si vous ajoutez un `EmailAddress` propriété le `Student` (classe), vous voyez un nouveau `EmailAddress` colonne dans la table recréée.

## <a name="conventions"></a>Conventions

La quantité de code que vous deviez écrire dans l’ordre pour Entity Framework pouvoir créer une base de données complète pour vous est minime en raison de l’utilisation des conventions ou les hypothèses qu’Entity Framework.

* Les noms des `DbSet` propriétés sont utilisées comme noms de tables. Pour les entités non référencées par un `DbSet` propriété, classe d’entité noms sont utilisés comme noms de tables.

* Noms de propriété d’entité sont utilisées pour les noms de colonne.

* Propriétés de l’entité qui sont nommées ID ou classnameID sont reconnues comme propriétés de clé primaire.

* Une propriété est interprétée comme une propriété de clé étrangère s’il est nommé  *<navigation property name> <primary key property name>*  (par exemple, `StudentID` pour le `Student` propriété de navigation depuis la `Student` la clé primaire de l’entité est `ID`). Propriétés de clé étrangère peuvent aussi être nommées simplement  *<primary key property name>*  (par exemple, `EnrollmentID` depuis le `Enrollment` la clé primaire de l’entité est `EnrollmentID`).

Un comportement conventionnel peut être remplacé. Par exemple, vous pouvez spécifier explicitement les noms de table, comme vous l’avez vu précédemment dans ce didacticiel. Et vous pouvez définir des noms de colonne et définissez une propriété en tant que clé primaire ou clé étrangère, comme vous le verrez dans un [didacticiel ultérieur](complex-data-model.md) dans cette série.

## <a name="asynchronous-code"></a>Code asynchrone

La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.

Un serveur web a un nombre limité de threads disponibles, et dans les situations de forte charge tous les threads disponibles peuvent être en cours d’utilisation. Lorsque cela se produit, le serveur ne peut pas traiter de nouvelles demandes jusqu'à ce que les threads ne sont pas libérées. Avec le code synchrone, plusieurs threads peuvent être bloqués dans pendant qu’ils ne sont pas réellement effectuer aucun travail car ils sont en attente pour les e/s. Avec le code asynchrone, lorsqu’un processus est en attente d’e/s, son thread est libéré pour le serveur à utiliser pour traiter d’autres demandes. Par conséquent, code asynchrone permet d’utiliser plus efficacement les ressources serveur et le serveur est activé pour gérer plus de trafic sans délai.

Code asynchrone introduit une petite quantité de charge au moment de l’exécution, mais dans les situations de faible trafic le gain de performances est négligeable, lors de la pour les cas de trafic élevé, l’amélioration potentielle des performances est importante.

Dans le code suivant, le `async` (mot clé), `Task<T>` valeur de retour, `await` (mot clé), et `ToListAsync` méthode rendre le code à exécuter de façon asynchrone.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* Le `async` mot clé indique au compilateur pour générer des rappels pour les parties du corps de méthode et pour créer automatiquement le `Task<IActionResult>` objet qui est retourné.

* Le type de retour `Task<IActionResult>` représente le travail en cours avec un résultat de type `IActionResult`.

* Le `await` (mot clé), le compilateur fractionner la méthode en deux parties. La première partie se termine par l’opération qui est démarrée en mode asynchrone. La deuxième partie est placée dans une méthode de rappel qui est appelée lorsque l’opération se termine.

* `ToListAsync`est la version asynchrone de la `ToList` méthode d’extension.

Éléments à connaître lorsque vous écrivez du code asynchrone qui utilise Entity Framework :

* Uniquement les instructions qui génèrent des requêtes ou des commandes à envoyer à la base de données sont exécutées de façon asynchrone. Qui inclut, par exemple, `ToListAsync`, `SingleOrDefaultAsync`, et `SaveChangesAsync`. Il n’inclut pas, par exemple, les instructions qui modifient simplement un `IQueryable`, tel que `var students = context.Students.Where(s => s.LastName == "Davolio")`.

* Un contexte EF n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle. Lorsque vous appelez une méthode EF async, vous devez toujours utiliser le `await` (mot clé).

* Si vous souhaitez tirer parti des avantages de performances du code asynchrone, assurez-vous que n’importe quelle bibliothèque packages que vous utilisez (telles que pour la pagination), également utiliser async si elles appellent toutes les méthodes Entity Framework qui provoquent des requêtes à envoyer à la base de données.

Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [vue d’ensemble de Async](https://docs.microsoft.com/dotnet/articles/standard/async).

## <a name="summary"></a>Récapitulatif

Vous venez de créer une application simple qui utilise l’Entity Framework Core et SQL Server Express LocalDB pour stocker et afficher des données. Dans ce didacticiel, vous allez apprendre à effectuer CRUD de base (créer, lire, mettre à jour, supprimer) les opérations.

>[!div class="step-by-step"]
[Next](crud.md)

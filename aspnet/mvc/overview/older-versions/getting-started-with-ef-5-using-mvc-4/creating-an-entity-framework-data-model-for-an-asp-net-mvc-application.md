---
uid: mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
title: "Création d’un modèle de données Entity Framework pour une Application ASP.NET MVC (1 / 10) | Documents Microsoft"
author: tdykstra
description: "Une version plus récente de cette série de didacticiels est disponible pour Visual Studio 2013, Entity Framework 6 et MVC 5. Le de application Contoso University exemple web..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/30/2013
ms.topic: article
ms.assetid: 4ba029b6-ee7c-4e45-a0e7-b703c37e5d9a
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 223dd48bb996de527f20291e4701e7d1b60a539d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="creating-an-entity-framework-data-model-for-an-aspnet-mvc-application-1-of-10"></a>Création d’un modèle de données Entity Framework pour une Application ASP.NET MVC (1 / 10)
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/Getting-Started-with-dd0e2ed8)

> > [!NOTE] 
> > 
> > A [une version plus récente de cette série de didacticiels](../../getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) est disponible pour Visual Studio 2013, Entity Framework 6 et MVC 5.
> 
> 
> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 et Visual Studio 2012. L’exemple d’application est un site web pour une université fictif de Contoso. Il inclut des fonctionnalités telles que leur admission d’étudiant, la création de cours et les affectations de formateur. Cette série de didacticiels explique comment générer l’exemple d’application Contoso University. Vous pouvez [Téléchargez l’application terminée](https://code.msdn.microsoft.com/Getting-Started-with-dd0e2ed8).
> 
> ## <a name="code-first"></a>Code First
> 
> Il existe trois méthodes que vous pouvez utiliser des données dans Entity Framework : *Database First*, *Model First*, et *Code First*. Ce didacticiel est destiné le Code First. Pour plus d’informations sur les différences entre ces flux de travail et les conseils sur la façon de choisir la meilleure pour votre scénario, consultez [flux de travail de développement Entity Framework](https://msdn.microsoft.com/library/ms178359.aspx#dbfmfcf).
> 
> ## <a name="mvc"></a>MVC
> 
> L’exemple d’application repose sur [ASP.NET MVC](../../../index.md). Si vous préférez travailler avec le modèle Web Forms ASP.NET, consultez le [liaison de modèle et les Web Forms](../../../../web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data.md) série de didacticiels et [ASP.NET Data Access Content Map](../../../../whitepapers/aspnet-data-access-content-map.md).
> 
> ## <a name="software-versions"></a>Versions du logiciel
> 
> | **Le didacticiel** | **Fonctionne également avec** |
> | --- | --- |
> | Windows 8 | Windows 7 |
> | Visual Studio 2012 | Visual Studio 2012 Express pour le Web. Il est automatiquement installé par le Kit de développement logiciel Windows Azure si vous n’avez pas encore Visual Studio 2012 ou Visual Studio 2012 Express pour le Web. Visual Studio 2013 doit fonctionner, mais le didacticiel n’a pas été testé avec lui, et certaines boîtes de dialogue et les sélections de menu sont différentes. Le [version VS 2013 de Windows Azure SDK](https://go.microsoft.com/fwlink/p/?linkid=323510) est requis pour le déploiement de Windows Azure. |
> | .NET 4.5 | La plupart des fonctionnalités indiquées fonctionne dans .NET 4, mais certains ne sont pas. Par exemple, la prise en charge de l’enum dans EF nécessite .NET 4.5. |
> | Entity Framework 5 |  |
> | [Windows Azure SDK 2.1](https://go.microsoft.com/fwlink/p/?linkid=323511) | Si vous ignorez les étapes de déploiement Windows Azure, vous n’avez pas besoin du Kit de développement logiciel. Quand une nouvelle version du SDK est publiée, le lien installera la version plus récente. Dans ce cas, vous devrez peut-être adapter certaines des instructions pour la nouvelle interface utilisateur et les fonctionnalités. |
> 
> ## <a name="questions"></a>Questions
> 
> Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [forum de ASP.NET Entity Framework](https://forums.asp.net/1227.aspx), le [Entity Framework et LINQ to forum d’entités](https://social.msdn.microsoft.com/forums/adodotnetentityframework/threads/), ou [ StackOverflow.com](http://stackoverflow.com/).
> 
> ## <a name="acknowledgments"></a>Accusés de réception
> 
> Consultez le didacticiel dernière dans la série de [accusés de réception et d’une remarque sur VB](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#acknowledgments).
> 
> ## <a name="original-version-of-the-tutorial"></a>Version d’origine du didacticiel
> 
> La version d’origine de ce didacticiel est disponible dans le [le 4.1 EF / MVC 3 livres](https://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#GettingStartedwiththeEntityFramework4.1usingASP.NETMVC).


## <a name="the-contoso-university-web-application"></a>L’Application Web de Contoso University

L’application que vous créez dans ces didacticiels est un site web de l’université simple.

Les utilisateurs peuvent afficher et mettre à jour des étudiants, les cours et les informations de formateur. Voici quelques exemples d’écrans que vous allez créer.

![Students_Index_page](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image1.png)

![](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image2.png)

Le style de l’interface utilisateur de ce site a été conservé proche de ce qui est généré par les modèles prédéfinis, afin de pouvoir le didacticiel concentrer principalement sur l’utilisation d’Entity Framework.

## <a name="prerequisites"></a>Prérequis

Les instructions et les captures d’écran de ce didacticiel supposent que vous utilisez [Visual Studio 2012](https://www.microsoft.com/visualstudio/eng/downloads) ou [Visual Studio 2012 Express pour Web](https://go.microsoft.com/fwlink/?LinkID=275131), avec les dernières mises à jour et Azure SDK pour .NET est installé à partir de juillet, 2013. Vous pouvez obtenir cette le lien suivant :

[Azure SDK pour .NET (Visual Studio 2012)](https://go.microsoft.com/fwlink/?LinkId=254364)

Si vous avez installé Visual Studio, le lien ci-dessus installe les composants manquants. Si vous n’avez pas Visual Studio, le lien installera Visual Studio 2012 Express pour le Web. Vous pouvez utiliser Visual Studio 2013, mais certains écrans et procédures requises sont différentes.

## <a name="create-an-mvc-web-application"></a>Créer une Application Web MVC

Ouvrez Visual Studio et créez un nouveau projet c# nommé « ContosoUniversity » à l’aide de la **ASP.NET MVC 4 Web Application** modèle. Assurez-vous que vous ciblez **.NET Framework 4.5** (vous allez utiliser [ `enum` propriétés](https://msdn.microsoft.com/data/hh859576.aspx), et qui nécessite .NET 4.5).

![New_project_dialog_box](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image3.png)

Dans le **nouveau projet ASP.NET MVC 4** boîte de dialogue Sélectionnez le **Application Internet** modèle.

Laissez le **Razor** sélectionné de moteur d’affichage et laissez le **créer un projet de test unitaire** case à cocher désactivée.

Cliquez sur **OK**.

![Project_template_options](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image4.png)

## <a name="set-up-the-site-style"></a>Définir le style de Site

Quelques modifications configurera le menu de site, la disposition et la page d’accueil.

Ouvrez *Views\Shared\\_Layout.cshtml*et remplacez le contenu du fichier par le code suivant. Les modifications sont mises en surbrillance.

[!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample1.cshtml?highlight=5,15,25-28,43)]

Ce code rend les modifications suivantes :

- Remplace les instances de modèle de « Mon Application MVC ASP.NET » et « votre logo ici » par « Contoso University ».
- Ajoute plusieurs liens d’action qui seront utilisés ultérieurement dans le didacticiel.

Dans *Views\Home\Index.cshtml*, remplacez le contenu du fichier par le code suivant pour éliminer les paragraphes du modèle sur ASP.NET et MVC :

[!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample2.cshtml)]

Dans *Controllers\HomeController.cs*, modifiez la valeur de `ViewBag.Message` dans le `Index` méthode d’Action « Welcome to Contoso University ! », comme illustré dans l’exemple suivant :

[!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample3.cs?highlight=3)]

Appuyez sur CTRL + F5 pour exécuter le site. Vous consultez la page d’accueil avec le menu principal.

![Contoso_University_home_page](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image5.png)

## <a name="create-the-data-model"></a>Créer le modèle de données

Vous allez ensuite créer des classes d’entité pour l’application Contoso University. Vous devez commencer par les trois entités suivantes :

![Class_diagram](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image6.png)

Il existe une relation un-à-plusieurs entre `Student` et `Enrollment` entités, et il existe une relation un-à-plusieurs entre `Course` et `Enrollment` entités. En d’autres termes, un étudiant peut être inscrit dans n’importe quel nombre de cours et un cours peut avoir n’importe quel nombre d’élèves inscrits.

Dans les sections suivantes, vous allez créer une classe pour chacune de ces entités.

> [!NOTE]
> Si vous essayez de compiler le projet avant de terminer la création de toutes ces classes d’entité, vous obtenez des erreurs du compilateur.


### <a name="the-student-entity"></a>L’entité Student

![Student_entity](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image7.png)

Dans le *modèles* dossier, créez *Student.cs* et remplacez le code existant par le code suivant :

[!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample4.cs)]

Le `StudentID` propriété deviendra la colonne de clé primaire de la table de base de données qui correspond à cette classe. Par défaut, Entity Framework interprète une propriété nommée `ID` ou *classname* `ID` comme clé primaire.

Le `Enrollments` propriété est un *propriété de navigation*. Propriétés de navigation contiennent d’autres entités qui sont associées à cette entité. Dans ce cas, le `Enrollments` propriété d’un `Student` entité contiendra tous les `Enrollment` entités qui sont associées à cette `Student` entité. En d’autres termes, si une donnée `Student` ligne dans la base de données possède deux liées `Enrollment` lignes (les lignes qui contiennent la clé primaire de student cette valeur dans leurs `StudentID` colonne clé étrangère), qui `Student` l’entité `Enrollments` propriété de navigation contiendra ces deux `Enrollment` entités.

Propriétés de navigation sont généralement définies en tant que `virtual` afin qu’ils peuvent tirer parti de certaines fonctionnalités d’Entity Framework telles que *chargement différé*. (Chargement différé est expliqué plus loin, dans le [lors de la lecture des données connexes](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) didacticiel plus loin dans cette série.

Si une propriété de navigation peut contenir plusieurs entités (par exemple, les relations plusieurs-à-plusieurs ou un-à-plusieurs), son type doit être une liste dans laquelle les entrées peuvent être ajoutées, supprimées et mis à jour, telles que `ICollection`.

### <a name="the-enrollment-entity"></a>L’entité de l’inscription

![Enrollment_entity](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image8.png)

Dans le *modèles* dossier, créez *Enrollment.cs* et remplacez le code existant par le code suivant :

[!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample5.cs)]

La propriété de classe est une [enum](https://msdn.microsoft.com/data/hh859576.aspx). Le point d’interrogation après la `Grade` déclaration de type indique que le `Grade` propriété est [nullable](https://msdn.microsoft.com/library/2cf62fcy.aspx). Un niveau qui a la valeur null est différent de zéro une note, cela signifie qu’une classe n’est pas connue ou n’a pas encore été affectée.

Le `StudentID` propriété est une clé étrangère, et la propriété de navigation correspondante est `Student`. Un `Enrollment` entité est associée à un `Student` entité, donc la propriété peut contenir uniquement un seul `Student` entité (contrairement à la `Student.Enrollments` propriété de navigation, vous avez vu précédemment, qui peut contenir plusieurs `Enrollment` entités).

Le `CourseID` propriété est une clé étrangère, et la propriété de navigation correspondante est `Course`. Un `Enrollment` entité est associée à un `Course` entité.

### <a name="the-course-entity"></a>L’entité de cours

![Course_entity](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image9.png)

Dans le *modèles* dossier, créez *Course.cs*, en remplaçant le code existant par le code suivant :

[!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample6.cs)]

Le `Enrollments` est une propriété de navigation. A `Course` entité peut être associée à un nombre quelconque de `Enrollment` entités.

Cet exemple plus d’informations sur le [[DatabaseGenerated](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute(v=vs.110).aspx)([valeur DatabaseGeneratedOption](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedoption(v=vs.95).aspx). Aucun)] l’attribut dans l’étape suivante du didacticiel. En fait, cet attribut vous permet d’entrer la clé primaire pour le cours plutôt que d’avoir à la base de données de sa génération.

## <a name="create-the-database-context"></a>Créer le contexte de base de données

La classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données spécifiée est la *contexte de base de données* classe. Vous créez cette classe en dérivant de la [System.Data.Entity.DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx) classe. Dans votre code, vous spécifiez les entités qui sont incluses dans le modèle de données. Vous pouvez également personnaliser le comportement de certaines Entity Framework. Dans ce projet, la classe est nommée `SchoolContext`.

Créez un dossier nommé *DAL* (pour la couche d’accès aux données). Dans ce dossier, créez un nouveau fichier de classe nommé *SchoolContext.cs*et remplacez le code existant par le code suivant :

[!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample7.cs)]

Ce code crée un [DbSet](https://msdn.microsoft.com/library/system.data.entity.dbset(v=VS.103).aspx) propriété pour chaque jeu d’entités. Dans la terminologie Entity Framework, une *jeu d’entités* correspond généralement à une table de base de données et un *entité* correspond à une ligne dans la table.

Le `modelBuilder.Conventions.Remove` instruction dans le [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating(v=vs.103).aspx) méthode fait des noms de table en cours pluralisés. Si vous n’avez pas dans ce cas, les tables générées sont nommés `Students`, `Courses`, et `Enrollments`. Au lieu de cela, les noms de la table seront `Student`, `Course`, et `Enrollment`. Les développeurs ne sont pas tous d’accord sur la nécessité d’utiliser des noms de table au pluriel. Ce didacticiel utilise la forme au singulier, mais le point important est que vous pouvez sélectionner quelle que soit la forme de votre choix en incluant ou l’omission de cette ligne de code.

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

[LocalDB](https://blogs.msdn.com/b/sqlexpress/archive/2011/07/12/introducing-localdb-a-better-sql-express.aspx) est une version allégée du moteur SQL Server Express de base de données qui démarre à la demande et s’exécute en mode utilisateur. Base de données locale s’exécute dans un mode spécial d’exécution de SQL Server Express qui vous permet de travailler avec les bases de données en tant que *.mdf* fichiers. En règle générale, les fichiers de base de données de base de données locale sont conservés dans le *application\_données* dossier d’un projet web. La fonctionnalité d’instance utilisateur dans SQL Server Express vous permet également de travailler avec *.mdf* fichiers, mais la fonctionnalité d’instance utilisateur est déconseillé ; par conséquent, il est recommandé LocalDB pour travailler avec *.mdf* fichiers.

En général, SQL Server Express n’est pas utilisé pour les applications web de production. Base de données locale en particulier n'est pas recommandée pour la production avec une application web, car il n’est pas conçu pour fonctionner avec les services IIS.

Dans Visual Studio 2012 et versions ultérieures, la base de données locale est installé par défaut avec Visual Studio. Dans Visual Studio 2010 et versions antérieures, SQL Server Express (sans LocalDB) est installé par défaut avec Visual Studio. Vous devez installer manuellement si vous utilisez Visual Studio 2010.

Dans ce didacticiel vous travaillerez avec la base de données locale afin que la base de données peut être stockée dans le *application\_données* dossier comme un *.mdf* fichier. Ouvrez la racine *Web.config* et ajoutez une nouvelle chaîne de connexion à la `connectionStrings` collection, comme indiqué dans l’exemple suivant. (Assurez-vous que vous mettez à jour le *Web.config* fichier dans le dossier racine du projet. Il existe également un *Web.config* fichier se trouve dans le *vues* sous-dossier que vous n’avez pas besoin de mettre à jour.)

[!code-xml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample8.xml)]

Par défaut, Entity Framework recherche une chaîne de connexion porte le même nom que le `DbContext` classe (`SchoolContext` pour ce projet). Vous avez ajouté la chaîne de connexion spécifie une base de données de la base de données locale nommée *ContosoUniversity.mdf* situé dans le *application\_données* dossier. Pour plus d’informations, consultez [chaînes de connexion SQL Server pour les Applications Web ASP.NET](https://msdn.microsoft.com/library/jj653752.aspx).

Vous n’avez pas réellement besoin spécifier la chaîne de connexion. Si vous ne fournissez pas une chaîne de connexion, Entity Framework crée une pour vous ; Toutefois, la base de données ne peut pas être dans le *application\_données* dossier de votre application. Pour plus d’informations sur la base de données où sera créé, consultez [Code First pour une base de données](https://msdn.microsoft.com/data/jj193542).

Le `connectionStrings` comporte également une chaîne de connexion nommée `DefaultConnection` qui est utilisé pour la base de données d’appartenance. Vous n’utiliserez pas la base de données d’appartenance de ce didacticiel. La seule différence entre les deux chaînes de connexion est le nom de la base de données et la valeur d’attribut de nom.

## <a name="set-up-and-execute-a-code-first-migration"></a>Configurer et exécuter une Migration du premier Code

Quand vous commencez à développer une application, votre modèle de données modifications fréquentes et chaque fois que les modifications de modèle qu’il est désynchronisé avec la base de données. Vous pouvez configurer pour automatiquement supprimer et recréer la base de données chaque fois que vous modifiez le modèle de données Entity Framework. Cela n’est pas un problème très tôt dans le développement, car les données de test sont recréées facilement, mais une fois que vous avez déployée en production souvent mettre à jour le schéma de base de données sans supprimer la base de données. Permet à la fonctionnalité Migrations Code First mettre à jour la base de données sans devoir supprimer et recréer. Au début du cycle de développement d’un nouveau projet, vous souhaiterez utiliser [DropCreateDatabaseIfModelChanges](https://msdn.microsoft.com/library/gg679604(v=vs.103).aspx) à supprimer, recréer et réamorcer sur la base de données chaque fois que les modifications apportées au modèle. Un vous préparez à déployer votre application, vous pouvez convertir l’approche de migrations. Pour ce didacticiel, vous allez uniquement utiliser les migrations. Pour plus d’informations, consultez [Migrations Code First](https://msdn.microsoft.com/data/jj591621) et [Migrations capture série](https://blogs.msdn.com/b/adonet/archive/2014/03/12/migrations-screencast-series.aspx).

### <a name="enable-code-first-migrations"></a>Activer les Migrations Code First

1. À partir de la **outils** menu, cliquez sur **Gestionnaire de Package de bibliothèque** , puis **Package Manager Console**.

    ![Selecting_Package_Manager_Console](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image10.png)
2. À la `PM>` invite Entrez la commande suivante :

    [!code-powershell[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample9.ps1)]

    ![commande Enable-migrations](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image11.png)

    Cette commande crée un *Migrations* dossier dans le projet ContosoUniversity et il place dans ce dossier un *Configuration.cs* fichier que vous pouvez modifier pour configurer des Migrations.

    ![Dossier migrations](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image12.png)

    Le `Configuration` classe inclut un `Seed` méthode qui est appelée lorsque la base de données est créé et chaque fois qu’il est mis à jour après modification du modèle de données.

    [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample10.cs)]

    Le but de cette `Seed` consiste à vous permettent d’insérer des données de test dans la base de données une fois que le Code First crée ou met à jour.

### <a name="set-up-the-seed-method"></a>Définir la méthode de valeur initiale

Le [Seed](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) méthode s’exécute lors de Migrations Code First crée la base de données et chaque fois qu’il met à jour la base de données pour la dernière migration. L’objectif de la méthode de la valeur initiale est pour vous permettre d’insérer des données dans vos tables avant que l’application accède à la base de données pour la première fois.

Dans les versions antérieures de Code First, avant la migration, il était courant pour `Seed` méthodes pour insérer des données de test, car avec chaque modification du modèle pendant le développement la base de données a dû être complètement supprimé et recréé à partir de zéro. Avec Migrations Code First, test, les données sont conservées après des modifications de la base de données, par conséquent, y compris les données de test dans le [Seed](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) méthode n’est généralement pas nécessaire. En fait, vous ne souhaitez pas que le `Seed` méthode pour insérer des données de test si vous allez utiliser les Migrations à déployer la base de données en production, car le `Seed` méthode s’exécute en production. Dans ce cas, vous souhaitez le `Seed` méthode pour insérer dans la base de données uniquement les données que vous souhaitez insérer dans la production. Par exemple, vous souhaitez la base de données pour inclure les noms de service réel dans le `Department` lorsque l’application est disponible en production de la table.

Pour ce didacticiel, vous allez utiliser les Migrations pour le déploiement, mais votre `Seed` méthode insère des données de test tout de même pour le rendre plus facile de déterminer le fonctionne de la fonctionnalité de l’application sans avoir à insérer manuellement une grande quantité de données.

1. Remplacez le contenu de la *Configuration.cs* fichier avec le code suivant, qui charge des données de test dans la base de données. 

    [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample11.cs)]

    Le [Seed](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) méthode prend l’objet de contexte de base de données en tant que paramètre d’entrée, et le code dans la méthode utilise cet objet pour ajouter de nouvelles entités à la base de données. Pour chaque type d’entité, le code crée une collection de nouvelles entités, les ajoute à la liste appropriée [DbSet](https://msdn.microsoft.com/library/system.data.entity.dbset(v=vs.103).aspx) propriété, puis enregistre les modifications apportées à la base de données. Il n’est pas nécessaire d’appeler le [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) méthode après chaque groupe d’entités, comme le fait ici, mais cela vous permet de localiser la source d’un problème si une exception se produit pendant que le code écrit dans la base de données.

    Certains des instructions qui insèrent des données utilisent le [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) méthode pour effectuer une opération « upsert ». Étant donné que la `Seed` méthode s’exécute avec chaque migration, vous ne peut pas simplement insérer des données, car les lignes que vous essayez d’ajouter sera déjà présente après la migration initiale qui crée la base de données. L’opération « upsert » empêche toute erreur qui se passerait si vous essayez d’insérer une ligne qui existe déjà, mais il ***substitue*** les modifications apportées aux données que vous avez apportées lors du test de l’application. Avec des données de test dans certaines tables vous pouvez qui doit se produire : dans certains cas lorsque vous modifiez des données pendant le test vous souhaitez que vos modifications restant après les mises à jour de la base de données. Dans ce cas, vous souhaitez effectuer une opération d’insertion conditionnel : insérer une ligne uniquement si elle n’existe pas déjà. La méthode de valeur initiale utilise les deux approches.

    Le premier paramètre passé à la [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) méthode spécifie la propriété à utiliser pour vérifier si une ligne existe déjà. Pour les données de student de test que vous fournissez, le `LastName` propriété peut être utilisée à cet effet dans la mesure où chaque nom dans la liste est unique :

    [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample12.cs)]

    Ce code suppose que les noms sont uniques. Si vous ajoutez manuellement un étudiant avec un nom en double, vous obtenez l’exception suivante, la prochaine fois que vous effectuez une migration.

    La séquence contient plusieurs éléments

    Pour plus d’informations sur la `AddOrUpdate` méthode, consultez [soyez prudent avec EF 4.3 AddOrUpdate méthode](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/) sur le blog de Julie Lerman.

    Le code qui ajoute `Enrollment` entités n’utilise pas le `AddOrUpdate` (méthode). Il vérifie si une entité existe déjà et qu’il insère l’entité si elle n’existe pas. Cette approche conserve les modifications que vous apportez à un niveau d’inscription lors de l’exécution de migrations. Le code effectue une itération sur chaque membre de la `Enrollment` [liste](https://msdn.microsoft.com/library/6sh2ey19.aspx) et si l’inscription est introuvable dans la base de données, il ajoute l’inscription à la base de données. La première fois que vous mettez à jour la base de données, la base de données sera vide, donc il ajoute chaque inscription.

    [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample13.cs)]

    Pour plus d’informations sur la façon de déboguer le `Seed` (méthode) et la gestion des données redondantes, telles que deux stagiaires nommés « Alexander Carson », consultez [Seeding et les bases de données Entity Framework (EF) débogage](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) sur le blog de Rick Anderson.
2. Générez le projet.

### <a name="create-and-execute-the-first-migration"></a>Créer et exécuter la Migration en premier

1. Dans la fenêtre de Console du Gestionnaire de Package, entrez les commandes suivantes : 

    [!code-powershell[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample14.ps1)]

    ![](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image13.png)

    Le `add-migration` commande ajoute le dossier Migrations un *[DateStamp]\_InitialCreate.cs* fichier qui contient le code qui crée la base de données. Le premier paramètre (`InitialCreate)` est utilisé pour le fichier de nommer et peut être tout ce que vous voulez ; vous choisissez généralement un mot ou une expression qui résume ce qui est effectué dans la migration. Par exemple, vous pouvez nommer une migration ultérieure &quot;AddDepartmentTable&quot;.

    ![Dossier migrations avec la migration initiale](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image14.png)

    Le `Up` méthode de la `InitialCreate` classe crée les tables de base de données qui correspondent aux jeux d’entités de modèle de données, et le `Down` méthode les supprime. Appels de migrations le `Up` méthode pour implémenter les modifications de modèle de données pour une migration. Lorsque vous entrez une commande pour restaurer la mise à jour, les appels de Migrations le `Down` (méthode). Le code suivant montre le contenu de la `InitialCreate` fichier :

    [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample15.cs)]

    Le `update-database` commande s’exécute le `Up` méthode pour créer la base de données, puis il exécute la `Seed` méthode pour remplir la base de données.

Une base de données SQL Server a maintenant été créé pour votre modèle de données. Le nom de la base de données est *ContosoUniversity*et le *.mdf* fichier est de votre projet *application\_données* dossier parce que c’est ce que vous avez spécifié dans votre chaîne de connexion.

Vous pouvez utiliser **l’Explorateur de serveurs** ou **l’Explorateur d’objets SQL Server** (SSOX) pour afficher la base de données dans Visual Studio. Pour ce didacticiel, vous allez utiliser **l’Explorateur de serveurs**. Dans Visual Studio Express 2012 pour le Web, **l’Explorateur de serveurs** est appelé **l’Explorateur de base de données**.

1. À partir de la **vue** menu, cliquez sur **l’Explorateur de serveurs**.
2. Cliquez sur le **ajouter une connexion** icône.

    ![](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image15.png)
3. Si vous êtes invité au **choisir la Source de données** boîte de dialogue, cliquez sur **Microsoft SQL Server**, puis cliquez sur **continuer**.  
  
    ![](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image16.png)
4. Dans le **ajouter une connexion** boîte de dialogue, entrez **(localdb) \v11.0** pour le **nom du serveur**. Sous **sélectionner ou entrer un nom de base de données**, sélectionnez **ContosoUniversity.**  
  
    ![](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image17.png)
5. Cliquez sur **OK**.
6. Développez **SchoolContext** , puis **Tables**.  
  
    ![](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image18.png)
7. Avec le bouton droit le **Student** de table et cliquez sur **afficher les données de Table** pour afficher les colonnes qui ont été créés et les lignes qui ont été insérées dans la table.

    ![Table d’étudiants](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image19.png)

## <a name="creating-a-student-controller-and-views"></a>Création d’un contrôleur de Student et des vues

L’étape suivante consiste à créer un ASP.NET MVC de contrôleur et des vues dans votre application peut utiliser une de ces tables.

1. Pour créer un `Student` contrôleur, cliquez sur le **contrôleurs** dossier **l’Explorateur de solutions**, sélectionnez **ajouter**, puis cliquez sur **contrôleur** . Dans le **ajouter un contrôleur** boîte de dialogue zone, effectuez les sélections suivantes, puis sur **ajouter**: 

    - Nom du contrôleur : **StudentController**.
    - Modèle : **contrôleur MVC avec des actions de lecture/écriture et de vues, utilisant Entity Framework**.
    - Classe de modèle : **étudiant (ContosoUniversity.Models)**. (Si vous ne voyez pas cette option dans la liste déroulante, générez le projet, puis réessayez.)
    - Classe de contexte de données : **SchoolContext (ContosoUniversity.Models)**.
    - Affichages : **Razor (CSHTML)**. (La valeur par défaut.)

    ![Add_Controller_dialog_box_for_Student_controller](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image20.png)
- Visual Studio ouvre le *Controllers\StudentController.cs* fichier. Vous consultez qu'une variable de classe a été créée qui instancie un objet de contexte de base de données :

    [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample16.cs)]

    Le `Index` méthode d’action Obtient une liste d’étudiants à partir de la *étudiants* entité définie par la lecture de la `Students` propriété de l’instance de contexte de base de données :

    [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample17.cs)]

    Le *Student\Index.cshtml* affiche cette liste dans une table :

    [!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample18.cshtml)]
- Appuyez sur CTRL+F5 pour exécuter le projet.

    Cliquez sur le **étudiants** onglet pour afficher les données de test qui le `Seed` méthode inséré.

    ![Page d’Index étudiant](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image21.png)

## <a name="conventions"></a>Conventions

La quantité de code que vous deviez écrire dans l’ordre pour Entity Framework pouvoir créer une base de données complète pour vous est minime en raison de l’utilisation de *conventions*, ou les hypothèses qu’Entity Framework. Certaines d'entre elles ont déjà été notées :

- Les formulaires pluralized des noms de classe d’entité sont utilisés comme noms de tables.
- Noms de propriété d’entité sont utilisées pour les noms de colonne.
- Propriétés de l’entité qui sont nommées `ID` ou *classname* `ID` sont reconnus en tant que propriétés de clé primaire.

Vous avez vu que les conventions peuvent être remplacées (par exemple, vous spécifié que les noms de table ne doit pas être pluralisés), et vous en apprendrez davantage sur les conventions et comment les remplacer dans les [création d’un modèle de données plus complexes](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md) didacticiel plus loin dans cette série. Pour plus d’informations, consultez [premier des Conventions de Code](https://msdn.microsoft.com/data/jj679962).

## <a name="summary"></a>Récapitulatif

Vous venez de créer une application simple qui utilise Entity Framework et SQL Server Express pour stocker et afficher des données. Dans ce didacticiel, vous allez apprendre à effectuer CRUD de base (créer, lire, mettre à jour, supprimer) les opérations. Vous pouvez laisser des commentaires au bas de cette page. Faites-nous savoir comment vous avez aimé cette partie du didacticiel et comment nous pouvons l’améliorer.

Vous trouverez des liens vers d’autres ressources Entity Framework dans le [ASP.NET Data Access Content Map](../../../../whitepapers/aspnet-data-access-content-map.md).

>[!div class="step-by-step"]
[Next](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)

---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
title: "Code First Migrations et déploiement avec Entity Framework dans une Application ASP.NET MVC | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/07/2014
ms.topic: article
ms.assetid: d4dfc435-bda6-4621-9762-9ba270f8de4e
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 2294f2aba3f765d7849d1f407e85f424dc8b2518
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="code-first-migrations-and-deployment-with-the-entity-framework-in-an-aspnet-mvc-application"></a>Code First Migrations et déploiement avec Entity Framework dans une Application ASP.NET MVC
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) ou [télécharger le PDF](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio 2013. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).

Jusqu'à présent l’application a été exécuté localement dans IIS Express sur votre ordinateur de développement. Pour rendre une application réelle disponible pour d’autres personnes à utiliser sur Internet, vous devez déployer vers un fournisseur d’hébergement web. Dans ce didacticiel, vous allez déployer l’application Contoso University au cloud dans Azure.

Ce didacticiel contient les sections suivantes :

- Activer les Migrations Code First. La fonctionnalité de migration vous permet de modifier le modèle de données et de déployer vos modifications en production en mettant à jour le schéma de base de données sans devoir supprimer et recréer la base de données.
- Déployer sur Azure. Cette étape est facultative. Vous pouvez continuer avec les autres didacticiels sans avoir déployé le projet.

Nous vous recommandons d’utiliser un processus d’intégration continue avec contrôle de code source pour le déploiement, mais ce didacticiel ne couvre pas ces rubriques. Pour plus d’informations, consultez la [contrôle de code source](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) et [intégration continue](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) chapitres de la [génération d’applications Cloud réel avec Azure](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction) livres.

## <a name="enable-code-first-migrations"></a>Activer les Migrations Code First

Lorsque vous développez une application, votre modèle de données change fréquemment et chaque fois que les modifications apportées au modèle, il est désynchronisé avec la base de données. Vous avez configuré pour automatiquement supprimer et recréer la base de données chaque fois que vous modifiez le modèle de données Entity Framework. Lorsque vous ajouter, supprimer ou modifier des classes d’entité ou modifier votre `DbContext` classe, la prochaine fois que vous exécutez l’application elle supprime de votre base de données existante, crée un nouveau qui correspond au modèle et l’alimente avec les données de test d’automatiquement.

Cette méthode de synchronisation de la base de données avec le modèle de données fonctionne bien jusqu'à ce que vous déployez l’application en production. Lorsque l’application est en cours d’exécution en production, il stocke généralement les données que vous souhaitez conserver, et vous ne voulez pas perdre tous les éléments chaque fois que vous apportez des modifications telles que l’ajout d’une nouvelle colonne. Le [Migrations Code First](https://msdn.microsoft.com/data/jj591621) fonctionnalité résout ce problème en activant Code First mettre à jour le schéma de base de données au lieu de devoir supprimer et recréer la base de données. Dans ce didacticiel, vous allez déployer l’application, et pour préparer pour que vous allez activer les Migrations.

1. Désactiver l’initialiseur que vous avez configurée précédemment en supprimant ou en supprimant le `contexts` élément que vous avez ajouté au fichier Web.config de l’application.

    [!code-xml[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.xml?highlight=2,6)]
2. Également dans l’application *Web.config* , modifiez le nom de la base de données dans la chaîne de connexion à ContosoUniversity2.

    [!code-xml[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.xml?highlight=2)]

    Cette modification affecte le projet afin que la première migration créera une nouvelle base de données. Cela n’est pas nécessaire, mais vous le verrez plus tard pourquoi il est judicieux.
3. À partir de la **outils** menu, cliquez sur **Gestionnaire de Package de bibliothèque** , puis **Package Manager Console**.

    ![Selecting_Package_Manager_Console](https://asp.net/media/4336350/1pm.png)
4. À la `PM>` invite Entrez les commandes suivantes :

    `enable-migrations`  
    `add-migration InitialCreate`

    ![commande Enable-migrations](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

    Le `enable-migrations` commande crée un *Migrations* dossier dans le projet ContosoUniversity et il place dans ce dossier un *Configuration.cs* fichier que vous pouvez modifier pour configurer des Migrations.

    (À l’issue de l’étape précédente qui dirige vous permet de modifier le nom de la base de données, les Migrations seront rechercher la base de données existante et faire automatiquement la `add-migration` commande. OK, cela signifie simplement que ne sera pas exécuter un test du code migrations avant de déployer la base de données. Ultérieures lorsque vous exécutez le `update-database` commande rien ne se produira, car la base de données existe déjà.)

    ![Dossier migrations](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

    Comme la classe de l’initialiseur que vous avez vu précédemment, le `Configuration` classe inclut un `Seed` (méthode).

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

    L’objectif de la [Seed](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) méthode consiste à vous permettent d’insérer ou mettre à jour des données de test après le premier Code crée ou met à jour la base de données. La méthode est appelée lorsque la base de données est créé et chaque fois que le schéma de base de données est mise à jour après la modification d’un modèle de données.

### <a name="set-up-the-seed-method"></a>Définir la méthode de valeur initiale

Lorsque vous supprimez et recréer la base de données pour chaque modification de modèle de données, vous utilisez la classe de l’initialiseur `Seed` méthode pour insérer des données de test, car après chaque modification de modèle, la base de données est supprimée et toutes les données de test sont perdus. Avec Migrations Code First, test, les données sont conservées après des modifications de la base de données, par conséquent, y compris les données de test dans le [Seed](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) méthode n’est généralement pas nécessaire. En fait, vous ne souhaitez pas que le `Seed` méthode pour insérer des données de test si vous allez utiliser les Migrations à déployer la base de données en production, car le `Seed` méthode s’exécute en production. Dans ce cas, vous souhaitez le `Seed` méthode pour insérer dans la base de données uniquement les données dont vous avez besoin en production. Par exemple, vous souhaitez la base de données pour inclure les noms de service réel dans le `Department` lorsque l’application est disponible en production de la table.

Pour ce didacticiel, vous allez utiliser les Migrations pour le déploiement, mais votre `Seed` méthode insère des données de test tout de même pour le rendre plus facile de déterminer le fonctionne de la fonctionnalité de l’application sans avoir à insérer manuellement une grande quantité de données.

1. Remplacez le contenu de la *Configuration.cs* fichier avec le code suivant, qui charge des données de test dans la base de données. 

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

    Le [Seed](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) méthode prend l’objet de contexte de base de données en tant que paramètre d’entrée, et le code dans la méthode utilise cet objet pour ajouter de nouvelles entités à la base de données. Pour chaque type d’entité, le code crée une collection de nouvelles entités, les ajoute à la liste appropriée [DbSet](https://msdn.microsoft.com/library/system.data.entity.dbset(v=vs.103).aspx) propriété, puis enregistre les modifications apportées à la base de données. Il n’est pas nécessaire d’appeler le [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) méthode après chaque groupe d’entités, comme le fait ici, mais cela vous permet de localiser la source d’un problème si une exception se produit pendant que le code écrit dans la base de données.

    Certains des instructions qui insèrent des données utilisent le [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) méthode pour effectuer une opération « upsert ». Étant donné que la `Seed` méthode s’exécute chaque fois que vous exécutez le `update-database` de commande, généralement après chaque migration, vous ne peut pas simplement insérer des données, car les lignes que vous essayez d’ajouter sera déjà présente après la migration initiale qui crée la base de données. L’opération « upsert » empêche toute erreur qui se passerait si vous essayez d’insérer une ligne qui existe déjà, mais il ***substitue*** les modifications apportées aux données que vous avez apportées lors du test de l’application. Avec des données de test dans certaines tables vous pouvez qui doit se produire : dans certains cas lorsque vous modifiez des données pendant le test vous souhaitez que vos modifications restant après les mises à jour de la base de données. Dans ce cas, vous souhaitez effectuer une opération d’insertion conditionnel : insérer une ligne uniquement si elle n’existe pas déjà. La méthode de valeur initiale utilise les deux approches.

    Le premier paramètre passé à la [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) méthode spécifie la propriété à utiliser pour vérifier si une ligne existe déjà. Pour les données de student de test que vous fournissez, le `LastName` propriété peut être utilisée à cet effet dans la mesure où chaque nom dans la liste est unique :

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

    Ce code suppose que les noms sont uniques. Si vous ajoutez manuellement un étudiant avec un nom en double, vous obtenez l’exception suivante, la prochaine fois que vous effectuez une migration.

    La séquence contient plusieurs éléments

    Pour plus d’informations sur la gestion des données redondantes, telles que deux stagiaires nommés « Alexander Carson », consultez [Seeding et les bases de données Entity Framework (EF) débogage](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) sur le blog de Rick Anderson. Pour plus d’informations sur la `AddOrUpdate` méthode, consultez [soyez prudent avec EF 4.3 AddOrUpdate méthode](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/) sur le blog de Julie Lerman.

    Le code qui crée `Enrollment` entités suppose que le `ID` valeur dans les entités dans le `students` collection, bien que vous n’avez pas définir cette propriété dans le code qui crée la collection.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs?highlight=2)]

    Vous pouvez utiliser la `ID` propriété ici, car le `ID` valeur est définie lorsque vous appelez `SaveChanges` pour la `students` collection. EF obtient automatiquement la valeur de clé primaire lorsqu’il insère une entité dans la base de données, et il met à jour le `ID` propriété de l’entité dans la mémoire.

    Le code qui ajoute chaque `Enrollment` entité à la `Enrollments` jeu d’entités n’utilise pas le `AddOrUpdate` (méthode). Il vérifie si une entité existe déjà et qu’il insère l’entité si elle n’existe pas. Cette approche conserve les modifications que vous apportez à un niveau d’inscription à l’aide de l’interface utilisateur de l’application. Le code effectue une itération sur chaque membre de la `Enrollment` [liste](https://msdn.microsoft.com/library/6sh2ey19.aspx) et si l’inscription est introuvable dans la base de données, il ajoute l’inscription à la base de données. La première fois que vous mettez à jour la base de données, la base de données sera vide, donc il ajoute chaque inscription.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]
2. Générez le projet.

### <a name="execute-the-first-migration"></a>Exécution de la Migration en premier

Lorsque vous avez exécuté le `add-migration` Migrations généré le code susceptibles de créer la base de données à partir de zéro de la commande. Ce code est également dans le *Migrations* dossier, dans le fichier nommé  *&lt;timestamp&gt;\_InitialCreate.cs*. Le `Up` méthode de la `InitialCreate` classe crée les tables de base de données qui correspondent aux jeux d’entités de modèle de données, et le `Down` méthode les supprime.

[!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

Appels de migrations le `Up` méthode pour implémenter les modifications de modèle de données pour une migration. Lorsque vous entrez une commande pour restaurer la mise à jour, les appels de Migrations le `Down` (méthode).

Il s’agit de la migration initiale qui a été créée lorsque vous avez entré la `add-migration InitialCreate` commande. Le paramètre (`InitialCreate` dans l’exemple) est utilisé pour le fichier de nommer et peut être tout ce que vous voulez ; vous choisissez généralement un mot ou une expression qui résume ce qui est effectué dans la migration. Par exemple, vous pouvez nommer une migration ultérieure &quot;AddDepartmentTable&quot;.

Si vous avez créé la migration initiale lors de la base de données existe déjà, le code de création de base de données est généré, mais ne doit pas nécessairement exécuter, car la base de données correspond déjà le modèle de données. Lorsque vous déployez l’application vers un autre environnement dans lequel la base de données n’existe pas encore, ce code sera exécuté pour créer votre base de données, il est donc judicieux de tester au préalable. C’est pourquoi vous avez modifié le nom de la base de données dans la chaîne de connexion précédemment--afin que les migrations peuvent créer un nouveau à partir de zéro.

1. Dans le **Package Manager Console** fenêtre, entrez la commande suivante :

    `update-database`

    ![](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

    Le `update-database` commande s’exécute le `Up` méthode pour créer la base de données, puis il exécute la `Seed` méthode pour remplir la base de données. Le même processus s’exécutera automatiquement en production après avoir déployé l’application, comme vous le verrez dans la section suivante.
- Utilisez **l’Explorateur de serveurs** pour inspecter la base de données comme vous le faisiez dans le premier didacticiel, exécutez l’application pour vérifier que tout fonctionne toujours le même qu’avant.

## <a name="deploy-to-azure"></a>Déployer vers Azure

Jusqu'à présent l’application a été exécuté localement dans IIS Express sur votre ordinateur de développement. Pour le rendre disponible pour d’autres personnes à utiliser sur Internet, vous devez déployer vers un fournisseur d’hébergement web. Dans cette section du didacticiel, vous allez le déployer vers Azure. Cette section est facultative. Vous pouvez ignorer cette étape et continuer le didacticiel suivant, ou vous pouvez adapter les instructions de cette section pour un autre fournisseur d’hébergement de votre choix.

### <a name="using-code-first-migrations-to-deploy-the-database"></a>À l’aide de Migrations Code First pour déployer la base de données

Pour déployer la base de données, vous utiliserez des Migrations Code First. Lorsque vous créez le profil de publication que vous utilisez pour configurer les paramètres de déploiement à partir de Visual Studio, vous devez sélectionner une case à cocher intitulée **mise à jour de la base de données**. Ce paramètre oblige le processus de déploiement configurer automatiquement l’application *Web.config* des fichiers sur le serveur de destination afin qu’utilise le premier Code la `MigrateDatabaseToLatestVersion` classe de l’initialiseur.

Visual Studio ne fait rien avec la base de données pendant le processus de déploiement pendant qu’il copie de votre projet sur le serveur de destination. Lorsque vous exécutez l’application déployée, et il accède à la base de données pour la première fois après le déploiement, le premier Code vérifie de si la base de données correspond au modèle de données. S’il existe une incompatibilité, Code First crée automatiquement la base de données (s’il n’existe pas encore) ou met à jour le schéma de base de données vers la dernière version (si une base de données existe mais ne correspond pas au modèle). Si l’application implémente une Migrations `Seed` méthode, l’exécution de la méthode après la création de la base de données ou le schéma est mis à jour.

Vos Migrations `Seed` méthode insère les données de test. Si vous déployez dans un environnement de production, vous devrez modifier le `Seed` méthode afin qu’il insère uniquement les données que vous souhaitez insérer dans votre base de données de production. Par exemple, dans votre modèle de données en cours, vous souhaiterez ont cours réels, mais les étudiants fictifs dans la base de données de développement. Vous pouvez écrire un `Seed` méthode charger à la fois dans le développement, puis commentez les étudiants fictifs avant de déployer en production. Ou vous pouvez écrire un `Seed` méthode pour charger uniquement des cours et entrer manuellement des étudiants fictifs dans la base de données de test à l’aide de l’interface utilisateur de l’application.

### <a name="get-an-azure-account"></a>Obtenir un compte Azure

Vous devez avoir un compte Azure. Si vous n’en avez pas encore, mais vous disposez d’un abonnement Visual Studio, vous pouvez [activer votre abonnement](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Sinon, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [d’évaluation gratuite Azure](https://azure.microsoft.com/free/).

### <a name="create-a-web-site-and-a-sql-database-in-azure"></a>Créer un site web et une base de données SQL dans Azure

Votre application web dans Azure s’exécute dans un environnement d’hébergement partagé, ce qui signifie qu’il s’exécute sur des machines virtuelles (VM) qui sont partagés avec d’autres clients Azure. Un environnement d’hébergement partagé est un moyen économique pour commencer dans le cloud. Ultérieurement, si le trafic web augmente, l’application peut évoluer pour répondre aux besoins en exécutant sur des machines virtuelles dédiées. Pour en savoir plus sur les Options de tarification pour Azure App Service, consultez la documentation sur [Docs de Azure](https://azure.microsoft.com/pricing/details/app-service/)

Vous allez déployer la base de données à la base de données SQL Azure. Base de données SQL est un service de base de données relationnelle informatique qui repose sur les technologies SQL Server. Outils et applications qui fonctionnent avec SQL Server fonctionnent également avec la base de données SQL.

1. Dans le [portail de gestion Azure](https://portal.azure.com), cliquez sur **nouveau** dans l’onglet gauche, cliquez sur **afficher tous les** dans le nouveau panneau, puis cliquez sur **Web App & SQL** dans le **Web** Section et enfin **créer**.

    ![Nouveau bouton dans le portail de gestion](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/CreateWeb-Sql.png)

 Le **nouvelle application Web & SQL - créer** Assistant s’ouvre.

2. Dans le panneau, entrez une chaîne dans le **nom de l’application** à cocher pour utiliser l’URL unique pour votre application. L’URL complète consiste à ce que vous entrez ici ainsi que le domaine par défaut des Services d’application Azure (. azurewebsites.net). Si le **nom de l’application** est déjà utilisé, l’Assistant vous avertit avec une croix rouge *le nom de l’application n’est pas disponible* message. Si le **nom de l’application** est disponible, vous obtiendrez une coche verte.

    ![Créer avec un lien de base de données dans le portail de gestion](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Create-WebApp.png)

3. Dans le **abonnement** liste déroulante, choisissez l’abonnement Azure dans lequel vous souhaitez le **du Service d’applications** doit résider.

4. Dans le **groupe de ressources** zone de texte, choisissez un groupe de ressources ou créez-en un. Ce paramètre spécifie votre site web s’exécutera dans quel centre de données. Pour plus d’informations sur les groupes de ressources, consultez la documentation sur [Azure Docs](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups).
5. Créer un nouveau **Plan App Service** en cliquant sur le *section App Service*, **créer un nouveau**et renseignez **plan App Service** (peut être identique à celui Service d’application), **emplacement**, et **niveau tarifaire** (il existe une option gratuite).

    ![](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Create-AppService.png)
6. Cliquez sur le **base de données SQL**, puis choisissez *créer un nouveau* ou sélectionnez une base de données existante

    ![](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Create-Database.png)

7. Dans le **nom** , entrez un nom pour votre base de données.
8. Cliquez sur le **serveur cible** boîte, sélectionnez **créer un nouveau serveur**. Vous pouvez également, si vous avez créé précédemment d’un serveur, vous pouvez sélectionner ce serveur à partir de la liste des serveurs disponibles.
9. Choisissez **niveau tarifaire** , choisissez *libre*. Si des ressources supplémentaires sont nécessaires, la base de données peut être mis à l’échelle à tout moment. Pour en savoir plus sur la tarification de SQL Azure, consultez la documentation sur [Azure Docs](https://azure.microsoft.com/pricing/details/sql-database/).
10. Modifier [classement](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support) en fonction des besoins.
11. Entrez un administrateur **nom d’utilisateur administrateur de SQL** et **mot de passe administrateur SQL**. Si vous avez sélectionné **serveur de base de données SQL**, vous n’êtes pas entrer un nom existant et un mot de passe ici, vous entrez un nouveau nom et mot de passe que vous définissez maintenant pour une utilisation ultérieure lorsque vous accédez à la base de données. Si vous avez sélectionné un serveur que vous avez créé précédemment, vous devez entrer les informations d’identification pour ce serveur.
12. Collecte de données de télémétrie peut être activée pour le Service d’applications à l’aide d’Application Insights. Application Insights avec peu de configuration collecte les événements précieuses, exception, dépendance, demande et les informations de trace. Pour en savoir plus sur Application Insights, prise en main de [Azure Docs](https://azure.microsoft.com/services/application-insights/).
12. Cliquez sur **créer** en bas du panneau pour indiquer que vous avez terminé.
  
 Le portail de gestion renvoie à la page de tableaux de bord et le **Notifications** panneau en haut de la page indique que le site est créé. Après un certain temps (en général moins d’une minute), il y aura une notification indiquant que le déploiement a réussi. Dans la barre de navigation à gauche, la nouvelle **du Service d’applications** s’affiche dans le *des Services d’application* section et les nouveaux **base de données SQL** s’affiche dans le *bases de données SQL*  section.

### <a name="deploy-the-application-to-azure"></a>Déployer l’application sur Azure

1. Dans Visual Studio, cliquez sur le projet dans **l’Explorateur de solutions** et sélectionnez **publier** dans le menu contextuel.
  
    ![Publier dans le menu contextuel du projet](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)
2. Dans le **profil** onglet de la **publier le site Web** Assistant, cliquez sur **Microsoft Azure App Service**.
  
    ![Importer les paramètres de publication](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Publish-ChooseTarget.png)
3. Si vous n’avez pas précédemment ajouté votre abonnement Azure dans Visual Studio, effectuez les opérations sur l’écran. Ces étapes activent Visual Studio pour se connecter à votre abonnement Azure ainsi que la liste des **des Services d’application** inclura votre site web.
 
4. Sélectionnez le **abonnement** vous avez ajouté le Service d’application, il doit le **Plan App Service** dossier de votre application de Service est une partie, et enfin la **du Service d’applications** elle-même suivie **Ok**.

    ![Sélectionnez le Service d’applications](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Publish-AppService.png)
5. Une fois que le profil a été configuré, le **connexion** onglet s’affiche. Cliquez sur **valider la connexion** pour vous assurer que les paramètres sont corrects

    ![Valider la connexion](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Publish-Connection.png)
7. Lorsque la connexion a été validée, une coche verte s’affiche en regard du **valider la connexion** bouton. Cliquez sur **Suivant**.
  
    ![Connexion validée avec succès](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Publish-SettingsValidated.png)
8. Ouvrez le **chaîne de connexion à distance** liste déroulante sous **SchoolContext** et sélectionnez la chaîne de connexion pour la base de données que vous avez créé.
9. Sélectionnez **base de données de mise à jour**.

    ![Onglet Paramètres](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Publish-Settings.png)

    Ce paramètre oblige le processus de déploiement configurer automatiquement l’application *Web.config* des fichiers sur le serveur de destination afin qu’utilise le premier Code la `MigrateDatabaseToLatestVersion` classe de l’initialiseur.
10. Cliquez sur **Suivant**.
11. Dans le **aperçu** , cliquez sur **démarrer la version préliminaire**.
  
    ![Bouton du démarrage dans l’onglet d’aperçu](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Publish-Preview.png)
  
 L’onglet affiche une liste des fichiers qui seront copiés vers le serveur. Affichage de l’aperçu n’est pas requise pour publier l’application, mais elle est une fonction utile de connaître. Dans ce cas, vous n’avez pas besoin de faire quelque chose avec la liste des fichiers qui s’affiche. La prochaine fois que vous déployez cette application, seuls les fichiers qui ont été modifiés seront dans cette liste.
    ![Sortie de fichier du démarrage](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Publish-PreviewLoaded.png)

12. Cliquez sur **Publier**.
 Visual Studio lance le processus de copie des fichiers vers le serveur Azure.
13. Le **sortie** fenêtre affiche les actions de déploiement ont été effectuées et signale la réussite du déploiement.
  
    ![Fenêtre Sortie reporting réussir le déploiement](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Publish-BuildOutput.png)
14. Lors du déploiement réussi, le navigateur par défaut s’ouvre automatiquement à l’URL du site web déployé.
 L’application que vous avez créé est en cours d’exécution dans le cloud. 
  
    ![Students_index_page_with_paging](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Publish-Site.png)

À ce stade votre *SchoolContext* base de données a été créé dans la base de données SQL Azure, car vous avez sélectionné **exécuter fonctionnalité Migrations Code First (s’exécute sur le démarrage de l’application)**. Le *Web.config* dans le site web déployé a été modifié afin que le [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx) initialiseur s’exécute la première fois que votre code lit ou écrit des données dans la base de données (qui s’est produite Lorsque vous avez sélectionné le **étudiants** onglet) :

![](https://asp.net/media/4367421/mig.png)

Le processus de déploiement créé également une nouvelle chaîne de connexion *(SchoolContext\_DatabasePublish*) pour les Migrations Code First à utiliser pour la mise à jour le schéma de base de données et d’amorçage de la base de données.

![Chaîne de connexion Database_Publish](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image26.png)

Vous pouvez rechercher la version déployée du fichier Web.config sur votre ordinateur dans *ContosoUniversity\obj\Release\Package\PackageTmp\Web.config*. Vous pouvez accéder à la version déployée *Web.config* fichier lui-même à l’aide de FTP. Pour obtenir des instructions, consultez [déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement d’une mise à jour du Code](xref:web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update). Suivez les instructions qui commencent par « pour utiliser un outil FTP, vous avez besoin de trois opérations : l’URL du serveur FTP, le nom d’utilisateur et le mot de passe. »

> [!NOTE]
> L’application web n’implémente pas la sécurité, toute personne qui recherche l’URL permettant de changer les données. Pour obtenir des instructions sur la façon de sécuriser le site web, consultez [déployer une application sécurisée ASP.NET MVC avec l’appartenance, OAuth et base de données SQL Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Vous pouvez empêcher d’autres personnes d’utiliser le site à l’aide du portail de gestion Azure ou **l’Explorateur de serveurs** dans Visual Studio pour arrêter le site.


![](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/Stop-Service.png)

## <a name="advanced-migrations-scenarios"></a>Scénarios de migration avancée

Si vous déployez une base de données en exécutant des migrations automatiquement comme indiqué dans ce didacticiel, et que vous déployez sur un site web qui s’exécute sur plusieurs serveurs, vous pouvez obtenir plusieurs serveurs que vous essayez d’exécuter les migrations en même temps. Les migrations sont atomiques, afin que si deux serveurs essaient d’exécuter la même migration, une réussit et que l’autre échoue (en supposant que les opérations ne peuvent pas être effectuées deux fois). Dans ce scénario si vous souhaitez éviter ces problèmes, vous pouvez appeler manuellement les migrations et définir votre propre code afin qu’il ne se produit qu’une seule fois. Pour plus d’informations, consultez [en cours d’exécution et d’écriture de scripts pour les Migrations à partir de Code](http://romiller.com/2012/02/09/running-scripting-migrations-from-code/) sur le blog de Rowan Miller et [Migrate.exe](https://msdn.microsoft.com/data/jj618307) (pour l’exécution de migrations à partir de la ligne de commande) sur MSDN.

Pour plus d’informations sur les autres scénarios de migration, consultez [Migrations capture série](https://blogs.msdn.com/b/adonet/archive/2014/03/12/migrations-screencast-series.aspx).

## <a name="code-first-initializers"></a>Initialiseurs de premier code

Dans la section déploiement, vous avez vu le [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx) initialiseur utilisé. Code tout d’abord fournit également des autres initialiseurs, y compris [CreateDatabaseIfNotExists](https://msdn.microsoft.com/library/gg679221(v=vs.103).aspx) (la valeur par défaut), [DropCreateDatabaseIfModelChanges](https://msdn.microsoft.com/library/gg679604(v=VS.103).aspx) (que vous avez utilisé précédemment) et [ DropCreateDatabaseAlways](https://msdn.microsoft.com/library/gg679506(v=VS.103).aspx). Le `DropCreateAlways` initialiseur peut être utile pour configurer des conditions pour les tests unitaires. Vous pouvez également écrire votre propre initialiseurs, et vous pouvez appeler explicitement un initialiseur si vous ne souhaitez pas attendre que l’application lit ou écrit dans la base de données. Au moment de que ce didacticiel est écrit en novembre 2013, vous ne pouvez utiliser les initialiseurs de créer et DropCreate avant d’activer les migrations. L’équipe de Entity Framework travaille sur la création de ces initialiseurs utilisables avec les migrations ainsi.

Pour plus d’informations sur les initialiseurs, consultez [compréhension des initialiseurs de base de données dans Entity Framework Code First](http://www.codeguru.com/csharp/article.php/c19999/Understanding-Database-Initializers-in-Entity-Framework-Code-First.htm) et chapitre 6 du livre [programmation Entity Framework : Code First](http://shop.oreilly.com/product/0636920022220.do) par Julie Lerman et Rowan Miller.

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez vu comment activer les migrations et déployer l’application. Dans le didacticiel suivant vous commencerez examinant rubriques plus avancées en développant le modèle de données.

Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer. Vous pouvez également demander de nouvelles rubriques à [afficher Me comment avec le Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

Vous trouverez des liens vers d’autres ressources Entity Framework dans [ASP.NET Data Access - ressources recommandées](xref:whitepapers/aspnet-data-access-content-map).

>[!div class="step-by-step"]
[Précédent](xref:mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application)
[Suivant](xref:mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-a-more-complex-data-model-for-an-asp-net-mvc-application)

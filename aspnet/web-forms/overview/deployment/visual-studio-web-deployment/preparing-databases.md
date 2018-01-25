---
uid: web-forms/overview/deployment/visual-studio-web-deployment/preparing-databases
title: "Déploiement de Web ASP.NET à l’aide de Visual Studio : préparation pour le déploiement de la base de données | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, en utilisant des éléments..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/15/2013
ms.topic: article
ms.assetid: ae4def81-fa37-4883-a13e-d9896cbf6c36
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/preparing-databases
msc.type: authoredcontent
ms.openlocfilehash: caa79725ede320c4bd3e87ac246966c57175eb8e
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-web-deployment-using-visual-studio-preparing-for-database-deployment"></a>Déploiement de Web ASP.NET à l’aide de Visual Studio : préparation pour le déploiement de la base de données
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, à l’aide de Visual Studio 2012 ou Visual Studio 2010. Pour plus d’informations sur la série, consultez [le premier didacticiel de la série](introduction.md).


## <a name="overview"></a>Vue d'ensemble

Ce didacticiel montre comment obtenir le projet est prêt pour le déploiement de la base de données. La structure de base de données et d’autres (et non la totalité) des données dans les deux de l’application bases de données doivent être déployés pour le test, intermédiaire et les environnements de production.

En règle générale, lorsque vous développez une application, vous devez entrer des données de test dans une base de données que vous ne souhaitez pas déployer sur un site en direct. Toutefois, vous pouvez également disposer des données de production que vous ne souhaitez pas déployer. Dans ce didacticiel vous allez configurer le projet de Contoso University et préparer des scripts SQL afin que les données correctes sont inclus lorsque vous déployez.

Rappel : Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez le didacticiel, veillez à consulter le [page Résolution des problèmes](troubleshooting.md).

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

L’exemple d’application utilise SQL Server Express LocalDB. SQL Server Express est l’édition gratuite de SQL Server. Elle est généralement utilisée lors du développement, car il est basé sur le même moteur de base de données que les versions complètes de SQL Server. Vous pouvez tester avec SQL Server Express et la garantie que l’application se comportera en production, avec quelques exceptions pour les fonctionnalités qui varient entre les éditions de SQL Server.

LocalDB est un mode spécial d’exécution de SQL Server Express qui vous permet de travailler avec les bases de données en tant que *.mdf* fichiers. En règle générale, les fichiers de base de données de base de données locale sont conservés dans le *application\_données* dossier d’un projet web. La fonctionnalité d’instance utilisateur dans SQL Server Express vous permet également de travailler avec *.mdf* fichiers, mais la fonctionnalité d’instance utilisateur est déconseillé ; par conséquent, il est recommandé LocalDB pour travailler avec *.mdf* fichiers.

En général, SQL Server Express n’est pas utilisé pour les applications web de production. Base de données locale en particulier n'est pas recommandée pour la production avec une application web, car il n’est pas conçu pour fonctionner avec les services IIS.

Dans Visual Studio 2012, la base de données locale est installé par défaut avec Visual Studio. Dans Visual Studio 2010 et versions antérieures, SQL Server Express (sans LocalDB) est installé par défaut avec Visual Studio. Autrement dit pourquoi vous l’installé en tant que les conditions préalables dans [le premier didacticiel de cette série](introduction.md).

Pour plus d’informations sur les éditions de SQL Server, y compris LocalDB, consultez les ressources suivantes [des bases de données SQL Server](../../../../whitepapers/aspnet-data-access-content-map.md#sqlserver).

## <a name="entity-framework-and-universal-providers"></a>Entity Framework et les fournisseurs universels

Pour l’accès de la base de données, l’application Contoso University requiert les logiciels suivants doivent être déployés avec l’application, car il n’est pas inclus dans le .NET Framework :

- [ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx) (permet le système d’appartenance ASP.NET utiliser la base de données SQL Azure)
- [Entity Framework](https://msdn.microsoft.com/library/gg696172.aspx)

Étant donné que ce logiciel est inclus dans les packages NuGet, le projet est déjà configuré afin que les assemblys nécessaires sont déployés avec le projet. (Les liens pointent vers les versions actuelles de ces packages, ce qui peuvent être plus récentes que celle qui est installée dans le projet de démarrage que vous avez téléchargé pour ce didacticiel.)

Si vous déployez sur un fournisseur d’hébergement tiers au lieu d’Azure, assurez-vous que vous utilisez Entity Framework 5.0 ou version ultérieure. Les versions antérieures de Migrations Code First nécessitent la confiance totale, et la plupart des fournisseurs d’hébergement exécutera votre application de confiance moyenne. Pour plus d’informations sur la confiance moyenne, consultez le [déploiement vers IIS comme environnement de Test](deploying-to-iis.md) didacticiel.

## <a name="configure-code-first-migrations-for-application-database-deployment"></a>Configurer les Migrations Code First pour le déploiement de base de données d’application

La base de données application Contoso University est géré par le Code First, et vous devez le déployer à l’aide de Migrations Code First. Pour une vue d’ensemble du déploiement de base de données à l’aide de Migrations Code First, consultez [le premier didacticiel de cette série](introduction.md).

Lorsque vous déployez une base de données de l’application, en général, vous ne simplement déployez votre base de données de développement avec toutes les données qu’il contient en production, car les données qu’il sont probablement il uniquement pour des tests. Par exemple, les noms de l’étudiant dans une base de données de test sont fictives. En revanche, vous souvent impossible de déployer uniquement la structure de base de données sans données qu’il contient tout. Certaines des données dans votre base de données de test peuvent être des données réelles et doivent déjà être lorsque les utilisateurs commencent à utiliser l’application. Par exemple, votre base de données peut avoir une table qui contient les valeurs de niveau valide ou les noms de service réel.

Pour simuler ce scénario courant, vous allez configurer un Migrations Code First `Seed` méthode qui insère, dans la base de données, uniquement les données que vous souhaitez être en production. Cela `Seed` méthode ne doit pas insérer des données de test, car il s’exécutera en production après le premier Code crée la base de données de production.

Dans les versions antérieures de Code First avant la migration, il était courant pour `Seed` méthodes pour insérer des données de test en outre, étant donné qu’avec chaque modification du modèle pendant le développement la base de données a dû être complètement supprimé et recréé à partir de zéro. Avec Migrations Code First, test, les données sont conservées après des modifications de la base de données, par conséquent, y compris les données de test dans le `Seed` méthode n’est pas nécessaire. Le projet que vous avez téléchargé utilise la méthode de, y compris toutes les données dans le `Seed` méthode d’une classe d’initialiseur. Dans ce didacticiel, vous devez désactiver cette classe initialiseur et `enable Migrations. Then you'll update the `valeur initiale « la méthode de la configuration de Migrations classe afin qu’il insère uniquement les données que vous souhaitez insérer dans la production.

Le diagramme suivant illustre le schéma de la base de données de l’application :

[![School_database_diagram](preparing-databases/_static/image2.png)](preparing-databases/_static/image1.png)

Pour ces didacticiels, vous devez supposer que le `Student` et `Enrollment` tables doivent être vides lorsque le site a été déployé. Les autres tables contiennent des données qui doit être préchargée lors de l’application passe en direct.

### <a name="disable-the-initializer"></a>Désactiver l’initialiseur

Étant donné que vous utilisez Migrations Code First, vous n’avez pas à utiliser le `DropCreateDatabaseIfModelChanges` initialiseur Code First. Le code de ce dernier est dans le *SchoolInitializer.cs* fichier dans le projet ContosoUniversity.DAL. Un paramètre dans le `appSettings` élément de la *Web.config* fichier entraîne cet initialiseur exécuter chaque fois que l’application tente d’accéder à la base de données pour la première fois :

[!code-xml[Main](preparing-databases/samples/sample1.xml?highlight=3)]

Ouvrez l’application *Web.config* de fichiers et supprimez ou commentez la `add` élément qui spécifie la classe de l’initialiseur Code First. Le `appSettings` élément ressemble à ceci :

[!code-xml[Main](preparing-databases/samples/sample2.xml)]

> [!NOTE]
> Une autre façon de spécifier un initialiseur de classe est de le faire en appelant `Database.SetInitializer` dans les `Application_Start` méthode dans le *Global.asax* fichier. Si vous activez les Migrations dans un projet qui utilise cette méthode pour spécifier l’initialiseur, supprimez cette ligne de code.


> [!NOTE]
> Si vous utilisez Visual Studio 2013, ajouter les étapes suivantes entre les étapes 2 et 3 : (a) PMC dans Entrez « entityframework de package de mise à jour-version 6.1.1 » pour obtenir la version actuelle de EF. Puis le build (b) le projet pour obtenir la liste des erreurs de build et de les corriger. Supprimer à l’aide des instructions pour les espaces de noms qui n’existent plus, avec le bouton droit et cliquez sur résoudre pour ajouter à l’aide d’instructions où ils sont nécessaires et remplacez les occurrences de System.Data.EntityState System.Data.Entity.EntityState.


### <a name="enable-code-first-migrations"></a>Activer les Migrations Code First

1. Assurez-vous que le projet de ContosoUniversity (pas ContosoUniversity.DAL) est défini comme projet de démarrage. Dans **l’Explorateur de solutions**, cliquez sur le projet ContosoUniversity et sélectionnez **définir comme projet de démarrage**. Migrations Code First recherchera dans le projet de démarrage pour rechercher la chaîne de connexion de base de données.
2. À partir de la **outils** menu, cliquez sur **Gestionnaire de Package de bibliothèque** (ou **Gestionnaire de Package NuGet**), puis **Package Manager Console**.

    ![Selecting_Package_Manager_Console](preparing-databases/_static/image3.png)
3. En haut de la **Package Manager Console** fenêtre Sélectionnez ContosoUniversity.DAL comme projet par défaut puis at la `PM>` invite Entrez « enable-migrations ».

    ![commande Enable-migrations](preparing-databases/_static/image4.png)

    (Si vous obtenez une prononciation erreur le *enable-migrations* commande n’est pas reconnue, entrez la commande *EntityFramework de package de mise à jour-réinstaller* et essayez à nouveau.)

    Cette commande crée un *Migrations* dossier dans le projet ContosoUniversity.DAL et il place dans le dossier de deux fichiers : un *Configuration.cs* fichier que vous pouvez utiliser pour configurer des Migrations et un *InitialCreate.cs* fichier pour la première migration qui crée la base de données.

    ![Dossier migrations](preparing-databases/_static/image5.png)

    Vous avez sélectionné le projet de la couche DAL dans le **projet par défaut** liste déroulante de la **Package Manager Console** , car le `enable-migrations` commande doit être exécutée dans le projet qui contient le Code First classe du contexte. Lorsque cette classe est dans un projet de bibliothèque de classes, Migrations Code First recherche la chaîne de connexion de base de données dans le projet de démarrage pour la solution. Dans la solution ContosoUniversity, le projet web a été défini comme projet de démarrage. Si vous ne souhaitez pas désigner le projet contenant la chaîne de connexion en tant que projet de démarrage dans Visual Studio, vous pouvez spécifier le projet de démarrage dans la commande PowerShell. Pour afficher la syntaxe de commande, entrez la commande `get-help enable-migrations`.

    Le `enable-migrations` commande a créé automatiquement la première migration car la base de données existe déjà. Une alternative consiste à disposer les Migrations à créer la base de données. Pour ce faire, utilisez **l’Explorateur de serveurs** ou **l’Explorateur d’objets SQL Server** pour supprimer la base de données ContosoUniversity avant d’activer les Migrations. Après avoir activé les migrations, créer la première migration manuellement en entrant la commande « Ajouter-migration InitialCreate ». Vous pouvez ensuite créer la base de données en entrant la commande « mise à jour de base de données ».

### <a name="set-up-the-seed-method"></a>Définir la méthode de valeur initiale

Pour ce didacticiel, vous ajouterez des données fixes en ajoutant du code pour le `Seed` méthode les Migrations Code First `Configuration` classe. Code Migrations First appelle la `Seed` méthode après chaque migration.

Étant donné que la `Seed` méthode s’exécute après chaque migration, données déjà dans les tables après la première migration. Pour gérer cette situation, vous allez utiliser le `AddOrUpdate` pour mettre à jour les lignes qui ont déjà été insérés, ou les insérer si elles n’existent pas encore. Le `AddOrUpdate` (méthode) ne peut pas être le meilleur choix pour votre scénario. Pour plus d’informations, consultez [soyez prudent avec EF 4.3 AddOrUpdate méthode](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/) sur le blog de Julie Lerman.

1. Ouvrez le *Configuration.cs* de fichier et remplacez les commentaires dans le `Seed` méthode avec le code suivant :

    [!code-csharp[Main](preparing-databases/samples/sample3.cs)]
2. Les références à `List` ont sous les traits rouges ondulés, car vous n’avez pas un `using` instruction encore pour son espace de noms. Cliquez sur une des instances de `List` et cliquez sur **résoudre**, puis cliquez sur **using System.Collections.Generic**.

    ![Résoudre à l’aide de l’instruction](preparing-databases/_static/image6.png)

    Cette option ajoute le code suivant à la `using` instructions située en haut du fichier.

    [!code-csharp[Main](preparing-databases/samples/sample4.cs)]
3. Appuyez sur CTRL-MAJ + B pour générer le projet.

Le projet est maintenant prêt à déployer le *ContosoUniversity* base de données. Après avoir déployé l’application, la première fois que vous l’exécuter et accédez à une page qui accède à la base de données Code First crée la base de données et exécuter cette `Seed` (méthode).

> [!NOTE]
> Ajout de code pour le `Seed` méthode est une des nombreuses méthodes que vous pouvez insérer des données fixes dans la base de données. Une alternative consiste à ajouter du code pour le `Up` et `Down` méthodes de chaque classe de la migration. Le `Up` et `Down` méthodes contient du code qui implémente les modifications de la base de données. Vous allez voir des exemples d’eux dans le [déploiement d’une mise à jour de la base de données](deploying-a-database-update.md) didacticiel.
> 
> Vous pouvez également écrire du code qui exécute des instructions SQL à l’aide de la `Sql` (méthode). Par exemple, si vous ajoutiez une colonne de Budget à la table Department et que vous voulez initialiser tous les budgets de service à 1 000,00 $ dans le cadre d’une migration, vous pouvez ajouter la ligne suivant montre du code pour le `Up` méthode pour que la migration :
> 
> `Sql("UPDATE Department SET Budget = 1000");`


## <a name="create-scripts-for-membership-database-deployment"></a>Créer des scripts de déploiement de base de données d’appartenance

L’application de l’université Contoso utilise l’authentification de formulaires et de système de l’appartenance ASP.NET pour authentifier et autoriser les utilisateurs. Le **mise à jour des crédits** page est accessible uniquement aux utilisateurs qui se trouvent dans le rôle d’administrateur.

Exécutez l’application et cliquez sur **cours**, puis cliquez sur **mise à jour des crédits**.

![Cliquez sur les crédits de mise à jour](preparing-databases/_static/image7.png)

Le **connecter** page s’affiche, car le **crédits de mise à jour** page nécessite des privilèges d’administrateur.

Entrez *admin* comme nom d’utilisateur et *devpwd* en tant que le mot de passe et cliquez sur **connecter**.

![Page de connexion](preparing-databases/_static/image8.png)

Le **mise à jour des crédits** page s’affiche.

![Page de crédits de mise à jour](preparing-databases/_static/image9.png)

Les informations utilisateur et le rôle sont dans le *aspnet-ContosoUniversity* base de données spécifiée par la **DefaultConnection** chaîne de connexion dans le *Web.config* fichier.

Cette base de données n’est pas géré par Entity Framework Code First, vous ne pouvez pas utiliser les Migrations à déployer. Vous allez utiliser le fournisseur dbDacFx pour déployer le schéma de base de données, et vous allez configurer le profil de publication pour exécuter un script qui insèrera initiale des données dans les tables de base de données.

> [!NOTE]
> Un nouveau système d’appartenance ASP.NET (maintenant appelé ASP.NET Identity) a été introduit avec Visual Studio 2013. Le nouveau système vous permet de conserver l’application et des tables d’appartenances dans la même base de données, et vous pouvez utiliser les Migrations Code First pour déployer les deux. L’exemple d’application utilise le système d’appartenance ASP.NET antérieur, ce qui ne peut pas être déployé à l’aide de Migrations Code First. Les procédures de déploiement de cette base de données d’appartenance s’appliquent également à tout autre scénario dans lequel votre application a besoin déployer une base de données SQL Server qui n’est pas créé par Entity Framework Code First.


Ici, vous préfèrent généralement les mêmes données en production que vous avez dans le développement. Lorsque vous déployez un site pour la première fois, il est courant pour exclure tout ou partie des comptes d’utilisateur que vous créez pour le test. Par conséquent, le projet téléchargé a deux bases de données d’appartenance : *aspnet-ContosoUniversity.mdf* avec les utilisateurs de développement et *aspnet-ContosoUniversity-Prod.mdf* avec des utilisateurs de production. Pour ce didacticiel, les noms d’utilisateur sont les mêmes dans les deux bases de données : *admin* et *nonadmin*. Les deux utilisateurs ont le mot de passe *devpwd* dans la base de données de développement et *prodpwd* dans la base de données de production.

Vous allez déployer les utilisateurs de développement à l’environnement de test et les utilisateurs de production à intermédiaire et de production. Vous allez créer deux scripts SQL dans ce didacticiel, un pour le développement et un environnement de production pour ce faire, et dans les didacticiels suivants vous configurerez le processus de publication pour les exécuter.

> [!NOTE]
> La base de données d’appartenance stocke un hachage des mots de passe de compte. Pour déployer des comptes à partir d’un ordinateur à un autre, il se peut que vous devez vous assurer que les routines de hachage ne génèrent pas hachages différents sur le serveur de destination et sur l’ordinateur source. Ils génèrent les hachages mêmes lorsque vous utilisez les fournisseurs universels ASP.NET, tant que vous ne modifiez pas l’algorithme par défaut. L’algorithme par défaut est HMACSHA256 et qu’il est spécifié dans le **validation** attribut de la  **[machineKey](https://msdn.microsoft.com/library/system.web.configuration.machinekeysection.aspx)**  élément dans le fichier Web.config.


Vous pouvez créer des scripts de déploiement de données manuellement, à l’aide de SQL Server Management Studio (SSMS), ou à l’aide d’un outil tiers. Cette suite de ce didacticiel, vous allez apprendre à le faire dans SSMS, mais si vous ne souhaitez pas installer et utiliser SSMS vous pouvez obtenir les scripts à partir de la version complète du projet et passez à la section où vous les stockez dans le dossier de solution.

Pour installer SSMS, installez-le à partir de [centre de téléchargement : Microsoft SQL Server 2012 Express](https://www.microsoft.com/download/details.aspx?id=29062) en cliquant sur [ENU\x64\SQLManagementStudio\_x64\_ENU.exe](https://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SQLManagementStudio_x64_ENU.exe) ou [ ENU\x86\SQLManagementStudio\_x86\_ENU.exe](https://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x86/SQLManagementStudio_x86_ENU.exe). Si vous choisissez une autre pour votre système, il n’est pas à installer, et vous pouvez essayez-en un autre.

(Notez qu’il s’agit d’un téléchargement de 600 mégaoctets. Il peut prendre beaucoup de temps à installer et nécessite un redémarrage de votre ordinateur.)

Dans la première page du centre d’Installation de SQL Server, cliquez sur **nouvelle installation SQL Server autonome ou ajouter des fonctionnalités à une installation existante**, suivez les instructions, en acceptant les choix par défaut.

### <a name="create-the-development-database-script"></a>Créez le script de base de données de développement

1. Exécutez SSMS.
2. Dans le **se connecter au serveur** boîte de dialogue, entrez *(localdb) \v11.0* comme le **nom du serveur**, laissez **authentification** lavaleur**L’authentification Windows**, puis cliquez sur **connexion**.

    ![SSMS se connecter au serveur](preparing-databases/_static/image10.png)
3. Dans le **l’Explorateur d’objets** fenêtre, développez **bases de données**, avec le bouton droit **aspnet-ContosoUniversity**, cliquez sur **tâches**, puis cliquez sur **Générer des Scripts**.

    ![Générer des Scripts SSMS](preparing-databases/_static/image11.png)
4. Dans le **générer et publier des Scripts** boîte de dialogue, cliquez sur **définir les Options de script**.

    Vous pouvez ignorer le **choisir objets** pas à pas, car la valeur par défaut est **Script de base de données entière et tous les objets de base de données** et c’est ce que vous voulez.
5. Cliquez sur **Avancé**.

    ![Options de script SSMS](preparing-databases/_static/image12.png)
6. Dans le **Options de script avancées** boîte de dialogue, faites défiler jusqu'à **Types de données au script**, puis cliquez sur le **uniquement les données** option dans la liste déroulante.
7. Modification **Script USE DATABASE** à **False**. Les instructions d’utilisation ne sont pas valides pour la base de données SQL Azure et ne sont pas nécessaires pour le déploiement de SQL Server Express dans l’environnement de test.

    ![SSMS Script uniquement les données, aucune instruction USE](preparing-databases/_static/image13.png)
8. Cliquez sur **OK**.
9. Dans le **générer et publier des Scripts** boîte de dialogue, la **nom de fichier** zone spécifie où le script doit être créé. Modifier le chemin d’accès à votre dossier de solution (le dossier contenant votre fichier ContosoUniversity.sln) et le nom de fichier à *aspnet-données-dev.sql*.
10. Cliquez sur **suivant** pour accéder à la **Résumé** onglet, puis cliquez sur **suivant** à nouveau pour créer le script.

    ![Script SSMS créé](preparing-databases/_static/image14.png)
11. Cliquez sur **Terminer**.

### <a name="create-the-production-database-script"></a>Créez le script de base de données de production

Étant donné que vous n’avez pas exécuté le projet avec la base de données de production, il n’est pas encore attaché à l’instance de base de données locale. Par conséquent, vous devez attacher la base de données tout d’abord.

1. Dans SSMS **l’Explorateur d’objets**, avec le bouton droit **bases de données** et cliquez sur **attacher**.

    ![Attachement de SSMS](preparing-databases/_static/image15.png)
- Dans le **attacher les bases de données** boîte de dialogue, cliquez sur **ajouter** , puis accédez à la *aspnet-ContosoUniversity-Prod.mdf* de fichiers dans le *application\_ Données* dossier.

    ![SSMS ajouter le fichier .mdf à attacher](preparing-databases/_static/image16.png)
- Cliquez sur **OK**.
- Suivez la même procédure que vous avez utilisé précédemment pour créer un script pour le fichier de production. Nommez le fichier de script *aspnet-données-prod.sql*.

## <a name="summary"></a>Récapitulatif

Les deux bases de données sont désormais prêtes à être déployée et vous avez deux scripts de déploiement de données dans votre dossier de solution.

![Scripts de déploiement de données](preparing-databases/_static/image17.png)

Dans ce didacticiel, vous configurez les paramètres de projet qui affectent le déploiement, et vous configurez automatique *Web.config* fichier transformations pour les paramètres qui doivent être différents dans l’application déployée.

## <a name="more-information"></a>Informations complémentaires

Pour plus d’informations sur NuGet, consultez [gérer les bibliothèques de projets avec NuGet](https://msdn.microsoft.com/magazine/hh547106.aspx) et [Documentation de NuGet](http://docs.nuget.org/docs/start-here/overview). Si vous ne souhaitez pas utiliser NuGet, vous devez apprendre à analyser un package NuGet pour déterminer ce qu’il fait lorsqu’il est installé. (Par exemple, il peut configurer *Web.config* transformations, configurer des scripts PowerShell à exécuter au moment de la génération, etc..) Pour en savoir plus sur le fonctionne de NuGet, consultez [créer et publier un Package](http://docs.nuget.org/docs/creating-packages/creating-and-publishing-a-package) et [fichier de Configuration et des Transformations de Code Source](http://docs.nuget.org/docs/creating-packages/configuration-file-and-source-code-transformations).

>[!div class="step-by-step"]
[Précédent](introduction.md)
[Suivant](web-config-transformations.md)

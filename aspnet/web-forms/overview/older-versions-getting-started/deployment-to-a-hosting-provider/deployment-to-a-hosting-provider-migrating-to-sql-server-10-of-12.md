---
uid: web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12
title: "Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : migration vers SQL Server - 10 12 | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Stu..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/17/2011
ms.topic: article
ms.assetid: a89d6f32-b71b-4036-8ff7-5f8ac2a6eca8
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12
msc.type: authoredcontent
ms.openlocfilehash: b97834e3e287645151bf927996fde63d93ae8356
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="deploying-an-aspnet-web-application-with-sql-server-compact-using-visual-studio-or-visual-web-developer-migrating-to-sql-server---10-of-12"></a>Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : migration vers SQL Server - 10 12
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://code.msdn.microsoft.com/Deploying-an-ASPNET-Web-4e31366b)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Studio 2012 RC ou Visual Studio Express 2012 RC pour le Web. Vous pouvez également utiliser Visual Studio 2010 si vous installez la mise à jour de publication Web. Pour une présentation de la série, consultez [le premier didacticiel de la série](deployment-to-a-hosting-provider-introduction-1-of-12.md).
> 
> Pour obtenir un didacticiel qui montre les fonctionnalités de déploiement introduites après la version RC de Visual Studio 2012, montre comment déployer des éditions de SQL Server autre que SQL Server Compact et montre comment déployer dans Azure App Service Web Apps, consultez [déploiement de Web ASP.NET à l’aide de Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md).


## <a name="overview"></a>Vue d'ensemble

Ce didacticiel vous montre comment effectuer une migration à partir de SQL Server Compact à SQL Server. Vous pouvez souhaiter faire l’une des raisons sont pour tirer parti des fonctionnalités de SQL Server SQL Server Compact ne prend pas en charge, tels que des procédures stockées, déclencheurs, les vues ou la réplication. Pour plus d’informations sur les différences entre SQL Server Compact et SQL Server, consultez le [déploiement SQL Server Compact](deployment-to-a-hosting-provider-deploying-sql-server-compact-databases-2-of-12.md) didacticiel.

### <a name="sql-server-express-versus-full-sql-server-for-development"></a>SQL Server Express et complète de SQL Server pour le développement

Une fois que vous avez décidé de mettre à niveau vers SQL Server, vous pouvez souhaiter utiliser SQL Server ou SQL Server Express dans vos environnements de développement et de test. Outre les différences de prise en charge de l’outil et fonctionnalités du moteur de base de données, il existe de différences dans les implémentations de fournisseur SQL Server Compact et d’autres versions de SQL Server. Ces différences peuvent occasionner le même code générer des résultats différents. Par conséquent, si vous décidez de conserver SQL Server Compact que votre base de données de développement, vous devez tester minutieusement votre site dans SQL Server ou SQL Server Express dans un environnement de test avant chaque déploiement en production.

Contrairement à SQL Server Compact, SQL Server Express est essentiellement le même moteur de base de données et utilise le même fournisseur .NET en tant que serveur SQL Server complet. Lorsque vous testez avec SQL Server Express, vous pouvez être certain d’obtenir les mêmes résultats que vous allez avec SQL Server. Vous pouvez utiliser la plupart des outils de base de données avec SQL Server Express que vous pouvez utiliser avec SQL Server (une exception notable étant [SQL Server Profiler](https://msdn.microsoft.com/library/ms181091.aspx)), et il prend en charge d’autres fonctionnalités de SQL Server, comme les procédures stockées, vues, déclencheurs, et la réplication. (Vous devez en général utiliser complète de SQL Server dans un site Web de production, cependant. SQL Server Express peut s’exécuter dans un environnement d’hébergement partagé, mais il n’a pas été conçu pour que, et de nombreux fournisseurs d’hébergements ne le prennent pas en charge.)

Si vous utilisez Visual Studio 2012, vous choisissez généralement SQL Server Express LocalDB pour votre environnement de développement, car c’est ce qui est installé par défaut avec Visual Studio. Toutefois, base de données locale ne fonctionne pas dans IIS, donc pour votre environnement de test, vous devez utiliser SQL Server ou SQL Server Express.

### <a name="combining-databases-versus-keeping-them-separate"></a>Combinaison de bases de données et les conserver distinct

L’application Contoso University a deux bases de données SQL Server Compact : la base de données d’appartenance (*aspnet.sdf*) et la base de données d’application (*School.sdf*). Lorsque vous effectuez la migration, vous pouvez migrer ces bases de données à deux bases de données distinctes ou à une base de données. Vous souhaiterez peut-être les combiner afin de faciliter les jointures de base de données entre la base de données de votre application et votre base de données d’appartenance. Votre plan d’hébergement peut également fournir une raison de les combiner. Par exemple, le fournisseur d’hébergement peut-être facturer pour plusieurs bases de données ou ne permettre pas encore de plusieurs bases de données. C’est le cas avec Cytanium Lite qui héberge le compte utilisé pour ce didacticiel, ce qui permet uniquement une base de données SQL Server unique.

Dans ce didacticiel, vous allez migrer vos deux bases de données de cette manière :

- Migrer vers deux bases de données de base de données locale dans l’environnement de développement.
- Migrer vers deux bases de données SQL Server Express dans l’environnement de test.
- Migrer vers une base de SQL Server complète combinée dans l’environnement de production.

Rappel : Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez le didacticiel, veillez à consulter le [page Résolution des problèmes](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12.md).

## <a name="installing-sql-server-express"></a>L’installation de SQL Server Express

SQL Server Express est installé automatiquement par défaut avec Visual Studio 2010, mais par défaut avec Visual Studio 2012 n’est pas installé. Pour installer SQL Server 2012 Express, cliquez sur le lien suivant

- [SQL Server Express 2012](https://www.microsoft.com/download/details.aspx?id=29062)

Choisissez *fra/x64/SQLEXPR\_x64\_ENU.exe* ou *fra/x86/SQLEXPR\_x86\_ENU.exe*et dans l’Assistant installation, acceptez la valeur par défaut Paramètres. Pour plus d’informations sur les options d’installation, consultez [installer SQL Server 2012 à partir de l’Assistant Installation (programme d’installation)](https://msdn.microsoft.com/library/ms143219.aspx).

## <a name="creating-sql-server-express-databases-for-the-test-environment"></a>Création de bases de données SQL Server Express pour l’environnement de Test

L’étape suivante consiste à créer l’appartenance d’ASP.NET et les bases de données School.

À partir de la **vue** menu, sélectionnez **l’Explorateur de serveurs** (**Explorateur de base de données** dans Visual Web Developer), cliquez sur **des connexions de données**et sélectionnez **créer une nouvelle base de données SQL Server**.

![Selecting_Create_New_SQL_Server_Database](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image1.png)

Dans le **créer une nouvelle base de données SQL Server** boîte de dialogue, entrez «. \SQLExpress » dans le **nom du serveur** zone et « aspnet-Test » dans le **nouveau nom de base de données** zone, puis cliquez sur **OK**.

![Create_New_SQL_Server_Database_aspnet](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image2.png)

Suivez la même procédure pour créer une nouvelle base de données SQL Server Express School nommé « School-Test ».

(Vous ajoutez « Test » dans ces noms de base de données, car les versions ultérieures, vous allez créer une instance supplémentaire de chaque base de données pour l’environnement de développement, et vous devez être en mesure de distinguer les deux ensembles de bases de données.)

**L’Explorateur de serveurs** affiche maintenant les deux bases de données.

![New_databases_in_Server_Explorer](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image3.png)

## <a name="creating-a-grant-script-for-the-new-databases"></a>Création d’un Script d’octroi de nouvelles bases de données

Lorsque l’application s’exécute dans IIS sur votre ordinateur de développement, l’application accède à la base de données à l’aide des informations d’identification du pool d’applications par défaut. Toutefois, par défaut, l’identité du pool d’applications n’a pas d’autorisation pour ouvrir les bases de données. Par conséquent, vous devez exécuter un script pour accorder cette autorisation. Dans cette section, vous créez le script que vous allez exécuter plus tard pour vous assurer que l’application peut ouvrir les bases de données lorsqu’elle s’exécute dans IIS.

Dans la solution *SolutionFiles* dossier que vous avez créé dans le [déploiement dans l’environnement de Production](deployment-to-a-hosting-provider-deploying-to-the-production-environment-7-of-12.md) (didacticiel), créez un nouveau fichier SQL nommé *Grant.sql*. Copiez les commandes SQL suivantes dans le fichier, puis enregistrez et fermez le fichier :

[!code-sql[Main](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/samples/sample1.sql)]

> [!NOTE]
> Ce script est conçu pour fonctionner avec SQL Server 2008 et les paramètres IIS dans Windows 7, car ils sont spécifiés dans ce didacticiel. Si vous utilisez une version différente de SQL Server ou de Windows, ou si vous configurez IIS sur votre ordinateur différemment, les modifications apportées à ce script peuvent être nécessaire. Pour plus d’informations sur les scripts SQL Server, consultez [la documentation en ligne de SQL Server](https://go.microsoft.com/fwlink/?LinkId=132511).


> [!NOTE] 
> 
> **Note de sécurité** ce script donne db\_les autorisations de propriétaire pour l’utilisateur qui accède à la base de données au moment de l’exécution, ce qui est que vous allez utiliser dans l’environnement de production. Dans certains scénarios, vous souhaiterez spécifier un utilisateur qui contient un schéma de base de données complète mettre à jour des autorisations uniquement pour le déploiement, et spécifier pour le moment de l’exécution d’un autre utilisateur qui dispose des autorisations uniquement pour lire et écrire des données. Pour plus d’informations, consultez **révision des modifications Web.config automatique pour les Migrations Code First** dans [déploiement vers IIS comme environnement de Test](deployment-to-a-hosting-provider-deploying-to-iis-as-a-test-environment-5-of-12.md).


## <a name="configuring-database-deployment-for-the-test-environment"></a>Configuration du déploiement de base de données pour l’environnement de Test

Ensuite, vous devez configurer Visual Studio afin qu’il effectuera les tâches suivantes pour chaque base de données :

- Générer un script SQL qui crée la structure de la base de données source (tables, colonnes, contraintes, etc.) dans la base de données de destination.
- Générer un script SQL qui insère des données de la base de données source dans les tables dans la base de données de destination.
- Exécuter les scripts générés et le script d’octroi que vous avez créé dans la base de données de destination.

Ouvrez le **propriétés du projet** et sélectionnez le **Package/Publication SQL** onglet.

Assurez-vous que **Active (Release)** ou **version** est sélectionné dans le **Configuration** liste déroulante.

Cliquez sur **activer cette Page**.

![Package_Publish_SQL_tab_Enable_This_page](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image4.png)

Le **Package/Publication SQL** onglet est normalement désactivée, car elle spécifie une méthode de déploiement hérité. La plupart des scénarios, vous devez configurer le déploiement de la base de données dans le **publier le site Web** Assistant. Migration à partir de SQL Server Compact dans SQL Server ou SQL Server Express est un cas spécial pour lequel cette méthode est un bon choix.

Cliquez sur **importation à partir de Web.config**.

![Selecting_Import_from_Web.config](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image5.png)

Visual Studio recherche des chaînes de connexion dans le *Web.config* fichier, recherche un pour la base de données d’appartenance et un pour la base de données School et ajoute une ligne correspondant à chaque chaîne de connexion dans le **les entrées de base de données**  table. Il recherche les chaînes de connexion sont pour les bases de données SQL Server Compact, et l’étape suivante sera pour configurer comment et où déployer ces bases de données.

Vous entrez des paramètres de déploiement de base de données dans le **détails de l’entrée de base de données** section ci-dessous le **les entrées de base de données** table. Les paramètres affichés dans le **détails de l’entrée de base de données** section se rapportent à la ligne dans le **les entrées de base de données** table est sélectionnée, comme indiqué dans l’illustration suivante.

![Database_Entry_Details_section_of_Package_Publish_SQL_tab](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image6.png)

### <a name="configuring-deployment-settings-for-the-membership-database"></a>Configuration des paramètres de déploiement pour la base de données d’appartenance

Sélectionnez le **DefaultConnection-déploiement** de la ligne dans le **les entrées de base de données** table afin de configurer les paramètres qui s’appliquent à la base de données d’appartenance.

Dans **chaîne de connexion pour la base de données de destination**, entrez une chaîne de connexion qui pointe vers la nouvelle base de l’appartenance de SQL Server Express. Vous pouvez obtenir la chaîne de connexion que vous avez besoin de **l’Explorateur de serveurs**. Dans **l’Explorateur de serveurs**, développez **des connexions de données** et sélectionnez le **aspnetTest** base de données, puis à partir de la **propriétés** copie de la fenêtre la **Chaîne de connexion** valeur.

![aspnet_connection_string_in_Server_Explorer](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image7.png)

La chaîne de connexion est reproduite ici :

[!code-console[Main](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/samples/sample2.cmd)]

Copiez et collez cette chaîne de connexion dans **chaîne de connexion pour la base de données de destination** dans les **Package/Publication SQL** onglet.

Assurez-vous que **par extraction de données et/ou un schéma à partir d’une base de données** est sélectionnée. C’est pourquoi les scripts SQL d’être automatiquement générée et s’exécuter dans la base de données de destination.

Le **chaîne de connexion pour la base de données source** valeur est extraite de la *Web.config* fichier et il pointe vers la base de données SQL Server Compact de développement. Il s’agit de la base de données source qui servira à générer les scripts qui seront exécutera plus loin dans la base de données de destination. Étant donné que vous souhaitez déployer la version de production de la base de données, remplacez « aspnet-Dev.sdf » par « aspnet-Prod.sdf ».

Modification **options de script de base de données** de **schéma uniquement** à **schéma et les données**, étant donné que vous souhaitez copier vos données (comptes d’utilisateurs et rôles), ainsi que la structure de base de données.

Pour configurer le déploiement pour exécuter les scripts de licence que vous avez créé précédemment, vous devez les ajouter à la **des Scripts de base de données** section. Cliquez sur **ajouter un Script**et dans le **ajouter des Scripts SQL** boîte de dialogue, accédez au dossier où vous avez stocké le script d’octroi (il s’agit du dossier qui contient votre fichier solution). Sélectionnez le fichier nommé *Grant.sql*, puis cliquez sur **ouvrir**.

[![Select_File_dialog_box_grant_script](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image9.png)](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image8.png)

Les paramètres pour le **DefaultConnection-déploiement** de la ligne dans **les entrées de base de données** maintenant ressembler à l’illustration suivante :

![Database_Entry_Details_for_DefaultConnection_Test](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image10.png)

### <a name="configuring-deployment-settings-for-the-school-database"></a>Configuration des paramètres de déploiement pour la base de données School

Ensuite, sélectionnez le **SchoolContext-déploiement** de la ligne dans le **les entrées de base de données** table afin de configurer les paramètres de déploiement pour la base de données School.

Vous pouvez utiliser la même méthode que vous avez utilisé précédemment pour obtenir la chaîne de connexion pour la nouvelle base de données SQL Server Express. Copiez cette chaîne de connexion dans **chaîne de connexion pour la base de données de destination** dans les **Package/Publication SQL** onglet.

[!code-console[Main](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/samples/sample3.cmd)]

Assurez-vous que **par extraction de données et/ou un schéma à partir d’une base de données** est sélectionnée.

Le **chaîne de connexion pour la base de données source** valeur est extraite de la *Web.config* fichier et il pointe vers la base de données SQL Server Compact de développement. Remplacez « School-Dev.sdf » « Scolaire Prod.sdf » pour déployer la version de production de la base de données. (Jamais, vous avez créé un fichier de l’école-Prod.sdf dans l’application\_dossier de données, donc vous devez copier ce fichier à partir de l’environnement de test dans l’application\_dossier de données dans le dossier du projet ContosoUniversity plus tard.)

Modification **options de script de base de données** à **schéma et les données**.

Vous souhaitez exécuter le script pour la lecture et l’autorisation pour cette base de données en écriture à l’identité de pool d’applications, ajoutez également le *Grant.sql* fichier de script comme vous l’avez fait pour la base de données d’appartenance.

Lorsque vous avez terminé, les paramètres le **SchoolContext-déploiement** de la ligne dans **les entrées de base de données** ressembler à l’illustration suivante :

![Database_Entry_Details_for_SchoolContext_Test](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image11.png)

Enregistrer les modifications apportées à la **Package/Publication SQL** onglet.

Copie le *School-Prod.sdf* à partir de la *c:\inetpub\wwwroot\ContosoUniversity\App\_données* dossier pour le *application\_données* dossier dans le projet ContosoUniversity.

### <a name="specifying-transacted-mode-for-the-grant-script"></a>En spécifiant le Mode de traitement pour le Script d’octroi

Le processus de déploiement génère les scripts qui déploient le schéma de base de données et les données. Par défaut, ces scripts s’exécutent dans une transaction. Toutefois, les scripts personnalisés (tels que les scripts grant) par défaut n’exécutent pas dans une transaction. Si le processus de déploiement combine les modes de transaction, vous pouvez obtenir une erreur de délai d’attente lorsque les scripts exécutés pendant le déploiement. Dans cette section, vous modifiez le fichier projet afin de configurer les scripts personnalisés à exécuter dans une transaction.

Dans **l’Explorateur de solutions**, avec le bouton droit le **ContosoUniversity** de projet et sélectionnez **décharger le projet**.

![Unload_Project_in_Solution_Explorer](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image12.png)

Cliquez à nouveau sur le projet, puis sélectionnez **ContosoUniversity.csproj de modifier**.

![Edit_Project_in_Solution_Explorer](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image13.png)

L’éditeur Visual Studio affiche le contenu XML du fichier projet. Notez qu’il existe plusieurs `PropertyGroup` éléments. (Dans l’image, le contenu de la `PropertyGroup` éléments ont été omis.)

![Fenêtre d’éditeur de fichier de projet](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image15.png)

Le, qui n’a aucun `Condition` d’attribut, pour les paramètres qui s’appliquent, indépendamment de configuration de build. Un `PropertyGroup` élément s’applique uniquement à la configuration de build Debug (Notez le `Condition` attribut), une s’applique uniquement à la configuration de build Release et 1 s’applique uniquement à la configuration de build de Test. Dans le `PropertyGroup` élément pour la configuration de build Release, vous verrez un `PublishDatabaseSettings` élément qui contient les paramètres que vous avez entrés dans le **Package/Publication SQL** onglet. Il existe un `Object` élément correspondant à chacun des scripts grant vous (Notez les deux instances spécifiées de « Grant.sql »). Par défaut, le `Transacted` attribut de la `Source` élément pour chaque script d’octroi est `False`.

![Transacted_false](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image16.png)

Modifiez la valeur de la `Transacted` attribut de la `Source` élément `True`.

![Transacted_true](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image17.png)

Enregistrez et fermez le fichier projet, cliquez sur le projet dans **l’Explorateur de solutions** et sélectionnez **recharger le projet**.

![Reload_project](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image18.png)

## <a name="setting-up-webconfig-transformations-for-the-connection-strings"></a>Configuration des Transformations Web.Config pour les chaînes de connexion

Chaînes de la connexion de SQL Express nouvelles bases de données que vous avez entré le **Package/Publication SQL** onglet sont utilisés par Web Deploy uniquement pour la mise à jour de la base de données de destination pendant le déploiement. Vous devez toujours configurer *Web.config* transformations afin que la connexion des chaînes dans déployé *Web.config* fichier point pour les nouvelles bases de données SQL Server Express. (Lorsque vous utilisez la **Package/Publication SQL** onglet, vous ne pouvez pas configurer les chaînes de connexion dans le profil de publication.)

Ouvrez *Web.Test.config* et remplacez le `connectionStrings` élément avec la `connectionStrings` élément dans l’exemple suivant. (Veillez à que copier l’élément connectionStrings, pas le code environnant qui est indiqué ici pour fournir un contexte uniquement.)

[!code-xml[Main](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/samples/sample4.xml?highlight=2-11)]

Ce code génère la `connectionString` et `providerName` les attributs de chaque `add` élément à remplacer dans la version déployée *Web.config* fichier. Ces chaînes de connexion ne sont pas identiques à ceux que vous avez entré dans le **Package/Publication SQL** onglet. Le paramètre « MultipleActiveResultSets = True » a été ajouté à ceux-ci, car elle est requise pour l’Entity Framework et les fournisseurs universels.

## <a name="installing-sql-server-compact"></a>L’installation de SQL Server Compact

Le package NuGet de SqlServerCompact fournit la base de données SQL Server Compact des assemblys de moteur pour l’application Contoso University. Mais maintenant n’est pas l’application mais Web Deploy qui doivent être en mesure de lire les bases de données SQL Server Compact, afin de créer des scripts à exécuter dans les bases de données SQL Server. Pour activer Web Deploy lire les bases de données SQL Server Compact, installez SQL Server Compact sur l’ordinateur de développement à l’aide du lien suivant : [Microsoft SQL Server Compact 4.0](https://www.microsoft.com/downloads/details.aspx?FamilyID=15F7C9B3-A150-4AD2-823E-E4E0DCF85DF6).

## <a name="deploying-to-the-test-environment"></a>Déploiement sur l’environnement de Test

Pour publier dans l’environnement de Test, vous devez créer un profil de publication qui est configuré pour utiliser le **Package/Publication SQL** onglet pour la base de données de publication à la place les paramètres de base de données de profil de publication.

Commencez par supprimer le profil de Test existant.

Dans **l’Explorateur de solutions**, cliquez sur le projet ContosoUniversity, puis cliquez sur **publier**.

Sélectionnez le **profil** onglet.

Cliquez sur **gérer les profils**.

Sélectionnez **Test**, cliquez sur **supprimer**, puis cliquez sur **fermer**.

Fermer le **publier le site Web** Assistant pour enregistrer cette modification.

Ensuite, créer un nouveau profil de Test et l’utiliser pour publier le projet.

Dans **l’Explorateur de solutions**, cliquez sur le projet ContosoUniversity, puis cliquez sur **publier**.

Sélectionnez le **profil** onglet.

Sélectionnez  **&lt;nouveau... &gt;**  à partir de la liste déroulante liste et entrez le nom du profil « Test ».

Dans le **URL du Service** , entrez *localhost*.

Dans le **Site/application** , entrez *Default Web Site/ContosoUniversity*.

Dans le **URL de Destination** , entrez `http://localhost/ContosoUniversity/`.

Cliquez sur **Suivant**.

Le **paramètres** onglet apparaît pour vous avertit que le **Package/Publication SQL** onglet a été configuré, et il vous donne la possibilité pour les remplacer en cliquant sur Activer la base de données nouvelles améliorations de la publication. Pour ce déploiement, vous souhaitez remplacer le **Package/Publication SQL** onglet Paramètres, cliquez simplement sur **suivant**.

![Publish_Web_wizard_Settings_tab_Migrate](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image19.png)

Un message sur le **aperçu** onglet indique que **aucune base de données n’est sélectionnés pour publication**, mais cela signifie uniquement que la publication de base de données n’est pas configurée dans le profil de publication.

Cliquez sur **Publier**.

![Publish_Web_wizard_Preview_tab_Migrate](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image20.png)

Visual Studio déploie l’application et ouvre le navigateur à la page d’accueil du site dans l’environnement de test. Exécutez la page instructeurs pour voir qu’il affiche les mêmes données que vous avez vu plus haut. Exécutez le **ajouter des étudiants** page, ajoutez un nouvel étudiant, puis afficher le nouvel étudiant dans la **étudiants** page. Cette opération vérifie que vous pouvez mettre à jour la base de données. Sélectionnez le **mise à jour des crédits** page (vous aurez besoin pour se connecter) pour vérifier que la base de données d’appartenance a été déployée et que vous y avez accès.

## <a name="creating-a-sql-server-database-for-the-production-environment"></a>Création d’une base de données SQL Server pour l’environnement de Production

Maintenant que vous avez déployé sur l’environnement de test, vous êtes prêt à configurer le déploiement en production. Comme vous le faisiez pour l’environnement de test, en créant une base de données à déployer pour commencer. Comme vous rappelez à partir de la vue d’ensemble, le plan d’hébergement Cytanium Lite autorise uniquement une seule base de données SQL Server, afin que vous allez définir qu’une seule base de données, pas les deux. Toutes les tables et les données à partir de l’appartenance et les bases de données School SQL Server Compact sont déployés dans une base de données SQL Server en production.

Accédez au panneau de Cytanium à [http://panel.cytanium.com](http://panel.cytanium.com). Maintenez la souris sur **bases de données** puis cliquez sur **SQL Server 2008**.

[![Selecting_Databases_in_Control_Panel](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image22.png)](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image21.png)

Dans le **SQL Server 2008** , cliquez sur **Create Database**.

[![Selecting_Create_Database](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image24.png)](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image23.png)

Nom de la base de données « School », cliquez sur **enregistrer**. (La page ajoute automatiquement le préfixe « contosou », le nom effectif est donc « contosouSchool ».)

[![Naming_the_database](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image26.png)](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image25.png)

Dans la même page, cliquez sur **Create User**. Sur les serveurs de Cytanium, plutôt qu’à l’aide de la sécurité intégrée de Windows et permettant à l’identité du pool d’applications d’ouvrir votre base de données, vous allez créer un utilisateur ayant les autorisations pour ouvrir votre base de données. Vous allez ajouter des informations d’identification de l’utilisateur pour les chaînes de connexion qui vont dans la production *Web.config* fichier. Dans cette étape, vous créez ces informations d’identification.

[![Creating_a_database_user](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image28.png)](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image27.png)

Renseignez les champs obligatoires de la **propriétés de l’utilisateur SQL** page :

- Entrez « ContosoUniversityUser » comme nom.
- Entrez un mot de passe.
- Sélectionnez **contosouSchool** en tant que la base de données par défaut.
- Sélectionnez le **contosouSchool** case à cocher.

[![SQL_User_Properties_page](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image30.png)](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image29.png)

## <a name="configuring-database-deployment-for-the-production-environment"></a>Configuration du déploiement de base de données pour l’environnement de Production

Maintenant, vous êtes prêt à configurer les paramètres de déploiement de base de données dans le **Package/Publication SQL** onglet, comme vous l’avez fait précédemment pour l’environnement de test.

Ouvrez le **propriétés du projet** fenêtre, sélectionnez le **Package/Publication SQL** onglet et vous assurer que **Active (Release)** ou **version** est sélectionné dans le **Configuration** liste déroulante.

Lorsque vous configurez les paramètres de déploiement pour chaque base de données, la principale différence entre ce que vous le feriez pour les environnements de production et de test est dans la façon dont vous configurez les chaînes de connexion. Pour l’environnement de test, vous avez entré les chaînes de connexion de base de données de destination différent, mais pour l’environnement de production, la chaîne de connexion de destination doit être le même pour les deux bases de données. Il s’agit, car vous déployez les deux bases de données à une base de données de production.

### <a name="configuring-deployment-settings-for-the-membership-database"></a>Configuration des paramètres de déploiement pour la base de données d’appartenance

Pour configurer les paramètres qui s’appliquent à la base de données d’appartenance, sélectionnez le **DefaultConnection-déploiement** de la ligne dans le **les entrées de base de données** table.

Dans **chaîne de connexion pour la base de données de destination**, entrez une chaîne de connexion qui pointe vers la nouvelle base de SQL Server de production que vous venez de créer. Vous pouvez obtenir la chaîne de connexion à partir de votre message électronique de bienvenue. La partie pertinente du message électronique contient la chaîne de connexion suivante :

[!code-console[Main](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/samples/sample5.cmd)]

Une fois que vous remplacez les trois variables, la chaîne de connexion que vous avez besoin ressemble à l’exemple suivant :

[!code-console[Main](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/samples/sample6.cmd)]

Copiez et collez cette chaîne de connexion dans **chaîne de connexion pour la base de données de destination** dans les **Package/Publication SQL** onglet.

Assurez-vous que **par extraction de données et/ou un schéma à partir d’une base de données** est toujours activée et que **options de script de base de données** est toujours **schéma et les données**.

Dans le **des Scripts de base de données** zone, désactivez la case à cocher en regard du script Grant.sql.

![Disable_Grant_script](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/_static/image31.png)

### <a name="configuring-deployment-settings-for-the-school-database"></a>Configuration des paramètres de déploiement pour la base de données School

Ensuite, sélectionnez le **SchoolContext-déploiement** de la ligne dans le **les entrées de base de données** table afin de configurer les paramètres de base de données School.

Copier la chaîne de connexion dans **chaîne de connexion pour la base de données de destination** que vous avez copiée dans ce champ pour la base de données d’appartenance.

Assurez-vous que **par extraction de données et/ou un schéma à partir d’une base de données** est toujours activée et que **options de script de base de données** est toujours **schéma et les données**.

Dans le **des Scripts de base de données** zone, désactivez la case à cocher en regard du script Grant.sql.

Enregistrer les modifications apportées à la **Package/Publication SQL** onglet.

## <a name="setting-up-webconfig-transforms-for-the-connection-strings-to-production-databases"></a>Configuration de Web.Config se transforme pour les chaînes de connexion aux bases de données de Production

Ensuite, vous allez configurer *Web.config* transformations afin que la connexion des chaînes dans déployé *Web.config* pour pointer vers la nouvelle base de données de production. La chaîne de connexion que vous avez entrés dans le **Package/Publication SQL** onglet pour Web Deploy à utiliser est le même que celui de l’application doit utiliser, à l’exception de l’ajout de la `MultipleResultSets` option.

Ouvrez *Web.Production.config* et remplacez le `connectionStrings` élément avec un `connectionStrings` élément qui ressemble à l’exemple suivant. (Copier uniquement le `connectionStrings` élément, pas les balises environnantes qui sont fournis pour montrer le contexte.)

[!code-xml[Main](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/samples/sample7.xml?highlight=2-11)]

Vous voyez parfois des conseils qui vous indique de chiffrer systématiquement les chaînes de connexion dans le *Web.config* fichier. Cela peut être approprié si vous déployez pour les serveurs sur le réseau de votre propre entreprise. Cependant, lorsque vous déployez dans un environnement d’hébergement partagé, vous êtes autorisé à approuver les pratiques de sécurité du fournisseur d’hébergement et il n’est pas nécessaire ou possible de chiffrer les chaînes de connexion.

## <a name="deploying-to-the-production-environment"></a>Déploiement sur l’environnement de Production

Vous êtes maintenant prêt à déployer en production. Lit les bases de données SQL Server Compact de votre projet Web Deploy *application\_données* dossier et de recréer tous les tables et les données dans la base de données SQL Server de production. Pour publier à l’aide de la **Package/Publication Web** paramètres de l’onglet, vous devez créer un nouveau profil de publication pour la production.

Commencez par supprimer le profil de Production existant comme précédemment le profil de Test.

Dans **l’Explorateur de solutions**, cliquez sur le projet ContosoUniversity, puis cliquez sur **publier**.

Sélectionnez le **profil** onglet.

Cliquez sur **gérer les profils**.

Sélectionnez **Production**, cliquez sur **supprimer**, puis cliquez sur **fermer**.

Fermer le **publier le site Web** Assistant pour enregistrer cette modification.

Ensuite, créez un nouveau profil de Production et utilisez-le pour publier le projet.

Dans **l’Explorateur de solutions**, cliquez sur le projet ContosoUniversity, puis cliquez sur **publier**.

Sélectionnez le **profil** onglet.

Cliquez sur **importation**, puis sélectionnez le fichier .publishsettings que vous avez téléchargé précédemment.

Sur le **connexion** , modifiez le **URL de Destination** à l’URL temporaire appropriée, qui dans cet exemple est http://contosouniversity.com.vserver01.cytanium.com.

Renommez le profil en Production. (Sélectionnez le **profil** onglet et cliquez sur **gérer les profils** à cet effet).

Fermer le **publier le site Web** Assistant pour enregistrer vos modifications.

Dans une application réelle dans laquelle la base de données a été mis à jour en production, vous procéderiez comme deux étapes supplémentaires maintenant avant de publier :

1. Télécharger *application\_offline.htm*, comme illustré dans le [déploiement dans l’environnement de Production](deployment-to-a-hosting-provider-deploying-to-the-production-environment-7-of-12.md) didacticiel.
2. Utilisez le **le Gestionnaire de fichiers** fonctionnalité du Panneau de Cytanium pour copier la *aspnet-Prod.sdf* et *School-Prod.sdf* fichiers à partir du site de production à le *Application\_données* dossier du projet ContosoUniversity. Cela garantit que les données que vous déployez la nouvelle base de données SQL Server incluent les dernières mises à jour effectuées par votre site Web de production.

Dans le **publication Web en un clic** barre d’outils, assurez-vous que le **Production** profil est sélectionné, puis cliquez sur **publier**.

Si vous avez téléchargé *application\_offline.htm* avant la publication, vous devez utiliser le **le Gestionnaire de fichiers** utilitaire dans le panneau de configuration Cytanium supprimer *application\_hors connexion.* htm avant de tester. Vous pouvez également en même temps supprimer la *.sdf* fichiers à partir de la *application\_données* dossier.

Vous pouvez maintenant ouvrir un navigateur et accédez à l’URL de votre site public pour tester l’application de la même façon que vous l’avez fait après leur déploiement sur l’environnement de test.

## <a name="switching-to-sql-server-express-localdb-in-development"></a>Basculement de SQL Server Express LocalDB dans le développement

Comme dans la vue d’ensemble, il est généralement préférable d’utiliser le même moteur de base de données en cours de développement que vous utilisez dans le test et de production. (N’oubliez pas que l’avantage d’utiliser SQL Server Express dans le développement est que la base de données fonctionnera de la même dans les environnements de développement, test et production). Dans cette section, vous devez définir le projet ContosoUniversity à utiliser SQL Server Express LocalDB lorsque vous exécutez l’application à partir de Visual Studio.

Pour effectuer cette migration le plus simple consiste à laisser Code First, et le système d’appartenance crée deux nouvelles bases de données de développement pour vous. À l’aide de cette méthode pour migrer les trois étapes :

1. Modifier les chaînes de connexion pour spécifier les nouvelles bases de données SQL Express LocalDB.
2. Exécutez l’outil d’Administration de Site Web pour créer un utilisateur administrateur. Cela crée la base de données d’appartenance.
3. Utilisez la commande de base de données de mise à jour de Migrations Code First pour créer et la valeur initiale de la base de données de l’application.

### <a name="updating-connection-strings-in-the-webconfig-file"></a>Mise à jour des chaînes de connexion dans le fichier Web.config

Ouvrez le *Web.config* de fichiers et de remplacer le `connectionStrings` par le code suivant :

[!code-xml[Main](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/samples/sample8.xml)]

### <a name="creating-the-membership-database"></a>Création de la base de données d’appartenance

Dans **l’Explorateur de solutions**, sélectionnez le projet ContosoUniversity, puis cliquez sur **Configuration ASP.NET** dans les **projet** menu.

Sélectionnez l’onglet sécurité.

Cliquez sur **créer ou gérer les rôles**, puis créez un **administrateur** rôle.

Revenir à l’onglet sécurité.

Cliquez sur **créer un utilisateur**, puis sélectionnez le **administrateur** case à cocher et créez un utilisateur nommé administrateur.

Fermer le **outil Administration de Site Web**.

### <a name="creating-the-school-database"></a>Création de la base de données School

Ouvrez la fenêtre de Console du Gestionnaire de Package.

Dans le **projet par défaut** liste déroulante, sélectionnez le projet ContosoUniversity.DAL.

Entrez la commande suivante :

[!code-powershell[Main](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12/samples/sample9.ps1)]

Migrations Code First s’applique à la migration initiale qui crée la base de données, puis applique la migration AddBirthDate, il exécute ensuite la méthode de valeur initiale.

Exécuter le site en appuyant sur CTRL-F5. Comme vous l’avez fait pour les environnements de test et de production, exécutez le **ajouter des étudiants** page, ajoutez un nouvel étudiant, puis afficher le nouvel étudiant dans la **étudiants** page. Cette opération vérifie que la base de données School a été créé et initialisé et que vous avez lu et l’accès en écriture à ce dernier.

Sélectionnez le **mise à jour des crédits** page et connectez-vous pour vérifier que la base de données d’appartenance a été déployée et que vous avez accès à ce dernier. Si vous n’avez pas migrez vos comptes d’utilisateur, créez un compte d’administrateur, puis le **mise à jour des crédits** page pour vérifier qu’elle fonctionne.

## <a name="cleaning-up-sql-server-compact-files"></a>Nettoyage des fichiers SQL Server Compact

Vous n’avez plus besoin de fichiers et les packages NuGet qui ont été inclus pour prendre en charge de SQL Server Compact. Si vous souhaitez (cette étape n’est pas obligatoire), vous pouvez nettoyer les fichiers inutiles et les références.

Dans **l’Explorateur de solutions**, supprimer le *.sdf* fichiers à partir de la *application\_données* dossier et le *amd64* et *x86* dossiers à partir de la *bin* dossier.

Dans **l’Explorateur de solutions**, avec le bouton droit de la solution (pas un des projets), puis cliquez sur **gérer les Packages NuGet pour la Solution**.

Dans le volet gauche de la **gérer les Packages NuGet** boîte de dialogue, sélectionnez **les packages installés**.

Sélectionnez le **EntityFramework.SqlServerCompact** du package et cliquez sur **gérer**.

Dans le **sélectionner les projets** boîte de dialogue, les deux projets sont sélectionnés. Pour désinstaller le package dans les deux projets, désactivez les cases à cocher, puis cliquez sur **OK**.

Dans la boîte de dialogue qui vous demande si vous souhaitez désinstaller également des packages dépendants, cliquez sur non. Une d’elles est le package d’Entity Framework que vous devez conserver.

Suivez la même procédure pour désinstaller le **SqlServerCompact** package. (Les packages doivent être désinstallés dans cet ordre, car le **EntityFramework.SqlServerCompact** package dépend de la **SqlServerCompact** package.)

Vous avez réussi à migrer vers SQL Server Express et complète de SQL Server. Dans la prochaine didacticiel vous allez apporter une autre modification de la base de données et que vous allez apprendre à déployer les modifications de la base de données lors de vos bases de données de test et de production utilisent SQL Server Express et complète de SQL Server.

>[!div class="step-by-step"]
[Précédent](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12.md)
[Suivant](deployment-to-a-hosting-provider-deploying-a-sql-server-database-update-11-of-12.md)

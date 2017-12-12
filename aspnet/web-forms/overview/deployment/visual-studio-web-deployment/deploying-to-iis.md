---
uid: web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
title: "Déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement de Test | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, en utilisant des éléments..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/23/2015
ms.topic: article
ms.assetid: 8bf2c4fb-4ee5-4841-bfc2-03462c1f7a7a
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
msc.type: authoredcontent
ms.openlocfilehash: 97910940f9de26ca71b111b945581d2de6650b02
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-web-deployment-using-visual-studio-deploying-to-test"></a>Déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement de Test
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, à l’aide de Visual Studio 2012 ou Visual Studio 2010. Pour plus d’informations sur la série, consultez [le premier didacticiel de la série](introduction.md).


## <a name="overview"></a>Vue d'ensemble

Ce didacticiel montre comment déployer une application de web ASP.NET sur IIS sur l’ordinateur local.

Lorsque vous développez une application, vous testez généralement en l’exécutant dans Visual Studio. Par défaut, les projets d’application web dans Visual Studio 2012 utilisent IIS Express en tant que le serveur web de développement. IIS Express se comporte davantage comme IIS complet que le serveur Visual Studio Development (également appelé « Cassini »), que Visual Studio 2010 utilise par défaut. Mais aucun serveur web de développement fonctionne exactement comme IIS. Par conséquent, il est possible qu’une application s’exécute correctement lorsque vous testez dans Visual Studio, mais échouer lorsqu’elle est déployée sur IIS.

Vous pouvez tester votre application de manière plus fiable des façons suivantes :

1. Déployez l’application à IIS sur votre ordinateur de développement à l’aide du même processus que vous utiliserez ultérieurement à la déployer dans votre environnement de production. Vous pouvez configurer Visual Studio pour utiliser IIS lorsque vous exécutez un projet web, mais cela pourrait tester pas votre processus de déploiement. Cette méthode valide votre processus de déploiement en plus de valider votre application fonctionnera correctement sous IIS.
2. Déployer l’application sur un environnement de test qui est quasiment identique à votre environnement de production. Étant donné que l’environnement de production pour ces didacticiels est Web Apps dans Azure App Service, l’environnement de test idéal est une application web supplémentaire créée dans Azure App Service. Vous utiliseriez cette application web de deuxième uniquement pour les tests, mais il aurait la valeur de la même façon que l’application web de production.

Option 2 est le moyen le plus fiable pour tester, et si vous procédez ainsi, vous ne sont pas nécessairement à options 1. Toutefois, si vous déployez sur une option fournisseur d’hébergement tiers 2 n’est peut-être pas possible ou peut être coûteuse, afin que cette série de didacticiels affiche les deux méthodes. Conseils pour l’option 2 sont fourni dans le [déploiement dans l’environnement de Production](deploying-to-production.md) didacticiel.

Pour plus d’informations sur l’utilisation de serveurs web dans Visual Studio, consultez [serveurs Web dans Visual Studio pour les projets Web ASP.NET](https://msdn.microsoft.com/en-us/library/58wxa9w5.aspx).

Rappel : Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez le didacticiel, veillez à consulter le [page Résolution des problèmes](troubleshooting.md).

## <a name="install-iis"></a>Installer IIS

Pour déployer sur IIS sur votre ordinateur de développement, vous devez disposer d’IIS et Web Deploy installé. Web Deploy est installé par défaut avec Visual Studio, mais IIS n’est pas inclus dans la configuration par défaut de Windows 8 ou Windows 7. Si vous avez déjà installé IIS et le pool d’applications par défaut est déjà défini sur .NET 4, passez à [la section suivante](#sqlexpress).

1. À l’aide de la [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) est recommandée pour installer IIS et Web Deploy, étant donné que Web Platform Installer installe une configuration recommandée pour IIS et installe automatiquement la configuration requise pour IIS et Web Déployez si nécessaire.

    Pour exécuter le programme Web Platform Installer pour installer IIS et Web Deploy, utilisez le lien suivant. Si vous avez déjà installé IIS, Web Deploy ou l’un de leurs composants requis, le programme Web Platform Installer installe uniquement ce qui est manquant.

    - [Installer IIS et Web Deploy à l’aide de WebPI](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=IIS7;ASPNET;NETFramework4;WDeploy)

    Vous verrez des messages indiquant que IIS 7 sera installé. Le fonctionnement du lien pour IIS 8 dans Windows 8, mais pour Windows 8 Assurez-vous que ASP.NET 4.5 est installé, effectuez les opérations suivantes :

    1. Ouvrez **le panneau de configuration**, **programmes et fonctionnalités**, **ou désactiver des fonctionnalités Windows d’activer**.
    2. Développez **Internet Information Services**, **Services World Wide Web**, et **fonctionnalités de développement d’applications**.
    3. Assurez-vous que **ASP.NET 4.5** est sélectionnée.

        ![Sélectionnez ASP.NET 4.5](deploying-to-iis/_static/image1.png)

Après avoir installé IIS, exécutez **Gestionnaire des services Internet** pour vous assurer que le .NET Framework version 4 est affecté au pool d’applications par défaut.

1. Appuyez sur la touche WINDOWS + R pour ouvrir la **exécuter** boîte de dialogue.

    (Ou dans Windows 8 Entrez « exécuter » sur le **Démarrer** page ou dans Windows 7, sélectionnez **exécuter** à partir de la **Démarrer** menu. Si **exécuter** n’est pas dans le **Démarrer** menu, avec le bouton droit de la barre des tâches, cliquez sur **propriétés**, sélectionnez le **Menu Démarrer** , cliquez sur **Personnaliser**, puis sélectionnez **exécuter la commande**.)
2. Entrez « inetmgr », puis cliquez sur **OK**.
3. Dans le **connexions** volet, développez le nœud du serveur, puis sélectionnez **Pools d’applications**. Dans le **Pools d’applications** volet, si **DefaultAppPool** est attribué à .NET framework version 4, comme dans l’illustration suivante, passez à la section suivante.

    [![Inetmgr_showing_4.0_app_pools](deploying-to-iis/_static/image3.png)](deploying-to-iis/_static/image2.png)
4. Si vous voyez uniquement deux pools d’applications et les deux sont définies pour le .NET Framework 2.0, vous devez installer ASP.NET 4 dans IIS.

    Pour Windows 8, consultez les instructions dans la précédente section pour s’assurer que ASP.NET 4.5 est installé, ou consultez [cet article](https://support.microsoft.com/kb/2736284). Pour Windows 7, ouvrez une fenêtre d’invite de commandes en cliquant sur **invite de commandes** dans les fenêtres **Démarrer** menu et en sélectionnant **exécuter en tant qu’administrateur**. Puis exécutez [aspnet\_regiis.exe](https://msdn.microsoft.com/en-us/library/k6h9cz8h.aspx) pour installer ASP.NET 4 dans IIS, à l’aide des commandes suivantes. (Dans les systèmes 32 bits, remplacez « Framework64 » avec « Infrastructure »).

    [!code-console[Main](deploying-to-iis/samples/sample1.cmd)]

    Cette commande crée les nouveaux pools d’applications pour le .NET Framework 4, mais le pool d’applications par défaut sera toujours défini sur la version 2.0. Vous allez déployer une application qui cible .NET 4 pour ce pool d’applications, donc vous devez modifier le pool d’applications pour .NET 4.
5. Si vous avez fermé **Gestionnaire des services Internet**, exécutez-le à nouveau, développez le nœud serveur, puis cliquez sur **Pools d’applications** pour afficher les **Pools d’applications** volet à nouveau.
6. Dans le **Pools d’applications** volet, cliquez sur **DefaultAppPool**, puis, dans le **Actions** volet, cliquez sur **les paramètres de base**.

    [![Inetmgr_selecting_Basic_Settings_for_app_pool](deploying-to-iis/_static/image5.png)](deploying-to-iis/_static/image4.png)
7. Dans le **modifier le Pool d’applications** boîte de dialogue, **version du .NET Framework** à **.NET Framework v4.0.30319** et cliquez sur **OK**.

    [![Selecting_.NET_4_for_DefaultAppPool](deploying-to-iis/_static/image7.png)](deploying-to-iis/_static/image6.png)

IIS est maintenant prêt à publier une application web, mais vous pouvez le faire avant de vous devez créer les bases de données que vous allez utiliser dans l’environnement de test.

<a id="sqlexpress"></a>

## <a name="install-sql-server-express"></a>Installer SQL Server Express

Base de données locale n’est pas conçu pour fonctionner dans IIS, pour votre environnement de test, vous devez disposer de SQL Server Express est installé. Si vous utilisez Visual Studio 2010 SQL Server Express est déjà installé par défaut. Si vous utilisez Visual Studio 2012, vous devez l’installer.

Pour installer SQL Server Express, installez-le à partir de [centre de téléchargement : Microsoft SQL Server 2012 Express](https://www.microsoft.com/en-us/download/details.aspx?id=29062) en cliquant sur [ENU\x64\SQLEXPR\_x64\_ENU.exe](https://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SQLEXPR_x64_ENU.exe) ou [ ENU\x86\SQLEXPR\_x86\_ENU.exe](https://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x86/SQLEXPR_x86_ENU.exe). Si vous choisissez une autre pour votre système, il n’est pas à installer, et vous pouvez essayez-en un autre.

Dans la première page du centre d’Installation de SQL Server, cliquez sur **nouvelle installation SQL Server autonome ou ajouter des fonctionnalités à une installation existante**, suivez les instructions, en acceptant les choix par défaut. Dans l’Assistant installation, acceptez les paramètres par défaut. Pour plus d’informations sur les options d’installation, consultez [installer SQL Server 2012 à partir de l’Assistant Installation (programme d’installation)](https://msdn.microsoft.com/en-us/library/ms143219.aspx).

## <a name="create-sql-server-express-databases-for-the-test-environment"></a>Créer des bases de données SQL Server Express pour l’environnement de test

L’application Contoso University a deux bases de données : la base de données d’appartenance et de la base de données de l’application. Vous pouvez déployer ces bases de données à deux bases de données distinctes ou à une base de données. Vous souhaiterez peut-être les combiner afin de faciliter les jointures de base de données entre la base de données de votre application et votre base de données d’appartenance. Si vous déployez sur un fournisseur d’hébergement tiers, votre plan d’hébergement peut également fournir une raison de les combiner. Par exemple, le fournisseur d’hébergement peut-être facturer pour plusieurs bases de données ou ne permettre pas encore de plusieurs bases de données.

Dans ce didacticiel, vous allez déployer les deux bases de données dans l’environnement de test et une base de données dans les environnements intermédiaire et de production.

À partir de la **vue** menu dans, sélectionnez Visual Studio **l’Explorateur de serveurs** (**l’Explorateur de base de données** dans Visual Web Developer), cliquez sur **des connexions de données**  et sélectionnez **créer une nouvelle base de données SQL Server**.

![Selecting_Create_New_SQL_Server_Database](deploying-to-iis/_static/image8.png)

Dans le **créer une nouvelle base de données SQL Server** boîte de dialogue, entrez «. \SQLExpress » dans le **nom du serveur** zone et « aspnet ContosoUniversity » dans le **nouveau nom de base de données** zone, puis Cliquez sur **OK**.

![Créer aspnet-ContosoUniversity](deploying-to-iis/_static/image9.png)

Suivez la même procédure pour créer une nouvelle base de données SQL Server Express School nommé « ContosoUniversity ».

**L’Explorateur de serveurs** affiche maintenant les deux bases de données.

![Nouvelles bases de données dans l’Explorateur de serveurs](deploying-to-iis/_static/image10.png)

## <a name="create-a-grant-script-for-the-new-databases"></a>Créer un script d’octroi de nouvelles bases de données

Lorsque l’application s’exécute dans IIS sur votre ordinateur de développement, l’application accède à la base de données à l’aide des informations d’identification du pool d’applications par défaut. Toutefois, par défaut, l’identité du pool d’applications n’a pas d’autorisation pour ouvrir les bases de données. Par conséquent, vous devez exécuter un script pour accorder cette autorisation. Dans cette section, vous créez le script que vous allez exécuter plus tard pour vous assurer que l’application peut ouvrir les bases de données lorsqu’elle s’exécute dans IIS.

Avec le bouton droit de la solution (pas un des projets), puis cliquez sur **ajouter un nouvel élément**, puis créez un nouveau **fichier SQL** nommé *Grant.sql*. Copiez les commandes SQL suivantes dans le fichier, puis enregistrez et fermez le fichier :

[!code-sql[Main](deploying-to-iis/samples/sample2.sql)]

> [!NOTE]
> Ce script est conçu pour fonctionner avec SQL Server Express 2012 et les paramètres IIS dans Windows 8 ou Windows 7, car ils sont spécifiés dans ce didacticiel. Si vous utilisez une version différente de SQL Server ou de Windows, ou si vous configurez IIS sur votre ordinateur différemment, les modifications apportées à ce script peuvent être nécessaire. Pour plus d’informations sur les scripts SQL Server, consultez [la documentation en ligne de SQL Server](https://go.microsoft.com/fwlink/?LinkId=132511).


> [!NOTE] 
> 
> **Note de sécurité** ce script donne db\_les autorisations de propriétaire pour l’utilisateur qui accède à la base de données au moment de l’exécution, ce qui est que vous allez utiliser dans l’environnement de production. Dans certains scénarios, vous souhaiterez spécifier un utilisateur qui contient un schéma de base de données complète mettre à jour des autorisations uniquement pour le déploiement, et spécifier pour le moment de l’exécution d’un autre utilisateur qui dispose des autorisations uniquement pour lire et écrire des données. Pour plus d’informations, consultez [révision des modifications Web.config automatique pour les Migrations Code First](#reviewingmigrations) plus loin dans ce didacticiel.


<a id="publish"></a>

## <a name="run-the-grant-script-in-the-application-database"></a>Exécutez le script d’octroi de la base de données d’application

Vous pouvez configurer le profil de publication pour exécuter le script d’octroi dans la base de données d’appartenance durant le déploiement, car ce déploiement de base de données utilise le fournisseur dbDacFx. Impossible d’exécuter des scripts lors du déploiement de Migrations Code First, qui est la façon dont vous déployez la base de données de l’application. Par conséquent, vous devez exécuter manuellement le script avant le déploiement dans la base de données de l’application.

1. Dans Visual Studio, ouvrez le *Grant.sql* fichier que vous avez créé précédemment.
2. Cliquez sur **Connexion**. 

    ![Bouton de connexion](deploying-to-iis/_static/image11.png)
3. Dans le **se connecter au serveur** boîte de dialogue, entrez *. \SQLExpress* comme le **nom du serveur**, puis cliquez sur **connexion**.
4. Dans la liste déroulante de base de données Sélectionnez **ContosoUniversity**, puis cliquez sur **Execute**. 

    ![](deploying-to-iis/_static/image12.png)

L’identité du pool d’applications par défaut dispose maintenant d’autorisations suffisantes dans la base de données d’application pour les Migrations Code First créer les tables de base de données lorsque l’application s’exécute.

## <a name="publish-to-iis"></a>Publier sur IIS

Il existe plusieurs méthodes que vous pouvez déployer sur IIS à l’aide de Visual Studio et déploiement Web :

- Utilisez la publication en un clic de Visual Studio.
- Publier à partir de la ligne de commande.
- Créer un *package de déploiement* et l’installer à l’aide du Gestionnaire des services Internet UI. Le package de déploiement se compose d’un *.zip* fichier qui contient tous les fichiers et les métadonnées nécessaires pour installer un site dans IIS.
- Créer un package de déploiement et l’installer à l’aide de la ligne de commande.

Le processus que vous avez parcouru dans les didacticiels précédents pour configurer Visual Studio pour automatiser les tâches de déploiement s’applique à toutes ces méthodes. Dans ces didacticiels, vous allez utiliser les deux premiers de ces méthodes. Pour plus d’informations sur l’utilisation de packages de déploiement, consultez [déploiement d’une application web en créant et en installant un package de déploiement web](https://go.microsoft.com/fwlink/p/?LinkId=282413#package) dans le plan de contenu de déploiement Web pour Visual Studio et ASP.NET.

Avant la publication, assurez-vous que vous exécutez Visual Studio en mode administrateur. Si vous ne voyez pas **(administrateur)** dans la barre de titre, fermez Visual Studio. Dans Windows 8 **Démarrer** page ou Windows 7 **Démarrer** menu, cliquez sur l’icône de la version de Visual Studio que vous utilisez, puis sélectionnez **exécuter en tant qu’administrateur**. Le mode administrateur est requis pour la publication uniquement lorsque vous publiez sur IIS sur l’ordinateur local.

### <a name="create-the-publish-profile"></a>Créer le profil de publication

1. Dans **l’Explorateur de solutions**, cliquez sur le projet ContosoUniversity (pas le projet ContosoUniversity.DAL) et sélectionnez **publier**.

    Le **publier le site Web** Assistant s’affiche.

    ![Onglet de profil Web Assistant Publication](deploying-to-iis/_static/image13.png)
2. Dans la liste déroulante, sélectionnez  **&lt;nouveau... &gt;**. (Avec la dernière version de Visual Studio est installée, il n’existe aucune liste déroulante, et le bouton pour créer un nouveau profil à partir de zéro est **personnalisé**.)
3. Dans le **nouveau profil** boîte de dialogue, entrez « Test », puis cliquez sur **OK**.

    L’Assistant passe automatiquement à la **connexion** onglet.
4. Dans le **URL du Service** , entrez *localhost*.
5. Dans le **Site/application** , entrez *Default Web Site/ContosoUniversity*
6. Dans le **URL de Destination** , entrez`http://localhost/ContosoUniversity`

    Le **URL de Destination** paramètre n’est pas nécessaire. Visual Studio après déploiement de l’application, il s’ouvre automatiquement votre navigateur par défaut pour cette URL. Si vous ne souhaitez pas le navigateur s’ouvre automatiquement après le déploiement, laissez cette zone vide.
7. Cliquez sur **valider la connexion** pour vérifier que les paramètres sont corrects et que vous pouvez vous connecter à IIS sur l’ordinateur local.

    Une coche verte vérifie que la connexion est établie.

    ![Onglet de connexion Web Assistant Publication](deploying-to-iis/_static/image14.png)
8. Cliquez sur **suivant** pour passer à la **paramètres** onglet.
9. Le **Configuration** zone déroulante spécifie la configuration de build à déployer. Laissez la valeur par défaut de mise en production. Dans ce didacticiel, vous ne déployer les versions Debug.
10. Développez **Options de publication du fichier**, puis sélectionnez **exclure des fichiers de l’application\_dossier de données**.

    Dans l’environnement de test de l’application sera accéder aux bases de données que vous avez créé dans l’instance locale de SQL Server Express, pas le fichier .mdf de fichiers dans le *application\_données* dossier.
11. Laissez le **Precompile lors de la publication** et **supprimer les fichiers supplémentaires à la destination** cases à cocher désactivées.

    ![Options de publication du fichier dans l’onglet Paramètres](deploying-to-iis/_static/image15.png)

    La précompilation est une option qui est utile surtout pour les très grands sites ; elle peut réduire les temps de démarrage de page pour la première fois qu’une page est demandée après que le site est publié.

    Vous n’avez pas besoin de supprimer les fichiers supplémentaires, car c’est votre premier déploiement et il ne sera pas avoir tous les fichiers dans le dossier de destination encore.

    > [!NOTE] 
    > 
    > [!CAUTION]
    > Si vous sélectionnez **supprimer les fichiers supplémentaires** pour un déploiement suivant sur le même site, assurez-vous que vous utilisez la fonctionnalité d’aperçu afin que vous voyez à l’avance quels fichiers seront supprimés avant de déployer. Le comportement attendu est que Web Deploy entraînera la suppression les fichiers sur le serveur de destination que vous avez supprimé de votre projet. Toutefois, la structure de dossier complet dans les dossiers source et de destination est comparée, et dans certains scénarios de déploiement Web risque de supprimer les fichiers que vous ne souhaitez pas supprimer.
    > 
    > Par exemple, si vous avez une application web dans un sous-dossier sur le serveur lorsque vous déployez un projet dans le dossier racine, le sous-dossier est supprimé. Vous pouvez avoir un projet pour le site principal à contoso.com et un autre projet pour un blog à Contoso.com/blog. La blog de l’application est dans un sous-dossier. Si vous sélectionnez Supprimer les fichiers supplémentaires à la destination lorsque vous déployez le site principal, l’application de blog est supprimée.
    > 
    > Pour un autre exemple, votre application\_dossier de données peut sont supprimé de manière inattendue. Certaines bases de données telles que SQL Server Compact stockent les fichiers de base de données dans l’application\_dossier de données. Après le déploiement initial, vous souhaitez conserver copie des fichiers de base de données dans les déploiements suivants afin de vous exclure une application\_données sous l’onglet Package/Publication Web. Après quoi, si vous devez supprimer les fichiers supplémentaires à la destination sélectionnée, vos fichiers de base de données et l’application\_dossier de données elle-même sera supprimé la prochaine fois que vous publiez.

### <a name="configure-deployment-for-the-membership-database"></a>Configurer le déploiement de la base de données d’appartenance

Les étapes suivantes s’appliquent à la **DefaultConnection** de la base de données dans le **bases de données** section de la boîte de dialogue.

1. Dans le **chaîne de connexion à distance** , entrez la chaîne de connexion qui pointe vers la nouvelle base de l’appartenance de SQL Server Express.

    [!code-console[Main](deploying-to-iis/samples/sample3.cmd)]

    Le processus de déploiement placera cette chaîne de connexion dans le fichier Web.config déployé, car **utiliser cette chaîne de connexion lors de l’exécution** est sélectionnée.

    Vous pouvez également obtenir la chaîne de connexion à partir de **l’Explorateur de serveurs**. Dans **l’Explorateur de serveurs**, développez **des connexions de données** et sélectionnez le  **&lt;machinename&gt;\sqlexpress.aspnet-ContosoUniversity** base de données, puis à partir de la **propriétés** copie de la fenêtre du **chaîne de connexion** valeur. Que la chaîne de connexion aura un paramètre supplémentaire que vous pouvez supprimer : `Pooling=False`.
2. Sélectionnez **base de données de mise à jour**.

    Cela entraîne le schéma de base de données qui sera créé dans la base de données de destination pendant le déploiement. Dans les étapes suivantes, vous spécifiez des scripts supplémentaires que vous devez exécuter : une pour accorder l’accès de base de données vers le pool d’applications par défaut et pour déployer des données.
3. Cliquez sur **configurer des mises à jour de la base de données**.
4. Dans le **configurer les mises à jour de base de données** boîte de dialogue, cliquez sur **ajouter un Script SQL** , puis accédez à la *Grant.sql* script précédemment enregistré dans le dossier de solution.
5. Répétez le processus pour ajouter le *aspnet-données-dev.sql* script.

    ![Configurer les mises à jour de la base de données pour la base de données d’appartenance](deploying-to-iis/_static/image16.png)
6. Cliquez sur **Fermer**.

### <a name="configure-deployment-for-the-application-database"></a>Configurer le déploiement de la base de données d’application

Lorsque Visual Studio détecte un Entity Framework `DbContext` (classe), il crée une entrée dans le **bases de données** section a une **exécuter des Migrations Code First** case à cocher au lieu d’une  **Mettre à jour de la base de données** case à cocher. Pour ce didacticiel, vous utiliserez cette case à cocher pour spécifier le déploiement de Migrations Code First.

Dans certains scénarios, vous utilisez peut-être une `DbContext` base de données, mais vous souhaitez utiliser le fournisseur dbDacFx au lieu de Migrations pour déployer la base de données. Dans ce cas, consultez [comment déployer une base de données Code First sans Migrations ?](https://msdn.microsoft.com/en-us/library/ee942158.aspx#deploy_code_first_without_migrations) dans le Forum aux questions de déploiement Web ASP.NET sur MSDN.

Les étapes suivantes s’appliquent à la **SchoolContext** de la base de données dans le **bases de données** section de la boîte de dialogue.

1. Dans le **chaîne de connexion à distance** , entrez la chaîne de connexion qui pointe vers la nouvelle base d’application SQL Server Express.

    [!code-console[Main](deploying-to-iis/samples/sample4.cmd)]

    Le processus de déploiement placera cette chaîne de connexion dans le fichier Web.config déployé, car **utiliser cette chaîne de connexion lors de l’exécution** est sélectionnée.

    Vous pouvez également obtenir la chaîne de connexion de base de données application **l’Explorateur de serveurs** la même manière que vous avez obtenu l’appartenance de base de données de la chaîne de connexion.
2. Sélectionnez **exécuter fonctionnalité Migrations Code First (s’exécute sur le démarrage de l’application)**.

    Cette option provoque le processus de déploiement configurer le fichier Web.config déployé pour spécifier le `MigrateDatabaseToLatestVersion` initialiseur. Ce dernier met automatiquement à jour la base de données vers la dernière version lors de l’application accède à la base de données pour la première fois après le déploiement.

### <a name="configure-publish-profile-transforms"></a>Configurer des transformations de profil de publication

1. Cliquez sur **fermer**, puis cliquez sur **Oui** lorsque le système vous demande si vous souhaitez enregistrer les modifications.
2. Dans **l’Explorateur de solutions**, développez **propriétés**, développez **PublishProfiles**.
3. Cliquez Rright *Test.pubxml,* puis cliquez sur **ajouter une transformation de Config**.

    ![Ajouter des menus de transformation Web.config](deploying-to-iis/_static/image17.png)

    Visual Studio crée le *Web.Test.config* fichier de transformation et l’ouvre.
4. Dans le *Web.Test.config* de transformer le fichier, insérez le code suivant immédiatement après l’ouverture balise de configuration.

    [!code-xml[Main](deploying-to-iis/samples/sample5.xml)]

    Lorsque vous utilisez le Test de profil de publication, cette transformation définit l’indicateur d’environnement à « Test ». Dans le site déployé, vous verrez « (Test) » après le titre H1 de l’université « Contoso ».
5. Enregistrez et fermez le fichier.
6. Avec le bouton droit le *Web.Test.config* de fichier et cliquez sur **aperçu transformer** pour vous assurer que la transformation que vous avez codé génère les modifications attendues.

    Le **Web.config aperçu** fenêtre affiche le résultat de l’application à la fois le *Web.Release.config* transforme et *Web.Test.config* transforme.

### <a name="preview-the-deployment-updates"></a>Afficher un aperçu du déploiement des mises à jour

1. Ouvrez le **publier le site Web** Assistant (droit sur le projet ContosoUniversity et cliquez sur **publier**).
2. Dans le **aperçu** onglet, vérifiez que le **Test** profil est toujours activée, puis cliquez sur **démarrer la version préliminaire** pour afficher la liste des fichiers à copier.

    ![Bouton Aperçu](deploying-to-iis/_static/image18.png)

    ![Aperçu de la publication](deploying-to-iis/_static/image19.png)

    Vous pouvez également cliquer sur le **base de données préliminaire** lien pour afficher les scripts qui seront exécutent dans la base de données d’appartenance. (Aucun des scripts ne sont exécutés pour le déploiement de Migrations Code First, donc rien pour afficher un aperçu de la base de données de l’application.)
3. Cliquez sur **Publier**.

    Si Visual Studio n’est pas en mode administrateur, vous pouvez obtenir un message d’erreur qui indique une erreur d’autorisation. Dans ce cas, fermez Visual Studio, ouvrez-le en mode administrateur et essayez de publier à nouveau.

    Si Visual Studio est en mode administrateur, le **sortie** rapports réussie de la fenêtre générer et publier.

    ![Output_window_publish_Test](deploying-to-iis/_static/image20.png)

    Si vous avez entré l’URL dans le **URL de Destination** zone sur le profil de publication **connexion** onglet, le navigateur s’ouvre automatiquement à la page d’accueil de l’Université de Contoso s’exécutant dans IIS sur l’ordinateur local.

## <a name="test-in-the-test-environment"></a>Tester dans l’environnement de test

Notez que l’indicateur d’environnement affiche « (Test) » au lieu de « (Dev) », ce qui indique que le *Web.config* réussie de la transformation de l’indicateur d’environnement.

Exécutez le **instructeurs** page pour vérifier que le premier Code amorcée la base de données de formateur. Lorsque vous sélectionnez cette page, il peut prendre quelques minutes à charger, car le premier Code de créer la base de données, puis le `Seed` (méthode). (Il n’a pas le faire quand vous étiez sur la page d’accueil, car l’application n’a pas été tentent d’accéder à la base de données encore.)

Cliquez sur le **étudiants** onglet pour vérifier ne qu’aucun élève la base de données déployée.

Sélectionnez **ajouter des étudiants** à partir de la **étudiants** menu, ajouter un étudiant et afficher le nouvel étudiant dans la **étudiants** page pour vérifier que vous pouvez écrire avec succès à la base de données .

À partir de la **cours** menu, sélectionnez **mise à jour des crédits**. Le **crédits de mise à jour** page nécessite des autorisations d’administrateur, afin que la **connexion** page s’affiche. Entrez les informations d’identification du compte administrateur que vous avez créé plus tôt (« admin » et « devpwd »). Le **mise à jour des crédits** page s’affiche, qui vérifie que le compte d’administrateur que vous avez créé dans le didacticiel précédent a été correctement déployé sur l’environnement de test.

Vérifiez qu’une *Elmah* dossier existe dans le *c:\inetpub\wwwroot\ContosoUniversity* dossier avec uniquement le fichier d’espace réservé qu’elle contient.

<a id="reviewingmigrations"></a>

## <a name="review-the-automatic-webconfig-changes-for-code-first-migrations"></a>Passez en revue les modifications de Web.config automatique pour les Migrations Code First

Ouvrez le *Web.config* fichier dans l’application déployée sur *C:\inetpub\wwwroot\ContosoUniversity* et vous pouvez voir où le processus de déploiement configurées automatiquement Migrations Code First pour mettre à jour la base de données vers la dernière version.

![](deploying-to-iis/_static/image21.png)

Le processus de déploiement créé également une nouvelle chaîne de connexion pour les Migrations Code First à utiliser exclusivement pour la mise à jour le schéma de base de données :

![Chaîne de connexion Database_Publish](deploying-to-iis/_static/image22.png)

Cette chaîne de connexion supplémentaires vous permet de spécifier un compte d’utilisateur pour les mises à jour du schéma de base de données et un autre compte d’utilisateur pour l’accès aux données application. Par exemple, vous pouvez attribuer le **db\_propriétaire** rôle pour les Migrations Code First, et **db\_datareader** et **db\_datawriter**rôles à l’application. Il s’agit d’un modèle de défense en profondeur commun qui empêche le code potentiellement malveillant dans l’application à partir de la modification du schéma de base de données. (Par exemple, cela peut se produire dans une attaque d’injection SQL.) Ce modèle n’est pas utilisé par ces didacticiels. Si vous souhaitez implémenter ce modèle dans votre scénario, vous pouvez le faire en effectuant les étapes suivantes :

1. Dans le **paramètres** onglet de la **publier le site Web** Assistant, entrez la chaîne de connexion qui spécifie un utilisateur avec les autorisations de mise à jour de schéma de base de données complète, puis désactivez la **utiliser cette chaîne de connexion lors de l’exécution** case à cocher. Dans le fichier Web.config déployé, cela devient le `DatabasePublish` chaîne de connexion.
2. Créer une transformation du fichier Web.config pour la chaîne de connexion que vous souhaitez que l’application peut utiliser au moment de l’exécution.

## <a name="summary"></a>Résumé

Vous avez déployé votre application à IIS sur votre ordinateur de développement et testé il.

![Page d’accueil dans le Test](deploying-to-iis/_static/image23.png)

Cette opération vérifie que le processus de déploiement copie le contenu de l’application vers l’emplacement approprié (sauf les fichiers que vous ne voulez pas déployer) et également que Web Deploy IIS correctement configuré au cours du déploiement. Dans l’étape suivante du didacticiel, vous allez exécuter un test plus qui se trouve une tâche de déploiement qui n’a pas encore été effectuée : définition des autorisations de dossier sur le *Elmah* dossier.

## <a name="more-information"></a>Complément d'information

Pour plus d’informations sur l’exécution d’IIS ou IIS Express dans Visual Studio, consultez les ressources suivantes :

- [Présentation d’IIS Express](https://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview) sur le site IIS.net.
- [Présentation d’IIS Express](https://weblogs.asp.net/scottgu/archive/2010/06/28/introducing-iis-express.aspx) sur le blog de Scott Guthrie.
- [Pour les projets Web ASP.NET des serveurs dans Visual Studio Web](https://msdn.microsoft.com/en-us/library/58wxa9w5.aspx).
- [Principales différences entre IIS et le serveur de développement ASP.NET](../../older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-cs.md) sur le site ASP.NET.

Pour plus d’informations sur les problèmes susceptibles de se produire lorsque votre application s’exécute en confiance moyenne, consultez [hébergeant des Applications ASP.NET en mode de confiance moyenne](http://www.4guysfromrolla.com/articles/100307-1.aspx) sur les 4 Guys Rolla site.

>[!div class="step-by-step"]
[Précédent](project-properties.md)
[Suivant](setting-folder-permissions.md)

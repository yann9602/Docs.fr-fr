---
uid: web-forms/overview/older-versions-security/membership/creating-the-membership-schema-in-sql-server-cs
title: "Création du schéma de l’appartenance dans SQL Server (c#) | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel commence par examiner les techniques permettant d’ajouter le schéma nécessaire à la base de données pour pouvoir utiliser SqlMembershipProvider. Après cela, nous wi..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/18/2008
ms.topic: article
ms.assetid: b4ac129d-1b8e-41ca-a38f-9b19d7c7bb0e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-security/membership/creating-the-membership-schema-in-sql-server-cs
msc.type: authoredcontent
ms.openlocfilehash: 38fc60b79a348ab198069a9a80a085e0dc4bcb88
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="creating-the-membership-schema-in-sql-server-c"></a>Création du schéma de l’appartenance dans SQL Server (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/f/5/3f5a8605-c526-4b34-b3fd-a34167117633/ASPNET_Security_Tutorial_04_CS.zip) ou [télécharger le PDF](http://download.microsoft.com/download/3/f/5/3f5a8605-c526-4b34-b3fd-a34167117633/aspnet_tutorial04_MembershipSetup_cs.pdf)

> Ce didacticiel commence par examiner les techniques permettant d’ajouter le schéma nécessaire à la base de données pour pouvoir utiliser SqlMembershipProvider. Après cela, nous examiner les tables de clés dans le schéma et discuter de leur but et l’importance. Ce didacticiel se termine par un examen de la procédure pour indiquer à quel fournisseur de l’infrastructure de l’appartenance doit utiliser une application ASP.NET.


## <a name="introduction"></a>Introduction

Les didacticiels de deux précédents examinés à l’aide de l’authentification par formulaire pour identifier les visiteurs du site Web. L’infrastructure d’authentification forms facilite aux développeurs connecte un utilisateur à un site Web et de mémoriser les entre les visites via l’utilisation de tickets d’authentification. La `FormsAuthentication` classe inclut des méthodes pour générer le ticket et son ajout aux cookies du visiteur. Le `FormsAuthenticationModule` examine toutes les demandes entrantes, pour ceux avec un ticket d’authentification valide, crée et associe un `GenericPrincipal` et un `FormsIdentity` objet avec la demande en cours. L’authentification par formulaire est simplement un mécanisme pour l’octroi d’un ticket d’authentification pour un visiteur lors de la journalisation dans et, pour les demandes suivantes, l’analyse de ce ticket pour déterminer l’identité de l’utilisateur. Pour une application web prendre en charge les comptes d’utilisateur, nous devons toujours implémenter un magasin d’utilisateurs et ajouter des fonctionnalités pour valider les informations d’identification, d’inscrire de nouveaux utilisateurs et la multitude d’autres tâches relatives aux comptes d’utilisateur.

Avant d’ASP.NET 2.0, les développeurs ont été sur le raccordement pour l’implémentation de toutes ces tâches relatives aux comptes d’utilisateur. Heureusement, l’équipe ASP.NET reconnu cet inconvénient et a introduit l’infrastructure d’appartenance avec ASP.NET 2.0. L’infrastructure de l’appartenance est un ensemble de classes dans le .NET Framework qui fournissent une interface de programmation pour accomplir des tâches de lié à un compte utilisateur principal. Cette infrastructure est construite sur le [modèle de fournisseur](http://aspnet.4guysfromrolla.com/articles/101905-1.aspx), ce qui permet aux développeurs une implémentation personnalisée dans une API standard.

Comme indiqué dans le <a id="Tutorial1"> </a> [ *principes élémentaires de sécurité et la prise en charge ASP.NET* ](../introduction/security-basics-and-asp-net-support-cs.md) (didacticiel), le .NET Framework est fourni avec deux fournisseurs d’appartenances intégrés : [ `ActiveDirectoryMembershipProvider` ](https://msdn.microsoft.com/library/system.web.security.activedirectorymembershipprovider.aspx) et [ `SqlMembershipProvider` ](https://msdn.microsoft.com/library/system.web.security.sqlmembershipprovider.aspx). Comme son nom l’indique, le `SqlMembershipProvider` utilise une base de données Microsoft SQL Server comme magasin de l’utilisateur. Pour utiliser ce fournisseur dans une application, nous devons indiquer au fournisseur de base de données à utiliser comme magasin. Comme vous pouvez l’imaginer, la `SqlMembershipProvider` attend la base de données du magasin utilisateur d’avoir certaines tables de base de données, les vues et les procédures stockées. Nous devons ajouter ce schéma attendu à la base de données sélectionnée.

Ce didacticiel commence par examiner les techniques permettant d’ajouter le schéma nécessaire à la base de données pour pouvoir utiliser le `SqlMembershipProvider`. Après cela, nous examiner les tables de clés dans le schéma et discuter de leur but et l’importance. Ce didacticiel se termine par un examen de la procédure pour indiquer à quel fournisseur de l’infrastructure de l’appartenance doit utiliser une application ASP.NET.

C’est parti !

## <a name="step-1-deciding-where-to-place-the-user-store"></a>Étape 1 : Vous décidez où placer le magasin de l’utilisateur

Données d’une application ASP.NET sont généralement stockées dans un nombre de tables dans une base de données. Lorsque vous implémentez le `SqlMembershipProvider` nous devons déterminer s’il faut placer le schéma d’abonnement dans la même base de données en tant que les données d’application ou dans une autre base de données de schéma de base de données.

Je vous recommande de localiser le schéma d’abonnement dans la même base de données en tant que les données d’application pour les raisons suivantes :

- **Facilité de maintenance** une application dont les données sont encapsulées dans une base de données est plus facile à comprendre, gérer et déployer une application qui a deux bases de données distinctes.
- **L’intégrité relationnelle** en recherchant les tables liées à l’appartenance dans la même base de données en tant que tables de l’application qu’il est possible d’établir [les contraintes de clé étrangères](http://en.wikipedia.org/wiki/Foreign_key) entre les clés primaires dans les Tables liées à l’appartenance et les tables d’application associé.

Découplage les données de magasin et application d’utilisateur dans les bases de données distinctes convient uniquement si vous possédez plusieurs applications, que chacun utilise des bases de données distinctes, mais doivent partager un magasin d’utilisateurs courants.

### <a name="creating-a-database"></a>Création d’une base de données

L’application que nous concevons depuis le deuxième didacticiel n’a pas encore nécessaire une base de données. Nous avons besoin d’un maintenant, toutefois, pour le magasin de l’utilisateur. Nous allons en créer un et puis lui ajouter le schéma requis par le `SqlMembershipProvider` fournisseur (voir l’étape 2).

> [!NOTE]
> Tout au long de cette série de didacticiels, nous utiliserons une [Microsoft SQL Server 2005 Express Edition](https://msdn.microsoft.com/sql/Aa336346.aspx) base de données pour stocker les tables d’application et le `SqlMembershipProvider` schéma. Cette décision a été effectuée pour deux raisons : tout d’abord, en raison de son coût - libre - Express Edition est la version la plus lisiblement accessible de SQL Server 2005 ; en second lieu, les bases de données SQL Server 2005 Express Edition peuvent être placés directement dans l’application web `App_Data` dossier, en rendant un jeu d’enfant pour la base de données du package et une application web ensemble dans un fichier ZIP et redéployer sans les instructions d’installation spéciales ou les options de configuration. Si vous préférez suivre son déroulement à l’aide d’une version non - Express Edition de SQL Server, n’hésitez pas. Les étapes sont virtuellement identiques. Le `SqlMembershipProvider` schéma fonctionne avec les versions de Microsoft SQL Server 2000 et de.


À partir de l’Explorateur de solutions, cliquez sur le `App_Data` dossier et choisir d’ajouter un nouvel élément. (Si vous ne voyez pas une `App_Data` dossier dans votre projet, avec le bouton droit sur le projet dans l’Explorateur de solutions, sélectionnez Ajouter le dossier ASP.NET et choisir `App_Data`.) À partir de la boîte de dialogue Ajouter un nouvel élément, choisissez Ajouter une nouvelle base de données SQL nommé `SecurityTutorials.mdf`. Dans ce didacticiel, nous allons ajouter le `SqlMembershipProvider` schéma pour cette base de données ; dans les didacticiels suivants, nous créerons supplémentaires tables pour capturer les données d’application.


[![Ajouter une nouvelle base de données SQL nommé SecurityTutorials.mdf de base de données dans le dossier App_Data](creating-the-membership-schema-in-sql-server-cs/_static/image2.png)](creating-the-membership-schema-in-sql-server-cs/_static/image1.png)

**Figure 1**: ajouter une nouvelle base de données SQL nommée `SecurityTutorials.mdf` de la base de données pour le `App_Data` dossier ([cliquez pour afficher l’image en taille réelle](creating-the-membership-schema-in-sql-server-cs/_static/image3.png))


Ajout d’une base de données pour le `App_Data` dossier inclut automatiquement dans la vue Explorateur de base de données. (Dans la version non - Express Edition de Visual Studio, l’Explorateur de base de données est appelée l’Explorateur de serveurs). Accédez à l’Explorateur de base de données et développez le juste ajoutés `SecurityTutorials` base de données. Si vous ne voyez pas l’Explorateur de base de données à l’écran, ouvrez le menu Affichage et choisissez Explorateur de base de données ou appuyez sur Ctrl + Alt + S. Comme le montre la Figure 2, le `SecurityTutorials` base de données est vide ; il contient aucune table, aucune vue et aucune procédure stockée.


[![La base de données SecurityTutorials est actuellement vide](creating-the-membership-schema-in-sql-server-cs/_static/image5.png)](creating-the-membership-schema-in-sql-server-cs/_static/image4.png)

**Figure 2**: le `SecurityTutorials` base de données est actuellement vide ([cliquez pour afficher l’image en taille réelle](creating-the-membership-schema-in-sql-server-cs/_static/image6.png))


## <a name="step-2-adding-thesqlmembershipproviderschema-to-the-database"></a>Étape 2 : Ajout de la`SqlMembershipProvider`schéma de la base de données

Le `SqlMembershipProvider` nécessite un ensemble particulier de tables, vues et procédures stockées doit être installé dans la base de données utilisateur. Ces objets de base de données requis peuvent être ajoutés à l’aide de la [ `aspnet_regsql.exe` outil](https://msdn.microsoft.com/library/ms229862.aspx). Ce fichier se trouve dans le `%WINDIR%\Microsoft.Net\Framework\v2.0.50727\` dossier.

> [!NOTE]
> Le `aspnet_regsql.exe` outil offre des fonctionnalités de ligne de commande et une interface utilisateur graphique. L’interface graphique est plus conviviale et est ce que nous allons examiner dans ce didacticiel. L’interface de ligne de commande est utile lorsque l’ajout de la `SqlMembershipProvider` schéma doit être automatisée, comme la génération de scripts ou automatisée des scénarios de test.


Le `aspnet_regsql.exe` outil est utilisé pour ajouter ou supprimer des *les services d’application ASP.NET* à une base de données SQL Server spécifiée. Les services d’application ASP.NET englobent les schémas pour le `SqlMembershipProvider` et `SqlRoleProvider`, ainsi que les schémas pour les fournisseurs basé sur SQL pour d’autres frameworks ASP.NET 2.0. Nous devons fournir deux bits d’information pour la `aspnet_regsql.exe` outil :

- Si vous voulez ajouter ou supprimer des services d’application, et
- La base de données à partir de laquelle ajouter ou supprimer le schéma de services d’application

Dans la demande pour la base de données à utiliser, le `aspnet_regsql.exe` outil nous demande de fournir le nom du serveur sur la base de données réside sur les informations d’identification de sécurité pour la connexion à la base de données et le nom de la base de données. Si vous n’utilisez pas la non - Express Edition de SQL Server, vous devez savoir déjà de ces informations, car il s’agit des mêmes informations que vous devez fournir via une chaîne de connexion lorsque vous travaillez avec la base de données via une page web ASP.NET. Déterminer le nom du serveur et de base de données lors de l’utilisation d’une base de données SQL Server 2005 Express Edition dans la `App_Data` dossier, toutefois, est un peu plus complexe.

La section suivante décrit une méthode simple pour spécifier le nom de serveur et de base de données pour une base de données SQL Server 2005 Express Edition dans la `App_Data` dossier. Si vous n’utilisez pas SQL Server 2005 Express Edition n’hésitez à passer à l’installation la section de Services d’Application.

### <a name="determining-the-server-and-database-name-for-a-sql-server-2005-express-edition-database-in-theappdatafolder"></a>Déterminer le serveur et le nom de la base de données pour une base de données SQL Server 2005 Express Edition dans la`App_Data`dossier

Pour pouvoir utiliser le `aspnet_regsql.exe` outil que nous avons besoin de connaître les noms de serveur et de base de données. Le nom du serveur est `localhost\InstanceName`. Très probablement, le *InstanceName* est `SQLExpress`. Toutefois, si vous avez installé SQL Server 2005 Express Edition manuellement (autrement dit, vous n’avez pas installé il automatiquement lors de l’installation de Visual Studio), il est possible que vous avez sélectionné un autre nom d’instance.

Le nom de la base de données est un peu plus difficile à déterminer. Bases de données dans le `App_Data` dossier ont généralement un nom de base de données qui inclut un [identificateur global unique](http://en.wikipedia.org/wiki/Globally_Unique_Identifier) , ainsi que le chemin d’accès au fichier de base de données. Nous devons déterminer ce nom de base de données afin d’ajouter le schéma de services d’application via `aspnet_regsql.exe`.

Pour vérifier le nom de la base de données, le plus simple consiste à examiner via SQL Server Management Studio. SQL Server Management Studio fournit une interface graphique pour la gestion des bases de données SQL Server 2005, mais il n’est pas livré avec la Express Edition de SQL Server 2005. La bonne nouvelle est que [que vous pouvez télécharger](https://www.microsoft.com/downloads/details.aspx?FamilyId=C243A5AE-4BD1-4E3D-94B8-5A0F62BF7796&amp;displaylang=en) le libre Express Edition de SQL Server Management Studio.

> [!NOTE]
> Si vous avez également une version non - Express Edition de SQL Server 2005 est installé sur votre bureau, est probablement installée à la version complète de Management Studio. Vous pouvez utiliser la version complète pour déterminer le nom de la base de données, suivant les mêmes étapes décrites ci-dessous pour l’édition Express.


Commencez par fermer Visual Studio pour vous assurer que tous les verrous imposées par Visual Studio sur le fichier de base de données sont fermées. Ensuite, lancez SQL Server Management Studio et connectez-vous à le `localhost\InstanceName` base de données pour SQL Server 2005 Express Edition. Comme indiqué précédemment, sans doute le nom d’instance est `SQLExpress`. Pour l’option d’authentification, sélectionnez l’authentification Windows.


[![Se connecter à l’Instance SQL Server 2005 Express Edition](creating-the-membership-schema-in-sql-server-cs/_static/image8.png)](creating-the-membership-schema-in-sql-server-cs/_static/image7.png)

**Figure 3**: se connecter à SQL Server 2005 Express Edition Instance ([cliquez pour afficher l’image en taille réelle](creating-the-membership-schema-in-sql-server-cs/_static/image9.png))


Une fois connecté à l’instance de SQL Server 2005 Express Edition, Management Studio affiche les dossiers pour les bases de données, les paramètres de sécurité, les objets de serveur et ainsi de suite. Si vous développez l’onglet bases de données vous verrez la `SecurityTutorials.mdf` base de données est *pas* enregistrée dans l’instance de la base de données - nous avons besoin attacher la base de données tout d’abord.

Avec le bouton droit sur le dossier bases de données et cliquez sur Attacher dans le menu contextuel. Cela affiche la boîte de dialogue Attacher les bases de données. À ce stade, cliquez sur le bouton Ajouter, recherchez le `SecurityTutorials.mdf` de base de données, puis cliquez sur OK. La figure 4 montre la boîte de dialogue Attacher les bases de données après la `SecurityTutorials.mdf` base de données a été sélectionné. La figure 5 illustre l’Explorateur d’objets de Management Studio une fois que la base de données a été attachée avec succès.


[![Attachez la base de données SecurityTutorials.mdf](creating-the-membership-schema-in-sql-server-cs/_static/image11.png)](creating-the-membership-schema-in-sql-server-cs/_static/image10.png)

**Figure 4**: attacher le `SecurityTutorials.mdf` base de données ([cliquez pour afficher l’image en taille réelle](creating-the-membership-schema-in-sql-server-cs/_static/image12.png))


[![La base de données SecurityTutorials.mdf apparaît dans le dossier de bases de données](creating-the-membership-schema-in-sql-server-cs/_static/image14.png)](creating-the-membership-schema-in-sql-server-cs/_static/image13.png)

**Figure 5**: le `SecurityTutorials.mdf` base de données apparaît dans le dossier de bases de données ([cliquez pour afficher l’image en taille réelle](creating-the-membership-schema-in-sql-server-cs/_static/image15.png))


Comme le montre la Figure 5, le `SecurityTutorials.mdf` base de données a un nom plutôt abstruse. Nous allons modifier plus explicite (et plus facile à taper) nom. Avec le bouton droit sur la base de données, cliquez sur Renommer dans le menu contextuel et renommez-le `SecurityTutorialsDatabase`. Cela ne modifie pas le nom de fichier, simplement le nom de la base de données utilise pour s’identifier auprès de SQL Server.


[![Renommez la base de données SecurityTutorialsDatabase](creating-the-membership-schema-in-sql-server-cs/_static/image17.png)](creating-the-membership-schema-in-sql-server-cs/_static/image16.png)

**Figure 6**: renommer la base de données `SecurityTutorialsDatabase`([cliquez pour afficher l’image en taille réelle](creating-the-membership-schema-in-sql-server-cs/_static/image18.png))


À ce stade, nous savons les noms de serveur et de base de données pour le `SecurityTutorials.mdf` fichier de base de données : `localhost\InstanceName` et `SecurityTutorialsDatabase`, respectivement. Nous sommes maintenant prêts à installer les services d’application via le `aspnet_regsql.exe` outil.

### <a name="installing-the-application-services"></a>L’installation des Services d’Application

Pour lancer le `aspnet_regsql.exe` outil, accédez au menu Démarrer et choisissez Exécuter. Entrez `%WINDIR%\Microsoft.Net\Framework\v2.0.50727\aspnet_regsql.exe` dans la zone de texte et cliquez sur OK. Vous pouvez également utiliser l’Explorateur Windows pour Explorer le dossier approprié et double-cliquez sur le `aspnet_regsql.exe` fichier. Chacune de ces approches ne supprime pas les mêmes résultats.

En cours d’exécution le `aspnet_regsql.exe` outil sans argument de ligne de commande lance l’interface utilisateur graphique d’Assistant Installation de ASP.NET SQL Server. L’Assistant facilite ajouter ou supprimer les services d’application ASP.NET sur une base de données spécifiée. Le premier écran de l’Assistant, illustré dans la Figure 7, décrit l’objectif de l’outil.


[![Utilisez l’Assistant pour le programme d’installation ASP.NET SQL Server simplifie pour ajouter le schéma de l’appartenance](creating-the-membership-schema-in-sql-server-cs/_static/image20.png)](creating-the-membership-schema-in-sql-server-cs/_static/image19.png)

**Figure 7**: utiliser le ASP.NET SQL Server Configuration Assistant rend pour ajouter le schéma de l’appartenance ([cliquez pour afficher l’image en taille réelle](creating-the-membership-schema-in-sql-server-cs/_static/image21.png))


La deuxième étape de l’Assistant vous demande de nous si nous voulons ajouter les services d’application ou les supprimer. Étant donné que nous souhaitons ajouter des tables, vues et procédures stockées nécessaires à la `SqlMembershipProvider`, choisissez la configuration de SQL Server pour l’option de services d’application. Ultérieurement, si vous souhaitez supprimer de ce schéma de votre base de données, réexécutez cet Assistant, mais choisissez plutôt les informations de services d’application supprimer à partir d’une option de base de données existante.


[![Choisissez la configuration de SQL Server pour l’Option de Services d’Application](creating-the-membership-schema-in-sql-server-cs/_static/image23.png)](creating-the-membership-schema-in-sql-server-cs/_static/image22.png)

**Figure 8**: choisissez la configuration de SQL Server pour l’Option de Services d’Application ([cliquez pour afficher l’image en taille réelle](creating-the-membership-schema-in-sql-server-cs/_static/image24.png))


La troisième étape vous invite à entrer les informations de base de données : le nom du serveur, les informations d’authentification et le nom de la base de données. Si vous avez suivi ce didacticiel et avoir ajouté le `SecurityTutorials.mdf` à la base de données `App_Data`, attaché à `localhost\InstanceName`et renommé en `SecurityTutorialsDatabase`, puis utilisez les valeurs suivantes :

- Serveur :`localhost\InstanceName`
- Authentification Windows
- Base de données :`SecurityTutorialsDatabase`


[![Entrez les informations de base de données](creating-the-membership-schema-in-sql-server-cs/_static/image26.png)](creating-the-membership-schema-in-sql-server-cs/_static/image25.png)

**Figure 9**: entrez les informations de base de données ([cliquez pour afficher l’image en taille réelle](creating-the-membership-schema-in-sql-server-cs/_static/image27.png))


Après avoir entré les informations de base de données, cliquez sur Suivant. La dernière étape résume les étapes à entreprendre. Cliquez sur Suivant pour installer les services d’application, puis sur Terminer pour terminer l’Assistant.

> [!NOTE]
> Si vous utilisez Management Studio pour attacher la base de données et renommez le fichier de base de données, veillez à détacher la base de données et fermez Management Studio avant de rouvrir Visual Studio. Pour détacher le `SecurityTutorialsDatabase` de base de données, avec le bouton droit sur le nom de la base de données et, dans le menu tâches, cliquez sur Détacher.


À la fin de l’Assistant, retournez dans Visual Studio et accédez à l’Explorateur de base de données. Développez le dossier Tables. Vous devez voir une série de tables dont les noms commencent par le préfixe `aspnet_`. De même, une variété de vues et procédures stockées sont accessibles dans les dossiers de vues et procédures stockées. Ces objets de base de données constituent le schéma de services d’application. Nous allons examiner les objets de base de données d’appartenance et de rôle spécifique à l’étape 3.


[![Une variété de Tables, vues et procédures stockées ont été ajoutées à la base de données](creating-the-membership-schema-in-sql-server-cs/_static/image29.png)](creating-the-membership-schema-in-sql-server-cs/_static/image28.png)

**La figure 10**: une variété de Tables, vues et stockées procédures ont été ajoutés à la base de données ([cliquez pour afficher l’image en taille réelle](creating-the-membership-schema-in-sql-server-cs/_static/image30.png))


> [!NOTE]
> Le `aspnet_regsql.exe` interface utilisateur graphique de l’outil installe le schéma de services d’application dans son intégralité. Mais lors de l’exécution `aspnet_regsql.exe` à partir de la ligne de commande, vous pouvez spécifier les composants à installer (ou supprimer) des services quelle application particulière. Par conséquent, si vous souhaitez ajouter uniquement les tables, vues et procédures nécessaires pour la `SqlMembershipProvider` et `SqlRoleProvider` fournisseurs, exécutez `aspnet_regsql.exe` à partir de la ligne de commande. Ou bien, vous pouvez exécuter manuellement approprié sous-ensemble de T-SQL create scripts utilisés par `aspnet_regsql.exe`. Ces scripts se trouvent dans le `WINDIR%\Microsoft.Net\Framework\v2.0.50727\` dossier avec des noms comme `InstallCommon.sql`,`InstallMembership.sql`,`InstallRoles.sql`, `InstallProfile.sql`,`InstallSqlState.sql`, et ainsi de suite.


À ce stade, nous avons créé les objets de base de données requis par le `SqlMembershipProvider`. Toutefois, nous devons encore indiquer à l’infrastructure d’appartenance qu’il doit utiliser le `SqlMembershipProvider` (et, par exemple, le `ActiveDirectoryMembershipProvider`) et que le `SqlMembershipProvider` doit utiliser le `SecurityTutorials` base de données. Nous allons examiner comment spécifier le fournisseur à utiliser et comment personnaliser les paramètres du fournisseur sélectionné à l’étape 4. Mais tout d’abord, examinons un plus approfondie les objets de base de données qui viennent d’être créés.

## <a name="step-3-a-look-at-the-schemas-core-tables"></a>Étape 3 : Examiner base Tables du schéma

Lorsque vous travaillez avec les infrastructures d’appartenance et les rôles dans une application ASP.NET, les détails d’implémentation sont encapsulés par le fournisseur. Dans les futures les didacticiels nous communiquent avec ces infrastructures via le .NET Framework `Membership` et `Roles` classes. Lorsque vous utilisez ces API principales nous n’avez pas besoin de nous-mêmes concernent avec les détails de bas niveau, tels que les requêtes sont exécutées ou les tables sont modifiées par le `SqlMembershipProvider` et `SqlRoleProvider`.

Compte tenu de cela, nous pourrions utiliser en toute confiance les infrastructures d’appartenance et de rôles sans avoir exploré le schéma de base de données créée à l’étape 2. Toutefois, lors de la création de tables pour stocker les données d’application, nous pouvons devons créer des entités qui se rapportent aux utilisateurs ou rôles. Vous pour devez avoir une connaissance de la `SqlMembershipProvider` et `SqlRoleProvider` schémas lors de l’établissement étrangère clé contraintes entre les tables de données d’application et les tables créées à l’étape 2. En outre, dans certains cas rares nous pouvons doivent interagir avec l’utilisateur et rôle stocke directement au niveau de la base de données (plutôt que via le `Membership` ou `Roles` classes).

### <a name="partitioning-the-user-store-into-applications"></a>Partitionnement du magasin de l’utilisateur dans des Applications

Les infrastructures d’appartenance et les rôles soient conçus pour un seul magasin de l’utilisateur et le rôle peut être partagé entre différentes applications. Une application ASP.NET qui utilise les infrastructures d’appartenance ou rôles doit spécifier quelle partition d’application à utiliser. En bref, plusieurs applications web peuvent utiliser les même utilisateur et le rôle des magasins. Figure 11 illustre l’utilisateur et le rôle des magasins qui sont répartis dans trois applications : HRSite, CustomerSite et SalesSite. Ces applications de trois web ont leurs propres utilisateurs uniques et les rôles, mais ils tous les physiquement stockent leurs informations de compte et le rôle d’utilisateur dans les mêmes tables de base de données.


[![Comptes d’utilisateur peuvent être partitionnées entre plusieurs Applications](creating-the-membership-schema-in-sql-server-cs/_static/image32.png)](creating-the-membership-schema-in-sql-server-cs/_static/image31.png)

**Figure 11**: comptes peuvent être partitionnées entre plusieurs Applications utilisateur ([cliquez pour afficher l’image en taille réelle](creating-the-membership-schema-in-sql-server-cs/_static/image33.png))


Le `aspnet_Applications` table est ce que définit ces partitions. Chaque application qui utilise la base de données pour stocker les informations de compte d’utilisateur est représentée par une ligne dans cette table. Le `aspnet_Applications` table comporte quatre colonnes : `ApplicationId`, `ApplicationName`, `LoweredApplicationName`, et `Description`. `ApplicationId`est de type [ `uniqueidentifier` ](https://msdn.microsoft.com/library/ms187942.aspx) et est la clé primaire de la table ; `ApplicationName` fournit un nom convivial unique pour chaque application.

Les autres tables l’appartenance et les rôles liés à un lien vers le `ApplicationId` champ `aspnet_Applications`. Par exemple, le `aspnet_Users` table, qui contient un enregistrement pour chaque compte d’utilisateur, a une `ApplicationId` champ de clé étrangère ; ditto pour le `aspnet_Roles` table. Le `ApplicationId` champ dans ces tables spécifie la partition d’applications le compte d’utilisateur ou le rôle auquel appartient.

### <a name="storing-user-account-information"></a>Stockage des informations de compte d’utilisateur

Informations de compte d’utilisateur sont hébergées dans deux tables : `aspnet_Users` et `aspnet_Membership`. Le `aspnet_Users` table contient des champs qui contiennent les informations de compte d’utilisateur essentielles. Les trois colonnes les plus pertinentes sont :

- `UserId`
- `UserName`
- `ApplicationId`

`UserId`est la clé primaire (et de type `uniqueidentifier`). `UserName`est de type `nvarchar(256)` et, en même temps que le mot de passe, informations d’identification de l’utilisateur. (Un mot de passe est stocké dans le `aspnet_Membership` table.) `ApplicationId` le compte d’utilisateur est liée à une application particulière dans `aspnet_Applications`. Il est un composite [ `UNIQUE` contrainte](https://msdn.microsoft.com/library/ms191166.aspx) sur la `UserName` et `ApplicationId` colonnes. Cela garantit que dans une application donnée, chaque nom d’utilisateur est unique, mais il permet la même `UserName` à utiliser dans différentes applications.

Le `aspnet_Membership` table inclut les informations de compte d’utilisateur supplémentaires, telles que le mot de passe, adresse de messagerie, la dernière date de connexion et heure et ainsi de suite. Il existe une correspondance univoque entre les enregistrements dans la `aspnet_Users` et `aspnet_Membership` tables. Cette relation est assurée par le `UserId` champ `aspnet_Membership`, qui sert de clé primaire de la table. Comme le `aspnet_Users` table, `aspnet_Membership` inclut un `ApplicationId` champ qui lie ces informations pour une partition d’application particulière.

### <a name="securing-passwords"></a>Sécurisation des mots de passe

Les informations de mot de passe sont stockées dans le `aspnet_Membership` table. La `SqlMembershipProvider` autorise les mots de passe à stocker dans la base de données à l’aide d’une des trois méthodes suivantes :

- **Désactivez** -le mot de passe est stocké dans la base de données en tant que texte brut. Je vous déconseillons d’utiliser cette option. Si la base de données est compromise - il être par un pirate informatique qui recherche une porte dérobée ou un employé mécontent qui dispose d’un accès de base de données - informations d’identification de chaque utilisateur unique sont pour la prise.
- **Haché** -les mots de passe sont hachés à l’aide d’un algorithme de hachage unidirectionnel et une valeur salt générée de façon aléatoire. Cette valeur de hachage (ainsi que la valeur salt) est stockée dans la base de données.
- **Chiffré** -une version chiffrée du mot de passe est stockée dans la base de données.

La technique du stockage de mot de passe utilisée varie selon le `SqlMembershipProvider` paramètres spécifiés dans `Web.config`. Nous allons nous intéresser à la personnalisation de le `SqlMembershipProvider` paramètres à l’étape 4. Le comportement par défaut est de stocker le hachage du mot de passe.

Les colonnes chargés de stocker le mot de passe sont `Password`, `PasswordFormat`, et `PasswordSalt`. `PasswordFormat`est un champ de type `int` dont la valeur indique la technique utilisée pour stocker le mot de passe : 0 pour effacer ; 1 pour Hashed ; 2 pour le chiffrement. `PasswordSalt`est affecté à une chaîne générée de façon aléatoire, quelle que soit la technique du stockage de mot de passe utilisée ; la valeur de `PasswordSalt` est utilisée uniquement lors du calcul du hachage du mot de passe. Enfin, la `Password` colonne contient les données de mot de passe réel, soit sur le mot de passe en texte brut, le hachage du mot de passe ou le mot de passe chiffré.

Le tableau 1 illustre ce que ces trois colonnes peut se présenter comme pour les différentes techniques de stockage pour stocker le mot de passe MySecret ! .

| **Technique de stockage&lt;\_o3a\_p /&gt;** | **Mot de passe&lt;\_o3a\_p /&gt;** | **PasswordFormat&lt;\_o3a\_p /&gt;** | **PasswordSalt&lt;\_o3a\_p /&gt;** |
| --- | --- | --- | --- |
| Effacer | MySecret! | 0 | tTnkPlesqissc2y2SMEygA== |
| Haché | 2oXm6sZHWbTHFgjgkGQsc2Ec9ZM= | 1 | wFgjUfhdUFOCKQiI61vtiQ== |
| Chiffré | 62RZgDvhxykkqsMchZ0Yly7HS6onhpaoCYaRxV8g0F4CW56OXUU3e7Inza9j9BKp | 2 | LSRzhGS/aa/oqAXGLHJNBw== |

**Tableau 1**: exemples de valeurs pour les champs liés au mot de passe lors du stockage du mot de passe MySecret !

> [!NOTE]
> Le chiffrement particulier ou un algorithme de hachage utilisé par le `SqlMembershipProvider` est déterminée par les paramètres dans le `<machineKey>` élément. Nous avons abordée cet élément de configuration à l’étape 3 de la <a id="Tutorial3"> </a> [ *Configuration de l’authentification de formulaires et les rubriques avancées* ](../introduction/forms-authentication-configuration-and-advanced-topics-cs.md) didacticiel.


### <a name="storing-roles-and-role-associations"></a>Le stockage des rôles et des Associations de rôle

L’infrastructure de rôles permet aux développeurs de définir un ensemble de rôles et de spécifier quels utilisateurs appartiennent à quels rôles. Ces informations sont capturées dans la base de données dans les deux tables : `aspnet_Roles` et `aspnet_UsersInRoles`. Chaque enregistrement dans le `aspnet_Roles` table représente un rôle pour une application particulière. Tout comme le `aspnet_Users` table, le `aspnet_Roles` comporte trois colonnes pertinentes à notre discussion :

- `RoleId`
- `RoleName`
- `ApplicationId`

`RoleId`est la clé primaire (et de type `uniqueidentifier`). `RoleName` est de type `nvarchar(256)`. Et `ApplicationId` le compte d’utilisateur est liée à une application particulière dans `aspnet_Applications`. Il est un composite `UNIQUE` contrainte sur la `RoleName` et `ApplicationId` colonnes, garantissant que chaque nom de rôle est unique dans une application donnée.

Le `aspnet_UsersInRoles` table sert un mappage entre les utilisateurs et les rôles. Il y a uniquement deux colonnes - `UserId` et `RoleId` - et elles constituent une clé primaire composite.

## <a name="step-4-specifying-the-provider-and-customizing-its-settings"></a>Étape 4 : Spécification du fournisseur et la personnalisation des paramètres de son

Toutes les infrastructures qui prennent en charge le modèle de fournisseur - telles que les infrastructures d’appartenance et les rôles - ne disposent pas de détails d’implémentation eux-mêmes et à la place déléguer cette responsabilité à une classe de fournisseur. Dans le cas de l’infrastructure d’appartenance, la `Membership` classe définit l’API de gestion des comptes d’utilisateur, mais il n’interagit pas directement avec n’importe quel magasin de l’utilisateur. Au lieu de cela, le `Membership` rassemblerez de méthodes de la classe à la demande pour le fournisseur configuré -, nous utiliserons le `SqlMembershipProvider`. Lorsque nous appeler une des méthodes dans le `Membership` (classe), comment le framework d’appartenance que pour déléguer l’appel à la `SqlMembershipProvider`?

Le `Membership` classe a un [ `Providers` propriété](https://msdn.microsoft.com/library/system.web.security.membership.providers.aspx) qui contient une référence à toutes les classes de fournisseur enregistré disponibles pour une utilisation par l’infrastructure d’appartenance. Chaque fournisseur enregistré a un nom associé et un type. Le nom offre un moyen convivial pour faire référence à un fournisseur particulier dans le `Providers` collection, tandis que le type identifie la classe de fournisseur. En outre, chaque fournisseur enregistré peut inclure des paramètres de configuration. Incluent des paramètres de configuration de l’infrastructure d’appartenance `passwordFormat` et `requiresUniqueEmail`, entre autres. Reportez-vous au tableau 2 pour obtenir la liste complète des paramètres de configuration utilisés par le `SqlMembershipProvider`.

Le `Providers` contenu de la propriété est spécifiés via les paramètres de configuration de l’application web. Par défaut, toutes les applications web ont un fournisseur nommé `AspNetSqlMembershipProvider` de type `SqlMembershipProvider`. Ce fournisseur d’appartenances par défaut est enregistré dans `machine.config` (situé dans `%WINDIR%\Microsoft.Net\Framework\v2.0.50727\CONFIG)`:

[!code-xml[Main](creating-the-membership-schema-in-sql-server-cs/samples/sample1.xml)]

En tant que le balisage ci-dessus, le [ `<membership>` élément](https://msdn.microsoft.com/library/1b9hw62f.aspx) définit les paramètres de configuration de l’infrastructure d’appartenance lors de la [ `<providers>` élément enfant](https://msdn.microsoft.com/library/6d4936ht.aspx) spécifie inscrit fournisseurs. Les fournisseurs peuvent être ajoutés ou supprimés à l’aide de la [ `<add>` ](https://msdn.microsoft.com/library/whae3t94.aspx) ou [ `<remove>` ](https://msdn.microsoft.com/library/aykw9a6d.aspx) éléments ; Utilisez la [ `<clear>` ](https://msdn.microsoft.com/library/t062y6yc.aspx) élément à supprimer tous les actuellement fournisseurs inscrits. En tant que le balisage ci-dessus, `machine.config` ajoute un fournisseur nommé `AspNetSqlMembershipProvider` de type `SqlMembershipProvider`.

Outre la `name` et `type` attributs, la `<add>` élément contient des attributs qui définissent les valeurs de configuration des paramètres différents. Le tableau 2 répertorie les `SqlMembershipProvider`-paramètres de configuration spécifiques, ainsi qu’une description de chacune.

> [!NOTE]
> Toutes les valeurs par défaut indiquées dans le tableau 2 font référence aux valeurs par défaut définies dans le `SqlMembershipProvider` classe. Notez que not tous les paramètres de configuration dans `AspNetSqlMembershipProvider` correspondent aux valeurs par défaut de la `SqlMembershipProvider` classe. Par exemple, si non spécifié dans un fournisseur d’appartenances, le `requiresUniqueEmail` définissant les valeurs par défaut sur true. Toutefois, le `AspNetSqlMembershipProvider` se substitue à cette valeur par défaut en spécifiant explicitement une valeur de `false`.


| **Paramètre&lt;\_o3a\_p /&gt;** | **Description&lt;\_o3a\_p /&gt;** |
| --- | --- |
| `ApplicationName` | Rappel qui permet de l’infrastructure de l’appartenance d’un magasin d’utilisateur unique d’être partitionnées entre plusieurs applications. Ce paramètre indique le nom de la partition d’applications utilisé par le fournisseur d’appartenances. Si cette valeur n'est pas explicitement spécifiée, elle est définie lors de l’exécution, la valeur du chemin d’accès virtuel racine de l’application. |
| `commandTimeout` | Spécifie la valeur de délai d’attente de commande SQL (en secondes). La valeur par défaut est 30. |
| `connectionStringName` | Le nom de la chaîne de connexion dans le `<connectionStrings>` élément à utiliser pour se connecter à la base de données utilisateur. Cette valeur est obligatoire. |
| `description` | Fournit une description conviviale du fournisseur inscrit. |
| `enablePasswordRetrieval` | Spécifie si les utilisateurs peuvent récupérer leur mot de passe oublié. La valeur par défaut est `false`. |
| `enablePasswordReset` | Indique si les utilisateurs sont autorisés à réinitialiser leur mot de passe. La valeur par défaut est `true`. |
| `maxInvalidPasswordAttempts` | Le nombre maximal de tentatives de connexion ayant échoué qui peuvent se produire pour un utilisateur donné spécifiées `passwordAttemptWindow` avant que l’utilisateur est verrouillé. La valeur par défaut est 5. |
| `minRequiredNonalphanumericCharacters` | Le nombre minimal de caractères non alphanumériques qui doivent apparaître dans un mot de passe. Cette valeur doit être comprise entre 0 et 128 ; la valeur par défaut est 1. |
| `minRequiredPasswordLength` | Le nombre minimal de caractères requis dans un mot de passe. Cette valeur doit être comprise entre 0 et 128 ; la valeur par défaut est 7. |
| `name` | Le nom du fournisseur inscrit. Cette valeur est obligatoire. |
| `passwordAttemptWindow` | Le nombre de minutes pendant lesquelles Échec de connexion tentatives sont suivies. Si un utilisateur fournit des informations d’identification de connexion non valide `maxInvalidPasswordAttempts` reprises dans cette spécifié fenêtre, ils sont verrouillés. La valeur par défaut est 10. |
| `PasswordFormat` | Le format de stockage de mot de passe : `Clear`, `Hashed`, ou `Encrypted`. La valeur par défaut est `Hashed`. |
| `passwordStrengthRegularExpression` | Si spécifié, cette expression régulière est utilisée pour évaluer la force de mot de passe de l’utilisateur sélectionné lors de la création d’un nouveau compte ou lors de la modification de leur mot de passe. La valeur par défaut est une chaîne vide. |
| `requiresQuestionAndAnswer` | Spécifie si un utilisateur doit répondre à sa question de sécurité lors de la récupération ou de réinitialiser son mot de passe. La valeur par défaut est `true`. |
| `requiresUniqueEmail` | Indique si tous les comptes d’utilisateur dans une partition d’applications donné doivent avoir une adresse de messagerie unique. La valeur par défaut est `true`. |
| `type` | Spécifie le type du fournisseur. Cette valeur est obligatoire. |

**Tableau 2**: l’appartenance et `SqlMembershipProvider` les paramètres de Configuration

En plus de `AspNetSqlMembershipProvider`, autres fournisseurs d’appartenances peuvent être enregistrés sur une base de l’application en application en ajoutant un balisage semblable à la `Web.config` fichier.

> [!NOTE]
> L’infrastructure de rôles fonctionne à peu près la même manière : il existe un fournisseur de rôle inscrit par défaut dans `machine.config` et les fournisseurs inscrits peuvent être personnalisés sur une base de l’application en application dans `Web.config`. Nous allons examiner l’infrastructure de rôles et de son balisage de la configuration en détail dans un didacticiel futures.


### <a name="customizing-thesqlmembershipprovidersettings"></a>Personnalisation de le`SqlMembershipProvider`paramètres

La valeur par défaut `SqlMembershipProvider` (`AspNetSqlMembershipProvider`) a son `connectionStringName` attribut la valeur `LocalSqlServer`. Comme le `AspNetSqlMembershipProvider` fournisseur, le nom de chaîne de connexion `LocalSqlServer` est défini dans `machine.config`.

[!code-xml[Main](creating-the-membership-schema-in-sql-server-cs/samples/sample2.xml)]

Comme vous pouvez le voir, cette chaîne de connexion définit la base de données SQL 2005 Express Edition | DataDirectory|aspnetdb.mdf. La chaîne | DataDirectory | est traduite en cours d’exécution pour pointer vers le `~/App_Data/` active, pour que le chemin d’accès de la base de données | DataDirectory|aspnetdb.mdf » se traduit par `~/App_Data` / `aspnet.mdf`.

Si nous n’avons pas spécifié toutes les informations de fournisseur d’appartenances dans notre application `Web.config` fichier, l’application utilise le fournisseur d’appartenances par défaut enregistré, `AspNetSqlMembershipProvider`. Si le `~/App_Data/aspnet.mdf` base de données n’existe pas, le runtime ASP.NET crée automatiquement et ajoutez le schéma de services d’application. Toutefois, nous ne souhaitez pas utiliser le `aspnet.mdf` base de données ; au lieu de cela, nous souhaitons utiliser le `SecurityTutorials.mdf` base de données créées à l’étape 2. Cette modification peut être accomplie de deux manières :

- **Spécifiez une valeur pour le ***`LocalSqlServer`*** nom de chaîne de connexion dans ***`Web.config`***.** En remplaçant le `LocalSqlServer` valeur du nom de chaîne de connexion `Web.config`, nous pouvons utiliser le fournisseur d’appartenances par défaut enregistré (`AspNetSqlMembershipProvider`) et qu’il fonctionne correctement avec le `SecurityTutorials.mdf` base de données. Cette approche consiste si vous êtes avec les paramètres de configuration spécifiés par `AspNetSqlMembershipProvider`. Pour plus d’informations sur cette technique, consultez [Scott Guthrie](https://weblogs.asp.net/scottgu/)du billet de blog, [configurer les Services d’Application ASP.NET 2.0 pour utiliser SQL Server 2000 ou SQL Server 2005](https://weblogs.asp.net/scottgu/archive/2005/08/25/423703.aspx).
- **Ajouter un nouveau fournisseur inscrit de type ***`SqlMembershipProvider`*** et configurer ses ***`connectionStringName`*** paramètre pour pointer vers le ***`SecurityTutorials.mdf`*** base de données.** Cette approche est utile dans les scénarios où vous souhaitez personnaliser les autres propriétés de configuration en plus de la chaîne de connexion de base de données. Dans mes propres projets, j’utilise toujours cette approche en raison de sa flexibilité et une meilleure lisibilité.

Avant que nous pouvons ajouter un nouveau fournisseur inscrit qui fait référence à la `SecurityTutorials.mdf` base de données, vous devez d’abord ajouter une valeur de chaîne de connexion approprié dans le `<connectionStrings>` section `Web.config`. Le balisage suivant ajoute une nouvelle chaîne de connexion nommée `SecurityTutorialsConnectionString` qui fait référence à SQL Server 2005 Express Edition `SecurityTutorials.mdf` de la base de données dans le `App_Data` dossier.

[!code-xml[Main](creating-the-membership-schema-in-sql-server-cs/samples/sample3.xml)]

> [!NOTE]
> Si vous utilisez un fichier de l’autre base de données, mettre à jour la chaîne de connexion en fonction des besoins. Pour plus d’informations sur la formation de la chaîne de connexion correct, reportez-vous à [ConnectionStrings.com](http://www.connectionstrings.com/).

Ensuite, ajoutez le balisage suivant de configuration d’appartenance pour le `Web.config` fichier. Ce balisage enregistre un nouveau fournisseur nommé `SecurityTutorialsSqlMembershipProvider`.

[!code-xml[Main](creating-the-membership-schema-in-sql-server-cs/samples/sample4.xml)]

En plus de l’enregistrement le `SecurityTutorialsSqlMembershipProvider` fournisseur, le balisage ci-dessus définit le `SecurityTutorialsSqlMembershipProvider` en tant que le fournisseur par défaut (via la `defaultProvider` d’attribut dans le `<membership>` élément). Rappelez-vous que l’infrastructure d’appartenance peut avoir plusieurs fournisseurs enregistrés. Étant donné que `AspNetSqlMembershipProvider` est inscrit en tant que premier fournisseur dans `machine.config`, il sert de fournisseur par défaut, sauf si nous indiquer dans le cas contraire.

Actuellement, notre application a deux fournisseurs enregistrés : `AspNetSqlMembershipProvider` et `SecurityTutorialsSqlMembershipProvider`. Toutefois, avant d’inscrire le `SecurityTutorialsSqlMembershipProvider` fournisseur nous pourrions avoir supprimé toutes les précédemment inscrit les fournisseurs en ajoutant un [ `<clear />` élément](https://msdn.microsoft.com/library/t062y6yc.aspx) immédiatement avant notre `<add>` élément. Cela serait effacer la `AspNetSqlMembershipProvider` à partir de la liste des fournisseurs enregistrés, ce qui signifie que le `SecurityTutorialsSqlMembershipProvider` est le seul fournisseur d’appartenances inscrit. Si nous avons utilisé cette approche, nous n’a pas besoin de marquer le `SecurityTutorialsSqlMembershipProvider` en tant que le fournisseur par défaut, car il est le fournisseur d’appartenances inscrit uniquement. Pour plus d’informations sur l’utilisation de `<clear />`, consultez [Using `<clear />` lorsque les fournisseurs ajout](https://weblogs.asp.net/scottgu/archive/2006/11/20/common-gotcha-don-t-forget-to-clear-when-adding-providers.aspx).

Notez que la `SecurityTutorialsSqlMembershipProvider`de `connectionStringName` définition de références le juste ajoutés `SecurityTutorialsConnectionString` nom de chaîne de connexion et que son `applicationName` paramètre a été défini sur une valeur de SecurityTutorials. En outre, le `requiresUniqueEmail` a été défini sur `true`. Toutes les autres options de configuration sont identiques aux valeurs dans `AspNetSqlMembershipProvider`. N’hésitez pas à apporter des modifications de configuration ici, si vous le souhaitez. Par exemple, vous pourriez renforcer la force du mot de passe en demandant à deux caractères non alphanumériques au lieu d’un, ou en augmentant la longueur de mot de passe à huit caractères au lieu de sept.

> [!NOTE]
> Rappel qui permet de l’infrastructure de l’appartenance d’un magasin d’utilisateur unique d’être partitionnées entre plusieurs applications. Le fournisseur d’appartenances `applicationName` paramètre indique quelle application, le fournisseur utilise lorsque vous travaillez avec le magasin de l’utilisateur. Il est important de définir explicitement une valeur pour le `applicationName` paramètre de configuration, car si le `applicationName` n’est pas défini explicitement, il est assigné à un chemin d’accès virtuel racine de l’application web lors de l’exécution. Cette méthode fonctionne parfaitement tant que chemin d’accès virtuel racine de l’application ne change pas, mais si vous déplacez l’application vers un autre chemin d’accès, le `applicationName` paramètre est modifiées. Dans ce cas, le fournisseur d’appartenances s’exécutera avec une partition d’application autre que celui précédemment utilisé. Comptes d’utilisateur créés avant le déplacement se trouvera dans une partition d’application et les utilisateurs ne seront plus en mesure de se connecter au site. Pour obtenir une explication plus approfondie à ce sujet, consultez [toujours définir la `applicationName` propriété lors de la configuration ASP.NET 2.0 de l’appartenance et les autres fournisseurs](https://weblogs.asp.net/scottgu/443634).


## <a name="summary"></a>Récapitulatif

À ce stade, nous avons une base de données avec les services d’application configuré (`SecurityTutorials.mdf`) et avez configuré notre application web afin que l’infrastructure d’appartenance utilise le `SecurityTutorialsSqlMembershipProvider` fournisseur nous enregistrés. Ce fournisseur enregistré est de type `SqlMembershipProvider` et a son `connectionStringName` défini sur la chaîne de connexion approprié (`SecurityTutorialsConnectionString`) et son `applicationName` valeur explicitement définie.

Nous sommes maintenant prêts à utiliser l’infrastructure de l’appartenance à partir de notre application. Dans l’étape suivante du didacticiel, nous allons examiner comment créer de nouveaux comptes d’utilisateur. Après que nous allons examiner l’authentification des utilisateurs, l’autorisation d’utilisateur et le stockage des informations supplémentaires relatives à l’utilisateur dans la base de données.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Toujours défini le `applicationName` propriété lors de la configuration d’ASP.NET 2.0 l’appartenance et autres fournisseurs](https://weblogs.asp.net/scottgu/443634)
- [Configuration d’ASP.NET 2.0 des Services d’Application pour utiliser SQL Server 2000 ou SQL Server 2005](https://weblogs.asp.net/scottgu/archive/2005/08/25/423703.aspx)
- [Télécharger SQL Server Management Studio Express Edition](https://www.microsoft.com/downloads/details.aspx?FamilyId=C243A5AE-4BD1-4E3D-94B8-5A0F62BF7796&amp;displaylang=en)
- [Examen de ASP.NET 2.0 s appartenance, rôles et profil](http://aspnet.4guysfromrolla.com/articles/120705-1.aspx)
- [Le `<add>` , élément pour les fournisseurs pour l’appartenance](https://msdn.microsoft.com/library/whae3t94.aspx)
- [Le `<membership>` élément](https://msdn.microsoft.com/library/1b9hw62f.aspx)
- [Le `<providers>` élément pour l’appartenance](https://msdn.microsoft.com/library/6d4936ht.aspx)
- [À l’aide de `<clear />` fournisseurs lors de l’ajout](https://weblogs.asp.net/scottgu/archive/2006/11/20/common-gotcha-don-t-forget-to-clear-when-adding-providers.aspx)
- [Travailler directement avec le`SqlMembershipProvider`](http://aspnet.4guysfromrolla.com/articles/091207-1.aspx)

### <a name="video-training-on-topics-contained-in-this-tutorial"></a>Formations vidéo sur les rubriques contenues dans ce didacticiel

- [Présentation des appartenances d’ASP.NET](../../../videos/authentication/understanding-aspnet-memberships.md)
- [Configuration de SQL pour l’utilisation de schémas d’appartenance](../../../videos/authentication/configuring-sql-to-work-with-membership-schemas.md)
- [Modification des paramètres d’appartenance dans le schéma d’appartenance par défaut](../../../videos/authentication/changing-membership-settings-in-the-default-membership-schema.md)

### <a name="about-the-author"></a>À propos de l’auteur

Scott Mitchell, auteur de plusieurs livres sur ASP/ASP.NET et créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est  *[SAM animer vous-même ASP.NET 2.0 des dernières 24 heures](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco)*. Scott peut être atteint à [ mitchell@4guysfromrolla.com ](mailto:mitchell@4guysfromrolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Alicja Maziarz. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4guysfromrolla.com).

>[!div class="step-by-step"]
[Next](creating-user-accounts-cs.md)

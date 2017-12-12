---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/configuring-a-website-that-uses-application-services-vb
title: "Configuration d’un site Web qui utilise les Services d’Application (VB) | Documents Microsoft"
author: rick-anderson
description: "Version d’ASP.NET 2.0 a introduit une série de services d’application, qui font partie du .NET Framework et répondre à une suite de bloc de construction de services qui v..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/23/2009
ms.topic: article
ms.assetid: 9c31a42f-d8bb-4c0f-9ccc-597d4f70ac42
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/configuring-a-website-that-uses-application-services-vb
msc.type: authoredcontent
ms.openlocfilehash: 7fb212638765589b998c4eca8265dfeb2910082f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="configuring-a-website-that-uses-application-services-vb"></a>Configuration d’un site Web qui utilise les Services d’Application (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/E/6/F/E6FE3A1F-EE3A-4119-989A-33D1A9F6F6DD/ASPNET_Hosting_Tutorial_09_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/C/3/9/C391A649-B357-4A7B-BAA4-48C96871FEA6/aspnet_tutorial09_AppServicesConfig_vb.pdf)

> Version d’ASP.NET 2.0 a introduit une série de services d’application, qui font partie du .NET Framework et agissent comme une suite de services de bloc de construction que vous pouvez utiliser pour ajouter des fonctionnalités à votre application web. Ce didacticiel explique comment configurer un site Web dans l’environnement de production à utiliser les services d’application et corrige les problèmes courants liés à la gestion des comptes d’utilisateur et les rôles sur l’environnement de production.


## <a name="introduction"></a>Introduction

Version d’ASP.NET 2.0 a introduit une série de *les services d’application*, qui font partie du .NET Framework et répondre à une suite de bloc de construction de services que vous pouvez utiliser pour ajouter des fonctionnalités à votre application web. Les services d’application sont les suivantes :

- **L’appartenance** : une API pour créer et gérer des comptes d’utilisateur.
- **Rôles** : une API pour classer les utilisateurs en groupes.
- **Profil** : une API de stockage du contenu personnalisé, spécifiques à l’utilisateur.
- **Plan du site** : une API permettant de définir une structure logique du site s sous la forme d’une hiérarchie, ce qui peut ensuite être affichée par le biais des contrôles de navigation, comme les menus et les vues miniatures.
- **Personnalisation** : une API de gestion de préférences de personnalisation, le plus souvent utilisées avec [ *WebParts*](https://msdn.microsoft.com/en-us/library/e0s9t4ck.aspx).
- **Surveillance de l’intégrité** : une API pour l’analyse des performances, la sécurité, les erreurs et autres mesures de contrôle d’intégrité système pour une application web en cours d’exécution.
  

Les services d’application API ne sont pas liés à une implémentation spécifique. Au lieu de cela, vous demandez les services d’application à utiliser un particulier *fournisseur*, et ce fournisseur implémente le service à l’aide d’une technologie particulière. Les fournisseurs couramment utilisées pour les applications web basés sur Internet hébergées sur une société d’hébergement web sont les fournisseurs qui utilisent une implémentation de base de données SQL Server. Par exemple, le `SqlMembershipProvider` est un fournisseur pour l’API d’appartenance qui stocke des informations de compte d’utilisateur dans une base de données Microsoft SQL Server.

Les services d’application et les fournisseurs de SQL Server ajoute des défis lors du déploiement de l’application. Pour commencer, les objets de base de données d’application services doivent être créées correctement sur les bases de données à la fois le développement et de production et initialisés de manière appropriée. Il existe également des paramètres de configuration importants qui doivent être apportées.

> [!NOTE]
> Les services d’application API ont été conçues à l’aide de la [ *modèle de fournisseur*](http://aspnet.4guysfromrolla.com/articles/101905-1.aspx), un modèle de conception qui permet d’API s détails d’implémentation doit être fourni lors de l’exécution. Le .NET Framework est fourni avec un nombre de fournisseurs de services d’application qui peut être utilisée, telle que la `SqlMembershipProvider` et `SqlRoleProvider`, qui sont des fournisseurs pour l’appartenance et les API de rôles qui utilisent un serveur SQL Server de la base de données mise en œuvre. Vous pouvez également créer et plug-in un fournisseur personnalisé. En fait, la révisions du livre web application contient déjà un fournisseur personnalisé pour l’API de mappage de Site (`ReviewSiteMapProvider`), qui construit le plan de site à partir des données dans le `Genres` et `Books` les tables dans la base de données.


Ce didacticiel commence par examiner comment j’étendue de l’application web critiques à utiliser l’appartenance et les rôles d’API. Ensuite, il guide de déploiement d’une application web qui utilise les services d’application avec une implémentation de base de données SQL Server et se termine en traitant les problèmes courants liés à la gestion des comptes d’utilisateur et les rôles sur l’environnement de production.

## <a name="updates-to-the-book-reviews-application"></a>Mises à jour de l’annuaire d’Application évaluations

Dernières didacticiels de que l’application web passe en revue du livre a été mis à jour à partir d’un site Web statique à une application web dynamique, pilotés par les données se terminer avec un ensemble de pages d’administration pour la gestion des genres et révisions. Toutefois, cette section de l’administration n’est actuellement pas protégée : tout utilisateur qui connaît (ou devine) l’URL de la page administration permettre s’introduire dans et créer, modifier ou supprimer les révisions sur notre site. Une méthode courante pour protéger certaines parties d’un site Web consiste à implémenter des comptes d’utilisateur et ensuite utiliser des règles d’autorisation d’URL pour restreindre l’accès à certains utilisateurs ou les rôles. L’application web critiques disponible pour téléchargement de ce didacticiel prend en charge les rôles et les comptes d’utilisateur. Il dispose d’un seul rôle défini nommé Admin et seuls les utilisateurs de ce rôle peuvent accéder les pages d’administration.

> [!NOTE]
> Vous venez de créer trois comptes d’utilisateur dans l’application web critiques : Scott Jisun et Alice. Tous les utilisateurs de trois ont le même mot de passe : **mot de passe !** Scott et Jisun se trouvent dans le rôle d’administrateur, Alice n’est pas. Les pages de non-administration de site s sont toujours accessibles aux utilisateurs anonymes. Autrement dit, vous n’avez pas besoin pour vous connecter à visiter le site, sauf si vous souhaitez administrer, auquel cas vous devez être connecté en tant qu’utilisateur dans le rôle d’administrateur.


La page maître s d’application critiques a été mis à jour pour inclure une interface utilisateur différente pour les utilisateurs authentifiés et anonymes. Si un utilisateur anonyme visite le site, elle voit un lien de connexion dans le coin supérieur droit. Un utilisateur authentifié voit le message « Bienvenue, *nom d’utilisateur*! » et un lien pour vous déconnecter. Il existe également une page de connexion de s (`~/Login.aspx`), qui contient un contrôle de connexion Web qui fournit l’interface utilisateur et la logique d’authentification d’un visiteur. Seuls les administrateurs peuvent créer de nouveaux comptes. (Il existe des pages pour créer et gérer des comptes d’utilisateur dans le `~/Admin` dossier.)

### <a name="configuring-the-membership-and-roles-apis"></a>Configuration de l’appartenance et les rôles API

L’application web critiques utilise l’appartenance et les rôles d’API pour prendre en charge les comptes d’utilisateur et pour regrouper les utilisateurs dans des rôles (à savoir, le rôle d’administrateur). Le `SqlMembershipProvider` et `SqlRoleProvider` des classes de fournisseur sont utilisées, car nous souhaitons stocker les informations de compte et de rôle dans une base de données SQL Server.

> [!NOTE]
> Ce didacticiel n’est pas destiné à être un examen approfondi au niveau de la configuration d’une application web pour prendre en charge l’appartenance et les rôles d’API. Pour une présentation approfondie sur ces API et les étapes à suivre pour configurer un site Web pour les utiliser, lisez mon [ *didacticiels de sécurité du site Web*](../../older-versions-security/introduction/security-basics-and-asp-net-support-cs.md).


Pour utiliser les services d’application avec une base de données SQL Server, vous devez tout d’abord ajouter l’objets de base de données utilisés par ces fournisseurs de la base de données où vous souhaitez que le compte d’utilisateur et les informations stockées sur les rôles. Ces objets de base de données requis incluent de nombreuses tables, vues et procédures stockées. Sauf indication contraire, le `SqlMembershipProvider` et `SqlRoleProvider` classes de fournisseur utilisent une base de données SQL Server Express Edition `ASPNETDB` situé dans le s application `App_Data` dossier ; si une base de données n’existe pas, il est automatiquement créé avec les objets de base de données nécessaires par ces fournisseurs lors de l’exécution.

Il est possible, et conviennent parfaitement créer des objets de base de données dans la même base de données où sont stockées les données spécifiques à l’application du site Web services de l’application. Le .NET Framework est fourni avec un outil nommé `aspnet_regsql.exe` qui installe les objets de base de données sur une base de données spécifiée. J’ai devenu avance et cet outil permet d’ajouter ces objets à la `Reviews.mdf` de la base de données dans le `App_Data` dossier (base de données de développement). Nous allons voir comment utiliser cet outil plus loin dans ce didacticiel lorsque nous ajouter ces objets à la base de données de production.

Si vous ajoutez l’application des services autres que les objets de base de données à une base de données `ASPNETDB` vous devez personnaliser le `SqlMembershipProvider` et `SqlRoleProvider` les classes de fournisseur de configurations afin qu’ils utilisent la base de données approprié. Pour personnaliser le fournisseur d’appartenances ajouter un [  *&lt;l’appartenance&gt; élément* ](https://msdn.microsoft.com/en-us/library/1b9hw62f.aspx) au sein de la `<system.web>` section `Web.config`; utiliser le [  *&lt;roleManager&gt; élément* ](https://msdn.microsoft.com/en-us/library/ms164660.aspx) pour configurer le fournisseur de rôles. L’extrait de code suivant provient de la s d’application critiques `Web.config` et affiche les paramètres de configuration pour l’appartenance et les rôles d’API. Notez que les deux inscrire un nouveau fournisseur - `ReviewMembership` et `ReviewRole` -qui utilisent la `SqlMembershipProvider` et `SqlRoleProvider` fournisseurs, respectivement.

[!code-xml[Main](configuring-a-website-that-uses-application-services-vb/samples/sample1.xml)]

Le `Web.config` fichier s `<authentication>` élément a été également configuré pour prendre en charge l’authentification basée sur des formulaires.
  

[!code-xml[Main](configuring-a-website-that-uses-application-services-vb/samples/sample2.xml)]

### <a name="limiting-access-to-the-administration-pages"></a>Limiter l’accès aux Pages d’Administration

ASP.NET facilite l’accorder ou refuser l’accès à un fichier particulier ou un dossier par utilisateur ou par rôle via son *l’autorisation d’URL* fonctionnalité. (Nous l’avons vu brièvement l’autorisation d’URL dans le *principales différences entre IIS et le serveur de développement ASP.NET* didacticiel et ont montré comment IIS et le serveur de développement ASP.NET appliquent des règles d’autorisation d’URL différemment pour statique par rapport à un contenu dynamique.) Étant donné que vous souhaitez interdire l’accès à la `~/Admin` dossier à l’exception de ces utilisateurs dans le rôle d’administrateur, nous devons ajouter des règles d’autorisation d’URL dans ce dossier. Plus précisément, les règles d’autorisation d’URL devront autoriser les utilisateurs dans le rôle d’administrateur et refuser tous les autres utilisateurs. Cela est accompli en ajoutant un `Web.config` le fichier le `~/Admin` dossier avec le contenu suivant :

[!code-xml[Main](configuring-a-website-that-uses-application-services-vb/samples/sample3.xml)]

Pour plus d’informations sur la fonctionnalité d’autorisation ASP.NET s URL et comment l’utiliser pour écrire des règles d’autorisation pour les utilisateurs et pour les rôles, veillez à lire le [ *d’autorisation basée sur l’utilisateur* ](../../older-versions-security/membership/user-based-authorization-vb.md) et [ *D’autorisation basée sur le rôle* ](../../older-versions-security/roles/role-based-authorization-vb.md) didacticiels à mon [ *didacticiels de sécurité du site Web*](../../older-versions-security/introduction/security-basics-and-asp-net-support-cs.md).

## <a name="deploying-a-web-application-that-uses-application-services"></a>Déploiement d’une Application Web qui utilise les Services d’Application

Lors du déploiement d’un site Web qui utilise les services d’application et un fournisseur qui stocke les informations de services d’application dans une base de données, il est impératif que les objets de base de données requis par les services d’application créés sur la base de données de production. Initialement la base de données de production ne contient pas ces objets, lors du premier déploiement de l’application (ou lorsqu’il est déployé pour la première fois après ont ajouté les services d’application), vous devez prendre des mesures supplémentaires pour obtenir ces objets de base de données requises sur le base de données de production.

Un autre défi peut se produire lors du déploiement d’un site Web qui utilise les services d’application si vous souhaitez répliquer les comptes d’utilisateur créés dans l’environnement de développement à l’environnement de production. Selon la configuration de l’appartenance et les rôles, il est possible que, même si vous copiez correctement les comptes d’utilisateurs qui ont été créés dans l’environnement de développement à la base de données de production, ces utilisateurs ne peuvent pas se connecter à l’application web en production. Nous examiner la cause de ce problème et expliquent comment l’empêcher de se produire.

ASP.NET est fourni avec une bonne [ *outil d’Administration de Site Web (WSAT)* ](https://msdn.microsoft.com/en-us/library/yy40ytx0.aspx) qui peut être lancé à partir de Visual Studio et permet à l’utilisateur compte, rôles, règles d’autorisation et d’être gérés via sur le web interface. Malheureusement, le WSAT fonctionne uniquement pour les sites Web de locales, c'est-à-dire qu’il ne peut pas être utilisé pour gérer à distance des comptes d’utilisateur, les rôles et les règles d’autorisation pour l’application web dans l’environnement de production. Nous allons examiner les différentes façons d’implémenter un comportement semblable aux WSAT à partir de votre site Web de production.

### <a name="adding-the-database-objects-using-aspnetregsqlexe"></a>Ajouter le compte aspnet à l’aide des objets de base de données\_regsql.exe

Le *déploiement d’une base de données* didacticiel vous a montré comment copier les tables et les données à partir de la base de données de développement à la base de données de production, et ces techniques certainement peuvent être utilisés pour copier les objets de base de données de services application à la base de données de production. Une autre option est la `aspnet_regsql.exe` outil, qui ajoute ou supprime les objets de base de données d’application services à partir d’une base de données.

> [!NOTE]
> Le `aspnet_regsql.exe` outil crée les objets de base de données sur une base de données spécifiée. Il ne migre pas les données dans les objets de base de données à partir de la base de données de développement à la base de données de production. Si vous voulez copier les informations de compte et le rôle d’utilisateur dans la base de données de développement à la base de données de production utiliser les techniques décrites dans le *déploiement d’une base de données* didacticiel.


S permettent d’examiner comment ajouter les objets de base de données à la base de données de production à l’aide du `aspnet_regsql.exe` outil. Commencez par ouvrir l’Explorateur Windows et accédez au répertoire .NET Framework version 2.0 sur votre ordinateur, %WINDIR%\ Microsoft.NET\Framework\v2.0.50727. Il vous devez rechercher la `aspnet_regsql.exe` outil. Cet outil peut être utilisé à partir de la ligne de commande, mais il inclut également une interface utilisateur graphique ; Double-cliquez sur le `aspnet_regsql.exe` fichier à lancer son composant de graphique.

L’outil démarre en affichant un écran de démarrage expliquant son objectif. Cliquez sur Suivant pour passer à l’écran « Sélectionnez une Option d’installation », qui est affichée dans la Figure 1. À ce stade, vous pouvez choisir d’ajouter les services d’application les objets de base de données ou les supprimer à partir d’une base de données. Étant donné que nous souhaitons ajouter ces objets à la base de données de production, sélectionnez l’option « Configurer SQL Server pour les services d’application » et cliquez sur Suivant.


[![Choisissez de configurer SQL Server pour les Services d’Application](configuring-a-website-that-uses-application-services-vb/_static/image2.jpg)](configuring-a-website-that-uses-application-services-vb/_static/image1.jpg)

**Figure 1**: choisissez de configurer SQL Server pour les Services d’Application ([cliquez pour afficher l’image en taille réelle](configuring-a-website-that-uses-application-services-vb/_static/image3.jpg))


Dans « Sélectionner le serveur et base de données » les invites pour plus d’informations pour se connecter à la base de données. Entrez le nom de la base de données fournie par votre société d’hébergement web, le serveur de base de données et les informations d’identification de sécurité et cliquez sur Suivant.

> [!NOTE]
> Après avoir entré vos informations d’identification et le serveur de base de données, vous pouvez obtenir une erreur lors de l’extension de la liste déroulante de base de données. Le `aspnet_regsql.exe` outil requêtes le `sysdatabases` (table système) pour récupérer la liste des bases de données sur le serveur, mais certains web hébergeant le verrouillage de sociétés de leurs serveurs de base de données afin que ces informations ne sont pas disponibles publiquement. Si vous obtenez cette erreur, vous pouvez taper le nom de la base de données directement dans la liste déroulante.


[![Fournir l’outil avec vos informations de connexion de base de données s](configuring-a-website-that-uses-application-services-vb/_static/image5.jpg)](configuring-a-website-that-uses-application-services-vb/_static/image4.jpg)

**Figure 2**: fournir des informations de connexion s outil avec votre base de données ([cliquez pour afficher l’image en taille réelle](configuring-a-website-that-uses-application-services-vb/_static/image6.jpg))


L’écran suivant récapitule les actions qui doivent avoir lieu, à savoir ce qui les objets de base de données d’application services vont être ajoutés à la base de données spécifié. Cliquez sur Suivant pour terminer cette action. Après quelques instants, le dernier écran s’affiche, en notant que les objets de base de données ont été ajoutés (voir Figure 3).


[![Opération réussie Les objets de base de données d’Application Services ont été ajoutés à la base de données de Production](configuring-a-website-that-uses-application-services-vb/_static/image8.jpg)](configuring-a-website-that-uses-application-services-vb/_static/image7.jpg)

**Figure 3**: opération réussie ! L’Application de Services de base de données objets ont été ajoutés à la base de données de Production ([cliquez pour afficher l’image en taille réelle](configuring-a-website-that-uses-application-services-vb/_static/image9.jpg))


Pour vérifier que les objets de base de données d’application services ont été ajoutés à la base de données de production, ouvrez SQL Server Management Studio et connectez-vous à votre base de données de production. Comme le montre la Figure 4, vous devez maintenant voir les tables de base de données d’application services dans votre base de données, `aspnet_Applications`, `aspnet_Membership`, `aspnet_Users`, et ainsi de suite.


[![Vérifiez que les objets de base de données ont été ajoutés à la base de données de Production](configuring-a-website-that-uses-application-services-vb/_static/image11.jpg)](configuring-a-website-that-uses-application-services-vb/_static/image10.jpg)

**Figure 4**: Vérifiez que les objets de base de données ont été ajoutés à la base de données de Production ([cliquez pour afficher l’image en taille réelle](configuring-a-website-that-uses-application-services-vb/_static/image12.jpg))


Vous devez uniquement utiliser le `aspnet_regsql.exe` outil lors du déploiement de votre application web pour la première fois ou pour la première fois après avoir démarré à l’aide des services d’application. Une fois ces objets de base de données sur la base de données de production, ils gagné pas nécessaire d’être ajouté de nouveau ou modifié.

### <a name="copying-user-accounts-from-development-to-production"></a>Copie des comptes d’utilisateur du développement jusqu'à la Production

Lorsque vous utilisez la `SqlMembershipProvider` et `SqlRoleProvider` des classes de fournisseur pour stocker les informations de services d’application dans une base de données SQL Server, les informations de compte et le rôle d’utilisateur sont stockées dans une variété de tables de base de données, y compris `aspnet_Users`, `aspnet_Membership`, `aspnet_Roles`, et `aspnet_UsersInRoles`, entre autres. Si au cours du développement, vous créez des comptes d’utilisateur dans l’environnement de développement, vous pouvez répliquer ces comptes d’utilisateur en production en copiant les enregistrements correspondants dans les tables de base de données. Si vous avez utilisé l’Assistant Publication de base de données pour déployer les objets de base de données de services application que vous pouvez également ont choisi pour copier les enregistrements, ce qui entraîneraient des comptes d’utilisateur créés dans le développement d’également être mise en production. Toutefois, en fonction de vos paramètres de configuration, vous souhaiterez peut-être que ces utilisateurs dont les comptes ont été créés dans le développement et copiées vers la production ne peuvent pas se connecter depuis le site Web de production. Ce qui donne ?

Le `SqlMembershipProvider` et `SqlRoleProvider` classes de fournisseur ont été conçus telles qu’une base de données peut servir de magasin de l’utilisateur de doivent de plusieurs applications, où chaque application peut, en théorie, les utilisateurs avec des noms d’utilisateurs qui se chevauchent et les rôles portant le même nom. Pour permettre cette souplesse, la base de données gère une liste d’applications dans le `aspnet_Applications` table et chaque utilisateur est associé à une de ces applications. Plus précisément, le `aspnet_Users` table a un `ApplicationId` colonne qui lie chaque utilisateur à un enregistrement dans le `aspnet_Applications` table.

En plus de la `ApplicationId` colonne, le `aspnet_Applications` table inclut également un `ApplicationName` colonne, qui fournit un nom plus convivial pour l’application. Lorsqu’un site Web tente de travailler avec un compte d’utilisateur, telles que la validation d’informations d’identification utilisateur s à partir de la page de connexion, elle doit indiquer la `SqlMembershipProvider` classe d’application à utiliser. Il effectue généralement cela, vous devez fournir le nom de l’application et cette valeur provient de la configuration du fournisseur s dans `Web.config` , en particulier via le `applicationName` attribut.

Mais que se passe-t-il si le `applicationName` attribut n’est pas spécifié dans `Web.config`? Dans un tel cas, l’appartenance au système utilise le chemin d’accès racine en tant que le `applicationName` valeur. Si le `applicationName` attribut n’est pas explicitement défini `Web.config`, puis, il existe un risque que l’environnement de production et un environnement de développement utilisent une racine d’application et par conséquent à associer à une autre application noms dans les services d’application. Si une telle non-correspondance se produit, puis les utilisateurs créés dans l’environnement de développement aura une `ApplicationId` valeur ne correspond pas à la `ApplicationId` valeur pour l’environnement de production. Le résultat net est que ces utilisateurs a gagné t être en mesure de se connecter.

> [!NOTE]
> Si vous vous trouvez dans cette situation - copié en production avec des comptes d’utilisateur `ApplicationId` valeur - vous pouvez écrire une requête pour mettre à jour ces incorrect `ApplicationId` valeurs à la `ApplicationId` utilisé en production. Une fois la mise à jour, les utilisateurs dont les comptes ont été créées sur l’environnement de développement seraient maintenant être en mesure de se connecter à l’application web de production.


La bonne nouvelle est qu’il existe une étape simple, vous pouvez prendre pour garantir que les deux environnements utilisent les mêmes `ApplicationId` - définir explicitement la `applicationName` attribut `Web.config` pour tous les fournisseurs de services de votre application. Définir explicitement la `applicationName` attribut « BookReviews » dans le `<membership>` et `<roleManager>` éléments en tant que cet extrait de code à partir de `Web.config` montre.

[!code-xml[Main](configuring-a-website-that-uses-application-services-vb/samples/sample4.xml)]

Pour plus d’informations sur la configuration de la `applicationName` attribut et la logique qui sous-tend, reportez-vous à [ *Scott Guthrie* ](https://weblogs.asp.net/scottgu/) billet de blog s, [ *toujours défini applicationName propriété lors de la configuration de l’appartenance d’ASP.NET et d’autres fournisseurs*](https://weblogs.asp.net/scottgu/443634).

### <a name="managing-user-accounts-in-the-production-environment"></a>La gestion des comptes d’utilisateur dans l’environnement de Production

L’outil d’Administration ASP.NET Web Site (WSAT) vous permet de facilement créer et gérer les comptes d’utilisateur, définir et appliquer des rôles et épeler utilisateur et rôle basée sur des règles d’autorisation. Vous pouvez lancer le WSAT à partir de Visual Studio en accédant à l’Explorateur de solutions et en cliquant sur l’icône de Configuration ASP.NET ou en sélectionnant les menus de site Web ou un projet, puis en sélectionnant l’élément de menu de Configuration ASP.NET. Malheureusement, le WSAT ne fonctionnent qu’avec les sites Web locaux. Par conséquent, vous ne pouvez pas utiliser le WSAT à partir de votre station de travail pour gérer le site Web dans l’environnement de production.

La bonne nouvelle est que toutes les fonctionnalités exposées fournies par le WSAT est disponible par programmation via l’appartenance et les rôles API ; en outre, la plupart des écrans WSAT utilisent les contrôles d’associées à la connexion de ASP.NET standards. En résumé, vous pouvez ajouter des pages ASP.NET à votre site Web qui offrent les fonctionnalités de gestion nécessaire.

Rappelez-vous qu’un didacticiel antérieur mis à jour l’application web critiques à inclure un `~/Admin` dossier et que ce dossier a été configuré pour autoriser uniquement les utilisateurs dans le rôle d’administrateur. J’ai ajouté une page de ce dossier nommé `CreateAccount.aspx` à partir de laquelle un administrateur peut créer un nouveau compte d’utilisateur. Cette page utilise le contrôle CreateUserWizard pour afficher la logique d’interface et le principal utilisateur pour la création d’un nouveau compte d’utilisateur. Nouveautés plus, j’ai personnalisé le contrôle pour inclure une case à cocher qui vous demande si le nouvel utilisateur doit également être ajouté au rôle administrateur (voir Figure 5). Avec un peu de travail, vous pouvez créer un jeu personnalisé de pages qui implémente les tâches liées à la gestion utilisateur et le rôle qui sont sinon fournies par le WSAT.

> [!NOTE]
> Pour plus d’informations sur l’utilisation de l’appartenance et les rôles d’API, ainsi que les contrôles liés à la connexion ASP.NET Web, veillez à lire mon [ *didacticiels de sécurité du site Web*](../../older-versions-security/introduction/security-basics-and-asp-net-support-cs.md). Pour plus d’informations sur la personnalisation du contrôle CreateUserWizard, reportez-vous à la [ *création de comptes utilisateur* ](../../older-versions-security/membership/creating-user-accounts-vb.md) et [ *le stockage des informations utilisateur supplémentaires* ](../../older-versions-security/membership/storing-additional-user-information-vb.md) didacticiels ou extraction [ *Erich Peterson* ](http://www.erichpeterson.com/) article s [ *personnalisation du contrôle CreateUserWizard* ](http://aspnet.4guysfromrolla.com/articles/070506-1.aspx).


[![Les administrateurs peuvent créer de nouveaux comptes d’utilisateur](configuring-a-website-that-uses-application-services-vb/_static/image14.jpg)](configuring-a-website-that-uses-application-services-vb/_static/image13.jpg)

**Figure 5**: les administrateurs peuvent créer des comptes d’utilisateur ([cliquez pour afficher l’image en taille réelle](configuring-a-website-that-uses-application-services-vb/_static/image15.jpg))


Si vous avez besoin de toutes les fonctionnalités de l’extraction WSAT [ *propagée votre propre outil*](http://aspnet.4guysfromrolla.com/articles/052307-1.aspx), dans laquelle l’auteur Dan Clem vous guide tout au long du processus de création d’un outil personnalisé de type WSAT. Dan partage son code source d’applications s (en c#) et fournit des instructions détaillées pour l’ajouter à votre site Web hébergé.

## <a name="summary"></a>Résumé

Lorsque vous déployez une application web qui utilise l’implémentation de base de données de services application vous devez vous assurer que la base de données de production dispose les objets de base de données requis. Ces objets peuvent être ajoutés en utilisant les techniques présentées dans les *déploiement d’une base de données* didacticiel ; vous pouvez également utiliser le `aspnet_regsql.exe` outil, comme nous l’avons vu dans ce didacticiel. Autres problèmes nous avons abordé autour de la synchronisation de nom d’application utilisé dans les environnements de développement et de production (ce qui est important si vous souhaitez que les utilisateurs et rôles créés dans l’environnement de développement soit valide sur la production) et les techniques de gestion des utilisateurs et des rôles dans l’environnement de production.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [*Outil d’inscription de serveur SQL d’ASP.NET (aspnet_regsql.exe)*](https://msdn.microsoft.com/en-us/library/ms229862.aspx)
- [*Création de la base de données de Services d’Application pour SQL Server*](https://msdn.microsoft.com/en-us/library/x28wfk74.aspx)
- [*Création du schéma de l’appartenance dans SQL Server*](../../older-versions-security/membership/creating-the-membership-schema-in-sql-server-vb.md)
- [*Examen de l’appartenance d’ASP.NET s, les rôles et profil*](http://aspnet.4guysfromrolla.com/articles/120705-1.aspx)
- [*Restauration de votre propre outil d’Administration de Site Web*](http://aspnet.4guysfromrolla.com/articles/052307-1.aspx)
- [*Didacticiels de sécurité de site Web*](../../older-versions-security/introduction/security-basics-and-asp-net-support-cs.md)
- [*Présentation de l’outil Administration Site Web*](https://msdn.microsoft.com/en-us/library/yy40ytx0.aspx)

>[!div class="step-by-step"]
[Précédent](configuring-the-production-web-application-to-use-the-production-database-vb.md)
[Suivant](strategies-for-database-development-and-deployment-vb.md)

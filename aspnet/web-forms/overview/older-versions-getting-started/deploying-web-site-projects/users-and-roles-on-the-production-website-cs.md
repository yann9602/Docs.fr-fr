---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/users-and-roles-on-the-production-website-cs
title: "Les utilisateurs et les rôles sur le site Web de Production (c#) | Documents Microsoft"
author: rick-anderson
description: "L’outil d’Administration ASP.NET du site Web (WSAT) fournit une interface utilisateur basée sur le web pour la configuration des paramètres d’appartenance et de rôles et de créer, modifier, un..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/09/2009
ms.topic: article
ms.assetid: dbc54313-5d05-4285-98b3-726edea6d0c9
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/users-and-roles-on-the-production-website-cs
msc.type: authoredcontent
ms.openlocfilehash: 0825b6bd6ca8d75f90cb7c4079e3af0213c5c4e9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="users-and-roles-on-the-production-website-c"></a>Les utilisateurs et les rôles sur le site Web de Production (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le PDF](http://download.microsoft.com/download/5/C/5/5C57DB8C-5DEA-4B3A-92CA-4405544D313B/aspnet_tutorial16_CustomAWAT_cs.pdf)

> L’outil d’Administration ASP.NET du site Web (WSAT) fournit une interface utilisateur basée sur le web pour la configuration des paramètres d’appartenance et de rôles et de création, la modification et la suppression des utilisateurs et rôles. Malheureusement, le WSAT fonctionne uniquement lorsque visité à partir de localhost, ce qui signifie que que vous ne pouvez atteindre l’outil d’Administration du site Web de la production via votre navigateur. La bonne nouvelle est qu’il existe des solutions de contournement qui permettent de gérer les utilisateurs et rôles de production. Ce didacticiel aborde ces solutions de contournement et d’autres.


## <a name="introduction"></a>Introduction

ASP.NET 2.0 a introduit un certain nombre de *les services d’application*, qui sont une suite de services de bloc de construction que vous pouvez ajouter à votre application web. Nous avons ajouté les services d’appartenance et les rôles pour le site Web critiques de nouveau la [ *configuration d’un site Web qu’utilise les Services d’Application* didacticiel](configuring-a-website-that-uses-application-services-cs.md). Le service d’appartenance facilite la création et la gestion des comptes d’utilisateur ; le service de rôles offre une API pour classer les utilisateurs en groupes. Le site critiques comporte trois comptes d’utilisateur - Scott, Jisun et Alice - et un seul rôle, Admin, Scott et Jisun dans le rôle d’administrateur.

ASP. Les services d’application de réseau ne sont pas liés à une implémentation spécifique. Au lieu de cela, vous demandez les services d’application à utiliser un particulier *fournisseur*, et ce fournisseur implémente le service à l’aide d’une technologie particulière. Nous avons configuré à l’application web critiques pour utiliser le `SqlMembershipProvider` et `SqlRoleProvider` fournisseurs pour les services d’appartenance et les rôles. Ces deux fournisseurs de stockant les informations de compte et le rôle d’utilisateur dans une base de données SQL Server et sont les fournisseurs couramment utilisées pour les applications web basés sur Internet hébergées sur une société d’hébergement web.

Un défi courant pour les développeurs qui utilisent les services d’appartenance et les rôles gère les utilisateurs et les rôles sur l’environnement de production. Comment supprimer un compte d’utilisateur à partir du site Web de production, ajoutez un nouveau rôle ou ajouter un utilisateur existant à un rôle existant ? Ce didacticiel explore différentes techniques pour la gestion des utilisateurs et des rôles sur le site Web de production.

## <a name="using-the-aspnet-web-site-administration-tool"></a>À l’aide de l’outil d’Administration de Site Web ASP.NET

ASP.NET inclut un [outil Administration de Site Web](https://msdn.microsoft.com/en-us/library/yy40ytx0.aspx) (WSAT) qui facilite la tâche pour créer et gérer des rôles et des comptes d’utilisateur et pour spécifier des règles d’autorisation basées sur les utilisateurs et les rôles. Pour utiliser le WSAT, cliquez sur l’icône de Configuration ASP.NET dans l’Explorateur de solutions, ou dans le menu projet ou le site Web et choisissez l’option de Configuration ASP.NET. Chacune de ces approches lance un navigateur web et il pointe vers le WSAT à une adresse telle que :`http://localhost:portNumber/asp.netwebadminfiles/default.aspx?applicationPhysicalPath=pathToApplication`

Le WSAT est divisé en trois sections :

- **Sécurité** -gérer les utilisateurs, les rôles et les règles d’autorisation.
- **ApplicationConfiguration** -gérer les &lt;appSettings&gt; et les paramètres SMTP à partir d’ici. Vous pouvez également mettre hors connexion de l’application et gérer le débogage et traçage de paramètres à partir d’ici, ainsi spécifier la page d’erreurs personnalisée par défaut.
- **ProviderConfiguration** -configurer les fournisseurs utilisés par les services d’application.

La section sécurité (illustré **Figure 1**) inclut des liens pour créer de nouveaux utilisateurs, la gestion des utilisateurs, création et la gestion des rôles, création et gestion des règles d’accès. À partir d’ici vous pouvez ajouter un nouveau rôle dans le système, supprimez un utilisateur existant, ou ajouter ou supprimer des rôles à partir d’un compte d’utilisateur particulier.

[![](users-and-roles-on-the-production-website-cs/_static/image2.png)](users-and-roles-on-the-production-website-cs/_static/image1.png)

**Figure 1**: la Section de sécurité WSAT inclut des Options pour la gestion des utilisateurs et des rôles  
([Cliquez pour afficher l’image en taille réelle](users-and-roles-on-the-production-website-cs/_static/image3.png))

Malheureusement, le WSAT est uniquement accessible localement. Vous ne peut pas visiter le WSAT sur votre site Web de production distant ; Si vous visitez `www.yoursite.com/asp.netwebadminfiles/default.aspx` vous obtenez une réponse 404 introuvable. Le code qui alimente les utilisations WSAT le `Membership` et `Roles` classes dans le .NET Framework pour créer, modifier et supprimer des utilisateurs et des rôles. Ces classes, consultez les informations de configuration de l’application web pour déterminer quel fournisseur utiliser ; dans le [ *configuration d’un site Web qu’utilise les Services d’Application* didacticiel](configuring-a-website-that-uses-application-services-cs.md) nous préparons le site Web critiques utilise le `SqlMembershipProvider` et `SqlRoleProvider` fournisseurs. Cela a entraîné l’ajout de `<membership>` et `<roleManager>` sections à `Web.config`.

[!code-xml[Main](users-and-roles-on-the-production-website-cs/samples/sample1.xml)]

Notez que la `<membership>` et `<roleManager>` sections référence le `SqlMembershipProvider` et `SqlRoleProvider` fournisseurs dans leurs `type` d’attribut, respectivement. Ces fournisseurs de stockent les informations de rôle dans une base de données SQL Server spécifiée. La base de données utilisé par ces fournisseurs est spécifié par le `connectionStringName` attribut, `ReviewsConnectionString`, qui est défini dans le `~/ConfigSections/databaseConnectionStrings.config` fichier. N’oubliez pas que le `databaseConnectionStrings.config` fichier dans l’environnement de développement contient la chaîne de connexion à la base de données de développement, alors que le `databaseConnectionStrings.config` fichier sur la production contient la chaîne de connexion à la base de données de production.

En bref, la WSAT doit être accessibles localement via l’environnement de développement, et fonctionne avec les informations utilisateur et le rôle dans la base de données spécifié dans le `databaseConnectionStrings.config` fichier. Par conséquent, si nous modifions les informations de chaîne de connexion dans le `databaseConnectionStrings.config` fichier sur l’environnement de développement nous pouvons utiliser le WSAT localement pour gérer les utilisateurs et les rôles dans l’environnement de production.

Pour illustrer cette fonctionnalité, ouvrez le `databaseConnectionStrings.config` fichier dans Visual Studio sur l’environnement de développement et remplacez la chaîne de connexion de base de données de développement avec la chaîne de connexion de base de données de production. Puis lancez le WSAT, consultez l’onglet sécurité et ajouter un nouvel utilisateur nommé Sam avec mot de passe « password » ! (inférieur entre les guillemets). **Figure 2** affiche l’écran WSAT lors de la création de ce compte.

[![](users-and-roles-on-the-production-website-cs/_static/image5.png)](users-and-roles-on-the-production-website-cs/_static/image4.png)

**Figure 2**: créer un utilisateur nommé Sam dans l’environnement de Production  
([Cliquez pour afficher l’image en taille réelle](users-and-roles-on-the-production-website-cs/_static/image6.png))

Étant donné que nous avons modifié dans la chaîne de connexion `databaseConnectionStrings.config` pour pointer vers le serveur de base de données de production, Sam a été ajoutée en tant qu’utilisateur dans l’environnement de production. Pour vérifier cela, modifiez la chaîne de connexion dans le `databaseConnectionStrings.config` de fichiers à la base de données de développement, puis visitez le `Login.aspx` page dans l’environnement de développement. Essayez de vous connecter en tant que Sam (consultez **Figure 3**).

[![](users-and-roles-on-the-production-website-cs/_static/image8.png)](users-and-roles-on-the-production-website-cs/_static/image7.png)

**Figure 3**: vous ne peut pas se connecter en tant que Sam dans l’environnement de développement  
([Cliquez pour afficher l’image en taille réelle](users-and-roles-on-the-production-website-cs/_static/image9.png))

Vous ne pouvez pas connectez-vous comme Sam dans l’environnement de développement, car les informations de compte d’utilisateur n’existent pas dans la base de données locale. En revanche, est ajouté à la base de données de production. Pour le vérifier, afficher le contenu de la `aspnet_Users` table dans les bases de données à la fois le développement et de production. Dans l’environnement de développement doit être uniquement trois enregistrements pour les utilisateurs Scott Jisun et Alice. Toutefois, le `aspnet_Users` table dans la base de données de production a quatre enregistrements : Scott, Jisun, Alice et Sam. Par conséquent, Sam peut se connecter via le site Web en production, mais pas par le biais de l’environnement de développement.

[![](users-and-roles-on-the-production-website-cs/_static/image11.png)](users-and-roles-on-the-production-website-cs/_static/image10.png)

**Figure 4**: Sam peut se connecter sur le site Web de Production  
([Cliquez pour afficher l’image en taille réelle](users-and-roles-on-the-production-website-cs/_static/image12.png))

> [!NOTE]
> N’oubliez pas de modifier la chaîne de connexion dans le `databaseConnectionStrings.config` fichier à la base de données de développement de chaîne de connexion lorsque vous avez terminé fonctionne avec le WSAT dans le cas contraire, vous allez travailler avec les données de production lorsque vous testez le site via le développement environnement. Gardez à l’esprit pendant que la technique que nous venons de vous permet du WSAT permet de gérer à distance des utilisateurs et des rôles, les modifications apportées à toutes les autres options de configuration WSAT (règles d’accès, SMTP Paramètres, débogage et de traçage paramètres et ainsi de suite) de modifier le `Web.config` fichier. Par conséquent, toutes les modifications apportées aux paramètres s’appliquent à l’environnement de développement et non à l’environnement de production.


## <a name="creating-custom-user-and-role-management-web-pages"></a>Création d’utilisateurs et les Pages Web de gestion des rôles

Le WSAT fournit un hors du système pour la gestion des utilisateurs et des rôles, mais peut être lancé uniquement localement et nécessite d’apporter des modifications aux informations de chaîne de connexion afin de gérer les utilisateurs et rôles de production. La plupart des sites Web qui prennent en charge les comptes d’utilisateur incluent également un numéro de l’utilisateur et rôle administration web pages qui permettent aux administrateurs de gérer des utilisateurs et des rôles à partir des pages du site. Ces pages de l’administration basée sur le web, ce qui la rendent beaucoup plus facile de gérer les utilisateurs et les rôles et sont essentielles pour les sites où il peut y avoir plusieurs administrateurs ou administrateurs qui n’ont pas accès à ou l’arrière-plan technique à utiliser Visual Studio pour lancer le WSAT.

ASP.NET inclut un nombre de contrôles associées à la connexion de Web intégrés qui permettent d’implémenter plusieurs de ces pages web d’administration aussi simple que le glisser -déplacer. Par exemple, vous pouvez créer une page pour les administrateurs créer un nouveau compte d’utilisateur en faisant glisser le contrôle CreateUserWizard sur la page et en définissant certaines propriétés. En fait, la page de création d’utilisateurs dans le WSAT illustré **Figure 2** utilise le même contrôle CreateUserWizard que vous pouvez ajouter à vos pages. En outre, les fonctionnalités des services d’appartenance et les rôles sont disponibles par programme via le `Membership` et `Roles` classes dans le .NET Framework. Avec ces classes vous pouvez écrire du code pour créer, modifier et supprimer des utilisateurs et rôles, ainsi que d’ajouter ou supprimer des utilisateurs aux rôles, pour déterminer quels sont les utilisateurs dans les rôles et pour effectuer d’autres tâches relatifs à l’utilisateur et rôle.

Dans le [ *configuration d’un site Web qu’utilise les Services d’Application* didacticiel](configuring-a-website-that-uses-application-services-cs.md) j’ai ajouté une page à la `Admin` dossier nommé `CreateAccount.aspx`. Cette page permet à un administrateur pour ajouter un nouveau compte d’utilisateur pour le site et spécifier si l’utilisateur nouvellement créé est dans le rôle d’administrateur (consultez **Figure 5**).

[![](users-and-roles-on-the-production-website-cs/_static/image14.png)](users-and-roles-on-the-production-website-cs/_static/image13.png)

**Figure 5**: les administrateurs peuvent créer de nouveaux comptes d’utilisateur  
([Cliquez pour afficher l’image en taille réelle](users-and-roles-on-the-production-website-cs/_static/image15.png))

Pour une présentation plus détaillée de création d’utilisateur et le rôle pages d’administration, ainsi que des instructions détaillées sur l’utilisation de la `Membership` et `Roles` classes et les contrôles liés à la connexion ASP.NET Web, veillez à lire mon [sécurité des sites Web Didacticiels](../../older-versions-security/introduction/security-basics-and-asp-net-support-cs.md). Vous y trouverez des conseils sur la façon de créer des pages web pour créer des comptes, la création et la gestion des rôles, assigner des utilisateurs aux rôles et d’autres tâches d’administration courantes.

Pour implémenter des fonctionnalités comme le WSAT sur le site Web de production, vous pouvez toujours générer votre propre série de pages web qui implémentent les fonctionnalités de la WSAT. Pour commencer, consultez le code source WSAT, qui se trouve dans le dossier `%WINDIR%\Microsoft.NET\Framework\v2.0.50727\ASP.NETWebAdminFiles`. Une autre option consiste à utiliser alternative de Dan Clem WSAT, lequel il partage dans son article, [propagée votre propre Site Web outil d’Administration](http://aspnet.4guysfromrolla.com/articles/052307-1.aspx). Dan parcourt les lecteurs via le processus de création d’un outil personnalisé de type WSAT, inclut le code source de son application pour le téléchargement (en c#) et donne des instructions détaillées pour l’ajout de son WSAT personnalisée à un site Web hébergé.

## <a name="summary"></a>Résumé

L’outil d’Administration ASP.NET Web Site (WSAT) peut être utilisé conjointement avec les services d’application d’appartenance et les rôles pour gérer les informations utilisateur et le rôle de votre site Web. Malheureusement, le WSAT est uniquement accessible localement et ne peut pas être visité à partir de votre site Web de production. Toutefois, en modifiant la chaîne de connexion dans le développement, environnement pour pointer vers la base de données de production, vous pouvez utiliser le WSAT pour gérer les utilisateurs et les rôles sur le site Web de production.

Alors que l’approche WSAT offre un moyen rapide et facile à gérer les utilisateurs et les rôles, il nécessite du lancement du WSAT à partir de Visual Studio, ainsi que des modifications temporaires pour les informations de chaîne de connexion. Le WSAT offre un moyen rapide de gérer les utilisateurs et rôles de production, mais est fastidieuse et ne fonctionne pas pour les sites Web avec plusieurs administrateurs ou administrateurs qui n’ont pas ou n’êtes pas familiarisé avec Visual Studio et le WSAT. Pour ces raisons, la plupart des sites Web qui prennent en charge les comptes d’utilisateur incluent un ensemble de pages web d’administration. Un ensemble de pages web élimine la nécessité pour le WSAT et utilisé par différents utilisateurs administratifs à partir de n’importe quel ordinateur.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Examen des ASP. L’appartenance, rôles et profil de NET](http://aspnet.4guysfromrolla.com/articles/120705-1.aspx)
- [Restauration de votre propre outil d’Administration de Site Web](http://aspnet.4guysfromrolla.com/articles/052307-1.aspx)
- [Présentation de l’outil Administration Site Web](https://msdn.microsoft.com/en-us/library/yy40ytx0.aspx)
- [Didacticiels de sécurité de site Web](../../older-versions-security/introduction/security-basics-and-asp-net-support-cs.md)

>[!div class="step-by-step"]
[Précédent](precompiling-your-website-cs.md)
[Suivant](asp-net-hosting-options-vb.md)

---
uid: web-forms/overview/older-versions-security/introduction/security-basics-and-asp-net-support-cs
title: "Principes élémentaires de sécurité et la prise en charge ASP.NET (c#) | Documents Microsoft"
author: rick-anderson
description: "Il s’agit du premier didacticiel dans une série de didacticiels qui vous allez découvrir les techniques d’authentification des visiteurs via un formulaire web, autoriser l’accès à partic..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/13/2008
ms.topic: article
ms.assetid: 07e15538-2f29-40c6-b2e7-e6115075ac83
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-security/introduction/security-basics-and-asp-net-support-cs
msc.type: authoredcontent
ms.openlocfilehash: 4080e3ccaffefd02c76b89a77e320e963f854961
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="security-basics-and-aspnet-support-c"></a>Principes élémentaires de sécurité et la prise en charge ASP.NET (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le PDF](http://download.microsoft.com/download/2/F/7/2F705A34-F9DE-4112-BBDE-60098089645E/aspnet_tutorial01_Basics_cs.pdf)

> Il s’agit du premier didacticiel dans une série de didacticiels qui vous allez découvrir les techniques pour l’authentification des visiteurs via un formulaire web, autoriser l’accès à certaines pages et les fonctionnalités et la gestion des comptes d’utilisateur dans une application ASP.NET.


## <a name="introduction"></a>Introduction

Les forums de la seule chose que les sites de commerce électronique, par courrier électronique en ligne des sites Web, sites Web du portail et les sites de réseau social que tous ont en commun Nouveautés Tous *des comptes d’utilisateur*. Les sites qui offrent des comptes d’utilisateur doivent fournir un certain nombre de services. Au minimum, nouveaux visiteurs doivent être en mesure de créer un compte et visiteurs de retour doivent être en mesure de se connecter. De telles applications web peuvent prendre des décisions basées sur l’utilisateur connecté : certaines pages ou des actions peuvent être limitées aux seuls consignés dans les utilisateurs, ou d’un sous-ensemble spécifique d’utilisateurs ; autres pages peuvent afficher des informations spécifiques à l’utilisateur connecté, ou il peuvent afficher plus ou moins d’informations, en fonction de l’utilisateur qui consulte la page.

Il s’agit du premier didacticiel dans une série de didacticiels qui vous allez découvrir les techniques pour l’authentification des visiteurs via un formulaire web, autoriser l’accès à certaines pages et les fonctionnalités et la gestion des comptes d’utilisateur dans une application ASP.NET. Au cours de ces didacticiels, nous allons examiner comment :

- Identifier et connecter les utilisateurs à un site Web
- Utilisez ASP. Framework de l’appartenance du réseau pour gérer les comptes d’utilisateur
- Créer, mettre à jour et supprimer des comptes d’utilisateur
- Limiter l’accès à une page web, directory ou des fonctionnalités spécifiques en fonction de l’utilisateur connecté
- Utilisez ASP. Infrastructure de rôles de NET à associer des comptes d’utilisateurs aux rôles
- Gérer les rôles d’utilisateur
- Limiter l’accès à une page web, directory ou des fonctionnalités spécifiques, selon le rôle de l’utilisateur connecté
- Personnaliser et étendre ASP. Contrôles Web de sécurité du réseau

Ces didacticiels sont destinées à être concis et fournissent des instructions détaillées avec beaucoup de captures d’écran pour vous guider dans le processus visuellement. Chaque didacticiel est disponible dans les versions de Visual Basic et c# et inclut un téléchargement de l’intégralité du code utilisé. (Ce premier didacticiel se concentre sur les concepts de sécurité à partir d’un point de vue de haut niveau et par conséquent ne contient-elle pas de code associé).

Dans ce didacticiel, nous allons décrire les concepts de sécurité importantes et quelles fonctionnalités sont disponibles dans ASP.NET pour faciliter la mise en œuvre l’authentification par formulaire, l’autorisation, les comptes d’utilisateurs et rôles. C’est parti !

> [!NOTE]
> La sécurité est un aspect important de toute application qui s’étend sur physiques, technologiques, et les décisions de stratégie et nécessite un degré élevé de domaine et planification de la base de connaissances. Cette série de didacticiels n’est pas destinée comme guide pour le développement d’applications web sécurisées. Au lieu de cela, il se concentre spécifiquement sur les rôles, l’autorisation, des comptes d’utilisateur et l’authentification par formulaire. Alors que certains concepts de sécurité révolution autour de ces problèmes sont décrits dans cette série, d’autres sont laissés sur les services Internet.


## <a name="authentication-authorization-user-accounts-and-roles"></a>L’authentification, l’autorisation, les comptes d’utilisateurs et rôles

L’authentification, l’autorisation, des comptes d’utilisateur et les rôles sont des quatre termes qui seront utilisés très souvent tout au long de cette série de didacticiels, donc je souhaite prendre un moment rapide de définir ces termes dans le contexte de sécurité web. Dans un modèle client-serveur, tel qu’Internet, il existe de nombreux scénarios dans lesquels le serveur doit identifier le client qui effectue la requête. *Authentification* est le processus permettant de vérifier l’identité du client. Un client qui a été correctement identifié est dite *authentifié*. Un client non identifié est dite *non authentifiées* ou *anonyme*.

Systèmes d’authentification impliquent au moins un des trois facettes suivantes : [quelque chose que vous connaissez, quelque chose que vous possédez ou quelque chose vous sont](http://www.cs.cornell.edu/Courses/cs513/2005fa/NNLauthPeople.html). La plupart des applications web s’appuient sur autre chose que le client sait, par exemple un mot de passe ou un code confidentiel. Les informations utilisées pour identifier un utilisateur - son nom d’utilisateur et un mot de passe, par exemple - sont appelés *informations d’identification*. Cette série de didacticiels se concentre sur *l’authentification par formulaire*, qui est un modèle d’authentification où les utilisateurs se connectent au site en fournissant leurs informations d’identification dans un formulaire de page web. Nous avons rencontré ce type d’authentification avant. Accédez à n’importe quel site de commerce électronique. Lorsque vous êtes prêt à extraire, vous êtes invité à se connecter en entrant votre nom d’utilisateur et un mot de passe dans les zones de texte sur une page web.

Non seulement identifier les clients, un serveur devrez peut-être limiter les ressources ou les fonctionnalités sont accessibles en fonction du client qui effectue la requête. *Autorisation* est le processus permettant de déterminer si un utilisateur particulier a l’autorité pour accéder à une ressource spécifique ou une fonctionnalité.

A *compte d’utilisateur* est un magasin pour conserver les informations relatives à un utilisateur particulier. Comptes d’utilisateur doivent inclure au minimum les informations qui identifient de façon unique l’utilisateur, telles que le nom de connexion de l’utilisateur et mot de passe. Avec ces informations essentielles, comptes d’utilisateur peuvent inclure des éléments tels que : adresse de messagerie de l’utilisateur ; date et heure de que création du compte ; date et heure de que leur dernière ouverture de session nom et prénom ; numéro de téléphone ; et son adresse postale. Lorsque vous utilisez l’authentification par formulaire, les informations de compte d’utilisateur sont généralement stockées dans une base de données relationnel telles que Microsoft SQL Server.

Les applications Web qui prennent en charge les comptes d’utilisateur peuvent éventuellement regrouper les utilisateurs à *rôles*. Un rôle est simplement une étiquette qui est appliquée à un utilisateur et fournit une abstraction pour définir des règles d’autorisation et fonctionnalités au niveau de la page. Par exemple, un site Web peut inclure un rôle d’administrateur avec des règles d’autorisation qui empêchent tout le monde, mais un administrateur pour accéder à un ensemble particulier de pages web. En outre, une variété de pages qui sont accessibles à tous les utilisateurs (y compris les non administrateurs) peut-être afficher des données supplémentaires ou offrent des fonctionnalités supplémentaires lorsque visité par les utilisateurs du rôle Administrateurs. À l’aide de rôles, nous pouvons définir ces règles d’autorisation sur un rôle en rôle-plutôt que par utilisateur.

## <a name="authenticating-users-in-an-aspnet-application"></a>L’authentification des utilisateurs dans une Application ASP.NET

Lorsqu’un utilisateur entre une URL dans la fenêtre de l’adresse de son navigateur ou de clics sur un lien, le navigateur rend une [protocole HTTP (Hypertext Transfer)](http://en.wikipedia.org/wiki/HTTP) demande au serveur web pour le contenu spécifié, soit sur ASP.NET page, une image, un code JavaScript fichier, ou tout autre type de contenu. Le serveur web est chargé de renvoyer le contenu demandé. Ce faisant, il doit déterminer un certain nombre de choses sur la demande, y compris l’émetteur de la requête et si l’identité est autorisée pour récupérer le contenu demandé.

Par défaut, les navigateurs envoient des demandes HTTP qui ne disposent pas d’aucune sorte d’informations d’identification. Mais si le navigateur n’inclut pas les informations d’authentification du serveur web démarre le flux de travail de l’authentification, qui tente d’identifier le client qui effectue la requête. Les étapes du flux de travail d’authentification varient selon le type d’authentification utilisé par l’application web. ASP.NET prend en charge trois types d’authentification : Passport, Windows et les formulaires. Cette série de didacticiels se concentre sur l’authentification par formulaire, mais nous allons prendre une minute pour comparer les banques d’utilisateur d’authentification Windows et les flux de travail.

### <a name="authentication-via-windows-authentication"></a>Authentification via l’authentification Windows

Le flux de travail de l’authentification Windows utilise l’une des techniques d’authentification suivantes :

- Authentification de base
- Authentification Digest
- Authentification intégrée de Windows

Tous les trois techniques fonctionnent dans approximativement de la même manière : lorsqu’un non autorisé, une demande anonyme arrive, le serveur web envoie une réponse HTTP qui indique que l’autorisation est nécessaire pour continuer. Puis, le navigateur affiche une boîte de dialogue qui invite l’utilisateur pour leur nom d’utilisateur et le mot de passe (voir Figure 1). Cette information est ensuite envoyée au serveur web via un en-tête HTTP.


![Une boîte de dialogue demande à l’utilisateur ses informations d’identification](security-basics-and-asp-net-support-cs/_static/image1.png)

**Figure 1**: boîte de dialogue modale demande à l’utilisateur ses informations d’identification


Les informations d’identification fournies sont validées dans le magasin de l’utilisateur Windows du serveur web. Cela signifie que chaque utilisateur authentifié dans votre application web doit avoir un compte Windows dans votre organisation. Il s’agit de courants dans les scénarios intranet. En fait, lorsque vous utilisez l’authentification intégrée Windows dans un environnement intranet, le navigateur fournit automatiquement le serveur web avec les informations d’identification utilisées pour se connecter au réseau, en supprimant la boîte de dialogue indiquée dans la Figure 1. Alors que l’authentification Windows est idéal pour les applications intranet, il est généralement irréalisable pour des applications Internet, car vous ne souhaitez pas créer de comptes Windows pour chaque utilisateur qui s’inscrit sur votre site.

### <a name="authentication-via-forms-authentication"></a>Authentification via l’authentification par formulaire

L’authentification par formulaire, est quant à lui, est idéale pour les applications web Internet. Souvenez-vous que l’authentification par formulaires identifie l’utilisateur en invitant à entrer leurs informations d’identification via un formulaire web. Par conséquent, lorsqu’un utilisateur tente d’accéder à une ressource non autorisée, ils sont automatiquement redirigés vers la page de connexion dans lequel ils peuvent entrer leurs informations d’identification. Les informations d’identification soumises sont ensuite validées par rapport à un magasin d’utilisateur personnalisée - généralement une base de données.

Après avoir vérifié les informations d’identification envoyées, un *ticket d’authentification de formulaires* est créé pour l’utilisateur. Ce ticket indique que l’utilisateur a été authentifié et inclut des informations d’identification, telles que le nom d’utilisateur. Le ticket d’authentification par formulaires est stocké (généralement) en tant que cookie sur l’ordinateur client. Par conséquent, les visites suivantes sur le site Web incluent le ticket d’authentification de formulaires dans la requête HTTP, permettant ainsi l’application web identifier l’utilisateur une fois qu’ils se sont connectés.

La figure 2 illustre le flux de travail de l’authentification de formulaires à partir d’un point de vue de niveau supérieur. Notez la façon dont les éléments d’authentification et d’autorisation dans ASP.NET agissent comme deux entités distinctes. Le système d’authentification forms identifie l’utilisateur (ou des rapports qu’ils soient anonymes). Le système d’autorisation est qui détermine si l’utilisateur a accès à la ressource demandée. Si l’utilisateur n’est pas autorisé (comme dans la Figure 2 lors de la tentative de façon anonyme visiter ProtectedPage.aspx), le système d’autorisation signale que l’utilisateur est refusé à l’origine les formulaires de système d’authentification pour rediriger automatiquement l’utilisateur à la page de connexion.

Une fois que l’utilisateur s’est connecté, les requêtes HTTP suivantes incluent le ticket d’authentification de formulaires. Le système d’authentification forms identifie simplement l’utilisateur : il s’agit du système d’autorisation qui détermine si l’utilisateur peut accéder à la ressource demandée.


![Le flux de travail de l’authentification de formulaires](security-basics-and-asp-net-support-cs/_static/image2.png)

**Figure 2**: le flux de travail de l’authentification de formulaires


Nous exploreront l’authentification par formulaire beaucoup plus en détail dans les deux didacticiels,[une vue d’ensemble de l’authentification par formulaire](an-overview-of-forms-authentication-cs.md) et [Configuration de l’authentification de formulaires et les rubriques avancées](forms-authentication-configuration-and-advanced-topics-cs.md). Pour plus d’informations sur ASP. Options d’authentification du réseau, consultez [l’authentification ASP.NET](https://msdn.microsoft.com/library/eeyk640h.aspx).

## <a name="limiting-access-to-web-pages-directories-and-page-functionality"></a>Limiter l’accès aux Pages Web, les répertoires et les fonctionnalités de la Page

ASP.NET inclut deux façons de déterminer si un utilisateur particulier a autorité pour accéder à un fichier spécifique ou un répertoire :

- **L’autorisation de fichiers** - étant donné que les pages ASP.NET et les services web sont implémentées comme les fichiers qui résident sur le système de fichiers du serveur web, l’accès à ces fichiers peuvent être spécifiés via les listes de contrôle d’accès (ACL). L’autorisation de fichier est plus couramment utilisée avec l’authentification Windows, car les ACL sont des autorisations qui s’appliquent aux comptes Windows. Lorsque vous utilisez l’authentification par formulaire, toutes les demandes de niveau système système d’exploitation et le fichier sont exécutées par le même compte Windows, quel que soit l’utilisateur visite le site.
- **Autorisation d’URL**-avec l’autorisation d’URL, le développeur de pages spécifie les règles d’autorisation dans Web.config. Ces règles d’autorisation spécifient ce que les utilisateurs ou les rôles sont autorisés à accéder ou non autorisés à accéder à certaines pages ou des répertoires dans l’application.

L’autorisation de fichier et l’autorisation d’URL définissent les règles d’autorisation pour accéder à une page ASP.NET spécifique ou pour toutes les pages ASP.NET dans un répertoire particulier. À l’aide de ces techniques, nous pouvons indiquer ASP.NET pour refuser les demandes vers une page particulière pour un utilisateur particulier, ou autoriser l’accès à un ensemble d’utilisateurs et refuser l’accès à tous les autres. Que se passe-t-il pour les scénarios où tous les utilisateurs peuvent accéder à la page, mais les fonctionnalités de la page dépendent de l’utilisateur ? Par exemple, de nombreux sites qui prennent en charge les comptes d’utilisateur ont des pages qui affichent un contenu différent ou des données pour les utilisateurs authentifiés par rapport aux utilisateurs anonymes. Un utilisateur anonyme peut afficher un lien pour vous connecter au site, alors qu’un utilisateur authentifié verriez à la place d’un message comme, Bienvenue, *nom d’utilisateur* avec un lien pour déconnecter. Un autre exemple : lors de l’affichage d’un élément à un site de vente aux enchères vous consultez des informations différentes selon que vous êtes un enchérisseur ou celui enchères l’élément.

Ces ajustements de niveau page peuvent être accomplies de manière déclarative ou par programme. Pour afficher du contenu pour anonyme à des utilisateurs authentifiés, il vous suffit de faire glisser un [contrôle LoginView](https://msdn.microsoft.com/library/system.web.ui.webcontrols.loginview.aspx) sur votre page et entrez le contenu approprié dans ses modèles AnonymousTemplate et LoggedInTemplate. Ou bien, vous pouvez déterminer par programme si la demande en cours est authentifiée, qui est l’utilisateur et les rôles auxquels ils appartiennent (le cas échéant). Vous pouvez utiliser ces informations pour puis afficher ou masquer des colonnes dans une grille ou de panneaux dans la page.

Cette série inclut trois didacticiels qui vous concentrer sur l’autorisation. ***Autorisation basée sur l’utilisateur***examine comment limiter l’accès à une page ou des pages dans un répertoire pour les comptes d’utilisateur spécifique ; ***En fonction du rôle d’autorisation*** examine en fournissant des règles d’autorisation au niveau du rôle niveau ; Enfin, le ***affichage de contenu selon l’actuellement connecté utilisateur*** didacticiel explore la modification d’un particulier la page contenu et les fonctionnalités en fonction de l’utilisateur sur la page. Pour plus d’informations sur ASP. Les options d’autorisation de NET, consultez [autorisation ASP.NET](https://msdn.microsoft.com/library/wce3kxhd.aspx).


## <a name="user-accounts-and-roles"></a>Comptes d’utilisateur et des rôles

ASP. L’authentification par formulaire de NET fournit une infrastructure pour les utilisateurs peuvent se connecter à un site et leur état authentifié mémorisé une visite de la page. Et l’autorisation d’URL offre une infrastructure permettant de limiter l’accès à certains fichiers ou dossiers dans une application ASP.NET. Aucune fonctionnalité, cependant, fournit un moyen pour stocker des informations de compte d’utilisateur ou de la gestion des rôles.

Avant d’ASP.NET 2.0, les développeurs ont été chargées de créer leurs propres magasins de l’utilisateur et le rôle. Ils étaient également sur le raccordement pour concevoir des interfaces utilisateur et l’écriture du code pour l’utilisateur essentiel relatives aux comptes des pages telles que la page de connexion et de la page pour créer un nouveau compte, entre autres. Sans une infrastructure de compte utilisateur intégré dans ASP.NET, chaque développeur implémentation des comptes d’utilisateur avaient pour arriver à sa propre décisions de conception des questions, stockage des mots de passe ou d’autres informations sensibles ? et quelles instructions dois-je imposer en matière de puissance et la longueur de mot de passe ?

Aujourd'hui, la mise en œuvre des comptes d’utilisateur dans une application ASP.NET est beaucoup plus simple grâce à la *framework de l’appartenance* et contrôles Web de connexion intégrés. L’infrastructure de l’appartenance est un petit nombre de classes dans le [espace de noms System.Web.Security](https://msdn.microsoft.com/library/system.web.security.aspx) qui fournissent des fonctionnalités pour effectuer des tâches relatives aux comptes d’utilisateur essentielles. La classe de clé dans le cadre de l’appartenance est la [classe d’appartenance](https://msdn.microsoft.com/library/system.web.security.membership.aspx), qui a des méthodes, telles que :

- CreateUser
- DeleteUser
- GetAllUsers
- GetUser
- UpdateUser
- ValidateUser

L’infrastructure d’appartenance utilise le [modèle de fournisseur](http://aspnet.4guysfromrolla.com/articles/101905-1.aspx), qui sépare les API de l’infrastructure d’appartenance à partir de son implémentation. Cela permet aux développeurs d’utiliser une API commune, mais les autorise à utiliser une implémentation qui répond aux besoins de l’application personnalisée. En bref, la classe d’appartenance définit les fonctionnalités essentielles de l’infrastructure (méthodes, propriétés et événements), mais ne fournit pas réellement les détails d’implémentation. Au lieu de cela, les méthodes de la classe d’appartenance appellent le fournisseur configuré, qui exécute le travail réel. Par exemple, lorsque la méthode CreateUser de la classe d’appartenance est appelée, la classe d’appartenance ne sait pas les détails de la banque de l’utilisateur. Il ne sait pas si les utilisateurs sont gérées dans une base de données ou dans un fichier XML ou dans un autre magasin. La classe d’appartenance examine la configuration de l’application web pour déterminer quel fournisseur de déléguer l’appel à, et cette classe de fournisseur est chargée de réellement créer le nouveau compte d’utilisateur dans le magasin de l’utilisateur approprié. Cette interaction est illustrée dans la Figure 3.

Microsoft fournit deux classes de fournisseur d’appartenance dans le .NET Framework :

- [ActiveDirectoryMembershipProvider](https://msdn.microsoft.com/library/system.web.security.activedirectorymembershipprovider.aspx) -implémente l’API d’appartenance dans les serveurs Active Directory et Active Directory Application Mode (ADAM).
- [SqlMembershipProvider](https://msdn.microsoft.com/library/system.web.security.sqlmembershipprovider.aspx) -implémente l’API d’appartenance dans une base de données SQL Server.

Cette série de didacticiels se concentre exclusivement sur SqlMembershipProvider.


[![Le fournisseur de modèle permet à des implémentations différentes en toute transparence sur secteur dans l’infrastructure&lt;/ fort&gt;](security-basics-and-asp-net-support-cs/_static/image4.png)](security-basics-and-asp-net-support-cs/_static/image3.png)

**Figure 03**: le fournisseur de modèle permet à des implémentations différentes en toute transparence sur secteur dans l’infrastructure ([cliquez pour afficher l’image en taille réelle](security-basics-and-asp-net-support-cs/_static/image5.png))


L’avantage du modèle de fournisseur est qu’autres implémentations peuvent être développées par Microsoft, des fournisseurs tiers ou les développeurs individuels et connectées en toute transparence dans le cadre de l’appartenance. Par exemple, Microsoft a publié [un fournisseur d’appartenances pour les bases de données Microsoft Access](https://download.microsoft.com/download/5/5/b/55bc291f-4316-4fd7-9269-dbf9edbaada8/sampleaccessproviders.vsi). Pour plus d’informations sur les fournisseurs d’appartenances, reportez-vous à la [fournisseur Toolkit](https://msdn.microsoft.com/asp.net/aa336558.aspx), qui inclut une procédure pas à pas de fournisseurs d’appartenances, les exemples de fournisseurs personnalisés, plus de 100 pages de documentation sur le modèle de fournisseur et le Terminer le code source pour les fournisseurs d’appartenances intégrés (à savoir, ActiveDirectoryMembershipProvider et SqlMembershipProvider).

ASP.NET 2.0 introduit également l’infrastructure de rôles. Comme l’infrastructure d’appartenance, l’infrastructure de rôles est construit sur le modèle de fournisseur. L’API est exposée via le [classe rôles](https://msdn.microsoft.com/library/system.web.security.roles.aspx) et le .NET Framework est fourni avec trois classes de fournisseur :

- [AuthorizationStoreRoleProvider](https://msdn.microsoft.com/library/system.web.security.authorizationstoreroleprovider.aspx) -gère les informations de rôle dans un magasin de stratégies de gestionnaire d’autorisations, comme Active Directory ou ADAM.
- [SqlRoleProvider](https://msdn.microsoft.com/library/system.web.security.sqlroleprovider.aspx) -implémente des rôles dans une base de données SQL Server.
- [WindowsTokenRoleProvider](https://msdn.microsoft.com/library/system.web.security.windowstokenroleprovider.aspx) -associe les informations de rôle en fonction de groupe de Windows du visiteur. Cette méthode est généralement utilisée avec l’authentification Windows.

Cette série de didacticiels se concentre exclusivement sur le SqlRoleProvider.

Étant donné que le modèle de fournisseur inclut une seule API orientés (les classes d’appartenance et les rôles), il est possible de générer des fonctionnalités autour de cette API sans avoir à vous soucier des détails d’implémentation - celles sont gérées par les fournisseurs sélectionnés dans la page développeur. Cette API unifiée permet de Microsoft et des fournisseurs tiers générer des contrôles Web qui communiquent avec les infrastructures d’appartenance et les rôles. ASP.NET est fourni avec un nombre de [contrôles Web d’ouverture de session](https://msdn.microsoft.com/library/ms178329.aspx) pour l’implémentation des interfaces utilisateur de compte utilisateur courantes. Par exemple, le [contrôle de connexion](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.aspx) un invite utilisateur à entrer leurs informations d’identification, les valide, puis les enregistre dans via l’authentification par formulaire. Le [contrôle LoginView](https://msdn.microsoft.com/library/system.web.ui.webcontrols.loginview.aspx) fournit des modèles d’affichage des différentes balises aux utilisateurs anonymes et les utilisateurs authentifiés ou un balisage différent selon le rôle de l’utilisateur. Et le [contrôle CreateUserWizard](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.aspx) fournit une interface utilisateur pas à pas pour la création d’un nouveau compte d’utilisateur.

Dans les coulisses les contrôles de connexion différentes interagissent avec les infrastructures d’appartenance et les rôles. La plupart des contrôles de connexion peuvent être implémentées sans avoir à écrire une seule ligne de code. Nous allons examiner ces contrôles plus en détail dans les didacticiels futures, y compris les techniques permettant d’étendre et de personnaliser leurs fonctionnalités.

## <a name="summary"></a>Récapitulatif

Toutes les applications web qui prennent en charge les comptes d’utilisateurs nécessitent des fonctionnalités similaires, telles que : la possibilité pour les utilisateurs peuvent se connecter et le journal des état mémorisés une visite de la page ; une page web pour les visiteurs de nouveau créer un compte ; et la possibilité du développeur de pages pour spécifier les ressources, les données et les fonctionnalités sont disponibles pour les utilisateurs ou les rôles. Les tâches d’authentifier et autoriser les utilisateurs et de gérer des rôles et des comptes d’utilisateur est compliqué accomplir dans les applications ASP.NET grâce à l’authentification par formulaire, l’autorisation d’URL et les infrastructures d’appartenance et les rôles.

Au cours des didacticiels plusieurs suivants, nous allons examiner ces aspects en créant une application web de travail de toutes pièces étape par étape. Dans le didacticiel de deux, nous allons examiner l’authentification par formulaire en détail. Nous allons voir le flux de travail de l’authentification de formulaires dans action, analysez le ticket d’authentification de formulaires, discuter des problèmes de sécurité et voir comment configurer le système d’authentification de formulaires - tous lors de la création d’une application web qui permet des visiteurs de se connecter et se déconnecter.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [ASP.NET 2.0 d’appartenance, rôles, l’authentification par formulaire et ressources de sécurité](https://weblogs.asp.net/scottgu/ASP.NET-2.0-Membership_2C00_-Roles_2C00_-Forms-Authentication_2C00_-and-Security-Resources-)
- [Recommandations de sécurité 2.0 d’ASP.NET](https://msdn.microsoft.com/library/ms998258.aspx)
- [Authentification ASP.NET](https://msdn.microsoft.com/library/eeyk640h.aspx)
- [Autorisation ASP.NET](https://msdn.microsoft.com/library/wce3kxhd.aspx)
- [Vue d’ensemble des contrôles de connexion ASP.NET](https://msdn.microsoft.com/library/ms178329.aspx)
- [Examen de ASP.NET de 2.0 l’appartenance, rôles et profil](http://aspnet.4guysfromrolla.com/articles/120705-1.aspx)
- [Comment faire pour sécuriser mon Site à l’aide de l’appartenance et les rôles ?](https://asp.net/learn/videos/video-45.aspx) (Vidéo)
- [Introduction à l’appartenance](https://msdn.microsoft.com/library/yh26yfzy.aspx)
- [MSDN Security Developer Center](https://msdn.microsoft.com/security/default.aspx)
- [Professionnel ASP.NET 2.0 sécurité, l’appartenance et la gestion des rôles](http://www.wrox.com/WileyCDA/WroxTitle/productCd-0764596985.html) (ISBN : 978-0-7645-9698 MSSQLServer-8)
- [Fournisseur de kit de ressources](https://msdn.microsoft.com/asp.net/aa336558.aspx)

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été ce didacticiel série a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel incluent Alicja Maziarz, John Suru et Teresa Murphy. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Next](an-overview-of-forms-authentication-cs.md)

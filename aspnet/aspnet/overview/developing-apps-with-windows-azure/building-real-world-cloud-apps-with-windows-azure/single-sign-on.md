---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/single-sign-on
title: "L’authentification unique (génération d’applications Cloud du monde réel avec Azure) | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/12/2014
ms.topic: article
ms.assetid: 7d82d5e9-0619-4f22-9e03-32a6d52940a5
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/single-sign-on
msc.type: authoredcontent
ms.openlocfilehash: f0d465b363652c691c203d608f2cb9d139e72fed
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="single-sign-on-building-real-world-cloud-apps-with-azure"></a>Single Sign-On (génération d’applications Cloud du monde réel avec Azure)
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur le livre électronique, consultez [le premier chapitre](introduction.md).


Il existe plusieurs problèmes de sécurité à considérer lorsque vous développez une application cloud, mais pour cette série nous allons nous concentrer sur un seul : l’authentification unique. Une question souvent questions des utilisateurs s’agit-il : « je suis principalement générer des applications pour les employés de ma société ; comment héberger ces applications dans le cloud et toujours leur permettre d’utiliser le même modèle de sécurité Mes employés connaître et à utilisent dans l’environnement local quand ils s’exécutent des applications qui sont hébergés au sein du pare-feu ? » Une des façons de que nous permettre ce scénario est appelée Azure Active Directory (Azure AD). Azure AD vous permet de proposer des enterprise line-of-business (LOB) applications via Internet, et vous permet de proposer ces applications à des partenaires commerciaux.

## <a name="introduction-to-azure-ad"></a>Introduction à Azure AD

[Azure AD](https://docs.microsoft.com/azure/active-directory/) fournit [Active Directory](https://msdn.microsoft.com/en-us/library/windows/desktop/aa746492.aspx) dans le cloud. Fonctionnalités clés sont les suivantes :

- Il s’intègre à Active Directory local.
- Il active l’authentification unique avec vos applications.
- Il prend en charge des normes ouvertes telles que [SAML](http://en.wikipedia.org/wiki/SAML_2.0), [WS-Fed](http://en.wikipedia.org/wiki/WS-Federation), et [OAuth 2.0](http://oauth.net/2/).
- Il prend en charge Enterprise [Graph API REST](https://msdn.microsoft.com/en-us/library/hh974476.aspx).

Supposons que vous avez un environnement Windows Server Active Directory local que vous utilisez pour permettre aux employés se connectent à des applications de l’Intranet :

![](single-sign-on/_static/image1.png)

Qu’Azure AD vous permet de faire est de créer un répertoire dans le cloud. Il s’agit d’une fonctionnalité gratuite et facile à configurer.

Il peut être entièrement indépendant de votre Active Directory local ; Vous pouvez placer tout le monde vous souhaitez qu’il contient et les authentifiez dans les applications Internet.

![Windows Azure Active Directory](single-sign-on/_static/image2.png)

Ou vous pouvez l’intégrer à votre site Active Directory.

![Active Directory et l’intégration de WA AD](single-sign-on/_static/image3.png)

Maintenant tous les employés qui peuvent s’authentifier sur site peuvent s’authentifier également sur l’Internet, sans avoir à ouvrir un pare-feu ou de déployer de nouveaux serveurs dans votre centre de données. Vous pouvez continuer d’exploiter tout l’environnement Active Directory existant que vous connaissez et que vous utilisez aujourd'hui pour permettre à vos applications internes à l’authentification unique sur la capacité.

Une fois que vous avez effectué cette connexion entre Active Directory et Azure AD, vous pouvez également activer vos applications web et vos appareils mobiles pour authentifier vos employés dans le cloud, et vous pouvez activer les applications tierces, telles que les applications Office 365, SalesForce.com ou Google, pour accepter votre informations d’identification des employés. Si vous utilisez Office 365, vous êtes déjà configuré avec Azure AD, car Office 365 utilise Azure AD pour l’authentification et d’autorisation.

![applications tierces 3e](single-sign-on/_static/image4.png)

L’avantage de cette approche est que votre organisation ajoute ou supprime un utilisateur, ou un utilisateur modifie un mot de passe, vous utilisez le même processus que vous utilisez aujourd'hui dans votre environnement local. Tous les de votre site sur les modifications apportées à AD sont propagées automatiquement à l’environnement du cloud.

Si votre entreprise est à l’aide d’ou en déplaçant dans Office 365, la bonne nouvelle est que vous avez Azure AD sont configuré automatiquement, car Office 365 utilise Azure AD pour l’authentification. Vous pouvez facilement utiliser dans vos propres applications Office 365 utilise l’authentification même.

## <a name="set-up-an-azure-ad-tenant"></a>Configurer un locataire Azure AD

un annuaire Azure AD est appelé une annonce Azure [client](https://technet.microsoft.com/en-us/library/jj573650.aspx), et la configuration d’un client est assez simple. Nous allons vous montrer comment procéder dans le portail de gestion Azure afin d’illustrer les concepts, mais bien entendu, comme les autres fonctions de portail vous pouvez également le faire à l’aide d’un script ou une API de gestion.

Dans le portail de gestion, cliquez sur l’onglet Active Directory.

![WAAD dans le portail](single-sign-on/_static/image5.png)

Vous disposez automatiquement d’un locataire Azure AD pour votre compte Azure, et vous pouvez cliquer sur le **ajouter** situé en bas de la page pour créer des répertoires supplémentaires. Vous pouvez choisir un pour un environnement de test et un environnement de production, par exemple. Réfléchissez bien sur ce que vous nommez un nouveau répertoire. Si vous utilisez votre nom pour le répertoire, puis vous utilisez votre nom d’un des utilisateurs, ce qui peuvent prêter à confus.

![Ajouter un répertoire](single-sign-on/_static/image6.png)

Le portail est prise en charge complète pour la création, la suppression et la gestion des utilisateurs au sein de cet environnement. Par exemple, pour ajouter un utilisateur doit accéder à la **utilisateurs** onglet et cliquez sur le **ajouter un utilisateur** bouton.

![Bouton Ajouter un utilisateur](single-sign-on/_static/image7.png)

![Boîte de dialogue utilisateur Ajouter](single-sign-on/_static/image8.png)

Vous pouvez créer un nouvel utilisateur qui existe uniquement dans ce répertoire, ou vous pouvez inscrire un Account Microsoft en tant qu’un utilisateur dans ce répertoire, ou le Registre ou un utilisateur à partir d’un autre annuaire Azure AD en tant qu’utilisateur dans ce répertoire. (Un répertoire réel, le domaine par défaut sera ContosoTest.onmicrosoft.com. Vous pouvez également utiliser un domaine de votre choix, comme contoso.com.)

![Types utilisateur](single-sign-on/_static/image9.png)

![Boîte de dialogue utilisateur Ajouter](single-sign-on/_static/image10.png)

Vous pouvez affecter l’utilisateur à un rôle.

![Profil utilisateur](single-sign-on/_static/image11.png)

Et le compte est créé avec un mot de passe temporaire.

![Mot de passe temporaire](single-sign-on/_static/image12.png)

Les utilisateurs de créez de cette façon, vous peuvent vous connecter immédiatement à vos applications web à l’aide de ce répertoire de cloud.

Ce qui est idéal pour enterprise SSO, cependant, est la **intégration d’annuaire** onglet :

![Onglet de l’intégration d’annuaire](single-sign-on/_static/image13.png)

Si vous activez l’intégration d’annuaire, et [télécharger un outil](https://social.technet.microsoft.com/wiki/contents/articles/19098.howto-install-the-windows-azure-active-directory-sync-tool-now-with-pictures.aspx), vous pouvez synchroniser ce répertoire de cloud avec votre Active Directory local existant que vous utilisez déjà à l’intérieur de votre organisation. Puis tous les utilisateurs stockés dans le répertoire seront afficheront dans ce répertoire de cloud. Vos applications cloud peuvent désormais s’authentifier tous les employés à l’aide de leurs informations d’identification Active Directory existantes. Et est libre, l’outil de synchronisation et Azure AD lui-même.

L’outil est un Assistant qui est facile à utiliser, comme vous pouvez le voir à partir de ces captures d’écran. Ils ne sont pas des instructions complètes, juste un exemple vous montrant le processus de base. Pour plus d’informations d’utilisation-à--it, consultez les liens de la [ressources](#resources) section à la fin de ce chapitre.

![Assistant de configuration d’outil de synchronisation de WA AD](single-sign-on/_static/image14.png)

Cliquez sur **suivant**, puis entrez vos informations d’identification Azure Active Directory.

![Assistant de configuration d’outil de synchronisation de WA AD](single-sign-on/_static/image15.png)

Cliquez sur **suivant**, puis entrez votre local informations d’identification Active Directory.

![Assistant de configuration d’outil de synchronisation de WA AD](single-sign-on/_static/image16.png)

Cliquez sur **suivant**, puis indiquez si vous souhaitez stocker un hachage de vos mots de passe Active Directory dans le cloud.

![Assistant de configuration d’outil de synchronisation de WA AD](single-sign-on/_static/image17.png)

Le hachage de mot de passe que vous pouvez stocker dans le cloud est un hachage unidirectionnel ; les mots de passe réels ne sont jamais stockées dans Azure AD. Si vous décidez par rapport à la stocker les hachages dans le cloud, vous devrez utiliser [Active Directory Federation Services](https://technet.microsoft.com/en-us/library/hh831502.aspx) (ADFS). Il existe également [autres facteurs à prendre en compte lorsque choisir s’il faut ou non utiliser AD FS](https://technet.microsoft.com/en-us/library/jj573653.aspx). L’option ADFS nécessite quelques étapes de configuration supplémentaires.

Si vous choisissez de stocker les hachages dans le cloud, vous avez terminé, l’outil démarre la synchronisation des annuaires lorsque vous cliquez sur **suivant**.

![Assistant de configuration d’outil de synchronisation de WA AD](single-sign-on/_static/image18.png)

Et dans quelques minutes, vous avez terminé.

![Assistant de configuration d’outil de synchronisation de WA AD](single-sign-on/_static/image19.png)

Vous devez uniquement exécuter cette procédure sur un contrôleur de domaine de l’organisation, sur Windows 2003 ou version ultérieure. Et le redémarrer. Lorsque vous avez terminé, tous les utilisateurs se trouvent dans le cloud et vous pouvez effectuer l’authentification unique à partir de n’importe quel web ou d’une application mobile, à l’aide de SAML, OAuth ou WS-Fed.

Parfois nous demande souvent sur le niveau de sécurité Il s’agit de : Microsoft l’utilise pour leurs données sensibles de l’entreprise ? Et la réponse est Oui, que nous faisons. Par exemple, si vous accédez au site Microsoft SharePoint interne à [https://microsoft.sharepoint.com/](https://microsoft.sharepoint.com/), vous êtes invité à se connecter.

![Connexion à Office 365](single-sign-on/_static/image20.png)

Microsoft a activé ADFS, donc lorsque vous entrez un ID Microsoft, vous êtes redirigé vers une page de connexion AD FS.

![L’authentification dans AD FS](single-sign-on/_static/image21.png)

Et une fois que vous entrez des informations d’identification stockées dans un compte Microsoft AD interne, vous avez accès à cette application interne.

![Site Microsoft SharePoint](single-sign-on/_static/image22.png)

Nous utilisons un serveur de connexion AD principalement parce que nous avions déjà ADFS configurer avant de Azure AD est désormais disponible, mais le processus d’ouverture de session est en cours sur un annuaire Azure AD dans le cloud. Nous placer nos documents importants, contrôle de code source, les fichiers de gestion des performances, rapports de ventes, etc., dans le cloud et sont à l’aide de cette même solution exacte afin de les sécuriser.

## <a name="create-an-aspnet-app-that-uses-azure-ad-for-single-sign-on"></a>Créer une application ASP.NET qui utilise Azure AD pour l’authentification unique

Visual Studio le rend très facile de créer une application qui utilise Azure AD pour l’authentification unique, comme vous pouvez le voir à partir de quelques captures d’écran.

Lorsque vous créez une nouvelle application ASP.NET, MVC ou Web Forms, la méthode d’authentification par défaut est ASP.NET Identity. Pour remplacer cette Azure AD, vous cliquez sur un **modifier l’authentification** bouton.

![Modifier l’authentification](single-sign-on/_static/image23.png)

Sélectionnez les comptes de société, entrez votre nom de domaine, puis sélectionnez l’authentification unique.

![Configurer la boîte de dialogue authentification](single-sign-on/_static/image24.png)

Vous pouvez également donner à la lecture de l’application ou en lecture/écriture pour les données d’annuaire. Si vous procédez ainsi, il peut utiliser le [Azure Graph API REST](https://msdn.microsoft.com/en-us/library/windowsazure/hh974476.aspx) pour rechercher le numéro de téléphone des utilisateurs, savoir si elles ne sont au bureau, lorsque leur dernière ouverture de session, etc.

C’est tout ce que vous avez à faire : Visual Studio vous demande les informations d’identification d’un administrateur pour votre locataire Azure AD, puis il configure votre projet et votre client Azure AD pour l’application.

Lorsque vous exécutez le projet, vous verrez une page de connexion, et vous pouvez vous connecter avec les informations d’identification d’un utilisateur dans votre annuaire Azure AD.

![Org compte connectez-vous](single-sign-on/_static/image25.png)

![Connecté](single-sign-on/_static/image26.png)

Lorsque vous déployez l’application sur Azure, il vous est sélectionné un **activer l’authentification d’organisation** case à cocher, et à nouveau Visual Studio prend en charge de tous les la configuration pour vous.

![Publier le site Web](single-sign-on/_static/image27.png)

Ces captures d’écran proviennent d’un didacticiel complet qui montre comment générer une application qui utilise l’authentification Azure AD : [développement des applications ASP.NET avec Azure Active Directory](../../../../identity/overview/getting-started/developing-aspnet-apps-with-windows-azure-active-directory.md).

## <a name="summary"></a>Résumé

Dans ce chapitre vous a appris que Azure Active Directory, Visual Studio et ASP.NET, rendent facile à configurer l’authentification unique dans les applications Internet aux utilisateurs de votre organisation. Vos utilisateurs peuvent se connecter dans les applications Internet à l’aide de ces mêmes informations qu’ils utilisent pour se connecter à l’aide d’Active Directory dans votre réseau interne.

Le [chapitre suivant](data-storage-options.md) examine les options de stockage de données disponibles pour une application cloud.

<a id="resources"></a>
## <a name="resources"></a>Ressources

Pour plus d'informations, reportez-vous aux ressources suivantes :

- [Documentation Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Page du portail pour la documentation d’Azure AD sur le site windowsazure.com. Pour consulter des didacticiels pas à pas, consultez le **développer** section.
- [L’authentification multifacteur Azure](https://docs.microsoft.com/azure/multi-factor-authentication/). Page du portail pour plus d’informations sur l’authentification multifacteur dans Azure.
- [Options d’authentification de compte de société](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#orgauthoptions). Explication des options d’authentification Azure AD dans la boîte de dialogue Nouveau projet Visual Studio 2013.
- [Microsoft Patterns and Practices - de modèle d’identité fédérée](https://msdn.microsoft.com/en-us/library/dn589790.aspx).
- [Comment faire : Installer l’outil de synchronisation Azure Active Directory](https://social.technet.microsoft.com/wiki/contents/articles/19098.howto-install-the-windows-azure-active-directory-sync-tool-now-with-pictures.aspx).
- [Active Directory Federation Services 2.0 Content Map](https://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx). Liens vers la documentation sur AD FS 2.0.
- [Autorisation basée sur les rôles et de listes ACL dans une Application Windows Azure AD](https://code.msdn.microsoft.com/Role-Based-and-ACL-Based-86ad71a1). Exemple d’application.
- [Blog de l’API Graph Active Directory Azure](https://blogs.msdn.com/b/aadgraphteam/).
- [Contrôle d’accès dans le modèle BYOD et intégration d’annuaire dans une Infrastructure d’identité hybride](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/PCIT-B213#fbid=). Tech Ed 2014 session vidéo par Gayana Bagdasaryan.

>[!div class="step-by-step"]
[Précédent](web-development-best-practices.md)
[Suivant](data-storage-options.md)

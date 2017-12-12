---
uid: web-forms/overview/deployment/deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview
title: "Déploiement Web d’entreprise : Vue d’ensemble du scénario | Documents Microsoft"
author: jrjlee
description: "Cet ensemble de didacticiels utilise un exemple de solution avec un niveau réaliste de complexité, ainsi que d’un scénario de déploiement d’une entreprise fictive, pour fournir une référence..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/03/2012
ms.topic: article
ms.assetid: aa862153-4cd8-4e33-beeb-abf502c6664f
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview
msc.type: authoredcontent
ms.openlocfilehash: f90db22bf98456661c530e728e854ce109aec6fd
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="enterprise-web-deployment-scenario-overview"></a>Déploiement Web d’entreprise : Vue d’ensemble du scénario
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cet ensemble de didacticiels utilise un exemple de solution avec un niveau réaliste de complexité, ainsi que d’un scénario de déploiement d’une entreprise fictive, pour fournir une implémentation de référence et pour donner les tâches et les procédures pas à pas un contexte commun. Cette rubrique décrit le scénario du didacticiel et présente l’exemple de solution.


## <a name="scenario-description"></a>Description du scénario

Fabrikam, Inc., une société fictive, consiste à créer une solution qui permet aux équipes de ventes à distance de stocker et récupérer des informations de contact à partir d’une interface web.

Les processus d’Application Lifecycle Management (ALM) chez Fabrikam, Inc. nécessitent la solution pour le déploiement sur trois environnements de serveur à différentes étapes du processus de développement logiciel :

- Un environnement de test ou de « bac à sable » développeur.
- Un environnement de mise en lots basée sur l’intranet.
- Un environnement de production sur Internet.

Chacun de ces environnements a des exigences de sécurité et de configuration différents, et chaque pose des défis de déploiement unique.

### <a name="the-fabrikam-inc-server-infrastructure"></a>Le Fabrikam, Inc. Infrastructure de serveur

Ceci est l’infrastructure de développement et de déploiement de haut niveau chez Fabrikam, Inc.

![](enterprise-web-deployment-scenario-overview/_static/image1.png)

Les stations de travail du développeur, l’infrastructure de contrôle de code source, l’environnement de développement de test et l’environnement intermédiaire que tous résider sur le réseau intranet au sein du domaine Fabrikam.net. L’environnement de production se trouve sur un réseau de périmètre (également appelé DMZ, zone démilitarisée et sous-réseau filtré), qui est isolé du réseau intranet par un pare-feu. Il s’agit d’un scénario de déploiement courant : en général, vous isolez vos serveurs de web exposés à Internet à partir de votre infrastructure de serveur interne via l’utilisation de pare-feu ou des serveurs de passerelle.

Dans cet exemple :

- Un serveur Team Foundation Server (TFS) 2010 avec un serveur de builds distincts fournit des fonctionnalités d’intégration continue (CI) et de contrôle de code source.
- L’environnement de test développeur inclut un serveur web d’Internet Information Services (IIS) 7.5 et un serveur de base de données SQL Server 2008 R2.
- L’environnement de production comprend plusieurs serveurs web d’IIS 7.5 synchronisées par un serveur de contrôleur de Web Farm Framework (WFF), ainsi que d’un serveur de base de données SQL Server 2008 R2. Dans la pratique, le serveur de base de données peut utiliser le clustering ou la mise en miroir pour améliorer l’évolutivité et la disponibilité.
- L’environnement intermédiaire est conçu pour répliquer la configuration de l’environnement de production aussi fidèlement que possible.
- Les stratégies d’isolation réseau et de pare-feu ne permettent pas de déploiement direct, automatisé à partir de l’intranet pour le réseau de périmètre.

La configuration de chacun de ces environnements est décrite plus en détail dans le deuxième didacticiel [configuration des environnements de serveur pour le déploiement Web](../configuring-server-environments-for-web-deployment/configuring-server-environments-for-web-deployment.md).

### <a name="team-roles-for-alm"></a>Rôles de l’équipe pour ALM

Ces utilisateurs sont impliqués dans la création, la gestion, la création et la publication de la solution de gestionnaire de contacts :

- Matt Hink est un développeur d’applications web Fabrikam, Inc. Il est membre de l’équipe qui a développé une solution de gestionnaire de contacts à l’aide de Visual Studio 2010. Matt dispose des droits d’administrateur complets sur les serveurs dans l’environnement de test de développeur, ce qui lui permet de configurer l’environnement pour répondre à ses besoins. Il possède également l’accès utilisateur à l’instance de Visual Studio 2010 TFS où il stocke le code source pour la solution de gestionnaire de contacts.
- Rob Walters est un administrateur de serveur pour l’équipe de développement de Fabrikam, Inc. Rob a un accès administratif sur le serveur TFS pour qu’il peut configurer tous les aspects de TFS et Team Build. Rob également avec un accès administratif pour le test et les serveurs web intermédiaires et agit comme l’administrateur de base de données (DBA) pour les serveurs de base de données dans le test et l’environnement intermédiaire. Rob a configuré Team Build sur le serveur TFS pour effectuer ces tâches :

    - Générer et exécuter des tests unitaires sur l’application chaque fois qu’un utilisateur archive un fichier à TFS. Il s’agit de l’élément de configuration.
    - Déployer l’application Gestionnaire de contacts à l’environnement de test automatiquement une fois que l’application transmet les tests unitaires. Cela inclut la base de données de publication pour les serveurs de test sur le déploiement initial et les mises à jour la base de données après le déploiement initial.
    - Déployez l’application Gestionnaire de contacts dans l’environnement intermédiaire dans un processus de la seule étape.
    - Créer un package Web un administrateur de serveur Web et un administrateur peuvent utiliser pour publier l’application sur l’environnement de production.
- Lisa Andrews est responsable du déploiement d’applications sur les serveurs de production de Fabrikam, Inc. un administrateur du serveur. Elle dispose d’un accès en lecture au partage où l’équipe TFS Build stocke le package de déploiement web une fois qu’il génère l’application Gestionnaire de contacts. Elle a également un accès administratif pour les serveurs web de production afin qu’elle peut déployer l’application en production. En outre, elle agit en tant que l’administrateur qui déploie des mises à jour de la base de données et des bases de données sur le serveur de base de données dans l’environnement de production.

<a id="_The_Contact_Manager"></a>

### <a name="the-contact-manager-solution"></a>La Solution de gestionnaire de contacts

La solution de gestionnaire de contacts est conçue pour permettre aux utilisateurs inscrits, connecté en ajouter et modifier les informations de contact via une interface web. La solution de gestionnaire de contacts se compose de quatre projets individuels :

![](enterprise-web-deployment-scenario-overview/_static/image2.png)

- **ContactManager.Mvc**. Il s’agit d’un projet d’application web ASP.NET MVC3 qui représente le point d’entrée pour la solution. Il offre des fonctionnalités d’application web de base, comme en fournissant aux utilisateurs la possibilité de créer et d’afficher les détails du contact. L’application s’appuie sur un service Windows Communication Foundation (WCF) pour gérer les contacts et une base de données de services application ASP.NET pour gérer l’authentification et l’autorisation.
- **ContactManager.Database**. Il s’agit d’un projet de base de données Visual Studio 2010. Le projet définit le schéma pour une base de données que les magasins de coordonnées.
- **ContactManager.Service**. Il s’agit d’un projet de service web WCF. L’expose WCF un point de terminaison qui permet aux appelants à effectuer créer, récupérer, mettre à jour et supprimer (CRUD) des opérations sur la base de données du Gestionnaire de contacts. Le service repose sur la base de données du Gestionnaire de contacts et de l’assembly ContactManager.Common.dll.
- **ContactManager.Common**. Il s’agit d’un projet de bibliothèque de classes. Le service WCF s’appuie sur les types définis dans cet assembly.

Une évaluation complète de la solution et ses exigences de déploiement est fournie dans le premier didacticiel de cette série, [déploiement Web de l’entreprise](../web-deployment-in-the-enterprise/web-deployment-in-the-enterprise.md).

<a id="_Deployment_Tasks"></a>

## <a name="deployment-tasks"></a>Tâches de déploiement

Plusieurs tâches distinctes sont impliqués dans le déploiement d’applications sur différents environnements dans une grande entreprise. Voici les principales tâches que les didacticiels abordent :

![](enterprise-web-deployment-scenario-overview/_static/image3.png)

Voici une liste de chaque étape du processus de déploiement du point de vue des utilisateurs décrit plus haut dans ce document :

1. Tous les membres de l’équipe de révision de la solution de gestionnaire de contacts dans Visual Studio 2010 pour déterminer les problèmes et les exigences de déploiement clés.
2. Matt Hink peut déployer la solution de gestionnaire de contacts directement à partir de la station de travail de développement à l’environnement de test de développeur, d’effectuer un test initial de la logique de déploiement.
3. Matt Hink ajoute l’application pour le contrôle de code source dans TFS.
4. Rob Walters crée plusieurs définitions de build pour la solution de gestionnaire de contacts dans Team Build. Une définition de build utilise l’élément de configuration pour déployer la solution à l’environnement de développement de test chaque fois qu’un utilisateur archive du nouveau code. Une autre définition de build vous permet de déploiements de déclencheur d’utilisateurs dans l’environnement intermédiaire en fonction des besoins.
5. Chaque fois qu’un utilisateur archive du nouveau code, Team Build automatiquement génère des composants de la solution, exécute les tests unitaires et déploie la solution dans l’environnement de test développeur si la génération a réussi et la passe des tests unitaires.
6. Lorsqu’un utilisateur déclenche un déploiement dans l’environnement intermédiaire, la solution empaquetage et du déploiement dans un processus de la seule étape. Ce processus génère également un package pour le déploiement manuel pour l’environnement de production.
7. Lisa Andrews déploie l’application sur l’environnement de production par manuellement l’importation du package web créé à l’étape 6.

### <a name="key-deployment-issues"></a>Principaux problèmes de déploiement

La solution de gestionnaire de contacts et le scénario de Fabrikam, Inc. Mettez en surbrillance les différents problèmes courants et des défis que vous pouvez rencontrer lorsque vous déployez complexes, les solutions d’entreprise à l’échelle. Exemple :

- Vous devez être en mesure de déployer des projets pour plusieurs environnements, comme le développeur environnements ou de test, intermédiaire des plates-formes et des serveurs de production. La solution doit être déployé avec différents paramètres de configuration pour chaque environnement.
- Vous devez déployer plusieurs projets dépendants simultanément dans le cadre d’un processus de génération et de déploiement étape unique ou automatisé.
- Vous devez être en mesure de déploiement de disque à partir d’un processus automatisé. Par exemple, vous souhaitez utiliser un processus de l’élément de configuration pour déployer des applications web dans un environnement intermédiaire lorsque le nouveau code est archivé.
- Vous devez être en mesure de contrôler le processus de déploiement et de définir des variables de déploiement en dehors de Visual Studio, comme les développeurs ont peu de chances pour que les paramètres de configuration corrects ou les informations d’identification nécessaires pour chaque environnement cible.
- Vous devez déployer des projets de base de données basée sur le schéma et conserver les données existantes sur les déploiements suivants.
- Vous devez déployer des bases de données d’appartenance sur une base ad hoc sans déployer des données de compte d’utilisateur. Vous devez également mettre à jour le schéma des bases de données d’appartenance déployé sans perte de données de compte utilisateur existant.
- Vous devez exclure certains fichiers ou dossiers lorsque vous déployez du contenu pour les environnements cibles différents.

En outre, la gestion du déploiement quand les mises à jour fréquentes et incrémentielle lève des défis supplémentaires. Exemple :

- Pour exécuter des tests unitaires chaque fois qu’un développeur vérifie dans le nouveau code. Vous souhaitez déployer la solution si le code réussit les tests unitaires uniquement.
- Lorsque vous déployez une application web dans un environnement intermédiaire ou de production, vous souhaitez rediriger les utilisateurs vers un *application\_offline.htm* fichier pendant la durée du processus de déploiement.
- Vous souhaitez connecter des activités de déploiement. Le processus de déploiement doit envoyer des notifications par courrier électronique des déploiements réussies ou échoués pour les destinataires désignés.
- En cas d’un déploiement automatisé, le processus de déploiement doit réexécuter le déploiement actuel ou déployer le package web précédente à la place.

>[!div class="step-by-step"]
[Précédent](deploying-web-applications-in-enterprise-scenarios.md)
[Suivant](application-lifecycle-management-from-development-to-production.md)

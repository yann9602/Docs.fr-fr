---
uid: web-forms/overview/deployment/web-deployment-in-the-enterprise/web-deployment-in-the-enterprise
title: "Le déploiement de l’entreprise Web | Documents Microsoft"
author: jrjlee
description: "Ce didacticiel explique comment répondre à un grand nombre des défis que vous pouvez rencontrer lorsque vous gérez le déploiement d’applications web de l’échelle de l’entreprise pour le développement..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: b8283698-7b82-42a8-8d83-3aeb18ca7fcc
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/web-deployment-in-the-enterprise/web-deployment-in-the-enterprise
msc.type: authoredcontent
ms.openlocfilehash: 6210d01f65bcadf8ae4209e372d5aac68861bd7a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="web-deployment-in-the-enterprise"></a>Déploiement Web de l’entreprise
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Ce didacticiel explique comment répondre à un grand nombre des défis que vous pouvez rencontrer lorsque vous gérez le déploiement d’applications web de l’échelle de l’entreprise dans les environnements de développement, test, intermédiaire et de production. Le didacticiel comprend une solution de référence avec un mélange de contenu conceptuel et de tâches pour vous guider à travers les différentes tâches courantes et les procédures.
> 
> Pour obtenir une traduction italienne de ces didacticiels, visitez [http://www.lucamorelli.it](http://www.lucamorelli.it).


## <a name="enterprise-deployment-challenges"></a>Problèmes de déploiement d’entreprise

Les organisations rencontrent souvent ces défis lorsqu’elles apparaîtront pour gérer le déploiement des solutions d’entreprise complexes :

- Vous devez être en mesure de déployer des projets pour plusieurs environnements, comme le développeur environnements ou de test, intermédiaire des plates-formes et des serveurs de production. La solution doit être déployé avec différents paramètres de configuration pour chaque environnement.
- Vous devez déployer plusieurs projets dépendants simultanément dans le cadre d’un processus de génération et de déploiement étape unique ou automatisé.
- Vous devez être en mesure de déploiement de disque à partir d’un processus automatisé. Par exemple, vous souhaitez utiliser un processus d’intégration continue (CI) pour déployer des applications web dans un environnement de test lorsque le nouveau code est archivé.
- Vous devez être en mesure de contrôler le processus de déploiement et de définir des variables de déploiement en dehors de Visual Studio, comme les développeurs ont peu de chances pour que les paramètres de configuration corrects ou les informations d’identification nécessaires pour chaque environnement cible.
- Vous devez déployer des projets de base de données basée sur le schéma et conserver les données existantes sur les déploiements suivants.
- Vous devez déployer des bases de données d’appartenance sur une base ad hoc sans déployer des données de compte d’utilisateur. Vous devez également mettre à jour le schéma des bases de données d’appartenance déployé sans perte de données de compte utilisateur existant.
- Vous devez exclure certains fichiers ou dossiers lorsque vous déployez du contenu pour les environnements cibles différents.

## <a name="overview-of-approach"></a>Vue d’ensemble de l’approche

Ce didacticiel, ainsi que les autres didacticiels de cette série, utilise cette approche de haut niveau pour relever les défis décrits ci-dessus.

- **Utilisez des fichiers de projet Microsoft Build Engine (MSBuild) personnalisés pour contrôler le processus de génération et de déploiement global.**
- Cela vous permet de créer et déployer chaque projet dans la solution dans le cadre d’une opération unique et scriptable.
- Les paramètres spécifiques à l’environnement sont configurés à l’aide de fichiers de projet spécifique à l’environnement simple. Contrairement à l’approche centré Visual Studio de l’utilisation de configurations de solution et des profils pour configurer les déploiements pour les différents environnements de publication, cette approche vous permet de configurer et gérer le processus de déploiement en dehors de Visual Studio. Cela signifie que les développeurs ne doivent passer connaissance des chaînes de connexion, les points de terminaison de service, les informations d’identification de serveur et autres variables de déploiement pour les environnements de destination.
- Les fichiers de projet personnalisés peuvent être appelées par Team Build dans le cadre d’un flux de travail de Team Foundation Server (TFS). Cela vous permet de configurer le déploiement automatisé pour les scénarios de l’élément de configuration.

**Pour empaqueter et déployer des projets d’application web, utilisez l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy).**

- Web Deploy fournit une infrastructure qui vous permet d’empaqueter et déployer votre contenu de l’application web sur un serveur web IIS de destination, ainsi que les dépendances, les paramètres de configuration, paramètres de sécurité et d’autres exigences.
- Vous pouvez contrôler le processus d’empaquetage et de déploiement entière à partir dans vos fichiers de projet MSBuild personnalisés. Vous pouvez également manipuler les paramètres de configuration qui accompagnent votre package de déploiement web, telles que les chaînes de connexion, les points de terminaison de service et les détails de destination de IIS.
- Web Deploy, ainsi que le Pipeline de publication Web, offre un grand nombre de points d’extensibilité qui vous permettent de personnaliser vos déploiements. Par exemple, il est facile exclure les fichiers inutiles et les dossiers à partir de vos packages de déploiement web.

**Utilisez l’utilitaire VSDBCMD.exe pour déployer et mettre à jour des schémas de base de données.**

- VSDBCMD vous permet de déployer des bases de données à partir d’un fichier de schéma de base de données (.dbschema), qui est généré lorsque vous générez un projet de base de données Visual Studio. En revanche, la fonctionnalité de déploiement de base de données incluse dans le déploiement Web est mieux adaptée au déploiement de bases de données existantes à partir d’une instance locale de SQL Server.
- Contrairement aux fonctionnalités de Visual Studio pour le déploiement de projets de base de données, VSDBCMD vous permet de déployer des mises à jour différentielles à une base de données cible. Cela vous permet de conserver les données existantes pendant que vous mettez à niveau le schéma de base de données.
- Vous pouvez exécuter des commandes VSDBCMD à partir de vos fichiers de projet MSBuild personnalisés.

## <a name="content-map"></a>Plan de contenu

Ce didacticiel inclut des rubriques qui entrent dans quatre domaines principaux.

Ces rubriques présentent la solution de référence & #x 2014 ; la solution Contact Manager & #x 2014 ; et décrivent comment télécharger et configurez-le sur votre ordinateur local :

- [La Solution de gestionnaire de contacts](the-contact-manager-solution.md)
- [Configuration de la Solution de gestionnaire de contacts](setting-up-the-contact-manager-solution.md)

Ces rubriques présentent des fichiers projet MSBuild, décrivent comment vous pouvez créer et utiliser des fichiers de projet personnalisés et suivez le processus de déploiement pour la solution de gestionnaire de contacts :

- [Présentation du fichier de projet](understanding-the-project-file.md)
- [Comprendre le processus de génération](understanding-the-build-process.md)

Ces rubriques décrivent le déploiement d’applications web, y compris comment empaquetage de build et de fonctionnement du processus, comment le processus de génération s’intègre avec le Pipeline de publication Web, comment modifier les paramètres de déploiement et comment déployer des packages web vers la destination environnements :

- [Création et l’empaquetage des projets d’Application Web](building-and-packaging-web-application-projects.md)
- [Configuration des paramètres de déploiement du Package Web](configuring-parameters-for-web-package-deployment.md)
- [Packages de déploiement Web](deploying-web-packages.md)

- [Déploiement de projets de base de données](deploying-database-projects.md) décrit les différentes techniques que vous pouvez utiliser pour déployer des projets de base de données de Visual Studio, ainsi que les avantages et inconvénients de chaque approche. [Création et exécution d’un fichier de commandes de déploiement](creating-and-running-a-deployment-command-file.md) décrit comment créer un fichier de commande simple qui encapsule la logique de votre déploiement et vous permet de déployer des solutions complexes comme un seule étape processus.
- Enfin, [installer manuellement les Packages Web](manually-installing-web-packages.md) conclut le didacticiel en vous montrant pour importer des packages web dans IIS.

## <a name="key-technologies"></a>Technologies de clé

Les rubriques de ce didacticiel utilisent principalement ces technologies pour gérer la génération et déploiement :

- Visual Studio 2010
- MSBuild
- IIS 7.5
- Web Deploy 2.0
- L’utilitaire de déploiement de base de données VSDBCMD.exe

## <a name="other-tutorials-in-this-series"></a>Autres didacticiels de cette série

Cela fait partie d’une série de cinq didacticiels sur le déploiement web de l’échelle de l’entreprise. Voici les autres didacticiels dans la série :

- [Déployer des Applications Web dans les scénarios d’entreprise](../deploying-web-applications-in-enterprise-scenarios/deploying-web-applications-in-enterprise-scenarios.md). Ce contenu introduction fournit l’arrière-plan contextuel pour la série de didacticiels. Il décrit le scénario du didacticiel, et illustre comment les tâches et les procédures pas à pas décrites dans l’ensemble de la série s’intègrent dans un processus d’Application Lifecycle Management (ALM) plus large.
- [Configuration d’environnements de serveur pour le déploiement Web](../configuring-server-environments-for-web-deployment/configuring-server-environments-for-web-deployment.md). Ce didacticiel explique comment configurer les serveurs Windows pour prendre en charge différents scénarios de déploiement, y compris le déploiement du package web à distance à l’aide le Service de l’Agent de déploiement Web (l’agent distant) ou le Gestionnaire de déploiement Web et le déploiement de la base de données distante. Il fournit des conseils sur le choix de la méthode de déploiement appropriée pour votre propre environnement, et décrit l’utilisation de Web Farm Framework (WFF) pour répliquer les applications web déployées sur tous les serveurs web dans une batterie de serveurs.
- [Configuration de Team Foundation Server pour le déploiement Web](../configuring-team-foundation-server-for-web-deployment/configuring-team-foundation-server-for-web-deployment.md). Ce didacticiel explique comment configurer TFS pour prendre en charge différents scénarios de déploiement, notamment le déploiement automatisé en tant que partie d’un processus de l’élément de configuration et de déclencher manuellement les déploiements des builds spécifiques.
- [Avancé de déploiement Web d’entreprise](../advanced-enterprise-web-deployment/advanced-enterprise-web-deployment.md). Ce didacticiel explique comment effectuer différentes tâches de déploiement plus avancées, telles que la personnalisation des déploiements de base de données pour plusieurs environnements, à l’exclusion des fichiers et dossiers de déploiement et en prenant des applications web en mode hors connexion pendant le processus de déploiement .

>[!div class="step-by-step"]
[Next](the-contact-manager-solution.md)

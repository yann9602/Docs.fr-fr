---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-server-environments-for-web-deployment
title: "Configuration d’environnements de serveur pour le déploiement Web | Documents Microsoft"
author: jrjlee
description: "Ce didacticiel va vous montrer comment configurer des environnements de serveur pour la prise en charge en un clic ou automatisé, le déploiement de site Web et la publication dans différentes du scénario de différentes..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 0bf0959b-4ca8-45de-bd13-b15347543b5a
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-server-environments-for-web-deployment
msc.type: authoredcontent
ms.openlocfilehash: 8a07d283e3e4344e5513152cf760ac90481d9f4b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="configuring-server-environments-for-web-deployment"></a>Configuration d’environnements de serveur pour le déploiement Web
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Ce didacticiel vous indiquera comment configurer des environnements de serveur pour la prise en charge en un clic ou automatisé, de déploiement de site Web et de publication dans divers scénarios différents. Le didacticiel inclut des rubriques pour vous guider dans diverses tâches, telles que la configuration d’un serveur web pour prendre en charge les approches spécifiques pour le déploiement et la configuration d’une batterie de serveurs Web Farm Framework (WFF), ainsi que des vues d’ensemble scénario qui fournissent à la fin conseils de bout en bout plus haut niveau.
> 
> Ce didacticiel utilise le scénario de déploiement de Fabrikam, Inc. décrit dans [déploiement Web d’entreprise : vue d’ensemble du scénario](../deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview.md) en tant que point de référence pour l’infrastructure réseau et des exemples.
> 
> Pour obtenir une traduction italienne de ces didacticiels, visitez [http://www.lucamorelli.it](http://www.lucamorelli.it).


Ce didacticiel inclut les rubriques suivantes :

- [Choisir l’approche appropriée pour le déploiement Web](choosing-the-right-approach-to-web-deployment.md)
- [Scénario : Configuration d’un environnement de Test pour le déploiement Web](scenario-configuring-a-test-environment-for-web-deployment.md)
- [Scénario : Configuration d’un environnement intermédiaire pour le déploiement Web](scenario-configuring-a-staging-environment-for-web-deployment.md)
- [Scénario : Configuration d’un environnement de Production pour le déploiement Web](scenario-configuring-a-production-environment-for-web-deployment.md)
- [Configuration d’un serveur Web pour la publication (Agent distant) de déploiement Web](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md)
- [Publication de déploiement de configuration d’un serveur Web pour le Web (Gestionnaire de déploiement Web)](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler.md)
- [Configuration d’un serveur Web pour la publication (déploiement en mode hors connexion) de déploiement Web](configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md)
- [Configuration d’un serveur de base de données de publication de déploiement Web](configuring-a-database-server-for-web-deploy-publishing.md)
- [Création d’une batterie de serveurs avec l’infrastructure de batterie de serveurs Web](creating-a-server-farm-with-the-web-farm-framework.md)
- [Configuration des propriétés de déploiement pour un environnement cible](configuring-deployment-properties-for-a-target-environment.md)

La première rubrique [en choisissant l’approche de droite pour le déploiement Web](choosing-the-right-approach-to-web-deployment.md), décrit les méthodes principales que vous pouvez utiliser pour publier des applications web à l’aide de l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy) 2.0. Il identifie également les scénarios correspondant à chaque approche. À ce stade, chaque rubrique scénario fournit une vue d’ensemble des tâches que vous devez effectuer et identifie les rubriques que vous devez réaliser pour vous aider à effectuer ces tâches.

Si vous utilisez l’approche de fichier de projet de fractionnement décrite dans [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md) pour générer et déployer votre solution, la dernière rubrique [configuration des propriétés de déploiement pour un environnement cible](configuring-deployment-properties-for-a-target-environment.md), explique comment configurer les fichiers de projet spécifique à un environnement pour le déploiement dans les environnements de destination différents.

## <a name="key-technologies"></a>Technologies de clé

Ce didacticiel se concentre sur l’utilisation de ces produits et technologies pour prendre en charge le déploiement web :

- IIS 7.5
- Web Deploy 2.x
- WFF 2.x
- Service de gestion IIS Web (WMSvc)

Ce didacticiel aborde également l’utilisation d’ASP.NET MVC 3, ASP.NET 4.0, Windows Server 2008 R2 et SQL Server 2008 R2.

## <a name="other-tutorials-in-this-series"></a>Autres didacticiels de cette série

Cela fait partie d’une série de cinq didacticiels sur le déploiement web de l’échelle de l’entreprise. Voici les autres didacticiels dans la série :

- [Déployer des Applications Web dans les scénarios d’entreprise](../deploying-web-applications-in-enterprise-scenarios/deploying-web-applications-in-enterprise-scenarios.md). Ce contenu introduction fournit l’arrière-plan contextuel pour la série de didacticiels. Il décrit le scénario du didacticiel, et illustre comment les tâches et les procédures pas à pas décrites dans l’ensemble de la série s’intègrent dans un processus d’Application Lifecycle Management (ALM) plus large.
- [Le déploiement de l’entreprise Web](../web-deployment-in-the-enterprise/web-deployment-in-the-enterprise.md). Ce didacticiel fournit une présentation conceptuelle pour les fichiers de projet Microsoft Build Engine (MSBuild), le Pipeline de publication Web, Web Deploy et autres technologies connexes. Il explique comment vous pouvez utiliser ces outils ensemble pour gérer les processus de déploiement complexe.
- [Configuration de Team Foundation Server pour le déploiement Web](../configuring-team-foundation-server-for-web-deployment/configuring-team-foundation-server-for-web-deployment.md). Ce didacticiel explique comment configurer Team Foundation Server (TFS) pour prendre en charge différents scénarios de déploiement, y compris le déploiement automatisé en tant que partie d’un processus d’intégration continue (CI) et déclencher manuellement les déploiements des builds spécifiques.
- [Avancé de déploiement Web d’entreprise](../advanced-enterprise-web-deployment/advanced-enterprise-web-deployment.md). Ce didacticiel explique comment effectuer différentes tâches de déploiement plus avancées, telles que la personnalisation des déploiements de base de données pour plusieurs environnements, à l’exclusion des fichiers et dossiers de déploiement et en prenant des applications web en mode hors connexion pendant le processus de déploiement .

>[!div class="step-by-step"]
[Next](choosing-the-right-approach-to-web-deployment.md)

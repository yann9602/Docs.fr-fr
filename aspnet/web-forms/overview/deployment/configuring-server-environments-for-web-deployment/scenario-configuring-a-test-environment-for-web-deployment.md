---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/scenario-configuring-a-test-environment-for-web-deployment
title: "Scénario : Configuration d’un environnement de Test pour le déploiement Web | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit un scénario de déploiement web par défaut pour le développeur ou environnements de test et décrit les tâches que vous devez effectuer pour configurer un si..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 44a22ac7-1fc7-4174-b946-c6129fb6a19b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/scenario-configuring-a-test-environment-for-web-deployment
msc.type: authoredcontent
ms.openlocfilehash: 008b9cd081152e6a378d0fa2e08497a6771fd9b5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="scenario-configuring-a-test-environment-for-web-deployment"></a>Scénario : Configuration d’un environnement de Test pour le déploiement Web
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit un scénario de déploiement web par défaut pour le développeur ou environnements de test et décrit les tâches que vous devez effectuer pour configurer un environnement similaire.


Lorsque les développeurs travaillent sur des applications web, souvent donnés accès à un environnement de serveur qu’ils peuvent utiliser pour tester les modifications apportées à leurs applications dans un paramètre réaliste. En règle générale, ce type d’environnement de développement ou de test possède les caractéristiques :

- L’environnement se compose d’un serveur web unique et un serveur de base de données unique.
- Les développeurs ont généralement des privilèges d’administrateur sur les serveurs, pour leur permettre de configurer l’environnement sur les exigences de leurs applications.
- Modifications dans les applications sont déployées des intervalles fréquents, par conséquent, l’environnement doit prendre en charge de la seule étape ou déploiement automatisé.

Par exemple, dans notre [scénario du didacticiel](../deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview.md), Matt Hink est un développeur chez Fabrikam, Inc. Matt travaille sur la solution de gestionnaire de contacts et régulièrement doit déployer les modifications dans un environnement de test. Matt est un administrateur sur le serveur web de test et le serveur de base de données de test. Au départ, Matt doit être en mesure de déployer la solution dans l’environnement de test directement.

![](scenario-configuring-a-test-environment-for-web-deployment/_static/image1.png)

En tant que le travail progresse aux développeurs plus de jointure et l’équipe, la solution est configurée pour l’intégration continue (CI) dans Team Foundation Server (TFS) du Gestionnaire de contacts. Chaque fois qu’un développeur vérifie dans le contenu, Team Build doit générer la solution, exécutez des tests unitaires et automatiquement déployer la solution dans l’environnement de test.

![](scenario-configuring-a-test-environment-for-web-deployment/_static/image2.png)

## <a name="solution-overview"></a>Vue d’ensemble de la solution

L’environnement de test doit prendre en charge de la seule étape ou automatisé déploiement à partir d’un ordinateur distant, vous avez le choix entre deux approches principales. Vous pouvez :

- Configurer le serveur web de test pour prendre en charge le déploiement à l’aide du Service de l’Agent de déploiement Web (le « agent à distance »).
- Configurer le serveur web de test pour prendre en charge le déploiement à l’aide du Gestionnaire de déploiement Web.

> [!NOTE]
> Vous pouvez également utiliser [Web déployer à la demande](https://technet.microsoft.com/en-us/library/ee517345(WS.10).aspx) (le « agent temporaire »). Cela est similaire à l’approche de l’agent distant en termes d’exigences et des contraintes.


Dans ce cas, les développeurs ont des privilèges d’administrateur sur les serveurs de destination, et l’environnement de test n’est pas soumis aux contraintes de sécurité strictes, le choix logique pour configurer le serveur web de test pour prendre en charge le déploiement à l’aide de l’agent distant. Cela est moins complexe et requiert une configuration initiale moins que l’approche du Gestionnaire de déploiement Web. Vous devez également configurer votre serveur de base de données pour prendre en charge le déploiement et accès à distance.

Ces rubriques fournissent toutes les informations dont vous avez besoin pour effectuer ces tâches :

- [Configurer un serveur Web pour la publication (Agent distant) de déploiement Web](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md). Cette rubrique décrit comment créer un serveur web qui prend en charge le déploiement Web de publication, à l’aide de l’approche de l’agent distant, à partir d’une génération complète de Windows Server 2008 R2.
- [Configurer un serveur de base de données de publication de déploiement Web](configuring-a-database-server-for-web-deploy-publishing.md). Cette rubrique décrit comment configurer un serveur de base de données pour prendre en charge l’accès à distance et le déploiement, à partir d’une installation par défaut de SQL Server 2008 R2.

## <a name="further-reading"></a>informations supplémentaires

Pour obtenir des conseils sur la configuration d’un environnement intermédiaire classique, consultez [scénario : configuration d’un environnement intermédiaire pour le déploiement Web](scenario-configuring-a-staging-environment-for-web-deployment.md). Pour obtenir des conseils sur la configuration d’un environnement de production typique, consultez [scénario : configuration d’un environnement de Production pour le déploiement Web](scenario-configuring-a-production-environment-for-web-deployment.md).

>[!div class="step-by-step"]
[Précédent](choosing-the-right-approach-to-web-deployment.md)
[Suivant](scenario-configuring-a-staging-environment-for-web-deployment.md)

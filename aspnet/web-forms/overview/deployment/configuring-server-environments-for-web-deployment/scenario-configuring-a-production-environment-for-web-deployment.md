---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/scenario-configuring-a-production-environment-for-web-deployment
title: "Scénario : Configuration d’un environnement de Production pour le déploiement Web | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit un scénario de déploiement web classique pour un environnement de production et décrit les tâches que vous devez effectuer pour configurer un similaire..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 2e861511-450e-4752-a61e-4a01933f9b6e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/scenario-configuring-a-production-environment-for-web-deployment
msc.type: authoredcontent
ms.openlocfilehash: cdd13f96ddf08ff86b01ef9de17ea82cf038ab28
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="scenario-configuring-a-production-environment-for-web-deployment"></a>Scénario : Configuration d’un environnement de Production pour le déploiement Web
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit un scénario de déploiement web classique pour un environnement de production et décrit les tâches que vous devez effectuer pour configurer un environnement similaire.


L’environnement de production constitue la destination finale pour une application web ou un site Web. À ce stade, votre application a été testé, a été déployée dans un environnement intermédiaire et est prête à « mise en ligne ». Les caractéristiques d’un environnement de production peuvent varier considérablement en fonction de la nature et l’objectif de votre contenu web, de la taille de votre organisation, votre public cible et un grand nombre d’autres facteurs. Dans un scénario d’entreprise à l’échelle, l’environnement de production peut avoir ces caractéristiques :

- L’environnement se compose de plusieurs serveurs web d’équilibrage de la charge et un ou plusieurs serveurs de base de données, souvent avec le clustering de basculement et la mise en miroir de base de données.
- Si l’environnement est connecté à Internet, il est susceptible d’être séparés à partir de votre réseau interne. Il peut être sur un autre sous-réseau dans un réseau de périmètre, il peut être sur un autre domaine, et il peut être sur une infrastructure réseau complètement différent.
- Les développeurs et les comptes de processus de serveur de build sont peu susceptibles d’avoir des privilèges d’administrateur sur les serveurs de production.
- Modifications dans les applications sont déployées sur les moins fréquemment que test ou des déploiements intermédiaires.

> [!NOTE]
> Montée en puissance parallèle d’un déploiement de base de données sur plusieurs serveurs est dépasse le cadre de ce didacticiel. Pour plus d’informations sur cette zone, consultez [la documentation en ligne de SQL Server](https://technet.microsoft.com/library/ms130214.aspx).


Par exemple, dans notre [scénario du didacticiel](../deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview.md), un serveur Team Build inclut les définitions de build qui permettent aux utilisateurs de générer la solution de gestionnaire de contacts et le déployer dans un environnement intermédiaire en une seule étape. Lorsque l’application est prête à être déployée en production, en raison de contraintes imposées par les exigences de sécurité et de l’infrastructure réseau, l’administrateur d’environnement de production doit copier le package web sur un serveur web de production et importez manuellement Il via le Gestionnaire des Services Internet (IIS).

![](scenario-configuring-a-production-environment-for-web-deployment/_static/image1.png)

## <a name="solution-overview"></a>Vue d’ensemble de la solution

Dans ce scénario, vous pouvez déduire ces faits d’une analyse de la configuration requise du déploiement :

- En raison de restrictions de sécurité et la configuration du réseau, vous ne pouvez pas configurer l’environnement de production pour prendre en charge le déploiement d’un clic ou automatisé. Le déploiement en mode hors connexion est la seule approche viable dans ce scénario.
- L’environnement de production comprend plusieurs serveurs web, vous pouvez utiliser Web Farm Framework (WFF) pour créer une batterie de serveurs. À l’aide de cette approche, l’administrateur doit uniquement importer l’application sur un serveur web (le serveur principal) et WFF réplique le déploiement sur tous les autres serveurs de web dans l’environnement de production.

Ces rubriques fournissent toutes les informations dont vous avez besoin pour effectuer ces tâches :

- [Créer une batterie de serveurs avec l’infrastructure de batterie de serveurs Web](configuring-a-database-server-for-web-deploy-publishing.md). Cette rubrique décrit comment créer et configurer une batterie de serveurs à l’aide de WFF, afin que les produits de plateforme web et composants, paramètres de configuration et des sites Web et applications sont répliquées sur plusieurs serveurs web d’équilibrage de la charge.
- [Configurer un serveur Web pour la publication (déploiement en mode hors connexion) de déploiement Web](configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md). Cette rubrique décrit comment créer un serveur web qui permet aux administrateurs importer et déploiement des packages web manuellement, en commençant à partir d’une génération complète de Windows Server 2008 R2.
- [Configurer un serveur de base de données de publication de déploiement Web](configuring-a-database-server-for-web-deploy-publishing.md). Cette rubrique décrit comment configurer un serveur de base de données pour prendre en charge l’accès à distance et le déploiement, à partir d’une installation par défaut de SQL Server 2008 R2.

## <a name="further-reading"></a>informations supplémentaires

Pour obtenir des conseils sur la configuration d’un environnement de test classique de développeur, consultez [scénario : configuration d’un environnement de Test pour le déploiement Web](scenario-configuring-a-test-environment-for-web-deployment.md). Pour obtenir des conseils sur la configuration d’un environnement intermédiaire classique, consultez [scénario : configuration d’un environnement intermédiaire pour le déploiement Web](scenario-configuring-a-staging-environment-for-web-deployment.md).

>[!div class="step-by-step"]
[Précédent](scenario-configuring-a-staging-environment-for-web-deployment.md)
[Suivant](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md)

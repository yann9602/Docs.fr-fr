---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/scenario-configuring-a-staging-environment-for-web-deployment
title: "Scénario : Configuration d’un environnement intermédiaire pour le déploiement Web | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit un scénario de déploiement web classique pour un environnement intermédiaire et décrit les tâches que vous devez effectuer pour configurer un environnement similaire..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 5a8e49b7-5317-4125-b107-7e2466b47bb3
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/scenario-configuring-a-staging-environment-for-web-deployment
msc.type: authoredcontent
ms.openlocfilehash: 683a0cf88225fee762e82925afe3785a2defd5bf
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="scenario-configuring-a-staging-environment-for-web-deployment"></a>Scénario : Configuration d’un environnement intermédiaire pour le déploiement Web
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit un scénario de déploiement web classique pour un environnement intermédiaire et décrit les tâches que vous devez effectuer pour configurer un environnement similaire.


Un grand nombre d’organisations utilisent des environnements intermédiaires pour afficher un aperçu des mises à jour des applications web ou des sites Web. Cela permet de personnes de l’organisation pour Explorer et passez en revue les nouvelles fonctionnalités ou du contenu avant que le site « publié », ou en d’autres termes, est déployé dans un environnement de production. L’environnement intermédiaire est conçu pour répliquer l’environnement de production aussi fidèlement que possible, afin de fournir un aperçu réaliste. Ce type d’environnement intermédiaire a généralement ces caractéristiques :

- L’environnement se compose de plusieurs serveurs web d’équilibrage de la charge et un ou plusieurs serveurs de base de données, souvent avec le clustering de basculement et la mise en miroir de base de données.
- Les applications peuvent être déployées manuellement par une équipe de développement ou automatiquement par un serveur Team Build.
- Les utilisateurs ou les comptes de processus que vous déploient des applications sont peu susceptibles d’avoir des privilèges d’administrateur sur les serveurs intermédiaires.
- Modifications dans les applications sont déployées des intervalles fréquents, par conséquent, l’environnement doit prendre en charge de la seule étape ou déploiement automatisé.

> [!NOTE]
> Montée en puissance parallèle d’un déploiement de base de données sur plusieurs serveurs est dépasse le cadre de ce didacticiel. Pour plus d’informations sur cette zone, consultez [la documentation en ligne de SQL Server](https://technet.microsoft.com/library/ms130214.aspx).


Par exemple, dans notre [scénario du didacticiel](../deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview.md), Team Foundation Server (TFS) gère la solution de gestionnaire de contacts. L’administrateur TFS, Rob Walters, a créé une définition de build qui permet aux développeurs de déclencher un déploiement dans l’environnement intermédiaire en fonction des besoins.

![](scenario-configuring-a-staging-environment-for-web-deployment/_static/image1.png)

Notez que dans la plupart des cas, vous ne sont pas nécessairement déployer la build la plus récente dans l’environnement intermédiaire. Au lieu de cela, vous risquez beaucoup plus de déployer une build spécifique qui a déjà subi vérification dans l’environnement de test et la validation.

## <a name="solution-overview"></a>Vue d’ensemble de la solution

Dans ce scénario, vous pouvez déduire ces faits d’une analyse de la configuration requise du déploiement :

- Le compte d’utilisateur ou un processus qui effectue le déploiement ne dispose des privilèges administrateur sur les serveurs intermédiaires, les serveurs web intermédiaire doivent prendre en charge le déploiement des non administrateurs. Par conséquent, vous devez configurer les serveurs web intermédiaire pour utiliser le Gestionnaire de déploiement Web au lieu de l’agent distant.
- L’environnement intermédiaire inclut plusieurs serveurs web, mais il doit prendre en charge de déploiement en un clic ou automatisé, vous devez utiliser Web Farm Framework (WFF) pour créer une batterie de serveurs. À l’aide de cette approche, vous pouvez déployer une application sur un serveur web (le serveur principal) et WFF réplique le déploiement sur tous les autres serveurs de web dans l’environnement intermédiaire.
- Le compte d’utilisateur ou processus qui exécute le déploiement doit avoir les autorisations nécessaires pour créer des bases de données. Par conséquent, vous devez ajouter le compte à la **dbcreator** rôle de serveur sur le serveur de base de données, en plus de la configuration du serveur de base de données pour prendre en charge l’accès à distance et le déploiement.

Ces rubriques fournissent toutes les informations dont vous avez besoin pour effectuer ces tâches :

- [Créer une batterie de serveurs avec l’infrastructure de batterie de serveurs Web](creating-a-server-farm-with-the-web-farm-framework.md). Cette rubrique décrit comment créer et configurer une batterie de serveurs à l’aide de WFF, afin que les produits de plateforme web et composants, paramètres de configuration et des sites Web et applications sont répliquées sur plusieurs serveurs web d’équilibrage de la charge.
- [Configurer un serveur Web pour la publication de déploiement Web (Gestionnaire de déploiement Web)](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler.md). Cette rubrique décrit comment créer un serveur web qui prend en charge le déploiement Web de publication, à l’aide de l’approche de l’agent distant, à partir d’une génération complète de Windows Server 2008 R2.
- [Configurer un serveur de base de données de publication de déploiement Web](configuring-a-database-server-for-web-deploy-publishing.md). Cette rubrique décrit comment configurer un serveur de base de données pour prendre en charge l’accès à distance et le déploiement, à partir d’une installation par défaut de SQL Server 2008 R2.

## <a name="further-reading"></a>informations supplémentaires

Pour obtenir des conseils sur la configuration d’un environnement de test classique de développeur, consultez [scénario : configuration d’un environnement de Test pour le déploiement Web](scenario-configuring-a-test-environment-for-web-deployment.md). Pour obtenir des conseils sur la configuration d’un environnement de production typique, consultez [scénario : configuration d’un environnement de Production pour le déploiement Web](scenario-configuring-a-production-environment-for-web-deployment.md).

>[!div class="step-by-step"]
[Précédent](scenario-configuring-a-test-environment-for-web-deployment.md)
[Suivant](scenario-configuring-a-production-environment-for-web-deployment.md)

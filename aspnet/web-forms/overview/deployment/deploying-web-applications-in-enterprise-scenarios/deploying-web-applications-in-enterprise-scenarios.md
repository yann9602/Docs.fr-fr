---
uid: web-forms/overview/deployment/deploying-web-applications-in-enterprise-scenarios/deploying-web-applications-in-enterprise-scenarios
title: "Déployer des Applications Web dans les scénarios d’entreprise à l’aide de Visual Studio 2010 | Documents Microsoft"
author: jrjlee
description: "Cet ensemble de didacticiels décrit les outils et techniques que vous pouvez utiliser pour déployer des applications web dans différents scénarios d’entreprise. Cette rubrique explique comment utiliser mieux..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/03/2012
ms.topic: article
ms.assetid: 48cfe378-d62a-48c6-a4db-6be3cead6898
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/deploying-web-applications-in-enterprise-scenarios/deploying-web-applications-in-enterprise-scenarios
msc.type: authoredcontent
ms.openlocfilehash: 99bab371dd34b30f3554843e49bbec7f57c3f96c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-web-applications-in-enterprise-scenarios-using-visual-studio-2010"></a>Déployer des Applications Web dans les scénarios d’entreprise à l’aide de Visual Studio 2010
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cet ensemble de didacticiels décrit les outils et techniques que vous pouvez utiliser pour déployer des applications web dans différents scénarios d’entreprise. Il explique comment mieux utiliser des technologies telles que Visual Studio 2010, Microsoft Build Engine (MSBuild), Internet Information Services (IIS) 7.5, l’outil de déploiement Web IIS (Web Deploy), le Web Farm Framework (WFF) et utilitaires comme VSDBCMD.exe à simplifier et gérer le processus de déploiement. Il inclut une vue d’ensemble conceptuelle et des conseils de tâches qui vous aideront à :
> 
> - Passez en revue et établir les exigences de déploiement pour une application web d’entreprise.
> - Configurer des environnements de serveur web test, intermédiaire et de production pour prendre en charge le déploiement web.
> - Configurer le processus d’intégration continue (CI) de Team Foundation Server (TFS) pour prendre en charge le déploiement web automatisée.
> - Déployer des applications web de l’échelle de l’entreprise dans les environnements de serveur différents avec des exigences et restrictions.
> - Déployer les modifications sur les applications web qui sont exécutent dans différents environnements serveur.
> 
> > [!NOTE]
> > Alors que ces didacticiels décrivent l’utilisation de TFS en tant que l’élément de configuration serveur, les instructions sont facilement adaptée à n’importe quel serveur de l’élément de configuration. Vous n’avez pas besoin une connaissance détaillée de TFS pour comprendre et exploiter les didacticiels.
> 
> 
> Pour obtenir une traduction italienne de ces didacticiels, visitez [http://www.lucamorelli.it](http://www.lucamorelli.it).


## <a name="about-the-authors"></a>À propos des auteurs

Jason Lee est technicien principal avec [contenu Master](http://www.contentmaster.com/) où il a travaillé avec des produits et technologies, notamment SharePoint et ASP.NET, Microsoft depuis plusieurs années. Jason conserve un bloc dans le calcul et est actuellement MCPD et MCTS certifié. Vous pouvez lire le blog de techniques de Jason à [www.jrjlee.com](http://www.jrjlee.com/).

Benjamin Curry est technicien principal avec [contenu Master](http://www.contentmaster.com/) qui a écrit des livres blancs, documentation du Kit de développement logiciel, des présentations PowerPoint et des formations en ligne et par un formateur lors de sa carrière. Un membre d’origine de l’équipe de documentation d’ASP.NET, il a travaillé sur les technologies web de Microsoft pour plus de dix ans.

## <a name="target-audience"></a>Public visé

Ce jeu de didacticiels est pour les développeurs d’applications web ASP.NET et aux architectes de solutions qui utilisent Visual Studio 2010 pour créer des applications web de l’échelle de l’entreprise. Pour tirer le meilleur parti du contenu, vous devez être familiarisé avec Visual Studio 2010 et avoir une connaissance élémentaire de TFS, ainsi que d’une connaissance des technologies de plateforme web Microsoft ASP.NET MVC 3, Windows Communication Foundation (WCF), IIS, SQL Serveur et les projets de base de données Visual Studio. Toutefois, vous n’avez pas besoin être familiarisé avec les technologies et outils de déploiement ou de savoir comment configurer des systèmes de l’élément de configuration.

## <a name="requirements"></a>Spécifications

Pour suivre les procédures pas à pas et effectuer les tâches décrivant ces didacticiels, vous devez installer ce logiciel sur votre ordinateur de développement :

- Visual Studio 2010 Premium ou Édition intégrale avec Service Pack 1
- .NET framework 4.0
- .NET framework 3.5 Service Pack 1
- ASP.NET MVC 3.0
- IIS 7.5 Express
- SQL Server Express 2008 R2

Pour effectuer les étapes de déploiement décrites dans l’ensemble de ces procédures pas à pas, vous devez accéder aux environnements de déploiement application exemple Web. Pour de meilleurs résultats, ces environnements doivent refléter modèle de déploiement d’entreprise de votre organisation. Vous pouvez ensuite modifier les procédures décrites dans cette documentation pour refléter les environnements de déploiement et les exigences de votre organisation.

## <a name="series-contents"></a>Contenu de la série

Cette section d’introduction est composé des deux rubriques supplémentaires. Ils sont conçus pour fournir un contexte plus large pour les didacticiels qui suivent :

- [Déploiement Web d’entreprise : Vue d’ensemble du scénario](enterprise-web-deployment-scenario-overview.md). Cette rubrique décrit le scénario qui sous-tend chacun des didacticiels de cette série. Le scénario se concentre sur les spécifications d’Application Lifecycle Management (ALM) d’une société fictive nommée Fabrikam, Inc. comme il développe une application web d’entreprise.
- [Gestion du cycle de vie des applications : Du développement jusqu'à la Production](application-lifecycle-management-from-development-to-production.md). Cette rubrique fournit une vue d’ensemble de haut niveau, de bout en bout d’un processus de déploiement. Il montre comment Fabrikam, Inc. se déplace d’une application de web ASP.NET à l’échelle de l’entreprise dans les environnements de test, intermédiaire et de production dans le cadre d’un processus de développement continu.

La série comprend quatre jeux de didacticiel. Chacun se concentre sur les différents aspects de déploiement web :

- [Le déploiement de l’entreprise Web](../web-deployment-in-the-enterprise/web-deployment-in-the-enterprise.md). Ce didacticiel fournit une présentation conceptuelle pour les fichiers projet MSBuild, le Pipeline de publication Web, Web Deploy et autres technologies connexes. Il explique comment vous pouvez utiliser ces outils ensemble pour gérer les processus de déploiement complexe.
- [Configuration d’environnements de serveur pour le déploiement Web](../configuring-server-environments-for-web-deployment/configuring-server-environments-for-web-deployment.md). Ce didacticiel explique comment configurer les serveurs Windows pour prendre en charge différents scénarios de déploiement, y compris le déploiement du package web à distance à l’aide le Service de l’Agent de déploiement Web (le « agent à distance ») ou le Gestionnaire de déploiement Web et le déploiement de la base de données distante. Il fournit des conseils sur le choix de la méthode de déploiement appropriée pour votre propre environnement, et décrit comment utiliser le WFF pour répliquer les applications web déployées sur tous les serveurs web dans une batterie de serveurs.
- [Configuration de Team Foundation Server pour le déploiement Web](../configuring-team-foundation-server-for-web-deployment/configuring-team-foundation-server-for-web-deployment.md). Ce didacticiel explique comment configurer TFS pour prendre en charge différents scénarios de déploiement, notamment le déploiement automatisé en tant que partie d’un processus de l’élément de configuration et de déclencher manuellement les déploiements des builds spécifiques.
- [Avancé de déploiement Web d’entreprise](../advanced-enterprise-web-deployment/advanced-enterprise-web-deployment.md). Ce didacticiel explique comment effectuer différentes tâches de déploiement plus avancées, telles que la personnalisation des déploiements de base de données pour plusieurs environnements, à l’exclusion des fichiers et dossiers de déploiement et en prenant des applications web en mode hors connexion pendant le processus de déploiement .

## <a name="where-to-start"></a>Par où commencer

Cet ensemble de didacticiels utilise un exemple de solution avec un niveau réaliste de complexité, ainsi que d’un scénario de déploiement d’une entreprise fictive, pour fournir une implémentation de référence et pour donner les tâches et les procédures pas à pas un contexte commun. La rubrique suivante, [déploiement Web d’entreprise : vue d’ensemble du scénario](enterprise-web-deployment-scenario-overview.md), présente le scénario et l’exemple de solution. À partir de là, vous pouvez travailler dans les didacticiels et les rubriques qui correspondent le mieux à vos besoins.

>[!div class="step-by-step"]
[Next](enterprise-web-deployment-scenario-overview.md)

---
uid: web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/configuring-team-foundation-server-for-web-deployment
title: "Configuration de Team Foundation Server pour le déploiement Web | Documents Microsoft"
author: jrjlee
description: "Ce didacticiel vous indiquera comment configurer Team Foundation Server (TFS) 2010 pour créer des solutions et de déployer le contenu web pour les environnements cibles différents. Cela..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: ff55233a-e795-4007-a4fc-861fe1bb590b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/configuring-team-foundation-server-for-web-deployment
msc.type: authoredcontent
ms.openlocfilehash: 72f60841a1381380c0ea6167077420f960180dc7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="configuring-team-foundation-server-for-web-deployment"></a>Configuration de Team Foundation Server pour le déploiement Web
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Ce didacticiel vous indiquera comment configurer Team Foundation Server (TFS) 2010 pour créer des solutions et de déployer le contenu web pour les environnements cibles différents. Cela inclut des scénarios d’intégration continue (CI), où vous déployez automatiquement du contenu chaque fois qu’un développeur apporte une modification. Il peut également inclure des scénarios de déclenchement manuel, où un administrateur peut vouloir déclencher le déploiement d’une build spécifique dans un environnement intermédiaire une fois la build a été vérifiée et validée dans l’environnement de test. Les rubriques de ce didacticiel va vous guider dans le processus d’ensemble de la configuration, y compris :
> 
> - Comment créer un nouveau projet d’équipe dans TFS.
> - Comment ajouter du contenu à un contrôle de code source.
> - Comment configurer un serveur de builds pour prendre en charge de l’élément de configuration et de déploiement.
> - Comment créer une définition de build qui inclut la logique de déploiement.
> - Comment configurer des autorisations pour un déploiement automatisé.
> 
> Pour obtenir une traduction italienne de ces didacticiels, visitez [http://www.lucamorelli.it](http://www.lucamorelli.it).


Ce didacticiel suppose que vous avez installé TFS 2010 et créé une collection de projets d’équipe dans le cadre du processus de configuration initiale. Le [Guide d’Installation de Team Foundation pour Visual Studio 2010](https://go.microsoft.com/?linkid=9805132) fournit des conseils détaillés sur ces tâches.

## <a name="context"></a>Contexte

Cela constitue une partie d’une série de didacticiels selon les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md) solution & #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md), dans lequel le processus de génération est contrôlé par deux fichiers de & projet #x 2014 ; un contenant les instructions qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement de génération. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="scenario-overview"></a>Vue d’ensemble du scénario

Le scénario de haut niveau pour ces didacticiels est décrit dans [déploiement Web d’entreprise : vue d’ensemble du scénario](../deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview.md). Nous vous recommandons de consulter cette rubrique avant de commencer ce didacticiel.

## <a name="how-to-use-this-tutorial"></a>Comment utiliser ce didacticiel

Si c’est la première fois que vous avez effectué les tâches décrites dans ce didacticiel, ou si vous souhaitez suivre les exemples à l’aide de l’exemple de solution, vous devez utiliser les rubriques didacticiels dans l’ordre. Vous pouvez également utiliser des rubriques comme guide pour des tâches spécifiques. Ce didacticiel inclut les rubriques suivantes :

- [Création d’un projet d’équipe dans TFS](creating-a-team-project-in-tfs.md). Un projet d’équipe est l’unité de base pour le contrôle de code source, la gestion des processus et build dans TFS. Vous devez créer un projet d’équipe avant que vous pouvez ajouter du contenu pour le contrôle de code source ou de créer des définitions de build.
- [Ajout de contenu au contrôle de code Source](adding-content-to-source-control.md). Une fois que vous avez créé un projet d’équipe, vous pouvez commencer à ajouter du contenu à un contrôle de code source. Vous devez ajouter vos projets et solutions, ainsi que toutes les dépendances externes, avant que vous pouvez commencer à configurer des builds.
- [Configuration d’un serveur TFS du serveur de builds pour le déploiement Web](configuring-a-tfs-build-server-for-web-deployment.md). Si vous souhaitez générer le contenu de votre projet d’équipe, vous devez configurer un serveur de builds. Dans la plupart des cas, il doit s’agir sur un ordinateur distinct à partir de votre installation de TFS. Pour configurer un serveur de builds, vous devez installer et configurer le service de build TFS, installez Visual Studio 2010, créer des contrôleurs de build et agents de build, installer les produits ou les composants dont votre code a besoin pour générer avec succès et installer le Internet Information Services (IIS) Web d’outil de déploiement (Web Deploy).
- [Création d’une définition de Build qui prend en charge le déploiement](creating-a-build-definition-that-supports-deployment.md). Avant de commencer queuing ou déclencher des builds dans TFS, vous devez créer la définition de build au moins un pour votre projet d’équipe. La définition de build définit tous les aspects de la build, y compris les éléments doivent être inclus dans la build et ce qui doit déclencher la build Team Build où doit envoyer les sorties de génération. Vous pouvez configurer une définition de build pour exécuter des fichiers de projet personnalisés Microsoft Build Engine (MSBuild), ce qui vous permet d’inclure la logique de déploiement dans vos builds automatisées.
- [Déploiement d’une Build spécifique](deploying-a-specific-build.md). Dans de nombreux scénarios, vous souhaiterez déployer une build spécifique, plutôt que la build la plus récente, à un environnement cible. Dans ce cas, vous pouvez configurer une définition de build qui déploie le contenu d’un dossier cible spécifique.
- [Déploiement de la configuration des autorisations de Team Build](configuring-permissions-for-team-build-deployment.md). Si le service de build est de déployer du contenu dans le cadre d’un processus de génération automatisé, vous devez accorder des autorisations différentes pour le compte de service de build sur tous les serveurs web de destination et les serveurs de base de données.

## <a name="key-technologies"></a>Technologies de clé

Ce didacticiel se concentre sur l’utilisation de ces produits et technologies pour prendre en charge la génération automatique et déploiement web :

- Visual Studio Team Foundation Server 2010
- Team Build et MSBuild
- Web Deploy

Il aborde également le didacticiel sur l’utilisation de Windows Server 2008 R2, IIS 7.5, SQL Server 2008 R2, ASP.NET 4.0 et ASP.NET MVC 3.

## <a name="other-tutorials-in-this-series"></a>Autres didacticiels de cette série

Cela fait partie d’une série de cinq didacticiels sur le déploiement web de l’échelle de l’entreprise. Voici les autres didacticiels dans la série :

- [Déployer des Applications Web dans les scénarios d’entreprise](../deploying-web-applications-in-enterprise-scenarios/deploying-web-applications-in-enterprise-scenarios.md). Ce contenu introduction fournit l’arrière-plan contextuel pour la série de didacticiels. Il décrit le scénario du didacticiel, et illustre comment les tâches et les procédures pas à pas décrites dans l’ensemble de la série s’intègrent dans un processus d’Application Lifecycle Management (ALM) plus large.
- [Le déploiement de l’entreprise Web](../web-deployment-in-the-enterprise/web-deployment-in-the-enterprise.md). Ce didacticiel fournit une présentation conceptuelle pour les fichiers projet MSBuild, le Pipeline de publication Web (WPP), Web Deploy et autres technologies connexes. Il explique comment vous pouvez utiliser ces outils ensemble pour gérer les processus de déploiement complexe.
- [Configuration d’environnements de serveur pour le déploiement Web](../configuring-server-environments-for-web-deployment/configuring-server-environments-for-web-deployment.md). Ce didacticiel explique comment configurer les serveurs Windows pour prendre en charge différents scénarios de déploiement, y compris le déploiement du package web à distance à l’aide le Service de l’Agent de déploiement Web (l’agent distant) ou le Gestionnaire de déploiement Web et le déploiement de la base de données distante. Il fournit des conseils sur le choix de la méthode de déploiement appropriée pour votre propre environnement, et décrit l’utilisation de Web Farm Framework (WFF) pour répliquer les applications web déployées sur tous les serveurs web dans une batterie de serveurs.
- [Avancé de déploiement Web d’entreprise](../advanced-enterprise-web-deployment/advanced-enterprise-web-deployment.md). Ce didacticiel explique comment effectuer différentes tâches de déploiement plus avancées, telles que la personnalisation des déploiements de base de données pour plusieurs environnements, à l’exclusion des fichiers et dossiers de déploiement et en prenant des applications web en mode hors connexion pendant le processus de déploiement .

>[!div class="step-by-step"]
[Next](creating-a-team-project-in-tfs.md)

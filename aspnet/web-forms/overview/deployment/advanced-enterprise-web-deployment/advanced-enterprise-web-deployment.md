---
uid: web-forms/overview/deployment/advanced-enterprise-web-deployment/advanced-enterprise-web-deployment
title: "Avancé de déploiement Web d’entreprise | Documents Microsoft"
author: jrjlee
description: "Ce didacticiel vous indiquera comment effectuer diverses tâches requises ou souhaitable dans de nombreux scénarios de déploiement d’entreprise. Pour un translati italien..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 7dcaba80-f2ec-4db3-ad98-daadc3afdb49
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/advanced-enterprise-web-deployment/advanced-enterprise-web-deployment
msc.type: authoredcontent
ms.openlocfilehash: c3cb7f63cf7c0246a0c4da6038a65a6ac43a7b59
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="advanced-enterprise-web-deployment"></a>Déploiement de Web avancées d’entreprise
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Ce didacticiel vous indiquera comment effectuer diverses tâches requises ou souhaitable dans de nombreux scénarios de déploiement d’entreprise.
> 
> Pour obtenir une traduction italienne de ces didacticiels, visitez [http://www.lucamorelli.it](http://www.lucamorelli.it).


Cela constitue une partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md) solution & #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md), dans lequel le processus de génération est contrôlé par deux fichiers de & projet #x 2014 ; un contenant les instructions qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement de génération. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="scenario-overview"></a>Vue d’ensemble du scénario

Le scénario de haut niveau pour ces didacticiels est décrit dans [déploiement Web d’entreprise : vue d’ensemble du scénario](../deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview.md). Nous vous recommandons de consulter cette rubrique avant de commencer ce didacticiel.

## <a name="how-to-use-this-tutorial"></a>Comment utiliser ce didacticiel

- Chacune des rubriques de ce didacticiel est autonome et adresse un défi particulier ou un problème qui se produit dans les scénarios de déploiement d’entreprise. Vous n’avez pas besoin de parcourir les rubriques suivantes dans un ordre particulier. Toutefois, ce didacticiel décrit certaines des tâches avancées. Par conséquent, vous devez vous familiariser avec les concepts et techniques qui le [déploiement Web de l’entreprise](../web-deployment-in-the-enterprise/web-deployment-in-the-enterprise.md) didacticiel couvre afin d’obtenir le meilleur parti de ce contenu.
- Ce didacticiel inclut les rubriques suivantes :
- [Exécution d’un déploiement « Que se passe-t-il si »](performing-a-what-if-deployment.md). Dans de nombreux scénarios, vous souhaiterez déterminer l’impact d’un déploiement proposé sur un environnement cible ou le contenu existant avant de procéder réellement des modifications. Cette rubrique décrit comment vous pouvez exécuter un déploiement « que se passe-t-il si » pour générer des fichiers journaux et les scripts de mise à jour de base de données comme si vous aviez déployé contenu à un environnement cible, sans réellement apporter de modifications. Analyse de ces ressources peut vous aider à repérer les problèmes potentiels avant un déploiement.
- [Personnalisation des déploiements de base de données pour plusieurs environnements](customizing-database-deployments-for-multiple-environments.md). Lorsque vous déployez un projet de base de données vers plusieurs destinations, vous souhaiterez souvent personnaliser les propriétés de déploiement pour chaque environnement cible. Par exemple, dans les environnements de test vous serez généralement recréer la base de données sur chaque déploiement, alors que dans les environnements de production ou de vous être beaucoup plus susceptibles d’effectuer des mises à jour incrémentielles pour préserver vos données. Cette rubrique décrit comment vous pouvez incorporer ces modifications de propriété dans votre logique de déploiement en créant un fichier de configuration (.sqldeployment) spécifiques à l’environnement de déploiement pour chaque environnement cible.
- Appartenances aux rôles de base de données à déployer dans des environnements de Test. Lorsque vous recréez une base de données sur chaque #x 2014 et le déploiement ; par exemple, dans le cadre d’une build d’intégration continue (CI) et que vous déployez sur un environnement de test #x 2014 ; vous devez généralement configurer les appartenances aux rôles de base de données chaque fois. Par exemple, vous devez généralement accorder des autorisations à l’identité de pool d’applications associé à votre application web. Cette rubrique décrit comment vous pouvez automatiser ce processus en ajoutant un script SQL de post-déploiement pour votre logique de déploiement.
- [Déploiement de bases de données d’appartenance pour les environnements d’entreprise](deploying-membership-databases-to-enterprise-environments.md). Bases de données d’appartenance ASP.NET ont différentes caractéristiques susceptibles de compliquer le processus de déploiement. Par exemple, un déploiement de schéma uniquement sera laisse la base de données dans un état non opérationnel. Dans la plupart des scénarios, il est préférable de créer une base de données d’appartenance directement dans chaque environnement de destination. Toutefois, si vous n’avez pas à déployer une base de données d’appartenance, cette rubrique décrit certaines des méthodes que vous pouvez utiliser pour relever les défis inhérents.
- [Exclusion de fichiers et dossiers de déploiement](excluding-files-and-folders-from-deployment.md). Dans certains scénarios, que vous souhaitez personnaliser le contenu de votre package web dans les environnements de destination spécifique. Par exemple, vous souhaiterez incluent les versions complètes de bibliothèques JavaScript lorsque vous déployez dans un environnement de test, prennent en charge le débogage côté client, mais d’utiliser des versions réduites des bibliothèques lorsque vous déployez dans un environnement intermédiaire ou de production. Cette rubrique décrit comment vous pouvez exclure certains fichiers et dossiers à partir du processus de création de package.
- [Déploiement des Applications Web prise en mode hors connexion avec Web](taking-web-applications-offline-with-web-deploy.md). Lorsque vous déployez des solutions dans un environnement intermédiaire ou de production, vous souhaiterez souvent prendre vos applications web en mode hors connexion pendant la durée du processus de déploiement. Cette rubrique décrit comment vous pouvez ajouter un *application\_offline.htm* de fichiers à votre application web au début du processus de déploiement et le supprimer à la fin. Alors que le *application\_offline.htm* fichier est en place, les utilisateurs qui accèdent à l’application web sont automatiquement redirigés vers le *application\_offline.htm* fichier.
- [En cours d’exécution de Scripts Windows PowerShell à partir de MSBuild](running-windows-powershell-scripts-from-msbuild-project-files.md). De nombreux scénarios de déploiement requièrent des actions de post-déploiement plus complexes, telles que l’ajout de sources d’événement personnalisés dans le Registre ou la configuration de la réplication entre des instances de SQL Server. Ces actions sont souvent obtenues via des scripts Windows PowerShell. Cette rubrique décrit comment exécuter des scripts Windows PowerShell à partir d’un fichier de projet Microsoft Build Engine (MSBuild) dans le cadre du processus de génération et de déploiement.
- [Dépannage du processus d’empaquetage](troubleshooting-the-packaging-process.md). Le Pipeline de publication Web (WPP) définit une propriété MSBuild nommée **EnablePackageProcessLoggingAndAssert** que vous pouvez utiliser pour générer des informations détaillées sur le processus d’empaquetage pour les projets d’application web. Cette rubrique décrit ce que fait la propriété et comment l’utiliser.

## <a name="key-technologies"></a>Technologies de clé

Ce didacticiel se concentre sur l’utilisation de ces produits et technologies pour prendre en charge la génération automatique et déploiement web :

- Visual Studio 2010 et Team Foundation Server (TFS) 2010
- MSBuild et Build d’équipe TFS
- Internet Information Services (IIS) 7.5
- Outil de déploiement de Web IIS (Web Deploy) 2.1
- L’utilitaire de déploiement de base de données VSDBCMD.exe

## <a name="other-tutorials-in-this-series"></a>Autres didacticiels de cette série

Cela fait partie d’une série de cinq didacticiels sur le déploiement web de l’échelle de l’entreprise. Voici d’autres didacticiels dans la série :

- [Déployer des Applications Web dans les scénarios d’entreprise](../deploying-web-applications-in-enterprise-scenarios/deploying-web-applications-in-enterprise-scenarios.md). Ce contenu introduction fournit l’arrière-plan contextuel pour la série de didacticiels. Il décrit le scénario du didacticiel, et illustre comment les tâches et les procédures pas à pas décrites dans l’ensemble de la série s’intègrent dans un processus d’Application Lifecycle Management (ALM) plus large.
- [Le déploiement de l’entreprise Web](../web-deployment-in-the-enterprise/web-deployment-in-the-enterprise.md). Ce didacticiel fournit une présentation conceptuelle pour les fichiers projet MSBuild, les fournisseurs de services, Web Deploy et autres technologies connexes. Il explique comment vous pouvez utiliser ces outils ensemble pour gérer les processus de déploiement complexe.
- [Configuration d’environnements de serveur pour le déploiement Web](../configuring-server-environments-for-web-deployment/configuring-server-environments-for-web-deployment.md). Ce didacticiel explique comment configurer les serveurs Windows pour prendre en charge différents scénarios de déploiement, y compris le déploiement du package web à distance à l’aide le Service de l’Agent de déploiement Web (l’agent distant) ou le Gestionnaire de déploiement Web et le déploiement de la base de données distante. Il fournit des conseils sur le choix de la méthode de déploiement appropriée pour votre propre environnement, et décrit l’utilisation de Web Farm Framework (WFF) pour répliquer les applications web déployées sur tous les serveurs web dans une batterie de serveurs.
- [Configuration de Team Foundation Server pour le déploiement Web](../configuring-team-foundation-server-for-web-deployment/configuring-team-foundation-server-for-web-deployment.md). Ce didacticiel explique comment configurer TFS pour prendre en charge différents scénarios de déploiement, notamment le déploiement automatisé en tant que partie d’un processus de l’élément de configuration et de déclencher manuellement les déploiements des builds spécifiques.

>[!div class="step-by-step"]
[Next](performing-a-what-if-deployment.md)

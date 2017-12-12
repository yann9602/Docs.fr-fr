---
uid: web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/configuring-permissions-for-team-build-deployment
title: "Déploiement de la configuration des autorisations de Team Build | Documents Microsoft"
author: jrjlee
description: "Cette rubrique explique comment configurer les autorisations requises pour activer votre serveur de builds déployer du contenu vers les serveurs web et serveurs de base de données dans le cadre d’un b automatisé..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 2488a91e-b0a8-465a-b874-3233f724b56b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/configuring-permissions-for-team-build-deployment
msc.type: authoredcontent
ms.openlocfilehash: cb3d013d69e36f97335ea31dd6e4997772ba2d8e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="configuring-permissions-for-team-build-deployment"></a>Déploiement de la configuration des autorisations de Team Build
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment configurer des autorisations pour activer votre serveur de builds déployer du contenu vers les serveurs web et serveurs de base de données dans le cadre d’un processus de génération automatisé.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md), dans lequel le processus de génération est contrôlé par deux fichiers de & projet #x 2014 ; un contenant les instructions qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement de génération. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="task-overview"></a>Vue d’ensemble de la tâche

Lorsque vous installez le service de build de 2010 Team Foundation Server (TFS), vous spécifiez l’identité avec laquelle vous souhaitez exécuter le service. Par défaut, il s’agit du compte Service réseau. Vous pouvez également configurer le service de build à exécuter à l’aide d’un compte de domaine.

Les tâches de déploiement qui nécessitent l’authentification Windows et que vous prévoyez d’automatiser à l’aide de Team Build, seront exécute à l’aide de l’identité de service de build. Par conséquent, vous devez accorder à l’identité de service de build toutes les autorisations requises sur vos serveurs web et vos serveurs de base de données.

> [!NOTE]
> Le compte Service réseau utilise le compte d’ordinateur pour s’authentifier auprès d’autres ordinateurs. Comptes d’ordinateur prennent la forme *[nom de domaine]\[nom de l’ordinateur]***$**& #x 2014 ; par exemple, **FABRIKAM\TFSBUILD$**. Par conséquent, si votre service de build s’exécute à l’aide de l’identité Service réseau, vous devriez toutes les autorisations requises pour l’identité du compte d’ordinateur pour votre serveur de builds.


## <a name="configuring-web-server-permissions"></a>Configuration des autorisations de serveur Web

Comme décrit dans [en choisissant l’approche de droite pour le déploiement Web](../configuring-server-environments-for-web-deployment/choosing-the-right-approach-to-web-deployment.md), il existe deux approches principales, vous pouvez utiliser si vous souhaitez déployer des packages web à un serveur web à distance :

- Déployer l’application à partir d’un emplacement distant en ciblant la *Service de l’Agent de déploiement Web* (également appelé l’agent distant) sur le serveur de destination.
- Déployer l’application à partir d’un emplacement distant en ciblant la *Internet Information Services* (*IIS) Gestionnaire de déploiement Web* sur le serveur de destination.

L’agent distant a deux principales limitations dans ce cas :

- L’agent distant prend en charge uniquement l’authentification NTLM. En d’autres termes, le déploiement doit utiliser l’identité de service de build & #x 2014 ; vous ne peut pas emprunter l’identité d’un autre compte.
- Pour utiliser l’agent distant, le compte qui exécute le déploiement doit être un administrateur sur le serveur cible.

Ensemble, ces deux limitations rendent l’approche de l’agent distant pas souhaitable pour un déploiement automatisé Team Build. Pour utiliser cette approche, vous devez rendre le service de build le compte d’administrateur sur tous les serveurs web cible.

En revanche, l’approche du Gestionnaire de déploiement Web offre différents avantages :

- Le Gestionnaire de déploiement Web prend en charge l’authentification de base sur HTTPS, ce qui vous permet de passer les informations d’identification d’un autre compte pour l’outil de déploiement Web IIS (Web Deploy).
- Vous pouvez configurer des serveurs web de cible pour permettre aux utilisateurs non administrateurs de déployer du contenu vers des sites Web IIS spécifiques à l’aide du Gestionnaire de déploiement Web.

Par conséquent, il est clairement préférable pour cibler le Gestionnaire de déploiement Web lorsque vous automatisez le déploiement du package web à partir de Team Build. Voici le processus recommandé :

1. Créez un compte de domaine à faibles privilèges à utiliser pour le déploiement.
2. Configurer le Gestionnaire de déploiement Web et accorder des autorisations requises pour déployer du contenu vers un site Web IIS, le compte comme décrit dans [configuration d’un serveur Web pour déployer de publication Web (Gestionnaire de déploiement Web)](../configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler.md).
3. Appel de Web Deploy et cibler le Gestionnaire de déploiement Web, à l’aide de l’authentification de base et en fournissant les informations d’identification du compte de domaine créé, pour effectuer le déploiement.

Dans le [Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md) exemple de solution, vous spécifiez le type d’authentification (base ou NTLM), les informations d’identification de déploiement Web et l’adresse de point de terminaison (l’agent distant ou un gestionnaire de déploiement Web) dans le fichier de projet spécifique à l’environnement. Ces valeurs sont utilisées pour élaborer et exécuter une commande de Web Deploy lorsque le fichier projet est exécuté. Pour plus d’informations, consultez [déploiement des Packages Web](../web-deployment-in-the-enterprise/deploying-web-packages.md).

Pour plus d’informations sur la configuration du Gestionnaire Web déployer, notamment la façon de configurer les autorisations, consultez [configuration d’un serveur Web pour déployer de publication Web (Gestionnaire de déploiement Web)](../configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler.md). Pour plus d’informations sur la configuration de l’agent distant, consultez [configuration d’un serveur Web pour déployer de publication Web (Agent distant)](../configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-remote-agent.md).

## <a name="configuring-database-server-permissions"></a>Configuration des autorisations de serveur de base de données

Pour déployer une base de données sur SQL Server, vous devez :

- Créez une connexion pour le compte de déploiement sur l’instance de SQL Server.
- Accordez à la connexion **DBCreator** autorisations sur l’instance de SQL Server.
- Après le déploiement initial, ajoutez la connexion à la **db\_propriétaire** sur la base de données cible. Cela est nécessaire, car sur les déploiements suivants, vous êtes modification d’une base de données existante au lieu de créer une nouvelle base de données.

Vous pouvez vous authentifier à une instance de SQL Server à l’aide de l’authentification NTLM ou authentification SQL Server :

- Si vous utilisez l’authentification NTLM, vous devez accorder les autorisations décrites ci-dessus pour le compte de service de build.
- Si vous utilisez l’authentification SQL Server, vous devez accorder les autorisations décrites ci-dessus pour le compte SQL Server. Vous devez également inclure le nom d’utilisateur SQL Server et le mot de passe dans la chaîne de connexion que vous utilisez pour déployer la base de données.

Pour plus d’informations détaillées sur la façon de configurer les autorisations pour le déploiement de la base de données, consultez [configuration d’un serveur de base de données pour la publication de déploiement Web](../configuring-server-environments-for-web-deployment/configuring-a-database-server-for-web-deploy-publishing.md).

## <a name="conclusion"></a>Conclusion

À ce stade, vous devez comprendre les autorisations requises, ainsi que les options d’authentification ouverts pour vous, lorsque vous automatisez les déploiements de base de données et d’application web à partir de Team Build. Vous devez également être en mesure d’implémenter les autorisations nécessaires sur les serveurs web IIS et les serveurs de base de données SQL Server.

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur la configuration des environnements Windows server pour prendre en charge le déploiement à distance, consultez [configuration des environnements de serveur pour le déploiement Web](../configuring-server-environments-for-web-deployment/configuring-server-environments-for-web-deployment.md).

>[!div class="step-by-step"]
[Précédent](deploying-a-specific-build.md)

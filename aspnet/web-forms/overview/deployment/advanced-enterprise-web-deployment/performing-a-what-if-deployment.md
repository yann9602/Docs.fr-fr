---
uid: web-forms/overview/deployment/advanced-enterprise-web-deployment/performing-a-what-if-deployment
title: "Effectuer le déploiement | Documents Microsoft"
author: jrjlee
description: "Cette rubrique explique comment effectuer « que se passe-t-il si » (ou simulé) déploiements à l’aide de l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy) et V..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: c711b453-01ac-4e65-a48c-93d99bf22e58
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/advanced-enterprise-web-deployment/performing-a-what-if-deployment
msc.type: authoredcontent
ms.openlocfilehash: cea805c86f0764c7443ccc5c9f89248860a6a842
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="performing-a-what-if-deployment"></a>Exécution d’un déploiement « Que se passe-t-il si »
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique explique comment effectuer « que se passe-t-il si » (ou simulé) déploiements à l’aide de l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy) et VSDBCMD. Cela vous permet de déterminer l’impact de votre logique de déploiement sur un environnement cible particulier avant de déployer votre application.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md), dans lequel le processus de génération et de déploiement est contrôlé par deux fichiers projet & #x 2014 ; o ne contenant les instructions de génération qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="performing-a-what-if-deployment-for-web-packages"></a>Exécution d’un déploiement « Que se passe-t-il si » pour les Packages Web

Web Deploy inclut des fonctionnalités qui vous permet d’effectuer des déploiements dans « que se passe-t-il si » (ou une version d’évaluation) mode. Lorsque vous déployez des artefacts en mode « que se passe-t-il si », Web Deploy génère un fichier journal, comme si vous avez effectué le déploiement, mais il réellement ne modifie en rien sur le serveur de destination. Examiner le fichier journal peut vous aider à comprendre les incidences de votre déploiement aura sur le serveur de destination, en particulier :

- Ce qui est ajouté.
- Ce qui sera mis à jour.
- Ce qui sera supprimé.

Car un déploiement « que se passe-t-il si » réellement ne modifie en rien sur le serveur de destination, ce qu’il ne peut pas toujours faire est de prédire si un déploiement réussira.

Comme décrit dans [déploiement des Packages Web](../web-deployment-in-the-enterprise/deploying-web-packages.md), vous pouvez déployer des packages web à l’aide de Web Deploy dans deux façons & #x 2014 ; à l’aide de l’utilitaire de ligne de commande MSDeploy.exe directement ou en exécutant la *. deploy.cmd* fichier qui génère des processus de génération.

Si vous utilisez MSDeploy.exe directement, vous pouvez exécuter un déploiement « que se passe-t-il si » en ajoutant le **– whatif** indicateur à votre commande. Par exemple, pour évaluer ce qui se passerait si vous avez déployé le package ContactManager.Mvc.zip dans un environnement intermédiaire, la commande MSDeploy doit ressembler à ceci :


[!code-console[Main](performing-a-what-if-deployment/samples/sample1.cmd)]


Lorsque vous êtes satisfait des résultats de votre déploiement « que se passe-t-il si », vous pouvez supprimer la **– whatif** indicateur pour exécuter un déploiement.

> [!NOTE]
> Pour plus d’informations sur les options de ligne de commande pour MSDeploy.exe, consultez [Web Deploy opération Settings](https://technet.microsoft.com/library/dd569089(WS.10).aspx).


Si vous utilisez la *. deploy.cmd* fichier, vous pouvez exécuter un déploiement « que se passe-t-il si » en incluant le **/t** drapeau de (mode d’évaluation) au lieu du **/y** indicateur (« Oui », ou en mode de mise à jour) dans votre commande. Par exemple, pour évaluer ce qui se passerait si vous avez déployé le package ContactManager.Mvc.zip en exécutant la *. deploy.cmd* fichier, votre commande doit ressembler à ceci :


[!code-console[Main](performing-a-what-if-deployment/samples/sample2.cmd)]


Lorsque vous êtes satisfait des résultats de votre déploiement de « mode d’évaluation », vous pouvez remplacer le **/t** indicateur avec un **/y** indicateur pour l’exécution d’un déploiement en direct :


[!code-console[Main](performing-a-what-if-deployment/samples/sample3.cmd)]


> [!NOTE]
> Pour plus d’informations sur les options de ligne de commande pour *. deploy.cmd* fichiers, voir [Comment : installer un Package de déploiement à l’aide du fichier deploy.cmd](https://msdn.microsoft.com/library/ff356104.aspx). Si vous exécutez le *. deploy.cmd* fichier sans spécifier de tous les indicateurs, l’invite de commandes affiche une liste des indicateurs disponibles.


## <a name="performing-a-what-if-deployment-for-databases"></a>Exécution d’un déploiement « Que se passe-t-il si » pour les bases de données

Cette section part du principe que vous utilisez l’utilitaire VSDBCMD pour effectuer le déploiement incrémentiel, basée sur un schéma de base de données. Cette approche est décrite plus en détail dans [déploiement de projets de base de données](../web-deployment-in-the-enterprise/deploying-database-projects.md). Nous vous recommandons de vous familiariser avec cette rubrique avant d’appliquer les concepts décrits ici.

Lorsque vous utilisez VSDBCMD dans **déployer** mode, vous pouvez utiliser la **/dd** (ou **/DeployToDatabase**) indicateur contrôle VSDBCMD réellement déploie la base de données ou génère simplement une script de déploiement. Si vous déployez un fichier .dbschema, il s’agit du comportement :

- Si vous spécifiez **/dd+** ou **/dd**, VSDBCMD génère un script de déploiement et déployer la base de données.
- Si vous spécifiez **/dd-** ou omettez le commutateur, VSDBCMD génère un script de déploiement uniquement.

> [!NOTE]
> Si vous déployez un fichier .deploymanifest plutôt que dans un fichier .dbschema, le comportement de la **/dd** commutateur est beaucoup plus compliqué. Essentiellement, VSDBCMD ignore la valeur de la **/dd** passer si le fichier .deploymanifest inclut un **DeployToDatabase** élément avec la valeur **True**. [Déploiement de projets de base de données](../web-deployment-in-the-enterprise/deploying-database-projects.md) décrit ce comportement dans sa totalité.


Par exemple, pour générer un script de déploiement pour le **ContactManager** base de données sans réellement déployer la base de données, votre commande VSDBCMD doit ressembler à ceci :


[!code-console[Main](performing-a-what-if-deployment/samples/sample4.cmd)]


VSDBCMD est un outil de déploiement de base de données différentielle, et par conséquent le script de déploiement est généré dynamiquement pour contenir toutes les commandes SQL nécessaires pour mettre à jour la base de données actuel, s’il en existe, pour le schéma spécifié. Examiner le script de déploiement est un moyen utile pour déterminer l’impact de votre déploiement aura sur la base de données actuelle et les données qu’il contient. Par exemple, vous souhaiterez déterminer :

- Si des tables existantes seront supprimés, et si cela provoquera une perte de données.
- Indique si l’ordre des opérations comporte un risque de perte de données, par exemple, si vous êtes fractionner ou fusionner des tables.

Si vous êtes satisfait avec le script de déploiement, vous pouvez répéter le VSDBCMD avec un **/dd+** indicateur pour apporter les modifications. Vous pouvez également modifier le script de déploiement pour répondre à vos besoins, puis l’exécuter manuellement sur le serveur de base de données.

## <a name="integrating-what-if-functionality-into-custom-project-files"></a>Intégration de fonctionnalité « Que se passe-t-il si » dans les fichiers de projet personnalisés

Dans les scénarios de déploiement plus complexes, que vous souhaitez utiliser un fichier de projet Microsoft Build Engine (MSBuild) personnalisé pour encapsuler votre logique de génération et de déploiement, comme décrit dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md). Par exemple, dans le [le Gestionnaire de contacts](../web-deployment-in-the-enterprise/the-contact-manager-solution.md) exemple de solution, le *Publish.proj* fichier :

- Génère la solution.
- Utilise Web Deploy pour empaqueter et déployer l’application ContactManager.Mvc.
- Utilise Web Deploy pour empaqueter et déployer l’application ContactManager.Service.
- Déploie le **ContactManager** base de données.

Lorsque vous intégrez le déploiement de plusieurs bases de données et/ou les packages web un processus de cette façon, vous pouvez également la possibilité d’effectuer le déploiement complet en mode « que se passe-t-il si ».

Le *Publish.proj* fichier montre comment vous pouvez faire cela. Tout d’abord, vous devez créer une propriété pour stocker la valeur « que se passe-t-il si » :


[!code-xml[Main](performing-a-what-if-deployment/samples/sample5.xml)]


Dans ce cas, vous avez créé une propriété nommée **WhatIf** avec la valeur par défaut **false**. Les utilisateurs peuvent remplacer cette valeur en affectant à la propriété **true** dans un paramètre de ligne de commande, comme vous le verrez bientôt.

L’étape suivante consiste à paramétrer les Web Deploy et des commandes de VSDBCMD afin que les indicateurs de reflètent la **WhatIf** valeur de propriété. Par exemple, la cible suivante (effectuée à partir de la *Publish.proj* de fichiers et de simplifier la) s’exécute le *. deploy.cmd* fichier pour déployer un package web. Par défaut, la commande inclut un **/Y** commutateur (« Oui » ou en mode de mise à jour). Si **WhatIf** a la valeur **true**, il est remplacé par un **/T** commutateur (version d’évaluation ou le mode « que se passe-t-il si »).


[!code-xml[Main](performing-a-what-if-deployment/samples/sample6.xml)]


De même, la cible suivante utilise l’utilitaire VSDBCMD pour déployer une base de données. Par défaut, un **/dd** commutateur n’est pas inclus. Cela signifie que VSDBCMD génère un script de déploiement, mais ne déploiera pas de la base de données & #x 2014 ; en d’autres termes, un scénario « que se passe-t-il si ». Si le **WhatIf** propriété n’est pas définie sur **true**, un **/dd** commutateur est ajouté et VSDBCMD déployez la base de données.


[!code-xml[Main](performing-a-what-if-deployment/samples/sample7.xml)]


Vous pouvez utiliser la même approche pour paramétrer toutes les commandes appropriées dans votre fichier projet. Lorsque vous souhaitez exécuter un déploiement « que se passe-t-il si », vous pouvez ensuite simplement fournir un **WhatIf** valeur de propriété à partir de la ligne de commande :


[!code-console[Main](performing-a-what-if-deployment/samples/sample8.cmd)]


De cette façon, vous pouvez exécuter un déploiement « que se passe-t-il si » pour tous les composants de votre projet en une seule étape.

## <a name="conclusion"></a>Conclusion

Cette rubrique décrit comment exécuter « que se passe-t-il si » les déploiements à l’aide de Web Deploy VSDBCMD et MSBuild. Un déploiement « que se passe-t-il si » vous permet d’évaluer l’impact d’un déploiement proposé avant de procéder réellement des modifications à l’environnement de destination.

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur la syntaxe de ligne de commande de Web Deploy, consultez [Web Deploy opération Settings](https://technet.microsoft.com/library/dd569089(WS.10).aspx). Pour obtenir des conseils sur les options de ligne de commande lorsque vous utilisez la *. deploy.cmd* de fichiers, consultez [Comment : installer un Package de déploiement à l’aide du fichier deploy.cmd](https://msdn.microsoft.com/library/ff356104.aspx). Pour obtenir des conseils sur la syntaxe de ligne de commande de VSDBCMD, consultez [référence de ligne de commande pour VSDBCMD. EXE (déploiement et importation de schéma)](https://msdn.microsoft.com/library/dd193283.aspx).

>[!div class="step-by-step"]
[Précédent](advanced-enterprise-web-deployment.md)
[Suivant](customizing-database-deployments-for-multiple-environments.md)

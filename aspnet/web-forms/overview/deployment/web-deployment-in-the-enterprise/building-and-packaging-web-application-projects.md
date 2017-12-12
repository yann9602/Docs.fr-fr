---
uid: web-forms/overview/deployment/web-deployment-in-the-enterprise/building-and-packaging-web-application-projects
title: "Création et l’empaquetage des projets d’Application Web | Documents Microsoft"
author: jrjlee
description: "Lorsque vous souhaitez déployer un projet d’application web dans un environnement de serveur distant, la première tâche consiste à générer le projet et générer un packa de déploiement web..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 94e92f80-a7e3-4d18-9375-ff8be5d666ac
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/web-deployment-in-the-enterprise/building-and-packaging-web-application-projects
msc.type: authoredcontent
ms.openlocfilehash: c05f725c9e6b493a6af8f5b5d20dbc9ff73a1ef8
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="building-and-packaging-web-application-projects"></a>Création et l’empaquetage des projets d’Application Web
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Lorsque vous souhaitez déployer un projet d’application web dans un environnement de serveur distant, la première tâche consiste à générer le projet et générer un package de déploiement web. Cette rubrique décrit le fonctionnement des processus de génération pour les projets d’application web. En particulier, elle explique :
> 
> - Comment le Pipeline de publication Web (WPP) étend le processus de génération pour inclure les fonctionnalités de déploiement.
> - Comment l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy) active de votre application web dans un package de déploiement.
> - Comment la génération et l’empaquetage fonctionnement du processus et les fichiers sont créés.


Dans Visual Studio 2010, le processus de génération et de déploiement pour les projets d’application web est prise en charge par les fournisseurs de services. Les fournisseurs de services fournit un jeu de cibles de Microsoft Build Engine (MSBuild) qui étendent les fonctionnalités de MSBuild et lui permettre de s’intégrer avec Web Deploy. Dans Visual Studio, vous pouvez voir cette fonctionnalité étendue sur les pages de propriétés pour votre projet d’application web. Le **Package/Publication Web** page, avec le **Package/Publication SQL** page, vous pouvez configurer la façon dont votre projet d’application web est empaqueté pour le déploiement lorsque le processus de génération est terminé.

![](building-and-packaging-web-application-projects/_static/image1.png)

## <a name="how-does-the-wpp-work"></a>Comment fonctionne le WPP ?

Si vous examinez le fichier projet pour c#-projet d’application web de base, vous pouvez voir qu’il importe de deux fichiers .targets.


[!code-xml[Main](building-and-packaging-web-application-projects/samples/sample1.xml)]


La première **importation** instruction est commune à tous les projets Visual c#. Ce fichier, *Microsoft.CSharp.targets*, contient les cibles et les tâches qui sont spécifiques à Visual c#. Par exemple, le compilateur c# (**Csc**) tâche est appelée ici. Le *Microsoft.CSharp.targets* de fichiers à son tour importe le *Microsoft.Common.targets* fichier. Définit les cibles qui sont communes à tous les projets, comme **générer**, **reconstruire**, **exécuter**, **compiler**, et **nettoyer** . La seconde **importation** instruction est spécifique aux projets d’application web. Le *Microsoft.WebApplication.targets* de fichiers à son tour importe le *Microsoft.Web.Publishing.targets* fichier. Le *Microsoft.Web.Publishing.targets* fichier essentiellement *est* les fournisseurs de services. Il définit des cibles, comme **Package** et **MSDeployPublish**, qui appeler Web Deploy pour effectuer diverses tâches de déploiement.

Pour comprendre comment ces cibles supplémentaires sont utilisés, dans l’exemple de solution du Gestionnaire de contacts, ouvrez le *Publish.proj* de fichiers et de jeter un coup de œil à la **BuildProjects** cible.


[!code-xml[Main](building-and-packaging-web-application-projects/samples/sample2.xml)]


Cette cible utilise la **MSBuild** tâche pour générer des projets différents. Notez le **DeployOnBuild** et **DeployTarget** propriétés :

- Le **DeployOnBuild = true** propriété signifie généralement que « Je souhaite exécuter cible supplémentaire lors de la build se termine avec succès ».
- Le **DeployTarget** propriété identifie le nom de la cible que vous voulez exécuter lorsque le **DeployOnBuild** propriété est égale à **true**. Dans ce cas, vous spécifiez que vous souhaitez MSBuild pour exécuter le **Package** cible après avoir généré le projet.

Le **Package** cible est définie dans le *Microsoft.Web.Publishing.targets* fichier. Pour l’essentiel, cette cible prend la sortie de génération de votre projet d’application web et la convertit en un package de déploiement web qui peut être publié sur un serveur web IIS.

> [!NOTE]
> Pour afficher un fichier de projet (par exemple, *ContactManager.Mvc.csproj*) dans Visual Studio 2010, vous devez tout d’abord décharger le projet à partir de votre solution. Dans le **l’Explorateur de solutions** fenêtre, cliquez sur le nœud du projet, puis cliquez sur **décharger le projet**. Cliquez à nouveau sur le nœud du projet, puis cliquez sur **modifier***[fichier de projet]*). Le fichier projet s’ouvre dans sa forme XML brut. N’oubliez pas de recharger le projet lorsque vous avez terminé.  
> Pour plus d’informations sur les cibles de MSBuild, tâches, et **importation** consultez [présentation du fichier de projet](understanding-the-project-file.md). Pour obtenir une présentation plus approfondie des fichiers projet et les fournisseurs de services, consultez [à l’intérieur de la Microsoft Build Engine : à l’aide de MSBuild et Team Foundation Build](http://amzn.com/0735645248) par Sayed Ibrahim Hashimi et William Bartholomew, numéro ISBN : 978-0-7356-4524-0.


## <a name="what-is-a-web-deployment-package"></a>Qu’est un Package de déploiement Web ?

Lorsque vous générez et déployez un projet d’application web à l’aide de Visual Studio 2010 ou à l’aide de MSBuild directement, le résultat final est généralement un *package de déploiement web*. Le package de déploiement web est un fichier .zip. Il contient tout ce qu’IIS et Web Deploy besoin afin de recréer votre application web, y compris :

- La sortie compilée de votre application web, y compris le contenu, les fichiers de ressources, fichiers de configuration, JavaScript et en cascade ressources des feuilles de style et ainsi de suite.
- Assemblys à votre projet d’application web et à tous les projets dans votre solution référencés.
- Scripts SQL pour générer des bases de données que vous déployez avec votre application web.

Une fois que le package de déploiement web a été généré, vous pouvez le publier sur un serveur web IIS de différentes manières. Par exemple, vous pouvez le déployer à distance en ciblant le service Web déployer l’Agent à distance ou le Gestionnaire de déploiement Web sur le serveur web de destination, ou vous pouvez utiliser le Gestionnaire des services Internet pour importer manuellement le package sur le serveur web de destination. Pour plus d’informations sur ces approches de déploiement, consultez [en choisissant l’approche de droite pour le déploiement Web](../configuring-server-environments-for-web-deployment/choosing-the-right-approach-to-web-deployment.md).

## <a name="how-does-the-build-process-work"></a>Comment fonctionne le processus de génération ?

Cet exemple montre que se passe-t-il lorsque vous générez et empaqueter un projet d’application web :

![](building-and-packaging-web-application-projects/_static/image2.png)

Lorsque vous générez un projet d’application web, le processus de génération génère un fichier nommé *[nom du projet]. SourceManifest.xml*. En même temps que le fichier projet et la sortie de génération, cela *. SourceManifest.xml* fichier indique au déploiement Web qu’il doit inclure dans le package de déploiement web. À l’aide de ces entrées, Web Deploy génère un package de déploiement web nommé *[nom du projet] .zip*.

En même temps que le package de déploiement web, le processus de génération génère deux fichiers qui peuvent vous aider à utiliser le package :

- Le *. deploy.cmd* fichier comprend un ensemble de commandes paramétrables Web Deploy (MSDeploy.exe) qui publient votre package de déploiement web sur un serveur web IIS à distance. En cours d’exécution le *. deploy.cmd* fichier, avec les paramètres appropriés, généralement fournit un moyen plus rapide et plus facile de remplacement à la construction de manuellement la MSDeploy.exe commandes vous-même.
- Le *SetParameters.xml* fichier fournit un ensemble de valeurs de paramètre à la commande MSDeploy.exe. Ces valeurs incluent des propriétés telles que le nom de l’application web IIS auquel vous souhaitez déployer le package, les valeurs des points de terminaison de service et les chaînes de connexion définies dans le *web.config* de fichiers et n’importe quelle propriété de déploiement valeurs définies dans les pages de propriétés de projet.

Le *SetParameters.xml* fichier est essentiel pour gérer le processus de déploiement. Ce fichier est généré dynamiquement selon le contenu de votre projet d’application web. Par exemple, si vous ajoutez une chaîne de connexion à votre *web.config* fichier, le processus de génération sera détecter automatiquement la chaîne de connexion, paramétrez le déploiement en conséquence et créer une entrée dans le  *SetParameters.xml* fichier vous permet de modifier la chaîne de connexion dans le cadre du processus de déploiement. La rubrique suivante, [configuration des paramètres pour le déploiement du Package Web](configuring-parameters-for-web-package-deployment.md), explique le rôle de ce fichier plus en détail et décrit les différentes façons dans laquelle vous pouvez la modifier pendant la génération et de déploiement.

> [!NOTE]
> Dans Visual Studio 2010, les fournisseurs de services ne prend pas en charge la précompiler les pages dans une application web avant d’empaquetage. La prochaine version de Visual Studio et les fournisseurs de services inclut la possibilité de précompiler une application web comme une option de package.


## <a name="conclusion"></a>Conclusion

Cette rubrique a fourni une vue d’ensemble de la génération et le processus d’empaquetage pour les projets d’application web dans Visual Studio 2010. Il décrit comment les fournisseurs de services vous permet d’appeler des commandes de déploiement Web à partir de MSBuild et elle explique comment la génération et l’empaquetage fonctionnement du processus.

Une fois que vous avez créé un package de déploiement web, l’étape suivante consiste à le déployer. Pour plus d’informations, consultez [configuration des paramètres pour le déploiement du Package Web](configuring-parameters-for-web-package-deployment.md) et [déploiement des Packages Web](deploying-web-packages.md).

## <a name="further-reading"></a>informations supplémentaires

Les rubriques suivantes dans ce didacticiel, [configuration des paramètres pour le déploiement du Package Web](configuring-parameters-for-web-package-deployment.md) et [déploiement des Packages Web](deploying-web-packages.md), fournissent des conseils sur l’utilisation du package web que vous avez créé. Le didacticiel final dans cette série, [déploiement avancé de Web Enterprise](../advanced-enterprise-web-deployment/advanced-enterprise-web-deployment.md), fournit des conseils sur la façon de personnaliser et dépanner le processus d’empaquetage.

Pour obtenir une présentation plus approfondie des fichiers projet et les fournisseurs de services, consultez [à l’intérieur de la Microsoft Build Engine : à l’aide de MSBuild et Team Foundation Build](http://amzn.com/0735645248) par Sayed Ibrahim Hashimi et William Bartholomew, numéro ISBN : 978-0-7356-4524-0.

>[!div class="step-by-step"]
[Précédent](understanding-the-build-process.md)
[Suivant](configuring-parameters-for-web-package-deployment.md)

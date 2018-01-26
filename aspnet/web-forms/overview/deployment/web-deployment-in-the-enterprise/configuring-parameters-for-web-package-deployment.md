---
uid: web-forms/overview/deployment/web-deployment-in-the-enterprise/configuring-parameters-for-web-package-deployment
title: "Configuration des paramètres de déploiement du Package Web | Documents Microsoft"
author: jrjlee
description: "Cette rubrique explique comment définir des valeurs de paramètre, telles que les noms d’application web Internet Information Services (IIS), les chaînes de connexion et les points de terminaison de service..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 37947d79-ab1e-4ba9-9017-52e7a2757414
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/web-deployment-in-the-enterprise/configuring-parameters-for-web-package-deployment
msc.type: authoredcontent
ms.openlocfilehash: 12a4ba8ad30df43e7192500ad4514dfa9679f899
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="configuring-parameters-for-web-package-deployment"></a>Configuration des paramètres de déploiement du Package Web
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment définir les valeurs de paramètre, telles que les noms d’application web Internet Information Services (IIS), les chaînes de connexion et les points de terminaison de service, lorsque vous déployez un package web sur un serveur web IIS à distance.


Lorsque vous générez un projet d’application web, le processus d’empaquetage et de build génère trois fichiers de clé :

- A *[nom du projet] .zip* fichier. Il s’agit de package de déploiement web pour votre projet d’application web. Ce package contient tous les assemblys, les fichiers, les scripts de base de données et les ressources requises pour recréer votre application web sur un serveur web IIS distant.
- A *[nom du projet].deploy.cmd* fichier. Contient un ensemble de commandes paramétrables Web Deploy (MSDeploy.exe) qui publient votre package de déploiement web sur un serveur web IIS à distance.
- A *[nom du projet]. SetParameters.xml* fichier. Cela fournit un ensemble de valeurs de paramètre à la commande MSDeploy.exe. Vous pouvez mettre à jour les valeurs dans ce fichier et passer au déploiement Web comme paramètre de ligne de commande lorsque vous déployez votre package web.

> [!NOTE]
> Pour plus d’informations sur la génération et le processus d’empaquetage, consultez [génération et des projets d’Application Web empaquetage](building-and-packaging-web-application-projects.md).


Le *SetParameters.xml* généré dynamiquement à partir de votre fichier de projet d’application web et tous les fichiers de configuration au sein de votre projet. Lorsque vous générez et package pour votre projet, le Pipeline de publication Web (WPP) détecte automatiquement un grand nombre de variables qui sont susceptibles de changer entre les environnements de déploiement, telles que l’application web IIS de destination et les chaînes de connexion de base de données. Ces valeurs sont automatiquement paramétrable dans le package de déploiement web et ajoutés à la *SetParameters.xml* fichier. Par exemple, si vous ajoutez une chaîne de connexion à la *web.config* fichier dans votre projet d’application web, le processus de génération détecte cette modification et ajoute une entrée à la *SetParameters.xml* fichier en conséquence.

Dans de nombreux cas, ce paramétrage automatique sera suffisant. Toutefois, si vos utilisateurs ont besoin pour faire varier les autres paramètres entre les environnements de déploiement, telles que les paramètres de l’application ou l’URL de point de terminaison de service, vous devez indiquer les fournisseurs de services pour paramétrer ces valeurs dans le package de déploiement et d’ajouter des entrées correspondantes à la *SetParameters.xml* fichier. Les sections qui suivent expliquent comment effectuer cette opération.

### <a name="automatic-parameterization"></a>Paramétrage automatique

Lorsque vous générez et empaquetez une application web, WPP paramétrer automatiquement des opérations suivantes :

- La destination IIS web les nom et chemin d’accès de l’application.
- Chaînes de n’importe quelle connexion dans votre *web.config* fichier.
- Chaînes de connexion pour les bases de données que vous ajoutez à la **Package/Publication SQL** onglet dans les pages de propriétés du projet.

Par exemple, si vous deviez générer et empaqueter le [le Gestionnaire de contacts](the-contact-manager-solution.md) cela génère l’exemple de solution sans toucher le processus de paramétrage de quelque façon, les fournisseurs de services *ContactManager.Mvc.SetParameters.xml* fichier :


[!code-xml[Main](configuring-parameters-for-web-package-deployment/samples/sample1.xml)]


Dans ce cas :

- Le **nom de l’Application Web IIS** paramètre est le chemin d’accès IIS sur lequel vous souhaitez déployer l’application web. La valeur par défaut provient de la **Package/Publication Web** page dans les pages de propriétés du projet.
- Le **chaîne de connexion ApplicationServices-Web.config** paramètre généré à partir d’un **connectionStrings/ajouter** élément dans le *web.config* fichier. Il représente la chaîne de connexion que l’application doit utiliser pour contacter la base de données d’appartenance. La valeur que vous fournissez ici seront remplacées dans déployé *web.config* fichier. La valeur par défaut est extraite du pré-déploiement *web.config* fichier.

Les fournisseurs de services est également ces propriétés dans le package de déploiement qu’il génère. Vous pouvez fournir des valeurs pour ces propriétés lorsque vous installez le package de déploiement. Si vous installez le package manuellement via IIS Manager, comme décrit dans [installer manuellement les Packages Web](manually-installing-web-packages.md), l’Assistant installation vous invite à fournir des valeurs pour tous les paramètres. Si vous installez le package à distance à l’aide de la *. deploy.cmd* de fichiers, comme décrit dans [déploiement des Packages Web](deploying-web-packages.md), Web Deploy ressemblera à ce *SetParameters.xml* le fichier Indiquez les valeurs de paramètre. Vous pouvez modifier les valeurs dans le *SetParameters.xml* fichier manuellement, ou vous pouvez personnaliser le fichier dans le cadre d’un processus de génération et de déploiement automatisé. Ce processus est décrit plus en détail plus loin dans cette rubrique.

### <a name="custom-parameterization"></a>Paramétrage personnalisé

Dans les scénarios de déploiement plus complexes, vous souhaiterez souvent paramétrer des propriétés supplémentaires avant de déployer votre projet. En règle générale, vous devez paramétrer toutes les propriétés et paramètres qui varient entre les environnements de destination. Celles-ci peuvent inclure :

- Des points de terminaison dans le *web.config* fichier.
- Paramètres d’application dans le *web.config* fichier.
- Toutes les autres propriétés déclaratives que vous souhaitez demander aux utilisateurs de spécifier.

Pour paramétrer ces propriétés le plus simple consiste à ajouter un *parameters.xml* fichier dans le dossier racine de votre projet d’application web. Par exemple, dans la solution de gestionnaire de contacts, le projet ContactManager.Mvc inclut un *parameters.xml* fichier dans le dossier racine.

![](configuring-parameters-for-web-package-deployment/_static/image1.png)

Si vous ouvrez ce fichier, vous verrez qu’il contient un seul **paramètre** entrée. L’entrée utilise une requête XML Path Language (XPath) pour rechercher et paramétrer l’URL de point de terminaison du service ContactService Windows Communication Foundation (WCF) dans le *web.config* fichier.


[!code-xml[Main](configuring-parameters-for-web-package-deployment/samples/sample2.xml)]


En plus de paramétrer l’URL de point de terminaison dans le package de déploiement, les fournisseurs de services ajoute également une entrée correspondante à la *SetParameters.xml* fichier généré en même temps que le package de déploiement.


[!code-xml[Main](configuring-parameters-for-web-package-deployment/samples/sample3.xml)]


Si vous installez manuellement le package de déploiement, le Gestionnaire IIS vous demandera l’adresse de point de terminaison de service en même temps que les propriétés qui ont été paramétré automatiquement. Si vous installez le package de déploiement en exécutant la *. deploy.cmd* fichier, vous pouvez modifier le *SetParameters.xml* fichier pour fournir une valeur pour l’adresse de point de terminaison de service ainsi que les valeurs pour le propriétés qui ont été paramétrées automatiquement.

Pour plus d’informations sur la création d’un *parameters.xml* de fichiers, consultez [Comment : utiliser les paramètres pour configurer les paramètres lorsqu’un Package de déploiement est installé](https://msdn.microsoft.com/library/ff398068.aspx). La procédure nommée **à utiliser les paramètres de déploiement pour les paramètres du fichier Web.config** fournit des instructions détaillées.

## <a name="modifying-the-setparametersxml-file"></a>Modification du fichier SetParameters.xml

Si vous envisagez de déployer le package d’application web manuellement & #x 2014 ; en exécutant la *. deploy.cmd* de fichier ou en exécutant MSDeploy.exe à partir de la ligne de commande & #x 2014 ; il est rien ne vous modifier manuellement la *SetParameters.xml* fichier avant le déploiement. Toutefois, si vous travaillez sur une solution à l’échelle de l’entreprise, vous devez déployer un package d’application web dans le cadre d’un processus de génération et de déploiement plus volumineux et automatisé. Dans ce scénario, vous avez besoin de Microsoft Build Engine (MSBuild) pour modifier la *SetParameters.xml* fichier pour vous. Vous pouvez pour cela à l’aide de MSBuild **XmlPoke** tâche.

Le [exemple de gestionnaire de contacts solution](the-contact-manager-solution.md) illustre ce processus. Les exemples de code qui suivent ont été modifiés pour afficher uniquement les détails qui s’appliquent à cet exemple.

> [!NOTE]
> Pour une vue d’ensemble plus large dans le fichier du modèle de projet dans l’exemple de solution et une introduction aux fichiers de projet personnalisé en général, consultez [présentation du fichier de projet](understanding-the-project-file.md) et [comprendre le processus de génération](understanding-the-build-process.md).


Tout d’abord, les valeurs de paramètre d’intérêt sont définies en tant que propriétés dans le fichier de projet spécifique à l’environnement (par exemple, *Env-Dev.proj*).


[!code-xml[Main](configuring-parameters-for-web-package-deployment/samples/sample4.xml)]


> [!NOTE]
> Pour obtenir des conseils sur la façon de personnaliser les fichiers de projet spécifique à un environnement pour vos propres environnements de serveur, consultez [configurer les propriétés de déploiement pour un environnement cible](../configuring-server-environments-for-web-deployment/configuring-deployment-properties-for-a-target-environment.md).


Ensuite, le *Publish.proj* fichier importe ces propriétés. Étant donné que chaque *SetParameters.xml* fichier est associé un *. deploy.cmd* de fichiers et nous avons finalement souhaitez le fichier projet pour appeler chaque *. deploy.cmd* de fichiers, le projet fichier crée une MSBuild *élément* pour chaque *. deploy.cmd* de fichiers et définit les propriétés dignes d’intérêt en tant que *métadonnée d’élément*.


[!code-xml[Main](configuring-parameters-for-web-package-deployment/samples/sample5.xml)]


Dans ce cas :

- Le **ParametersXml** valeur des métadonnées indique l’emplacement de la *SetParameters.xml* fichier.
- Le **IisWebAppName** valeur est le chemin d’accès IIS à laquelle vous souhaitez déployer l’application web.
- Le **MembershipDBConnectionString** valeur est la chaîne de connexion pour la base de données d’appartenance et le **MembershipDBConnectionName** valeur est la **nom** attribut du paramètre correspondant dans le *SetParameters.xml* fichier.
- Le **ServiceEndpointValue** valeur est l’adresse de point de terminaison pour le service WCF sur le serveur de destination et le **ServiceEndpointParamName** valeur est l’attribut de nom du paramètre correspondant dans le *SetParameters.xml* fichier.

Enfin, dans le *Publish.proj* fichier, le **PublishWebPackages** cible utilise la **XmlPoke** tâche pour modifier ces valeurs dans le *SetParameters.xml* fichier.


[!code-xml[Main](configuring-parameters-for-web-package-deployment/samples/sample6.xml)]


Vous remarquerez que chaque **XmlPoke** tâche spécifie quatre valeurs d’attribut :

- Le **XmlInputPath** attribut indique où trouver le fichier que vous souhaitez modifier à la tâche.
- Le **requête** attribut est une requête XPath qui identifie le nœud XML que vous souhaitez modifier.
- Le **valeur** attribut est la nouvelle valeur que vous souhaitez insérer dans le nœud XML sélectionné.
- Le **Condition** attribut est le critère sur lequel la tâche doit s’exécuter ou non. Dans ces cas, la condition permet de s’assurer que vous n’essayez pas d’insérer une valeur null ou vide dans le *SetParameters.xml* fichier.

## <a name="conclusion"></a>Conclusion

Cette rubrique décrit le rôle de la *SetParameters.xml* de fichiers et explique comment il est généré lorsque vous générez un projet d’application web. Il explique comment vous pouvez paramétrer des paramètres supplémentaires en ajoutant un *parameters.xml* fichier à votre projet. Il décrit également comment vous pouvez modifier le *SetParameters.xml* fichier en tant que partie d’un processus de génération supérieure, automatisés, à l’aide de la **XmlPoke** tâche dans vos fichiers projet.

La rubrique suivante, [déploiement des Packages Web](deploying-web-packages.md), décrit comment vous pouvez déployer un package web en exécutant la *. deploy.cmd* de fichiers ou commandes directement à l’aide de MSDeploy.exe. Dans les deux cas, vous pouvez spécifier votre *SetParameters.xml* fichier comme un paramètre de déploiement.

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur la façon de créer des packages web, consultez [génération et des projets d’Application Web empaquetage](building-and-packaging-web-application-projects.md). Pour obtenir des conseils sur la façon de déployer un package web, consultez [déploiement des Packages Web](deploying-web-packages.md). Pour connaître la procédure pas à pas sur la création d’un *parameters.xml* de fichiers, consultez [Comment : utiliser les paramètres pour configurer les paramètres lorsqu’un Package de déploiement est installé](https://msdn.microsoft.com/library/ff398068.aspx).

Pour plus d’informations sur le paramétrage de Web Deploy, consultez [Web déployer le paramétrage dans l’Action](https://go.microsoft.com/?linkid=9805119) (billet de blog).

>[!div class="step-by-step"]
[Précédent](building-and-packaging-web-application-projects.md)
[Suivant](deploying-web-packages.md)

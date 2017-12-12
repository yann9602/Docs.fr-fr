---
uid: web-forms/overview/deployment/advanced-enterprise-web-deployment/troubleshooting-the-packaging-process
title: "Dépannage du processus d’empaquetage | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit comment vous pouvez collecter des informations détaillées sur le processus d’empaquetage à l’aide de la propriété EnablePackageProcessLoggingAndAssert dans le M..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 794bd819-00fc-47e2-876d-fc5d15e0de1c
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/advanced-enterprise-web-deployment/troubleshooting-the-packaging-process
msc.type: authoredcontent
ms.openlocfilehash: 977077357eb5774193a40c55fabee9733dd5ab2f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="troubleshooting-the-packaging-process"></a>Le processus d’empaquetage de résolution des problèmes
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment vous pouvez collecter des informations détaillées sur le processus d’empaquetage à l’aide de la **EnablePackageProcessLoggingAndAssert** propriété dans Microsoft Build Engine (MSBuild).
> 
> Lorsque vous définissez la **EnablePackageProcessLoggingAndAssert** propriété **true**, MSBuild sera :
> 
> - Ajouter des informations supplémentaires sur le processus d’empaquetage pour les journaux de génération.
> - Consigner les erreurs sous certaines conditions, par exemple, si les fichiers en double sont trouvés dans la liste d’empaquetage.
> - Créer un répertoire de journal dans le *nom_projet*\_dossier du Package et l’utiliser pour enregistrer des informations sur les fichiers que vous créez un package.
> 
> Si le processus d’empaquetage est défectueux ou si vos packages de déploiement web ne contiennent pas les fichiers que vous attendez, vous pouvez utiliser ces informations pour dépanner le processus et pinpoint où des opérations incorrect.
> 
> > [!NOTE]
> > Le **EnablePackageProcessLoggingAndAssert** propriété ne fonctionne que si vous générez votre projet en utilisant le **déboguer** configuration. La propriété est ignorée dans d’autres configurations.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md), dans lequel le processus de génération est contrôlé par deux fichiers de & projet #x 2014 ; un contenant les instructions qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement de génération. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="understanding-the-enablepackageprocessloggingandassert-property"></a>Description de la propriété EnablePackageProcessLoggingAndAssert

[Génération et des projets d’Application Web empaquetage](../web-deployment-in-the-enterprise/building-and-packaging-web-application-projects.md) décrit comment le Pipeline de publication Web (WPP) fournit un jeu de cibles de MSBuild qui étendent les fonctionnalités de MSBuild et lui permettre de s’intégrer avec le site Web Internet Information Services (IIS) Outil de déploiement (Web Deploy). Lorsque vous créez un projet d’application web le package, vous appelez des cibles WPP.

Un grand nombre de ces cibles WPP inclure une logique conditionnelle qui enregistre des informations supplémentaires lorsque les **EnablePackageProcessLoggingAndAssert** est définie sur **true**. Par exemple, si vous passez en revue les **Package** cible, vous pouvez voir qu’il crée un répertoire de journal supplémentaires et écrit une liste de fichiers dans un fichier texte si **EnablePackageProcessLoggingAndAssert** est égal à **true**.


[!code-xml[Main](troubleshooting-the-packaging-process/samples/sample1.xml)]


> [!NOTE]
> Les cibles WPP sont définies dans le *Microsoft.Web.Publishing.targets* fichier dans le dossier de %\MSBuild\Microsoft\VisualStudio\v10.0\Web % PROGRAMFILES (x 86). Vous pouvez ouvrir ce fichier et passez en revue les cibles dans Visual Studio 2010 ou de n’importe quel éditeur XML. Vous ne devez ne pas pour modifier le contenu du fichier.


## <a name="enabling-the-additional-logging"></a>L’activation de la journalisation supplémentaire

Vous pouvez fournir une valeur pour le **EnablePackageProcessLoggingAndAssert** propriété de différentes manières, selon la façon dont vous générez votre projet.

Si vous générez votre projet à partir de la ligne de commande, vous pouvez fournir une valeur pour le **EnablePackageProcessLoggingAndAssert** propriété comme argument de ligne de commande :


[!code-console[Main](troubleshooting-the-packaging-process/samples/sample2.cmd)]


Si vous utilisez un fichier de projet personnalisé pour générer vos projets, vous pouvez inclure le **EnablePackageProcessLoggingAndAssert** valeur dans le **propriétés** attribut de la **MSBuild**tâches :


[!code-xml[Main](troubleshooting-the-packaging-process/samples/sample3.xml)]


Si vous utilisez une définition de build Team Foundation Server (TFS) pour créer vos projets, vous pouvez fournir une valeur pour le **EnablePackageProcessLoggingAndAssert** propriété dans le **Arguments MSBuild** ligne :![](troubleshooting-the-packaging-process/_static/image1.png)

> [!NOTE]
> Pour plus d’informations sur la création et configuration des définitions de build, consultez [création d’une Build définition que prend en charge déploiement](../configuring-team-foundation-server-for-web-deployment/creating-a-build-definition-that-supports-deployment.md).


Ou bien, si vous souhaitez inclure le package dans chaque build, vous pouvez modifier le fichier projet pour votre projet d’application web définir le **EnablePackageProcessLoggingAndAssert** propriété **true**. Vous devez ajouter la propriété à la première **PropertyGroup** élément dans votre fichier .csproj ou .vbproj.


[!code-xml[Main](troubleshooting-the-packaging-process/samples/sample4.xml)]


## <a name="reviewing-the-log-files"></a>Vérifier les fichiers journaux

Lorsque vous générez et empaqueter un projet d’application web avec **EnablePackageProcessLoggingAndAssert** la valeur **true**, MSBuild crée un dossier supplémentaire nommé Log dans le *nom_projet* \_Dossier du package. Le dossier du journal contient des fichiers différents :

![](troubleshooting-the-packaging-process/_static/image2.png)

La liste des fichiers que vous voyez varient en fonction des éléments de votre projet et votre processus de génération. Toutefois, ces fichiers sont généralement utilisés pour enregistrer la liste des fichiers qui collecte les fournisseurs de services pour l’empaquetage, à différents stades du processus :

- Le *PreExcludePipelineCollectFilesPhaseFileList.txt* fichier répertorie les fichiers qui collecte de MSBuild pour empaqueter avant que tous les fichiers qui sont spécifiés pour l’exclusion soient supprimés.
- Le *AfterExcludeFilesFilesList.txt* fichier contient la liste des fichiers modifiés après la suppression de tous les fichiers qui sont spécifiés pour l’exclusion.

    > [!NOTE]
    > Pour plus d’informations sur l’exclusion des fichiers et dossiers à partir du processus d’empaquetage, consultez [à l’exclusion des fichiers et dossiers de déploiement](excluding-files-and-folders-from-deployment.md).
- Le *AfterTransformWebConfig.txt* fichier répertorie les fichiers collectés pour l’empaquetage après n’importe quel *Web.config* transformations ont été effectuées. Dans cette liste, toute spécifiques à la configuration *Web.config* transformer des fichiers, tel que *Web.Debug.config* et *Web.Release.config*, sont exclus de la liste des fichiers pour empaquetage. Une seule transformée *Web.config* est inclus à leur place.
- Le *PostAutoParameterizationWebConfigConnectionStrings.txt* fichier contient la liste des fichiers après les chaînes de connexion dans le *Web.config* fichier ont été paramétrables. C’est le processus qui vous permet de remplacer les chaînes de connexion avec les paramètres corrects pour votre environnement cible lorsque vous déployez le package.
- Le *Prepackage.txt* fichier contient la liste de pré-build finalisée de fichiers à inclure dans le package.

> [!NOTE]
> En général, les noms des fichiers journaux supplémentaires correspondent aux cibles WPP. Vous pouvez consulter ces cibles en examinant le *Microsoft.Web.Publishing.targets* fichier dans le dossier de %\MSBuild\Microsoft\VisualStudio\v10.0\Web % PROGRAMFILES (x 86).


Si le contenu de votre package web ne sont pas bien à vos attentes, l’examen de ces fichiers peut être un moyen utile pour identifier à quel moment dans les opérations de processus s’est produite.

## <a name="conclusion"></a>Conclusion

Cette rubrique décrit comment vous pouvez utiliser la **EnablePackageProcessLoggingAndAssert** propriété dans MSBuild pour dépanner le processus d’empaquetage. Il explique les différentes façons dans laquelle vous pouvez fournir la valeur de propriété pour le processus de génération, et il décrit les informations supplémentaires qui sont enregistrées lorsque vous affectez à la propriété **true**.

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur l’utilisation des fichiers projet MSBuild personnalisés pour contrôler le processus de déploiement, consultez [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md) et [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md). Pour plus d’informations sur les fournisseurs de services et la façon dont il gère le processus d’empaquetage, consultez [génération et des projets d’Application Web empaquetage](../web-deployment-in-the-enterprise/building-and-packaging-web-application-projects.md). Pour obtenir des conseils sur la façon d’exclure certains fichiers et dossiers à partir de packages de déploiement web, consultez [à l’exclusion des fichiers et dossiers de déploiement](excluding-files-and-folders-from-deployment.md).

>[!div class="step-by-step"]
[Précédent](running-windows-powershell-scripts-from-msbuild-project-files.md)

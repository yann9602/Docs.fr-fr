---
uid: web-forms/overview/deployment/advanced-enterprise-web-deployment/excluding-files-and-folders-from-deployment
title: "Exclusion de fichiers et dossiers du déploiement | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit comment vous pouvez exclure les fichiers et dossiers à partir d’un package de déploiement web lorsque vous générez et empaqueter un projet d’application web."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: f4cc2d40-6a78-429b-b06f-07d000d4caad
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/advanced-enterprise-web-deployment/excluding-files-and-folders-from-deployment
msc.type: authoredcontent
ms.openlocfilehash: 80810415bac473a58f60110fb9d08772e0627bd5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="excluding-files-and-folders-from-deployment"></a>Exclusion de fichiers et dossiers de déploiement
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment vous pouvez exclure les fichiers et dossiers à partir d’un package de déploiement web lorsque vous générez et empaqueter un projet d’application web.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md), dans lequel le processus de génération est contrôlé par deux fichiers de & projet #x 2014 ; un contenant les instructions qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement de génération. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="overview"></a>Vue d'ensemble

Lorsque vous générez un projet d’application web dans Visual Studio 2010, le Pipeline de publication Web (WPP) vous permet d’étendre ce processus de génération par l’empaquetage de votre application web compilée dans un package de déploiement web. Vous pouvez ensuite utiliser l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy) pour déployer ce package web à un serveur web IIS distant, ou importer le package web manuellement via le Gestionnaire IIS. Ce processus d’empaquetage est expliqué dans [génération et des projets d’Application Web empaquetage](../web-deployment-in-the-enterprise/building-and-packaging-web-application-projects.md).

Comment vous contrôlez ce qui obtient inclus dans votre site web du package ? Les paramètres du projet dans Visual Studio, le fichier de projet sous-jacent, fournissent un contrôle suffisant pour de nombreux scénarios. Toutefois, dans certains cas, vous souhaiterez adapter le contenu de votre package web dans les environnements de destination spécifique. Par exemple, vous souhaiterez inclure un dossier pour les fichiers journaux lorsque vous déployez votre application dans un environnement de test mais excluez le dossier lorsque vous déployez l’application dans un environnement intermédiaire ou de production. Cette rubrique vous indique comment effectuer cette opération.

## <a name="what-gets-included-by-default"></a>Ce qui obtient inclus par défaut ?

Lorsque vous configurez les propriétés de projet d’application web dans Visual Studio, le **éléments à déployer** liste sur le **Package/Publication Web** page vous permet de spécifier ce que vous souhaitez inclure dans votre déploiement web package. Par défaut, il a la valeur **uniquement les fichiers nécessaires pour exécuter cette application**.

![](excluding-files-and-folders-from-deployment/_static/image1.png)

Lorsque vous choisissez **uniquement les fichiers nécessaires pour exécuter cette application**, les fournisseurs de services va tenter de déterminer quels fichiers doivent être ajoutés au package web. Cela concerne :

- Tous les a été généré pour le projet.
- Tous les fichiers marqués avec une action de génération **contenu**.

> [!NOTE]
> La logique qui détermine les fichiers à inclure est contenue dans ce fichier :   
> *%ProgramFiles%\MSBuild\Microsoft\VisualStudio\v10.0\Web\ Microsoft.Web.Publishing.OnlyFilesToRunTheApp.targets*


## <a name="excluding-specific-files-and-folders"></a>À l’exclusion des fichiers et des dossiers

Dans certains cas, vous pouvez répondre à un contrôle plus précis sur lequel les fichiers et dossiers sont déployés. Si vous connaissez les fichiers que vous souhaitez exclure avance par rapport de temps et l’exclusion s’applique à tous les environnements de destination, vous pouvez simplement affecter la **Action de génération** de chaque fichier **aucun**.

**Pour exclure certains fichiers de déploiement**

1. Dans le **l’Explorateur de solutions** fenêtre, cliquez sur le fichier, puis cliquez sur **propriétés**.
2. Dans le **propriétés** fenêtre, dans le **Action de génération** ligne, sélectionnez **aucun**.

Toutefois, cette approche n’est pas toujours pratique. Par exemple, vous pouvez faire varier les fichiers et dossiers sont inclus en fonction de votre environnement de destination et en dehors de Visual Studio. Par exemple, dans l’exemple de solution du Gestionnaire de contacts, examinez le contenu du projet ContactManager.Mvc :

![](excluding-files-and-folders-from-deployment/_static/image2.png)

- Le dossier interne contient des scripts SQL utilisé par le développeur à créer, supprimer et remplir les bases de données locales à des fins de développement. Rien dans ce dossier doit être déployé dans un environnement intermédiaire ou de production.
- Le dossier Scripts contient plusieurs fichiers JavaScript. Un grand nombre de ces fichiers sont incluses uniquement pour prendre en charge le débogage ou fournir IntelliSense dans Visual Studio. Certains de ces fichiers ne doivent pas être déployés dans les environnements de production ou intermédiaire. Toutefois, vous souhaiterez les déployer dans un environnement de test de développeur pour faciliter la résolution des problèmes.

Bien que vous pourriez manipuler vos fichiers projet pour exclure des fichiers et des dossiers, il existe un moyen plus simple. Les fournisseurs de services comprend un mécanisme pour exclure des fichiers et dossiers en créant des listes d’éléments nommés **ExcludeFromPackageFolders** et **ExcludeFromPackageFiles**. Vous pouvez étendre ce mécanisme en ajoutant vos propres éléments de ces listes. Pour ce faire, vous devez effectuer les étapes générales suivantes :

1. Créez un fichier de projet personnalisé nommé *[nom du projet].wpp.targets* dans le même dossier que votre fichier projet.

    > [!NOTE]
    > Le *. wpp.targets* fichier doit aller dans le même dossier que votre fichier de projet d’application web et le #x 2014 ; par exemple, *ContactManager.Mvc.csproj*& #x 2014 ; plutôt que dans le même dossier les fichiers de projet personnalisés que vous utilisez pour le contrôle la génération et le processus de déploiement.
2. Dans le *. wpp.targets* , ajoutez une **ItemGroup** élément.
3. Dans le **ItemGroup** élément, ajouter **ExcludeFromPackageFolders** et **ExcludeFromPackageFiles** éléments à exclure certains fichiers et dossiers en fonction des besoins.

Voici la structure de base de ce *. wpp.targets* fichier :


[!code-xml[Main](excluding-files-and-folders-from-deployment/samples/sample1.xml)]


Notez que chaque élément contient un élément de métadonnées d’élément nommé **FromTarget**. Il s’agit d’une valeur facultative qui n’affecte pas le processus de génération ; Il sert simplement à indiquer pourquoi certains fichiers ou dossiers ont été omises si un utilisateur passe en revue les journaux de génération.

## <a name="excluding-files-and-folders-from-a-web-package"></a>Exclusion de fichiers et dossiers à partir d’un Package Web

La procédure suivante vous montre comment ajouter un *. wpp.targets* fichier pour un projet d’application web et comment utiliser le fichier à exclure certains fichiers et dossiers à partir du package web lorsque vous générez votre projet.

**Pour exclure des fichiers et dossiers à partir d’un package de déploiement web**

1. Ouvrez votre solution dans Visual Studio 2010.
2. Dans le **l’Explorateur de solutions** fenêtre, cliquez sur le nœud de projet de votre application web (par exemple, **ContactManager.Mvc**), pointez sur **ajouter**, puis cliquez sur **Un nouvel élément**.
3. Dans le **ajouter un nouvel élément** boîte de dialogue, sélectionnez le **fichier XML** modèle.
4. Dans le **nom** , tapez *[nom du projet]***. wpp.targets** (par exemple, **ContactManager.Mvc.wpp.targets**), puis cliquez sur  **Ajouter**.

    ![](excluding-files-and-folders-from-deployment/_static/image3.png)

    > [!NOTE]
    > Si vous ajoutez un nouvel élément au nœud racine d’un projet, le fichier est créé dans le même dossier que le fichier projet. Vous pouvez le vérifier en ouvrant le dossier dans l’Explorateur Windows.
5. Dans le fichier, ajoutez une **projet** élément et un **ItemGroup** élément :

    [!code-xml[Main](excluding-files-and-folders-from-deployment/samples/sample2.xml)]
6. Si vous souhaitez exclure des dossiers à partir du package web, ajoutez un **ExcludeFromPackageFolders** élément à la **ItemGroup** élément :

    1. Dans le **Include** d’attribut, de fournir une liste délimitée par des points-virgules des dossiers à exclure.
    2. Dans le **FromTarget** l’élément de métadonnées, fournir une valeur explicite pour indiquer pourquoi les dossiers sont exclus, telles que le nom de la *. wpp.targets* fichier.

    [!code-xml[Main](excluding-files-and-folders-from-deployment/samples/sample3.xml)]
7. Si vous souhaitez exclure des fichiers à partir du package web, ajoutez un **ExcludeFromPackageFiles** élément à la **ItemGroup** élément :

    1. Dans le **Include** d’attribut, de fournir une liste délimitée par des points-virgules des fichiers à exclure.
    2. Dans le **FromTarget** l’élément de métadonnées, fournir une valeur explicite pour indiquer pourquoi les fichiers sont exclus, telles que le nom de la *. wpp.targets* fichier.

    [!code-xml[Main](excluding-files-and-folders-from-deployment/samples/sample4.xml)]
8. Le *[nom du projet].wpp.targets* fichier doit maintenant ressembler à ceci :

    [!code-xml[Main](excluding-files-and-folders-from-deployment/samples/sample5.xml)]
9. Enregistrez et fermez le *[nom du projet].wpp.targets* fichier.

La prochaine génération et package de votre projet d’application web, les fournisseurs de services détecte automatiquement le *. wpp.targets* fichier. Tous les fichiers et dossiers que vous avez spécifié ne pas être inclus dans le package web.

## <a name="conclusion"></a>Conclusion

Cette rubrique décrit comment exclure des fichiers et des dossiers lorsque vous générez un package web, en créant une personnalisée *. wpp.targets* fichier dans le même dossier que votre fichier de projet d’application web.

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur l’utilisation de fichiers de projet Microsoft Build Engine (MSBuild) personnalisés pour contrôler le processus de déploiement, consultez [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md) et [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md). Pour plus d’informations sur l’empaquetage et le processus de déploiement, consultez [génération et des projets d’Application Web Packaging](../web-deployment-in-the-enterprise/building-and-packaging-web-application-projects.md), [configuration des paramètres pour le déploiement du Package Web](../web-deployment-in-the-enterprise/configuring-parameters-for-web-package-deployment.md), et [ Déploiement de Packages Web](../web-deployment-in-the-enterprise/deploying-web-packages.md).

>[!div class="step-by-step"]
[Précédent](deploying-membership-databases-to-enterprise-environments.md)
[Suivant](taking-web-applications-offline-with-web-deploy.md)

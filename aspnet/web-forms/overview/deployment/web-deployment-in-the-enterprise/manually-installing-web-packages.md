---
uid: web-forms/overview/deployment/web-deployment-in-the-enterprise/manually-installing-web-packages
title: Installation manuelle des Packages Web | Documents Microsoft
author: jrjlee
description: "Cette rubrique décrit comment importer manuellement un package de déploiement web dans Internet Information Services (IIS). La construction de la rubrique et l’empaquetage Web Applicati..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: f11d22a7-5d32-4ad0-8a9b-276460a61c06
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/web-deployment-in-the-enterprise/manually-installing-web-packages
msc.type: authoredcontent
ms.openlocfilehash: e06d37c01ab66f0723b687f4ed1ee72561099aef
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="manually-installing-web-packages"></a>Installation manuelle des Packages Web
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment importer manuellement un package de déploiement web dans Internet Information Services (IIS).
> 
> La rubrique [génération et des projets d’Application Web empaquetage](building-and-packaging-web-application-projects.md) décrit comment IIS outil de déploiement Web (Web Deploy), en association avec Microsoft Build Engine (MSBuild) et Web Publishing Pipeline (WPP), vous permet de vous empaquetez votre projets d’application Web dans un fichier zip unique. Ce fichier, communément appelé un package de déploiement web (ou simplement un package de déploiement), contient toutes les informations de contenu et de configuration que IIS a besoin pour recréer votre application web sur un serveur web.
> 
> Une fois que vous avez créé un package de déploiement web, vous pouvez le publier sur un serveur IIS de différentes manières. Dans de nombreux scénarios, que vous souhaitez tirer parti des points d’intégration entre MSBuild, WPP et Web Deploy pour créer et installer des packages web à distance dans le cadre d’un processus de génération et de déploiement automatique ou seule étape. Ce processus est décrit dans [déploiement des Packages Web](deploying-web-packages.md). Toutefois, cela n’est pas toujours possible. Supposons que vous souhaitez déployer une application web dans un environnement de production sur Internet. Pour des raisons de sécurité, tel un environnement de production est à la moins susceptibles d’être derrière un pare-feu sur un sous-réseau qui est distinct du serveur de build, dans un réseau de périmètre (également appelé DMZ, zone démilitarisée et sous-réseau filtré). Dans de nombreux cas, l’environnement de production peut être sur un domaine distinct ou sur un réseau isolé physiquement.
> 
> Dans ces scénarios, votre seule option peut être déplacer le package web sur le serveur de destination et de l’importer manuellement dans IIS. Bien que cette approche vous empêche de déploiement automatisé, il est toujours une technique très efficace pour la publication d’une application web & le #x 2014 ; vous copiez simplement un fichier zip unique à votre serveur web, puis utilisez un Assistant pour vous guider tout au long du processus d’importation.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

## <a name="task-overview"></a>Vue d’ensemble de la tâche

Vous aurez besoin effectuer ces tâches de haut niveau pour importer un package de déploiement web dans IIS :

- Créer un package de déploiement web à l’aide de la MSBuild ligne de commande, Team Build ou Visual Studio 2010.
- Copiez le package web vers le serveur web de destination.
- Utilisez l’Assistant Importation de Package d’Application dans le Gestionnaire des services Internet pour installer le package web et de fournir des valeurs pour les variables telles que les chaînes de connexion et les points de terminaison de service.

Cette rubrique vous indique comment effectuer ces procédures. Les tâches et les procédures pas à pas dans cette rubrique supposent que vous êtes déjà familiarisé avec les concepts qui sous-tendent les packages web, Web Deploy et les fournisseurs de services. Pour plus d’informations, consultez [génération et des projets d’Application Web empaquetage](building-and-packaging-web-application-projects.md).

> [!NOTE]
> Cette rubrique est mieux utilisée conjointement avec [configurer un serveur Web pour déployer de publication Web (le déploiement en mode hors connexion)](../configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md), qui explique comment installer les composants requis et préparer un site Web IIS pour l’importation du package.


## <a name="create-a-web-deployment-package"></a>Créer un Package de déploiement Web

La première tâche consiste à créer un package de déploiement web pour le projet d’application web à déployer. Vous pouvez créer des packages web de plusieurs façons.

**Méthode 1 : Créer un package dans le cadre du processus de génération avec Visual Studio**

Vous pouvez configurer votre projet d’application web pour créer un package de déploiement web après chaque génération via la **Package/Publication Web** onglet sur les pages de propriétés du projet. Ce processus est décrit dans [génération et des projets d’Application Web empaquetage](building-and-packaging-web-application-projects.md).

**Méthode 2 : Créer un package dans le cadre du processus de génération avec MSBuild**

Si vous générez votre projet d’application web à l’aide de MSBuild directement, soit via un fichier de projet MSBuild personnalisé à partir de la ligne de commande, vous pouvez créer un package de déploiement web dans le cadre du processus de génération en incluant le **DeployOnBuild = true** et **DeployTarget = Package** propriétés dans votre commande. Ce processus est décrit dans [comprendre le processus de génération](understanding-the-build-process.md).

**Méthode 3 : Créer un package à la demande dans Visual Studio**

Vous pouvez créer un package de déploiement web pour un projet d’application web à tout moment dans Visual Studio 2010. Pour ce faire, dans le **l’Explorateur de solutions** fenêtre, avec le bouton droit de votre projet d’application web, puis cliquez sur **générer un Package de déploiement**.

![](manually-installing-web-packages/_static/image1.png)

**Méthode 4 : Créer un package à la demande à partir de la ligne de commande**

Vous pouvez créer un package de déploiement web à partir de la ligne de commande en appelant le **Package** cible sur votre projet d’application web à l’aide de MSBuild. La commande doit ressembler à ceci :


[!code-console[Main](manually-installing-web-packages/samples/sample1.cmd)]


Selon l’approche que vous utilisez, le résultat final est le même. Les fournisseurs de services crée un package de déploiement web comme un fichier .zip, ainsi que diverses ressources prise en charge dans le dossier de sortie de votre projet d’application web.

![](manually-installing-web-packages/_static/image2.png)

Lorsque vous avez l’intention d’importer le package web manuellement, vous avez besoin uniquement le fichier zip. Copiez ce fichier sur votre serveur web de cible, et vous pouvez commencer le processus d’importation.

## <a name="import-a-web-package-into-iis"></a>Importer un Package Web dans IIS

Vous pouvez utiliser la procédure suivante pour importer un package de déploiement web à partir du système de fichiers local dans un site Web IIS. Avant d’effectuer cette procédure, assurez-vous d’avoir :

- Copier le package de déploiement web sur le serveur web.
- Configuration du serveur web IIS pour héberger votre application.

Pour plus d’informations sur la configuration d’un serveur web d’IIS pour prendre en charge les packages de déploiement web, consultez [configurer un serveur Web pour déployer de publication Web (le déploiement en mode hors connexion)](../configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md).

**Pour importer un package de déploiement web à l’aide du Gestionnaire des services Internet**

1. Dans le Gestionnaire des services Internet, dans le **connexions** volet, avec le bouton droit de votre site Web IIS, pointez sur **déployer**, puis cliquez sur **importer l’Application**.

    ![](manually-installing-web-packages/_static/image3.png)
2. Dans l’Assistant Importation de Package Application, sur le **sélectionner le Package** page, accédez à l’emplacement de votre package de déploiement web, puis cliquez sur **suivant**.
3. Sur le **sélectionner le contenu du Package** page, désactivez tout contenu que vous ne nécessitent, puis cliquez sur **suivant**.

    ![](manually-installing-web-packages/_static/image4.png)

    > [!NOTE]
    > Dans de nombreux cas, vous voudrez pas importer tout ce qui est fourni avec un package de déploiement web. Par exemple, vous voudrez pas autoriser le déploiement Web pour remplacer la base de données associée.  
    > Le **accorder des autorisations** entrées de définir des autorisations sur le système de fichiers de destination pour vérifier que l’identité du pool d’applications permettre accéder le dossier physique qui stocke le contenu du site Web. En outre, l’utilisateur de l’authentification anonyme autorisation de lecture au dossier pour permettre à l’application de servir des fichiers de type MIME Multipurpose Internet Mail Extensions (). Si vous préférez, vous pouvez supprimer ces entrées et configurer manuellement les autorisations.
4. Sur le **entrer les informations sur le Package d’Application** , fournissez les informations demandées.

    ![](manually-installing-web-packages/_static/image5.png)
5. Lorsque vous créez un package web, les fournisseurs de services analyse le fichier de configuration pour votre application et détecte toutes les variables, telles que les chaînes de connexion et les points de terminaison de service. Dans ce cas :

    1. **Chemin d’accès de l’application** est le chemin d’accès IIS où vous souhaitez installer votre application. Ce paramètre est commun à tous les packages de déploiement créés par les fournisseurs de services.
    2. **Adresse de point de terminaison de Service ContactService** est l’adresse de l’application doit utiliser pour communiquer avec le service WCF déployé. Ce paramètre correspond à une entrée dans le *web.config* fichier.
    3. La première **chaîne de connexion** paramètre est la chaîne de connexion que Web Deploy doit utiliser pour déployer la base de données associé à l’application (dans ce cas une appartenance base de données ASP.NET). Ce paramètre correspond au paramètre sur le **Package/Publication SQL** onglet dans Visual Studio.
    4. La seconde **chaîne de connexion** paramètre est la chaîne de connexion qui utilise votre application pour communiquer avec la base de données lorsqu’il est en cours d’exécution. Cela correspond à une entrée de chaîne de connexion dans le *web.config* fichier.

        > [!NOTE]
        > Pour plus d’informations sur la provenance de ces paramètres, consultez [configuration des paramètres pour le déploiement du Package Web](configuring-parameters-for-web-package-deployment.md).
6. Cliquez sur **Suivant**.
7. Si ce n’est pas la première fois que vous avez déployé l’application à ce site Web, vous êtes invité à spécifier si vous souhaitez supprimer tout le contenu existant avant l’installation. Choisissez l’option qui convient à vos besoins, puis cliquez sur **suivant**.

    ![](manually-installing-web-packages/_static/image6.png)
8. Lorsque IIS a terminé l’installation du package, cliquez sur **Terminer**.

    ![](manually-installing-web-packages/_static/image7.png)

À ce stade, vous avez publié votre application web sur IIS.

## <a name="conclusion"></a>Conclusion

Cette rubrique décrit comment importer un package de déploiement web dans un site Web IIS à l’aide du Gestionnaire des services Internet. Cette approche pour la publication d’application web est appropriée lorsque les contraintes de sécurité ou d’infrastructure facilitent le déploiement distant impossible ou déconseillé.

## <a name="further-reading"></a>informations supplémentaires

Pour obtenir des conseils sur la façon de configurer un serveur web d’IIS pour prendre en charge l’importation manuelle d’un package web, consultez [configurer un serveur Web pour déployer de publication Web (le déploiement en mode hors connexion)](../configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md). Pour obtenir des instructions plus générales sur le déploiement des packages web, consultez [procédure pas à pas : déploiement d’un projet d’Application Web à l’aide d’un Package de déploiement Web (partie 1 sur 4)](https://msdn.microsoft.com/library/dd483479.aspx).

>[!div class="step-by-step"]
[Précédent](creating-and-running-a-deployment-command-file.md)

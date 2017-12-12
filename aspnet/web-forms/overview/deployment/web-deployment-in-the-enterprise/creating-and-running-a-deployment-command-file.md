---
uid: web-forms/overview/deployment/web-deployment-in-the-enterprise/creating-and-running-a-deployment-command-file
title: "Fichier de commandes de création et exécution d’un déploiement | Documents Microsoft"
author: jrjlee
description: "Cette rubrique explique comment générer un fichier de commandes qui vous permettent d’exécuter un déploiement à l’aide de fichiers de projet Microsoft Build Engine (MSBuild) en tant qu’une seule étape, re..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: c61560e9-9f6c-4985-834a-08a3eabf9c3c
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/web-deployment-in-the-enterprise/creating-and-running-a-deployment-command-file
msc.type: authoredcontent
ms.openlocfilehash: 729b4fa4c461eedbd0447371102010451eb51586
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-and-running-a-deployment-command-file"></a>Création et exécution d’un fichier de commandes de déploiement
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment créer un fichier de commandes qui vous permettent d’exécuter un déploiement à l’aide de fichiers de projet Microsoft Build Engine (MSBuild) comme un processus étape unique et reproductible.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [Contact Manager](the-contact-manager-solution.md) solution & #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [comprendre le processus de génération](understanding-the-build-process.md), dans lequel le processus de génération est contrôlé par deux fichiers de & projet #x 2014 ; un contenant les instructions qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement de génération. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="process-overview"></a>Vue d’ensemble du processus

Dans cette rubrique, vous allez apprendre à créer et exécuter un fichier de commande qui utilise ces fichiers de projet pour effectuer un déploiement reproductible pour votre environnement cible. Essentiellement, le fichier de commandes doit simplement contenir une commande MSBuild qui :

- Indique à MSBuild pour exécuter l’environnement agnostique *Publish.proj* fichier.
- Indique le *Publish.proj* fichier fichier qui contient des paramètres spécifiques à l’environnement et où il se trouve.

## <a name="create-an-msbuild-command"></a>Créer une commande de MSBuild

Comme décrit dans [comprendre le processus de génération](understanding-the-build-process.md), le fichier de projet spécifique à un environnement & #x 2014 ; par exemple, *Env-Dev.proj*& #x 2014 ; est conçue pour être importé dans le indépendant de l’environnement *Publish.proj* fichier au moment de la génération. Ensemble, ces deux fichiers fournissent un ensemble complet d’instructions qui indiquent comment générer et déployer votre solution à MSBuild.

Le *Publish.proj* fichier utilise une **importer** élément pour importer le fichier de projet spécifique à l’environnement.


[!code-xml[Main](creating-and-running-a-deployment-command-file/samples/sample1.xml)]


Par conséquent, lorsque vous utilisez MSBuild.exe pour générer et déployer la solution de gestionnaire de contacts, vous devez :

- Exécutez MSBuild.exe le *Publish.proj* fichier.
- Spécifiez l’emplacement du fichier de projet spécifique à un environnement en fournissant un paramètre de ligne de commande nommé **TargetEnvPropsFile**.

Pour ce faire, votre commande MSBuild doit ressembler à ceci :


[!code-console[Main](creating-and-running-a-deployment-command-file/samples/sample2.cmd)]


À ce stade, il s’agit d’une étape simple à déplacer vers un déploiement reproductible, la seule étape. Vous devez faire consiste à ajouter votre commande de MSBuild à un fichier .cmd. Dans la solution de gestionnaire de contacts, le dossier de publication contient un fichier nommé *Dev.cmd de publication* qui fait cela.


[!code-console[Main](creating-and-running-a-deployment-command-file/samples/sample3.cmd)]


> [!NOTE]
> Le **/fl** commutateur indique à MSBuild pour créer un fichier journal nommé *msbuild.log* dans le répertoire de travail dans lequel MSBuild.exe a été appelé.


Pour déployer ou de redéployer la solution de gestionnaire de contacts, vous devez simplement est exécuté le *Dev.cmd de publication* fichier. Lorsque vous exécutez le fichier, MSBuild va :

- Générer tous les projets de la solution.
- Génèrent des packages de déploiement web pour les projets d’application web.
- Générer des fichiers .dbschema et .deploymanifest pour les projets de base de données.
- Déployer les packages web sur le serveur web.
- Déployer la base de données sur le serveur de base de données.

## <a name="run-the-deployment"></a>Exécuter le déploiement

Lorsque vous avez créé un fichier de commandes pour votre environnement cible, vous devez être en mesure d’effectuer le déploiement complet en exécutant simplement le fichier.

**Pour déployer la solution de gestionnaire de contacts dans votre environnement de test**

1. Sur votre station de travail du développeur, ouvrez l’Explorateur Windows, puis accédez à l’emplacement de la *Dev.cmd de publication* fichier.
2. Double-cliquez sur le fichier pour l’exécuter.
3. Si un **ouvrir un fichier – Avertissement de sécurité** boîte de dialogue s’affiche, cliquez sur **exécuter**.
4. Si vos paramètres de configuration et les serveurs de test sont configurés correctement, la fenêtre d’invite de commandes affiche un **Build a réussi** message lorsque MSBuild a terminé le traitement de fichiers du projet.

    ![](creating-and-running-a-deployment-command-file/_static/image1.png)
5. S’il s’agit de la première fois que vous avez déployé la solution à cet environnement, vous devez ajouter le compte d’ordinateur du serveur test web pour le **db\_datawriter** et **db\_datareader**rôles sur le **ContactManager** base de données. Cette procédure est décrite dans [configurer un serveur de base de données pour la publication de déploiement Web](../configuring-server-environments-for-web-deployment/configuring-a-database-server-for-web-deploy-publishing.md).

    > [!NOTE]
    > Il vous suffit de vous attribuez ces autorisations lorsque vous créez la base de données. Par défaut, le processus de génération ne recrée pas la base de données sur chaque #x 2014 et le déploiement ; au lieu de cela, il compare la base de données pour le schéma le plus récent et assurez-vous que les modifications nécessaires. Par conséquent, vous devez uniquement mapper ces rôles de base de données de la première fois que vous déployez la solution.
6. Ouvrez Internet Explorer et accédez à l’URL de l’application du responsable du Contact (par exemple, `http://testweb1:85/ContactManager/`).
7. Vérifiez que l’application fonctionne comme prévu et que vous êtes en mesure d’ajouter des contacts.

    ![](creating-and-running-a-deployment-command-file/_static/image2.png)

## <a name="conclusion"></a>Conclusion

Création d’un fichier de commandes contenant vos instructions MSBuild fournit un moyen simple et rapide de créer et déployer une solution à projets multiples dans un environnement de destination spécifique. Si vous avez besoin déployer à plusieurs reprises de votre solution dans plusieurs environnements de destination, vous pouvez créer plusieurs fichiers de commandes. Dans chaque fichier de commandes, la commande MSBuild génère le même fichier de projet d’application universelle, mais elle spécifie un fichier de projet spécifique à l’environnement différent. Par exemple, un fichier de commandes pour publier à un développeur ou l’environnement de test peut contenir cette commande MSBuild :


[!code-console[Main](creating-and-running-a-deployment-command-file/samples/sample4.cmd)]


Un fichier de commandes pour publier dans un environnement intermédiaire peut contenir cette commande MSBuild :


[!code-console[Main](creating-and-running-a-deployment-command-file/samples/sample5.cmd)]


> [!NOTE]
> Pour obtenir des conseils sur la façon de personnaliser les fichiers de projet spécifique à un environnement pour vos propres environnements de serveur, consultez [configurer les propriétés de déploiement pour un environnement cible](../configuring-server-environments-for-web-deployment/configuring-deployment-properties-for-a-target-environment.md).


Vous pouvez également personnaliser le processus de génération pour chaque environnement par substitution de propriétés ou de la définition de plusieurs autres commutateurs dans votre commande de MSBuild. Pour plus d’informations, consultez [référence de ligne de commande MSBuild](https://msdn.microsoft.com/en-us/library/ms164311.aspx).

>[!div class="step-by-step"]
[Précédent](deploying-database-projects.md)
[Suivant](manually-installing-web-packages.md)

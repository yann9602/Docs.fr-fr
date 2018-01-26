---
uid: web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/configuring-a-tfs-build-server-for-web-deployment
title: "Configuration d’un serveur TFS du serveur de builds pour le déploiement Web | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit comment préparer un serveur de builds Team Foundation Server (TFS) pour générer et déployer vos solutions à l’aide de Team Build et le Informat Internet..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: f8400241-4f4b-4bbd-9994-54fb64909e6e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/configuring-a-tfs-build-server-for-web-deployment
msc.type: authoredcontent
ms.openlocfilehash: de31a9dffb95b863a4ec38b74fd2c6e03f287a7f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="configuring-a-tfs-build-server-for-web-deployment"></a>Configuration d’un serveur de Build TFS pour le déploiement Web
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment préparer un serveur de builds Team Foundation Server (TFS) pour générer et déployer vos solutions à l’aide de Team Build et l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy).


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md), dans lequel le processus de génération est contrôlé par deux fichiers de & projet #x 2014 ; un contenant les instructions qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement de génération. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="task-overview"></a>Vue d’ensemble de la tâche

Pour préparer un serveur de builds pour générer et déployer vos solutions, vous devez :

- Installez et configurez le service de build TFS.
- Installer Visual Studio 2010.
- Installer les produits et les composants qui sont requis pour générer votre solution, comme dans les versions du .NET Framework ou ASP.NET MVC.
- Installez Web Deploy 2.0 ou version ultérieure.

Cette rubrique vous indique comment effectuer ces procédures, ou pointer sur d’autres ressources s’ils existent. Les tâches et les procédures pas à pas dans cette rubrique supposent que :

- Vous débutez avec une build propre serveur exécutant Windows Server 2008 R2 Service Pack 1.
- Le serveur est joint au domaine avec une adresse IP statique.
- Vous avez installé la couche application TFS sur un serveur distinct, comme décrit dans [déploiement Web d’entreprise : vue d’ensemble du scénario](../deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview.md).

### <a name="who-performs-these-procedures"></a>Qui exécute ces procédures ?

Dans la plupart des cas, un administrateur TFS sera chargé de configurer les serveurs de builds. Dans certains cas, l’équipe de développement peut prendre possession des serveurs de build spécifique.

## <a name="install-and-configure-the-tfs-build-service"></a>Installer et configurer le Service de Build TFS

Lorsque vous configurez un serveur de builds, la première tâche consiste à installer et configurer le service de build TFS. Dans le cadre de ce processus, vous devez :

- Installer le service de build TFS et configurer un compte de service. Toutes les tâches de génération, notamment le déploiement, seront exécute à l’aide de l’identité du compte de service de build.
- Créer un *le contrôleur de build* et un ou plusieurs *agents de build*. Chaque contrôleur de build gère un ensemble d’agents de build. Lorsque vous la file d’attente une build, le contrôleur de build assigne la tâche de génération à un agent de build disponible. Chaque collection de projets d’équipe dans TFS est mappée à un contrôleur de build unique.
- Configurer un dossier de dépôt pour les sorties de génération. Il s’agit d’un partage réseau. Une génération, comme les packages de déploiement web, sont envoyés dans le dossier de dépôt.

Le [administration de Team Foundation Build](https://msdn.microsoft.com/library/ms252495.aspx) chapitre sur MSDN contient toutes les ressources que vous avez besoin pour effectuer ces tâches :

- Pour obtenir une vue d’ensemble conceptuelle de Team Foundation Build, y compris le service de build, les contrôleurs de build et les agents de build, consultez [présentation d’un système de génération Team Foundation](https://msdn.microsoft.com/library/dd793166.aspx).
- Pour plus d’informations sur l’installation et configuration du service de build, consultez [configurer un ordinateur de Build](https://msdn.microsoft.com/library/ms181712.aspx).
- Pour plus d’informations sur la création des contrôleurs de build, consultez [créer et utiliser avec un contrôleur de Build](https://msdn.microsoft.com/library/ee330987.aspx).
- Pour plus d’informations sur la création des agents de build, consultez [créer et utiliser des Agents de Build](https://msdn.microsoft.com/library/bb399135.aspx).
- Pour plus d’informations sur la création et configuration de dossiers de dépôt, consultez [configurer des dossiers cibles](https://msdn.microsoft.com/library/bb778394.aspx).

## <a name="install-required-products-and-components"></a>Installer des composants et des produits requis

Pour activer le serveur de builds générer vos solutions, vous devez installer les produits, les composants ou les assemblys requis par votre solution. Avant d’installer tous les composants de plateforme web, vous devez installer Visual Studio 2010 (toute version) sur le serveur de builds. Cela garantit que les fichiers de cible de base Microsoft Build Engine (MSBuild) et les fichiers de la cible Web Publishing Pipeline (WPP) sont disponibles pour le service de build. Le programme d’installation de Visual Studio doit également installer Web Deploy, vous aurez besoin si vous envisagez de déployer des packages web dans le cadre de votre processus de génération.

La meilleure façon d’installer les composants de plateforme web commune consiste à utiliser le [Web Platform Installer](https://go.microsoft.com/?linkid=9805118). Cela garantit que vous installez la version la plus récente de chaque produit et qu’il détecte et installe les composants requis pour chaque produit d’également automatiquement. Dans le cas de la [Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md) solution, vous devez utiliser Web Platform Installer pour installer ces produits et composants :

- **.NET framework 4.0**. Cela est nécessaire pour exécuter des applications qui ont été créées sur cette version du .NET Framework.
- **Outil de déploiement 2.1 ou version ultérieure de Web**. Cette commande installe Web Deploy (et son fichier exécutable sous-jacent, MSDeploy.exe) sur votre serveur. Dans le cadre de ce processus, il installe et démarre le Service de l’Agent de déploiement Web. Ce service vous permet de déployer des packages web à partir d’un ordinateur distant.
- **ASP.NET MVC 3**. Cette opération installe les assemblys que vous avez besoin pour exécuter des applications ASP.NET MVC 3.

**Pour installer les produits requis et les composants**

1. Installer Visual Studio 2010. Lorsque vous êtes invité à sélectionner les fonctionnalités à installer, vous devez inclure :

    1. Les langages de programmation dont vous avez besoin pour compiler.
    2. Visual Web Developer. Cela garantit que les cibles WPP sont ajoutés à votre serveur de builds.

        ![](configuring-a-tfs-build-server-for-web-deployment/_static/image1.png)
2. Lors de l’installation de Visual Studio 2010 est terminée, téléchargez et installez [Visual Studio 2010 Service Pack 1](https://go.microsoft.com/?linkid=9805133) (si elle n'est pas déjà inclus dans votre support d’installation).

    > [!NOTE]
    > Visual Studio 2010 Service Pack 1 résout un bogue qui peut empêcher le localiser l’exécutable de MSDeploy de MSBuild.
3. Téléchargez et lancez le [Web Platform Installer](https://go.microsoft.com/?linkid=9805118).
4. En haut de la **Web Platform Installer 3.0** fenêtre, cliquez sur **produits**.
5. Sur le côté gauche de la fenêtre, dans le volet de navigation, cliquez sur **infrastructures**.
6. Dans le **Microsoft .NET Framework 4** de ligne, si le .NET Framework n’est pas déjà installé, cliquez sur **ajouter**.

    > [!NOTE]
    > Vous avez peut-être déjà installé le .NET Framework 4.0 via Windows Update. Si un produit ou un composant est déjà installé, le programme d’installation de la plateforme Web indiquera en remplaçant le **ajouter** bouton avec le texte **installé**.

    ![](configuring-a-tfs-build-server-for-web-deployment/_static/image2.png)
7. Dans le **ASP.NET MVC 3 (Visual Studio 2010)** , cliquez sur **ajouter**.
8. Dans le volet de navigation, cliquez sur **Server**.
9. Dans le **2.1 d’outil de déploiement Web** , cliquez sur **ajouter**.
10. Cliquez sur **Installer**. Le programme d’installation de la plateforme Web affiche une liste de produits & #x 2014 ; ainsi que toutes les dépendances associées et les #x 2014 ; à installer et vous invite à accepter les termes du contrat de licence.
11. Passez en revue les termes du contrat de licence et si vous acceptez les termes du contrat, cliquez sur **J’accepte**.
12. Une fois l’installation terminée, cliquez sur **Terminer**, puis fermez le **Web Platform Installer 3.0** fenêtre.

> [!NOTE]
> Si votre processus de déploiement inclut l’utilisation des outils comme VSDBCMD.exe ou SQLCMD.exe, vous devez vous assurer qu’ils sont installés sur votre serveur de builds. VSDBCMD.exe est un outil de Visual Studio et est généralement ajoutée au serveur lorsque vous installez Team Foundation Build. SQLCMD.exe est un outil de SQL Server. Vous pouvez télécharger une version autonome de SQLCMD.exe à partir de la [Microsoft SQL Server 2008 R2 Feature Pack](https://go.microsoft.com/?linkid=9805134) page.


## <a name="conclusion"></a>Conclusion

À ce stade, votre serveur de builds est prêt à commencer à créer et déployer vos projets d’application web. La rubrique suivante, [création d’une Build définition que prend en charge déploiement](creating-a-build-definition-that-supports-deployment.md), décrit comment créer et configurer une définition de build pour contrôler quand et comment vos projets sont générés et déployés.

## <a name="further-reading"></a>informations supplémentaires

Pour obtenir des instructions plus générales sur l’utilisation de Team Build, consultez [administration de Team Foundation Build](https://msdn.microsoft.com/library/ms252495.aspx).

>[!div class="step-by-step"]
[Précédent](adding-content-to-source-control.md)
[Suivant](creating-a-build-definition-that-supports-deployment.md)

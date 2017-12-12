---
uid: web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/creating-a-build-definition-that-supports-deployment
title: "Création d’une définition de Build qui prend en charge le déploiement | Documents Microsoft"
author: jrjlee
description: "Si vous souhaitez effectuer tout type de build dans Team Foundation Server (TFS) 2010, vous devez créer une définition de build dans votre projet d’équipe. De cette rubrique..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: fe47a018-f6d0-4979-80e7-5b1fa75a5865
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/creating-a-build-definition-that-supports-deployment
msc.type: authoredcontent
ms.openlocfilehash: c2e7a768c2cf9900731b822ec187093a4b250ead
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-build-definition-that-supports-deployment"></a>Création d’une définition de Build qui prend en charge le déploiement
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Si vous souhaitez effectuer tout type de build dans Team Foundation Server (TFS) 2010, vous devez créer une définition de build dans votre projet d’équipe. Cette rubrique décrit comment créer une définition de build dans TFS et comment contrôler le déploiement web dans le cadre du processus de génération dans Team Build.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md), dans lequel le processus de génération et de déploiement est contrôlé par deux fichiers projet & #x 2014 ; o ne contenant les instructions de génération qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="task-overview"></a>Vue d’ensemble de la tâche

Une définition de build est le mécanisme qui contrôle comment et quand les builds se produisent pour les projets d’équipe dans TFS. Chaque définition de build spécifie :

- Les éléments que vous souhaitez générer, tels que les fichiers de solution Visual Studio ou des fichiers de projet personnalisés de Microsoft Build Engine (MSBuild).
- Les critères qui déterminent quand une build doit avoir lieu, tout comme les déclencheurs manuelles, l’intégration continue (CI), ou archivages.
- L’emplacement sur lequel Team Build doit envoyer les sorties de génération, y compris les artefacts de déploiement tels que les packages web et de scripts de base de données.
- La durée pendant laquelle chaque build doit être conservé.
- Divers autres paramètres du processus de génération.

> [!NOTE]
> Pour plus d’informations sur les définitions de build, consultez [définir votre processus de génération](https://msdn.microsoft.com/en-us/library/ms181715.aspx).


Cette rubrique vous indique comment créer une définition de build qui utilise l’élément de configuration, afin qu’une build est déclenchée lorsqu’un développeur archive du nouveau contenu. Si la génération réussit, le service de build exécute un fichier de projet personnalisés pour déployer la solution dans un environnement de test.

Lorsque vous déclenchez une build, ces actions doivent se produire :

- Tout d’abord, Team Build doit générer la solution. Dans le cadre de ce processus, Team Build appelle le Pipeline de publication Web (WPP) pour générer des packages de déploiement web pour les projets d’application web dans la solution. Team Build sera également exécuter des tests unitaires associés à la solution.
- Si la génération échoue, la Team Build ne doit prendre aucune autre action. Échecs des tests unitaires doivent être traités comme un échec de génération.
- Si la génération réussit, Team Build doit s’exécuter le fichier de projet personnalisé qui contrôle le déploiement de la solution. Dans le cadre de ce processus, Team Build appelle l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy) pour installer les applications empaquetées web sur les serveurs web de destination, et il appelle l’utilitaire VSDBCMD.exe pour exécuter la création de la base de données scripts sur les serveurs de base de données de destination.

Ceci illustre le processus :

![](creating-a-build-definition-that-supports-deployment/_static/image1.png)

Le [le Gestionnaire de contacts](../web-deployment-in-the-enterprise/the-contact-manager-solution.md) exemple de solution inclut un fichier de projet MSBuild personnalisé, *Publish.proj*, que vous pouvez exécuter à partir de MSBuild ou Team Build. Comme décrit dans [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md), ce fichier de projet définit la logique qui déploie vos packages web et les bases de données à un environnement cible. Le fichier inclut une logique qui omet la génération et le processus d’empaquetage si elle s’exécute dans Team Build, en laissant uniquement les tâches de déploiement à exécuter. En effet, lorsque vous automatisez le déploiement de cette façon, vous souhaiterez généralement vous assurer que la solution générée avec succès et passe des tests unitaires avant le processus de déploiement.

La section suivante explique comment implémenter ce processus en créant une définition de build.

> [!NOTE]
> Cette procédure, & #x 2014 ; dans lequel un processus automatisé unique génère, tests et déploie une solution & le #x 2014 ; est susceptibles d’être plus adaptée au déploiement pour les environnements de test. Pour les environnements intermédiaire et de production, vous êtes plus susceptible de vouloir déployer le contenu à partir d’une build précédente que vous avez déjà vérifié et validé dans un environnement de test. Cette approche est décrite dans la rubrique suivante, [déploiement d’une Build spécifique](deploying-a-specific-build.md).


### <a name="who-performs-this-procedure"></a>Qui exécute cette procédure ?

En règle générale, un administrateur TFS effectue cette procédure. Dans certains cas, un responsable d’équipe de développement peut prendre la responsabilité de la collection de projets d’équipe dans TFS. Pour créer une définition de build, vous devez être un membre de la **Project Collection Build Administrators** groupe pour la collection de projets d’équipe qui contient votre solution.

## <a name="create-a-build-definition-for-ci-and-deployment"></a>Créer une définition de Build pour l’élément de configuration et de déploiement

La procédure suivante décrit comment créer une définition de build qui a déclenché par l’élément de configuration. Si la génération réussit, la solution est déployée à l’aide de la logique d’un fichier de projet MSBuild personnalisé.

**Pour créer une définition de build pour l’élément de configuration et de déploiement**

1. Dans Visual Studio 2010, dans le **Team Explorer** fenêtre, développez le nœud de votre projet d’équipe, cliquez sur **génère**, puis cliquez sur **nouvelle définition de Build**.

    ![](creating-a-build-definition-that-supports-deployment/_static/image2.png)
2. Sur le **général** onglet, donnez un nom à la définition de build (par exemple, **DeployToTest**) et une description facultative.
3. Sur le **déclencheur** , sélectionnez les critères sur lesquels vous souhaitez déclencher une nouvelle build. Par exemple, si vous souhaitez générer la solution et le déployer dans l’environnement de test chaque fois qu’un développeur vérifie dans le nouveau code, sélectionnez **intégration continue**.
4. Sur le **Build par défaut est** sous l’onglet du **copie sortie de build dans le dossier de dépôt suivant** , tapez le chemin d’accès UNC Universal Naming Convention () de votre dossier de dépôt (par exemple,  **\\TFSBUILD\Drops**).

    ![](creating-a-build-definition-that-supports-deployment/_static/image3.png)

    > [!NOTE]
    > Cet emplacement de dépôt stocke plusieurs builds, en fonction de la stratégie de rétention que vous configurez. Lorsque vous souhaitez publier les artefacts de déploiement à partir d’une build spécifique à un environnement intermédiaire ou de production, il s’agit où vous les trouverez.
5. Sur le **processus** sous l’onglet du **fichier de processus de génération** liste déroulante, laissez le champ **DefaultTemplate.xaml** sélectionné. Il s’agit d’un des modèles de processus de build par défaut qui sont ajoutées à tous les nouveaux projets d’équipe.
6. Dans le **paramètres du processus de génération** , cliquez sur dans le **éléments à générer** de ligne, puis cliquez sur le **points de suspension** bouton.

    ![](creating-a-build-definition-that-supports-deployment/_static/image4.png)
7. Dans le **éléments à générer** boîte de dialogue, cliquez sur **ajouter**.
8. Accédez à l’emplacement de votre fichier de solution, puis cliquez sur **OK**.

    ![](creating-a-build-definition-that-supports-deployment/_static/image5.png)
9. Dans le **éléments à générer** boîte de dialogue, cliquez sur **ajouter**.
10. Dans le **des éléments de type** liste déroulante, sélectionnez **les fichiers projet MSBuild**.
11. Accédez à l’emplacement du fichier de projet personnalisé avec lequel vous contrôler le processus de déploiement, sélectionnez le fichier, puis cliquez sur **OK**.

    ![](creating-a-build-definition-that-supports-deployment/_static/image6.png)
12. Le **éléments à générer** boîte de dialogue doit désormais afficher les deux éléments. Cliquez sur **OK**.

    ![](creating-a-build-definition-that-supports-deployment/_static/image7.png)
13. Sur le **processus** sous l’onglet du **paramètres du processus de génération** table, développez la **avancé** section.
14. Dans le **Arguments MSBuild** de ligne, ajoutez les arguments de ligne de commande MSBuild qui *soit* de vos éléments de build requiert. Dans le scénario de solution de gestionnaire de contacts, ces arguments sont requis :

    [!code-console[Main](creating-a-build-definition-that-supports-deployment/samples/sample1.cmd)]

    ![](creating-a-build-definition-that-supports-deployment/_static/image8.png)
15. Dans cet exemple :

    1. Le **DeployOnBuild = true** et **DeployTarget = package** arguments sont requis lorsque vous générez la solution de gestionnaire de contacts. Cela indique à MSBuild pour créer web des packages de déploiement après la génération de chaque projet d’application web, comme décrit dans [génération et des projets d’Application Web empaquetage](../web-deployment-in-the-enterprise/building-and-packaging-web-application-projects.md).
    2. Le **TargetEnvPropsFile** argument est requis lorsque vous générez le *Publish.proj* fichier. Cette propriété indique l’emplacement du fichier de configuration de l’environnement spécifique, comme décrit dans [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md).
16. Sur le **stratégie de rétention** onglet, configurer le nombre de builds de chaque type à conserver en fonction des besoins.
17. Cliquez sur **Enregistrer**.

## <a name="queue-a-build"></a>Mettre une build en file d'attente

À ce stade, vous avez créé au moins une définition de build. Le processus de génération que vous avez défini s’exécutera selon les déclencheurs que vous avez spécifié dans la définition de build.

Si vous avez configuré votre définition de build pour utiliser l’élément de configuration, vous pouvez tester votre définition de build de deux manières :

- Vérifiez dans la partie du contenu au projet d’équipe pour déclencher une build automatique.
- File d’attente une build manuellement.

**File d’attente une build manuellement**

1. Dans le **Team Explorer** fenêtre, avec le bouton droit de la définition de build, puis cliquez sur **file d’attente une nouvelle Build**.

    ![](creating-a-build-definition-that-supports-deployment/_static/image9.png)
2. Dans le **file d’attente de** boîte de dialogue, passez en revue les propriétés de build, puis cliquez sur **file d’attente**.

    ![](creating-a-build-definition-that-supports-deployment/_static/image10.png)

Pour passer en revue la progression et le résultat d’une build & le #x 2014 ; indépendamment de si elle a été déclenchée manuellement ou automatiquement & #x 2014 ; double-cliquez sur la définition de build dans le **Team Explorer** fenêtre. Cela ouvre une **Explorateur de builds** onglet.

![](creating-a-build-definition-that-supports-deployment/_static/image11.png)

À ce stade, vous pouvez résoudre l’échec des builds. Si vous double-cliquez sur une build individuelle, vous pouvez afficher des informations de résumé et cliquez pour les fichiers journaux détaillés.

![](creating-a-build-definition-that-supports-deployment/_static/image12.png)

Vous pouvez utiliser ces informations pour résoudre les problèmes d’échec des builds et résoudre les problèmes avant d’essayer une autre build.

> [!NOTE]
> Les builds qui s’exécutent la logique de déploiement sont susceptibles d’échouer jusqu'à ce que vous disposez des autorisations du serveur de builds tout requis dans l’environnement de destination. Pour plus d’informations, consultez [configuration des autorisations pour le déploiement de Team Build](configuring-permissions-for-team-build-deployment.md).


## <a name="monitor-the-build-process"></a>Surveiller le processus de génération

TFS fournit un large éventail de fonctionnalités pour vous aider à surveiller le processus de génération. Par exemple, TFS peut vous envoyer un courrier électronique ou afficher les alertes dans la zone de notification de la barre des tâches lorsqu’une build est terminée. Pour plus d’informations, consultez [exécuter et surveiller les Builds](https://msdn.microsoft.com/en-us/library/ms181721.aspx).

## <a name="conclusion"></a>Conclusion

Cette rubrique décrit comment créer une définition de build dans TFS. La définition de build est configurée pour l’élément de configuration, pour que le processus de génération s’exécute chaque fois qu’un développeur vérifie dans le contenu vers le projet d’équipe. La définition de build exécute un fichier de projet MSBuild personnalisé pour déployer des packages web et des scripts de base de données à un environnement de serveur cible.

Dans l’ordre pour un déploiement automatisé réussir dans le cadre du processus de génération, vous devez accorder des autorisations appropriées pour le compte de service de build sur les serveurs web cible et le serveur de base de données cible. La dernière rubrique de ce didacticiel, [configuration des autorisations pour le déploiement de Team Build](configuring-permissions-for-team-build-deployment.md), explique comment identifier et de configurer les autorisations requises pour le déploiement automatisé à partir d’un serveur Team Build.

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur la création de définitions de build, consultez [créer une définition de Build](https://msdn.microsoft.com/en-us/library/ms181716.aspx) et [définir votre processus de génération](https://msdn.microsoft.com/en-us/library/ms181715.aspx). Pour plus d’informations sur les files d’attente de builds, consultez [une Build en file d’attente](https://msdn.microsoft.com/en-us/library/ms181722.aspx).

>[!div class="step-by-step"]
[Précédent](configuring-a-tfs-build-server-for-web-deployment.md)
[Suivant](deploying-a-specific-build.md)

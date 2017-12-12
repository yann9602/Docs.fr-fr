---
uid: web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/deploying-a-specific-build
title: "Déploiement d’une Build spécifique | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit comment déployer des packages web et les scripts de base de données à partir d’une build précédente spécifique vers une nouvelle destination, comme un environnement intermédiaire ou de production..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: c979535f-48a3-4ec4-a633-a77889b86ddb
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/deploying-a-specific-build
msc.type: authoredcontent
ms.openlocfilehash: afac083c96c1396ad60275fcb55a0ec9c4c0bd44
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-a-specific-build"></a>Déploiement d’une Build spécifique
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment déployer des packages web et des scripts de base de données à partir d’une build précédente spécifique vers une nouvelle destination, comme un environnement intermédiaire ou de production.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md), dans lequel le processus de génération et de déploiement est contrôlé par deux fichiers projet & #x 2014 ; o ne contenant les instructions de génération qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="task-overview"></a>Vue d’ensemble de la tâche

Jusqu'à présent, les rubriques de ce jeu de didacticiel explique comment générer, empaqueter et déployer des applications web et les bases de données dans le cadre d’une seule étape ou processus automatisé. Toutefois, dans certains scénarios, vous souhaiterez sélectionner les ressources que vous déployez à partir d’une liste des builds dans un dossier de dépôt. En d’autres termes, la build la plus récente ne peut pas être la build que vous souhaitez déployer.

Considérez le scénario d’intégration continue (CI) décrit dans la rubrique précédente, [création d’une Build définition que prend en charge déploiement](creating-a-build-definition-that-supports-deployment.md). Vous avez créé une définition de build dans Team Foundation Server (TFS) 2010. Chaque fois qu’un développeur archive le code dans TFS, Team Build sera générer votre code, créer des packages web et les scripts de base de données dans le cadre du processus de génération, exécuter des tests unitaires et déployer vos ressources dans un environnement de test. En fonction de la stratégie de rétention que vous avez configuré lorsque vous avez créé la définition de build, TFS conserve un certain nombre de générations précédentes.

![](deploying-a-specific-build/_static/image1.png)

Maintenant, supposons que vous avez effectué la vérification et validation de test par rapport à un de ces builds dans votre environnement de test, et vous êtes prêt à déployer votre application dans un environnement intermédiaire. En attendant, les développeurs ont peut-être archivé dans le nouveau code. Vous ne souhaitez pas régénérer la solution et le déployer dans l’environnement intermédiaire, et vous ne voulez pas déployer la build la plus récente dans l’environnement intermédiaire. Au lieu de cela, vous souhaitez déployer cette build spécifique que vous avez vérifié et validé sur les serveurs de test.

Pour ce faire, vous devez indiquer Microsoft Build Engine (MSBuild) où se trouvent les packages web et les scripts de base de données générés par une build spécifique.

## <a name="overriding-the-outputroot-property"></a>Substitution de la propriété OutputRoot

Dans le [exemple de solution](../web-deployment-in-the-enterprise/the-contact-manager-solution.md), le *Publish.proj* fichier déclare une propriété nommée **OutputRoot**. Comme son nom l’indique, il s’agit du dossier racine qui contient tout ce qui génère des processus de génération. Dans le *Publish.proj* de fichiers, vous pouvez voir que la **OutputRoot** propriété fait référence à l’emplacement racine pour toutes les ressources de déploiement.

> [!NOTE]
> **OutputRoot** est un nom de propriété couramment utilisés. Les fichiers de projet Visual c# et Visual Basic également déclarent cette propriété pour stocker l’emplacement racine de toutes les sorties de génération.


[!code-xml[Main](deploying-a-specific-build/samples/sample1.xml)]


Si vous souhaitez que votre fichier projet pour déployer des packages web et des scripts de base de données à partir d’un autre emplacement & le #x 2014 ; par exemple, les sorties d’une build TFS antérieures de & #x 2014 ; vous devez simplement remplacer le **OutputRoot** propriété. Vous devez définir la valeur de propriété dans le dossier génération pertinentes sur le serveur Team Build. Si vous utilisiez MSBuild à partir de la ligne de commande, vous pouvez spécifier une valeur pour **OutputRoot** comme un argument de ligne de commande :


[!code-console[Main](deploying-a-specific-build/samples/sample2.cmd)]


Dans la pratique, cependant, vous serez également ignorer le **générer** cible & #x 2014 ; il est inutile de la création de votre solution si vous ne prévoyez pas d’utiliser les sorties de génération. Vous pouvez effectuer ceci en spécifiant les cibles que vous souhaitez exécuter à partir de la ligne de commande :


[!code-console[Main](deploying-a-specific-build/samples/sample3.cmd)]


Toutefois, dans la plupart des cas, que vous souhaitez intégrer votre logique de déploiement d’une définition de build TFS. Cela permet aux utilisateurs avec le **file d’attente de builds** autorisation pour déclencher le déploiement à partir de toute installation de Visual Studio avec une connexion au serveur TFS.

## <a name="creating-a-build-definition-to-deploy-specific-builds"></a>Création d’une définition de Build pour déployer des Builds

La procédure suivante décrit comment créer une définition de build qui permet aux utilisateurs de déploiements de déclencheur dans un environnement intermédiaire avec une seule commande.

Dans ce cas, vous ne souhaitez pas générer réellement quoi que ce soit, la définition de build & #x 2014 ; vous souhaitiez qu’il s’exécute la logique de déploiement dans votre fichier projet personnalisé. Le *Publish.proj* fichier inclut une logique conditionnelle qui ignore le **générer** cible si le fichier est en cours d’exécution dans Team Build. Pour ce faire, il l’évaluation de la fonction intégrée **BuildingInTeamBuild** propriété, qui est automatiquement définie sur **true** si vous exécutez votre fichier projet dans Team Build. Par conséquent, vous pouvez ignorer le processus de génération et il suffit d’exécuter le fichier projet pour déployer une build existante.

**Pour créer une définition de build pour déclencher le déploiement manuellement**

1. Dans Visual Studio 2010, dans le **Team Explorer** fenêtre, développez le nœud de votre projet d’équipe, cliquez sur **génère**, puis cliquez sur **nouvelle définition de Build**.

    ![](deploying-a-specific-build/_static/image2.png)
2. Sur le **général** onglet, donnez un nom à la définition de build (par exemple, **DeployToStaging**) et une description facultative.
3. Sur le **déclencheur** onglet, sélectionnez **manuel – les archivages ne déclenchent pas d’une nouvelle build**.
4. Sur le **Build par défaut est** sous l’onglet du **copie sortie de build dans le dossier de dépôt suivant** , tapez le chemin d’accès UNC Universal Naming Convention () de votre dossier de dépôt (par exemple,  **\\TFSBUILD\Drops**).

    ![](deploying-a-specific-build/_static/image3.png)
5. Sur le **processus** sous l’onglet du **fichier de processus de génération** liste déroulante, laissez le champ **DefaultTemplate.xaml** sélectionné. Il s’agit d’un des modèles de processus de build par défaut qui sont ajoutées à tous les nouveaux projets d’équipe.
6. Dans le **paramètres du processus de génération** , cliquez sur dans le **éléments à générer** de ligne, puis cliquez sur le **points de suspension** bouton.

    ![](deploying-a-specific-build/_static/image4.png)
7. Dans le **éléments à générer** boîte de dialogue, cliquez sur **ajouter**.
8. Dans le **des éléments de type** liste déroulante, sélectionnez **les fichiers projet MSBuild**.
9. Accédez à l’emplacement du fichier de projet personnalisé avec lequel vous contrôler le processus de déploiement, sélectionnez le fichier, puis cliquez sur **OK**.

    ![](deploying-a-specific-build/_static/image5.png)
10. Dans le **éléments à générer** boîte de dialogue, cliquez sur **OK**.
11. Dans le **paramètres du processus de génération** table, développez la **avancé** section.
12. Dans le **Arguments MSBuild** de ligne, spécifiez l’emplacement de votre fichier de projet spécifique à l’environnement et ajouter un espace réservé pour l’emplacement de votre dossier de build :

    [!code-console[Main](deploying-a-specific-build/samples/sample4.cmd)]

    ![](deploying-a-specific-build/_static/image6.png)

    > [!NOTE]
    > Vous aurez besoin de remplacer le **OutputRoot** valeur chaque fois que vous la file d’attente une build. Ce sujet est abordé dans la procédure suivante.
13. Cliquez sur **Enregistrer**.

Lorsque vous déclenchez une build, vous devez mettre à jour le **OutputRoot** propriété pour pointer vers la build que vous souhaitez déployer.

**Pour déployer une build spécifique à partir d’une définition de build**

1. Dans le **Team Explorer** fenêtre, avec le bouton droit de la définition de build, puis cliquez sur **file d’attente une nouvelle Build**.

    ![](deploying-a-specific-build/_static/image7.png)
2. Dans le **file d’attente de** boîte de dialogue le **paramètres** onglet, développez le **avancé** section.
3. Dans le **Arguments MSBuild** de ligne, remplacez la valeur de la **OutputRoot** propriété avec l’emplacement de votre dossier de build. Exemple :

    [!code-console[Main](deploying-a-specific-build/samples/sample5.cmd)]

    ![](deploying-a-specific-build/_static/image8.png)

    > [!NOTE]
    > Veillez à inclure une barre oblique à la fin du chemin d’accès à votre dossier de build.
4. Cliquez sur **file d’attente**.

Lorsque vous la build en file d’attente, le fichier projet déploierez les scripts de base de données et web packages à partir du dossier de dépôt de build que vous avez spécifié dans le **OutputRoot** propriété.

## <a name="conclusion"></a>Conclusion

Cette rubrique décrit comment publier des ressources de déploiement, telles que les packages web et de scripts de base de données, à partir d’une précédente spécifique à générer à l’aide du modèle de déploiement du fichier de projet fractionnement. Il explique comment remplacer la **OutputRoot** définition de build de propriété et comment intégrer la logique de déploiement d’un serveur TFS.

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur la création de définitions de build, consultez [créer une définition de Build](https://msdn.microsoft.com/en-us/library/ms181716.aspx) et [définir votre processus de génération](https://msdn.microsoft.com/en-us/library/ms181715.aspx). Pour plus d’informations sur les files d’attente de builds, consultez [une Build en file d’attente](https://msdn.microsoft.com/en-us/library/ms181722.aspx).

>[!div class="step-by-step"]
[Précédent](creating-a-build-definition-that-supports-deployment.md)
[Suivant](configuring-permissions-for-team-build-deployment.md)

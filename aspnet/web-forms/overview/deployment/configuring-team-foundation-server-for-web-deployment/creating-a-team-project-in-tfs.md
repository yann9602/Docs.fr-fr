---
uid: web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/creating-a-team-project-in-tfs
title: "Création d’un projet d’équipe dans TFS | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit comment créer un nouveau projet d’équipe dans Team Foundation Server (TFS) 2010."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: b28d3e2d-0bb4-4e29-a780-af810b964722
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/creating-a-team-project-in-tfs
msc.type: authoredcontent
ms.openlocfilehash: 4cb0d72330086ecb8cd9e6fb70ce0a57698dda5a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-team-project-in-tfs"></a>Création d’un projet d’équipe dans TFS
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment créer un nouveau projet d’équipe dans Team Foundation Server (TFS) 2010.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

## <a name="task-overview"></a>Vue d’ensemble de la tâche

Pour configurer et utiliser un nouveau projet d’équipe dans TFS, vous devez effectuer les étapes générales suivantes :

- Accorder des autorisations à l’utilisateur qui crée le projet d’équipe.
- Créer le projet d’équipe.
- Permet d’accorder des autorisations aux membres de l’équipe qui travaillent sur le projet.
- Vérifiez dans la partie du contenu.

Cette rubrique va vous montrer comment effectuer ces procédures, et il identifie les utilisateurs et rôles de travail qui sont susceptibles d’être responsable de chaque procédure. N’oubliez pas que, selon la structure de votre organisation, chacune de ces tâches peut être la responsabilité d’une autre personne.

Les tâches et les procédures pas à pas dans cette rubrique supposent que vous avez installé et configuré TFS et que vous avez créé une collection de projets d’équipe dans le cadre du processus de configuration. Pour plus d’informations sur les hypothèses et pour des informations plus générales sur le scénario, consultez [configurer un serveur de Build TFS pour le déploiement Web](configuring-a-tfs-build-server-for-web-deployment.md).

## <a name="grant-permissions-to-the-team-project-creator"></a>Accorder des autorisations pour le créateur de projet d’équipe

Pour créer un nouveau projet d’équipe, vous avez besoin de ces autorisations :

- Vous devez avoir le **créer de nouveaux projets** autorisation sur la couche application TFS. En règle générale, vous accordez cette autorisation en ajoutant des utilisateurs à la **Project Collection Administrators** groupe TFS. Le **Team Foundation Administrators** groupe global comprend également cette autorisation.
- Vous devez être autorisé à créer des sites d’équipe au sein de la collection de sites SharePoint qui correspond à la collection de projets d’équipe TFS. En règle générale, vous accordez cette autorisation par l’ajout de l’utilisateur à un groupe SharePoint avec **contrôle total** collection de sites de droits sur SharePoint.
- Si vous utilisez des fonctionnalités de SQL Server Reporting Services, vous devez être un membre de la **Gestionnaire de contenu Team Foundation** rôle dans Reporting Services.

### <a name="who-performs-these-procedures"></a>Qui exécute ces procédures ?

En règle générale, la personne ou le groupe qui administre le déploiement TFS effectue également ces procédures.

Comme il s’agit d’un jeu de privilèges élevés d’autorisations, les nouveaux projets d’équipe sont généralement créés par un petit sous-ensemble d’utilisateurs avec la responsabilité de l’administration d’un déploiement de TFS. Les développeurs ne pas généralement accordées les autorisations requises pour créer des projets d’équipe.

### <a name="grant-permissions-in-tfs"></a>Accorder des autorisations dans TFS

Si vous souhaitez permettre aux utilisateurs de créer des projets d’équipe, la première tâche de haut niveau consiste à ajouter l’utilisateur à la **Project Collection Administrators** groupe pour la collection de projets d’équipe.

**Pour ajouter un utilisateur au groupe Project Collection Administrators**

1. Sur le serveur TFS, sur le **Démarrer** menu, pointez sur **tous les programmes**, cliquez sur **Microsoft Team Foundation Server 2010**, puis cliquez sur **Team Foundation Console d’administration**.
2. Dans l’arborescence de navigation, développez **couche Application**, puis cliquez sur **Collections de projets d’équipe**.

    ![](creating-a-team-project-in-tfs/_static/image1.png)
3. Dans le **Collections de projets d’équipe** volet, sélectionnez le projet d’équipe collection que vous souhaitez gérer.

    ![](creating-a-team-project-in-tfs/_static/image2.png)
4. Sur le **général** , cliquez sur **l’appartenance au groupe**.

    ![](creating-a-team-project-in-tfs/_static/image3.png)
5. Dans le **groupes globaux** boîte de dialogue, sélectionnez le **Project Collection Administrators** de groupe, puis cliquez sur **propriétés**.
6. Dans le **propriétés du groupe Team Foundation Server** boîte de dialogue, sélectionnez **utilisateur ou groupe Windows**, puis cliquez sur **ajouter**.

    ![](creating-a-team-project-in-tfs/_static/image4.png)
7. Dans le **sélectionnez utilisateurs, ordinateurs ou groupes** boîte de dialogue, tapez le nom d’utilisateur de l’utilisateur que vous souhaitez être en mesure de créer de nouveaux projets d’équipe, cliquez sur **vérifier les noms**, puis cliquez sur **OK** .

    ![](creating-a-team-project-in-tfs/_static/image5.png)
8. Dans le **propriétés du groupe Team Foundation Server** boîte de dialogue, cliquez sur **OK**.
9. Dans le **groupes globaux** boîte de dialogue, cliquez sur **fermer**.

### <a name="grant-permissions-in-sharepoint-services"></a>Accorder des autorisations dans SharePoint Services

Ensuite, vous devez donner à l’utilisateur l’autorisation de créer de nouveaux sites d’équipe dans la collection de sites SharePoint qui correspond à votre collection de projets d’équipe TFS.

**Pour accorder des autorisations Contrôle total sur la collection de sites SharePoint**

1. Dans la Console Administration Team Foundation Server, sur le **Collections de projets d’équipe** , sélectionnez la collection de projets d’équipe à gérer.
2. Sur le **SharePoint Site** onglet, notez la valeur de la **emplacement du Site par défaut actuel** URL.

    ![](creating-a-team-project-in-tfs/_static/image6.png)
3. Ouvrez Internet Explorer, puis accédez à l’URL que vous avez noté à l’étape 2.

    > [!NOTE]
    > Si vous n’êtes pas connecté à Windows en tant que l’utilisateur qui a créé la collection de projets d’équipe, vous devez vous connecter à SharePoint en tant que cet utilisateur pour pouvoir continuer.
4. Sur le **Actions du Site** menu, cliquez sur **paramètres du Site**.

    ![](creating-a-team-project-in-tfs/_static/image7.png)
5. Sur le **paramètres du Site** sous **les utilisateurs et les autorisations**, cliquez sur **personnes et groupes**.
6. Dans le volet de navigation gauche, cliquez sur **groupes**.

    ![](creating-a-team-project-in-tfs/_static/image8.png)
7. Sur le **personnes et groupes : tous les groupes de** , cliquez sur **configurer les groupes pour ce Site**.

    ![](creating-a-team-project-in-tfs/_static/image9.png)

    > [!NOTE]
    > Vous pouvez recevoir un **HTTP 404 introuvable** erreur en raison d’un bogue de codage HTTP double. Si cela se produit, remplacez l’URL par ceci :   
    > [*URL de collection de site*] /\_layouts/permsetup.aspx  
    > Exemple :  
    > http://TFS/sites/Fabrikam%20Web%20Projects/\_layouts/permsetup.aspx
8. Sur le **configurer les groupes pour ce Site** page, ajoutez l’utilisateur qui doivent créer des projets d’équipe pour le **propriétaires** de groupe, puis cliquez sur **OK**.

    ![](creating-a-team-project-in-tfs/_static/image10.png)

Pour plus d’informations sur l’activation aux utilisateurs de créer de nouveaux projets d’équipe au sein d’une collection de projets d’équipe, consultez [définition d’autorisations d’administrateur pour les Collections de projets d’équipe](https://msdn.microsoft.com/en-us/library/dd547204.aspx).

## <a name="create-a-new-team-project-and-add-users"></a>Créer un nouveau projet d’équipe et ajouter des utilisateurs

Une fois que vous avez les autorisations nécessaires, vous pouvez utiliser la **Team Explorer** fenêtre dans Visual Studio 2010 pour créer un nouveau projet d’équipe. Cette approche fournit un Assistant qui collecte les informations nécessaires et effectue les tâches nécessaires dans TFS, SharePoint et SQL Server Reporting Services. Vous devez également accorder des autorisations sur le nouveau projet d’équipe aux membres de l’équipe de développement, pour pouvoir ajouter et modifier le contenu.

### <a name="who-performs-these-procedures"></a>Qui exécute ces procédures ?

En règle générale un administrateur TFS ou un chef d’équipe développeur effectue ces procédures.

### <a name="create-a-new-team-project"></a>Créer un nouveau projet d’équipe

La procédure suivante décrit comment créer un nouveau projet d’équipe dans TFS 2010.

**Pour créer un nouveau projet d’équipe**

1. Sur le **Démarrer** menu, pointez sur **tous les programmes**, cliquez sur **Microsoft Visual Studio 2010**, avec le bouton droit **Microsoft Visual Studio 2010**, puis cliquez sur **exécuter en tant qu’administrateur**.

    > [!NOTE]
    > Si vous n’exécutez pas Visual Studio 2010 en tant qu’administrateur, l’Assistant Nouveau projet d’équipe échouera sur la dernière étape.
2. Si le **contrôle de compte d’utilisateur** boîte de dialogue s’affiche, cliquez sur **Oui**.
3. Dans Visual Studio, sur le **équipe** menu, cliquez sur **se connecter à Team Foundation Server**.

    > [!NOTE]
    > Si vous avez déjà configuré une connexion à un serveur TFS, vous pouvez omettre les étapes 4 à 7.
4. Dans le **connexion au projet d’équipe** boîte de dialogue, cliquez sur **serveurs**.
5. Dans le **ajouter/supprimer Team Foundation Server** boîte de dialogue, cliquez sur **ajouter**.
6. Dans le **Ajouter Team Foundation Server** boîte de dialogue, fournissez les détails de votre instance TFS, puis cliquez sur **OK**.

    ![](creating-a-team-project-in-tfs/_static/image11.png)
7. Dans le **ajouter/supprimer Team Foundation Server** boîte de dialogue, cliquez sur **fermer**.
8. Dans le **se connecter au projet d’équipe** boîte de dialogue, sélectionnez l’instance TFS que vous souhaitez vous connecter, sélectionnez l’équipe de projet collection que vous souhaitez ajouter à, puis cliquez sur **connexion**.

    ![](creating-a-team-project-in-tfs/_static/image12.png)
9. Dans le **Team Explorer** (fenêtre), avec le bouton de l’équipe de la collection de projets, puis cliquez sur **nouveau projet d’équipe**.

    ![](creating-a-team-project-in-tfs/_static/image13.png)
10. Dans le **nouveau projet d’équipe** boîte de dialogue, indiquez un nom et une description pour le projet d’équipe, puis cliquez sur **suivant**.

    > [!NOTE]
    > Si votre projet d’équipe comprend des espaces, vous pouvez rencontrer des problèmes lorsque vous accédez à l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy) permet de déployer des packages à partir du chemin de sortie. Les espaces dans le chemin d’accès peuvent rendre beaucoup plus difficile exécuter des commandes de déploiement Web.

    ![](creating-a-team-project-in-tfs/_static/image14.png)
11. Sur le **sélectionner un modèle de processus** , sélectionnez le modèle de processus que vous souhaitez utiliser pour gérer le processus de développement, puis cliquez sur **suivant**.

    > [!NOTE]
    > Pour plus d’informations sur les modèles de processus pour TFS, consultez [modèles de processus et outils](https://msdn.microsoft.com/en-us/vstudio/aa718795).
12. Sur le **paramètres du Site d’équipe** page, laissez les paramètres par défaut inchangées, puis cliquez sur **suivant**.
13. Ce paramètre crée ou identifie, un site d’équipe SharePoint associé au projet d’équipe TFS. Votre équipe de développement peut utiliser ce site pour gérer la documentation, participer aux discussions, créer des pages wiki et effectuer diverses autres tâches qui ne sont pas liés au code. Pour plus d’informations, consultez [Interactions entre les produits SharePoint et Team Foundation Server](https://msdn.microsoft.com/en-us/library/ms253177.aspx).
14. Sur le **spécifier les paramètres de contrôle Source** page, laissez les paramètres par défaut inchangées, puis cliquez sur **suivant**.
15. Ce paramètre identifie ou crée l’emplacement dans la hiérarchie de dossiers TFS qui agira en tant qu’un dossier racine pour votre contenu.
16. Sur le **confirmer les paramètres du projet d’équipe** , cliquez sur **Terminer**.
17. Lorsque le projet d’équipe est correctement créé, dans le **projet d’équipe créé** , cliquez sur **fermer**.

### <a name="add-users-to-a-team-project"></a>Ajouter des utilisateurs à un projet d’équipe

Maintenant que vous avez créé le projet d’équipe, vous pouvez accorder des autorisations aux utilisateurs pour leur permettre de démarrer l’ajout et la collaboration sur le contenu.

**Pour ajouter des utilisateurs à un projet d’équipe**

1. Dans Visual Studio 2010, dans le **Team Explorer** fenêtre, cliquez sur le projet d’équipe, pointez sur **paramètres du projet d’équipe**, puis cliquez sur **l’appartenance au groupe**.

    ![](creating-a-team-project-in-tfs/_static/image15.png)
2. Pour permettre à un utilisateur à ajouter, modifier et supprimer le code sous contrôle de code source, vous devez l’ajouter à la **collaborateurs** groupe.
3. Dans le **groupes de projets** boîte de dialogue, sélectionnez le **collaborateurs** de groupe, puis cliquez sur **propriétés**.

    ![](creating-a-team-project-in-tfs/_static/image16.png)
4. Dans le **propriétés du groupe Team Foundation Server** boîte de dialogue, sélectionnez **utilisateur ou groupe Windows**, puis cliquez sur **ajouter**.

    ![](creating-a-team-project-in-tfs/_static/image17.png)
5. Dans le **sélectionnez utilisateurs, ordinateurs ou groupes** boîte de dialogue, tapez le nom d’utilisateur de l’utilisateur que vous souhaitez ajouter au projet d’équipe, cliquez sur **vérifier les noms**, puis cliquez sur **OK**.

    ![](creating-a-team-project-in-tfs/_static/image18.png)
6. Dans le **propriétés du groupe Team Foundation Server** boîte de dialogue, cliquez sur **OK**.
7. Dans le **groupes de projets** boîte de dialogue, cliquez sur **fermer**.

## <a name="conclusion"></a>Conclusion

À ce stade, votre projet d’équipe est prête à utiliser, et votre équipe de développement peut commencer à ajouter du contenu et la collaboration sur le processus de développement.

La rubrique suivante, [Ajout de contenu pour le contrôle de code Source](adding-content-to-source-control.md), explique comment ajouter du contenu à un contrôle de code source.

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur la création de projets d’équipe dans TFS, consultez [créer un projet d’équipe](https://msdn.microsoft.com/en-us/library/ms181477(v=VS.100).aspx). Pour plus d’informations sur l’activation aux utilisateurs de créer de nouveaux projets d’équipe au sein d’une collection de projets d’équipe, consultez [définition d’autorisations d’administrateur pour les Collections de projets d’équipe](https://msdn.microsoft.com/en-us/library/dd547204.aspx). Pour plus d’informations sur l’ajout d’utilisateurs aux projets d’équipe, consultez [ajouter des utilisateurs aux projets d’équipe](https://msdn.microsoft.com/en-us/library/bb558971.aspx).

>[!div class="step-by-step"]
[Précédent](configuring-team-foundation-server-for-web-deployment.md)
[Suivant](adding-content-to-source-control.md)

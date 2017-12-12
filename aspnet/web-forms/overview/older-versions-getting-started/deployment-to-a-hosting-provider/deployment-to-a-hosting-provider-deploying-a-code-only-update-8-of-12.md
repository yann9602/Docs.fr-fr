---
uid: web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12
title: "Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : déploiement d’une mise à jour Code-Only - 8 de 12 | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Stu..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/17/2011
ms.topic: article
ms.assetid: ddf6252f-9413-4c0c-a360-2cef8d231717
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12
msc.type: authoredcontent
ms.openlocfilehash: a07ac968482866ba9a4eca25722d99fd641080e6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-an-aspnet-web-application-with-sql-server-compact-using-visual-studio-or-visual-web-developer-deploying-a-code-only-update---8-of-12"></a>Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : déploiement d’une mise à jour Code-Only - 8 de 12
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://code.msdn.microsoft.com/Deploying-an-ASPNET-Web-4e31366b)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Studio 2012 RC ou Visual Studio Express 2012 RC pour le Web. Vous pouvez également utiliser Visual Studio 2010 si vous installez la mise à jour de publication Web. Pour une présentation de la série, consultez [le premier didacticiel de la série](deployment-to-a-hosting-provider-introduction-1-of-12.md).
> 
> Pour obtenir un didacticiel qui montre les fonctionnalités de déploiement introduites après la version RC de Visual Studio 2012, montre comment déployer des éditions de SQL Server autre que SQL Server Compact et montre comment déployer dans Azure App Service Web Apps, consultez [déploiement de Web ASP.NET à l’aide de Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md).


## <a name="overview"></a>Vue d'ensemble

Après le déploiement initial, votre travail de maintenance et de développement de votre site web se poursuit et avant de longue durée, vous souhaiterez déployer une mise à jour. Ce didacticiel vous guide dans le processus de déploiement d’une mise à jour à votre code d’application. Cette mise à jour n’implique pas une modification de la base de données ; Vous verrez que ce qui est différent sur le déploiement d’une modification de la base de données dans le didacticiel suivant.

Rappel : Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez le didacticiel, veillez à consulter le [page Résolution des problèmes](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12.md).

## <a name="making-a-code-change"></a>Vous apportez une modification de Code

Un exemple simple d’une mise à jour à votre application, vous allez ajouter à la **instructeurs** page d’une liste de dispensés par le formateur sélectionné.

Si vous exécutez le **instructeurs** page, vous remarquerez qu’il n’y **sélectionnez** des liens dans la grille, mais qu’ils n’autre chose qu’une marque le gris de tour de ligne en arrière-plan.

[![Instructors_page](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image2.png)](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image1.png)

Maintenant vous allez ajouter du code qui s’exécute lorsque le **sélectionnez** lien est sélectionnée et affiche une liste de dispensés par le formateur sélectionné.

Dans *Instructors.aspx*, ajoutez le balisage suivant immédiatement après le **ErrorMessageLabel** `Label` contrôle :

[!code-aspx[Main](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/samples/sample1.aspx)]

Exécutez la page et sélectionner un formateur. Vous consultez une liste de dispensés par ce formateur.

[![Instructors_page_with_courses](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image4.png)](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image3.png)

## <a name="deploying-the-code-update-to-the-test-environment"></a>Déploiement de la mise à jour du Code dans l’environnement de Test

Déploiement sur l’environnement de test est très simple de l’exécution d’un seul clic publier à nouveau. Pour accélérer ce processus, vous pouvez utiliser la **publication Web en un clic** barre d’outils.

Dans le **vue** menu, choisissez **barres d’outils** , puis sélectionnez **publication Web en un clic**.

![Selecting_One_Click_Publish_toolbar](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image5.png)

Dans **l’Explorateur de solutions**, sélectionnez le projet ContosoUniversity.

le **publication Web en un clic** barre d’outils, choisissez le **Test** le profil de publication, puis cliquez sur **publier le site Web** (l’icône avec flèches pointant vers la gauche et droite).

![Web_One_Click_Publish_toolbar](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image6.png)

Visual Studio déploie l’application mis à jour, et le navigateur s’ouvre automatiquement à la page d’accueil. Exécutez la page de formateurs et sélectionnez un formateur pour vérifier que la mise à jour a été déployé.

[![Instructors_page_with_courses_Test](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image8.png)](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image7.png)

Vous le feriez normalement également effectuer un test de régression (autrement dit, le reste du site pour vous assurer que la nouvelle modification n’a pas été arrête toutes les fonctionnalités de test). Mais pour ce didacticiel vous ignorez cette étape et passez à la déployer en production.

## <a name="preventing-redeployment-of-the-initial-database-state-to-production"></a>Pour empêcher le redéploiement de l’état de la base de données Initial à la Production

Dans une application réelle, les utilisateurs interagissent avec votre site de production après le déploiement initial, et les bases de données sont remplis avec des données actives. Par conséquent, vous ne souhaitez pas redéployer la base de données d’appartenance dans son état initial, ce qui serait réinitialiser toutes les données actives. Étant donné que les bases de données SQL Server Compact sont des fichiers dans le *application\_données* dossier, vous devez éviter ce problème en modifiant les paramètres de déploiement afin que les fichiers dans le *application\_données* dossier ne sont pas déployés.

Ouvrez le **propriétés du projet** fenêtre pour le projet de ContosoUniversity, puis sélectionnez le **Package/Publication Web** onglet. Assurez-vous que le **Configuration** zone déroulante a **Active (Release)** ou **version** sélectionnée, sélectionnez **exclure des fichiers de l’application\_Dossier de données**.

![Exclude_files_from_the_App_Data_folder](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image9.png)

Si vous décidez de déployer une version debug à l’avenir, il est judicieux d’apporter la même modification pour la configuration de build Debug : modifier **Configuration** à **déboguer** , puis sélectionnez **exclure fichiers à partir de l’application\_dossier de données**.

Enregistrez et fermez le **Package/Publication Web** onglet.

> [!NOTE] 
> 
> [!IMPORTANT]
> Assurez-vous que vous n’avez pas **supprimer les fichiers supplémentaires à la destination** sélectionné dans les profils de publication. Si vous sélectionnez cette option, le processus de déploiement supprime les bases de données que vous avez dans application\_les données dans le site déployé et supprime l’application\_dossier de données lui-même.


## <a name="preventing-user-access-to-the-production-site-during-update"></a>Empêcher l’accès de l’utilisateur pour le Site de Production au cours de la mise à jour

La modification que vous déployez est maintenant une modification simple à une seule page. Mais parfois vous déployez les modifications plus importantes, et dans ce cas le site peut se comportent de façon étrange si un utilisateur demande une page avant le déploiement est terminé. Pour éviter ce problème, vous pouvez utiliser un *application\_offline.htm* fichier. Lorsque vous placez un fichier nommé *application\_offline.htm* dans le dossier racine de votre application, IIS affiche automatiquement ce fichier au lieu d’exécuter votre application. Donc pour empêcher l’accès au cours du déploiement, de placer des *application\_offline.htm* dans le dossier racine, exécutez le processus de déploiement, puis supprimez *application\_offline.htm*.

Dans **l’Explorateur de solutions**, avec le bouton droit de la solution (pas un des projets), puis sélectionnez **nouveau dossier de Solution**.

![Creating_a_solution_folder](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image10.png)

Nommez le dossier *SolutionFiles*.

Dans le nouveau dossier, créez une page HTML nommée *application\_offline.htm*. Remplacez le contenu existant par le balisage suivant :

[!code-html[Main](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/samples/sample2.html)]

Vous pouvez copier le *application\_offline.htm* fichier vers le site à l’aide d’une connexion FTP ou le **le Gestionnaire de fichiers** utilitaire dans le panneau de configuration du fournisseur d’hébergement. Pour ce didacticiel, vous allez utiliser le **le Gestionnaire de fichiers**.

Ouvrez le panneau de configuration, puis sélectionnez **le Gestionnaire de fichiers** comme vous le faisiez le [déploiement dans l’environnement de Production](deployment-to-a-hosting-provider-deploying-to-the-production-environment-7-of-12.md) didacticiel. Sélectionnez **contosouniversity.com** , puis **wwwroot** à obtenir pour le dossier racine de votre application, puis cliquez sur **télécharger**.

[![Upload_button_in_File_Manager](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image12.png)](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image11.png)

Dans le **télécharger le fichier** boîte de dialogue, sélectionnez le *application\_offline.htm* , puis cliquez sur **télécharger**.

[![Upload_dialog_box_in_File_Manager](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image14.png)](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image13.png)

Accédez à l’URL de votre site. Vous constatez que la *application\_offline.htm* page est maintenant affichée au lieu de votre page d’accueil.

[![App_offline.htm_page_in_production](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image16.png)](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image15.png)

Vous êtes maintenant prêt à déployer en production.

## <a name="deploying-the-code-update-to-the-production-environment"></a>Déploiement de la mise à jour du Code dans l’environnement de Production

Dans le **publication Web en un clic** barre d’outils, choisissez le **Production** le profil de publication, puis cliquez sur **publier le site Web**.

Visual Studio déploie l’application de mises à jour et ouvre le navigateur à la page d’accueil du site. Le *application\_offline.htm* fichier s’affiche. Avant de pouvoir tester pour vérifier la réussite du déploiement, vous devez supprimer la *application\_offline.htm* fichier.

Retour à la **le Gestionnaire de fichiers** application dans le panneau de configuration. Sélectionnez **contosouniversity.com** et **wwwroot**, sélectionnez **application\_offline.htm**, puis cliquez sur **supprimer**.

[![Deleting_app_offline.htm](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image18.png)](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image17.png)

Dans le navigateur, ouvrez la page instructeurs sur le site public et sélectionnez un formateur pour vérifier que la mise à jour a été déployé.

[![Instructors_page_with_courses_Prod](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image20.png)](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12/_static/image19.png)

Vous avez maintenant déployé une mise à jour d’application qui n’impliquait pas une modification de la base de données. Le didacticiel suivant vous montre comment déployer une modification de la base de données.

>[!div class="step-by-step"]
[Précédent](deployment-to-a-hosting-provider-deploying-to-the-production-environment-7-of-12.md)
[Suivant](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12.md)

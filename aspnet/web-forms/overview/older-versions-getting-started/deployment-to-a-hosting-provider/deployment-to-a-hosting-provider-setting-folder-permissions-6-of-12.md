---
uid: web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12
title: "Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : définition des autorisations dossier - 6 de 12 | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Stu..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/17/2011
ms.topic: article
ms.assetid: cd03a188-e947-4f55-9bda-b8bce201d8c6
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12
msc.type: authoredcontent
ms.openlocfilehash: 42085fff5f1aed1440f49e1e2ceee0cf0e751e2c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-an-aspnet-web-application-with-sql-server-compact-using-visual-studio-or-visual-web-developer-setting-folder-permissions---6-of-12"></a>Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : définition des autorisations dossier - 6 de 12
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://code.msdn.microsoft.com/Deploying-an-ASPNET-Web-4e31366b)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Studio 2012 RC ou Visual Studio Express 2012 RC pour le Web. Vous pouvez également utiliser Visual Studio 2010 si vous installez la mise à jour de publication Web. Pour une présentation de la série, consultez [le premier didacticiel de la série](deployment-to-a-hosting-provider-introduction-1-of-12.md).
> 
> Pour obtenir un didacticiel qui montre les fonctionnalités de déploiement introduites après la version RC de Visual Studio 2012, montre comment déployer des éditions de SQL Server autre que SQL Server Compact et montre comment déployer dans Azure App Service Web Apps, consultez [déploiement de Web ASP.NET à l’aide de Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md).


## <a name="overview"></a>Vue d'ensemble

Dans ce didacticiel, vous définissez des autorisations de dossier pour le *Elmah* dossier dans le site web déployé afin que l’application peut créer des fichiers journaux dans le dossier de site.

Lorsque vous testez une application web dans Visual Studio à l’aide du serveur Visual Studio Development (Cassini), l’application s’exécute sous votre identité. Vous êtes très probablement d’un administrateur sur votre ordinateur de développement et que vous avez autorité pour faire quelque chose à n’importe quel fichier dans un dossier. Mais quand une application s’exécute sous IIS, il s’exécute sous l’identité définie pour le pool d’applications auquel le site appartient. Il s’agit généralement d’un compte défini par le système qui dispose d’autorisations limitées. Par défaut, il a lu et les autorisations d’exécution sur des fichiers et des dossiers de votre application web, mais il n’a accès en écriture.

Cela devient un problème si votre application crée ou des fichiers de mises à jour, qui est commune dans les applications web. Dans l’application Contoso University, Elmah crée des fichiers XML dans le *Elmah* dossier afin d’enregistrer les détails des erreurs. Même si vous n’utilisez pas quelque chose comme Elmah, votre site peut permettre aux utilisateurs de télécharger des fichiers ou effectuer d’autres tâches qui écrivent des données dans un dossier de votre site.

Rappel : Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez le didacticiel, veillez à consulter le [page Résolution des problèmes](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12.md).

## <a name="testing-error-logging-and-reporting"></a>Journalisation des erreurs et création de rapports de test

Pour voir comment l’application ne fonctionne pas correctement dans IIS (bien qu’il était au moment de vous la testez dans Visual Studio), vous pouvez provoquer une erreur qui serait normalement être considérée par Elmah, puis ouvrez le journal des erreurs Elmah pour afficher les détails. Si Elmah n’a pas pu créer un fichier XML et de stocker les détails de l’erreur, vous consultez un rapport d’erreurs vide.

Ouvrez un navigateur et accédez à `http://localhost/ContosoUniversity`, et ensuite demander une URL non valide comme *Studentsxxx.aspx*. Vous consultez une page d’erreur générée par le système à la place de la *GenericErrorPage.aspx* page, car le `customErrors` paramètre dans le fichier Web.config est « RemoteOnly » et que vous exécutez IIS localement :

[![Error_page_Test](deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12/_static/image2.png)](deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12/_static/image1.png)

Exécutez maintenant *Elmah.axd* pour afficher le rapport d’erreurs. Vous consultez une page du journal des erreurs vide car Elmah n’a pas pu créer un fichier XML dans le *Elmah* dossier :

[![Error_log_page_empty](deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12/_static/image4.png)](deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12/_static/image3.png)

## <a name="setting-write-permission-on-the-elmah-folder"></a>Paramètre autorisation d’écriture sur le dossier Elmah

Vous pouvez définir manuellement les autorisations de dossier, ou vous pouvez rendre automatique dans le cadre du processus de déploiement. Rend automatique nécessite un code complexe MSBuild et étant donné que vous ne devez procéder à la première fois que vous déployez, ce didacticiel montre uniquement comment le faire manuellement. (Pour plus d’informations sur la façon de rendre cette partie du processus de déploiement, consultez [définition des autorisations de dossier de publication Web](http://sedodream.com/2011/11/08/SettingFolderPermissionsOnWebPublish.aspx) sur le blog de Sayed Hashimi.)

Dans **l’Explorateur Windows**, accédez à *C:\inetpub\wwwroot\ContosoUniversity*. Avec le bouton droit le *Elmah* dossier, sélectionnez **propriétés**, puis sélectionnez le **sécurité** onglet.

[![Elmah_folder_Properties_Security_tab](deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12/_static/image6.png)](deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12/_static/image5.png)

(Si vous ne voyez pas **DefaultAppPool** dans les **noms d’utilisateur ou groupe** la liste, vous avez probablement utilisé une autre méthode que celle spécifiée dans ce didacticiel pour configurer IIS et ASP.NET 4 sur votre ordinateur. Dans ce cas, déterminez quelle identité est utilisée par le pool d’applications affecté à l’application de l’Université de Contoso et accorder l’autorisation d’écriture à cette identité. Consultez les liens sur les identités du pool d’applications à la fin de ce didacticiel.)

Cliquez sur **Modifier**. Dans le **autorisations pour Elmah** boîte de dialogue, sélectionnez **DefaultAppPool**, puis sélectionnez le **écrire** case à cocher dans la **autoriser** colonne.

[![Permissions_for_Elmah_dialog_box](deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12/_static/image8.png)](deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12/_static/image7.png)

Cliquez sur **OK** dans les deux boîtes de dialogue.

## <a name="retesting-error-logging-and-reporting"></a>Le nouveau test de la journalisation des erreurs et création de rapports

Testez en provoquant une erreur à nouveau de la même façon (requête d’une URL incorrecte) et exécutez le **journal des erreurs** page. Cette fois, l’erreur s’affiche sur la page.

[![Elmah_Error_Log_page_Test](deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12/_static/image10.png)](deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12/_static/image9.png)

Vous devez également écrire des autorisations sur le *application\_données* dossier, car vous avez des fichiers de base de données SQL Server Compact dans ce dossier, et que vous souhaitez être en mesure de mettre à jour des données dans ces bases de données. Dans ce cas, toutefois, vous n’avez rien à faire supplémentaire, car le processus de déploiement définit automatiquement les autorisations d’écriture sur le *application\_données* dossier.

Maintenant terminé toutes les tâches nécessaires pour obtenir de l’université Contoso fonctionne correctement dans IIS sur votre ordinateur local. Dans l’étape suivante du didacticiel, vous allez apporter au site disponible publiquement en la déployant sur un fournisseur d’hébergement.

## <a name="more-information"></a>Informations complémentaires

Dans cet exemple, la raison pour laquelle Elmah a été impossible d’enregistrer les fichiers journaux était assez évidente. Vous pouvez utiliser le traçage de IIS dans les cas où la cause du problème n’est pas évidente ; consultez [dépannage des demandes à l’aide de suivi des ayant échoué dans IIS 7](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis) sur le site IIS.net.

Pour plus d’informations sur la façon d’accorder des autorisations pour les identités du pool d’applications, consultez [identités du Pool d’applications](https://www.iis.net/learn/manage/configuring-security/application-pool-identities) et [le contenu sécurisé dans IIS via ACL du système de fichiers](https://www.iis.net/learn/get-started/planning-for-security/secure-content-in-iis-through-file-system-acls) sur le site IIS.net.

>[!div class="step-by-step"]
[Précédent](deployment-to-a-hosting-provider-deploying-to-iis-as-a-test-environment-5-of-12.md)
[Suivant](deployment-to-a-hosting-provider-deploying-to-the-production-environment-7-of-12.md)

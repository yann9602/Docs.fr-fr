---
uid: web-forms/overview/deployment/visual-studio-web-deployment/setting-folder-permissions
title: "Déploiement de Web ASP.NET à l’aide de Visual Studio : définition des autorisations de dossier | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, en utilisant des éléments..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/15/2013
ms.topic: article
ms.assetid: 9715a121-fa55-4f1b-a5d2-fb3f6cd8be8f
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/setting-folder-permissions
msc.type: authoredcontent
ms.openlocfilehash: 19bef5ff97fd5b79135df8ca9bd6bd316594cc5e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-web-deployment-using-visual-studio-setting-folder-permissions"></a>Déploiement de Web ASP.NET à l’aide de Visual Studio : définition des autorisations de dossier
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, à l’aide de Visual Studio 2012 ou Visual Studio 2010. Pour plus d’informations sur la série, consultez [le premier didacticiel de la série](introduction.md).


## <a name="overview"></a>Vue d'ensemble

Dans ce didacticiel, vous définissez des autorisations de dossier pour le *Elmah* dossier dans le site web déployé afin que l’application peut créer des fichiers journaux dans le dossier de site.

Lorsque vous testez une application web dans Visual Studio à l’aide du serveur Visual Studio Development (Cassini) ou IIS Express, l’application s’exécute sous votre identité. Vous êtes très probablement d’un administrateur sur votre ordinateur de développement et que vous avez autorité pour faire quelque chose à n’importe quel fichier dans un dossier. Mais quand une application s’exécute sous IIS, il s’exécute sous l’identité définie pour le pool d’applications auquel le site appartient. Il s’agit généralement d’un compte défini par le système qui dispose d’autorisations limitées. Par défaut, il a lu et les autorisations d’exécution sur des fichiers et des dossiers de votre application web, mais il n’a accès en écriture.

Cela devient un problème si votre application crée ou des fichiers de mises à jour, qui est commune dans les applications web. Dans l’application Contoso University, Elmah crée des fichiers XML dans le *Elmah* dossier afin d’enregistrer les détails des erreurs. Même si vous n’utilisez pas quelque chose comme Elmah, votre site peut permettre aux utilisateurs de télécharger des fichiers ou effectuer d’autres tâches qui écrivent des données dans un dossier de votre site.

Rappel : Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez le didacticiel, veillez à consulter le [page Résolution des problèmes](troubleshooting.md).

## <a name="test-error-logging-and-reporting"></a>Journalisation des erreurs de test et de création de rapports

Pour voir comment l’application ne fonctionne pas correctement dans IIS (bien qu’il était au moment de vous la testez dans Visual Studio), vous pouvez provoquer une erreur qui serait normalement être considérée par Elmah, puis ouvrez le journal des erreurs Elmah pour afficher les détails. Si Elmah n’a pas pu créer un fichier XML et de stocker les détails de l’erreur, vous consultez un rapport d’erreurs vide.

Ouvrez un navigateur et accédez à `http://localhost/ContosoUniversity`, et ensuite demander une URL non valide comme *Studentsxxx.aspx*. Vous consultez une page d’erreur générée par le système à la place de la *GenericErrorPage.aspx* page, car le `customErrors` paramètre dans le fichier Web.config est « RemoteOnly » et que vous exécutez IIS localement :

![Page d’erreur 404 HTTP](setting-folder-permissions/_static/image1.png)

Exécutez maintenant *Elmah.axd* pour afficher le rapport d’erreurs. Une fois que vous ouvrez une session avec les informations d’identification du compte administrateur (&quot;admin&quot; et &quot;devpwd&quot;), vous consultez une page du journal des erreurs vide car Elmah n’a pas pu créer un fichier XML dans le *Elmah*dossier :

![Erreur journal vide](setting-folder-permissions/_static/image2.png)

## <a name="set-write-permission-on-the-elmah-folder"></a>Définir l’autorisation en écriture sur le dossier Elmah

Vous pouvez définir manuellement les autorisations de dossier, ou vous pouvez rendre automatique dans le cadre du processus de déploiement. Rend automatique nécessite un code complexe MSBuild, et étant donné que vous ne devez procéder à la première fois que vous déployez, suit les étapes comment le faire manuellement. (Pour plus d’informations sur la façon de rendre cette partie du processus de déploiement, consultez [définition des autorisations de dossier de publication Web](http://sedodream.com/2011/11/08/SettingFolderPermissionsOnWebPublish.aspx) sur le blog de Sayed Hashimi.)

1. Dans **l’Explorateur de fichiers**, accédez à *C:\inetpub\wwwroot\ContosoUniversity*. Avec le bouton droit le *Elmah* dossier, sélectionnez **propriétés**, puis sélectionnez le **sécurité** onglet.
2. Cliquez sur **Modifier**.
3. Dans le **autorisations pour Elmah** boîte de dialogue, sélectionnez **DefaultAppPool**, puis sélectionnez le **écrire** case à cocher dans la **autoriser** colonne.

    ![Autorisations pour le dossier ELMAH](setting-folder-permissions/_static/image3.png)

    (Si vous ne voyez pas **DefaultAppPool** dans les **noms d’utilisateur ou groupe** la liste, vous avez probablement utilisé une autre méthode que celle spécifiée dans ce didacticiel pour configurer IIS et ASP.NET 4 sur votre ordinateur. Dans ce cas, déterminez quelle identité est utilisée par le pool d’applications affecté à l’application de l’Université de Contoso et accorder l’autorisation d’écriture à cette identité. Consultez les liens sur les identités du pool d’applications à la fin de ce didacticiel.) Cliquez sur **OK** dans les deux boîtes de dialogue.

## <a name="retest-error-logging-and-reporting"></a>Testez à nouveau l’enregistrement des erreurs et la création de rapports

Testez en provoquant une erreur à nouveau de la même façon (requête d’une URL incorrecte) et exécutez le **journal des erreurs** page. Cette fois, l’erreur s’affiche sur la page.

![Page de journal d’erreurs ELMAH](setting-folder-permissions/_static/image4.png)

## <a name="summary"></a>Résumé

Maintenant terminé toutes les tâches nécessaires pour obtenir de l’université Contoso fonctionne correctement dans IIS sur votre ordinateur local. Dans l’étape suivante du didacticiel, vous allez apporter au site disponible publiquement en la déployant sur Azure.

## <a name="more-information"></a>Complément d'information

Dans cet exemple, la raison pour laquelle Elmah a été impossible d’enregistrer les fichiers journaux était assez évidente. Vous pouvez utiliser le traçage de IIS dans les cas où la cause du problème n’est pas évidente ; consultez [dépannage des demandes à l’aide de suivi des ayant échoué dans IIS 7](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis) sur le site IIS.net.

Pour plus d’informations sur la façon d’accorder des autorisations pour les identités du pool d’applications, consultez [identités du Pool d’applications](https://www.iis.net/learn/manage/configuring-security/application-pool-identities) et [le contenu sécurisé dans IIS via ACL du système de fichiers](https://www.iis.net/learn/get-started/planning-for-security/secure-content-in-iis-through-file-system-acls) sur le site IIS.net.

>[!div class="step-by-step"]
[Précédent](deploying-to-iis.md)
[Suivant](deploying-to-production.md)

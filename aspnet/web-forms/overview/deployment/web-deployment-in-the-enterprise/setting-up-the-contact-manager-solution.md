---
uid: web-forms/overview/deployment/web-deployment-in-the-enterprise/setting-up-the-contact-manager-solution
title: Configuration de la Solution de gestionnaire de contacts | Documents Microsoft
author: jrjlee
description: "Cette rubrique décrit comment télécharger et configurer la solution de gestionnaire de contacts pour exécuter localement sur une station de travail du développeur."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 200b973c-776b-4a9b-9e82-39fda6120a52
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/web-deployment-in-the-enterprise/setting-up-the-contact-manager-solution
msc.type: authoredcontent
ms.openlocfilehash: 85468949ee61504d6076a191b70a96e8018c67aa
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="setting-up-the-contact-manager-solution"></a>Configuration de la Solution de gestionnaire de contacts
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment télécharger et configurer la solution de gestionnaire de contacts pour exécuter localement sur une station de travail du développeur.


## <a name="system-requirements"></a>Configuration système requise

Pour exécuter la solution de gestionnaire de contacts localement et effectuer les tâches décrites dans ce didacticiel, vous devez installer ce logiciel sur votre station de travail du développeur :

- Visual Studio 2010 Service Pack 1, Premium ou Édition intégrale
- Internet Information Services (IIS) 7.5 Express
- SQL Server Express 2008 R2
- IIS outil de déploiement Web (Web Deploy) 2.1 ou version ultérieure
- ASP.NET 4.0
- ASP.NET MVC 3
- .NET Framework 4
- .NET Framework 3.5 SP1

À l’exception de Visual Studio 2010, vous pouvez télécharger et installer les dernières versions de tous ces produits et les composants à travers le [Web Platform Installer](https://go.microsoft.com/?linkid=9805118).

## <a name="download-and-extract-the-solution"></a>Téléchargez et extrayez la Solution

Vous pouvez télécharger l’exemple d’application Gestionnaire de contacts à partir de MSDN Code Gallery [ici](https://code.msdn.microsoft.com/Deploying-Web-Applications-9d9093c0).

## <a name="configure-and-run-the-solution"></a>Configurer et exécuter la Solution

Pour configurer et exécuter la solution de gestionnaire de contacts sur votre ordinateur local, vous devez effectuer ces étapes :

1. Si vous n’avez pas déjà, créez une base de services d’application ASP.NET locale avec les fonctionnalités de gestion des appartenances et de rôles est activées.
2. Modifier les chaînes de connexion dans le *web.config* fichiers pour pointer vers votre instance locale de SQL Server Express.
3. Exécutez la solution dans Visual Studio 2010.

Le reste de cette section fournit plus d’informations sur l’exécution de chacune de ces tâches.

**Pour créer la base de données de services d’application**

1. Ouvrez une invite de commandes de Visual Studio 2010. Pour ce faire, sur le **Démarrer** menu, pointez sur **tous les programmes**, cliquez sur **Microsoft Visual Studio 2010**, cliquez sur **Visual Studio Tools**, puis Cliquez sur **invite de commandes Visual Studio (2010)**.
2. À l’invite de commandes, tapez la commande suivante et appuyez sur ENTRÉE :

    [!code-console[Main](setting-up-the-contact-manager-solution/samples/sample1.cmd)]

    1. Utilisez le **– C** commutateur pour spécifier la chaîne de connexion pour votre serveur de base de données.
    2. Utilisez le **–** commutateur pour spécifier les fonctionnalités que vous souhaitez ajouter à la base de données de services de l’application. Dans ce cas, **m** indique que vous souhaitez ajouter la prise en charge pour le fournisseur d’appartenances et **r** indique que vous souhaitez ajouter la prise en charge pour le Gestionnaire de rôles.
    3. Utilisez le **– d** commutateur pour spécifier un nom pour votre base de données de services d’application. Si vous omettez cette option, l’utilitaire créera une base de données portant le nom par défaut **aspnetdb**.
3. Lorsque la base de données a été créé avec succès, l’invite de commandes affiche une confirmation.

    ![](setting-up-the-contact-manager-solution/_static/image1.png)

> [!NOTE]
> Pour plus d’informations sur le compte aspnet\_regsql utilitaire, consultez [ASP.NET SQL Server Registration Tool (Aspnet\_regsql.exe)](https://msdn.microsoft.com/en-us/library/ms229862(v=vs.100).aspx).


L’étape suivante consiste à vous assurer que les chaînes de connexion dans la solution de gestionnaire de contacts pointent sur votre instance locale de SQL Server Express.

**Pour mettre à jour les chaînes de connexion**

1. Ouvrez la solution de gestionnaire de contacts dans Visual Studio 2010.
2. Dans le **l’Explorateur de solutions** fenêtre, développez le **ContactManager.Mvc** de projet, puis double-cliquez sur le **Web.config** nœud.

    > [!NOTE]
    > Le projet ContactManager.Mvc inclut deux *web.config* fichiers. Vous devez modifier le fichier au niveau du projet.

    ![](setting-up-the-contact-manager-solution/_static/image2.png)
3. Dans le **connectionStrings** élément, vérifiez que la chaîne de connexion nommée **ApplicationServices** pointe vers votre base de services d’application ASP.NET locale.

    [!code-xml[Main](setting-up-the-contact-manager-solution/samples/sample2.xml)]
4. Dans le **l’Explorateur de solutions** fenêtre, développez le **ContactManager.Service** de projet, puis double-cliquez sur le **Web.config** nœud.

    ![](setting-up-the-contact-manager-solution/_static/image3.png)
5. Dans le **connectionStrings** élément, dans la chaîne de connexion nommée **ContactManagerContext**, vérifiez que le **Source de données** est définie sur votre instance locale de SQL Serveur Express. Vous n’avez pas besoin de modifier rien d’autre dans la chaîne de connexion.

    [!code-xml[Main](setting-up-the-contact-manager-solution/samples/sample3.xml)]
6. Enregistrez tous les fichiers ouverts.

Vous devez maintenant être prêt à exécuter la solution de gestionnaire de contacts sur votre ordinateur local.

> [!NOTE]
> Si vous suivez ces étapes sans d’abord créer une base de données de services d’application, ASP.NET crée la base de données la première fois que vous essayez de créer un utilisateur. Toutefois, création manuelle de la base de données vous donne un contrôle plus important sur l’ensemble des fonctionnalités services application que vous souhaitez prendre en charge.


**Pour exécuter la solution de gestionnaire de contacts**

1. Dans Visual Studio 2010, appuyez sur F5.
2. Internet Explorer démarre et demande l’URL de l’application ASP.NET MVC 3 Contact Manager. Par défaut, l’application affiche la **tous les Contacts** page.

    ![](setting-up-the-contact-manager-solution/_static/image4.png)
3. Ajouter quelques contacts et vérifiez que l’application fonctionne comme prévu.

    ![](setting-up-the-contact-manager-solution/_static/image5.png)
4. Accédez à `http://localhost:50114/Account/Register` (ajuster l’URL si vous hébergez l’application sur un port différent). Ajouter un nom d’utilisateur, une adresse de messagerie et un mot de passe et vérifiez que vous êtes en mesure d’inscrire un compte avec succès.

    ![](setting-up-the-contact-manager-solution/_static/image6.png)
5. Accédez à `http://localhost:50114/Account/LogOn` (ajuster l’URL si vous hébergez l’application sur un port différent). Vérifiez que vous êtes en mesure de se connecter en utilisant le compte que vous venez de créer.

    ![](setting-up-the-contact-manager-solution/_static/image7.png)
6. Fermez Internet Explorer pour arrêter le débogage.

## <a name="conclusion"></a>Conclusion

À ce stade, la solution de gestionnaire de contacts doit-elle être configurée pour s’exécuter sur votre ordinateur local. Lorsque vous travaillez dans les autres rubriques dans ce didacticiel, vous pouvez utiliser la solution en tant que référence.

La rubrique suivante, [présentation du fichier de projet](understanding-the-project-file.md), explique comment vous pouvez utiliser les fichiers de projet Microsoft Build Engine (MSBuild) personnalisés au sein de la solution de gestionnaire de contacts pour contrôler le processus de déploiement.

>[!div class="step-by-step"]
[Précédent](the-contact-manager-solution.md)
[Suivant](understanding-the-project-file.md)

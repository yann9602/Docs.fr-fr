---
title: "Déploiement continu sur Azure avec Visual Studio et Git"
author: rick-anderson
description: "Découvrez comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer sur Azure App Service en utilisant Git pour le déploiement continu."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 78f4eed188323f2f43fafbb69d3fca9b59129ad2
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="continuous-deployment-to-azure-for-aspnet-core-with-visual-studio-and-git"></a>Déploiement continu dans Azure pour ASP.NET Core avec Visual Studio et Git

Par [Erik Reitan](https://github.com/Erikre)

Ce didacticiel montre comment créer une application de web ASP.NET Core à l’aide de Visual Studio et le déployer vers Azure App Service à partir de Visual Studio à l’aide d’un déploiement continu.

Consultez aussi [Use VSTS to Build and Publish to an Azure Web App with Continuous Deployment](/vsts/build-release/archive/apps/aspnet/aspnet-4-ci-cd-azure-automatic), qui montre comment configurer un workflow de livraison continue pour [Azure App Service](/azure/app-service/app-service-web-overview) en utilisant Visual Studio Team Services. Azure livraison continue dans Team Services simplifie la configuration d’un pipeline de déploiement fiable pour publier des mises à jour pour les applications hébergées dans Azure App Service. Le pipeline peut être configuré à partir du portail Azure pour générer, exécuter des tests, déployer vers un emplacement intermédiaire, puis déployer en production.

> [!NOTE]
> Pour effectuer ce didacticiel, un compte Microsoft Azure est requis. Pour obtenir un compte, [activer les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) ou [s’inscrire à une évaluation gratuite](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prérequis

Ce didacticiel suppose que les logiciels suivants sont installés :

* [Visual Studio](https://www.visualstudio.com)
* [Kit de développement .NET core](https://www.microsoft.com/net/download/core) (runtime et les outils)
* [Git](https://git-scm.com/downloads) pour Windows

## <a name="create-an-aspnet-core-web-app"></a>Créer une application web ASP.NET Core

1. Démarrez Visual Studio.

1. Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.

1. Sélectionnez le modèle de projet **Application web ASP.NET Core**. Il apparaît sous **Installé** > **Modèles** > **Visual C#** > **.NET Core**. Attribuez un nom au projet `SampleWebAppDemo`. Sélectionnez l’option **Créer un dépôt Git**, puis cliquez sur **OK**.

   ![Boîte de dialogue Nouveau projet](azure-continuous-deployment/_static/01-new-project.png)

1. Dans la boîte de dialogue **Nouveau projet ASP.NET Core**, sélectionnez le modèle ASP.NET Core **Vide**, puis cliquez sur **OK**.

   ![Boîte de dialogue Nouveau projet ASP.NET](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> La version la plus récente du .NET Core est 2.0.

### <a name="running-the-web-app-locally"></a>Exécution de l’application web localement

1. Une fois que Visual Studio a terminé la création de l’application, exécutez l’application en sélectionnant **Déboguer** > **Démarrer le débogage**. En guise d’alternative, appuyez sur **F5**.

   L’initialisation de Visual Studio et de la nouvelle application peut prendre un certain temps. Une fois terminé, le navigateur affiche l’application en cours d’exécution.

   ![Fenêtre de navigateur montrant l’application en cours d’exécution qui affiche « Hello World! »](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Après avoir vérifié l’application Web en cours d’exécution, fermez le navigateur et sélectionnez l’icône « Arrêter le débogage » dans la barre d’outils de Visual Studio pour arrêter l’application.

## <a name="create-a-web-app-in-the-azure-portal"></a>Créer une application web dans le portail Azure

La procédure suivante crée une application web dans le portail Azure :

1. Se connecter à la [portail Azure](https://portal.azure.com).

1. Sélectionnez **nouveau** en haut à gauche de l’interface du portail.

1. Sélectionnez **Web + Mobile** > **application Web**.

   ![Portail Microsoft Azure : Nouveau bouton Web + Mobile sous Place de marché - Bouton Application Web sous Applications à la une](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. Dans le panneau **Application web**, entrez une valeur unique pour le **Nom App Service**.

   ![Panneau Application web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > Le **nom du Service application** nom doit être unique. Le portail applique cette règle lorsque le nom est fourni. Si vous en fournissant une valeur différente, remplacez cette valeur pour chaque occurrence de **SampleWebAppDemo** dans ce didacticiel.

   Dans le panneau **Application web**, sélectionnez un **Plan/emplacement App Service** existant ou créez-en un. Si vous créez un nouveau plan, sélectionnez le niveau de tarification, d’emplacement et d’autres options. Pour plus d’informations sur les plans de Service d’applications, consultez [vue d’ensemble approfondie des plans de Service d’applications Azure](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

1. Sélectionnez **Créer**. Windows Azure configurer et démarrer l’application web.

   ![Portail Azure : Panneau Exemple d’application web Demo 01 Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Activer la publication Git pour la nouvelle application web

GIT est un système de contrôle de version distribuée qui peut être utilisé pour déployer une application web de Service d’applications Azure. Code d’application Web est stocké dans un référentiel Git local et le code est déployé sur Azure en envoyant à un référentiel distant.

1. Connectez-vous à la [portail Azure](https://portal.azure.com).

1. Sélectionnez **des Services d’application** pour afficher la liste des services d’application associé à l’abonnement Azure.

1. Sélectionnez l’application web créée dans la section précédente de ce didacticiel.

1. Dans le panneau **Déploiement**, sélectionnez **Options de déploiement** > **Choisir une source** > **Référentiel Git local**.

   ![Panneau Paramètres : Panneau Source de déploiement : Panneau Choisir une source](azure-continuous-deployment/_static/deployment-options.png)

1. Sélectionnez **OK**.

1. Si les informations d’identification de déploiement pour la publication d’une application web ou autre application de Service d’applications n’ont pas été précédemment configurées, vous pouvez les configurer maintenant :

   * Sélectionnez **paramètres** > **informations d’identification de déploiement**. Le **définir les informations d’identification de déploiement** panneau s’affiche.
   * Créez un nom d’utilisateur et un mot de passe. Enregistrer le mot de passe pour une utilisation ultérieure lorsque vous configurez Git.
   * Sélectionnez **Enregistrer**.

1. Dans le **Web App** panneau, sélectionnez **paramètres** > **propriétés**. L’URL du référentiel Git distant à déployer sur est affiché sous **URL GIT**.

1. Copiez la valeur de **URL Git** pour l’utiliser plus tard dans le didacticiel.

   ![Portail Azure : Panneau Propriétés de l’application](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publier l’application web vers Azure App Service

Dans cette section, créez un référentiel Git local à l’aide de Visual Studio et par envoi de données à partir de ce référentiel vers Azure pour déployer l’application web. Les étapes impliquées sont les suivantes :

* Ajoutez le paramètre de référentiel distant à l’aide de la valeur d’URL de GIT, donc le référentiel local peut être déployé sur Azure.
* Valider les modifications du projet.
* Push des modifications de projet à partir du référentiel local vers le référentiel distant sur Azure.

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Solution 'SampleWebAppDemo'** et sélectionnez **Valider**. Le **Team Explorer** s’affiche.

   ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/10-team-explorer.png)

1. Dans **Team Explorer**, sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres du dépôt**.

1. Dans le **distantes** section de la **paramètres du référentiel**, sélectionnez **ajouter**. Le **ajouter distant** boîte de dialogue s’affiche.

1. Définissez le **Nom** de l’élément distant sur **Azure-SampleApp**.

1. Définir la valeur de **Fetch** à la **URL Git** qui est copié à partir de Azure précédemment dans ce didacticiel. Notez qu’il s’agit de l’URL qui se termine par **.git**.

   ![Boîte de dialogue Modifier un élément distant](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > En guise d’alternative, spécifiez le référentiel distant à partir de la **fenêtre commande** en ouvrant le **fenêtre commande**, la modification au répertoire du projet et en entrant la commande. Exemple :
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. Sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres globaux**. Confirmez que le nom et adresse de messagerie sont définis. Sélectionnez **mise à jour** si nécessaire.

1. Sélectionnez **Accueil** > **Modifications** pour revenir à la vue **Modifications**.

1. Entrez un message de validation, tel que **initiale Push #1** et sélectionnez **validation**. Cette action crée un *validation* localement.

   ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > En guise d’alternative, écrire les modifications à partir de la **fenêtre commande** en ouvrant le **fenêtre commande**, la modification au répertoire du projet et en entrant les commandes git. Exemple :
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. Sélectionnez **Accueil** > **Synchroniser** > **Actions** > **Ouvrir l’invite de commandes**. L’invite de commandes s’ouvre dans le répertoire du projet.

1. Entrez la commande suivante dans la fenêtre Commande :

   `git push -u Azure-SampleApp master`

1. Entrez Azure **informations d’identification de déploiement** mot de passe créé précédemment dans Azure.

   Cette commande démarre le processus de l’envoi de fichiers de projet local vers Azure. La sortie de la commande ci-dessus se termine par un message que le déploiement a réussi.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Si la collaboration sur le projet est requise, envisagez d’envoi au [GitHub](https://github.com) avant d’installer sur Azure.
 
### <a name="verify-the-active-deployment"></a>Vérifier le déploiement actif

Vérifiez que le transfert d’application web à partir de l’environnement local vers Azure a réussi.

Dans le [Azure Portal](https://portal.azure.com), sélectionnez l’application web. Sélectionnez **déploiement** > **options de déploiement**.

![Portail Azure : Panneau Paramètres : Panneau Déploiements montrant la réussite du déploiement](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Exécuter l’application dans Azure

Maintenant que l’application web est déployée sur Azure, exécutez l’application.

Cela peut être accompli de deux manières :

* Dans le portail Azure, recherchez le panneau des applications web pour l’application web. Sélectionnez **Parcourir** pour l’afficher dans le navigateur par défaut.
* Ouvrez un navigateur et entrez l’URL de l’application web. Exemple : `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Mettre à jour de l’application web et publier à nouveau

Après avoir apporté des modifications du code local, republiez :

1. Dans **l’Explorateur de solutions** de Visual Studio, ouvrez le fichier *Startup.cs*.

1. Dans la méthode `Configure`, modifiez la méthode `Response.WriteAsync` comme suit :

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Enregistrer les modifications apportées à *Startup.cs*.

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Solution 'SampleWebAppDemo'** et sélectionnez **Valider**. Le **Team Explorer** s’affiche.

1. Entrez un message de validation, tel que `Update #2`.

1. Appuyez sur le bouton **Valider** pour valider les modifications du projet.

1. Sélectionnez **Accueil** > **Synchroniser** > **Actions** > **Envoyer (push)**.

> [!NOTE]
> En guise d’alternative, envoyer les modifications à partir de la **fenêtre commande** en ouvrant le **fenêtre commande**, la modification au répertoire du projet et en entrant une commande de git. Exemple :
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Afficher l’application web mise à jour dans Azure

Afficher l’application web mis à jour en sélectionnant **Parcourir** depuis le panneau des applications web dans le portail Azure ou en ouvrant un navigateur et entrez l’URL de l’application web. Exemple : `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Ressources supplémentaires

* [Utiliser VSTS pour générer et publier à une application Web Azure avec un déploiement continu](/vsts/build-release/archive/apps/aspnet/aspnet-4-ci-cd-azure-automatic)
* [Projet Kudu](https://github.com/projectkudu/kudu/wiki)

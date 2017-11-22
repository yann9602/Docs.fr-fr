---
title: "Déploiement continu sur Azure avec Visual Studio et Git"
author: rick-anderson
description: "Découvrez comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer sur Azure App Service, en utilisant Git pour le déploiement continu."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 2707c7a8-2350-4304-9856-fda58e5c0a16
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/azure-continuous-deployment
ms.openlocfilehash: f7ea2e76fdee19a3d964e42053f0060a0a505e5b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="continuous-deployment-to-azure-for-aspnet-core-with-visual-studio-and-git"></a>Déploiement continu sur Azure pour ASP.NET Core, avec Visual Studio et Git

Par [Erik Reitan](https://github.com/Erikre)

Ce didacticiel vous montre comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer à partir de Visual Studio sur Azure App Service en utilisant le déploiement continu. 

Consultez aussi [Use VSTS to Build and Publish to an Azure Web App with Continuous Deployment](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic), qui montre comment configurer un workflow de livraison continue pour [Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/) en utilisant Visual Studio Team Services. La livraison continue Azure dans Team Services simplifie la configuration d’un pipeline de déploiement fiable pour publier les mises à jour de votre application dans Azure App Service. Le pipeline peut être configuré à partir du portail Azure pour générer, exécuter des tests, déployer sur un emplacement de préproduction, puis déployer en production.

> [!NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous n’avez pas de compte, vous pouvez [activer vos avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [vous inscrire pour une évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Conditions préalables

Ce didacticiel suppose que vous avez déjà installé les éléments suivants :

* [Visual Studio](https://www.visualstudio.com)

* [ASP.NET Core](https://www.microsoft.com/net/download/core) (runtime et outils)

* [Git](https://git-scm.com/downloads) pour Windows

## <a name="create-an-aspnet-core-web-app"></a>Créer une application web ASP.NET Core

1. Démarrez Visual Studio.

2. Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.

3. Sélectionnez le modèle de projet **Application web ASP.NET Core**. Il apparaît sous **Installé** > **Modèles** > **Visual C#** > **.NET Core**. Attribuez un nom au projet `SampleWebAppDemo`. Sélectionnez l’option **Créer un dépôt Git**, puis cliquez sur **OK**.

   ![Boîte de dialogue Nouveau projet](azure-continuous-deployment/_static/01-new-project.png)

4. Dans la boîte de dialogue **Nouveau projet ASP.NET Core**, sélectionnez le modèle ASP.NET Core **Vide**, puis cliquez sur **OK**.

   ![Boîte de dialogue Nouveau projet ASP.NET](azure-continuous-deployment/_static/02-web-site-template.png)

>[!NOTE]
    >La dernière version de .NET Core est 2.0.

### <a name="running-the-web-app-locally"></a>Exécution de l’application web localement

1. Une fois que Visual Studio a terminé la création de l’application, exécutez l’application en sélectionnant **Déboguer** -> **Démarrer le débogage**. Vous pouvez aussi appuyer sur **F5**.

   L’initialisation de Visual Studio et de la nouvelle application peut prendre un certain temps. Une fois qu’elle est terminée, le navigateur affiche l’application en cours d’exécution.

   ![Fenêtre de navigateur montrant l’application en cours d’exécution qui affiche « Hello World! »](azure-continuous-deployment/_static/04-browser-runapp.png)

2. Après avoir examiné l’application web en cours d’exécution, fermez le navigateur et cliquez sur l’icône « Arrêter le débogage » dans la barre d’outils de Visual Studio pour arrêter l’application.

## <a name="create-a-web-app-in-the-azure-portal"></a>Créer une application web dans le portail Azure

Les étapes suivantes vous guident dans la création d’une application web dans le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Appuyez sur **NOUVEAU** en haut à gauche du portail
3. Appuyez sur **Web + Mobile** > **Application Web**

    ![Portail Microsoft Azure : Nouveau bouton Web + Mobile sous Place de marché - Bouton Application Web sous Applications à la une](azure-continuous-deployment/_static/05-azure-newwebapp.png)

4.  Dans le panneau **Application web**, entrez une valeur unique pour le **Nom App Service**.

    ![Panneau Application web](azure-continuous-deployment/_static/06-azure-newappblade.png)

    >[!NOTE]
    >Le nom dans **Nom App Service** doit être unique. Le portail applique cette règle quand vous essayez d’entrer le nom. Si vous avez entré une valeur différente, vous devez substituer cette valeur pour chaque occurrence de **SampleWebAppDemo** que vous voyez dans ce didacticiel.

    &nbsp;
    
    Dans le panneau **Application web**, sélectionnez un **Plan/emplacement App Service** existant ou créez-en un. Si vous créez un plan, sélectionnez le niveau tarifaire, l’emplacement et les autres options. Pour plus d’informations sur les plans App Service, consultez [Présentation détaillée des plans Azure App Service](https://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/).

5.  Cliquez sur **Créer**. Azure approvisionne et démarre votre application web.

    ![Portail Azure : Panneau Exemple d’application web Demo 01 Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Activer la publication Git pour la nouvelle application web

GIT est un système de gestion de versions distribué que vous pouvez utiliser pour déployer votre application web Azure App Service. Vous stockez le code que vous écrivez pour votre application web dans un dépôt Git local et vous déployez votre code sur Azure en publiant sur un dépôt distant.

1. Connectez-vous au [portail Azure](https://portal.azure.com) si vous n’y êtes pas déjà connecté.

2. Cliquez sur **App Services** pour afficher la liste des services App Services associées à votre abonnement Azure.

3. Sélectionnez l’application web que vous avez créée dans la section précédente de ce didacticiel.

4. Dans le panneau **Déploiement**, sélectionnez **Options de déploiement** > **Choisir une source** > **Référentiel Git local**.

   ![Panneau Paramètres : Panneau Source de déploiement : Panneau Choisir une source](azure-continuous-deployment/_static/deployment-options.png)

5. Cliquez sur **OK**.

6. Si vous n’avez pas déjà configuré les informations d’identification de déploiement pour la publication d’une application web ou d’une autre application App Service, vous pouvez les configurer maintenant :

   * Cliquez sur **Paramètres** > **Informations d’identification de déploiement**. Le panneau **Définir les informations d’identification de déploiement** s’affiche.

   * Créez un nom d’utilisateur et un mot de passe.  Vous aurez besoin de ce mot de passe plus tard lors de la configuration de Git.

   * Cliquez sur **Enregistrer**.

7. Dans le panneau **Application web**, cliquez sur **Paramètres** > **Propriétés**. L’URL du dépôt Git distant sur lequel vous allez déployer est affiché sous **URL Git**.

8. Copiez la valeur de **URL Git** pour l’utiliser plus tard dans le didacticiel.

   ![Portail Azure : Panneau Propriétés de l’application](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a>Publier votre application web sur Azure App Service

Dans cette section, vous créez un dépôt Git local à l’aide de Visual Studio, et vous envoyez (push) de ce dépôt vers Azure pour déployer votre application web. Les étapes impliquées sont les suivantes :

   * Ajoutez le paramètre de dépôt distant en utilisant la valeur de votre URL Git, afin de pouvoir déployer votre dépôt sur Azure.

   * Validez les modifications de votre projet.

   * Envoyez (push) les modifications de votre projet depuis votre dépôt local vers votre dépôt distant sur Azure.

&nbsp;
   
1.  Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Solution 'SampleWebAppDemo'** et sélectionnez **Valider**. **Team Explorer** s’affiche.

    ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/10-team-explorer.png)

2.  Dans **Team Explorer**, sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres du dépôt**.

3.  Dans la section **Distants** des **Paramètres du dépôt**, sélectionnez **Ajouter**. La boîte de dialogue **Ajouter un élément distant** s’affiche.

4.  Définissez le **Nom** de l’élément distant sur **Azure-SampleApp**.

5.  Définissez la valeur de **Récupérer** sur **l’URL Git** que vous avez précédemment copiée depuis Azure dans ce didacticiel. Notez qu’il s’agit de l’URL qui se termine par **.git**.

    ![Boîte de dialogue Modifier un élément distant](azure-continuous-deployment/_static/11-add-remote.png)

    >[!NOTE]
    >Vous pouvez aussi spécifier le dépôt distant à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire de votre projet et en entrant la commande. Par exemple :`git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

6.  Sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres globaux**. Vérifiez que votre nom et votre adresse e-mail sont définis. Il peut aussi être nécessaire de sélectionner **Mettre à jour**.

7.  Sélectionnez **Accueil** > **Modifications** pour revenir à la vue **Modifications**.

8.  Entrez un message de validation, comme **Initial Push #1**, et cliquez sur **Valider**. Cette action crée une *validation* localement. Ensuite, vous devez vous *synchroniser* avec Azure.

    ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/12-initial-commit.png)

    >[!NOTE]
    >Vous pouvez aussi valider vos modifications à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire de votre projet et en entrant les commandes Git. Exemple :
    >
    >`git add .`
    >
    >`git commit -am "Initial Push #1"`

9.  Sélectionnez **Accueil** > **Synchroniser** > **Actions** > **Ouvrir l’invite de commandes**. L’invite de commandes s’ouvre sur le répertoire de votre projet.

10.  Entrez la commande suivante dans la fenêtre Commande :

    `git push -u Azure-SampleApp master`

11.  Entrez votre mot de passe des **informations d’identification de déploiement** Azure que vous avez créé précédemment dans Azure.

    >[!NOTE]
    >Votre mot de passe n’est pas visible quand vous l’entrez.

    Cette commande démarre le processus d’envoi (push) de vos fichiers de projet locaux vers Azure. La sortie de la commande ci-dessus se termine par un message indiquant que le déploiement a réussi.
        
    ```
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
    * [new branch]      master -> master
    Branch master set up to track remote branch master from Azure-SampleApp.
    ```
    > [!NOTE]
    > Si vous avez besoin de collaborer sur un projet, vous devez envisager d’envoyer (push) à [GitHub](https://github.com) entre les envois (push) à Azure.
 
### <a name="verify-the-active-deployment"></a>Vérifier le déploiement actif

Vous pouvez vérifier que vous avez transféré correctement l’application web depuis votre environnement local vers Azure. Vous voyez dans la liste le déploiement réussi.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre application web. Ensuite, sélectionnez **Déploiement** > **Options de déploiement**.

   ![Portail Azure : Panneau Paramètres : Panneau Déploiements montrant la réussite du déploiement](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Exécuter l’application dans Azure

Maintenant que vous avez déployé votre application web sur Azure, vous pouvez l’exécuter.

Vous pouvez le faire de deux façons :

* Dans le portail Azure, recherchez le panneau Application web pour votre application, et cliquez sur **Parcourir** pour afficher votre application dans votre navigateur par défaut.

* Ouvrez un navigateur et entrez l’URL de votre application web. Exemple :

  `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-your-web-app-and-republish"></a>Mettre à jour votre application web et la republier

Après avoir apporté des modifications à votre code local, vous pouvez republier.

1.  Dans **l’Explorateur de solutions** de Visual Studio, ouvrez le fichier *Startup.cs*.

2.  Dans la méthode `Configure`, modifiez la méthode `Response.WriteAsync` comme suit :

    ```aspx-cs
    await context.Response.WriteAsync("Hello World! Deploy to Azure.");
    ```
3.  Enregistrer les modifications de *Startup.cs*.

4.  Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Solution 'SampleWebAppDemo'** et sélectionnez **Valider**. **Team Explorer** s’affiche.

5.  Entrez un message de validation, comme:

    ```none
    Update #2
    ```

6.  Appuyez sur le bouton **Valider** pour valider les modifications du projet.

7.  Sélectionnez **Accueil** > **Synchroniser** > **Actions** > **Envoyer (push)**.

>[!NOTE]
>Vous pouvez aussi envoyer (push) vos modifications à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire de votre projet et en entrant une commande Git. Exemple :
>
>`git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Afficher l’application web mise à jour dans Azure

Affichez votre application web mise à jour en sélectionnant **Parcourir** dans le panneau Application web du portail Azure, ou en ouvrant un navigateur et en entrant l’URL de votre application web. Exemple :

   `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Ressources supplémentaires

* [Publication et déploiement](index.md)

* [Projet Kudu](https://github.com/projectkudu/kudu/wiki)

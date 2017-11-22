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
# <a name="continuous-deployment-to-azure-for-aspnet-core-with-visual-studio-and-git"></a><span data-ttu-id="3a892-104">Déploiement continu sur Azure pour ASP.NET Core, avec Visual Studio et Git</span><span class="sxs-lookup"><span data-stu-id="3a892-104">Continuous deployment to Azure for ASP.NET Core, with Visual Studio and Git</span></span>

<span data-ttu-id="3a892-105">Par [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="3a892-105">By [Erik Reitan](https://github.com/Erikre)</span></span>

<span data-ttu-id="3a892-106">Ce didacticiel vous montre comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer à partir de Visual Studio sur Azure App Service en utilisant le déploiement continu.</span><span class="sxs-lookup"><span data-stu-id="3a892-106">This tutorial shows you how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span> 

<span data-ttu-id="3a892-107">Consultez aussi [Use VSTS to Build and Publish to an Azure Web App with Continuous Deployment](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic), qui montre comment configurer un workflow de livraison continue pour [Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/) en utilisant Visual Studio Team Services.</span><span class="sxs-lookup"><span data-stu-id="3a892-107">See also [Use VSTS to Build and Publish to an Azure Web App with Continuous Deployment](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/) using Visual Studio Team Services.</span></span> <span data-ttu-id="3a892-108">La livraison continue Azure dans Team Services simplifie la configuration d’un pipeline de déploiement fiable pour publier les mises à jour de votre application dans Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="3a892-108">Azure Continuous Delivery in Team Services simplifies setting up a robust deployment pipeline to publish updates for your app to Azure App Service.</span></span> <span data-ttu-id="3a892-109">Le pipeline peut être configuré à partir du portail Azure pour générer, exécuter des tests, déployer sur un emplacement de préproduction, puis déployer en production.</span><span class="sxs-lookup"><span data-stu-id="3a892-109">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot,  and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="3a892-110">Pour effectuer ce didacticiel, vous avez besoin d’un compte Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="3a892-110">To complete this tutorial, you need a Microsoft Azure account.</span></span> <span data-ttu-id="3a892-111">Si vous n’avez pas de compte, vous pouvez [activer vos avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [vous inscrire pour une évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="3a892-111">If you don't have an account, you can [activate your MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3a892-112">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="3a892-112">Prerequisites</span></span>

<span data-ttu-id="3a892-113">Ce didacticiel suppose que vous avez déjà installé les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="3a892-113">This tutorial assumes you have already installed the following:</span></span>

* [<span data-ttu-id="3a892-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a892-114">Visual Studio</span></span>](https://www.visualstudio.com)

* <span data-ttu-id="3a892-115">[ASP.NET Core](https://www.microsoft.com/net/download/core) (runtime et outils)</span><span class="sxs-lookup"><span data-stu-id="3a892-115">[ASP.NET Core](https://www.microsoft.com/net/download/core) (runtime and tooling)</span></span>

* <span data-ttu-id="3a892-116">[Git](https://git-scm.com/downloads) pour Windows</span><span class="sxs-lookup"><span data-stu-id="3a892-116">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="3a892-117">Créer une application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a892-117">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="3a892-118">Démarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3a892-118">Start Visual Studio.</span></span>

2. <span data-ttu-id="3a892-119">Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="3a892-119">From the **File** menu, select **New** > **Project**.</span></span>

3. <span data-ttu-id="3a892-120">Sélectionnez le modèle de projet **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3a892-120">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="3a892-121">Il apparaît sous **Installé** > **Modèles** > **Visual C#** > **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3a892-121">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="3a892-122">Attribuez un nom au projet `SampleWebAppDemo`.</span><span class="sxs-lookup"><span data-stu-id="3a892-122">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="3a892-123">Sélectionnez l’option **Créer un dépôt Git**, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="3a892-123">Select the **Create new Git respository** option and click **OK**.</span></span>

   ![Boîte de dialogue Nouveau projet](azure-continuous-deployment/_static/01-new-project.png)

4. <span data-ttu-id="3a892-125">Dans la boîte de dialogue **Nouveau projet ASP.NET Core**, sélectionnez le modèle ASP.NET Core **Vide**, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="3a892-125">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Boîte de dialogue Nouveau projet ASP.NET](azure-continuous-deployment/_static/02-web-site-template.png)

>[!NOTE]
    ><span data-ttu-id="3a892-127">La dernière version de .NET Core est 2.0.</span><span class="sxs-lookup"><span data-stu-id="3a892-127">Most recent release of .NET Core is 2.0</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="3a892-128">Exécution de l’application web localement</span><span class="sxs-lookup"><span data-stu-id="3a892-128">Running the web app locally</span></span>

1. <span data-ttu-id="3a892-129">Une fois que Visual Studio a terminé la création de l’application, exécutez l’application en sélectionnant **Déboguer** -> **Démarrer le débogage**.</span><span class="sxs-lookup"><span data-stu-id="3a892-129">Once Visual Studio finishes creating the app, run the app by selecting **Debug** -> **Start Debugging**.</span></span> <span data-ttu-id="3a892-130">Vous pouvez aussi appuyer sur **F5**.</span><span class="sxs-lookup"><span data-stu-id="3a892-130">As an alternative, you can press **F5**.</span></span>

   <span data-ttu-id="3a892-131">L’initialisation de Visual Studio et de la nouvelle application peut prendre un certain temps.</span><span class="sxs-lookup"><span data-stu-id="3a892-131">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="3a892-132">Une fois qu’elle est terminée, le navigateur affiche l’application en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="3a892-132">Once it is complete, the browser will show the running app.</span></span>

   ![Fenêtre de navigateur montrant l’application en cours d’exécution qui affiche « Hello World! »](azure-continuous-deployment/_static/04-browser-runapp.png)

2. <span data-ttu-id="3a892-134">Après avoir examiné l’application web en cours d’exécution, fermez le navigateur et cliquez sur l’icône « Arrêter le débogage » dans la barre d’outils de Visual Studio pour arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="3a892-134">After reviewing the running Web app, close the browser and click the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="3a892-135">Créer une application web dans le portail Azure</span><span class="sxs-lookup"><span data-stu-id="3a892-135">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="3a892-136">Les étapes suivantes vous guident dans la création d’une application web dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="3a892-136">The following steps will guide you through creating a web app in the Azure Portal.</span></span>

1. <span data-ttu-id="3a892-137">Connectez-vous au [portail Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="3a892-137">Log in to the [Azure Portal](https://portal.azure.com)</span></span>
2. <span data-ttu-id="3a892-138">Appuyez sur **NOUVEAU** en haut à gauche du portail</span><span class="sxs-lookup"><span data-stu-id="3a892-138">TAP **NEW** at the top left of the Portal</span></span>
3. <span data-ttu-id="3a892-139">Appuyez sur **Web + Mobile** > **Application Web**</span><span class="sxs-lookup"><span data-stu-id="3a892-139">TAP **Web + Mobile** > **Web App**</span></span>

    ![Portail Microsoft Azure : Nouveau bouton Web + Mobile sous Place de marché - Bouton Application Web sous Applications à la une](azure-continuous-deployment/_static/05-azure-newwebapp.png)

4.  <span data-ttu-id="3a892-141">Dans le panneau **Application web**, entrez une valeur unique pour le **Nom App Service**.</span><span class="sxs-lookup"><span data-stu-id="3a892-141">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

    ![Panneau Application web](azure-continuous-deployment/_static/06-azure-newappblade.png)

    >[!NOTE]
    ><span data-ttu-id="3a892-143">Le nom dans **Nom App Service** doit être unique.</span><span class="sxs-lookup"><span data-stu-id="3a892-143">The **App Service Name** name needs to be unique.</span></span> <span data-ttu-id="3a892-144">Le portail applique cette règle quand vous essayez d’entrer le nom.</span><span class="sxs-lookup"><span data-stu-id="3a892-144">The portal will enforce this rule when you attempt to enter the name.</span></span> <span data-ttu-id="3a892-145">Si vous avez entré une valeur différente, vous devez substituer cette valeur pour chaque occurrence de **SampleWebAppDemo** que vous voyez dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="3a892-145">After you enter a different value, you'll need to substitute that value for each occurrence of **SampleWebAppDemo** that you see in this tutorial.</span></span>

    &nbsp;
    
    <span data-ttu-id="3a892-146">Dans le panneau **Application web**, sélectionnez un **Plan/emplacement App Service** existant ou créez-en un.</span><span class="sxs-lookup"><span data-stu-id="3a892-146">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="3a892-147">Si vous créez un plan, sélectionnez le niveau tarifaire, l’emplacement et les autres options.</span><span class="sxs-lookup"><span data-stu-id="3a892-147">If you create a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="3a892-148">Pour plus d’informations sur les plans App Service, consultez [Présentation détaillée des plans Azure App Service](https://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/).</span><span class="sxs-lookup"><span data-stu-id="3a892-148">For more information on App Service plans, [Azure App Service plans in-depth overview](https://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/).</span></span>

5.  <span data-ttu-id="3a892-149">Cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="3a892-149">Click **Create**.</span></span> <span data-ttu-id="3a892-150">Azure approvisionne et démarre votre application web.</span><span class="sxs-lookup"><span data-stu-id="3a892-150">Azure will provision and start your web app.</span></span>

    ![Portail Azure : Panneau Exemple d’application web Demo 01 Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="3a892-152">Activer la publication Git pour la nouvelle application web</span><span class="sxs-lookup"><span data-stu-id="3a892-152">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="3a892-153">GIT est un système de gestion de versions distribué que vous pouvez utiliser pour déployer votre application web Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="3a892-153">Git is a distributed version control system that you can use to deploy your Azure App Service web app.</span></span> <span data-ttu-id="3a892-154">Vous stockez le code que vous écrivez pour votre application web dans un dépôt Git local et vous déployez votre code sur Azure en publiant sur un dépôt distant.</span><span class="sxs-lookup"><span data-stu-id="3a892-154">You'll store the code you write for your web app in a local Git repository, and you'll deploy your code to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="3a892-155">Connectez-vous au [portail Azure](https://portal.azure.com) si vous n’y êtes pas déjà connecté.</span><span class="sxs-lookup"><span data-stu-id="3a892-155">Log into the [Azure Portal](https://portal.azure.com), if you're not already logged in.</span></span>

2. <span data-ttu-id="3a892-156">Cliquez sur **App Services** pour afficher la liste des services App Services associées à votre abonnement Azure.</span><span class="sxs-lookup"><span data-stu-id="3a892-156">Click **App Services** to view a list of the app services associated with your Azure subscription.</span></span>

3. <span data-ttu-id="3a892-157">Sélectionnez l’application web que vous avez créée dans la section précédente de ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="3a892-157">Select the web app you created in the previous section of this tutorial.</span></span>

4. <span data-ttu-id="3a892-158">Dans le panneau **Déploiement**, sélectionnez **Options de déploiement** > **Choisir une source** > **Référentiel Git local**.</span><span class="sxs-lookup"><span data-stu-id="3a892-158">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Panneau Paramètres : Panneau Source de déploiement : Panneau Choisir une source](azure-continuous-deployment/_static/deployment-options.png)

5. <span data-ttu-id="3a892-160">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="3a892-160">Click **OK**.</span></span>

6. <span data-ttu-id="3a892-161">Si vous n’avez pas déjà configuré les informations d’identification de déploiement pour la publication d’une application web ou d’une autre application App Service, vous pouvez les configurer maintenant :</span><span class="sxs-lookup"><span data-stu-id="3a892-161">If you have not previously set up deployment credentials for publishing a web app or other App Service app, set them up now:</span></span>

   * <span data-ttu-id="3a892-162">Cliquez sur **Paramètres** > **Informations d’identification de déploiement**.</span><span class="sxs-lookup"><span data-stu-id="3a892-162">Click **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="3a892-163">Le panneau **Définir les informations d’identification de déploiement** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="3a892-163">The **Set deployment credentials** blade will be displayed.</span></span>

   * <span data-ttu-id="3a892-164">Créez un nom d’utilisateur et un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="3a892-164">Create a user name and password.</span></span>  <span data-ttu-id="3a892-165">Vous aurez besoin de ce mot de passe plus tard lors de la configuration de Git.</span><span class="sxs-lookup"><span data-stu-id="3a892-165">You'll need this password later when setting up Git.</span></span>

   * <span data-ttu-id="3a892-166">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="3a892-166">Click **Save**.</span></span>

7. <span data-ttu-id="3a892-167">Dans le panneau **Application web**, cliquez sur **Paramètres** > **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="3a892-167">In the **Web App** blade, click **Settings** > **Properties**.</span></span> <span data-ttu-id="3a892-168">L’URL du dépôt Git distant sur lequel vous allez déployer est affiché sous **URL Git**.</span><span class="sxs-lookup"><span data-stu-id="3a892-168">The URL of the remote Git repository that you'll deploy to is shown under **GIT URL**.</span></span>

8. <span data-ttu-id="3a892-169">Copiez la valeur de **URL Git** pour l’utiliser plus tard dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="3a892-169">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Portail Azure : Panneau Propriétés de l’application](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a><span data-ttu-id="3a892-171">Publier votre application web sur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="3a892-171">Publish your web app to Azure App Service</span></span>

<span data-ttu-id="3a892-172">Dans cette section, vous créez un dépôt Git local à l’aide de Visual Studio, et vous envoyez (push) de ce dépôt vers Azure pour déployer votre application web.</span><span class="sxs-lookup"><span data-stu-id="3a892-172">In this section, you will create a local Git repository using Visual Studio and push from that repository to Azure to deploy your web app.</span></span> <span data-ttu-id="3a892-173">Les étapes impliquées sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="3a892-173">The steps involved include the following:</span></span>

   * <span data-ttu-id="3a892-174">Ajoutez le paramètre de dépôt distant en utilisant la valeur de votre URL Git, afin de pouvoir déployer votre dépôt sur Azure.</span><span class="sxs-lookup"><span data-stu-id="3a892-174">Add the remote repository setting using your GIT URL value, so you can deploy your local repository to Azure.</span></span>

   * <span data-ttu-id="3a892-175">Validez les modifications de votre projet.</span><span class="sxs-lookup"><span data-stu-id="3a892-175">Commit your project changes.</span></span>

   * <span data-ttu-id="3a892-176">Envoyez (push) les modifications de votre projet depuis votre dépôt local vers votre dépôt distant sur Azure.</span><span class="sxs-lookup"><span data-stu-id="3a892-176">Push your project changes from your local repository to your remote repository on Azure.</span></span>

&nbsp;
   
1.  <span data-ttu-id="3a892-177">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Solution 'SampleWebAppDemo'** et sélectionnez **Valider**.</span><span class="sxs-lookup"><span data-stu-id="3a892-177">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="3a892-178">**Team Explorer** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="3a892-178">The **Team Explorer** will be displayed.</span></span>

    ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/10-team-explorer.png)

2.  <span data-ttu-id="3a892-180">Dans **Team Explorer**, sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres du dépôt**.</span><span class="sxs-lookup"><span data-stu-id="3a892-180">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

3.  <span data-ttu-id="3a892-181">Dans la section **Distants** des **Paramètres du dépôt**, sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="3a892-181">In the **Remotes** section of the **Repository Settings** select **Add**.</span></span> <span data-ttu-id="3a892-182">La boîte de dialogue **Ajouter un élément distant** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="3a892-182">The **Add Remote** dialog box will be displayed.</span></span>

4.  <span data-ttu-id="3a892-183">Définissez le **Nom** de l’élément distant sur **Azure-SampleApp**.</span><span class="sxs-lookup"><span data-stu-id="3a892-183">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

5.  <span data-ttu-id="3a892-184">Définissez la valeur de **Récupérer** sur **l’URL Git** que vous avez précédemment copiée depuis Azure dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="3a892-184">Set the value for **Fetch** to the **Git URL** that you copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="3a892-185">Notez qu’il s’agit de l’URL qui se termine par **.git**.</span><span class="sxs-lookup"><span data-stu-id="3a892-185">Note that this is the URL that ends with **.git**.</span></span>

    ![Boîte de dialogue Modifier un élément distant](azure-continuous-deployment/_static/11-add-remote.png)

    >[!NOTE]
    ><span data-ttu-id="3a892-187">Vous pouvez aussi spécifier le dépôt distant à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire de votre projet et en entrant la commande.</span><span class="sxs-lookup"><span data-stu-id="3a892-187">As an alternative, you can specify the remote repository from the **Command Window** by opening the **Command Window**, changing to your project directory, and entering the command.</span></span> <span data-ttu-id="3a892-188">Par exemple :`git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`</span><span class="sxs-lookup"><span data-stu-id="3a892-188">For example:`git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`</span></span>

6.  <span data-ttu-id="3a892-189">Sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres globaux**.</span><span class="sxs-lookup"><span data-stu-id="3a892-189">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="3a892-190">Vérifiez que votre nom et votre adresse e-mail sont définis.</span><span class="sxs-lookup"><span data-stu-id="3a892-190">Make sure you have your name and your email address set.</span></span> <span data-ttu-id="3a892-191">Il peut aussi être nécessaire de sélectionner **Mettre à jour**.</span><span class="sxs-lookup"><span data-stu-id="3a892-191">You may also need to select **Update**.</span></span>

7.  <span data-ttu-id="3a892-192">Sélectionnez **Accueil** > **Modifications** pour revenir à la vue **Modifications**.</span><span class="sxs-lookup"><span data-stu-id="3a892-192">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

8.  <span data-ttu-id="3a892-193">Entrez un message de validation, comme **Initial Push #1**, et cliquez sur **Valider**.</span><span class="sxs-lookup"><span data-stu-id="3a892-193">Enter a commit message, such as **Initial Push #1** and click **Commit**.</span></span> <span data-ttu-id="3a892-194">Cette action crée une *validation* localement.</span><span class="sxs-lookup"><span data-stu-id="3a892-194">This action will create a *commit* locally.</span></span> <span data-ttu-id="3a892-195">Ensuite, vous devez vous *synchroniser* avec Azure.</span><span class="sxs-lookup"><span data-stu-id="3a892-195">Next, you need to *sync* with Azure.</span></span>

    ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/12-initial-commit.png)

    >[!NOTE]
    ><span data-ttu-id="3a892-197">Vous pouvez aussi valider vos modifications à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire de votre projet et en entrant les commandes Git.</span><span class="sxs-lookup"><span data-stu-id="3a892-197">As an alternative, you can commit your changes from the **Command Window** by opening the **Command Window**, changing to your project directory, and entering the git commands.</span></span> <span data-ttu-id="3a892-198">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3a892-198">For example:</span></span>
    >
    >`git add .`
    >
    >`git commit -am "Initial Push #1"`

9.  <span data-ttu-id="3a892-199">Sélectionnez **Accueil** > **Synchroniser** > **Actions** > **Ouvrir l’invite de commandes**.</span><span class="sxs-lookup"><span data-stu-id="3a892-199">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="3a892-200">L’invite de commandes s’ouvre sur le répertoire de votre projet.</span><span class="sxs-lookup"><span data-stu-id="3a892-200">The command prompt will open to your project directory.</span></span>

10.  <span data-ttu-id="3a892-201">Entrez la commande suivante dans la fenêtre Commande :</span><span class="sxs-lookup"><span data-stu-id="3a892-201">Enter the following command in the command window:</span></span>

    `git push -u Azure-SampleApp master`

11.  <span data-ttu-id="3a892-202">Entrez votre mot de passe des **informations d’identification de déploiement** Azure que vous avez créé précédemment dans Azure.</span><span class="sxs-lookup"><span data-stu-id="3a892-202">Enter your Azure **deployment credentials** password that you created earlier in Azure.</span></span>

    >[!NOTE]
    ><span data-ttu-id="3a892-203">Votre mot de passe n’est pas visible quand vous l’entrez.</span><span class="sxs-lookup"><span data-stu-id="3a892-203">Your password will not be visible as you enter it.</span></span>

    <span data-ttu-id="3a892-204">Cette commande démarre le processus d’envoi (push) de vos fichiers de projet locaux vers Azure.</span><span class="sxs-lookup"><span data-stu-id="3a892-204">This command will start the process of pushing your local project files to Azure.</span></span> <span data-ttu-id="3a892-205">La sortie de la commande ci-dessus se termine par un message indiquant que le déploiement a réussi.</span><span class="sxs-lookup"><span data-stu-id="3a892-205">The output from the above command ends with a message that deployment was successful.</span></span>
        
    ```
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
    * [new branch]      master -> master
    Branch master set up to track remote branch master from Azure-SampleApp.
    ```
    > [!NOTE]
    > <span data-ttu-id="3a892-206">Si vous avez besoin de collaborer sur un projet, vous devez envisager d’envoyer (push) à [GitHub](https://github.com) entre les envois (push) à Azure.</span><span class="sxs-lookup"><span data-stu-id="3a892-206">If you need to collaborate on a project, you should consider pushing to [GitHub](https://github.com) in between pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="3a892-207">Vérifier le déploiement actif</span><span class="sxs-lookup"><span data-stu-id="3a892-207">Verify the Active Deployment</span></span>

<span data-ttu-id="3a892-208">Vous pouvez vérifier que vous avez transféré correctement l’application web depuis votre environnement local vers Azure.</span><span class="sxs-lookup"><span data-stu-id="3a892-208">You can verify that you successfully transferred the web app from your local environment to Azure.</span></span> <span data-ttu-id="3a892-209">Vous voyez dans la liste le déploiement réussi.</span><span class="sxs-lookup"><span data-stu-id="3a892-209">You'll see the listed successful deployment.</span></span>

1. <span data-ttu-id="3a892-210">Dans le [portail Azure](https://portal.azure.com), sélectionnez votre application web.</span><span class="sxs-lookup"><span data-stu-id="3a892-210">In the [Azure Portal](https://portal.azure.com), select your web app.</span></span> <span data-ttu-id="3a892-211">Ensuite, sélectionnez **Déploiement** > **Options de déploiement**.</span><span class="sxs-lookup"><span data-stu-id="3a892-211">Then, select **Deployment** > **Deployment options**.</span></span>

   ![Portail Azure : Panneau Paramètres : Panneau Déploiements montrant la réussite du déploiement](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="3a892-213">Exécuter l’application dans Azure</span><span class="sxs-lookup"><span data-stu-id="3a892-213">Run the app in Azure</span></span>

<span data-ttu-id="3a892-214">Maintenant que vous avez déployé votre application web sur Azure, vous pouvez l’exécuter.</span><span class="sxs-lookup"><span data-stu-id="3a892-214">Now that you have deployed your web app to Azure, you can run the app.</span></span>

<span data-ttu-id="3a892-215">Vous pouvez le faire de deux façons :</span><span class="sxs-lookup"><span data-stu-id="3a892-215">This can be done in two ways:</span></span>

* <span data-ttu-id="3a892-216">Dans le portail Azure, recherchez le panneau Application web pour votre application, et cliquez sur **Parcourir** pour afficher votre application dans votre navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="3a892-216">In the Azure Portal, locate the web app blade for your web app, and click **Browse** to view your app in your default browser.</span></span>

* <span data-ttu-id="3a892-217">Ouvrez un navigateur et entrez l’URL de votre application web.</span><span class="sxs-lookup"><span data-stu-id="3a892-217">Open a browser and enter the URL for your web app.</span></span> <span data-ttu-id="3a892-218">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3a892-218">For example:</span></span>

  `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-your-web-app-and-republish"></a><span data-ttu-id="3a892-219">Mettre à jour votre application web et la republier</span><span class="sxs-lookup"><span data-stu-id="3a892-219">Update your web app and republish</span></span>

<span data-ttu-id="3a892-220">Après avoir apporté des modifications à votre code local, vous pouvez republier.</span><span class="sxs-lookup"><span data-stu-id="3a892-220">After you make changes to your local code, you can republish.</span></span>

1.  <span data-ttu-id="3a892-221">Dans **l’Explorateur de solutions** de Visual Studio, ouvrez le fichier *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3a892-221">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

2.  <span data-ttu-id="3a892-222">Dans la méthode `Configure`, modifiez la méthode `Response.WriteAsync` comme suit :</span><span class="sxs-lookup"><span data-stu-id="3a892-222">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

    ```aspx-cs
    await context.Response.WriteAsync("Hello World! Deploy to Azure.");
    ```
3.  <span data-ttu-id="3a892-223">Enregistrer les modifications de *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3a892-223">Save changes to *Startup.cs*.</span></span>

4.  <span data-ttu-id="3a892-224">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Solution 'SampleWebAppDemo'** et sélectionnez **Valider**.</span><span class="sxs-lookup"><span data-stu-id="3a892-224">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="3a892-225">**Team Explorer** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="3a892-225">The **Team Explorer** will be displayed.</span></span>

5.  <span data-ttu-id="3a892-226">Entrez un message de validation, comme:</span><span class="sxs-lookup"><span data-stu-id="3a892-226">Enter a commit message, such as:</span></span>

    ```none
    Update #2
    ```

6.  <span data-ttu-id="3a892-227">Appuyez sur le bouton **Valider** pour valider les modifications du projet.</span><span class="sxs-lookup"><span data-stu-id="3a892-227">Press the **Commit** button to commit the project changes.</span></span>

7.  <span data-ttu-id="3a892-228">Sélectionnez **Accueil** > **Synchroniser** > **Actions** > **Envoyer (push)**.</span><span class="sxs-lookup"><span data-stu-id="3a892-228">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

>[!NOTE]
><span data-ttu-id="3a892-229">Vous pouvez aussi envoyer (push) vos modifications à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire de votre projet et en entrant une commande Git.</span><span class="sxs-lookup"><span data-stu-id="3a892-229">As an alternative, you can push your changes from the **Command Window** by opening the **Command Window**, changing to your project directory, and entering a git command.</span></span> <span data-ttu-id="3a892-230">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3a892-230">For example:</span></span>
>
>`git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="3a892-231">Afficher l’application web mise à jour dans Azure</span><span class="sxs-lookup"><span data-stu-id="3a892-231">View the updated web app in Azure</span></span>

<span data-ttu-id="3a892-232">Affichez votre application web mise à jour en sélectionnant **Parcourir** dans le panneau Application web du portail Azure, ou en ouvrant un navigateur et en entrant l’URL de votre application web.</span><span class="sxs-lookup"><span data-stu-id="3a892-232">View your updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for your web app.</span></span> <span data-ttu-id="3a892-233">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3a892-233">For example:</span></span>

   `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a><span data-ttu-id="3a892-234">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3a892-234">Additional Resources</span></span>

* [<span data-ttu-id="3a892-235">Publication et déploiement</span><span class="sxs-lookup"><span data-stu-id="3a892-235">Publishing and Deployment</span></span>](index.md)

* [<span data-ttu-id="3a892-236">Projet Kudu</span><span class="sxs-lookup"><span data-stu-id="3a892-236">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)

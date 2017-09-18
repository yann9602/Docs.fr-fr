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
ms.openlocfilehash: a9efad38b1c75bd3a186b4ec85861357ecf744b9
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="continuous-deployment-to-azure-for-aspnet-core-with-visual-studio-and-git"></a><span data-ttu-id="02c56-104">Déploiement continu sur Azure pour ASP.NET Core, avec Visual Studio et Git</span><span class="sxs-lookup"><span data-stu-id="02c56-104">Continuous deployment to Azure for ASP.NET Core, with Visual Studio and Git</span></span>

<span data-ttu-id="02c56-105">Par [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="02c56-105">By [Erik Reitan](https://github.com/Erikre)</span></span>

<span data-ttu-id="02c56-106">Ce didacticiel vous montre comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer à partir de Visual Studio sur Azure App Service en utilisant le déploiement continu.</span><span class="sxs-lookup"><span data-stu-id="02c56-106">This tutorial shows you how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span> 

<span data-ttu-id="02c56-107">Consultez aussi [Use VSTS to Build and Publish to an Azure Web App with Continuous Deployment](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic), qui montre comment configurer un workflow de livraison continue pour [Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/) en utilisant Visual Studio Team Services.</span><span class="sxs-lookup"><span data-stu-id="02c56-107">See also [Use VSTS to Build and Publish to an Azure Web App with Continuous Deployment](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/) using Visual Studio Team Services.</span></span> <span data-ttu-id="02c56-108">La livraison continue Azure dans Team Services simplifie la configuration d’un pipeline de déploiement fiable pour publier les mises à jour de votre application dans Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="02c56-108">Azure Continuous Delivery in Team Services simplifies setting up a robust deployment pipeline to publish updates for your app to Azure App Service.</span></span> <span data-ttu-id="02c56-109">Le pipeline peut être configuré à partir du portail Azure pour générer, exécuter des tests, déployer sur un emplacement de préproduction, puis déployer en production.</span><span class="sxs-lookup"><span data-stu-id="02c56-109">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot,  and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="02c56-110">Pour effectuer ce didacticiel, vous avez besoin d’un compte Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="02c56-110">To complete this tutorial, you need a Microsoft Azure account.</span></span> <span data-ttu-id="02c56-111">Si vous n’avez pas de compte, vous pouvez [activer vos avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [vous inscrire pour une évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="02c56-111">If you don't have an account, you can [activate your MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="02c56-112">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="02c56-112">Prerequisites</span></span>

<span data-ttu-id="02c56-113">Ce didacticiel suppose que vous avez déjà installé les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="02c56-113">This tutorial assumes you have already installed the following:</span></span>

* [<span data-ttu-id="02c56-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c56-114">Visual Studio</span></span>](https://www.visualstudio.com)

* <span data-ttu-id="02c56-115">[ASP.NET Core](https://download.microsoft.com/download/F/6/E/F6ECBBCC-B02F-424E-8E03-D47E9FA631B7/DotNetCore.1.0.1-VS2015Tools.Preview2.0.3.exe) (runtime et outils)</span><span class="sxs-lookup"><span data-stu-id="02c56-115">[ASP.NET Core](https://download.microsoft.com/download/F/6/E/F6ECBBCC-B02F-424E-8E03-D47E9FA631B7/DotNetCore.1.0.1-VS2015Tools.Preview2.0.3.exe) (runtime and tooling)</span></span>

* <span data-ttu-id="02c56-116">[Git](https://git-scm.com/downloads) pour Windows</span><span class="sxs-lookup"><span data-stu-id="02c56-116">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="02c56-117">Créer une application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="02c56-117">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="02c56-118">Démarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="02c56-118">Start Visual Studio.</span></span>

2. <span data-ttu-id="02c56-119">Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="02c56-119">From the **File** menu, select **New** > **Project**.</span></span>

3. <span data-ttu-id="02c56-120">Sélectionnez le modèle de projet **Application web ASP.NET**.</span><span class="sxs-lookup"><span data-stu-id="02c56-120">Select the **ASP.NET Web Application** project template.</span></span> <span data-ttu-id="02c56-121">Elle apparaît sous **Installé** > **Modèles** > **Visual C#** > **Web**.</span><span class="sxs-lookup"><span data-stu-id="02c56-121">It appears under **Installed** > **Templates** > **Visual C#** > **Web**.</span></span> <span data-ttu-id="02c56-122">Attribuez un nom au projet `SampleWebAppDemo`.</span><span class="sxs-lookup"><span data-stu-id="02c56-122">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="02c56-123">Sélectionnez l’option **Créer un dépôt Git**, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="02c56-123">Select the **Create new Git respository** option and click **OK**.</span></span>

   ![Boîte de dialogue Nouveau projet](azure-continuous-deployment/_static/01-new-project.png)

4. <span data-ttu-id="02c56-125">Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle ASP.NET Core **Vide** puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="02c56-125">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Boîte de dialogue Nouveau projet ASP.NET](azure-continuous-deployment/_static/02-web-site-template.png)


### <a name="running-the-web-app-locally"></a><span data-ttu-id="02c56-127">Exécution de l’application web localement</span><span class="sxs-lookup"><span data-stu-id="02c56-127">Running the web app locally</span></span>

1. <span data-ttu-id="02c56-128">Une fois que Visual Studio a terminé la création de l’application, exécutez l’application en sélectionnant **Déboguer** -> **Démarrer le débogage**.</span><span class="sxs-lookup"><span data-stu-id="02c56-128">Once Visual Studio finishes creating the app, run the app by selecting **Debug** -> **Start Debugging**.</span></span> <span data-ttu-id="02c56-129">Vous pouvez aussi appuyer sur **F5**.</span><span class="sxs-lookup"><span data-stu-id="02c56-129">As an alternative, you can press **F5**.</span></span>

   <span data-ttu-id="02c56-130">L’initialisation de Visual Studio et de la nouvelle application peut prendre un certain temps.</span><span class="sxs-lookup"><span data-stu-id="02c56-130">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="02c56-131">Une fois qu’elle est terminée, le navigateur affiche l’application en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="02c56-131">Once it is complete, the browser will show the running app.</span></span>

   ![Fenêtre de navigateur montrant l’application en cours d’exécution qui affiche « Hello World! »](azure-continuous-deployment/_static/04-browser-runapp.png)

2. <span data-ttu-id="02c56-133">Après avoir examiné l’application web en cours d’exécution, fermez le navigateur et cliquez sur l’icône « Arrêter le débogage » dans la barre d’outils de Visual Studio pour arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="02c56-133">After reviewing the running Web app, close the browser and click the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="02c56-134">Créer une application web dans le portail Azure</span><span class="sxs-lookup"><span data-stu-id="02c56-134">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="02c56-135">Les étapes suivantes vous guident dans la création d’une application web dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="02c56-135">The following steps will guide you through creating a web app in the Azure Portal.</span></span>

1. <span data-ttu-id="02c56-136">Connectez-vous au [portail Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="02c56-136">Log in to the [Azure Portal](https://portal.azure.com)</span></span>
2. <span data-ttu-id="02c56-137">Appuyez sur **NOUVEAU** en haut à gauche du portail</span><span class="sxs-lookup"><span data-stu-id="02c56-137">TAP **NEW** at the top left of the Portal</span></span>
3. <span data-ttu-id="02c56-138">Appuyez sur **Web + Mobile** > **Application Web**</span><span class="sxs-lookup"><span data-stu-id="02c56-138">TAP **Web + Mobile** > **Web App**</span></span>

    ![Portail Microsoft Azure : Nouveau bouton Web + Mobile sous Place de marché - Bouton Application Web sous Applications à la une](azure-continuous-deployment/_static/05-azure-newwebapp.png)

4.  <span data-ttu-id="02c56-140">Dans le panneau **Application web**, entrez une valeur unique pour le **Nom App Service**.</span><span class="sxs-lookup"><span data-stu-id="02c56-140">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

    ![Panneau Application web](azure-continuous-deployment/_static/06-azure-newappblade.png)

    >[!NOTE]
    ><span data-ttu-id="02c56-142">Le nom dans **Nom App Service** doit être unique.</span><span class="sxs-lookup"><span data-stu-id="02c56-142">The **App Service Name** name needs to be unique.</span></span> <span data-ttu-id="02c56-143">Le portail applique cette règle quand vous essayez d’entrer le nom.</span><span class="sxs-lookup"><span data-stu-id="02c56-143">The portal will enforce this rule when you attempt to enter the name.</span></span> <span data-ttu-id="02c56-144">Si vous avez entré une valeur différente, vous devez substituer cette valeur pour chaque occurrence de **SampleWebAppDemo** que vous voyez dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="02c56-144">After you enter a different value, you'll need to substitute that value for each occurrence of **SampleWebAppDemo** that you see in this tutorial.</span></span>

    &nbsp;
    
    <span data-ttu-id="02c56-145">Dans le panneau **Application web**, sélectionnez un **Plan/emplacement App Service** existant ou créez-en un.</span><span class="sxs-lookup"><span data-stu-id="02c56-145">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="02c56-146">Si vous créez un plan, sélectionnez le niveau tarifaire, l’emplacement et les autres options.</span><span class="sxs-lookup"><span data-stu-id="02c56-146">If you create a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="02c56-147">Pour plus d’informations sur les plans App Service, consultez [Présentation détaillée des plans Azure App Service](https://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/).</span><span class="sxs-lookup"><span data-stu-id="02c56-147">For more information on App Service plans, [Azure App Service plans in-depth overview](https://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/).</span></span>

5.  <span data-ttu-id="02c56-148">Cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="02c56-148">Click **Create**.</span></span> <span data-ttu-id="02c56-149">Azure approvisionne et démarre votre application web.</span><span class="sxs-lookup"><span data-stu-id="02c56-149">Azure will provision and start your web app.</span></span>

    ![Portail Azure : Panneau Exemple d’application web Demo 01 Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="02c56-151">Activer la publication Git pour la nouvelle application web</span><span class="sxs-lookup"><span data-stu-id="02c56-151">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="02c56-152">GIT est un système de gestion de versions distribué que vous pouvez utiliser pour déployer votre application web Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="02c56-152">Git is a distributed version control system that you can use to deploy your Azure App Service web app.</span></span> <span data-ttu-id="02c56-153">Vous stockez le code que vous écrivez pour votre application web dans un dépôt Git local et vous déployez votre code sur Azure en publiant sur un dépôt distant.</span><span class="sxs-lookup"><span data-stu-id="02c56-153">You'll store the code you write for your web app in a local Git repository, and you'll deploy your code to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="02c56-154">Connectez-vous au [portail Azure](https://portal.azure.com) si vous n’y êtes pas déjà connecté.</span><span class="sxs-lookup"><span data-stu-id="02c56-154">Log into the [Azure Portal](https://portal.azure.com), if you're not already logged in.</span></span>

2. <span data-ttu-id="02c56-155">Cliquez sur **Parcourir**, qui se trouve dans le bas du volet de navigation.</span><span class="sxs-lookup"><span data-stu-id="02c56-155">Click **Browse**, located at the bottom of the navigation pane.</span></span>

3. <span data-ttu-id="02c56-156">Cliquez sur **Applications web** pour afficher la liste des applications web associées à votre abonnement Azure.</span><span class="sxs-lookup"><span data-stu-id="02c56-156">Click **Web Apps** to view a list of the web apps associated with your Azure subscription.</span></span>

4. <span data-ttu-id="02c56-157">Sélectionnez l’application web que vous avez créée dans la section précédente de ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="02c56-157">Select the web app you created in the previous section of this tutorial.</span></span>

5. <span data-ttu-id="02c56-158">Si le panneau **Paramètres** n’est pas affiché, sélectionnez **Paramètres** dans le panneau **Application web**.</span><span class="sxs-lookup"><span data-stu-id="02c56-158">If the **Settings** blade is not shown, select **Settings** in the **Web App** blade.</span></span>

6. <span data-ttu-id="02c56-159">Dans le panneau **Paramètres**, sélectionnez **Source de déploiement** > **Choisir une source** > **Dépôt Git local**.</span><span class="sxs-lookup"><span data-stu-id="02c56-159">In the **Settings** blade, select **Deployment source** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Panneau Paramètres : Panneau Source de déploiement : Panneau Choisir une source](azure-continuous-deployment/_static/08-azure-localrepository.png)

7. <span data-ttu-id="02c56-161">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="02c56-161">Click **OK**.</span></span>

8. <span data-ttu-id="02c56-162">Si vous n’avez pas déjà configuré les informations d’identification de déploiement pour la publication d’une application web ou d’une autre application App Service, vous pouvez les configurer maintenant :</span><span class="sxs-lookup"><span data-stu-id="02c56-162">If you have not previously set up deployment credentials for publishing a web app or other App Service app, set them up now:</span></span>

   * <span data-ttu-id="02c56-163">Cliquez sur **Paramètres** > **Informations d’identification de déploiement**.</span><span class="sxs-lookup"><span data-stu-id="02c56-163">Click **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="02c56-164">Le panneau **Définir les informations d’identification de déploiement** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="02c56-164">The **Set deployment credentials** blade will be displayed.</span></span>

   * <span data-ttu-id="02c56-165">Créez un nom d’utilisateur et un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="02c56-165">Create a user name and password.</span></span>  <span data-ttu-id="02c56-166">Vous aurez besoin de ce mot de passe plus tard lors de la configuration de Git.</span><span class="sxs-lookup"><span data-stu-id="02c56-166">You'll need this password later when setting up Git.</span></span>

   * <span data-ttu-id="02c56-167">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="02c56-167">Click **Save**.</span></span>

9. <span data-ttu-id="02c56-168">Dans le panneau **Application web**, cliquez sur **Paramètres** > **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="02c56-168">In the **Web App** blade, click **Settings** > **Properties**.</span></span> <span data-ttu-id="02c56-169">L’URL du dépôt Git distant sur lequel vous allez déployer est affiché sous **URL Git**.</span><span class="sxs-lookup"><span data-stu-id="02c56-169">The URL of the remote Git repository that you'll deploy to is shown under **GIT URL**.</span></span>

10. <span data-ttu-id="02c56-170">Copiez la valeur de **URL Git** pour l’utiliser plus tard dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="02c56-170">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Portail Azure : Panneau Propriétés de l’application](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a><span data-ttu-id="02c56-172">Publier votre application web sur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="02c56-172">Publish your web app to Azure App Service</span></span>

<span data-ttu-id="02c56-173">Dans cette section, vous créez un dépôt Git local à l’aide de Visual Studio, et vous envoyez (push) de ce dépôt vers Azure pour déployer votre application web.</span><span class="sxs-lookup"><span data-stu-id="02c56-173">In this section, you will create a local Git repository using Visual Studio and push from that repository to Azure to deploy your web app.</span></span> <span data-ttu-id="02c56-174">Les étapes impliquées sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="02c56-174">The steps involved include the following:</span></span>

   * <span data-ttu-id="02c56-175">Ajoutez le paramètre de dépôt distant en utilisant la valeur de votre URL Git, afin de pouvoir déployer votre dépôt sur Azure.</span><span class="sxs-lookup"><span data-stu-id="02c56-175">Add the remote repository setting using your GIT URL value, so you can deploy your local repository to Azure.</span></span>

   * <span data-ttu-id="02c56-176">Validez les modifications de votre projet.</span><span class="sxs-lookup"><span data-stu-id="02c56-176">Commit your project changes.</span></span>

   * <span data-ttu-id="02c56-177">Envoyez (push) les modifications de votre projet depuis votre dépôt local vers votre dépôt distant sur Azure.</span><span class="sxs-lookup"><span data-stu-id="02c56-177">Push your project changes from your local repository to your remote repository on Azure.</span></span>

&nbsp;
   
1.  <span data-ttu-id="02c56-178">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Solution 'SampleWebAppDemo'** et sélectionnez **Valider**.</span><span class="sxs-lookup"><span data-stu-id="02c56-178">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="02c56-179">**Team Explorer** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="02c56-179">The **Team Explorer** will be displayed.</span></span>

    ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/10-team-explorer.png)

2.  <span data-ttu-id="02c56-181">Dans **Team Explorer**, sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres du dépôt**.</span><span class="sxs-lookup"><span data-stu-id="02c56-181">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

3.  <span data-ttu-id="02c56-182">Dans la section **Distants** des **Paramètres du dépôt**, sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="02c56-182">In the **Remotes** section of the **Repository Settings** select **Add**.</span></span> <span data-ttu-id="02c56-183">La boîte de dialogue **Ajouter un élément distant** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="02c56-183">The **Add Remote** dialog box will be displayed.</span></span>

4.  <span data-ttu-id="02c56-184">Définissez le **Nom** de l’élément distant sur **Azure-SampleApp**.</span><span class="sxs-lookup"><span data-stu-id="02c56-184">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

5.  <span data-ttu-id="02c56-185">Définissez la valeur de **Récupérer** sur **l’URL Git** que vous avez précédemment copiée depuis Azure dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="02c56-185">Set the value for **Fetch** to the **Git URL** that you copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="02c56-186">Notez qu’il s’agit de l’URL qui se termine par **.git**.</span><span class="sxs-lookup"><span data-stu-id="02c56-186">Note that this is the URL that ends with **.git**.</span></span>

    ![Boîte de dialogue Modifier un élément distant](azure-continuous-deployment/_static/11-add-remote.png)

    >[!NOTE]
    ><span data-ttu-id="02c56-188">Vous pouvez aussi spécifier le dépôt distant à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire de votre projet et en entrant la commande.</span><span class="sxs-lookup"><span data-stu-id="02c56-188">As an alternative, you can specify the remote repository from the **Command Window** by opening the **Command Window**, changing to your project directory, and entering the command.</span></span> <span data-ttu-id="02c56-189">Par exemple :`git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`</span><span class="sxs-lookup"><span data-stu-id="02c56-189">For example:`git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`</span></span>

6.  <span data-ttu-id="02c56-190">Sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres globaux**.</span><span class="sxs-lookup"><span data-stu-id="02c56-190">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="02c56-191">Vérifiez que votre nom et votre adresse e-mail sont définis.</span><span class="sxs-lookup"><span data-stu-id="02c56-191">Make sure you have your name and your email address set.</span></span> <span data-ttu-id="02c56-192">Il peut aussi être nécessaire de sélectionner **Mettre à jour**.</span><span class="sxs-lookup"><span data-stu-id="02c56-192">You may also need to select **Update**.</span></span>

7.  <span data-ttu-id="02c56-193">Sélectionnez **Accueil** > **Modifications** pour revenir à la vue **Modifications**.</span><span class="sxs-lookup"><span data-stu-id="02c56-193">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

8.  <span data-ttu-id="02c56-194">Entrez un message de validation, comme **Initial Push #1**, et cliquez sur **Valider**.</span><span class="sxs-lookup"><span data-stu-id="02c56-194">Enter a commit message, such as **Initial Push #1** and click **Commit**.</span></span> <span data-ttu-id="02c56-195">Cette action crée une *validation* localement.</span><span class="sxs-lookup"><span data-stu-id="02c56-195">This action will create a *commit* locally.</span></span> <span data-ttu-id="02c56-196">Ensuite, vous devez vous *synchroniser* avec Azure.</span><span class="sxs-lookup"><span data-stu-id="02c56-196">Next, you need to *sync* with Azure.</span></span>

    ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/12-initial-commit.png)

    >[!NOTE]
    ><span data-ttu-id="02c56-198">Vous pouvez aussi valider vos modifications à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire de votre projet et en entrant les commandes Git.</span><span class="sxs-lookup"><span data-stu-id="02c56-198">As an alternative, you can commit your changes from the **Command Window** by opening the **Command Window**, changing to your project directory, and entering the git commands.</span></span> <span data-ttu-id="02c56-199">Exemple :</span><span class="sxs-lookup"><span data-stu-id="02c56-199">For example:</span></span>
    >
    >`git add .`
    >
    >`git commit -am "Initial Push #1"`

9.  <span data-ttu-id="02c56-200">Sélectionnez **Accueil** > **Synchroniser** > **Actions** > **Ouvrir l’invite de commandes**.</span><span class="sxs-lookup"><span data-stu-id="02c56-200">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="02c56-201">L’invite de commandes s’ouvre sur le répertoire de votre projet.</span><span class="sxs-lookup"><span data-stu-id="02c56-201">The command prompt will open to your project directory.</span></span>

10.  <span data-ttu-id="02c56-202">Entrez la commande suivante dans la fenêtre Commande :</span><span class="sxs-lookup"><span data-stu-id="02c56-202">Enter the following command in the command window:</span></span>

    `git push -u Azure-SampleApp master`

11.  <span data-ttu-id="02c56-203">Entrez votre mot de passe des **informations d’identification de déploiement** Azure que vous avez créé précédemment dans Azure.</span><span class="sxs-lookup"><span data-stu-id="02c56-203">Enter your Azure **deployment credentials** password that you created earlier in Azure.</span></span>

    >[!NOTE]
    ><span data-ttu-id="02c56-204">Votre mot de passe n’est pas visible quand vous l’entrez.</span><span class="sxs-lookup"><span data-stu-id="02c56-204">Your password will not be visible as you enter it.</span></span>

    <span data-ttu-id="02c56-205">Cette commande démarre le processus d’envoi (push) de vos fichiers de projet locaux vers Azure.</span><span class="sxs-lookup"><span data-stu-id="02c56-205">This command will start the process of pushing your local project files to Azure.</span></span> <span data-ttu-id="02c56-206">La sortie de la commande ci-dessus se termine par un message indiquant que le déploiement a réussi.</span><span class="sxs-lookup"><span data-stu-id="02c56-206">The output from the above command ends with a message that deployment was successful.</span></span>
        
    ```
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
    * [new branch]      master -> master
    Branch master set up to track remote branch master from Azure-SampleApp.
    ```
    > [!NOTE]
    > <span data-ttu-id="02c56-207">Si vous avez besoin de collaborer sur un projet, vous devez envisager d’envoyer (push) à [GitHub](https://github.com) entre les envois (push) à Azure.</span><span class="sxs-lookup"><span data-stu-id="02c56-207">If you need to collaborate on a project, you should consider pushing to [GitHub](https://github.com) in between pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="02c56-208">Vérifier le déploiement actif</span><span class="sxs-lookup"><span data-stu-id="02c56-208">Verify the Active Deployment</span></span>

<span data-ttu-id="02c56-209">Vous pouvez vérifier que vous avez transféré correctement l’application web depuis votre environnement local vers Azure.</span><span class="sxs-lookup"><span data-stu-id="02c56-209">You can verify that you successfully transferred the web app from your local environment to Azure.</span></span> <span data-ttu-id="02c56-210">Vous voyez dans la liste le déploiement réussi.</span><span class="sxs-lookup"><span data-stu-id="02c56-210">You'll see the listed successful deployment.</span></span>

1. <span data-ttu-id="02c56-211">Dans le [portail Azure](https://portal.azure.com), sélectionnez votre application web.</span><span class="sxs-lookup"><span data-stu-id="02c56-211">In the [Azure Portal](https://portal.azure.com), select your web app.</span></span> <span data-ttu-id="02c56-212">Ensuite, sélectionnez **Paramètres** > **Déploiement continu**.</span><span class="sxs-lookup"><span data-stu-id="02c56-212">Then, select **Settings** > **Continuous deployment**.</span></span>

   ![Portail Azure : Panneau Paramètres : Panneau Déploiements montrant la réussite du déploiement](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="02c56-214">Exécuter l’application dans Azure</span><span class="sxs-lookup"><span data-stu-id="02c56-214">Run the app in Azure</span></span>

<span data-ttu-id="02c56-215">Maintenant que vous avez déployé votre application web sur Azure, vous pouvez l’exécuter.</span><span class="sxs-lookup"><span data-stu-id="02c56-215">Now that you have deployed your web app to Azure, you can run the app.</span></span>

<span data-ttu-id="02c56-216">Vous pouvez le faire de deux façons :</span><span class="sxs-lookup"><span data-stu-id="02c56-216">This can be done in two ways:</span></span>

* <span data-ttu-id="02c56-217">Dans le portail Azure, recherchez le panneau Application web pour votre application, et cliquez sur **Parcourir** pour afficher votre application dans votre navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="02c56-217">In the Azure Portal, locate the web app blade for your web app, and click **Browse** to view your app in your default browser.</span></span>

* <span data-ttu-id="02c56-218">Ouvrez un navigateur et entrez l’URL de votre application web.</span><span class="sxs-lookup"><span data-stu-id="02c56-218">Open a browser and enter the URL for your web app.</span></span> <span data-ttu-id="02c56-219">Exemple :</span><span class="sxs-lookup"><span data-stu-id="02c56-219">For example:</span></span>

  `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-your-web-app-and-republish"></a><span data-ttu-id="02c56-220">Mettre à jour votre application web et la republier</span><span class="sxs-lookup"><span data-stu-id="02c56-220">Update your web app and republish</span></span>

<span data-ttu-id="02c56-221">Après avoir apporté des modifications à votre code local, vous pouvez republier.</span><span class="sxs-lookup"><span data-stu-id="02c56-221">After you make changes to your local code, you can republish.</span></span>

1.  <span data-ttu-id="02c56-222">Dans **l’Explorateur de solutions** de Visual Studio, ouvrez le fichier *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="02c56-222">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

2.  <span data-ttu-id="02c56-223">Dans la méthode `Configure`, modifiez la méthode `Response.WriteAsync` comme suit :</span><span class="sxs-lookup"><span data-stu-id="02c56-223">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

    ```aspx-cs
    await context.Response.WriteAsync("Hello World! Deploy to Azure.");
    ```
3.  <span data-ttu-id="02c56-224">Enregistrer les modifications de *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="02c56-224">Save changes to *Startup.cs*.</span></span>

4.  <span data-ttu-id="02c56-225">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Solution 'SampleWebAppDemo'** et sélectionnez **Valider**.</span><span class="sxs-lookup"><span data-stu-id="02c56-225">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="02c56-226">**Team Explorer** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="02c56-226">The **Team Explorer** will be displayed.</span></span>

5.  <span data-ttu-id="02c56-227">Entrez un message de validation, comme:</span><span class="sxs-lookup"><span data-stu-id="02c56-227">Enter a commit message, such as:</span></span>

    ```none
    Update #2
    ```

6.  <span data-ttu-id="02c56-228">Appuyez sur le bouton **Valider** pour valider les modifications du projet.</span><span class="sxs-lookup"><span data-stu-id="02c56-228">Press the **Commit** button to commit the project changes.</span></span>

7.  <span data-ttu-id="02c56-229">Sélectionnez **Accueil** > **Synchroniser** > **Actions** > **Envoyer (push)**.</span><span class="sxs-lookup"><span data-stu-id="02c56-229">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

>[!NOTE]
><span data-ttu-id="02c56-230">Vous pouvez aussi envoyer (push) vos modifications à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire de votre projet et en entrant une commande Git.</span><span class="sxs-lookup"><span data-stu-id="02c56-230">As an alternative, you can push your changes from the **Command Window** by opening the **Command Window**, changing to your project directory, and entering a git command.</span></span> <span data-ttu-id="02c56-231">Exemple :</span><span class="sxs-lookup"><span data-stu-id="02c56-231">For example:</span></span>
>
>`git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="02c56-232">Afficher l’application web mise à jour dans Azure</span><span class="sxs-lookup"><span data-stu-id="02c56-232">View the updated web app in Azure</span></span>

<span data-ttu-id="02c56-233">Affichez votre application web mise à jour en sélectionnant **Parcourir** dans le panneau Application web du portail Azure, ou en ouvrant un navigateur et en entrant l’URL de votre application web.</span><span class="sxs-lookup"><span data-stu-id="02c56-233">View your updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for your web app.</span></span> <span data-ttu-id="02c56-234">Exemple :</span><span class="sxs-lookup"><span data-stu-id="02c56-234">For example:</span></span>

   `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a><span data-ttu-id="02c56-235">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="02c56-235">Additional Resources</span></span>

* [<span data-ttu-id="02c56-236">Publication et déploiement</span><span class="sxs-lookup"><span data-stu-id="02c56-236">Publishing and Deployment</span></span>](index.md)

* [<span data-ttu-id="02c56-237">Projet Kudu</span><span class="sxs-lookup"><span data-stu-id="02c56-237">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)

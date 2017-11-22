---
title: "Publier une application ASP.NET Core sur Azure à l’aide de Visual Studio"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/05/2017
ms.topic: get-started-article
ms.assetid: 78571e4a-a143-452d-9cf2-0860f85972e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 6f697ed4d8876a19cd058533e4f6a5d4f7cdc2fb
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a><span data-ttu-id="2f7f8-103">Publier une application web ASP.NET Core sur Azure App Service à l’aide de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f7f8-103">Publish an ASP.NET Core web app to Azure App Service using Visual Studio</span></span>

<span data-ttu-id="2f7f8-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs) et [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="2f7f8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs), and [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="2f7f8-105">Consultez la page [Publier sur Azure à partir de Visual Studio pour Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) si vous travaillez sur un Mac.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-105">See [Publish to Azure from Visual Studio for Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) if you are working on a Mac.</span></span>

## <a name="set-up"></a><span data-ttu-id="2f7f8-106">Installer</span><span class="sxs-lookup"><span data-stu-id="2f7f8-106">Set up</span></span>

* <span data-ttu-id="2f7f8-107">Ouvrez un [compte Azure gratuit](https://aka.ms/K5y5yh) si vous n’en avez pas un.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-107">Open a [free Azure account](https://aka.ms/K5y5yh) if you do not have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="2f7f8-108">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="2f7f8-108">Create a web app</span></span>

<span data-ttu-id="2f7f8-109">Dans la page de démarrage de Visual Studio, sélectionnez **Fichier > Nouveau > Projet...**</span><span class="sxs-lookup"><span data-stu-id="2f7f8-109">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![menu Fichier](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="2f7f8-111">Renseignez la boîte de dialogue **Nouveau projet** :</span><span class="sxs-lookup"><span data-stu-id="2f7f8-111">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="2f7f8-112">Dans le volet gauche, sélectionnez **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-112">In the left pane, select **.NET Core**.</span></span>

* <span data-ttu-id="2f7f8-113">Dans le volet central, sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-113">In the center pane, select **ASP.NET Core Web Application**.</span></span>

* <span data-ttu-id="2f7f8-114">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-114">Select **OK**.</span></span>

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="2f7f8-116">Dans la boîte de dialogue **Nouvelle application web ASP.NET Core** :</span><span class="sxs-lookup"><span data-stu-id="2f7f8-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="2f7f8-117">Sélectionnez **Application web**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-117">Select **Web Application**.</span></span>

* <span data-ttu-id="2f7f8-118">Sélectionnez **Modifier l’authentification**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-118">Select **Change Authentication**.</span></span>

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="2f7f8-120">La boîte de dialogue **Modifier l’authentification** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="2f7f8-121">Sélectionnez **Comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-121">Select **Individual User Accounts**.</span></span>

* <span data-ttu-id="2f7f8-122">Sélectionnez **OK** pour revenir à la **nouvelle application web ASP.NET Core**, puis sélectionnez à nouveau **OK**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-122">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Boîte de dialogue Nouvelle application web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="2f7f8-124">Visual Studio crée la solution.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app-locally"></a><span data-ttu-id="2f7f8-125">Exécuter l’application localement</span><span class="sxs-lookup"><span data-stu-id="2f7f8-125">Run the app locally</span></span>

* <span data-ttu-id="2f7f8-126">Choisissez **Débogage**, puis **Exécuter sans débogage** pour exécuter l’application localement.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-126">Choose **Debug** then **Start Without Debugging** to run the app locally.</span></span>

* <span data-ttu-id="2f7f8-127">Cliquez sur les liens **À propos de** et **Contact** pour vérifier que l’application web fonctionne.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-127">Click the **About** and **Contact** links to verify the web application works.</span></span>

![Application web ouverte dans Microsoft Edge sur localhost](publish-to-azure-webapp-using-vs/_static/show.png)

* <span data-ttu-id="2f7f8-129">Sélectionnez **S’inscrire**, puis inscrivez un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-129">Select **Register** and register a new user.</span></span> <span data-ttu-id="2f7f8-130">Vous pouvez utiliser une adresse e-mail fictive.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-130">You can use a fictitious email address.</span></span> <span data-ttu-id="2f7f8-131">Quand vous effectuez l’envoi, la page affiche l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="2f7f8-131">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="2f7f8-132">*« Erreur de serveur interne : Une opération de base de données a échoué lors du traitement de la requête. Exception SQL : Impossible d’ouvrir la base de données. Appliquer des migrations existantes pour le contexte de base de données d’application peut résoudre ce problème. »*</span><span class="sxs-lookup"><span data-stu-id="2f7f8-132">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>

* <span data-ttu-id="2f7f8-133">Sélectionnez **Appliquer les migrations**, puis, une fois la page mise à jour, actualisez-la.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-133">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Erreur de serveur interne : Une opération de base de données a échoué lors du traitement de la requête.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="2f7f8-137">L’application affiche l’adresse e-mail utilisée pour inscrire le nouvel utilisateur et un lien **Se déconnecter**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-137">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Application web ouverte dans Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="2f7f8-140">Déployer l’application sur Azure</span><span class="sxs-lookup"><span data-stu-id="2f7f8-140">Deploy the app to Azure</span></span>

<span data-ttu-id="2f7f8-141">Fermez la page web, retournez dans Visual Studio, puis sélectionnez **Arrêter le débogage** dans le menu **Déboguer**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-141">Close the web page, return to Visual Studio, and select **Stop Debugging** from the **Debug** menu.</span></span>

<span data-ttu-id="2f7f8-142">Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, puis sélectionnez **Publier...**</span><span class="sxs-lookup"><span data-stu-id="2f7f8-142">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="2f7f8-144">Dans la boîte de dialogue **Publier**, sélectionnez **Microsoft Azure App Service**, puis cliquez sur **Publier**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-144">In the **Publish** dialog, select **Microsoft Azure App Service** and click **Publish**.</span></span>

![Boîte de dialogue Publier](publish-to-azure-webapp-using-vs/_static/maas1.png)

* <span data-ttu-id="2f7f8-146">Attribuez un nom unique à l’application.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-146">Name the app a unique name.</span></span> 

* <span data-ttu-id="2f7f8-147">Sélectionnez un abonnement.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-147">Select a subscription.</span></span>

* <span data-ttu-id="2f7f8-148">Sélectionnez **Nouveau...** pour le groupe de ressources, puis entrez un nom pour le nouveau groupe de ressources.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-148">Select **New...** for the resource group and enter a name for the new resource group.</span></span>

* <span data-ttu-id="2f7f8-149">Sélectionnez **Nouveau...** pour le plan App Service, puis sélectionnez un lieu près de chez vous.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-149">Select **New...** for the app service plan and select a location near you.</span></span> <span data-ttu-id="2f7f8-150">Vous pouvez conserver le nom qui est généré par défaut.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-150">You can keep the name that is generated by default.</span></span>

![Boîte de dialogue App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="2f7f8-152">Sélectionnez l’onglet **Services** pour créer une base de données.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-152">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="2f7f8-153">Sélectionnez l’icône **+** verte pour créer une base de données SQL.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-153">Select the green **+** icon to create a new SQL Database</span></span>

![Nouvelle base de données SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="2f7f8-155">Sélectionnez **Nouveau...** dans la boîte de dialogue **Configurer la base de données SQL** pour créer une base de données.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-155">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Nouvelle base de données et nouveau serveur SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="2f7f8-157">La boîte de dialogue **Configurer le serveur SQL Server** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-157">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="2f7f8-158">Entrez un nom d’utilisateur et un mot de passe administrateur, puis sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-158">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="2f7f8-159">Mémorisez le nom d’utilisateur et le mot de passe que vous créez à cette étape.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-159">Don't forget the user name and password you create in this step.</span></span> <span data-ttu-id="2f7f8-160">Vous pouvez conserver le **Nom du serveur** par défaut.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-160">You can keep the default **Server Name**.</span></span> 

* <span data-ttu-id="2f7f8-161">Entrez le nom de la base de données et de la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-161">Enter names for the database and connection string.</span></span>

> [!NOTE]
> <span data-ttu-id="2f7f8-162">« admin » n’est pas autorisé comme nom d’utilisateur administrateur.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-162">"admin" is not allowed as the administrator user name.</span></span>

![Boîte de dialogue Configurer le serveur SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="2f7f8-164">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-164">Select **OK**.</span></span>

<span data-ttu-id="2f7f8-165">Visual Studio retourne la boîte de dialogue **Créer App Service**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-165">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="2f7f8-166">Sélectionnez **Créer** dans la boîte de dialogue **Créer App Service**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-166">Select **Create** on the **Create App Service** dialog.</span></span>

![Boîte de dialogue Configurer la base de données SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

* <span data-ttu-id="2f7f8-168">Cliquez sur le lien **Paramètres** dans la boîte de dialogue **Publier**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-168">Click the **Settings** link in the **Publish** dialog.</span></span>

![Boîte de dialogue Publier : panneau Connexion](publish-to-azure-webapp-using-vs/_static/pubc.png)

<span data-ttu-id="2f7f8-170">Dans la page **Paramètres** de la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="2f7f8-170">On the **Settings** page of the **Publish** dialog:</span></span>

  * <span data-ttu-id="2f7f8-171">Développez **Bases de données**, puis cochez **Utilisez cette chaîne de connexion au moment de l’exécution**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-171">Expand **Databases** and check **Use this connection string at runtime**.</span></span>

  * <span data-ttu-id="2f7f8-172">Développez **Migrations Entity Framework**, puis cochez **Appliquer cette migration lors de la publication**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-172">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="2f7f8-173">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-173">Select **Save**.</span></span> <span data-ttu-id="2f7f8-174">Visual Studio retourne à la boîte de dialogue **Publier**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-174">Visual Studio returns to the **Publish** dialog.</span></span> 

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="2f7f8-176">Cliquez sur **Publier**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-176">Click **Publish**.</span></span> <span data-ttu-id="2f7f8-177">Visual Studio publie votre application sur Azure et lance l’application cloud dans votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-177">Visual Studio will publish your app to Azure and launch the cloud app in your browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="2f7f8-178">Tester votre application dans Azure</span><span class="sxs-lookup"><span data-stu-id="2f7f8-178">Test your app in Azure</span></span>

* <span data-ttu-id="2f7f8-179">Testez les liens **À propos de** et **Contact**</span><span class="sxs-lookup"><span data-stu-id="2f7f8-179">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="2f7f8-180">Inscrivez un nouvel utilisateur</span><span class="sxs-lookup"><span data-stu-id="2f7f8-180">Register a new user</span></span>

![Application web ouverte dans Microsoft Edge sur Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="2f7f8-182">Mettre à jour l’application</span><span class="sxs-lookup"><span data-stu-id="2f7f8-182">Update the app</span></span>

* <span data-ttu-id="2f7f8-183">Modifiez la page Razor *Pages/About.cshtml*, puis changez son contenu.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-183">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="2f7f8-184">Par exemple, vous pouvez modifier le paragraphe pour indiquer « Hello ASP.NET Core! » :</span><span class="sxs-lookup"><span data-stu-id="2f7f8-184">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="2f7f8-185">Cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-185">Right-click on the project and select **Publish...** again.</span></span>

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="2f7f8-187">Une fois l’application publiée, vérifiez que les changements apportés sont disponibles sur Azure.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-187">After the app is published, verify the changes you made are available on Azure.</span></span>

![Vérifiez que la tâche est terminée](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="2f7f8-189">Nettoyer</span><span class="sxs-lookup"><span data-stu-id="2f7f8-189">Clean up</span></span>

<span data-ttu-id="2f7f8-190">Après avoir testé l’application, accédez au [portail Azure](https://portal.azure.com/), puis supprimez l’application.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-190">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="2f7f8-191">Sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-191">Select **Resource groups**, then select the resource group you created.</span></span>

![Portail Azure : Groupes de ressources dans le menu de la barre latérale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="2f7f8-193">Dans la page **Groupes de ressources**, sélectionnez **Supprimer**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-193">In the **Resource groups** page, select **Delete**.</span></span>

![Portail Azure : page Groupes de ressources](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="2f7f8-195">Entrez le nom du groupe de ressources, puis sélectionnez **Supprimer**.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-195">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="2f7f8-196">Votre application et toutes les autres ressources créées dans ce didacticiel sont désormais supprimées d’Azure.</span><span class="sxs-lookup"><span data-stu-id="2f7f8-196">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="2f7f8-197">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="2f7f8-197">Next steps</span></span>

* [<span data-ttu-id="2f7f8-198">Déploiement continu sur Azure avec Visual Studio et Git</span><span class="sxs-lookup"><span data-stu-id="2f7f8-198">Continuous Deployment to Azure with Visual Studio and Git</span></span>](../publishing/azure-continuous-deployment.md)

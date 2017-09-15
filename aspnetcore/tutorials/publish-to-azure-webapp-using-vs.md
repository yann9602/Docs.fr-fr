---
title: "Publier une application ASP.NET Core sur Azure à l’aide de Visual Studio"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: get-started-article
ms.assetid: 78571e4a-a143-452d-9cf2-0860f85972e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: c4df5f4551760fbc4ed4b11362d249b24f186e00
ms.sourcegitcommit: 8f5277871eff86134ebf68d3737196cfd4a62c2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2017
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a><span data-ttu-id="f4982-103">Publier une application web ASP.NET Core sur Azure App Service à l’aide de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f4982-103">Publish an ASP.NET Core web app to Azure App Service using Visual Studio</span></span>

<span data-ttu-id="f4982-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Cesar Blum Silveira](https://github.com/cesarbs)</span><span class="sxs-lookup"><span data-stu-id="f4982-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Cesar Blum Silveira](https://github.com/cesarbs)</span></span>

## <a name="set-up-the-development-environment"></a><span data-ttu-id="f4982-105">Configurer l’environnement de développement</span><span class="sxs-lookup"><span data-stu-id="f4982-105">Set up the development environment</span></span>

* <span data-ttu-id="f4982-106">Installez la dernière version du [SDK Azure pour Visual Studio](https://www.visualstudio.com/features/azure-tools-vs).</span><span class="sxs-lookup"><span data-stu-id="f4982-106">Install the latest [Azure SDK for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs).</span></span> <span data-ttu-id="f4982-107">Le SDK installe Visual Studio s’il ne l’est pas déjà.</span><span class="sxs-lookup"><span data-stu-id="f4982-107">The SDK installs Visual Studio if you don't already have it.</span></span>

> [!NOTE]
> <span data-ttu-id="f4982-108">L’installation du SDK peut prendre plus de 30 minutes si votre ordinateur ne possède pas la plupart des dépendances.</span><span class="sxs-lookup"><span data-stu-id="f4982-108">The SDK installation can take more than 30 minutes if your machine doesn't have many of the dependencies.</span></span>

* <span data-ttu-id="f4982-109">Installer [.NET Core et les outils Visual Studio](http://go.microsoft.com/fwlink/?LinkID=798306)</span><span class="sxs-lookup"><span data-stu-id="f4982-109">Install [.NET Core + Visual Studio tooling](http://go.microsoft.com/fwlink/?LinkID=798306)</span></span>

* <span data-ttu-id="f4982-110">Vérifiez votre [compte Azure](https://portal.azure.com/).</span><span class="sxs-lookup"><span data-stu-id="f4982-110">Verify your [Azure account](https://portal.azure.com/).</span></span> <span data-ttu-id="f4982-111">Vous pouvez [ouvrir un compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/) ou [activer les avantages pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).</span><span class="sxs-lookup"><span data-stu-id="f4982-111">You can [open a free Azure account](https://azure.microsoft.com/pricing/free-trial/) or [Activate Visual Studio subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).</span></span>

## <a name="create-a-web-app"></a><span data-ttu-id="f4982-112">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="f4982-112">Create a web app</span></span>

<span data-ttu-id="f4982-113">Dans la page de démarrage de Visual Studio, appuyez sur **Nouveau projet...**</span><span class="sxs-lookup"><span data-stu-id="f4982-113">In the Visual Studio Start Page, tap **New Project...**.</span></span>

![Page de démarrage](publish-to-azure-webapp-using-vs/_static/new_project.png)

<span data-ttu-id="f4982-115">Vous pouvez également créer un projet à l’aide des menus.</span><span class="sxs-lookup"><span data-stu-id="f4982-115">Alternatively, you can use the menus to create a new project.</span></span> <span data-ttu-id="f4982-116">Appuyez sur **Fichier > Nouveau > Projet...**</span><span class="sxs-lookup"><span data-stu-id="f4982-116">Tap **File > New > Project...**.</span></span>

![Menu Fichier](publish-to-azure-webapp-using-vs/_static/alt_new_project.png)

<span data-ttu-id="f4982-118">Renseignez la boîte de dialogue **Nouveau projet** :</span><span class="sxs-lookup"><span data-stu-id="f4982-118">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="f4982-119">Dans le volet gauche, appuyez sur **Web**</span><span class="sxs-lookup"><span data-stu-id="f4982-119">In the left pane, tap **Web**</span></span>

* <span data-ttu-id="f4982-120">Dans le volet central, appuyez sur **Application web ASP.NET Core (.NET Core)**</span><span class="sxs-lookup"><span data-stu-id="f4982-120">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>

* <span data-ttu-id="f4982-121">Appuyez sur **OK**</span><span class="sxs-lookup"><span data-stu-id="f4982-121">Tap **OK**</span></span>

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="f4982-123">Dans la boîte de dialogue **Nouvelle application web ASP.NET Core (.NET Core)** :</span><span class="sxs-lookup"><span data-stu-id="f4982-123">In the **New ASP.NET Core Web Application (.NET Core)** dialog:</span></span>

* <span data-ttu-id="f4982-124">Appuyez sur **Application web**</span><span class="sxs-lookup"><span data-stu-id="f4982-124">Tap **Web Application**</span></span>

* <span data-ttu-id="f4982-125">Vérifiez que **Authentification** est défini sur **Comptes d’utilisateur individuels**</span><span class="sxs-lookup"><span data-stu-id="f4982-125">Verify **Authentication** is set to **Individual User Accounts**</span></span>

* <span data-ttu-id="f4982-126">Vérifiez que la case **Hôte dans le cloud** n’est **pas** cochée</span><span class="sxs-lookup"><span data-stu-id="f4982-126">Verify **Host in the cloud** is **not** checked</span></span>

* <span data-ttu-id="f4982-127">Appuyez sur **OK**</span><span class="sxs-lookup"><span data-stu-id="f4982-127">Tap **OK**</span></span>

![Boîte de dialogue Nouvelle application web ASP.NET Core (.NET Core)](publish-to-azure-webapp-using-vs/_static/noath.png)

## <a name="test-the-app-locally"></a><span data-ttu-id="f4982-129">Tester l’application localement</span><span class="sxs-lookup"><span data-stu-id="f4982-129">Test the app locally</span></span>

* <span data-ttu-id="f4982-130">Appuyez sur **Ctrl+F5** pour exécuter l’application localement.</span><span class="sxs-lookup"><span data-stu-id="f4982-130">Press **Ctrl-F5** to run the app locally</span></span>

* <span data-ttu-id="f4982-131">Appuyez sur les liens **À propos de** et **Contact**.</span><span class="sxs-lookup"><span data-stu-id="f4982-131">Tap the **About** and **Contact** links.</span></span> <span data-ttu-id="f4982-132">En fonction de la taille de votre appareil, vous devrez peut-être appuyer sur l’icône de navigation pour afficher ces liens.</span><span class="sxs-lookup"><span data-stu-id="f4982-132">Depending on the size of your device, you might need to tap the navigation icon to show the links</span></span>

![Application web ouverte dans Microsoft Edge sur localhost](publish-to-azure-webapp-using-vs/_static/show.png)

* <span data-ttu-id="f4982-134">Appuyez sur **S’inscrire**, puis inscrivez un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f4982-134">Tap **Register** and register a new user.</span></span> <span data-ttu-id="f4982-135">Vous pouvez utiliser une adresse e-mail fictive.</span><span class="sxs-lookup"><span data-stu-id="f4982-135">You can use a fictitious email address.</span></span> <span data-ttu-id="f4982-136">Quand vous effectuez l’envoi, l’erreur suivante s’affiche :</span><span class="sxs-lookup"><span data-stu-id="f4982-136">When you submit, you'll get the following error:</span></span>

![Erreur de serveur interne : Une opération de base de données a échoué lors du traitement de la requête.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="f4982-140">Vous pouvez résoudre le problème de deux manières différentes :</span><span class="sxs-lookup"><span data-stu-id="f4982-140">You can fix the problem in two different ways:</span></span>

* <span data-ttu-id="f4982-141">Appuyez sur **Appliquer les migrations**, puis, une fois la page mise à jour, actualisez-la ; ou</span><span class="sxs-lookup"><span data-stu-id="f4982-141">Tap **Apply Migrations** and, once the page updates, refresh the page; or</span></span>

* <span data-ttu-id="f4982-142">Exécutez la commande suivante à partir d’une invite de commandes dans le répertoire du projet :</span><span class="sxs-lookup"><span data-stu-id="f4982-142">Run the following from a command prompt in the project's directory:</span></span>

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet ef database update
     ```

<span data-ttu-id="f4982-143">L’application affiche l’adresse e-mail utilisée pour inscrire le nouvel utilisateur et un lien **Fermer la session**.</span><span class="sxs-lookup"><span data-stu-id="f4982-143">The app displays the email used to register the new user and a **Log off** link.</span></span>

![Application web ouverte dans Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="f4982-146">Déployer l’application sur Azure</span><span class="sxs-lookup"><span data-stu-id="f4982-146">Deploy the app to Azure</span></span>

<span data-ttu-id="f4982-147">Vérifiez que l’application publiée pour le déploiement n’est pas en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="f4982-147">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="f4982-148">Les fichiers dans le dossier *publish* sont verrouillés quand l’application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="f4982-148">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="f4982-149">Le déploiement ne peut pas se produire car les fichiers verrouillés ne peuvent pas être copiés.</span><span class="sxs-lookup"><span data-stu-id="f4982-149">Deployment can't occur because locked files can't be copied.</span></span>

<span data-ttu-id="f4982-150">Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, puis sélectionnez **Publier...**</span><span class="sxs-lookup"><span data-stu-id="f4982-150">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="f4982-152">Dans la boîte de dialogue **Publier**, appuyez sur **Microsoft Azure App Service**.</span><span class="sxs-lookup"><span data-stu-id="f4982-152">In the **Publish** dialog, tap **Microsoft Azure App Service**.</span></span>

![Boîte de dialogue Publier](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="f4982-154">Appuyez sur **Nouveau...** pour créer un groupe de ressources.</span><span class="sxs-lookup"><span data-stu-id="f4982-154">Tap **New...** to create a new resource group.</span></span> <span data-ttu-id="f4982-155">La création d’un groupe de ressources permet de supprimer plus facilement toutes les ressources Azure que vous créez dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="f4982-155">Creating a new resource group will make it easier to delete all the Azure resources you create in this tutorial.</span></span>

![Boîte de dialogue App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="f4982-157">Créez un groupe de ressources et un plan App Service :</span><span class="sxs-lookup"><span data-stu-id="f4982-157">Create a new resource group and app service plan:</span></span>

* <span data-ttu-id="f4982-158">Appuyez sur **Nouveau...** pour le groupe de ressources, puis entrez un nom pour le nouveau groupe de ressources</span><span class="sxs-lookup"><span data-stu-id="f4982-158">Tap **New...** for the resource group and enter a name for the new resource group</span></span>

* <span data-ttu-id="f4982-159">Appuyez sur **Nouveau...** pour le plan App Service, puis sélectionnez un lieu près de chez vous.</span><span class="sxs-lookup"><span data-stu-id="f4982-159">Tap **New...** for the  app service plan and select a location near you.</span></span> <span data-ttu-id="f4982-160">Vous pouvez conserver le nom généré par défaut</span><span class="sxs-lookup"><span data-stu-id="f4982-160">You can keep the default generated name</span></span>

* <span data-ttu-id="f4982-161">Appuyez sur **Explorer des services Azure supplémentaires** pour créer une base de données</span><span class="sxs-lookup"><span data-stu-id="f4982-161">Tap **Explore additional Azure services** to create a new database</span></span>

![Boîte de dialogue Nouveau groupe de ressources : panneau Hébergement](publish-to-azure-webapp-using-vs/_static/cas.png)

* <span data-ttu-id="f4982-163">Appuyez sur l’icône **+** verte pour créer une base de données SQL</span><span class="sxs-lookup"><span data-stu-id="f4982-163">Tap the green **+** icon to create a new SQL Database</span></span>

![Boîte de dialogue Nouveau groupe de ressources : panneau Services](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="f4982-165">Appuyez sur **Nouveau...** dans la boîte de dialogue **Configurer la base de données SQL** pour créer un serveur de base de données.</span><span class="sxs-lookup"><span data-stu-id="f4982-165">Tap **New...** on the **Configure SQL Database** dialog to create a new database server.</span></span>

![Boîte de dialogue Configurer la base de données SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

* <span data-ttu-id="f4982-167">Entrez un nom d’utilisateur et un mot de passe administrateur, puis appuyez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="f4982-167">Enter an administrator user name and password, and then tap **OK**.</span></span> <span data-ttu-id="f4982-168">Mémorisez le nom d’utilisateur et le mot de passe que vous créez à cette étape.</span><span class="sxs-lookup"><span data-stu-id="f4982-168">Don't forget the user name and password you create in this step.</span></span> <span data-ttu-id="f4982-169">Vous pouvez conserver le **Nom du serveur** par défaut</span><span class="sxs-lookup"><span data-stu-id="f4982-169">You can keep the default **Server Name**</span></span>

![Boîte de dialogue Configurer le serveur SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

> [!NOTE]
> <span data-ttu-id="f4982-171">« admin » n’est pas autorisé comme nom d’utilisateur administrateur.</span><span class="sxs-lookup"><span data-stu-id="f4982-171">"admin" is not allowed as the administrator user name.</span></span>

* <span data-ttu-id="f4982-172">Appuyez sur **OK** dans la boîte de dialogue **Configurer la base de données SQL**</span><span class="sxs-lookup"><span data-stu-id="f4982-172">Tap **OK** on the  **Configure SQL Database** dialog</span></span>

![Boîte de dialogue Configurer la base de données SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

* <span data-ttu-id="f4982-174">Appuyez sur **Créer** dans la boîte de dialogue **Créer App Service**</span><span class="sxs-lookup"><span data-stu-id="f4982-174">Tap **Create** on the **Create App Service** dialog</span></span>

![Boîte de dialogue Créer App Service](publish-to-azure-webapp-using-vs/_static/create_as.png)

* <span data-ttu-id="f4982-176">Appuyez sur **Suivant** dans la boîte de dialogue **Publier**</span><span class="sxs-lookup"><span data-stu-id="f4982-176">Tap **Next** in the **Publish** dialog</span></span>

![Boîte de dialogue Publier : panneau Connexion](publish-to-azure-webapp-using-vs/_static/pubc.png)

* <span data-ttu-id="f4982-178">Dans la phase **Paramètres** de la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="f4982-178">On the **Settings** stage of the **Publish** dialog:</span></span>

  * <span data-ttu-id="f4982-179">Développez **Bases de données**, puis cochez **Utilisez cette chaîne de connexion au moment de l’exécution**</span><span class="sxs-lookup"><span data-stu-id="f4982-179">Expand **Databases** and check **Use this connection string at runtime**</span></span>

  * <span data-ttu-id="f4982-180">Développez **Migrations Entity Framework**, puis cochez **Appliquer cette migration lors de la publication**</span><span class="sxs-lookup"><span data-stu-id="f4982-180">Expand **Entity Framework Migrations** and check **Apply this migration on publish**</span></span>

* <span data-ttu-id="f4982-181">Appuyez sur **Publier**, puis attendez que Visual Studio ait terminé de publier votre application</span><span class="sxs-lookup"><span data-stu-id="f4982-181">Tap **Publish** and wait until Visual Studio finishes publishing your app</span></span>

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="f4982-183">Visual Studio publie votre application sur Azure et lance l’application cloud dans votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="f4982-183">Visual Studio will publish your app to Azure and launch the cloud app in your browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="f4982-184">Tester votre application dans Azure</span><span class="sxs-lookup"><span data-stu-id="f4982-184">Test your app in Azure</span></span>

* <span data-ttu-id="f4982-185">Testez les liens **À propos de** et **Contact**</span><span class="sxs-lookup"><span data-stu-id="f4982-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="f4982-186">Inscrivez un nouvel utilisateur</span><span class="sxs-lookup"><span data-stu-id="f4982-186">Register a new user</span></span>

![Application web ouverte dans Microsoft Edge sur Azure App Service](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="update-the-app"></a><span data-ttu-id="f4982-188">Mettre à jour l’application</span><span class="sxs-lookup"><span data-stu-id="f4982-188">Update the app</span></span>

* <span data-ttu-id="f4982-189">Modifiez le fichier vue `Views/Home/About.cshtml` Razor et changez son contenu.</span><span class="sxs-lookup"><span data-stu-id="f4982-189">Edit the `Views/Home/About.cshtml` Razor view file and change its contents.</span></span> <span data-ttu-id="f4982-190">Exemple :</span><span class="sxs-lookup"><span data-stu-id="f4982-190">For example:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [7]}} -->

```html
@{
       ViewData["Title"] = "About";
   }
   <h2>@ViewData["Title"].</h2>
   <h3>@ViewData["Message"]</h3>

   <p>My updated about page.</p>
   ```

* <span data-ttu-id="f4982-191">Cliquez avec le bouton droit sur le projet, puis appuyez à nouveau sur **Publier...**</span><span class="sxs-lookup"><span data-stu-id="f4982-191">Right-click on the project and tap **Publish...** again</span></span>

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="f4982-193">Une fois l’application publiée, vérifiez que les modifications apportées sont disponibles sur Azure</span><span class="sxs-lookup"><span data-stu-id="f4982-193">After the app is published, verify the changes you made are available on Azure</span></span>

### <a name="clean-up"></a><span data-ttu-id="f4982-194">Nettoyer</span><span class="sxs-lookup"><span data-stu-id="f4982-194">Clean up</span></span>

<span data-ttu-id="f4982-195">Après avoir testé l’application, accédez au [portail Azure](https://portal.azure.com/), puis supprimez l’application.</span><span class="sxs-lookup"><span data-stu-id="f4982-195">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="f4982-196">Sélectionnez **Groupes de ressources**, puis appuyez sur le groupe de ressources que vous avez créé</span><span class="sxs-lookup"><span data-stu-id="f4982-196">Select **Resource groups**, then tap the resource group you created</span></span>

![Portail Azure : Groupes de ressources dans le menu de la barre latérale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="f4982-198">Dans le panneau **Groupe de ressources**, appuyez sur **Supprimer**</span><span class="sxs-lookup"><span data-stu-id="f4982-198">In the **Resource group** blade, tap **Delete**</span></span>

![Portail Azure : panneau Groupes de ressources](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="f4982-200">Entrez le nom du groupe de ressources, puis appuyez sur **Supprimer**.</span><span class="sxs-lookup"><span data-stu-id="f4982-200">Enter the name of the resource group and tap **Delete**.</span></span> <span data-ttu-id="f4982-201">Votre application et toutes les autres ressources créées dans ce didacticiel sont désormais supprimés d’Azure</span><span class="sxs-lookup"><span data-stu-id="f4982-201">Your app and all other resources created in this tutorial are now deleted from Azure</span></span>

### <a name="next-steps"></a><span data-ttu-id="f4982-202">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="f4982-202">Next steps</span></span>

* [<span data-ttu-id="f4982-203">Bien démarrer avec ASP.NET Core MVC et Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f4982-203">Getting started with ASP.NET Core MVC and Visual Studio</span></span>](first-mvc-app/start-mvc.md)

* [<span data-ttu-id="f4982-204">Présentation d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f4982-204">Introduction to ASP.NET Core</span></span>](../index.md)

* [<span data-ttu-id="f4982-205">Notions de base</span><span class="sxs-lookup"><span data-stu-id="f4982-205">Fundamentals</span></span>](../fundamentals/index.md)

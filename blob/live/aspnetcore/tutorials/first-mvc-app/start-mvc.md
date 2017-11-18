---
title: "Bien démarrer avec ASP.NET Core MVC et Visual Studio"
author: rick-anderson
description: "Bien démarrer avec ASP.NET Core MVC et Visual Studio"
keywords: ASP.NET Core, MVC
ms.author: riande
manager: wpickett
ms.date: 10/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 4b86eb22e367d2305b7995421aec6f37008c5637
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="getting-started-with-aspnet-core-mvc-and-visual-studio"></a><span data-ttu-id="40af7-104">Bien démarrer avec ASP.NET Core MVC et Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af7-104">Getting started with ASP.NET Core MVC and Visual Studio</span></span>

<span data-ttu-id="40af7-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="40af7-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[consider RP](../../includes/razor.md)]

<span data-ttu-id="40af7-106">Il existe trois versions de ce didacticiel :</span><span class="sxs-lookup"><span data-stu-id="40af7-106">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="40af7-107">macOS : [Créer une application ASP.NET Core MVC avec Visual Studio pour Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="40af7-107">macOS: [Create an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="40af7-108">Windows : [Créer une application ASP.NET Core MVC avec Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="40af7-108">Windows: [Create an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="40af7-109">macOS, Linux et Windows : [Créer une application ASP.NET Core MVC avec Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="40af7-109">macOS, Linux, and Windows: [Create an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

## <a name="install-visual-studio-and-net-core"></a><span data-ttu-id="40af7-110">Installer Visual Studio et .NET Core</span><span class="sxs-lookup"><span data-stu-id="40af7-110">Install Visual Studio and .NET Core</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="40af7-111">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="40af7-111">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="40af7-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="40af7-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="40af7-113">Installez Visual Studio Community 2017.</span><span class="sxs-lookup"><span data-stu-id="40af7-113">Install Visual Studio Community 2017.</span></span> <span data-ttu-id="40af7-114">Sélectionnez le téléchargement Community.</span><span class="sxs-lookup"><span data-stu-id="40af7-114">Select the Community download.</span></span> <span data-ttu-id="40af7-115">Ignorez cette étape si Visual Studio 2017 est installé sur votre ordinateur.</span><span class="sxs-lookup"><span data-stu-id="40af7-115">Skip this step if you have Visual Studio 2017 installed.</span></span>

* [<span data-ttu-id="40af7-116">Page d’accueil du programme d’installation de Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="40af7-116">Visual Studio 2017 Home page installer</span></span>](https://www.visualstudio.com/)

<span data-ttu-id="40af7-117">Exécutez le programme d’installation et sélectionnez les charges de travail suivantes :</span><span class="sxs-lookup"><span data-stu-id="40af7-117">Run the installer and select the following workloads:</span></span>

* <span data-ttu-id="40af7-118">**ASP.NET et développement web** (sous **Web et cloud**)</span><span class="sxs-lookup"><span data-stu-id="40af7-118">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
* <span data-ttu-id="40af7-119">**Développement multiplateforme .NET Core** (sous **Autres ensembles d’outils**)</span><span class="sxs-lookup"><span data-stu-id="40af7-119">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

![**ASP.NET et développement web** (sous **Web et cloud**)](start-mvc/_static/web_workload.png)

![**Développement multiplateforme .NET Core** (sous **Autres ensembles d’outils**)](start-mvc/_static/x_plat_wl.png)

---

## <a name="create-a-web-app"></a><span data-ttu-id="40af7-122">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="40af7-122">Create a web app</span></span>

<span data-ttu-id="40af7-123">Dans Visual Studio, sélectionnez **Fichier > Nouveau > Projet**.</span><span class="sxs-lookup"><span data-stu-id="40af7-123">From Visual Studio, select  **File > New > Project**.</span></span>

![Fichier > Nouveau > Projet](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="40af7-125">Renseignez la boîte de dialogue **Nouveau projet** :</span><span class="sxs-lookup"><span data-stu-id="40af7-125">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="40af7-126">Dans le volet gauche, cliquez sur **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="40af7-126">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="40af7-127">Dans le volet central, cliquez sur **Application web ASP.NET Core (.NET Core)**.</span><span class="sxs-lookup"><span data-stu-id="40af7-127">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="40af7-128">Nommez le projet « MvcMovie » (ceci est important pour que l’espace de noms corresponde quand vous copierez le code).</span><span class="sxs-lookup"><span data-stu-id="40af7-128">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="40af7-129">Appuyez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="40af7-129">Tap **OK**</span></span>

![<span data-ttu-id="40af7-130">Boîte de dialogue Nouveau projet, .Net Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40af7-130">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2.png)


# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="40af7-131">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="40af7-131">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="40af7-132">Renseignez la boîte de dialogue **Nouvelle application web ASP.NET Core (.NET Core) - MvcMovie** :</span><span class="sxs-lookup"><span data-stu-id="40af7-132">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="40af7-133">Dans la zone de liste déroulante du sélecteur de version, sélectionnez **ASP.NET Core 2.-**.</span><span class="sxs-lookup"><span data-stu-id="40af7-133">In the version selector drop-down box select **ASP.NET Core 2.-**</span></span>
* <span data-ttu-id="40af7-134">Sélectionnez **Application web (Model-View-Controller)**</span><span class="sxs-lookup"><span data-stu-id="40af7-134">Select **Web Application(Model-View-Controller)**</span></span>
* <span data-ttu-id="40af7-135">Appuyez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="40af7-135">Tap **OK**.</span></span>

![<span data-ttu-id="40af7-136">Boîte de dialogue Nouveau projet, .Net Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40af7-136">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22.png)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="40af7-137">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="40af7-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="40af7-138">Renseignez la boîte de dialogue **Nouvelle application web ASP.NET Core (.NET Core) - MvcMovie** :</span><span class="sxs-lookup"><span data-stu-id="40af7-138">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="40af7-139">Dans la zone de liste déroulante du sélecteur de version, appuyez sur **ASP.NET Core 1.1**.</span><span class="sxs-lookup"><span data-stu-id="40af7-139">In the version selector drop-down box tap **ASP.NET Core 1.1**</span></span>
* <span data-ttu-id="40af7-140">Appuyez sur **Application web**.</span><span class="sxs-lookup"><span data-stu-id="40af7-140">Tap **Web Application**</span></span>
* <span data-ttu-id="40af7-141">Conservez la valeur par défaut **Aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="40af7-141">Keep the default **No Authentication**</span></span>
* <span data-ttu-id="40af7-142">Appuyez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="40af7-142">Tap **OK**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/p3.png)

---

<span data-ttu-id="40af7-144">Visual Studio a utilisé un modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="40af7-144">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="40af7-145">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="40af7-145">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="40af7-146">Il s’agit d’un projet de démarrage simple qui constitue un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="40af7-146">This is a simple starter project, and it's a good place to start,</span></span>

<span data-ttu-id="40af7-147">Appuyez sur **F5** pour exécuter l’application en mode débogage ou sur **Ctrl-F5** pour l’exécuter en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="40af7-147">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![application en cours d’exécution](start-mvc/_static/1.png)

* <span data-ttu-id="40af7-149">Visual Studio démarre [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute votre application.</span><span class="sxs-lookup"><span data-stu-id="40af7-149">Visual Studio starts [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="40af7-150">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="40af7-150">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="40af7-151">C’est parce que `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="40af7-151">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="40af7-152">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="40af7-152">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="40af7-153">Dans l’image ci-dessus, le numéro de port est 5000.</span><span class="sxs-lookup"><span data-stu-id="40af7-153">In the image above, the port number is 5000.</span></span> <span data-ttu-id="40af7-154">Quand vous exécuterez l’application, un numéro de port différent sera affiché.</span><span class="sxs-lookup"><span data-stu-id="40af7-154">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="40af7-155">Si vous lancez l’application avec **Ctrl+F5** (mode sans débogage), vous pouvez effectuer des modifications du code, enregistrer le fichier, actualiser le navigateur et afficher les modifications du code.</span><span class="sxs-lookup"><span data-stu-id="40af7-155">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="40af7-156">De nombreux développeurs préfèrent utiliser le mode non-débogage pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="40af7-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="40af7-157">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="40af7-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="40af7-159">Vous pouvez déboguer l’application en appuyant sur le bouton **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="40af7-159">You can debug the app by tapping the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="40af7-161">Le modèle par défaut donne des liens **Accueil, À propos** et **Contact** fonctionnels.</span><span class="sxs-lookup"><span data-stu-id="40af7-161">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="40af7-162">L’image de navigateur ci-dessus ne montre pas ces liens.</span><span class="sxs-lookup"><span data-stu-id="40af7-162">The browser image above doesn't show these links.</span></span> <span data-ttu-id="40af7-163">En fonction de la taille de votre navigateur, vous devrez peut-être cliquer sur l’icône de navigation pour les afficher.</span><span class="sxs-lookup"><span data-stu-id="40af7-163">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![Icône de navigation en haut à droite](start-mvc/_static/2.png)

<span data-ttu-id="40af7-165">Si vous exécutiez l’application en mode débogage, appuyez sur **Maj-F5** pour arrêter le débogage.</span><span class="sxs-lookup"><span data-stu-id="40af7-165">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="40af7-166">Dans la prochaine partie de ce didacticiel, nous allons découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="40af7-166">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

>[!div class="step-by-step"]
[<span data-ttu-id="40af7-167">Next</span><span class="sxs-lookup"><span data-stu-id="40af7-167">Next</span></span>](adding-controller.md)  

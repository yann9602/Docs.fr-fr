---
title: "Génération de projets avec Yeoman dans ASP.NET Core"
author: spboyer
description: "Cet article Guide de création d’application web à l’aide de la Yeoman une ASP.NET Core générateur sur macOS."
keywords: ASP.NET Core, Yeoman, multiplateforme, v aspnet
ms.author: spboyer
manager: wpickett
ms.date: 07/05/2017
ms.topic: article
ms.assetid: fda0c2a8-1743-4505-be1a-7f8ceeef8647
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/yeoman
ms.openlocfilehash: d7411c1635e9fef2857f9a03e7310224ee8d7344
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-building-projects-with-yeoman-in-aspnet-core"></a><span data-ttu-id="6bf24-104">Introduction à la génération de projets avec Yeoman dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6bf24-104">Introduction to building projects with Yeoman in ASP.NET Core</span></span>

<span data-ttu-id="6bf24-105">[Yeoman](http://yeoman.io/) est un système de la structure de projet pour la création de nombreux types d’applications.</span><span class="sxs-lookup"><span data-stu-id="6bf24-105">[Yeoman](http://yeoman.io/) is a project scaffolding system for creating many kinds of applications.</span></span> <span data-ttu-id="6bf24-106">Le Yeoman générateur pour ASP.NET Core contient une variété de modèles de projet pour démarrer un nouveau site web, MVC ou application console.</span><span class="sxs-lookup"><span data-stu-id="6bf24-106">The Yeoman generator for ASP.NET Core contains a variety of project templates for starting a new web, MVC, or console application.</span></span>

## <a name="install-nodejs-npm-and-yeoman"></a><span data-ttu-id="6bf24-107">Installation de Node.js et npm Yeoman</span><span class="sxs-lookup"><span data-stu-id="6bf24-107">Install Node.js, npm, and Yeoman</span></span>

### <a name="prerequisites"></a><span data-ttu-id="6bf24-108">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="6bf24-108">Prerequisites</span></span>

<span data-ttu-id="6bf24-109">Node.js et npm sont requis pour Yeoman.</span><span class="sxs-lookup"><span data-stu-id="6bf24-109">Node.js and npm are required for Yeoman.</span></span> <span data-ttu-id="6bf24-110">Télécharger à partir de [Node.js](https://nodejs.org/).</span><span class="sxs-lookup"><span data-stu-id="6bf24-110">Download from [Node.js](https://nodejs.org/).</span></span> <span data-ttu-id="6bf24-111">Le programme d’installation inclut [Node.js](https://nodejs.org/) et [npm](https://www.npmjs.com/).</span><span class="sxs-lookup"><span data-stu-id="6bf24-111">The installer includes [Node.js](https://nodejs.org/) and [npm](https://www.npmjs.com/).</span></span> <span data-ttu-id="6bf24-112">Bower est également requis pour l’installation des infrastructures d’interface utilisateur tels que les données d’amorçage.</span><span class="sxs-lookup"><span data-stu-id="6bf24-112">Bower is also required for installing UI frameworks like Bootstrap.</span></span>

<span data-ttu-id="6bf24-113">Pour installer Yeoman et Bower, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="6bf24-113">To install Yeoman and Bower, run the following command:</span></span>

```console
npm install -g yo bower
```

>[!Note]
><span data-ttu-id="6bf24-114">Si vous obtenez l’erreur `npm ERR! Please try running this command again as root/Administrator.` sur macOS, exécutez la commande suivante à l’aide [sudo](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/sudo.8.html):`sudo npm install -g yo bower`</span><span class="sxs-lookup"><span data-stu-id="6bf24-114">If you get the error `npm ERR! Please try running this command again as root/Administrator.` on macOS, run the following command using [sudo](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/sudo.8.html): `sudo npm install -g yo bower`</span></span>

<span data-ttu-id="6bf24-115">À partir d’une invite de commandes, installez le générateur ASP.NET :</span><span class="sxs-lookup"><span data-stu-id="6bf24-115">From a command prompt, install the ASP.NET generator:</span></span>

```console
npm install -g generator-aspnet
```

> [!NOTE]
> <span data-ttu-id="6bf24-116">Si vous obtenez une erreur d’autorisation, exécutez la commande sous `sudo` comme décrit ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="6bf24-116">If you get a permission error, run the command under `sudo` as described above.</span></span>

<span data-ttu-id="6bf24-117">Le `–g` indicateur installe le Générateur de manière globale, afin qu’il peut être utilisé à partir de n’importe quel chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="6bf24-117">The `–g` flag installs the generator globally, so that it can be used from any path.</span></span>

## <a name="create-an-aspnet-app"></a><span data-ttu-id="6bf24-118">Créer une application ASP.NET</span><span class="sxs-lookup"><span data-stu-id="6bf24-118">Create an ASP.NET app</span></span>

<span data-ttu-id="6bf24-119">Exécutez le générateur ASP.NET basées sur Yeoman :</span><span class="sxs-lookup"><span data-stu-id="6bf24-119">Run the Yeoman-based ASP.NET generator:</span></span>

```console
yo aspnet
```

<span data-ttu-id="6bf24-120">Le générateur affiche un menu.</span><span class="sxs-lookup"><span data-stu-id="6bf24-120">The generator displays a menu.</span></span> <span data-ttu-id="6bf24-121">Flèche vers le bas pour le **Web Application base [sans l’appartenance et l’autorisation]** de projet, puis appuyez sur **entrée**:</span><span class="sxs-lookup"><span data-stu-id="6bf24-121">Arrow down to the **Web Application Basic [without Membership and Authorization]** project and tap **Enter**:</span></span>

![Fenêtre de commande : quel type d’application voulez-vous créer ?](yeoman/_static/yeoman-yo-aspnet.png)

<span data-ttu-id="6bf24-124">Sélectionnez les données d’amorçage en tant que l’infrastructure d’interface utilisateur, puis appuyez sur **entrée**.</span><span class="sxs-lookup"><span data-stu-id="6bf24-124">Select Bootstrap as the UI Framework and tap **Enter**.</span></span>

<span data-ttu-id="6bf24-125">Utilisez «**MyWebApp**» pour le nom de l’application, puis sur **entrée**.</span><span class="sxs-lookup"><span data-stu-id="6bf24-125">Use "**MyWebApp**" for the app name and then tap **Enter**.</span></span>

<span data-ttu-id="6bf24-126">Yeoman sera structurer le projet et ses fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="6bf24-126">Yeoman will scaffold the project and its supporting files.</span></span> <span data-ttu-id="6bf24-127">Étapes suggérées sont également fournis sous la forme de commandes.</span><span class="sxs-lookup"><span data-stu-id="6bf24-127">Suggested next steps are also provided in the form of commands.</span></span>

![Fenêtre de commande : quel est le nom de votre application ASP.NET ?](yeoman/_static/yeoman-yo-aspnet-created.png)

<span data-ttu-id="6bf24-130">Le [ASP.NET Générateur](https://www.npmjs.com/package/generator-aspnet) crée des projets qui peuvent être chargé dans le Code de Visual Studio, Visual Studio, ou exécuter à partir de la ligne de commande ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6bf24-130">The [ASP.NET generator](https://www.npmjs.com/package/generator-aspnet) creates ASP.NET Core projects that can be loaded into Visual Studio Code, Visual Studio, or run from the command line.</span></span>

## <a name="restore-build-and-run"></a><span data-ttu-id="6bf24-131">Restaurer, générer et exécuter</span><span class="sxs-lookup"><span data-stu-id="6bf24-131">Restore, build, and run</span></span>

<span data-ttu-id="6bf24-132">Suivez les commandes suggérées en modifiant les répertoires dans lesquels le `MyWebApp` active.</span><span class="sxs-lookup"><span data-stu-id="6bf24-132">Follow the suggested commands by changing directories to the `MyWebApp` directory.</span></span> <span data-ttu-id="6bf24-133">Puis exécutez `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="6bf24-133">Then run `dotnet restore`.</span></span>

![Commande (fenêtre)](yeoman/_static/dotnet-restore.png)

<span data-ttu-id="6bf24-135">Générer et exécuter l’application à l’aide de `dotnet build` et `dotnet run`:</span><span class="sxs-lookup"><span data-stu-id="6bf24-135">Build and run the app using `dotnet build` and `dotnet run`:</span></span>

![Commande (fenêtre)](yeoman/_static/dotnet-build-run.png)

<span data-ttu-id="6bf24-137">À ce stade, vous pouvez naviguer vers l’URL affichée pour tester l’application ASP.NET Core nouvellement créé.</span><span class="sxs-lookup"><span data-stu-id="6bf24-137">At this point, you can navigate to the URL shown to test the newly-created ASP.NET Core app.</span></span>

## <a name="client-side-packages"></a><span data-ttu-id="6bf24-138">Packages côté client</span><span class="sxs-lookup"><span data-stu-id="6bf24-138">Client-side packages</span></span>

<span data-ttu-id="6bf24-139">Les ressources frontaux sont fournies par les modèles à partir de la Yeoman à l’aide de générateur le [Bower](xref:client-side/bower) le Gestionnaire de package de côté client, ajout *bower.json* et *.bowerrc* fichiers à restaurer les packages côté client à l’aide de Bower.</span><span class="sxs-lookup"><span data-stu-id="6bf24-139">The front-end resources are provided by the templates from the Yeoman generator using the [Bower](xref:client-side/bower) client-side package manager, adding *bower.json* and *.bowerrc* files to restore client-side packages using Bower.</span></span>

<span data-ttu-id="6bf24-140">Le [BundlerMinifier](xref:client-side/bundling-and-minification) composant est également inclus par défaut pour la facilité de concaténation (regroupement) et la réduction de CSS, JavaScript et HTML.</span><span class="sxs-lookup"><span data-stu-id="6bf24-140">The [BundlerMinifier](xref:client-side/bundling-and-minification) component is also included by default for ease of concatenation (bundling) and minification of CSS, JavaScript, and HTML.</span></span>

## <a name="building-and-running-from-visual-studio"></a><span data-ttu-id="6bf24-141">Création et l’exécution à partir de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bf24-141">Building and running from Visual Studio</span></span>

<span data-ttu-id="6bf24-142">Vous pouvez charger votre projet de web ASP.NET Core généré directement dans Visual Studio, puis générer et exécuter votre projet à partir de là.</span><span class="sxs-lookup"><span data-stu-id="6bf24-142">You can load your generated ASP.NET Core web project directly into Visual Studio, then build and run your project from there.</span></span> <span data-ttu-id="6bf24-143">Suivez les instructions ci-dessus pour structurer une application ASP.NET Core à l’aide de Yeoman.</span><span class="sxs-lookup"><span data-stu-id="6bf24-143">Follow the instructions above to scaffold a new ASP.NET Core app using Yeoman.</span></span> <span data-ttu-id="6bf24-144">Cette fois-ci, choisissez **Application Web** dans le menu et le nom de l’application `MyWebApp`.</span><span class="sxs-lookup"><span data-stu-id="6bf24-144">This time, choose **Web Application** from the menu and name the app `MyWebApp`.</span></span>

<span data-ttu-id="6bf24-145">Ouvrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6bf24-145">Open Visual Studio.</span></span> <span data-ttu-id="6bf24-146">Dans le menu fichier, sélectionnez Ouvrir ‣ projet/Solution.</span><span class="sxs-lookup"><span data-stu-id="6bf24-146">From the File menu, select Open ‣ Project/Solution.</span></span>

<span data-ttu-id="6bf24-147">Dans la boîte de dialogue Ouvrir un projet, accédez à la *.csproj* de fichier, sélectionnez-le, puis cliquez sur le **ouvrir** bouton.</span><span class="sxs-lookup"><span data-stu-id="6bf24-147">In the Open Project dialog, navigate to the *.csproj* file, select it, and click the **Open** button.</span></span> <span data-ttu-id="6bf24-148">Dans l’Explorateur de solutions, le projet doit ressembler à la capture d’écran ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="6bf24-148">In the Solution Explorer, the project should look something like the screenshot below.</span></span>

![Fichiers et dossiers d’un nouveau projet dans l’Explorateur de solutions](yeoman/_static/yeoman-solution.png)

<span data-ttu-id="6bf24-150">Yeoman micro-capsules une application web MVC, support de build côté serveur et côté client avec les deux.</span><span class="sxs-lookup"><span data-stu-id="6bf24-150">Yeoman scaffolds a MVC web application, complete with both server- and client-side build support.</span></span> <span data-ttu-id="6bf24-151">Dépendances de côté serveur sont répertoriés sous la **dépendances/NuGet** nœud et des dépendances côté client dans le **dépendances/Bower** nœud de l’Explorateur de solutions.</span><span class="sxs-lookup"><span data-stu-id="6bf24-151">Server-side dependencies are listed under the **Dependencies/NuGet** node, and client-side dependencies in the **Dependencies/Bower** node of Solution Explorer.</span></span> <span data-ttu-id="6bf24-152">Dépendances soient restaurés automatiquement lorsque le projet est chargé.</span><span class="sxs-lookup"><span data-stu-id="6bf24-152">Dependencies are restored automatically when the project is loaded.</span></span>

![Sous le nœud dépendances dans l’arborescence de l’Explorateur de solutions, le dossier Bower est ouvert de liste de ses dépendances.](yeoman/_static/yeoman-loading-dependencies.png)

<span data-ttu-id="6bf24-154">Lorsque toutes les dépendances sont restaurés, appuyez sur **F5** pour exécuter le projet.</span><span class="sxs-lookup"><span data-stu-id="6bf24-154">When all the dependencies are restored, press **F5** to run the project.</span></span> <span data-ttu-id="6bf24-155">La page d’accueil par défaut s’affiche dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="6bf24-155">The default home page displays in the browser.</span></span>

![Application Web ouverte dans Microsoft Edge](yeoman/_static/yeoman-home-page.png)

## <a name="restoring-building-and-hosting-from-a-command-line"></a><span data-ttu-id="6bf24-157">Restauration, de la création et l’hébergement d’une ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6bf24-157">Restoring, building, and hosting from a command line</span></span>

<span data-ttu-id="6bf24-158">Vous pouvez préparer et héberger votre application web à l’aide de l’interface de ligne de base de .NET.</span><span class="sxs-lookup"><span data-stu-id="6bf24-158">You can prepare and host your web application using the .NET Core CLI.</span></span>

<span data-ttu-id="6bf24-159">À une invite de commandes, accédez au répertoire en cours dans le dossier contenant le projet (autrement dit, le dossier contenant le *.csproj* fichier) :</span><span class="sxs-lookup"><span data-stu-id="6bf24-159">At a command prompt, change the current directory to the folder containing the project (that is, the folder containing the *.csproj* file):</span></span>

```console
cd src\MyWebApp
```

<span data-ttu-id="6bf24-160">Restaurer les dépendances du package NuGet du projet :</span><span class="sxs-lookup"><span data-stu-id="6bf24-160">Restore the project's NuGet package dependencies:</span></span>

```console
dotnet restore
```

<span data-ttu-id="6bf24-161">Exécutez l’application :</span><span class="sxs-lookup"><span data-stu-id="6bf24-161">Run the application:</span></span>

```console
dotnet run
```

<span data-ttu-id="6bf24-162">L’inter-plateformes [Kestrel](xref:fundamentals/servers/kestrel) serveur web commencera à l’écoute sur le port 5000.</span><span class="sxs-lookup"><span data-stu-id="6bf24-162">The cross-platform [Kestrel](xref:fundamentals/servers/kestrel) web server will begin listening on port 5000.</span></span>

<span data-ttu-id="6bf24-163">Ouvrez un navigateur web et accédez à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="6bf24-163">Open a web browser, and navigate to `http://localhost:5000`.</span></span>

![Application Web ouverte dans Microsoft Edge](yeoman/_static/yeoman-home-page_5000.png)

## <a name="adding-to-your-project-with-sub-generators"></a><span data-ttu-id="6bf24-165">Ajout à votre projet avec des générateurs de sub</span><span class="sxs-lookup"><span data-stu-id="6bf24-165">Adding to your project with sub generators</span></span>

<span data-ttu-id="6bf24-166">À l’aide de Yeoman [sub générateurs](https://github.com/omnisharp/generator-aspnet), vous pouvez ajouter un `nuget.config` ou un `web.config` après la création du projet.</span><span class="sxs-lookup"><span data-stu-id="6bf24-166">Using Yeoman [sub generators](https://github.com/omnisharp/generator-aspnet), you can add either a `nuget.config` or a `web.config` after the project is created.</span></span> <span data-ttu-id="6bf24-167">Par exemple, exécutez la commande suivante à partir du répertoire dans lequel le fichier doit être créé :</span><span class="sxs-lookup"><span data-stu-id="6bf24-167">For example, execute the following command from the directory in which the file should be created:</span></span>

```console
yo aspnet:nugetconfig
```

<span data-ttu-id="6bf24-168">Le résultat est un fichier de configuration NuGet nommé `nuget.config` avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="6bf24-168">The result is a NuGet configuration file named `nuget.config` with the following content:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
 <packageSources>
    <!--To inherit the global NuGet package sources remove the <clear/> line below -->
    <clear />
 </packageSources>
</configuration>
```

## <a name="additional-resources"></a><span data-ttu-id="6bf24-169">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6bf24-169">Additional resources</span></span>

* [<span data-ttu-id="6bf24-170">Serveurs (Kestrel et WebListener)</span><span class="sxs-lookup"><span data-stu-id="6bf24-170">Servers (Kestrel and WebListener)</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="6bf24-171">Notions de base</span><span class="sxs-lookup"><span data-stu-id="6bf24-171">Fundamentals</span></span>](xref:fundamentals/index)

---
title: "Migration à partir de ASP.NET MVC à cœur d’ASP.NET MVC"
author: ardalis
description: 
keywords: ASP.NET Core,MVC,migration
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: 3155cc9e-d0c9-424b-886c-35c0ec6f9f4e
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/mvc
ms.openlocfilehash: 385ab7dfea5b92687a427bdfe9558462227113b1
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="migrating-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="2e581-103">Migration à partir de ASP.NET MVC à cœur d’ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="2e581-103">Migrating From ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="2e581-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Michel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), et [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="2e581-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="2e581-105">Cet article explique comment commencer la migration d’un projet ASP.NET MVC à [ASP.NET Core MVC](../mvc/overview.md).</span><span class="sxs-lookup"><span data-stu-id="2e581-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="2e581-106">Dans le processus, il présente la plupart des éléments qui ont été modifiés dans ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="2e581-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="2e581-107">Migration à partir d’ASP.NET MVC est un processus en plusieurs étapes, et cet article couvre la configuration initiale, contrôleurs de base et les vues, contenu statique et dépendances du côté client.</span><span class="sxs-lookup"><span data-stu-id="2e581-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="2e581-108">Autres articles couvrent la migration de la configuration et le code d’identité trouvée dans de nombreux projets ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="2e581-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="2e581-109">Les numéros de version dans les exemples ne sont peut-être pas en cours.</span><span class="sxs-lookup"><span data-stu-id="2e581-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="2e581-110">Vous devrez peut-être mettre à jour vos projets en conséquence.</span><span class="sxs-lookup"><span data-stu-id="2e581-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="2e581-111">Créer le projet ASP.NET MVC starter</span><span class="sxs-lookup"><span data-stu-id="2e581-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="2e581-112">Pour illustrer la mise à niveau, nous allons commencer par la création d’une application ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="2e581-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="2e581-113">Créer avec le nom *WebApp1* pour le projet ASP.NET Core nous créer à l’étape suivante correspond à l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="2e581-113">Create it with the name *WebApp1* so the namespace will match the ASP.NET Core project we create in the next step.</span></span>

![Boîte de dialogue Visual Studio nouveau projet](mvc/_static/new-project.png)

![Boîte de dialogue nouvelle Application Web : modèle de projet MVC sélectionné dans le panneau de configuration de modèles ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="2e581-116">*Facultatif :* modifier le nom de la Solution à partir de *WebApp1* à *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="2e581-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="2e581-117">Visual Studio affiche le nouveau nom de solution (*Mvc5*), ce qui rend plus facile indiquer ce projet à partir du projet suivant.</span><span class="sxs-lookup"><span data-stu-id="2e581-117">Visual Studio will display the new solution name (*Mvc5*), which will make it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="2e581-118">Créer le projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e581-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="2e581-119">Créer un nouveau *vide* application de web ASP.NET Core avec le même nom que le projet précédent (*WebApp1*) afin de correspondre à des espaces de noms dans les deux projets.</span><span class="sxs-lookup"><span data-stu-id="2e581-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="2e581-120">Ayant le même espace de noms rend plus facile de copier le code entre les deux projets.</span><span class="sxs-lookup"><span data-stu-id="2e581-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="2e581-121">Vous devrez créer ce projet dans un répertoire autre que le projet précédent pour utiliser le même nom.</span><span class="sxs-lookup"><span data-stu-id="2e581-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Boîte de dialogue Nouveau projet](mvc/_static/new_core.png)

![Boîte de dialogue nouvelle Application Web ASP.NET : modèle de projet vide sélectionnée dans le panneau de configuration de modèles de base ASP.NET](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="2e581-124">*Facultatif :* créer une nouvelle application ASP.NET Core à l’aide du *Application Web* modèle de projet.</span><span class="sxs-lookup"><span data-stu-id="2e581-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="2e581-125">Nommez le projet *WebApp1*, puis sélectionnez une option d’authentification de **comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="2e581-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="2e581-126">Renommer cette application *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="2e581-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="2e581-127">Création de ce projet seront gagner du temps lors de la conversion.</span><span class="sxs-lookup"><span data-stu-id="2e581-127">Creating this project will save you time in the conversion.</span></span> <span data-ttu-id="2e581-128">Vous pouvez examiner le code généré de modèle pour afficher le résultat final ou copiez le code dans le projet de conversion.</span><span class="sxs-lookup"><span data-stu-id="2e581-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="2e581-129">Il est également utile lorsque vous êtes bloqué sur une étape de conversion à comparer avec le projet de modèle généré.</span><span class="sxs-lookup"><span data-stu-id="2e581-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="2e581-130">Configurer le site pour utiliser MVC</span><span class="sxs-lookup"><span data-stu-id="2e581-130">Configure the site to use MVC</span></span>

* <span data-ttu-id="2e581-131">Installer le `Microsoft.AspNetCore.Mvc` et `Microsoft.AspNetCore.StaticFiles` les packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="2e581-131">Install the `Microsoft.AspNetCore.Mvc` and `Microsoft.AspNetCore.StaticFiles` NuGet packages.</span></span>

  <span data-ttu-id="2e581-132">`Microsoft.AspNetCore.Mvc`est de l’infrastructure ASP.NET MVC de base.</span><span class="sxs-lookup"><span data-stu-id="2e581-132">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="2e581-133">`Microsoft.AspNetCore.StaticFiles`est le Gestionnaire de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="2e581-133">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="2e581-134">Le runtime ASP.NET est modulaire, et vous devez explicitement s’abonner à des fichiers statiques (consultez [utilisation des fichiers statiques](../fundamentals/static-files.md)).</span><span class="sxs-lookup"><span data-stu-id="2e581-134">The ASP.NET runtime is modular, and you must explicitly opt in to serve static files (see [Working with Static Files](../fundamentals/static-files.md)).</span></span>

* <span data-ttu-id="2e581-135">Ouvrez le *.csproj* fichier (avec le bouton droit dans le projet de **l’Explorateur de solutions** et sélectionnez **WebApp1.csproj de modifier**) et ajoutez un `PrepareForPublish` cible :</span><span class="sxs-lookup"><span data-stu-id="2e581-135">Open the *.csproj* file (right-click the project in **Solution Explorer** and select **Edit WebApp1.csproj**) and add a `PrepareForPublish` target:</span></span>

  [!code-xml[Main](mvc/sample/WebApp1.csproj?range=21-23)]

  <span data-ttu-id="2e581-136">Le `PrepareForPublish` cible est nécessaire pour l’acquisition des bibliothèques côté client via Bower.</span><span class="sxs-lookup"><span data-stu-id="2e581-136">The `PrepareForPublish` target is needed for acquiring client-side libraries via Bower.</span></span> <span data-ttu-id="2e581-137">Nous parlerons que plus tard.</span><span class="sxs-lookup"><span data-stu-id="2e581-137">We'll talk about that later.</span></span>

* <span data-ttu-id="2e581-138">Ouvrez le *Startup.cs* de fichiers et de modifier le code pour faire correspondre les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="2e581-138">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[Main](mvc/sample/Startup.cs?highlight=14,27-34)]

  <span data-ttu-id="2e581-139">Le `UseStaticFiles` méthode d’extension ajoute le Gestionnaire de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="2e581-139">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="2e581-140">Comme mentionné précédemment, le runtime ASP.NET est modulaire, et vous devez explicitement s’abonner à des fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="2e581-140">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="2e581-141">Le `UseMvc` méthode d’extension ajoute le routage.</span><span class="sxs-lookup"><span data-stu-id="2e581-141">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="2e581-142">Pour plus d’informations, consultez [démarrage de l’Application](../fundamentals/startup.md) et [routage](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="2e581-142">For more information, see [Application Startup](../fundamentals/startup.md) and [Routing](../fundamentals/routing.md).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="2e581-143">Ajouter un contrôleur et une vue</span><span class="sxs-lookup"><span data-stu-id="2e581-143">Add a controller and view</span></span>

<span data-ttu-id="2e581-144">Dans cette section, vous allez ajouter un contrôleur minimale et la vue comme des espaces réservés pour le contrôleur ASP.NET MVC et les vues que vous allez migrer dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="2e581-144">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="2e581-145">Ajouter un *contrôleurs* dossier.</span><span class="sxs-lookup"><span data-stu-id="2e581-145">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="2e581-146">Ajouter un **classe de contrôleur MVC** portant le nom *HomeController.cs* à la *contrôleurs* dossier.</span><span class="sxs-lookup"><span data-stu-id="2e581-146">Add an **MVC controller class** with the name *HomeController.cs* to the *Controllers* folder.</span></span>

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="2e581-148">Ajouter un *vues* dossier.</span><span class="sxs-lookup"><span data-stu-id="2e581-148">Add a *Views* folder.</span></span>

* <span data-ttu-id="2e581-149">Ajouter un *Views/Home* dossier.</span><span class="sxs-lookup"><span data-stu-id="2e581-149">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="2e581-150">Ajouter un *Index.cshtml* page de vue MVC à le *Views/Home* dossier.</span><span class="sxs-lookup"><span data-stu-id="2e581-150">Add an *Index.cshtml* MVC view page to the *Views/Home* folder.</span></span>

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/view.png)

<span data-ttu-id="2e581-152">La structure de projet est indiquée ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="2e581-152">The project structure is shown below:</span></span>

![Explorateur de solutions affichant les fichiers et dossiers de WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="2e581-154">Remplacez le contenu de la *Views/Home/Index.cshtml* fichier avec les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="2e581-154">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="2e581-155">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="2e581-155">Run the app.</span></span>

![Application Web ouverte dans Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="2e581-157">Consultez [contrôleurs](../mvc/controllers/index.md) et [vues](../mvc/views/index.md) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="2e581-157">See [Controllers](../mvc/controllers/index.md) and [Views](../mvc/views/index.md) for more information.</span></span>

<span data-ttu-id="2e581-158">Maintenant que nous avons un projet ASP.NET Core minimale, nous pouvons commencer la migration des fonctionnalités à partir du projet ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="2e581-158">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="2e581-159">Vous devrez déplacer les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="2e581-159">We will need to move the following:</span></span>

* <span data-ttu-id="2e581-160">contenu du côté client (CSS, des polices et des scripts)</span><span class="sxs-lookup"><span data-stu-id="2e581-160">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="2e581-161">contrôleurs</span><span class="sxs-lookup"><span data-stu-id="2e581-161">controllers</span></span>

* <span data-ttu-id="2e581-162">vues</span><span class="sxs-lookup"><span data-stu-id="2e581-162">views</span></span>

* <span data-ttu-id="2e581-163">modèles</span><span class="sxs-lookup"><span data-stu-id="2e581-163">models</span></span>

* <span data-ttu-id="2e581-164">Regroupement</span><span class="sxs-lookup"><span data-stu-id="2e581-164">bundling</span></span>

* <span data-ttu-id="2e581-165">filtres</span><span class="sxs-lookup"><span data-stu-id="2e581-165">filters</span></span>

* <span data-ttu-id="2e581-166">Ouvrez une session en entrée/sortie, identité (cela est effectuée dans l’étape suivante du didacticiel.)</span><span class="sxs-lookup"><span data-stu-id="2e581-166">Log in/out, identity (This will be done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="2e581-167">Contrôleurs et vues</span><span class="sxs-lookup"><span data-stu-id="2e581-167">Controllers and views</span></span>

* <span data-ttu-id="2e581-168">Chacune des méthodes copier à partir de l’ASP.NET MVC `HomeController` au nouveau `HomeController`.</span><span class="sxs-lookup"><span data-stu-id="2e581-168">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="2e581-169">Notez que dans ASP.NET MVC, type de retour de méthode de modèle prédéfini contrôleur action est [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); dans ASP.NET MVC de base, les méthodes d’action retournés `IActionResult` à la place.</span><span class="sxs-lookup"><span data-stu-id="2e581-169">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="2e581-170">`ActionResult`implémente `IActionResult`, il est donc inutile de modifier le type de retour de vos méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="2e581-170">`ActionResult` implements `IActionResult`, so there is no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="2e581-171">Copie le *About.cshtml*, *Contact.cshtml*, et *Index.cshtml* Razor afficher les fichiers à partir du projet ASP.NET MVC au projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e581-171">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="2e581-172">Exécuter l’application ASP.NET Core et chaque méthode de test.</span><span class="sxs-lookup"><span data-stu-id="2e581-172">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="2e581-173">Nous n’avons pas encore effectué la migration l’ou les styles de mise en page, pour les vues de rendu ne contient que le contenu dans les fichiers de vue.</span><span class="sxs-lookup"><span data-stu-id="2e581-173">We haven't migrated the layout file or styles yet, so the rendered views will only contain the content in the view files.</span></span> <span data-ttu-id="2e581-174">Vous n’avez pas les liens de fichier généré de disposition pour le `About` et `Contact` consulte, afin d’avoir à les appeler à partir du navigateur (remplacez **4492** avec le numéro de port utilisé dans votre projet).</span><span class="sxs-lookup"><span data-stu-id="2e581-174">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Page de contact](mvc/_static/contact-page.png)

<span data-ttu-id="2e581-176">Notez l’absence de style et éléments de menu.</span><span class="sxs-lookup"><span data-stu-id="2e581-176">Note the lack of styling and menu items.</span></span> <span data-ttu-id="2e581-177">Qui sera résolu dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="2e581-177">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="2e581-178">Contenu statique</span><span class="sxs-lookup"><span data-stu-id="2e581-178">Static content</span></span>

<span data-ttu-id="2e581-179">Dans les versions précédentes d’ASP.NET MVC, contenu statique a été hébergé à partir de la racine du projet web et a été inséré avec fichiers côté serveur.</span><span class="sxs-lookup"><span data-stu-id="2e581-179">In previous versions of  ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="2e581-180">Dans ASP.NET Core, contenu statique est hébergé dans le *wwwroot* dossier.</span><span class="sxs-lookup"><span data-stu-id="2e581-180">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="2e581-181">Que vous souhaitez copier le contenu statique à partir de votre application ASP.NET MVC ancien le *wwwroot* dossier dans votre projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e581-181">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="2e581-182">Dans cette conversion exemple :</span><span class="sxs-lookup"><span data-stu-id="2e581-182">In this sample conversion:</span></span>

* <span data-ttu-id="2e581-183">Copie le *favicon.ico* fichier à partir de l’ancien projet MVC dans le *wwwroot* dossier dans le projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e581-183">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="2e581-184">L’ancien ASP.NET MVC project utilise [Bootstrap](http://getbootstrap.com/) pour que son style et le programme d’amorçage des fichiers dans le *contenu* et *Scripts* dossiers.</span><span class="sxs-lookup"><span data-stu-id="2e581-184">The old ASP.NET MVC project uses [Bootstrap](http://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="2e581-185">Le modèle, ce qui a généré l’ancien projet ASP.NET MVC, fait référence à l’amorçage dans le fichier de disposition (*Views/Shared/_Layout.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="2e581-185">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="2e581-186">Vous pouvez copier le *bootstrap.js* et *bootstrap.css* à partir de l’ASP.NET MVC, les fichiers de projet pour le *wwwroot* n’utilise pas de dossier dans le nouveau projet, mais cette approche le mécanisme améliorée pour la gestion des dépendances de côté client dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e581-186">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project, but that approach doesn't use the improved mechanism for managing client-side dependencies in ASP.NET Core.</span></span>

<span data-ttu-id="2e581-187">Dans le nouveau projet, nous allons ajouter la prise en charge des données d’amorçage (et d’autres bibliothèques côté client) à l’aide de [Bower](https://bower.io/):</span><span class="sxs-lookup"><span data-stu-id="2e581-187">In the new project, we'll add support for Bootstrap (and other client-side libraries) using [Bower](https://bower.io/):</span></span>

* <span data-ttu-id="2e581-188">Ajouter un [Bower](https://bower.io/) fichier de configuration nommé *bower.json* à la racine du projet (avec le bouton droit sur le projet, puis **Ajouter > nouvel élément > fichier de Configuration Bower**).</span><span class="sxs-lookup"><span data-stu-id="2e581-188">Add a [Bower](https://bower.io/) configuration file named *bower.json* to the project root (Right-click on the project, and then **Add > New Item > Bower Configuration File**).</span></span> <span data-ttu-id="2e581-189">Ajouter [Bootstrap](http://getbootstrap.com/) et [jQuery](https://jquery.com/) au fichier (voir les lignes en surbrillance ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="2e581-189">Add [Bootstrap](http://getbootstrap.com/) and [jQuery](https://jquery.com/) to the file (see the highlighted lines below).</span></span>

  [!code-json[Main](mvc/sample/bower.json?highlight=5-6)]

<span data-ttu-id="2e581-190">Lors de l’enregistrement du fichier, Bower télécharge automatiquement les dépendances à la *wwwroot/lib* dossier.</span><span class="sxs-lookup"><span data-stu-id="2e581-190">Upon saving the file, Bower will automatically download the dependencies to the *wwwroot/lib* folder.</span></span> <span data-ttu-id="2e581-191">Vous pouvez utiliser la **recherche l’Explorateur de solutions** pour rechercher le chemin d’accès des actifs :</span><span class="sxs-lookup"><span data-stu-id="2e581-191">You can use the **Search Solution Explorer** box to find the path of the assets:</span></span>

![ressources de jQuery apparaissent dans les résultats de recherche de l’Explorateur de solutions](mvc/_static/search.png)

<span data-ttu-id="2e581-193">Consultez [gérer les Packages côté Client avec Bower](../client-side/bower.md) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="2e581-193">See [Manage Client-Side Packages with Bower](../client-side/bower.md) for more information.</span></span>

<a name=migrate-layout-file></a>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="2e581-194">Migrer le fichier de disposition</span><span class="sxs-lookup"><span data-stu-id="2e581-194">Migrate the layout file</span></span>

* <span data-ttu-id="2e581-195">Copie le *_ViewStart.cshtml* fichier à partir de l’ancien projet ASP.NET MVC *vues* dossier dans le projet de ASP.NET Core *vues* dossier.</span><span class="sxs-lookup"><span data-stu-id="2e581-195">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="2e581-196">Le *_ViewStart.cshtml* fichier n’a pas changé dans ASP.NET MVC de base.</span><span class="sxs-lookup"><span data-stu-id="2e581-196">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="2e581-197">Créer un *Views/Shared* dossier.</span><span class="sxs-lookup"><span data-stu-id="2e581-197">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="2e581-198">*Facultatif :* copie *_ViewImports.cshtml* à partir de la *FullAspNetCore* du projet MVC *vues* dossier du projet de ASP.NET Core *Vues* dossier.</span><span class="sxs-lookup"><span data-stu-id="2e581-198">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="2e581-199">Supprimez toute déclaration d’espace de noms dans le *_ViewImports.cshtml* fichier.</span><span class="sxs-lookup"><span data-stu-id="2e581-199">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2e581-200">Le *_ViewImports.cshtml* fournit des espaces de noms pour tous les fichiers de l’affichage de fichiers et permet de bénéficier de [programmes d’assistance de balise](../mvc/views/tag-helpers/index.md).</span><span class="sxs-lookup"><span data-stu-id="2e581-200">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](../mvc/views/tag-helpers/index.md).</span></span> <span data-ttu-id="2e581-201">Programmes d’assistance de balise sont utilisés dans le nouveau fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="2e581-201">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="2e581-202">Le *_ViewImports.cshtml* fichier est une nouveauté pour ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e581-202">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="2e581-203">Copie le *_Layout.cshtml* fichier à partir de l’ancien projet ASP.NET MVC *Views/Shared* dossier dans le projet de ASP.NET Core *Views/Shared* dossier.</span><span class="sxs-lookup"><span data-stu-id="2e581-203">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="2e581-204">Ouvrez *_Layout.cshtml* et apportez les modifications suivantes (le code complet est indiqué ci-dessous) :</span><span class="sxs-lookup"><span data-stu-id="2e581-204">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

   * <span data-ttu-id="2e581-205">Remplacez `@Styles.Render("~/Content/css")` avec un `<link>` élément à charger *bootstrap.css* (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="2e581-205">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

   * <span data-ttu-id="2e581-206">Supprimez `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="2e581-206">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

   * <span data-ttu-id="2e581-207">Commentez la `@Html.Partial("_LoginPartial")` ligne (entourer la ligne de `@*...*@`).</span><span class="sxs-lookup"><span data-stu-id="2e581-207">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="2e581-208">Nous allons y revenir dans un didacticiel futures.</span><span class="sxs-lookup"><span data-stu-id="2e581-208">We'll return to it in a future tutorial.</span></span>

   * <span data-ttu-id="2e581-209">Remplacez `@Scripts.Render("~/bundles/jquery")` avec un `<script>` élément (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="2e581-209">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

   * <span data-ttu-id="2e581-210">Remplacez `@Scripts.Render("~/bundles/bootstrap")` avec un `<script>` élément (voir ci-dessous)...</span><span class="sxs-lookup"><span data-stu-id="2e581-210">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below)..</span></span>

<span data-ttu-id="2e581-211">Le lien CSS de remplacement :</span><span class="sxs-lookup"><span data-stu-id="2e581-211">The replacement CSS link:</span></span>

```html
<link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
```

<span data-ttu-id="2e581-212">Les balises de script de remplacement :</span><span class="sxs-lookup"><span data-stu-id="2e581-212">The replacement script tags:</span></span>

```html
<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
```

<span data-ttu-id="2e581-213">La mise à jour *_Layout.cshtml* fichier est présenté ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="2e581-213">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-html[Main](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7,27,39-40)]

<span data-ttu-id="2e581-214">Afficher le site dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="2e581-214">View the site in the browser.</span></span> <span data-ttu-id="2e581-215">Il doit maintenant charger correctement, avec les styles attendus en place.</span><span class="sxs-lookup"><span data-stu-id="2e581-215">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="2e581-216">*Facultatif :* vous souhaiterez peut-être essayer d’utiliser le nouveau fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="2e581-216">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="2e581-217">Pour ce projet, vous pouvez copier le fichier de disposition à partir de la *FullAspNetCore* projet.</span><span class="sxs-lookup"><span data-stu-id="2e581-217">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="2e581-218">Le nouveau fichier de mise en page utilise [programmes d’assistance de balise](../mvc/views/tag-helpers/index.md) et a d’autres améliorations.</span><span class="sxs-lookup"><span data-stu-id="2e581-218">The new layout file uses [Tag Helpers](../mvc/views/tag-helpers/index.md) and has other improvements.</span></span>

## <a name="configure-bundling--minification"></a><span data-ttu-id="2e581-219">Configurer le regroupement des & réduction</span><span class="sxs-lookup"><span data-stu-id="2e581-219">Configure Bundling & Minification</span></span>

<span data-ttu-id="2e581-220">Pour plus d’informations sur la configuration de groupement et la minimisation, consultez [Bundling and Minification](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="2e581-220">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solving-http-500-errors"></a><span data-ttu-id="2e581-221">Résolution des erreurs HTTP 500</span><span class="sxs-lookup"><span data-stu-id="2e581-221">Solving HTTP 500 errors</span></span>

<span data-ttu-id="2e581-222">Il existe de nombreux problèmes qui peuvent provoquer un message d’erreur HTTP 500 qui ne contiennent aucune information sur la source du problème.</span><span class="sxs-lookup"><span data-stu-id="2e581-222">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="2e581-223">Par exemple, si le *Views/_ViewImports.cshtml* fichier contient un espace de noms qui n’existe pas dans votre projet, vous obtiendrez une erreur HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="2e581-223">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="2e581-224">Pour obtenir un message d’erreur détaillé, ajoutez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2e581-224">To get a detailed error message, add the following code:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    if (env.IsDevelopment())
    {
         app.UseDeveloperExceptionPage();
    }

    app.UseStaticFiles();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="2e581-225">Consultez **à l’aide de la Page d’Exception Developer** dans [gestion des erreurs](../fundamentals/error-handling.md) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="2e581-225">See **Using the Developer Exception Page** in [Error Handling](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e581-226">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2e581-226">Additional Resources</span></span>

* [<span data-ttu-id="2e581-227">Développement côté client</span><span class="sxs-lookup"><span data-stu-id="2e581-227">Client-Side Development</span></span>](../client-side/index.md)

* [<span data-ttu-id="2e581-228">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="2e581-228">Tag Helpers</span></span>](../mvc/views/tag-helpers/index.md)

---
title: "Créer une API web avec ASP.NET Core et Visual Studio pour Windows"
author: rick-anderson
description: "Générer une API web avec ASP.NET Core MVC et Visual Studio pour Windows"
keywords: ASP.NET Core, APIweb, API web, REST, HTTP, Service, Service HTTP
ms.author: riande
manager: wpickett
ms.date: 08/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-web-api
ms.openlocfilehash: 617b11cd7652e393c06446c62138802e4a4e90df
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2017
---
#<a name="create-a-web-api-with-aspnet-core-and-visual-studio-for-windows"></a><span data-ttu-id="a0ff9-104">Créer une API web avec ASP.NET Core et Visual Studio pour Windows</span><span class="sxs-lookup"><span data-stu-id="a0ff9-104">Create a web API with ASP.NET Core and Visual Studio for Windows</span></span>

<span data-ttu-id="a0ff9-105">De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="a0ff9-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="a0ff9-106">Dans ce didacticiel, vous allez générer une API web pour la gestion d’une liste de tâches.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-106">In this tutorial, you’ll build a web API for managing a list of "to-do" items.</span></span> <span data-ttu-id="a0ff9-107">Vous ne générerez pas d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-107">You won’t build a UI.</span></span>

<span data-ttu-id="a0ff9-108">Il existe trois versions de ce didacticiel :</span><span class="sxs-lookup"><span data-stu-id="a0ff9-108">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="a0ff9-109">Windows : API web avec Visual Studio pour Windows (ce didacticiel)</span><span class="sxs-lookup"><span data-stu-id="a0ff9-109">Windows: Web API with Visual Studio for Windows (This tutorial)</span></span>
* <span data-ttu-id="a0ff9-110">macOS : [API web avec Visual Studio pour Mac](xref:tutorials/first-web-api-mac)</span><span class="sxs-lookup"><span data-stu-id="a0ff9-110">macOS: [Web API with Visual Studio for Mac](xref:tutorials/first-web-api-mac)</span></span>
* <span data-ttu-id="a0ff9-111">macOS, Linux, Windows : [API web avec Visual Studio Code](xref:tutorials/web-api-vsc)</span><span class="sxs-lookup"><span data-stu-id="a0ff9-111">macOS, Linux, Windows: [Web API with Visual Studio Code](xref:tutorials/web-api-vsc)</span></span>

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a><span data-ttu-id="a0ff9-112">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="a0ff9-112">Prerequisites</span></span>

[!INCLUDE[install 2.0](../includes/install2.0.md)]

<span data-ttu-id="a0ff9-113">Consultez [ce fichier PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) pour la version ASP.NET Core 1.1.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-113">See [this PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) for the ASP.NET Core 1.1 version.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="a0ff9-114">Créer le projet</span><span class="sxs-lookup"><span data-stu-id="a0ff9-114">Create the project</span></span>

<span data-ttu-id="a0ff9-115">Dans Visual Studio, sélectionnez **Fichier** Menu > **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-115">From Visual Studio, select **File** menu, > **New** > **Project**.</span></span>

<span data-ttu-id="a0ff9-116">Sélectionnez le modèle de projet **Application web ASP.NET Core (.NET Core)**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-116">Select the **ASP.NET Core Web Application (.NET Core)** project template.</span></span> <span data-ttu-id="a0ff9-117">Nommez le projet `TodoApi` et sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-117">Name the project `TodoApi` and select **OK**.</span></span>

![Boîte de dialogue Nouveau projet](first-web-api/_static/new-project.png)

<span data-ttu-id="a0ff9-119">Dans la boîte de dialogue **Nouvelle application web ASP.NET Core - TodoApi**, sélectionnez le modèle **API web**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-119">In the **New ASP.NET Core Web Application - TodoApi** dialog, select the **Web API** template.</span></span> <span data-ttu-id="a0ff9-120">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-120">Select **OK**.</span></span> <span data-ttu-id="a0ff9-121">Ne sélectionnez **pas** **Activer la prise en charge de Docker**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-121">Do **not** select **Enable Docker Support**.</span></span>

![Boîte de dialogue Nouvelle application web ASP.NET avec modèle de projet API web sélectionné parmi les modèles ASP.NET Core](first-web-api/_static/web-api-project.png)

### <a name="launch-the-app"></a><span data-ttu-id="a0ff9-123">Lancer l’application</span><span class="sxs-lookup"><span data-stu-id="a0ff9-123">Launch the app</span></span>

<span data-ttu-id="a0ff9-124">Dans Visual Studio, appuyez sur Ctrl+F5 pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-124">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="a0ff9-125">Visual Studio lance un navigateur et accède à `http://localhost:port/api/values`, où *port* est un numéro de port choisi au hasard.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-125">Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly-chosen port number.</span></span> <span data-ttu-id="a0ff9-126">Chrome, Edge et Firefox affichent ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a0ff9-126">Chrome, Edge, and Firefox display the following:</span></span>

```
["value1","value2"]
``` 

### <a name="add-a-model-class"></a><span data-ttu-id="a0ff9-127">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="a0ff9-127">Add a model class</span></span>

<span data-ttu-id="a0ff9-128">Un modèle est un objet qui représente les données dans votre application.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-128">A model is an object that represents the data in your application.</span></span> <span data-ttu-id="a0ff9-129">Ici, le seul modèle est une tâche à effectuer.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-129">In this case, the only model is a to-do item.</span></span>

<span data-ttu-id="a0ff9-130">Ajoutez un dossier nommé « Models ».</span><span class="sxs-lookup"><span data-stu-id="a0ff9-130">Add a folder named "Models".</span></span> <span data-ttu-id="a0ff9-131">Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-131">In Solution Explorer, right-click the project.</span></span> <span data-ttu-id="a0ff9-132">Sélectionnez **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-132">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="a0ff9-133">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-133">Name the folder *Models*.</span></span>

<span data-ttu-id="a0ff9-134">Remarque : Vous pouvez placer des classes de modèle n’importe où dans votre projet, mais le dossier *Models* est utilisé par convention.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-134">Note: The model classes go anywhere in your project, but the *Models* folder is used by convention.</span></span>

<span data-ttu-id="a0ff9-135">Ajoutez une classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-135">Add a `TodoItem` class.</span></span> <span data-ttu-id="a0ff9-136">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-136">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="a0ff9-137">Nommez la classe `TodoItem` et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-137">Name the class `TodoItem` and select **Add**.</span></span>

<span data-ttu-id="a0ff9-138">Remplacez le code généré par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a0ff9-138">Replace the generated code with the following:</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="a0ff9-139">La base de données génère le `Id` quand un `TodoItem` est créé.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-139">The database generates the `Id` when a `TodoItem` is created.</span></span>

### <a name="create-the-database-context"></a><span data-ttu-id="a0ff9-140">Créer le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="a0ff9-140">Create the database context</span></span>

<span data-ttu-id="a0ff9-141">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données spécifié.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-141">The *database context* is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="a0ff9-142">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-142">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

<span data-ttu-id="a0ff9-143">Ajoutez une classe `TodoContext`.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-143">Add a `TodoContext` class.</span></span> <span data-ttu-id="a0ff9-144">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-144">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="a0ff9-145">Nommez la classe `TodoContext` et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-145">Name the class `TodoContext` and select **Add**.</span></span>

<span data-ttu-id="a0ff9-146">Remplacez le code généré par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a0ff9-146">Replace the generated code with the following:</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a><span data-ttu-id="a0ff9-147">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="a0ff9-147">Add a controller</span></span>

<span data-ttu-id="a0ff9-148">Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-148">In Solution Explorer, right-click the *Controllers* folder.</span></span> <span data-ttu-id="a0ff9-149">Sélectionnez **Ajouter** > **Nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-149">Select **Add** > **New Item**.</span></span> <span data-ttu-id="a0ff9-150">Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez le modèle **Classe de contrôleur des API web**.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-150">In the **Add New Item** dialog, select the **Web  API Controller Class** template.</span></span> <span data-ttu-id="a0ff9-151">Nommez la classe `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-151">Name the class `TodoController`.</span></span>

![Boîte de dialogue Ajouter un nouvel élément avec contrôleur dans la zone de recherche et le contrôleur des API web sélectionné](first-web-api/_static/new_controller.png)

<span data-ttu-id="a0ff9-153">Remplacez le code généré par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a0ff9-153">Replace the generated code with the following:</span></span>

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]
  
### <a name="launch-the-app"></a><span data-ttu-id="a0ff9-154">Lancer l’application</span><span class="sxs-lookup"><span data-stu-id="a0ff9-154">Launch the app</span></span>

<span data-ttu-id="a0ff9-155">Dans Visual Studio, appuyez sur Ctrl+F5 pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-155">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="a0ff9-156">Visual Studio lance un navigateur et accède à `http://localhost:port/api/values`, où *port* est un numéro de port choisi au hasard.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-156">Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly chosen port number.</span></span> <span data-ttu-id="a0ff9-157">Si vous utilisez Chrome, Edge ou Firefox, les données sont affichées.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-157">If you're using Chrome, Edge or Firefox, the data will be displayed.</span></span> <span data-ttu-id="a0ff9-158">Si vous utilisez Internet Explorer, ce dernier vous invite à ouvrir ou à enregistrer le fichier *values.json*.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-158">If you're using IE, IE will prompt to you open or save the *values.json* file.</span></span> <span data-ttu-id="a0ff9-159">Accédez au contrôleur `Todo` que nous venons de créer `http://localhost:port/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="a0ff9-159">Navigate to the `Todo` controller we just created `http://localhost:port/api/todo`.</span></span>

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]


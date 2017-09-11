---
title: "Créer une API web avec ASP.NET Core et Visual Studio pour Mac"
author: rick-anderson
description: "Créer une API web avec ASP.NET Core MVC et Visual Studio pour Mac"
keywords: ASP.NET Core, APIweb, API web, REST, mac, macOS, HTTP, Service, Service HTTP
ms.author: riande
manager: wpickett
ms.date: 5/24/2017
ms.topic: get-started-article
ms.assetid: 830b4af5-ed14-1638-7734-764a6f13a8f6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-web-api-mac
ms.openlocfilehash: 08619d3b4ab2d6fdb04794dcbafac0b696dd8504
ms.sourcegitcommit: 3273675dad5ac3e1dc1c589938b73db3f7d6660a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2017
---
# <a name="create-a-web-api-with-aspnet-core-mvc-and-visual-studio-for-mac"></a><span data-ttu-id="cdd53-104">Créer une API web avec ASP.NET Core MVC et Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cdd53-104">Create a Web API with ASP.NET Core MVC and Visual Studio for Mac</span></span>

<span data-ttu-id="cdd53-105">De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="cdd53-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="cdd53-106">Dans ce didacticiel, vous allez générer une API web pour la gestion d’une liste de tâches.</span><span class="sxs-lookup"><span data-stu-id="cdd53-106">In this tutorial, you’ll build a web API for managing a list of "to-do" items.</span></span> <span data-ttu-id="cdd53-107">Vous ne générerez pas d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cdd53-107">You won’t build a UI.</span></span>

<span data-ttu-id="cdd53-108">Il existe trois versions de ce didacticiel :</span><span class="sxs-lookup"><span data-stu-id="cdd53-108">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="cdd53-109">macOS : API web avec Visual Studio pour Mac (ce didacticiel)</span><span class="sxs-lookup"><span data-stu-id="cdd53-109">macOS: Web API with Visual Studio for Mac (This tutorial)</span></span>
* <span data-ttu-id="cdd53-110">Windows : [API web avec Visual Studio pour Windows](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="cdd53-110">Windows: [Web API with Visual Studio for Windows](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="cdd53-111">macOS, Linux, Windows : [API web avec Visual Studio Code](xref:tutorials/web-api-vsc)</span><span class="sxs-lookup"><span data-stu-id="cdd53-111">macOS, Linux, Windows: [Web API with Visual Studio Code](xref:tutorials/web-api-vsc)</span></span>

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[template files](../includes/webApi/intro.md)]

* <span data-ttu-id="cdd53-112">Pour obtenir un exemple qui utilise une base de données persistante, consultez [Introduction à ASP.NET Core MVC sur Mac ou Linux](xref:tutorials/first-mvc-app-xplat/index).</span><span class="sxs-lookup"><span data-stu-id="cdd53-112">See [Introduction to ASP.NET Core MVC on Mac or Linux](xref:tutorials/first-mvc-app-xplat/index) for an example that uses a persistent database.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cdd53-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="cdd53-113">Prerequisites</span></span>

<span data-ttu-id="cdd53-114">Installez les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="cdd53-114">Install the following:</span></span>

- [<span data-ttu-id="cdd53-115">SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdd53-115">.NET Core SDK</span></span>](https://www.microsoft.com/net/core#macos)  
- [<span data-ttu-id="cdd53-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cdd53-116">Visual Studio for Mac</span></span>](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-the-project"></a><span data-ttu-id="cdd53-117">Créer le projet</span><span class="sxs-lookup"><span data-stu-id="cdd53-117">Create the project</span></span>

<span data-ttu-id="cdd53-118">Dans Visual Studio, sélectionnez **Fichier > Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="cdd53-118">From Visual Studio, select **File > New Solution**.</span></span>

![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

<span data-ttu-id="cdd53-120">Sélectionnez **Application .NET Core > API web ASP.NET Core > Suivant**.</span><span class="sxs-lookup"><span data-stu-id="cdd53-120">Select **.NET Core App >  ASP.NET Core Web API > Next**.</span></span>

![macOS - Boîte de dialogue Nouveau projet](first-web-api-mac/_static/1.png)

<span data-ttu-id="cdd53-122">Entrez **TodoApi** comme **Nom du projet**, puis sélectionnez Créer.</span><span class="sxs-lookup"><span data-stu-id="cdd53-122">Enter **TodoApi** for the **Project Name**, and then select Create.</span></span>

![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

### <a name="launch-the-app"></a><span data-ttu-id="cdd53-124">Lancer l’application</span><span class="sxs-lookup"><span data-stu-id="cdd53-124">Launch the app</span></span>

<span data-ttu-id="cdd53-125">Dans Visual Studio, sélectionnez **Exécuter > Démarrer avec débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd53-125">In Visual Studio, select **Run > Start With Debugging** to launch the app.</span></span> <span data-ttu-id="cdd53-126">Visual Studio lance un navigateur et accède à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="cdd53-126">Visual Studio launches a browser and navigates to `http://localhost:5000`.</span></span> <span data-ttu-id="cdd53-127">Vous obtenez une erreur HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="cdd53-127">You get an HTTP 404 (Not Found) error.</span></span>  <span data-ttu-id="cdd53-128">Remplacez l’URL par `http://localhost:port/api/values`.</span><span class="sxs-lookup"><span data-stu-id="cdd53-128">Change the URL to `http://localhost:port/api/values`.</span></span> <span data-ttu-id="cdd53-129">Les données `ValuesController` s’affichent :</span><span class="sxs-lookup"><span data-stu-id="cdd53-129">The `ValuesController` data will be displayed:</span></span>

```
["value1","value2"]
```

### <a name="add-support-for-entity-framework-core"></a><span data-ttu-id="cdd53-130">Ajouter la prise en charge d’Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="cdd53-130">Add support for Entity Framework Core</span></span>

<span data-ttu-id="cdd53-131">Installez le fournisseur de base de données [Entity Framework Core InMemory](https://docs.microsoft.com/ef/core/providers/in-memory/).</span><span class="sxs-lookup"><span data-stu-id="cdd53-131">Install the [Entity Framework Core InMemory](https://docs.microsoft.com/ef/core/providers/in-memory/) database provider.</span></span> <span data-ttu-id="cdd53-132">Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="cdd53-132">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span>

* <span data-ttu-id="cdd53-133">Dans le menu **Projet**, choisissez **Ajouter des packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="cdd53-133">From the **Project** menu, select **Add NuGet Packages**.</span></span> 

  *  <span data-ttu-id="cdd53-134">Vous pouvez aussi cliquer sur **Dépendances**, puis sélectionner **Ajouter des packages**.</span><span class="sxs-lookup"><span data-stu-id="cdd53-134">Alternately, you can right-click **Dependencies**, and then select **Add Packages**.</span></span>

* <span data-ttu-id="cdd53-135">Entrez `EntityFrameworkCore.InMemory` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="cdd53-135">Enter `EntityFrameworkCore.InMemory` in the search box.</span></span>
* <span data-ttu-id="cdd53-136">Sélectionnez `Microsoft.EntityFrameworkCore.InMemory`, puis **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="cdd53-136">Select `Microsoft.EntityFrameworkCore.InMemory`, and then select **Add Package**.</span></span>

### <a name="add-a-model-class"></a><span data-ttu-id="cdd53-137">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="cdd53-137">Add a model class</span></span>

<span data-ttu-id="cdd53-138">Un modèle est un objet qui représente les données dans votre application.</span><span class="sxs-lookup"><span data-stu-id="cdd53-138">A model is an object that represents the data in your application.</span></span> <span data-ttu-id="cdd53-139">Ici, le seul modèle est une tâche à effectuer.</span><span class="sxs-lookup"><span data-stu-id="cdd53-139">In this case, the only model is a to-do item.</span></span>

<span data-ttu-id="cdd53-140">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="cdd53-140">Add a folder named *Models*.</span></span> <span data-ttu-id="cdd53-141">Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="cdd53-141">In Solution Explorer, right-click the project.</span></span> <span data-ttu-id="cdd53-142">Sélectionnez **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="cdd53-142">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="cdd53-143">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="cdd53-143">Name the folder *Models*.</span></span>

![nouveau dossier](first-web-api-mac/_static/folder.png)

<span data-ttu-id="cdd53-145">Remarque : Vous pouvez placer des classes de modèle n’importe où dans votre projet, mais le dossier *Models* est utilisé par convention.</span><span class="sxs-lookup"><span data-stu-id="cdd53-145">Note: You can put model classes anywhere in your project, but the *Models* folder is used by convention.</span></span>

<span data-ttu-id="cdd53-146">Ajoutez une classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="cdd53-146">Add a `TodoItem` class.</span></span> <span data-ttu-id="cdd53-147">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter > Nouveau fichier > Général > Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="cdd53-147">Right-click the *Models* folder and select **Add > New File > General > Empty Class**.</span></span> <span data-ttu-id="cdd53-148">Nommez la classe `TodoItem`, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="cdd53-148">Name the class `TodoItem`, and then select **New**.</span></span>

<span data-ttu-id="cdd53-149">Remplacez le code généré par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="cdd53-149">Replace the generated code with:</span></span>

<span data-ttu-id="cdd53-150">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]</span><span class="sxs-lookup"><span data-stu-id="cdd53-150">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]</span></span>

<span data-ttu-id="cdd53-151">La base de données génère le `Id` quand un `TodoItem` est créé.</span><span class="sxs-lookup"><span data-stu-id="cdd53-151">The database generates the `Id` when a `TodoItem` is created.</span></span>

### <a name="create-the-database-context"></a><span data-ttu-id="cdd53-152">Créer le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="cdd53-152">Create the database context</span></span>

<span data-ttu-id="cdd53-153">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données spécifié.</span><span class="sxs-lookup"><span data-stu-id="cdd53-153">The *database context* is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="cdd53-154">Vous créez cette classe en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="cdd53-154">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

<span data-ttu-id="cdd53-155">Ajoutez une classe `TodoContext` au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="cdd53-155">Add a `TodoContext` class to the *Models* folder.</span></span>

<span data-ttu-id="cdd53-156">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]</span><span class="sxs-lookup"><span data-stu-id="cdd53-156">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]</span></span>

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

## <a name="add-a-controller"></a><span data-ttu-id="cdd53-157">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="cdd53-157">Add a controller</span></span>

<span data-ttu-id="cdd53-158">Dans l’Explorateur de solutions, dans le dossier *Contrôleurs*, ajoutez la classe `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="cdd53-158">In Solution Explorer, in the *Controllers* folder, add the class `TodoController`.</span></span>

<span data-ttu-id="cdd53-159">Remplacez le code généré par ce qui suit (et ajoutez des accolades fermantes) :</span><span class="sxs-lookup"><span data-stu-id="cdd53-159">Replace the generated code with the following (and add closing braces):</span></span>

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a><span data-ttu-id="cdd53-160">Lancer l’application</span><span class="sxs-lookup"><span data-stu-id="cdd53-160">Launch the app</span></span>

<span data-ttu-id="cdd53-161">Dans Visual Studio, sélectionnez **Exécuter > Démarrer avec débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd53-161">In Visual Studio, select **Run > Start With Debugging** to launch the app.</span></span> <span data-ttu-id="cdd53-162">Visual Studio lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi au hasard.</span><span class="sxs-lookup"><span data-stu-id="cdd53-162">Visual Studio launches a browser and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span> <span data-ttu-id="cdd53-163">Vous obtenez une erreur HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="cdd53-163">You get an HTTP 404 (Not Found) error.</span></span>  <span data-ttu-id="cdd53-164">Remplacez l’URL par `http://localhost:port/api/values`.</span><span class="sxs-lookup"><span data-stu-id="cdd53-164">Change the URL to `http://localhost:port/api/values`.</span></span> <span data-ttu-id="cdd53-165">Les données `ValuesController` s’affichent :</span><span class="sxs-lookup"><span data-stu-id="cdd53-165">The `ValuesController` data will be displayed:</span></span>

```
["value1","value2"]
```

<span data-ttu-id="cdd53-166">Accédez au contrôleur `Todo` à l’adresse `http://localhost:port/api/todo` :</span><span class="sxs-lookup"><span data-stu-id="cdd53-166">Navigate to the `Todo` controller at`http://localhost:port/api/todo`:</span></span>

```
[{"key":1,"name":"Item1","isComplete":false}]
```

## <a name="implement-the-other-crud-operations"></a><span data-ttu-id="cdd53-167">Implémenter les autres opérations CRUD</span><span class="sxs-lookup"><span data-stu-id="cdd53-167">Implement the other CRUD operations</span></span>

<span data-ttu-id="cdd53-168">Nous allons ajouter des méthodes `Create`, `Update` et `Delete` au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cdd53-168">We'll add `Create`, `Update`, and `Delete` methods to the controller.</span></span> <span data-ttu-id="cdd53-169">Comme il s’agit de variations sur un thème, je vais simplement montrer le code et mettre en évidence les principales différences.</span><span class="sxs-lookup"><span data-stu-id="cdd53-169">These are variations on a theme, so I'll just show the code and highlight the main differences.</span></span> <span data-ttu-id="cdd53-170">Générez le projet après avoir ajouté ou modifier du code.</span><span class="sxs-lookup"><span data-stu-id="cdd53-170">Build the project after adding or changing code.</span></span>

### <a name="create"></a><span data-ttu-id="cdd53-171">Create</span><span class="sxs-lookup"><span data-stu-id="cdd53-171">Create</span></span>

<span data-ttu-id="cdd53-172">[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]</span><span class="sxs-lookup"><span data-stu-id="cdd53-172">[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]</span></span>

<span data-ttu-id="cdd53-173">Il s’agit d’une méthode HTTP POST, indiquée par l’attribut [`[HttpPost]`](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/HttpPostAttribute/index.html).</span><span class="sxs-lookup"><span data-stu-id="cdd53-173">This is an HTTP POST method, indicated by the [`[HttpPost]`](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/HttpPostAttribute/index.html) attribute.</span></span> <span data-ttu-id="cdd53-174">L’attribut [`[FromBody]`](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/FromBodyAttribute/index.html) indique à MVC qu’il faut obtenir la tâche à partir du corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="cdd53-174">The [`[FromBody]`](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/FromBodyAttribute/index.html) attribute tells MVC to get the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="cdd53-175">La méthode `CreatedAtRoute` retourne une réponse 201, qui constitue la réponse standard pour une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="cdd53-175">The `CreatedAtRoute` method returns a 201 response, which is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="cdd53-176">`CreatedAtRoute` ajoute également un en-tête Location dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="cdd53-176">`CreatedAtRoute` also adds a Location header to the response.</span></span> <span data-ttu-id="cdd53-177">L’en-tête Location spécifie l’URI de la tâche qui vient d’être créée.</span><span class="sxs-lookup"><span data-stu-id="cdd53-177">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="cdd53-178">Voir [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="cdd53-178">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

### <a name="use-postman-to-send-a-create-request"></a><span data-ttu-id="cdd53-179">Utiliser Postman pour envoyer une requête Create</span><span class="sxs-lookup"><span data-stu-id="cdd53-179">Use Postman to send a Create request</span></span>

* <span data-ttu-id="cdd53-180">Démarrez l’application (**Exécuter > Démarrer avec débogage**).</span><span class="sxs-lookup"><span data-stu-id="cdd53-180">Start the app (**Run > Start With Debugging**).</span></span>
* <span data-ttu-id="cdd53-181">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="cdd53-181">Start Postman.</span></span>

![Console Postman](first-web-api/_static/pmc.png)

* <span data-ttu-id="cdd53-183">Affectez la valeur `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="cdd53-183">Set the HTTP method to `POST`</span></span>
* <span data-ttu-id="cdd53-184">Sélectionnez la case d’option **Body**.</span><span class="sxs-lookup"><span data-stu-id="cdd53-184">Select the **Body** radio button</span></span>
* <span data-ttu-id="cdd53-185">Sélectionnez la case d’option **raw**.</span><span class="sxs-lookup"><span data-stu-id="cdd53-185">Select the **raw** radio button</span></span>
* <span data-ttu-id="cdd53-186">Sélectionnez JSON comme type.</span><span class="sxs-lookup"><span data-stu-id="cdd53-186">Set the type to JSON</span></span>
* <span data-ttu-id="cdd53-187">Dans l’éditeur de valeur de clé, entrez une tâche telle que</span><span class="sxs-lookup"><span data-stu-id="cdd53-187">In the key-value editor, enter a Todo item such as</span></span>

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* <span data-ttu-id="cdd53-188">Sélectionnez **Send**.</span><span class="sxs-lookup"><span data-stu-id="cdd53-188">Select **Send**</span></span>

* <span data-ttu-id="cdd53-189">Sélectionnez l’onglet Headers dans le volet inférieur et copiez l’en-tête **Location** :</span><span class="sxs-lookup"><span data-stu-id="cdd53-189">Select the Headers tab in the lower pane and copy the **Location** header:</span></span>

![Onglet Headers de la console Postman](first-web-api/_static/pmget.png)

<span data-ttu-id="cdd53-191">Vous pouvez utiliser l’URI d’en-tête Location pour accéder à la ressource que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="cdd53-191">You can use the Location header URI to access the resource you just created.</span></span> <span data-ttu-id="cdd53-192">Rappelez la méthode `GetById` qui a créé l’itinéraire nommé `"GetTodo"` :</span><span class="sxs-lookup"><span data-stu-id="cdd53-192">Recall the `GetById` method created the `"GetTodo"` named route:</span></span>

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(string id)
```

### <a name="update"></a><span data-ttu-id="cdd53-193">Update</span><span class="sxs-lookup"><span data-stu-id="cdd53-193">Update</span></span>

<span data-ttu-id="cdd53-194">[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]</span><span class="sxs-lookup"><span data-stu-id="cdd53-194">[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]</span></span>

<span data-ttu-id="cdd53-195">`Update` est semblable à `Create`, mais utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="cdd53-195">`Update` is similar to `Create`, but uses HTTP PUT.</span></span> <span data-ttu-id="cdd53-196">La réponse est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="cdd53-196">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="cdd53-197">D’après la spécification HTTP, une requête PUT exige que le client envoie l’entité mise à jour entière, et pas seulement les deltas.</span><span class="sxs-lookup"><span data-stu-id="cdd53-197">According to the HTTP spec, a PUT request requires the client to send the entire updated entity, not just the deltas.</span></span> <span data-ttu-id="cdd53-198">Pour prendre en charge les mises à jour partielles, utilisez HTTP PATCH.</span><span class="sxs-lookup"><span data-stu-id="cdd53-198">To support partial updates, use HTTP PATCH.</span></span>

```json
{
  "key": 1,
  "name": "walk dog",
  "isComplete": true
}
```

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmcput.png)

### <a name="delete"></a><span data-ttu-id="cdd53-200">Delete</span><span class="sxs-lookup"><span data-stu-id="cdd53-200">Delete</span></span>

<span data-ttu-id="cdd53-201">[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]</span><span class="sxs-lookup"><span data-stu-id="cdd53-201">[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]</span></span>

<span data-ttu-id="cdd53-202">La réponse est [204 (Pas de contenu)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="cdd53-202">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmd.png)

## <a name="next-steps"></a><span data-ttu-id="cdd53-204">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="cdd53-204">Next steps</span></span>

* [<span data-ttu-id="cdd53-205">Routage vers les actions de contrôleur</span><span class="sxs-lookup"><span data-stu-id="cdd53-205">Routing to Controller Actions</span></span>](xref:mvc/controllers/routing)
* <span data-ttu-id="cdd53-206">Pour plus d’informations sur le déploiement de votre API, consultez [Publication et déploiement](../publishing/index.md).</span><span class="sxs-lookup"><span data-stu-id="cdd53-206">For information about deploying your API, see [Publishing and Deployment](../publishing/index.md).</span></span>
* [<span data-ttu-id="cdd53-207">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="cdd53-207">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample)
* [<span data-ttu-id="cdd53-208">Postman</span><span class="sxs-lookup"><span data-stu-id="cdd53-208">Postman</span></span>](https://www.getpostman.com/)
* [<span data-ttu-id="cdd53-209">Fiddler</span><span class="sxs-lookup"><span data-stu-id="cdd53-209">Fiddler</span></span>](http://www.fiddler2.com/fiddler2/)

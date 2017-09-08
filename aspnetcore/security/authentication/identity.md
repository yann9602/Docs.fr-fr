---
title: "Introduction à l’identité sur ASP.NET Core"
author: rick-anderson
description: "À l’aide d’identité avec une application ASP.NET Core"
keywords: "Autorisation ASP.NET Core, identité, sécurité"
ms.author: riande
manager: wpickett
ms.date: 7/7/2017
ms.topic: article
ms.assetid: cf119f21-1a2b-49a2-b052-547ccb66ee83
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity
ms.openlocfilehash: 5718336868f3ee5ab08162ae2bc885c695d19a1d
ms.sourcegitcommit: f3366461010da37981cf7fc092b9b9613eb4ca89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2017
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="bbc4d-104">Introduction à l’identité sur ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbc4d-104">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="bbc4d-105">Par [Pranav Rastogi](https://github.com/rustd), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), Jon Galloway, [Erik Reitan](https://github.com/Erikre), et [Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="bbc4d-105">By [Pranav Rastogi](https://github.com/rustd), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), Jon Galloway, [Erik Reitan](https://github.com/Erikre), and [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="bbc4d-106">Identité de ASP.NET Core est un système d’appartenance qui vous permet d’ajouter des fonctionnalités de connexion à votre application.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-106">ASP.NET Core Identity is a membership system which allows you to add login functionality to your application.</span></span> <span data-ttu-id="bbc4d-107">Les utilisateurs peuvent créer une connexion et un compte avec un nom d’utilisateur et mot de passe, ou ils peuvent utiliser un fournisseur de connexion externe tels que Facebook, Google, Microsoft Account, Twitter ou d’autres.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-107">Users can create an account and login with a user name and password or they can use an external login provider such as Facebook, Google, Microsoft Account, Twitter or others.</span></span>

<span data-ttu-id="bbc4d-108">Vous pouvez configurer l’identité du principal ASP.NET pour utiliser une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-108">You can configure ASP.NET Core Identity to use a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="bbc4d-109">Vous pouvez également utiliser votre propre magasin persistant, par exemple le stockage de Table Azure.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-109">Alternatively, you can use your own persistent store, for example Azure Table Storage.</span></span> <span data-ttu-id="bbc4d-110">Ce document contient des instructions pour Visual Studio et à l’aide de l’interface CLI.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-110">This document contains instructions for Visual Studio and for using the CLI.</span></span>

## <a name="overview-of-identity"></a><span data-ttu-id="bbc4d-111">Vue d’ensemble de l’identité</span><span class="sxs-lookup"><span data-stu-id="bbc4d-111">Overview of Identity</span></span>

<span data-ttu-id="bbc4d-112">Dans cette rubrique, vous allez apprendre à utiliser ASP.NET Core Identity pour ajouter des fonctionnalités pour vous inscrire, connectez-vous et déconnecter un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-112">In this topic, you'll learn how to use ASP.NET Core Identity to add functionality to register, log in, and log out a user.</span></span> <span data-ttu-id="bbc4d-113">Pour obtenir des instructions plus détaillées sur la création d’applications à l’aide d’ASP.NET Core Identity, consultez la section étapes suivantes à la fin de cet article.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-113">For more detailed instructions about creating apps using ASP.NET Core Identity, see the Next Steps section at the end of this article.</span></span>

1.  <span data-ttu-id="bbc4d-114">Créez un projet d’Application ASP.NET Core Web avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-114">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

    # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bbc4d-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbc4d-115">Visual Studio</span></span>](#tab/visual-studio)
    <span data-ttu-id="bbc4d-116">Dans Visual Studio, sélectionnez **fichier** -> **nouveau** -> **projet**.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-116">In Visual Studio, select **File** -> **New** -> **Project**.</span></span> <span data-ttu-id="bbc4d-117">Sélectionnez le **Application Web ASP.NET** à partir de la **nouveau projet** boîte de dialogue.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-117">Select the **ASP.NET Web Application** from the **New Project** dialog box.</span></span> <span data-ttu-id="bbc4d-118">En sélectionnant un ASP.NET Core **Application Web** avec **comptes d’utilisateur individuels** comme méthode d’authentification.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-118">Selecting an ASP.NET Core **Web Application** with **Individual User Accounts** as the authentication method.</span></span>

    <span data-ttu-id="bbc4d-119">Remarque : Vous devez sélectionner **comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-119">Note: You must select **Individual User Accounts**.</span></span>
 
    ![Boîte de dialogue Nouveau projet](identity/_static/01-mvc.png)
    
    # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bbc4d-121">.NET core CLI</span><span class="sxs-lookup"><span data-stu-id="bbc4d-121">.NET Core CLI</span></span>](#tab/netcore-cli)
    <span data-ttu-id="bbc4d-122">Si vous utilisez l’interface de ligne de base de .NET, créer le projet à l’aide ``dotnet new mvc --auth Individual``.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-122">If using the .NET Core CLI, create the new project using ``dotnet new mvc --auth Individual``.</span></span> <span data-ttu-id="bbc4d-123">Cela crée un nouveau projet avec le même code de modèle d’identité crée de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-123">This will create a new project with the same Identity template code Visual Studio creates.</span></span>
 
    <span data-ttu-id="bbc4d-124">Le projet créé contient le `Microsoft.AspNetCore.Identity.EntityFrameworkCore` package, qui rendront les données d’identité et le schéma à l’aide de SQL Server [Entity Framework Core](https://docs.efproject.net).</span><span class="sxs-lookup"><span data-stu-id="bbc4d-124">The created project contains the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` package, which will persist the Identity data and schema to SQL Server using [Entity Framework Core](https://docs.efproject.net).</span></span>
    
    ---
 
2.  <span data-ttu-id="bbc4d-125">Configurer les services d’identité et d’ajouter l’intergiciel (middleware) dans `Startup`.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-125">Configure Identity services and add middleware in `Startup`.</span></span>

    <span data-ttu-id="bbc4d-126">Les services d’identité sont ajoutés à l’application dans le `ConfigureServices` méthode dans la `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="bbc4d-126">The Identity services are added to the application in the `ConfigureServices` method in the `Startup` class:</span></span>
 
    <span data-ttu-id="bbc4d-127">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=configureservices&highlight=7-9,13-34)]</span><span class="sxs-lookup"><span data-stu-id="bbc4d-127">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=configureservices&highlight=7-9,13-34)]</span></span>
    
    <span data-ttu-id="bbc4d-128">Ces services sont accessibles à l’application via [injection de dépendance](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bbc4d-128">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
 
    <span data-ttu-id="bbc4d-129">Identité est activée pour l’application en appelant `UseIdentity` dans le `Configure` (méthode).</span><span class="sxs-lookup"><span data-stu-id="bbc4d-129">Identity is enabled for the application by calling  `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="bbc4d-130">`UseIdentity`Ajoute l’authentification par cookie [intergiciel (middleware)](xref:fundamentals/middleware) au pipeline de demande.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-130">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware) to the request pipeline.</span></span>
 
    <span data-ttu-id="bbc4d-131">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=configure&highlight=21)]</span><span class="sxs-lookup"><span data-stu-id="bbc4d-131">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=configure&highlight=21)]</span></span>
 
    <span data-ttu-id="bbc4d-132">Pour plus d’informations sur le démarrage de l’application des processus, consultez [démarrage de l’Application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="bbc4d-132">For more information about the application start up process, see [Application Startup](xref:fundamentals/startup).</span></span>

3.  <span data-ttu-id="bbc4d-133">Créez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-133">Create a user.</span></span>
 
    <span data-ttu-id="bbc4d-134">Lancer l’application, puis cliquez sur le **inscrire** lien.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-134">Launch the application and then click on the **Register** link.</span></span>

    <span data-ttu-id="bbc4d-135">S’il s’agit de la première fois que vous effectuez cette action, vous serez peut-être requis pour exécuter des migrations.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-135">If this is the first time you're performing this action, you may be required to run migrations.</span></span> <span data-ttu-id="bbc4d-136">L’application vous invite à **s’appliquent les Migrations**:</span><span class="sxs-lookup"><span data-stu-id="bbc4d-136">The application prompts you to **Apply Migrations**:</span></span>
    
    ![Appliquer la Page Web de Migrations](identity/_static/apply-migrations.png)
    
    <span data-ttu-id="bbc4d-138">Ou bien, vous pouvez tester à l’aide d’ASP.NET Core Identity avec votre application sans une base de données persistantes à l’aide d’une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-138">Alternately, you can test using ASP.NET Core Identity with your app without a persistent database by using an in-memory database.</span></span> <span data-ttu-id="bbc4d-139">Pour utiliser une base de données en mémoire, ajoutez le ``Microsoft.EntityFrameworkCore.InMemory`` le package à votre application et de modifier l’appel de votre application à ``AddDbContext`` dans ``ConfigureServices`` comme suit :</span><span class="sxs-lookup"><span data-stu-id="bbc4d-139">To use an in-memory database, add the ``Microsoft.EntityFrameworkCore.InMemory`` package to your app and modify your app's call to ``AddDbContext`` in ``ConfigureServices`` as follows:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseInMemoryDatabase(Guid.NewGuid().ToString()));
    ```
    
    <span data-ttu-id="bbc4d-140">Lorsque l’utilisateur clique sur le **inscrire** lien, le ``Register`` action est appelée sur ``AccountController``.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-140">When the user clicks the **Register** link, the ``Register`` action is invoked on ``AccountController``.</span></span> <span data-ttu-id="bbc4d-141">Le ``Register`` action crée l’utilisateur en appelant `CreateAsync` sur la `_userManager` objet (fourni à ``AccountController`` par injection de dépendances) :</span><span class="sxs-lookup"><span data-stu-id="bbc4d-141">The ``Register`` action creates the user by calling `CreateAsync` on the  `_userManager` object (provided to ``AccountController`` by dependency injection):</span></span>
 
    <span data-ttu-id="bbc4d-142">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=register&highlight=11)]</span><span class="sxs-lookup"><span data-stu-id="bbc4d-142">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=register&highlight=11)]</span></span>

    <span data-ttu-id="bbc4d-143">Si l’utilisateur a été créé avec succès, l’utilisateur est connecté par l’appel à ``_signInManager.SignInAsync``.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-143">If the user was created successfully, the user is logged in by the call to ``_signInManager.SignInAsync``.</span></span>

    <span data-ttu-id="bbc4d-144">**Remarque :** consultez [compte confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) pour connaître les étapes empêcher la connexion immédiate lors de l’inscription.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-144">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>
 
4.  <span data-ttu-id="bbc4d-145">Se connecter.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-145">Log in.</span></span>
 
    <span data-ttu-id="bbc4d-146">Les utilisateurs peuvent se connecter en cliquant sur le **connecter** lien en haut du site, ou ils peuvent être accédés à la page de connexion s’ils tentent d’accéder à une partie du site qui nécessite une autorisation.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-146">Users can sign in by clicking the **Log in** link at the top of the site, or they may be navigated to the Login page if they attempt to access a part of the site that requires authorization.</span></span> <span data-ttu-id="bbc4d-147">Lorsque l’utilisateur envoie le formulaire sur la page de connexion, le ``AccountController`` ``Login`` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-147">When the user submits the form on the Login page, the ``AccountController`` ``Login`` action is called.</span></span>

    <span data-ttu-id="bbc4d-148">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=login&highlight=13-14)]</span><span class="sxs-lookup"><span data-stu-id="bbc4d-148">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=login&highlight=13-14)]</span></span>
 
    <span data-ttu-id="bbc4d-149">Le ``Login`` action appelle ``PasswordSignInAsync`` sur la ``_signInManager`` objet (fourni à ``AccountController`` par injection de dépendance).</span><span class="sxs-lookup"><span data-stu-id="bbc4d-149">The ``Login`` action calls ``PasswordSignInAsync`` on the ``_signInManager`` object (provided to ``AccountController`` by dependency injection).</span></span>
 
    <span data-ttu-id="bbc4d-150">La base de ``Controller`` classe expose un ``User`` propriété que vous pouvez accéder à partir de méthodes de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-150">The base ``Controller`` class exposes a ``User`` property that you can access from controller methods.</span></span> <span data-ttu-id="bbc4d-151">Par exemple, vous pouvez énumérer `User.Claims` et prendre des décisions d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-151">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="bbc4d-152">Pour plus d’informations, consultez [autorisation](xref:security/authorization/index).</span><span class="sxs-lookup"><span data-stu-id="bbc4d-152">For more information, see [Authorization](xref:security/authorization/index).</span></span>
 
5.  <span data-ttu-id="bbc4d-153">Fermez la session.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-153">Log out.</span></span>
 
    <span data-ttu-id="bbc4d-154">En cliquant sur le **déconnecter** lien appelle le `LogOut` action.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-154">Clicking the **Log out** link calls the `LogOut` action.</span></span>
 
    <span data-ttu-id="bbc4d-155">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=logout&highlight=7)]</span><span class="sxs-lookup"><span data-stu-id="bbc4d-155">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=logout&highlight=7)]</span></span>
 
    <span data-ttu-id="bbc4d-156">Le code précédent ci-dessus appelle le `_signInManager.SignOutAsync` (méthode).</span><span class="sxs-lookup"><span data-stu-id="bbc4d-156">The preceding code above calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="bbc4d-157">Le `SignOutAsync` méthode efface les revendications de l’utilisateur stockées dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-157">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>
 
6.  <span data-ttu-id="bbc4d-158">Configuration.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-158">Configuration.</span></span>

    <span data-ttu-id="bbc4d-159">Identité a certains comportements par défaut que vous pouvez substituer dans une classe de démarrage de votre application.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-159">Identity has some default behaviors that you can override in your application's startup class.</span></span> <span data-ttu-id="bbc4d-160">Vous n’avez pas besoin de configurer ``IdentityOptions`` si vous utilisez les comportements par défaut.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-160">You do not need to configure ``IdentityOptions`` if you are using the default behaviors.</span></span>
 
    <span data-ttu-id="bbc4d-161">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=configureservices&highlight=13-34)]</span><span class="sxs-lookup"><span data-stu-id="bbc4d-161">[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=configureservices&highlight=13-34)]</span></span>
    
    <span data-ttu-id="bbc4d-162">Pour plus d’informations sur la façon de configurer l’identité, consultez [configurer une identité](xref:security/authentication/identity-configuration).</span><span class="sxs-lookup"><span data-stu-id="bbc4d-162">For more information about how to configure Identity, see [Configure Identity](xref:security/authentication/identity-configuration).</span></span>
    
    <span data-ttu-id="bbc4d-163">Vous pouvez également configurer le type de données de la clé primaire, consultez [type de données de clés primaires de configurer une identité](xref:security/authentication/identity-primary-key-configuration).</span><span class="sxs-lookup"><span data-stu-id="bbc4d-163">You also can configure the data type of the primary key, see [Configure Identity primary keys data type](xref:security/authentication/identity-primary-key-configuration).</span></span>
 
7.  <span data-ttu-id="bbc4d-164">Afficher la base de données.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-164">View the database.</span></span>

    <span data-ttu-id="bbc4d-165">Si votre application utilise une base de données SQL Server (la valeur par défaut sur Windows et pour les utilisateurs de Visual Studio), vous pouvez afficher la base de données de l’application créée.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-165">If your app is using a SQL Server database (the default on Windows and for Visual Studio users), you can view the database the app created.</span></span> <span data-ttu-id="bbc4d-166">Vous pouvez utiliser **SQL Server Management Studio**.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-166">You can use **SQL Server Management Studio**.</span></span> <span data-ttu-id="bbc4d-167">Vous pouvez également, à partir de Visual Studio, sélectionnez **vue** -> **l’Explorateur d’objets SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-167">Alternatively, from Visual Studio, select **View** -> **SQL Server Object Explorer**.</span></span> <span data-ttu-id="bbc4d-168">Se connecter à **(localdb) \MSSQLLocalDB**.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-168">Connect to **(localdb)\MSSQLLocalDB**.</span></span> <span data-ttu-id="bbc4d-169">La base de données dont le nom correspond  **aspnet - <*nom de votre projet*>-<*chaîne de date*> ** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-169">The database with a name matching **aspnet-<*name of your project*>-<*date string*>** is displayed.</span></span>

    ![Menu contextuel sur la table de base de données AspNetUsers](identity/_static/04-db.png)
    
    <span data-ttu-id="bbc4d-171">Développez la base de données et ses **Tables**, puis cliquez sur le **dbo. AspNetUsers** de table et sélectionnez **des données d’affichage**.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-171">Expand the database and its **Tables**, then right-click the **dbo.AspNetUsers** table and select **View Data**.</span></span>

## <a name="identity-components"></a><span data-ttu-id="bbc4d-172">Composants de l’identité</span><span class="sxs-lookup"><span data-stu-id="bbc4d-172">Identity Components</span></span>

<span data-ttu-id="bbc4d-173">L’assembly de référence principale du système d’identité est `Microsoft.AspNetCore.Identity`.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-173">The primary reference assembly for the Identity system is `Microsoft.AspNetCore.Identity`.</span></span> <span data-ttu-id="bbc4d-174">Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity et est inclus par `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-174">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

<span data-ttu-id="bbc4d-175">Ces dépendances sont nécessaires pour utiliser le système d’identité dans les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="bbc4d-175">These dependencies are needed to use the Identity system in ASP.NET Core applications:</span></span>

* <span data-ttu-id="bbc4d-176">`Microsoft.AspNetCore.Identity.EntityFrameworkCore`-Contient les types requis pour utiliser l’identité avec Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-176">`Microsoft.AspNetCore.Identity.EntityFrameworkCore` - Contains the required types to use Identity with Entity Framework Core.</span></span>

* <span data-ttu-id="bbc4d-177">`Microsoft.EntityFrameworkCore.SqlServer`-Entity Framework Core est la technologie d’accès aux données recommandée par Microsoft pour les bases de données relationnelles telles que SQL Server.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-177">`Microsoft.EntityFrameworkCore.SqlServer` - Entity Framework Core is Microsoft's recommended data access technology for relational databases like SQL Server.</span></span> <span data-ttu-id="bbc4d-178">Pour le test, vous pouvez utiliser `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-178">For testing, you can use `Microsoft.EntityFrameworkCore.InMemory`.</span></span>

* <span data-ttu-id="bbc4d-179">`Microsoft.AspNetCore.Authentication.Cookies`-Intergiciel (middleware) qui permet à une application utiliser l’authentification basée sur le cookie.</span><span class="sxs-lookup"><span data-stu-id="bbc4d-179">`Microsoft.AspNetCore.Authentication.Cookies` - Middleware that enables an app to use cookie-based authentication.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="bbc4d-180">Migration vers ASP.NET Core identité</span><span class="sxs-lookup"><span data-stu-id="bbc4d-180">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="bbc4d-181">Pour plus d’informations et des conseils sur la migration des identités existantes de votre magasin voir [migration de l’authentification et identité](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="bbc4d-181">For additional information and guidance on migrating your existing Identity store see [Migrating Authentication and Identity](xref:migration/identity).</span></span>

## <a name="next-steps"></a><span data-ttu-id="bbc4d-182">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="bbc4d-182">Next Steps</span></span>

* [<span data-ttu-id="bbc4d-183">Identité et authentification de migration</span><span class="sxs-lookup"><span data-stu-id="bbc4d-183">Migrating Authentication and Identity</span></span>](xref:migration/identity)
* [<span data-ttu-id="bbc4d-184">Confirmation du compte et la récupération de mot de passe</span><span class="sxs-lookup"><span data-stu-id="bbc4d-184">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="bbc4d-185">Authentification à deux facteurs avec SMS</span><span class="sxs-lookup"><span data-stu-id="bbc4d-185">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="bbc4d-186">Activation de l’authentification à l’aide de Facebook, Google et autres fournisseurs externes</span><span class="sxs-lookup"><span data-stu-id="bbc4d-186">Enabling authentication using Facebook, Google and other external providers</span></span>](xref:security/authentication/social/index)

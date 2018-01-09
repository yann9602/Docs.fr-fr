---
title: "Introduction à Identity sur ASP.NET Core"
author: rick-anderson
description: "Utiliser Identity à une application ASP.NET Core"
keywords: "Autorisation ASP.NET Core, identité, sécurité"
ms.author: riande
manager: wpickett
ms.date: 01/02/2018
ms.topic: article
ms.assetid: cf119f21-1a2b-49a2-b052-547ccb66ee83
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity
ms.openlocfilehash: 4a5d3622a22b70daa22333cafe58f8831bf0918e
ms.sourcegitcommit: fc98e93464ccf37d9904e89a71cdddbd4bbdb86a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Introduction à Identity sur ASP.NET Core

Par [Pranav Rastogi](https://github.com/rustd), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), Jon Galloway, [Erik Reitan](https://github.com/Erikre), et [Steve Smith](https://ardalis.com/)

Identity de ASP.NET Core est un système d’appartenance qui vous permet d’ajouter des fonctionnalités de connexion à votre application. Les utilisateurs peuvent créer une connexion et un compte avec un nom d’utilisateur et mot de passe, ou ils peuvent utiliser un fournisseur de connexion externe tels que Facebook, Google, Microsoft Account, Twitter ou d’autres.

Vous pouvez configurer Identity du principal ASP.NET pour utiliser une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil. Vous pouvez également utiliser votre propre magasin persistant, par exemple, un stockage de tables Azure. Ce document contient des instructions pour Visual Studio et à l’aide de l’interface CLI.

## <a name="overview-of-identity"></a>Vue d’ensemble d'Identity

Dans cette rubrique, vous allez apprendre à utiliser ASP.NET Core Identity pour ajouter des fonctionnalités pour vous inscrire, connectez-vous et déconnecter un utilisateur. Pour obtenir des instructions plus détaillées sur la création d’applications à l’aide d’ASP.NET Core Identity, consultez la section étapes suivantes à la fin de cet article.

1.  Créez un projet d’Application ASP.NET Core Web avec des comptes d’utilisateur individuels.

    # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

    Dans Visual Studio, sélectionnez **fichier** -> **nouveau** -> **projet**. Sélectionnez **Application ASP.NET Core Web** et cliquez sur **OK**.

    ![Boîte de dialogue Nouveau projet](identity/_static/01-new-project.png)

    Sélectionnez un ASP.NET Core **l’Application Web (Model-View-Controller)** pour ASP.NET 2.x de base, puis sélectionnez **modifier l’authentification**.

    ![Boîte de dialogue Nouveau projet](identity/_static/02-new-project.png)

    Une boîte de dialogue offre différentes possibilités d’authentification. Sélectionnez **comptes d’utilisateur individuels** et cliquez sur **OK** pour revenir à la boîte de dialogue précédente.

    ![Boîte de dialogue Nouveau projet](identity/_static/03-new-project-auth.png)

    En sélectionnant **comptes d’utilisateur individuels** dirige Visual Studio pour créer des modèles, ViewModel, vues, contrôleurs et autres composants requis pour l’authentification en tant que partie du modèle de projet.

    # <a name="net-core-clitabnetcore-cli"></a>[CLI .NET Core](#tab/netcore-cli)


    Le projet créé contient le `Microsoft.AspNetCore.Identity.EntityFrameworkCore` package, qui conserve les données d’Identity et le schéma à l’aide de SQL Server [Entity Framework Core](https://docs.microsoft.com/ef/).

    ---

2.  Configurer les services d’Identity et d’ajouter l’intergiciel (middleware) dans `Startup`.

    Les services d’Identity sont ajoutés à l’application dans le `ConfigureServices` méthode dans la `Startup` classe :

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-39)]
    
    Ces services sont accessibles à l’application via [injection de dépendance](xref:fundamentals/dependency-injection).
    
    Identity est activée pour l’application en appelant `UseAuthentication` dans le `Configure` (méthode). `UseAuthentication`Ajoute l’authentification [intergiciel (middleware)](xref:fundamentals/middleware) au pipeline de demande.
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-34)]
    
    Ces services sont accessibles à l’application via [injection de dépendance](xref:fundamentals/dependency-injection).
    
    Identity est activée pour l’application en appelant `UseIdentity` dans le `Configure` (méthode). `UseIdentity`Ajoute l’authentification par cookie [intergiciel (middleware)](xref:fundamentals/middleware) au pipeline de demande.
        
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]
    
    ---
     
    Pour plus d’informations sur le démarrage de l’application des processus, consultez [démarrage de l’Application](xref:fundamentals/startup).

3.  Créez un utilisateur.

    Lancer l’application, puis cliquez sur le **inscrire** lien.

    S’il s’agit de la première fois que vous effectuez cette action, vous serez peut-être requis pour exécuter des migrations. L’application vous invite à **s’appliquent les Migrations**. Actualisez la page, si nécessaire.
    
    ![Appliquer la Page Web de Migrations](identity/_static/apply-migrations.png)
    
    Ou bien, vous pouvez tester à l’aide d’ASP.NET Core Identity avec votre application sans une base de données persistantes à l’aide d’une base de données en mémoire. Pour utiliser une base de données en mémoire, ajoutez le ``Microsoft.EntityFrameworkCore.InMemory`` le package à votre application et de modifier l’appel de votre application à ``AddDbContext`` dans ``ConfigureServices`` comme suit :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseInMemoryDatabase(Guid.NewGuid().ToString()));
    ```
    
    Lorsque l’utilisateur clique sur le **inscrire** lien, le ``Register`` action est appelée sur ``AccountController``. Le ``Register`` action crée l’utilisateur en appelant `CreateAsync` sur la `_userManager` objet (fourni à ``AccountController`` par injection de dépendances) :
 
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

    Si l’utilisateur a été créé avec succès, l’utilisateur est connecté par l’appel à ``_signInManager.SignInAsync``.

    **Remarque :** consultez [compte confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) pour connaître les étapes empêcher la connexion immédiate lors de l’inscription.
 
4.  Se connecter.
 
    Les utilisateurs peuvent se connecter en cliquant sur le **connecter** lien en haut du site, ou ils peuvent être accédés à la page de connexion s’ils tentent d’accéder à une partie du site qui nécessite une autorisation. Lorsque l’utilisateur envoie le formulaire sur la page de connexion, le ``AccountController`` ``Login`` action est appelée.

    Le ``Login`` action appelle ``PasswordSignInAsync`` sur la ``_signInManager`` objet (fourni à ``AccountController`` par injection de dépendance).

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]
 
    La base de ``Controller`` classe expose un ``User`` propriété que vous pouvez accéder à partir de méthodes de contrôleur. Par exemple, vous pouvez énumérer `User.Claims` et prendre des décisions d’autorisation. Pour plus d’informations, consultez [autorisation](xref:security/authorization/index).
 
5.  Fermez la session.
 
    En cliquant sur le **déconnecter** lien appelle le `LogOut` action.
 
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]
 
    Le code précédent ci-dessus appelle le `_signInManager.SignOutAsync` (méthode). Le `SignOutAsync` méthode efface les revendications de l’utilisateur stockées dans un cookie.
 
6.  Configuration.

    Identity a certains comportements par défaut que vous pouvez substituer dans une classe de démarrage de votre application. Vous n’avez pas besoin de configurer ``IdentityOptions`` si vous utilisez les comportements par défaut.

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-39)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=13-34)]

    ---
    
    Pour plus d’informations sur la façon de configurer Identity, consultez [configurer Identity](xref:security/authentication/identity-configuration).
    
    Vous pouvez également configurer le type de données de la clé primaire, consultez [type de données de clés primaires de configurer Identity](xref:security/authentication/identity-primary-key-configuration).
 
7.  Afficher la base de données.

    Si votre application utilise une base de données SQL Server (la valeur par défaut sur Windows et pour les utilisateurs de Visual Studio), vous pouvez afficher la base de données de l’application créée. Vous pouvez utiliser **SQL Server Management Studio**. Vous pouvez également, à partir de Visual Studio, sélectionnez **vue** -> **l’Explorateur d’objets SQL Server**. Se connecter à **(localdb) \MSSQLLocalDB**. La base de données dont le nom correspond  **aspnet - <*nom de votre projet*>-<*chaîne de date*> ** s’affiche.

    ![Menu contextuel sur la table de base de données AspNetUsers](identity/_static/04-db.png)
    
    Développez la base de données et ses **Tables**, puis cliquez sur le **dbo. AspNetUsers** de table et sélectionnez **des données d’affichage**.

8. Vérifiez le fonctionnement de l’identité

    La valeur par défaut *Application ASP.NET Core Web* modèle de projet permet aux utilisateurs d’accéder à toute action dans l’application sans avoir à se connecter. Pour vérifier que ASP.NET Identity fonctionne, ajoutez une`[Authorize]` d’attribut pour le `About` action de la `Home` contrôleur.
 
    ```cs
    [Authorize]
    public IActionResult About()
    {
        ViewData["Message"] = "Your application description page.";
        return View();
    }
    ```
    
    # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

    Exécutez le projet à l’aide de **Ctrl** + **F5** et accédez à la **sur** page. Seuls les utilisateurs authentifiés peuvent accéder à la **sur** page maintenant, donc ASP.NET vous redirige vers la page de connexion pour la connexion ou une inscription.

    # <a name="net-core-clitabnetcore-cli"></a>[CLI .NET Core](#tab/netcore-cli)

    Ouvrez une fenêtre de commande et accédez à la racine du projet répertoire contenant le `.csproj` fichier. Exécutez le `dotnet run` commande pour exécuter l’application :

    ```cs
    dotnet run 
    ```

    Parcourir l’URL spécifiée dans la sortie de la `dotnet run` commande. L’URL doit pointer vers `localhost` avec un numéro de port généré. Accédez à la **sur** page. Seuls les utilisateurs authentifiés peuvent accéder à la **sur** page maintenant, donc ASP.NET vous redirige vers la page de connexion pour la connexion ou une inscription.

    ---

## <a name="identity-components"></a>Composants d'Identity

L’assembly de référence principale du système d'Identity est `Microsoft.AspNetCore.Identity`. Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity et est inclus par `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

Ces dépendances sont nécessaires pour utiliser le système d'Identity dans les applications ASP.NET Core :

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`-Contient les types requis pour utiliser Identity avec Entity Framework Core.

* `Microsoft.EntityFrameworkCore.SqlServer`-Entity Framework Core est la technologie d’accès aux données recommandée par Microsoft pour les bases de données relationnelles telles que SQL Server. Pour le test, vous pouvez utiliser `Microsoft.EntityFrameworkCore.InMemory`.

* `Microsoft.AspNetCore.Authentication.Cookies`-Intergiciel (middleware) qui permet à une application utiliser l’authentification basée sur le cookie.

## <a name="migrating-to-aspnet-core-identity"></a>Migration vers ASP.NET Core Identity

Pour plus d’informations et des conseils sur la migration d'Identity voir [migration de l’authentification et Identity](xref:migration/identity).

## <a name="next-steps"></a>Étapes suivantes

* [Migration de l’authentification et d'Identity](xref:migration/identity)
* [Confirmation de compte et récupération de mot de passe](xref:security/authentication/accconfirm)
* [Authentification à deux facteurs avec SMS](xref:security/authentication/2fa)
* [Activation de l’authentification via Facebook, Google et d’autres fournisseurs externes](xref:security/authentication/social/index)

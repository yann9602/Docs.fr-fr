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
ms.openlocfilehash: 5a76cac1d64718b9dece3a3201db06c8192fb6f3
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Introduction à l’identité sur ASP.NET Core

Par [Pranav Rastogi](https://github.com/rustd), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), Jon Galloway, [Erik Reitan](https://github.com/Erikre), et [Steve Smith](https://ardalis.com/)

Identité de ASP.NET Core est un système d’appartenance qui vous permet d’ajouter des fonctionnalités de connexion à votre application. Les utilisateurs peuvent créer une connexion et un compte avec un nom d’utilisateur et mot de passe, ou ils peuvent utiliser un fournisseur de connexion externe tels que Facebook, Google, Microsoft Account, Twitter ou d’autres.

Vous pouvez configurer l’identité du principal ASP.NET pour utiliser une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil. Vous pouvez également utiliser votre propre magasin persistant, par exemple le stockage de Table Azure. Ce document contient des instructions pour Visual Studio et à l’aide de l’interface CLI.

## <a name="overview-of-identity"></a>Vue d’ensemble de l’identité

Dans cette rubrique, vous allez apprendre à utiliser ASP.NET Core Identity pour ajouter des fonctionnalités pour vous inscrire, connectez-vous et déconnecter un utilisateur. Pour obtenir des instructions plus détaillées sur la création d’applications à l’aide d’ASP.NET Core Identity, consultez la section étapes suivantes à la fin de cet article.

1.  Créez un projet d’Application ASP.NET Core Web avec des comptes d’utilisateur individuels.

    # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)
    Dans Visual Studio, sélectionnez **fichier** -> **nouveau** -> **projet**. Sélectionnez le **Application Web ASP.NET** à partir de la **nouveau projet** boîte de dialogue. En sélectionnant un ASP.NET Core **Application Web** avec **comptes d’utilisateur individuels** comme méthode d’authentification.

    Remarque : Vous devez sélectionner **comptes d’utilisateur individuels**.
 
    ![Boîte de dialogue Nouveau projet](identity/_static/01-mvc.png)
    
    # <a name="net-core-clitabnetcore-cli"></a>[CLI .NET Core](#tab/netcore-cli)
    Si vous utilisez l’interface de ligne de base de .NET, créer le projet à l’aide ``dotnet new mvc --auth Individual``. Cela crée un nouveau projet avec le même code de modèle d’identité crée de Visual Studio.
 
    Le projet créé contient le `Microsoft.AspNetCore.Identity.EntityFrameworkCore` package, qui rendront les données d’identité et le schéma à l’aide de SQL Server [Entity Framework Core](https://docs.microsoft.com/ef/).
    
    ---
 
2.  Configurer les services d’identité et d’ajouter l’intergiciel (middleware) dans `Startup`.

    Les services d’identité sont ajoutés à l’application dans le `ConfigureServices` méthode dans la `Startup` classe :

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-39)]
    
    Ces services sont accessibles à l’application via [injection de dépendance](xref:fundamentals/dependency-injection).
    
    Identité est activée pour l’application en appelant `UseAuthentication` dans le `Configure` (méthode). `UseAuthentication`Ajoute l’authentification [intergiciel (middleware)](xref:fundamentals/middleware) au pipeline de demande.
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-34)]
    
    Ces services sont accessibles à l’application via [injection de dépendance](xref:fundamentals/dependency-injection).
    
    Identité est activée pour l’application en appelant `UseIdentity` dans le `Configure` (méthode). `UseIdentity`Ajoute l’authentification par cookie [intergiciel (middleware)](xref:fundamentals/middleware) au pipeline de demande.
        
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]
    
    ---
     
    Pour plus d’informations sur le démarrage de l’application des processus, consultez [démarrage de l’Application](xref:fundamentals/startup).

3.  Créez un utilisateur.

    Lancer l’application, puis cliquez sur le **inscrire** lien.

    S’il s’agit de la première fois que vous effectuez cette action, vous serez peut-être requis pour exécuter des migrations. L’application vous invite à **s’appliquent les Migrations**:
    
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

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]
 
    Le ``Login`` action appelle ``PasswordSignInAsync`` sur la ``_signInManager`` objet (fourni à ``AccountController`` par injection de dépendance).
 
    La base de ``Controller`` classe expose un ``User`` propriété que vous pouvez accéder à partir de méthodes de contrôleur. Par exemple, vous pouvez énumérer `User.Claims` et prendre des décisions d’autorisation. Pour plus d’informations, consultez [autorisation](xref:security/authorization/index).
 
5.  Fermez la session.
 
    En cliquant sur le **déconnecter** lien appelle le `LogOut` action.
 
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]
 
    Le code précédent ci-dessus appelle le `_signInManager.SignOutAsync` (méthode). Le `SignOutAsync` méthode efface les revendications de l’utilisateur stockées dans un cookie.
 
6.  Configuration.

    Identité a certains comportements par défaut que vous pouvez substituer dans une classe de démarrage de votre application. Vous n’avez pas besoin de configurer ``IdentityOptions`` si vous utilisez les comportements par défaut.

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-39)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=13-34)]

    ---
    
    Pour plus d’informations sur la façon de configurer l’identité, consultez [configurer une identité](xref:security/authentication/identity-configuration).
    
    Vous pouvez également configurer le type de données de la clé primaire, consultez [type de données de clés primaires de configurer une identité](xref:security/authentication/identity-primary-key-configuration).
 
7.  Afficher la base de données.

    Si votre application utilise une base de données SQL Server (la valeur par défaut sur Windows et pour les utilisateurs de Visual Studio), vous pouvez afficher la base de données de l’application créée. Vous pouvez utiliser **SQL Server Management Studio**. Vous pouvez également, à partir de Visual Studio, sélectionnez **vue** -> **l’Explorateur d’objets SQL Server**. Se connecter à **(localdb) \MSSQLLocalDB**. La base de données dont le nom correspond * *aspnet - <*nom de votre projet*>-<*chaîne de date*> ** s’affiche.

    ![Menu contextuel sur la table de base de données AspNetUsers](identity/_static/04-db.png)
    
    Développez la base de données et ses **Tables**, puis cliquez sur le **dbo. AspNetUsers** de table et sélectionnez **des données d’affichage**.

## <a name="identity-components"></a>Composants de l’identité

L’assembly de référence principale du système d’identité est `Microsoft.AspNetCore.Identity`. Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity et est inclus par `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

Ces dépendances sont nécessaires pour utiliser le système d’identité dans les applications ASP.NET Core :

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`-Contient les types requis pour utiliser l’identité avec Entity Framework Core.

* `Microsoft.EntityFrameworkCore.SqlServer`-Entity Framework Core est la technologie d’accès aux données recommandée par Microsoft pour les bases de données relationnelles telles que SQL Server. Pour le test, vous pouvez utiliser `Microsoft.EntityFrameworkCore.InMemory`.

* `Microsoft.AspNetCore.Authentication.Cookies`-Intergiciel (middleware) qui permet à une application utiliser l’authentification basée sur le cookie.

## <a name="migrating-to-aspnet-core-identity"></a>Migration vers ASP.NET Core identité

Pour plus d’informations et des conseils sur la migration des identités existantes de votre magasin voir [migration de l’authentification et identité](xref:migration/identity).

## <a name="next-steps"></a>Étapes suivantes

* [Migration de l’authentification et de l’identité](xref:migration/identity)
* [Confirmation de compte et récupération de mot de passe](xref:security/authentication/accconfirm)
* [Authentification à 2 facteurs avec SMS](xref:security/authentication/2fa)
* [Activation de l’authentification via Facebook, Google et d’autres fournisseurs externes](xref:security/authentication/social/index)

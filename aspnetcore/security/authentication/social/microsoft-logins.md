---
title: "Programme d’installation de Microsoft Account connexion externe"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 08/24/2017
ms.topic: article
ms.assetid: 66DB4B94-C78C-4005-BA03-3D982B87C268
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 4e1512aa151a87f677b400f16dbf53ca22b61e9e
ms.sourcegitcommit: 93785b6b29a4996066fba05149348f1bdf881263
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2017
---
# <a name="configuring-microsoft-account-authentication"></a><span data-ttu-id="7ee06-103">Configuration de l’authentification Microsoft Account</span><span class="sxs-lookup"><span data-stu-id="7ee06-103">Configuring Microsoft Account authentication</span></span>

<span data-ttu-id="7ee06-104">Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ee06-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7ee06-105">Ce didacticiel vous montre comment permettre aux utilisateurs de se connecter avec son compte Microsoft à l’aide d’un exemple de projet ASP.NET Core 2.0 créé sur le [page précédente](index.md).</span><span class="sxs-lookup"><span data-stu-id="7ee06-105">This tutorial shows you how to enable your users to sign in with their Microsoft account using a sample ASP.NET Core 2.0 project created on the [previous page](index.md).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="7ee06-106">Créer l’application dans le portail des développeurs de Microsoft</span><span class="sxs-lookup"><span data-stu-id="7ee06-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="7ee06-107">Accédez à [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) et créer ou se connecter à un compte Microsoft :</span><span class="sxs-lookup"><span data-stu-id="7ee06-107">Navigate to [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) and create or sign into a Microsoft account:</span></span>

![Boîte de dialogue se connecter](index/_static/MicrosoftDevLogin.png)

<span data-ttu-id="7ee06-109">Si vous n’avez pas déjà un compte Microsoft, appuyez sur  **[créez-en un !](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)**</span><span class="sxs-lookup"><span data-stu-id="7ee06-109">If you don't already have a Microsoft account, tap **[Create one!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)**</span></span> <span data-ttu-id="7ee06-110">Une fois connecté, vous êtes redirigé vers **mes applications** page :</span><span class="sxs-lookup"><span data-stu-id="7ee06-110">After signing in you are redirected to **My applications** page:</span></span>

![Portail des développeurs Microsoft ouvert dans Microsoft Edge](index/_static/MicrosoftDev.png)

* <span data-ttu-id="7ee06-112">Appuyez sur **ajouter une application** dans le coin supérieur droit d’angle, puis entrez votre **nom de l’Application** et **messagerie du Contact**:</span><span class="sxs-lookup"><span data-stu-id="7ee06-112">Tap **Add an app** in the upper right corner and enter your **Application Name** and **Contact Email**:</span></span>

![Boîte de dialogue nouvelle inscription d’Application](index/_static/MicrosoftDevAppCreate.png)

* <span data-ttu-id="7ee06-114">Pour les besoins de ce didacticiel, désactivez le **guidée par le programme d’installation** case à cocher.</span><span class="sxs-lookup"><span data-stu-id="7ee06-114">For the purposes of this tutorial, clear the **Guided Setup** check box.</span></span>

* <span data-ttu-id="7ee06-115">Appuyez sur **créer** pour continuer au **inscription** page.</span><span class="sxs-lookup"><span data-stu-id="7ee06-115">Tap **Create** to continue to the **Registration** page.</span></span> <span data-ttu-id="7ee06-116">Fournir un **nom** et notez la valeur de la **Id d’Application**, que vous utilisez comme `ClientId` plus loin dans le didacticiel :</span><span class="sxs-lookup"><span data-stu-id="7ee06-116">Provide a **Name** and note the value of the **Application Id**, which you use as `ClientId` later in the tutorial:</span></span>

![Page d’inscription](index/_static/MicrosoftDevAppReg.png)

* <span data-ttu-id="7ee06-118">Appuyez sur **ajouter une plateforme** dans les **plateformes** section et sélectionnez le **Web** plateforme :</span><span class="sxs-lookup"><span data-stu-id="7ee06-118">Tap **Add Platform** in the **Platforms** section and select the **Web** platform:</span></span>

![Boîte de dialogue plateforme ajouter](index/_static/MicrosoftDevAppPlatform.png)

* <span data-ttu-id="7ee06-120">Dans la nouvelle **Web** plateforme section, entrez votre URL de développement avec */signin-microsoft* ajoutées dans le **URL de redirection** champ (par exemple : `https://localhost:44320/signin-microsoft`).</span><span class="sxs-lookup"><span data-stu-id="7ee06-120">In the new **Web** platform section, enter your development URL with */signin-microsoft* appended into the **Redirect URLs** field (for example: `https://localhost:44320/signin-microsoft`).</span></span> <span data-ttu-id="7ee06-121">Le schéma d’authentification Microsoft configuré plus loin dans ce didacticiel va gérer automatiquement les demandes à */signin-microsoft* itinéraire pour implémenter le flux OAuth :</span><span class="sxs-lookup"><span data-stu-id="7ee06-121">The Microsoft authentication scheme configured later in this tutorial will automatically handle requests at */signin-microsoft* route to implement the OAuth flow:</span></span>

![Section de plateforme Web](index/_static/MicrosoftRedirectUri.png)

* <span data-ttu-id="7ee06-123">Appuyez sur **ajouter une URL** afin de vérifier l’URL a été ajoutée.</span><span class="sxs-lookup"><span data-stu-id="7ee06-123">Tap **Add URL** to ensure the URL was added.</span></span>

* <span data-ttu-id="7ee06-124">Renseignez tous les autres paramètres de l’application si nécessaire, puis appuyez sur **enregistrer** au bas de la page pour enregistrer les modifications apportées à la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="7ee06-124">Fill out any other application settings if necessary and tap **Save** at the bottom of the page to save changes to app configuration.</span></span>

* <span data-ttu-id="7ee06-125">Lorsque vous déployez le site, vous devez revoir la **inscription** page et de définir une nouvelle URL publique.</span><span class="sxs-lookup"><span data-stu-id="7ee06-125">When deploying the site you'll need to revisit the **Registration** page and set a new public URL.</span></span>

## <a name="store-microsoft-application-id-and-password"></a><span data-ttu-id="7ee06-126">Stocker les Id d’Application de Microsoft et le mot de passe</span><span class="sxs-lookup"><span data-stu-id="7ee06-126">Store Microsoft Application Id and Password</span></span>

* <span data-ttu-id="7ee06-127">Remarque la `Application Id` affichées sur le **inscription** page.</span><span class="sxs-lookup"><span data-stu-id="7ee06-127">Note the `Application Id` displayed on the **Registration** page.</span></span>

* <span data-ttu-id="7ee06-128">Appuyez sur **générer un nouveau mot de passe** dans les **Application Secrets** section.</span><span class="sxs-lookup"><span data-stu-id="7ee06-128">Tap **Generate New Password** in the **Application Secrets** section.</span></span> <span data-ttu-id="7ee06-129">Cela affiche une zone où vous pouvez copier le mot de passe d’application :</span><span class="sxs-lookup"><span data-stu-id="7ee06-129">This displays a box where you can copy the application password:</span></span>

![Boîte de dialogue Nouveau mot de passe généré](index/_static/MicrosoftDevPassword.png)

<span data-ttu-id="7ee06-131">Lier les paramètres sensibles telles que Microsoft `Application ID` et `Password` à votre configuration d’application à l’aide du [Secret Manager](../../app-secrets.md).</span><span class="sxs-lookup"><span data-stu-id="7ee06-131">Link sensitive settings like Microsoft `Application ID` and `Password` to your application configuration using the [Secret Manager](../../app-secrets.md).</span></span> <span data-ttu-id="7ee06-132">Pour les besoins de ce didacticiel, nommez les jetons `Authentication:Microsoft:ApplicationId` et `Authentication:Microsoft:Password`.</span><span class="sxs-lookup"><span data-stu-id="7ee06-132">For the purposes of this tutorial, name the tokens `Authentication:Microsoft:ApplicationId` and `Authentication:Microsoft:Password`.</span></span>

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="7ee06-133">Configurer l’authentification de compte Microsoft</span><span class="sxs-lookup"><span data-stu-id="7ee06-133">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="7ee06-134">Le modèle de projet utilisé dans ce didacticiel garantit que [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) package est déjà installé.</span><span class="sxs-lookup"><span data-stu-id="7ee06-134">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) package is already installed.</span></span>

* <span data-ttu-id="7ee06-135">Pour installer ce package avec Visual Studio 2017, cliquez sur le projet et sélectionnez **gérer les Packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="7ee06-135">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="7ee06-136">Pour installer avec l’interface CLI de .NET Core, exécutez le code suivant dans votre répertoire de projet :</span><span class="sxs-lookup"><span data-stu-id="7ee06-136">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7ee06-137">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7ee06-137">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="7ee06-138">Ajoutez le service de Account Microsoft dans le `ConfigureServices` méthode dans *Startup.cs* fichier :</span><span class="sxs-lookup"><span data-stu-id="7ee06-138">Add the Microsoft Account service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddMicrosoftAccount(microsoftOptions =>
{
    microsoftOptions.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    microsoftOptions.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```

[!INCLUDE[default settings configuration](includes/default-settings.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7ee06-139">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7ee06-139">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="7ee06-140">Ajouter l’intergiciel (middleware) Microsoft Account dans les `Configure` méthode dans *Startup.cs* fichier :</span><span class="sxs-lookup"><span data-stu-id="7ee06-140">Add the Microsoft Account middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

---

<span data-ttu-id="7ee06-141">Bien que les noms de la terminologie utilisée sur le portail des développeurs Microsoft ces jetons `ApplicationId` et `Password`, ils sont exposés en tant que `ClientId` et `ClientSecret` à l’API de configuration.</span><span class="sxs-lookup"><span data-stu-id="7ee06-141">Although the terminology used on Microsoft Developer Portal names these tokens `ApplicationId` and `Password`, they are exposed as `ClientId` and `ClientSecret` to the configuration API.</span></span>

<span data-ttu-id="7ee06-142">Consultez le [MicrosoftAccountOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.microsoftaccountoptions) référence des API pour plus d’informations sur les options de configuration prises en charge par l’authentification de Microsoft Account.</span><span class="sxs-lookup"><span data-stu-id="7ee06-142">See the [MicrosoftAccountOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="7ee06-143">Cela peut être utilisé pour demander des différentes informations relatives à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7ee06-143">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="7ee06-144">Connectez-vous avec un compte Microsoft</span><span class="sxs-lookup"><span data-stu-id="7ee06-144">Sign in with Microsoft Account</span></span>

<span data-ttu-id="7ee06-145">Exécutez votre application et cliquez sur **connecter**.</span><span class="sxs-lookup"><span data-stu-id="7ee06-145">Run your application and click **Log in**.</span></span> <span data-ttu-id="7ee06-146">Une option pour vous connecter avec Microsoft s’affiche :</span><span class="sxs-lookup"><span data-stu-id="7ee06-146">An option to sign in with Microsoft appears:</span></span>

![Application de journal dans la page Web : utilisateur non authentifié](index/_static/DoneMicrosoft.png)

<span data-ttu-id="7ee06-148">Lorsque vous cliquez sur Microsoft, vous êtes redirigé à Microsoft pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="7ee06-148">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="7ee06-149">Après la connexion avec votre Account Microsoft (si l’est pas déjà fait) vous devrez laisser l’application à accéder à vos informations :</span><span class="sxs-lookup"><span data-stu-id="7ee06-149">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

![Boîte de dialogue d’authentification Microsoft](index/_static/MicrosoftLogin.png)

<span data-ttu-id="7ee06-151">Appuyez sur **Oui** et vous allez être redirigé vers le site web où vous pouvez définir votre adresse de messagerie.</span><span class="sxs-lookup"><span data-stu-id="7ee06-151">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="7ee06-152">Vous êtes désormais connecté à l’aide de vos informations d’identification de Microsoft :</span><span class="sxs-lookup"><span data-stu-id="7ee06-152">You are now logged in using your Microsoft credentials:</span></span>

![Application Web : utilisateur authentifié](index/_static/Done.png)

## <a name="troubleshooting"></a><span data-ttu-id="7ee06-154">Résolution des problèmes</span><span class="sxs-lookup"><span data-stu-id="7ee06-154">Troubleshooting</span></span>

* <span data-ttu-id="7ee06-155">Si le fournisseur de Microsoft Account vous redirige vers une page d’erreur de connexion, notez les titre et la description requête chaîne paramètres d’erreur directement après le `#` (#sqlhelp) dans l’Uri.</span><span class="sxs-lookup"><span data-stu-id="7ee06-155">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="7ee06-156">Bien que le message d’erreur semble indiquer un problème avec l’authentification Microsoft, la cause la plus courante est votre application Uri ne correspond à aucune de la **URI de redirection** spécifié pour le **Web** plateforme .</span><span class="sxs-lookup"><span data-stu-id="7ee06-156">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="7ee06-157">**ASP.NET Core 2.x uniquement :** si identité n’est pas configurée en appelant `services.AddIdentity` dans `ConfigureServices`, une tentative d’authentification entraîne *ArgumentException : l’option 'SignInScheme' doit être fournie*.</span><span class="sxs-lookup"><span data-stu-id="7ee06-157">**ASP.NET Core 2.x only:** If Identity is not configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="7ee06-158">Le modèle de projet utilisé dans ce didacticiel permet de s’assurer que cette opération est effectuée.</span><span class="sxs-lookup"><span data-stu-id="7ee06-158">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="7ee06-159">Si la base de données de site n’a pas été créé en appliquant la migration initiale, vous obtiendrez *une opération de base de données a échoué lors du traitement de la demande* erreur.</span><span class="sxs-lookup"><span data-stu-id="7ee06-159">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="7ee06-160">Appuyez sur **s’appliquent les Migrations** pour créer la base de données et actualiser pour passer à l’erreur.</span><span class="sxs-lookup"><span data-stu-id="7ee06-160">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7ee06-161">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="7ee06-161">Next steps</span></span>

* <span data-ttu-id="7ee06-162">Cet article a montré comment vous pouvez vous authentifier auprès de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7ee06-162">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="7ee06-163">Vous pouvez suivre une approche similaire pour s’authentifier auprès d’autres fournisseurs répertoriés sur le [page précédente](index.md).</span><span class="sxs-lookup"><span data-stu-id="7ee06-163">You can follow a similar approach to authenticate with other providers listed on the [previous page](index.md).</span></span>

* <span data-ttu-id="7ee06-164">Une fois que vous publiez votre site web à l’application web Azure, vous devez créer un nouveau `Password` dans le portail des développeurs Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7ee06-164">Once you publish your web site to Azure web app, you should create a new `Password` in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="7ee06-165">Définir le `Authentication:Microsoft:ApplicationId` et `Authentication:Microsoft:Password` en tant que paramètres de l’application dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="7ee06-165">Set the `Authentication:Microsoft:ApplicationId` and `Authentication:Microsoft:Password` as application settings in the Azure portal.</span></span> <span data-ttu-id="7ee06-166">Le système de configuration est configuré pour lire les clés à partir de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="7ee06-166">The configuration system is set up to read keys from environment variables.</span></span>

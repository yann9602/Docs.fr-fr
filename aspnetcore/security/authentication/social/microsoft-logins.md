---
title: "Programme d’installation de Microsoft Account connexion externe"
author: rick-anderson
description: "Ce didacticiel illustre l’intégration de l’authentification d’utilisateur de compte Microsoft dans une application ASP.NET Core existante."
keywords: "ASP.NET Core, compte Microsoft, connexion, l’authentification"
ms.author: riande
manager: wpickett
ms.date: 08/24/2017
ms.topic: article
ms.assetid: 66DB4B94-C78C-4005-BA03-3D982B87C268
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 77c16e3ae93c9bfe1f569d0a5888c5b765d04241
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="configuring-microsoft-account-authentication"></a>Configuration de l’authentification Microsoft Account

Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel vous montre comment permettre aux utilisateurs de se connecter avec son compte Microsoft à l’aide d’un exemple de projet ASP.NET Core 2.0 créé sur le [page précédente](index.md).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Créer l’application dans le portail des développeurs de Microsoft

* Accédez à [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) et créer ou se connecter à un compte Microsoft :

![Boîte de dialogue se connecter](index/_static/MicrosoftDevLogin.png)

Si vous n’avez pas déjà un compte Microsoft, appuyez sur  **[créez-en un !](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)** Une fois connecté, vous êtes redirigé vers **mes applications** page :

![Portail des développeurs Microsoft ouvert dans Microsoft Edge](index/_static/MicrosoftDev.png)

* Appuyez sur **ajouter une application** dans le coin supérieur droit d’angle, puis entrez votre **nom de l’Application** et **messagerie du Contact**:

![Boîte de dialogue nouvelle inscription d’Application](index/_static/MicrosoftDevAppCreate.png)

* Pour les besoins de ce didacticiel, désactivez le **guidée par le programme d’installation** case à cocher.

* Appuyez sur **créer** pour continuer au **inscription** page. Fournir un **nom** et notez la valeur de la **Id d’Application**, que vous utilisez comme `ClientId` plus loin dans le didacticiel :

![Page d’inscription](index/_static/MicrosoftDevAppReg.png)

* Appuyez sur **ajouter une plateforme** dans les **plateformes** section et sélectionnez le **Web** plateforme :

![Boîte de dialogue plateforme ajouter](index/_static/MicrosoftDevAppPlatform.png)

* Dans la nouvelle **Web** plateforme section, entrez votre URL de développement avec */signin-microsoft* ajoutées dans le **URL de redirection** champ (par exemple : `https://localhost:44320/signin-microsoft`). Le schéma d’authentification Microsoft configuré plus loin dans ce didacticiel va gérer automatiquement les demandes à */signin-microsoft* itinéraire pour implémenter le flux OAuth :

![Section de plateforme Web](index/_static/MicrosoftRedirectUri.png)

* Appuyez sur **ajouter une URL** afin de vérifier l’URL a été ajoutée.

* Renseignez tous les autres paramètres de l’application si nécessaire, puis appuyez sur **enregistrer** au bas de la page pour enregistrer les modifications apportées à la configuration de l’application.

* Lorsque vous déployez le site, vous devez revoir la **inscription** page et de définir une nouvelle URL publique.

## <a name="store-microsoft-application-id-and-password"></a>Stocker les Id d’Application de Microsoft et le mot de passe

* Remarque la `Application Id` affichées sur le **inscription** page.

* Appuyez sur **générer un nouveau mot de passe** dans les **Application Secrets** section. Cela affiche une zone où vous pouvez copier le mot de passe d’application :

![Boîte de dialogue Nouveau mot de passe généré](index/_static/MicrosoftDevPassword.png)

Lier les paramètres sensibles telles que Microsoft `Application ID` et `Password` à votre configuration d’application à l’aide du [Secret Manager](../../app-secrets.md). Pour les besoins de ce didacticiel, nommez les jetons `Authentication:Microsoft:ApplicationId` et `Authentication:Microsoft:Password`.

## <a name="configure-microsoft-account-authentication"></a>Configurer l’authentification de compte Microsoft

Le modèle de projet utilisé dans ce didacticiel garantit que [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) package est déjà installé.

* Pour installer ce package avec Visual Studio 2017, cliquez sur le projet et sélectionnez **gérer les Packages NuGet**.
* Pour installer avec l’interface CLI de .NET Core, exécutez le code suivant dans votre répertoire de projet :

   `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Ajoutez le service de Account Microsoft dans le `ConfigureServices` méthode dans *Startup.cs* fichier :

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Ajouter l’intergiciel (middleware) Microsoft Account dans les `Configure` méthode dans *Startup.cs* fichier :

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

---

Bien que les noms de la terminologie utilisée sur le portail des développeurs Microsoft ces jetons `ApplicationId` et `Password`, ils sont exposés en tant que `ClientId` et `ClientSecret` à l’API de configuration.

Consultez le [MicrosoftAccountOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.microsoftaccountoptions) référence des API pour plus d’informations sur les options de configuration prises en charge par l’authentification de Microsoft Account. Cela peut être utilisé pour demander des différentes informations relatives à l’utilisateur.

## <a name="sign-in-with-microsoft-account"></a>Connectez-vous avec un compte Microsoft

Exécutez votre application et cliquez sur **connecter**. Une option pour vous connecter avec Microsoft s’affiche :

![Application de journal dans la page Web : utilisateur non authentifié](index/_static/DoneMicrosoft.png)

Lorsque vous cliquez sur Microsoft, vous êtes redirigé à Microsoft pour l’authentification. Après la connexion avec votre Account Microsoft (si l’est pas déjà fait) vous devrez laisser l’application à accéder à vos informations :

![Boîte de dialogue d’authentification Microsoft](index/_static/MicrosoftLogin.png)

Appuyez sur **Oui** et vous allez être redirigé vers le site web où vous pouvez définir votre adresse de messagerie.

Vous êtes désormais connecté à l’aide de vos informations d’identification de Microsoft :

![Application Web : utilisateur authentifié](index/_static/Done.png)

## <a name="troubleshooting"></a>Résolution des problèmes

* Si le fournisseur de Microsoft Account vous redirige vers une page d’erreur de connexion, notez les titre et la description requête chaîne paramètres d’erreur directement après le `#` (#sqlhelp) dans l’Uri.

  Bien que le message d’erreur semble indiquer un problème avec l’authentification Microsoft, la cause la plus courante est votre application Uri ne correspond à aucune de la **URI de redirection** spécifié pour le **Web** plateforme .
* **ASP.NET Core 2.x uniquement :** si identité n’est pas configurée en appelant `services.AddIdentity` dans `ConfigureServices`, une tentative d’authentification entraîne *ArgumentException : l’option 'SignInScheme' doit être fournie*. Le modèle de projet utilisé dans ce didacticiel permet de s’assurer que cette opération est effectuée.
* Si la base de données de site n’a pas été créé en appliquant la migration initiale, vous obtiendrez *une opération de base de données a échoué lors du traitement de la demande* erreur. Appuyez sur **s’appliquent les Migrations** pour créer la base de données et actualiser pour passer à l’erreur.

## <a name="next-steps"></a>Étapes suivantes

* Cet article a montré comment vous pouvez vous authentifier auprès de Microsoft. Vous pouvez suivre une approche similaire pour s’authentifier auprès d’autres fournisseurs répertoriés sur le [page précédente](index.md).

* Une fois que vous publiez votre site web à l’application web Azure, vous devez créer un nouveau `Password` dans le portail des développeurs Microsoft.

* Définir le `Authentication:Microsoft:ApplicationId` et `Authentication:Microsoft:Password` en tant que paramètres de l’application dans le portail Azure. Le système de configuration est configuré pour lire les clés à partir de variables d’environnement.

---
title: "Programme d’installation de Facebook connexion externe dans ASP.NET Core"
author: rick-anderson
description: "Ce didacticiel illustre l’intégration de l’authentification d’utilisateur de compte Facebook dans une application ASP.NET Core existante."
ms.author: riande
manager: wpickett
ms.date: 08/01/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/facebook-logins
ms.openlocfilehash: 2d36aa78f82b4a52a7c6a152bee2c4ca9923409f
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="configuring-facebook-authentication"></a>Configuration de l’authentification Facebook

Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel vous montre comment permettre aux utilisateurs de se connecter avec leur compte Facebook à l’aide d’un exemple de projet ASP.NET Core 2.0 créé sur le [page précédente](index.md). Nous commençons par créer un ID d’application Facebook en suivant le [étapes officiels](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Créer l’application en Facebook

*  Accédez à la [aux développeurs de Facebook application](https://developers.facebook.com/apps/) page et vous connecter. Si vous n’avez pas déjà un compte Facebook, utilisez le **souscrire à Facebook** sur la page de connexion pour créer un lien.

* Appuyez sur la **ajouter une nouvelle application** bouton dans le coin supérieur droit pour créer un nouvel ID d’application.

   ![Facebook pour le portail des développeurs ouvrir dans Microsoft Edge](index/_static/FBMyApps.png)

* Remplissez le formulaire, puis appuyez sur la **ID d’application créer** bouton.

   ![Créer un formulaire de nouvel ID d’application](index/_static/FBNewAppId.png)

* Sur le **sélectionner un produit** , cliquez sur **Set Up** sur la **connexion Facebook** carte.

   ![Page de configuration de produit](index/_static/FBProductSetup.png)
  
* Le **Quickstart** Assistant lancera avec **choisir une plate-forme** en tant que la première page. Ignorer l’Assistant pour l’instant, en cliquant sur le **paramètres** lien dans le menu de gauche :

   ![Démarrage rapide de Skip](index/_static/FBSkipQuickStart.png)

* Vous voyez s’afficher la **les paramètres Client OAuth** page :

![Page de paramètres OAuth du client](index/_static/FBOAuthSetup.png)

* Entrez votre développement URI avec */signin-facebook* ajoutées dans le **URI de redirection OAuth valide** champ (par exemple : `https://localhost:44320/signin-facebook`). L’authentification Facebook configurée plus loin dans ce didacticiel va gérer automatiquement les demandes à */signin-facebook* itinéraire pour implémenter le flux OAuth.

* Cliquez sur **enregistrer les modifications**.

* Cliquez sur le **tableau de bord** lien dans le volet de navigation gauche. 

    Dans cette page, prenez note de votre `App ID` et votre `App Secret`. Vous allez ajouter à la fois dans votre application ASP.NET Core dans la section suivante :

   ![Tableau de bord Facebook Developer](index/_static/FBDashboard.png)

* Lorsque vous déployez le site vous devez revoir la **connexion Facebook** page Installation et inscrire un URI public.

## <a name="store-facebook-app-id-and-app-secret"></a>Stocker les ID d’application Facebook et Secret d’application

Lier les paramètres sensibles tels que Facebook `App ID` et `App Secret` à votre configuration d’application à l’aide du [Secret Manager](xref:security/app-secrets). Pour les besoins de ce didacticiel, nommez les jetons `Authentication:Facebook:AppId` et `Authentication:Facebook:AppSecret`.

Exécutez les commandes suivantes pour stocker en toute sécurité `App ID` et `App Secret` à l’aide du Gestionnaire de Secret :

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>Configurer l’authentification Facebook

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Ajoutez le service de Facebook dans le `ConfigureServices` méthode dans le *Startup.cs* fichier :

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE[default settings configuration](includes/default-settings.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Installer le [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) package.

* Pour installer ce package avec Visual Studio 2017, cliquez sur le projet et sélectionnez **gérer les Packages NuGet**.
* Pour installer avec l’interface CLI de .NET Core, exécutez le code suivant dans votre répertoire de projet :

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

Ajouter l’intergiciel (middleware) Facebook dans le `Configure` méthode dans *Startup.cs* fichier :

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

Consultez le [FacebookOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.facebookoptions) référence des API pour plus d’informations sur les options de configuration prises en charge par l’authentification Facebook. Options de configuration peuvent être utilisées pour :

* Demander des informations différentes sur l’utilisateur.
* Ajoutez des arguments de chaîne de requête pour personnaliser l’expérience de connexion.

## <a name="sign-in-with-facebook"></a>Se connecter avec Facebook

Exécutez votre application et cliquez sur **connecter**. Vous voyez une option pour vous connecter avec Facebook.

![Application Web : utilisateur non authentifié](index/_static/DoneFacebook.png)

Lorsque vous cliquez sur **Facebook**, vous êtes redirigé vers Facebook pour l’authentification :

![Page d’authentification Facebook](index/_static/FBLogin.png)

Les demandes d’authentification de Facebook publique profil et adresse de messagerie par défaut :

![Page d’authentification Facebook](index/_static/FBLoginDone.png)

Une fois que vous entrez vos informations d’identification de Facebook, vous êtes redirigé vers votre site où vous pouvez définir votre adresse de messagerie.

Vous êtes désormais connecté à l’aide de vos informations d’identification de Facebook :

![Application Web : utilisateur authentifié](index/_static/Done.png)

## <a name="troubleshooting"></a>Résolution des problèmes

* **ASP.NET Core 2.x uniquement :** si identité n’est pas configurée en appelant `services.AddIdentity` dans `ConfigureServices`, une tentative d’authentification entraîne *ArgumentException : l’option 'SignInScheme' doit être fournie*. Le modèle de projet utilisé dans ce didacticiel permet de s’assurer que cette opération est effectuée.
* Si la base de données de site n’a pas été créé en appliquant la migration initiale, vous obtenez *une opération de base de données a échoué lors du traitement de la demande* erreur. Appuyez sur **s’appliquent les Migrations** pour créer la base de données et actualiser pour passer à l’erreur.

## <a name="next-steps"></a>Étapes suivantes

* Cet article a montré comment vous pouvez vous authentifier avec Facebook. Vous pouvez suivre une approche similaire pour s’authentifier auprès d’autres fournisseurs répertoriés sur le [page précédente](index.md).

* Une fois que vous publiez votre site web à l’application web Azure, vous devez réinitialiser le `AppSecret` dans le portail des développeurs Facebook.

* Définir le `Authentication:Facebook:AppId` et `Authentication:Facebook:AppSecret` en tant que paramètres de l’application dans le portail Azure. Le système de configuration est configuré pour lire les clés à partir de variables d’environnement.

---
title: "Programme d’installation de la connexion externe Google dans ASP.NET Core"
author: rick-anderson
description: "Programme d’installation de la connexion externe Google dans ASP.NET Core"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/google-logins
ms.openlocfilehash: 7e37a8af4ae5a957483fa5f4a89ea4e8999a3d1d
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2017
---
# <a name="configuring-google-authentication-in-aspnet-core"></a>Configuration de l’authentification Google dans ASP.NET Core

<a name=security-authentication-google-logins></a>

Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel vous montre comment permettre aux utilisateurs de se connecter avec leur compte Google + à l’aide d’un exemple de projet ASP.NET Core 2.0 créé sur le [page précédente](index.md). Nous allons commencer en suivant le [étapes officiels](https://developers.google.com/identity/sign-in/web/devconsole-project) pour créer une nouvelle application de Console des API Google.

## <a name="create-the-app-in-google-api-console"></a>Créer l’application dans la Console des API Google

* Accédez à [https://console.developers.google.com/projectselector/apis/library](https://console.developers.google.com/projectselector/apis/library) et connectez-vous. Si vous n’avez pas encore un compte Google, utilisez **davantage d’options** > **[créer compte](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api) ** lien pour en créer un :

![Console des API Google](index/_static/GoogleConsoleLogin.png)

* Vous êtes redirigé vers **bibliothèque API Manager** page :

![Page de la bibliothèque d’API Manager](index/_static/GoogleConsoleSwitchboard.png)

* Appuyez sur **créer** et entrez votre **nom du projet**:

![Boîte de dialogue Nouveau projet](index/_static/GoogleConsoleNewProj.png)

* Après acceptation de la boîte de dialogue, vous êtes redirigé vers la page de bibliothèque qui vous permet de choisir les fonctionnalités pour votre nouvelle application. Rechercher **Google + API** dans la liste et cliquez sur son lien pour ajouter la fonctionnalité d’API :

![Page de la bibliothèque d’API Manager](index/_static/GoogleConsoleChooseApi.png)

* La page de l’API nouvellement ajoutée s’affiche. Appuyez sur **activer** pour ajouter Google + signe fonctionnalité à votre application :

![Page Gestionnaire de l’API Google + API](index/_static/GoogleConsoleEnableApi.png)

* Après l’activation de l’API, appuyez sur **créer les informations d’identification** pour configurer les clés secrètes :

![Page Gestionnaire de l’API Google + API](index/_static/GoogleConsoleGoCredentials.png)

* Choisissez :
   * **Google + API**
   * **Le serveur Web (par exemple, node.js, Tomcat)**, et
   * **Données utilisateur**:

![Page informations d’identification de l’API Gestionnaire : savoir quel type d’informations d’identification vous avez besoin de panneau de configuration](index/_static/GoogleConsoleChooseCred.png)

* Appuyez sur **les informations d’identification ai-je besoin ?** décrites à la deuxième étape de configuration de l’application, **créer un ID de client OAuth 2.0**:

![Page informations d’identification de l’API Gestionnaire : créer un ID de client OAuth 2.0](index/_static/GoogleConsoleCreateClient.png)

* Étant donné que nous allons créer un projet Google + avec simplement une fonction (connexion), nous pouvons entrer le même **nom** de l’ID de client OAuth 2.0 que celui que nous avons utilisé pour le projet.

* Entrez votre développement URI avec */signin-google* ajoutées dans le **URI de redirection autorisés** champ (par exemple : `https://localhost:44320/signin-google`). L’authentification Google configurée plus loin dans ce didacticiel va gérer automatiquement les demandes à */signin-google* itinéraire pour implémenter le flux OAuth.

* Appuyez sur TAB pour ajouter le **URI de redirection autorisés** entrée.

* Appuyez sur **créer un identifiant client**, ce qui vous permet de la troisième étape, **configurer à l’écran de consentement OAuth 2.0**:

![Page informations d’identification de l’API Gestionnaire : configurer l’écran de consentement OAuth 2.0](index/_static/GoogleConsoleAddCred.png)

* Entrez votre publique **adresse de messagerie** et **nom de produit** indiqué pour votre application lorsque Google + invite l’utilisateur à se connecter. Options supplémentaires sont disponibles sous **plus d’options de personnalisation**.

* Appuyez sur **continuer** pour passer à la dernière étape, **télécharger les informations d’identification**:

![Page informations d’identification de l’API Gestionnaire : télécharger des informations d’identification](index/_static/GoogleConsoleFinish.png)

* Appuyez sur **télécharger** pour enregistrer un fichier JSON comportant des secrets de l’application, et **fait** pour terminer la création de la nouvelle application.

* Lorsque vous déployez le site, vous devez revoir la **Google Console** et inscrire une nouvelle url publique.

## <a name="store-google-clientid-and-clientsecret"></a>Magasin Google ClientID et ClientSecret

Lier les paramètres sensibles telles que Google `Client ID` et `Client Secret` à votre configuration d’application à l’aide du [Secret Manager](../../app-secrets.md). Pour les besoins de ce didacticiel, nommez les jetons `Authentication:Google:ClientId` et `Authentication:Google:ClientSecret`.

Les valeurs de ces jetons sont accessibles dans le fichier JSON téléchargé à l’étape précédente sous `web.client_id` et `web.client_secret`.

## <a name="configure-google-authentication"></a>Configurer l’authentification Google

Le modèle de projet utilisé dans ce didacticiel garantit que [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) package est installé.

 * Pour installer ce package avec Visual Studio 2017, cliquez sur le projet et sélectionnez **gérer les Packages NuGet**.
 * Pour installer avec l’interface CLI de .NET Core, exécutez le code suivant dans votre répertoire de projet :

   `dotnet add package Microsoft.AspNetCore.Authentication.Google`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Ajoutez le service Google dans le `ConfigureServices` méthode dans *Startup.cs* fichier :

```csharp
services.AddAuthentication().AddGoogle(googleOptions =>
{
    googleOptions.ClientId = Configuration["Authentication:Google:ClientId"];
    googleOptions.ClientSecret = Configuration["Authentication:Google:ClientSecret"];
});
```

Le `AddAuthentication` méthode doit uniquement être appelée qu’une seule fois lors de l’ajout de plusieurs fournisseurs d’authentification. Les appels suivants à ce dernier ont la possibilité de remplacement de tous configurés précédemment [AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) propriétés.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Ajouter l’intergiciel (middleware) Google dans le `Configure` méthode dans *Startup.cs* fichier :

```csharp
app.UseGoogleAuthentication(new GoogleOptions()
{
    ClientId = Configuration["Authentication:Google:ClientId"],
    ClientSecret = Configuration["Authentication:Google:ClientSecret"]
});
```

---

Consultez le [GoogleOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.googleoptions) référence des API pour plus d’informations sur les options de configuration prises en charge par l’authentification Google. Cela peut être utilisé pour demander des différentes informations relatives à l’utilisateur.

## <a name="sign-in-with-google"></a>Se connecter avec Google

Exécutez votre application et cliquez sur **connecter**. Une option pour vous connecter avec Google s’affiche :

![Application Web s’exécutant dans Microsoft Edge : utilisateur non authentifié](index/_static/DoneGoogle.png)

Lorsque vous cliquez sur Google, vous êtes redirigé vers Google pour l’authentification :

![Boîte de dialogue authentification Google](index/_static/GoogleLogin.png)

Après avoir entré vos informations d’identification Google, puis vous êtes redirigé vers le site web où vous pouvez définir votre adresse de messagerie.

Vous êtes désormais connecté à l’aide de vos informations d’identification Google :

![Application Web s’exécutant dans Microsoft Edge : utilisateur authentifié](index/_static/Done.png)

## <a name="troubleshooting"></a>Résolution des problèmes

* Si vous recevez un `403 (Forbidden)` page d’erreur à partir de votre propre application lors de l’exécution en mode de développement (ou pause dans le débogueur avec le même message d’erreur), vérifiez que **Google + API** a été activée dans le **bibliothèque d’API Manager** en suivant les étapes répertoriées [plus haut dans cette page](#create-the-app-in-google-api-console). Si la connexion ne fonctionne pas et que vous n’obtenez pas les erreurs, passez en mode de développement pour rendre le problème plus facile à déboguer.
* **ASP.NET Core 2.x uniquement :** si identité n’est pas configurée en appelant `services.AddIdentity` dans `ConfigureServices`, une tentative d’authentification entraîne *ArgumentException : l’option 'SignInScheme' doit être fournie*. Le modèle de projet utilisé dans ce didacticiel permet de s’assurer que cette opération est effectuée.
* Si la base de données de site n’a pas été créé en appliquant la migration initiale, vous obtiendrez *une opération de base de données a échoué lors du traitement de la demande* erreur. Appuyez sur **s’appliquent les Migrations** pour créer la base de données et actualiser pour passer à l’erreur.

## <a name="next-steps"></a>Étapes suivantes

* Cet article a montré comment vous pouvez vous authentifier avec Google. Vous pouvez suivre une approche similaire pour s’authentifier auprès d’autres fournisseurs répertoriés sur le [page précédente](index.md).

* Une fois que vous publiez votre site web à l’application web Azure, vous devez réinitialiser le `ClientSecret` dans la Console des API Google.

* Définir le `Authentication:Google:ClientId` et `Authentication:Google:ClientSecret` en tant que paramètres de l’application dans le portail Azure. Le système de configuration est configuré pour lire les clés à partir de variables d’environnement.

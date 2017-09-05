---
title: "Programme d’installation de Twitter connexion externe"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 11/1/2016
ms.topic: article
ms.assetid: E5931607-31C0-4B20-B416-85E3550F5EA8
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/twitter-logins
ms.openlocfilehash: 800f98285859a54198b76411aea000384de05cd3
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2017
---
# <a name="configuring-twitter-authentication"></a>Configuration de l’authentification Twitter

<a name=security-authentication-twitter-logins></a>

Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel vous montre comment permettre aux utilisateurs de [se connecter avec leur compte Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) à l’aide d’un exemple de projet ASP.NET Core 2.0 créé sur le [page précédente](index.md).

## <a name="create-the-app-in-twitter"></a>Créer l’application en Twitter

* Accédez à [https://apps.twitter.com/](https://apps.twitter.com/) et connectez-vous. Si vous n’avez pas encore un compte Twitter, utilisez le  **[s’inscrire maintenant](https://twitter.com/signup)**  lien pour en créer un. Une fois connecté, le **gestion des applications** page s’affiche :

![Gestion des applications Twitter ouvert dans Microsoft Edge](index/_static/TwitterAppManage.png)

* Appuyez sur **créer une application** et remplir l’application **nom**, **Description** public et **site Web** URI (Cela peut être temporaire jusqu'à ce que vous Enregistrez le domaine nom) :

![Créer une page d’application](index/_static/TwitterCreate.png)

* Entrez votre développement URI avec */signin-twitter* ajoutées dans le **URI de redirection OAuth valide** champ (par exemple : `https://localhost:44320/signin-twitter`). Le schéma d’authentification Twitter configuré plus loin dans ce didacticiel va gérer automatiquement les demandes à */signin-twitter* itinéraire pour implémenter le flux OAuth.

* Remplissez le reste du formulaire, puis appuyez sur **créer votre application Twitter**. Détails de l’application sont affichent :

![Onglet Détails de la page d’Application](index/_static/TwitterAppDetails.png)

* Lorsque vous déployez le site, vous devez revoir la **gestion des applications** page et inscrire un URI public.

## <a name="storing-twitter-consumerkey-and-consumersecret"></a>Le stockage Twitter ConsumerKey et ConsumerSecret

Lier les paramètres sensibles tels que Twitter `Consumer Key` et `Consumer Secret` à votre configuration d’application à l’aide du [Secret Manager](../../app-secrets.md). Pour les besoins de ce didacticiel, nommez les jetons `Authentication:Twitter:ConsumerKey` et `Authentication:Twitter:ConsumerSecret`.

Ces jetons sont accessibles sur le **clés et les jetons d’accès** onglet après la création de votre nouvelle application Twitter :

![Onglet clés et les jetons d’accès](index/_static/TwitterKeys.png)

## <a name="configure-twitter-authentication"></a>Configurer l’authentification Twitter

Le modèle de projet utilisé dans ce didacticiel garantit que [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter) package est déjà installé.

* Pour installer ce package avec Visual Studio 2017, cliquez sur le projet et sélectionnez **gérer les Packages NuGet**.
* Pour installer avec l’interface CLI de .NET Core, exécutez le code suivant dans votre répertoire de projet :

   `dotnet add package Microsoft.AspNetCore.Authentication.Twitter`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Ajoutez le service de Twitter dans le `ConfigureServices` méthode dans *Startup.cs* fichier :

```csharp
services.AddAuthentication().AddTwitter(twitterOptions =>
{
    twitterOptions.ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"];
    twitterOptions.ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"];
});
```

Le `AddAuthentication` méthode doit uniquement être appelée qu’une seule fois lors de l’ajout de plusieurs fournisseurs d’authentification. Les appels suivants à ce dernier ont la possibilité de remplacement de tous configurés précédemment [AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) propriétés.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Ajouter l’intergiciel (middleware) Twitter dans le `Configure` méthode dans *Startup.cs* fichier :

```csharp
app.UseTwitterAuthentication(new TwitterOptions()
{
    ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"],
    ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"]
});
```

---

Consultez le [TwitterOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.twitteroptions) référence des API pour plus d’informations sur les options de configuration prises en charge par l’authentification Twitter. Cela peut être utilisé pour demander des différentes informations relatives à l’utilisateur.

## <a name="sign-in-with-twitter"></a>Se connecter avec Twitter

Exécutez votre application et cliquez sur **connecter**. Une option pour vous connecter avec Twitter s’affiche :

![Application Web : utilisateur non authentifié](index/_static/DoneTwitter.png)

En cliquant sur **Twitter** redirige vers Twitter pour l’authentification :

![Page d’authentification Twitter](index/_static/TwitterLogin.png)

Après avoir entré vos informations d’identification Twitter, vous êtes redirigé vers le site web où vous pouvez définir votre adresse de messagerie.

Vous êtes désormais connecté à l’aide de vos informations d’identification Twitter :

![Application Web : utilisateur authentifié](index/_static/Done.png)

## <a name="troubleshooting"></a>Résolution des problèmes

* **ASP.NET Core 2.x uniquement :** si identité n’est pas configurée en appelant `services.AddIdentity` dans `ConfigureServices`, une tentative d’authentification entraîne *ArgumentException : l’option 'SignInScheme' doit être fournie*. Le modèle de projet utilisé dans ce didacticiel permet de s’assurer que cette opération est effectuée.
* Si la base de données de site n’a pas été créé en appliquant la migration initiale, vous obtiendrez *une opération de base de données a échoué lors du traitement de la demande* erreur. Appuyez sur **s’appliquent les Migrations** pour créer la base de données et actualiser pour passer à l’erreur.

## <a name="next-steps"></a>Étapes suivantes

* Cet article a montré comment vous pouvez vous authentifier avec Twitter. Vous pouvez suivre une approche similaire pour s’authentifier auprès d’autres fournisseurs répertoriés sur le [page précédente](index.md).

* Une fois que vous publiez votre site web à l’application web Azure, vous devez réinitialiser le `ConsumerSecret` dans le portail des développeurs Twitter.

* Définir le `Authentication:Twitter:ConsumerKey` et `Authentication:Twitter:ConsumerSecret` en tant que paramètres de l’application dans le portail Azure. Le système de configuration est configuré pour lire les clés à partir de variables d’environnement.

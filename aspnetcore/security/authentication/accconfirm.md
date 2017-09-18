---
title: "Confirmation du compte et récupération de mot de passe dans ASP.NET Core"
author: rick-anderson
description: "Montre comment créer une application ASP.NET Core à la réinitialisation par courrier électronique de confirmation et le mot de passe."
keywords: "ASP.NET Core, mot de passe réinitialisé, e-mail de confirmation, sécurité"
ms.author: riande
manager: wpickett
ms.date: 07/19/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/accconfirm
ms.openlocfilehash: 8fe21b1a1ccb93c124dbd12a540b195400d45ef6
ms.sourcegitcommit: f8f6b5934bd071a349f5bc1e389365c52b1c00fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2017
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Confirmation du compte et récupération de mot de passe dans ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel vous montre comment créer une application ASP.NET Core à la réinitialisation par courrier électronique de confirmation et le mot de passe.

## <a name="create-a-new-aspnet-core-project"></a>Créer un projet ASP.NET Core

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Cette étape s’applique à Visual Studio sous Windows. Consultez la section suivante pour obtenir des instructions de l’interface CLI.

Le didacticiel requiert Visual Studio 2017 (Preview) 2 ou version ultérieure.

* Dans Visual Studio, créez un nouveau projet d’Application Web.
* Sélectionnez **ASP.NET Core 2.0**. L’illustration suivante show **.NET Core** sélectionné, mais vous pouvez sélectionner **.NET Framework**.
* Sélectionnez **modifier l’authentification** et la valeur **comptes d’utilisateur individuels**.
* Conservez la valeur par défaut **dans l’application des comptes d’utilisateurs de magasin**.

![Nouvelle boîte de dialogue projet indiquant « Radio de comptes d’utilisateur individuels » sélectionnée](accconfirm/_static/2.png)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Le didacticiel requiert Visual Studio 2017 ou version ultérieure.

* Dans Visual Studio, créez un nouveau projet d’Application Web.
* Sélectionnez **modifier l’authentification** et la valeur **comptes d’utilisateur individuels**.

![Nouvelle boîte de dialogue projet indiquant « Radio de comptes d’utilisateur individuels » sélectionnée](accconfirm/_static/indiv.png)

---

### <a name="net-core-cli-project-creation-for-macos-and-linux"></a>Création du projet .NET core CLI pour macOS et Linux

Si vous utilisez l’interface CLI ou SQLite, exécutez la commande suivante dans une fenêtre de commande :

```console
dotnet new mvc --auth Individual
```

* `--auth Individual`Spécifie le modèle de comptes d’utilisateur individuels.
* Sur Windows, ajoutez le `-uld` option. Le `-uld` option permet de créer une chaîne de connexion de base de données locale plutôt que dans une base de données SQLite.
* Exécutez `new mvc --help` pour obtenir de l’aide sur cette commande.

## <a name="test-new-user-registration"></a>Nouvelle inscription de l’utilisateur de test

Exécuter l’application, sélectionnez le **inscrire** lier et inscrire un utilisateur. Suivez les instructions pour exécuter les migrations d’Entity Framework Core. À ce stade, la seule validation de l’adresse de messagerie est avec le [[EmailAddress]](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribut. Une fois que vous soumettez l’inscription, vous êtes connecté à l’application. Plus loin dans ce didacticiel, nous allons modifier cela pour les nouveaux utilisateurs ne peuvent pas se connecter jusqu'à ce que leur courrier électronique a été validée.

## <a name="view-the-identity-database"></a>Afficher la base de données d’identité

# <a name="sql-servertabsql-server"></a>[SQL Server](#tab/sql-server)

* À partir de la **vue** menu, sélectionnez **l’Explorateur d’objets SQL Server** (SSOX). 
* Accédez à **(localdb) MSSQLLocalDB (SQL Server 13)**. Avec le bouton droit sur **dbo. AspNetUsers** > **afficher les données**:

![Menu contextuel sur la table AspNetUsers dans l’Explorateur d’objets SQL Server](accconfirm/_static/ssox.png)

Remarque la `EmailConfirmed` champ est `False`.

Vous pouvez souhaiter réutiliser ce courrier électronique à l’étape suivante lors de l’application envoie un message électronique de confirmation. Avec le bouton droit sur la ligne, puis sélectionnez **supprimer**. Suppression de l’adresse de messagerie alias maintenant facilitera dans les étapes suivantes.

# <a name="sqlitetabsqlite"></a>[SQLite](#tab/sqlite)

Consultez [utilisation de SQLite dans un projet ASP.NET MVC de base](xref:tutorials/first-mvc-app-xplat/working-with-sql) pour obtenir des instructions sur l’affichage de la base de données SQLite. 

---

## <a name="require-ssl-and-setup-iis-express-for-ssl"></a>Exiger le protocole SSL et le programme d’installation d’IIS Express pour le protocole SSL

Consultez [en appliquant SSL](xref:security/enforcing-ssl).

<a name="prevent-login-at-registration"></a>
## <a name="require-email-confirmation"></a>Demander confirmation de courrier électronique

Il est recommandé de confirmer l’adresse de messagerie d’un nouvel enregistrement d’utilisateur pour vérifier qu’ils n’empruntent pas une autre personne (autrement dit, ils n’ont pas inscrit avec par quelqu'un d’autre adresse de messagerie). Supposons que vous aviez un forum de discussion, et vous souhaitez empêcher «yli@example.com« lors de l’inscription en tant que »nolivetto@contoso.com. » Sans la confirmation par courrier électronique, «nolivetto@contoso.com« Impossible d’obtenir le courrier indésirable à partir de votre application. Supposons que l’utilisateur inscrit par inadvertance en tant que «ylo@example.com» et vous n’aviez pas remarqué la faute d’orthographe de « yli », ils semblent ne pas pouvoir à utiliser la récupération de mot de passe, car l’application n’a pas leur courrier électronique correct. E-mail de confirmation offre uniquement une protection limitée de robots et ne fournit la protection à partir des expéditeurs déterminés ayant plusieurs alias de messagerie de travail qu’ils peuvent utiliser pour enregistrer.

En règle générale, vous souhaitez empêcher les nouveaux utilisateurs à partir de la validation des données à votre site web avant d’avoir un message électronique de confirmation. 

Mise à jour `ConfigureServices` pour exiger un message électronique de confirmation :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](accconfirm/sample/WebPW/Startup.cs?name=snippet1&highlight=6-9)]


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](accconfirm/sample/WebApp1/Startup.cs?name=snippet1&highlight=13-16)]

---

 
```csharp
config.SignIn.RequireConfirmedEmail = true;
```
La ligne précédente empêche les utilisateurs enregistrés d’étant connecté en tant que leur adresse de messagerie est confirmée. Toutefois, cette ligne n’empêche pas les nouveaux utilisateurs ne soient enregistrées dans après leur inscrivent. Le code par défaut se connecte un utilisateur après leur inscrivent. Une fois qu’ils se déconnecter, ils ne pourront pas se connecter à nouveau jusqu'à ce qu’ils s’inscrivent. Plus loin dans ce didacticiel, nous allons modifier l’utilisateur de code donc nouvellement inscrit sont **pas** connecté.

### <a name="configure-email-provider"></a>Configurer le fournisseur de messagerie

Dans ce didacticiel, SendGrid est utilisé pour envoyer un courrier électronique. Vous avez besoin d’un compte SendGrid et une clé pour envoyer un courrier électronique. Vous pouvez utiliser d’autres fournisseurs de messagerie. ASP.NET Core 2.x inclut `System.Net.Mail`, qui vous permet d’envoyer par courrier électronique à partir de votre application. Nous vous recommandons de qu'utiliser SendGrid ou un autre service de messagerie pour envoyer un courrier électronique.

Le [modèle d’Options](xref:fundamentals/configuration#options-config-objects) est utilisé pour accéder aux paramètres de compte et une clé utilisateur. Pour plus d’informations, consultez [configuration](xref:fundamentals/configuration).

Créez une classe pour extraire la clé de sécuriser la messagerie électronique. Pour cet exemple, le `AuthMessageSenderOptions` classe est créée dans le *Services/AuthMessageSenderOptions.cs* fichier.

[!code-csharp[Main](accconfirm/sample/WebApp1/Services/AuthMessageSenderOptions.cs?name=snippet1)]

Définir le `SendGridUser` et `SendGridKey` avec la [outil Gestionnaire de secret](../app-secrets.md). Exemple :

```none
C:\WebAppl\src\WebApp1>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Sous Windows, Gestionnaire de secret principal stocke vos paires de clés/valeur dans un *secrets.json* fichier dans le répertoire %APPDATA%/Microsoft/UserSecrets/ < WebAppName-userSecretsId >.

Le contenu de la *secrets.json* fichier ne sont pas chiffrés. Le *secrets.json* fichier est présenté ci-dessous (le `SendGridKey` valeur a été supprimée.)

  ```json
  {
    "SendGridUser": "RickAndMSFT",
    "SendGridKey": "<key removed>"
  }
  ```

### <a name="configure-startup-to-use-authmessagesenderoptions"></a>Configurer le démarrage pour utiliser AuthMessageSenderOptions

Ajouter `AuthMessageSenderOptions` au conteneur de service à la fin de la `ConfigureServices` méthode dans le *Startup.cs* fichier :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](accconfirm/sample/WebPW/Startup.cs?name=snippet1&highlight=18)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)
[!code-csharp[Main](accconfirm/sample/WebApp1/Startup.cs?name=snippet1&highlight=26)]

---

### <a name="configure-the-authmessagesender-class"></a>Configurer la classe AuthMessageSender

Ce didacticiel montre comment ajouter des notifications par courrier électronique via [SendGrid](https://sendgrid.com/), mais vous pouvez envoyer par courrier électronique à l’aide de SMTP et autres mécanismes.

* Installer le `SendGrid` package NuGet. À partir de la Console du Gestionnaire de Package, entrez ce qui suit la commande suivante :

  `Install-Package SendGrid`

* Consultez [prise en main SendGrid gratuitement](https://sendgrid.com/free/) s’inscrire pour un compte SendGrid gratuit.

#### <a name="configure-sendgrid"></a>Configurer SendGrid

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

* Ajoutez le code dans *Services/EmailSender.cs* similaire à ce qui suit pour configurer SendGrid :

[!code-csharp[Main](accconfirm/sample/WebPW/Services/EmailSender.cs)]


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)
* Ajoutez le code dans *Services/MessageServices.cs* similaire à ce qui suit pour configurer SendGrid :

[!code-csharp[Main](accconfirm/sample/WebApp1/Services/MessageServices.cs)]

---

## <a name="enable-account-confirmation-and-password-recovery"></a>Activer la récupération de confirmation et le mot de passe du compte

Le modèle a le code pour la récupération de confirmation et le mot de passe du compte. Rechercher les `[HttpPost] Register` méthode dans le *AccountController.cs* fichier.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Interdire nouvellement inscrit qui est automatiquement connecté en supprimant la ligne suivante :

```csharp 
await _signInManager.SignInAsync(user, isPersistent: false);
```

La méthode complète s’affiche avec la ligne modifiée mis en surbrillance :

[!code-csharp[Main](accconfirm/sample/WebPW/Controllers/AccountController.cs?highlight=19&name=snippet_Register)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Ne pas commenter le code pour activer la confirmation du compte.

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=16-25&name=snippet_Register)]

Remarque : Nous avons également empêche un utilisateur qui vient d’être inscrit qui est automatiquement connecté en supprimant la ligne suivante :

```csharp 
//await _signInManager.SignInAsync(user, isPersistent: false);
```

Activer la récupération de mot de passe par le code de commentaire de la `ForgotPassword` action dans le *Controllers/AccountController.cs* fichier.

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=17-23&name=snippet_ForgotPassword)]

Supprimez les commentaires de l’élément form *Views/Account/ForgotPassword.cshtml*. Vous souhaiterez peut-être supprimer le `<p> For more information on how to enable reset password ... </p>` élément qui contient un lien vers cet article.

[!code-html[Main](accconfirm/sample/WebApp1/Views/Account/ForgotPassword.cshtml?highlight=7-10,12,28)]

---

## <a name="register-confirm-email-and-reset-password"></a>Inscrire, par courrier électronique de confirmation et réinitialiser le mot de passe

Exécuter l’application web et testez la confirmation du compte et le flux de récupération de mot de passe.

* Exécutez l’application et inscrire un nouvel utilisateur

 ![Affichage du livre de comptes application Web](accconfirm/_static/loginaccconfirm1.png)

* Vérifiez votre adresse de messagerie pour le lien de confirmation du compte. Consultez [déboguer messagerie](#debug) si vous n’obtenez pas le message électronique.
* Cliquez sur le lien pour confirmer votre adresse de messagerie.
* Connectez-vous avec votre adresse électronique et un mot de passe.
* Fermez la session.

### <a name="view-the-manage-page"></a>Afficher la page de gestion

Sélectionnez votre nom d’utilisateur dans le navigateur : ![fenêtre de navigateur avec le nom d’utilisateur](accconfirm/_static/un.png)

Vous devrez peut-être développer la barre de navigation pour afficher le nom d’utilisateur.

![barre de navigation](accconfirm/_static/x.png)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

La page de gestion s’affiche avec le **profil** onglet sélectionné. Le **messagerie** affiche une case à cocher indiquant l’adresse de messagerie a été confirmée. 

![page Gérer](accconfirm/_static/rick2.png)


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Nous parlerons de cette page plus tard dans le didacticiel.
![page Gérer](accconfirm/_static/rick2.png)

---

### <a name="test-password-reset"></a>Réinitialisation de mot de passe de test

* Si vous êtes connecté, sélectionnez **déconnexion**.  
* Sélectionnez le **connecter** lien et sélectionnez le **vous avez oublié votre mot de passe ?** lien.
* Entrez l’e-mail que vous avez utilisé pour enregistrer le compte.
* Un e-mail avec un lien pour réinitialiser votre mot de passe sera envoyé. Vérifiez votre adresse de messagerie et cliquez sur le lien pour réinitialiser votre mot de passe.  Une fois votre mot de passe a été réinitialisé avec succès, vous pouvez vous connecter avec votre adresse électronique et un nouveau mot de passe.

<a name="debug"></a>

### <a name="debug-email"></a>Déboguer le courrier électronique

Si vous ne parvenez le travail de la messagerie :

* Examinez le [activité de messagerie](https://sendgrid.com/docs/User_Guide/email_activity.html) page.
* Vérifiez votre dossier courrier indésirable.
* Essayez un autre alias de messagerie électronique sur un fournisseur de messagerie différents (Microsoft, Yahoo, Gmail, etc.).
* Créer un [application console pour envoyer un courrier électronique](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html).
* Essayez d’envoyer à des comptes de messagerie différents.

**Remarque :** meilleure pratique de sécurité est de ne pas utiliser les secrets de fabrication dans le développement et de test. Si vous publiez l’application sur Azure, vous pouvez définir les clés secrètes SendGrid en tant que paramètres de l’application dans le portail de l’application Web Azure. Le système de configuration est configuré pour lire les clés à partir de variables d’environnement.

## <a name="prevent-login-at-registration"></a>Empêcher la connexion lors de l’inscription

Avec les modèles en cours, une fois qu’un utilisateur termine le formulaire d’inscription, ils ne sont connectés (authentifiés). En règle générale, vous souhaitez confirmer leur adresse de messagerie avant de les enregistrer. Dans la section ci-dessous, nous allons modifier le code pour exiger des utilisateurs ont un message électronique de confirmation avant d’être enregistrées. Mise à jour la `[HttpPost] Login` action dans le *AccountController.cs* fichier avec les modifications en surbrillance suivantes.

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=11-21&name=snippet_Login)]

**Remarque :** meilleure pratique de sécurité est de ne pas utiliser les secrets de fabrication dans le développement et de test. Si vous publiez l’application sur Azure, vous pouvez définir les clés secrètes SendGrid en tant que paramètres de l’application dans le portail de l’application Web Azure. Le système de configuration est configuré pour lire les clés à partir de variables d’environnement.


## <a name="combine-social-and-local-login-accounts"></a>Combiner des comptes de connexion de réseaux sociaux et local

Remarque : Cette section s’applique uniquement aux ASP.NET Core 1.x. Pour ASP.NET 2.x de base, consultez [cela](https://github.com/aspnet/Docs/issues/3753) problème.

Pour terminer cette section, vous devez d’abord activer un fournisseur d’authentification externe. Consultez [activation de l’authentification à l’aide de Facebook, Google et autres fournisseurs externes](social/index.md).

Vous pouvez combiner les comptes locaux et réseaux sociaux en cliquant sur le lien de votre messagerie. Dans l’ordre suivant, «RickAndMSFT@gmail.com» est tout d’abord créé en tant qu’une connexion locale ; Toutefois, vous pouvez créer le compte en tant qu’une connexion sociale tout d’abord, puis ajouter une connexion locale.

![Application Web : RickAndMSFT@gmail.com utilisateur authentifié](accconfirm/_static/rick.png)

Cliquez sur le **gérer** lien. Notez la valeur 0 externe (connexions sociales) associé à ce compte.

![Gérer les affichages](accconfirm/_static/manage.png)

Cliquez sur le lien vers un autre service de connexion et d’accepter les demandes d’application. Dans l’image ci-dessous, Facebook est le fournisseur d’authentification externes :

![Gestion de votre vue de logins externes répertoriant Facebook](accconfirm/_static/fb.png)

Les deux comptes ont été combinés. Vous ne pourrez pas vous connecter avec un compte. Vous pourriez vos utilisateurs pour ajouter des comptes locaux au cas où le journal des réseaux sociaux dans le service d’authentification est arrêté, ou plus probable qu’ils ont perdu l’accès à leur compte sociaux.

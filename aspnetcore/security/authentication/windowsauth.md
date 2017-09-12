---
title: "Configurer l’authentification Windows dans ASP.NET Core"
author: ardalis
description: "Comment configurer l’authentification Windows dans ASP.NET Core"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 7/5/2017
ms.topic: article
ms.assetid: cf119f21-1a2b-49a2-b052-548ccb66ee83
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/windowsauth
ms.openlocfilehash: aa401f956d74680efd3964203af3e8866b129887
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Configurer l’authentification Windows dans ASP.NET Core

Par [Steve Smith](https://ardalis.com)

L’authentification Windows peut être configurée pour les applications ASP.NET Core hébergées par IIS ou WebListener.

## <a name="what-is-windows-authentication"></a>Qu’est l’authentification Windows

L’authentification Windows s’appuie sur le système d’exploitation pour authentifier les utilisateurs d’applications ASP.NET Core. Vous pouvez utiliser l’authentification Windows lorsque votre serveur s’exécute sur un réseau d’entreprise à l’aide des identités de domaine Active Directory ou d’autres comptes Windows pour identifier les utilisateurs. L’authentification Windows est une forme sécurisée d’authentification meilleures adaptée aux environnements intranet où les utilisateurs, les applications clientes et les serveurs web appartiennent au même domaine Windows.

[En savoir plus sur l’authentification Windows et l’installation pour IIS](https://docs.microsoft.com/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).

## <a name="enabling-windows-authentication-in-an-aspnet-core-application"></a>L’activation de l’authentification Windows dans une application ASP.NET Core

Le modèle d’Application Web de Visual Studio peut être configuré pour prendre en charge l’authentification Windows.

### <a name="using-the-windows-authentication-app-template"></a>À l’aide du modèle d’application de l’authentification Windows

Dans Visual Studio :
* Créez une application web ASP.NET Core. 
* Sélectionnez l’Application Web à partir de la liste des modèles.
* Sélectionnez le bouton Modifier l’authentification, sélectionnez **l’authentification Windows**. 

Exécuter l’application. Le nom d’utilisateur s’affiche dans le coin supérieur droit de l’application.

![Capture d’écran de navigateur de l’authentification Windows](windowsauth/_static/browser-screenshot.png)

Pour le travail de développement à l’aide d’IIS Express, le modèle fournit la configuration nécessaire pour utiliser l’authentification Windows. La section suivante montre comment configurer une application ASP.NET Core manuellement pour l’authentification Windows.

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a>Paramétrage de Visual Studio pour Windows et l’authentification anonyme

La page de propriétés de Visual Studio, onglet débogage fournit des cases à cocher pour l’authentification Windows et l’authentification anonyme.

![Capture d’écran de navigateur de l’authentification Windows](windowsauth/_static/vs-auth-property-menu.png)

Vous pouvez également configurer ces propriétés dans le `launchSettings.json` fichier :

```json
{
  "iisSettings": {
    "windowsAuthentication": true,
    "anonymousAuthentication": false,
    "iisExpress": {
      "applicationUrl": "http://localhost:52171/",
      "sslPort": 0
    }
  } // additional options trimmed
}
```

## <a name="enabling-windows-authentication-with-iis"></a>L’activation de l’authentification Windows avec IIS

IIS utilise le [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) (ANCM) pour héberger des applications ASP.NET Core. La ANCM flux l’authentification Windows sur le serveur IIS par défaut. Configuration de l’authentification Windows est effectuée dans les services IIS, pas le projet d’application. Les sections suivantes montrent comment utiliser le Gestionnaire des services Internet pour configurer une application ASP.NET Core pour utiliser l’authentification Windows :

### <a name="create-a-new-iis-site"></a>Créer un nouveau site IIS

Spécifiez un nom et un dossier et lui permettre de créer un nouveau pool d’applications.

### <a name="customize-authentication"></a>Personnaliser l’authentification

Ouvrez le menu de l’authentification pour le site.

![Menu de l’authentification IIS](windowsauth/_static/iis-authentication-menu.png)

Désactivez l’authentification anonyme et activer l’authentification Windows.

![Paramètres d’authentification IIS](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a>Publier votre projet dans le dossier de site IIS

À l’aide de Visual Studio ou l’interface CLI de base .NET, *publier* votre application dans le dossier de destination.

![Boîte de dialogue Publier de Visual Studio](windowsauth/_static/vs-publish-app.png)

En savoir plus sur [publication sur IIS](xref:publishing/iis).

Lancez l’application pour vérifier l’utilisation de l’authentification Windows.

## <a name="enabling-windows-authentication-with-weblistener"></a>L’activation de l’authentification Windows avec WebListener

Bien que Kestrel ne prend pas en charge l’authentification Windows, vous pouvez utiliser [WebListener](xref:fundamentals/servers/weblistener) pour prendre en charge les scénarios auto-hébergés sur Windows. L’exemple suivant configure l’hôte web de l’application pour utiliser WebListener avec l’authentification Windows :

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseWebListener(options =>
            {
                options.ListenerSettings.Authentication.Schemes = 
                    AuthenticationSchemes.Negotiate | AuthenticationSchemes.NTLM;
                options.ListenerSettings.Authentication.AllowAnonymous = false;
            })
            .UseContentRoot(Directory.GetCurrentDirectory())
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

## <a name="working-with-windows-authentication"></a>Utilisation de l’authentification Windows

Si votre application utilise l’authentification Windows et l’accès anonyme, vous pouvez utiliser la ``[Authorize]`` et ``[AllowAnonymous]`` attributs. Les applications qui n’ont pas activé anonyme ne nécessitent ne pas ``[Authorize]``; l’application est considérée comme nécessitant une authentification, les demandes anonymes sont rejetées. Notez que si le site IIS est configuré **pas** pour autoriser l’accès anonyme, la ``[AllowAnonymous]`` attribut effectue **pas** autoriser les demandes anonymes. Le ``[AllowAnonymous]`` les substitutions d’attributs ``[Authorize]`` attribut d’utilisation dans les applications qui autorisent l’accès anonyme.

### <a name="impersonation"></a>Emprunt d'identité

ASP.NET Core n’implémente pas l’emprunt d’identité. Les applications s’exécutent avec l’identité de l’application pour toutes les demandes, à l’aide d’identité de processus ou pool d’application. Si vous avez besoin effectuer explicitement une action de la part d’un utilisateur, utilisez ``WindowsIdentity.RunImpersonated``. Exécuter une action unique dans ce contexte, puis fermez le contexte. Notez que ``RunImpersonated`` ne prend pas en charge asynchrone et ne doit pas être utilisé pour les scénarios complexes. Par exemple, la habillage demandes entières ou des chaînes d’intergiciel (middleware) n'est pas pris en charge ou recommandé.

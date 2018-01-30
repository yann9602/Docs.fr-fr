---
title: "Configurer l’authentification Windows dans ASP.NET Core"
author: ardalis
description: "Cet article décrit comment configurer l’authentification Windows dans ASP.NET Core, à l’aide d’IIS Express, IIS, HTTP.sys et WebListener."
manager: wpickett
ms.author: riande
ms.date: 10/24/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/windowsauth
ms.openlocfilehash: aaa14e2f2704a7cfa836c5524642d2138a3ae7c8
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="configure-windows-authentication-in-an-aspnet-core-app"></a>Configurer l’authentification Windows dans une application ASP.NET Core

Par [Steve Smith](https://ardalis.com) et [Scott Addie](https://twitter.com/Scott_Addie)

L’authentification Windows peut être configurée pour les applications ASP.NET Core hébergées par IIS, [HTTP.sys](xref:fundamentals/servers/httpsys), ou [WebListener](xref:fundamentals/servers/weblistener).

## <a name="what-is-windows-authentication"></a>Qu’est l’authentification Windows ?

L’authentification Windows s’appuie sur le système d’exploitation pour authentifier les utilisateurs d’applications ASP.NET Core. Vous pouvez utiliser l’authentification Windows lorsque votre serveur s’exécute sur un réseau d’entreprise à l’aide des identités de domaine Active Directory ou d’autres comptes Windows pour identifier les utilisateurs. L’authentification Windows est idéale pour les environnements intranet dans lequel les utilisateurs, les applications clientes et les serveurs web appartiennent au même domaine Windows.

[En savoir plus sur l’authentification Windows et l’installation pour IIS](https://docs.microsoft.com/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a>Activer l’authentification Windows dans une application ASP.NET Core

Le modèle d’Application Web de Visual Studio peut être configuré pour prendre en charge l’authentification Windows.

### <a name="use-the-windows-authentication-app-template"></a>Utilisez le modèle d’application de l’authentification Windows

Dans Visual Studio :
1. Créez une application web ASP.NET Core. 
1. Sélectionnez l’Application Web à partir de la liste des modèles.
1. Sélectionnez le **modifier l’authentification** sélectionnez **l’authentification Windows**. 

Exécutez l’application. Le nom d’utilisateur s’affiche dans le coin supérieur droit de l’application.

![Capture d’écran de navigateur de l’authentification Windows](windowsauth/_static/browser-screenshot.png)

Pour le travail de développement à l’aide d’IIS Express, le modèle fournit la configuration nécessaire pour utiliser l’authentification Windows. La section suivante montre comment configurer manuellement une application ASP.NET Core pour l’authentification Windows.

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a>Paramétrage de Visual Studio pour Windows et l’authentification anonyme

Le projet Visual Studio **propriétés** la page **déboguer** onglet fournit des cases à cocher pour l’authentification Windows et l’authentification anonyme.

![Capture d’écran de navigateur de l’authentification Windows](windowsauth/_static/vs-auth-property-menu.png)

Vous pouvez également ces deux propriétés peuvent être configurées dans le *launchSettings.json* fichier :

[!code-json[](windowsauth/sample/launchSettings.json?highlight=3-4)]

## <a name="enable-windows-authentication-with-iis"></a>Activer l’authentification Windows avec IIS

IIS utilise le [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) (ANCM) pour héberger des applications ASP.NET Core. La ANCM flux l’authentification Windows sur le serveur IIS par défaut. Configuration de l’authentification Windows est effectuée dans les services IIS, pas le projet d’application. Les sections suivantes montrent comment utiliser le Gestionnaire des services Internet pour configurer une application ASP.NET Core pour utiliser l’authentification Windows.

### <a name="create-a-new-iis-site"></a>Créer un nouveau site IIS

Spécifiez un nom et un dossier et lui permettre de créer un nouveau pool d’applications.

### <a name="customize-authentication"></a>Personnaliser l’authentification

Ouvrez le menu de l’authentification pour le site.

![Menu de l’authentification IIS](windowsauth/_static/iis-authentication-menu.png)

Désactivez l’authentification anonyme et activer l’authentification Windows.

![Paramètres d’authentification IIS](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a>Publier votre projet dans le dossier de site IIS

À l’aide de Visual Studio ou l’interface de ligne de base de .NET, de publier l’application dans le dossier de destination.

![Boîte de dialogue Publier de Visual Studio](windowsauth/_static/vs-publish-app.png)

En savoir plus sur [publication sur IIS](xref:host-and-deploy/iis/index).

Lancez l’application pour vérifier l’utilisation de l’authentification Windows.

## <a name="enable-windows-authentication-with-httpsys-or-weblistener"></a>Activer l’authentification Windows avec HTTP.sys ou WebListener

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Bien que Kestrel ne prend pas en charge l’authentification Windows, vous pouvez utiliser [HTTP.sys](xref:fundamentals/servers/httpsys) pour prendre en charge les scénarios auto-hébergés sur Windows. L’exemple suivant configure l’hôte web de l’application pour utiliser HTTP.sys avec l’authentification Windows :

[!code-csharp[](windowsauth/sample/Program2x.cs?highlight=9-14)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Bien que Kestrel ne prend pas en charge l’authentification Windows, vous pouvez utiliser [WebListener](xref:fundamentals/servers/weblistener) pour prendre en charge les scénarios auto-hébergés sur Windows. L’exemple suivant configure l’hôte web de l’application pour utiliser WebListener avec l’authentification Windows :

[!code-csharp[](windowsauth/sample/Program1x.cs?highlight=6-11)]

---

## <a name="work-with-windows-authentication"></a>Utiliser l’authentification Windows

L’état de configuration de l’accès anonyme détermine la façon dont le `[Authorize]` et `[AllowAnonymous]` attributs sont utilisés dans l’application. Les deux sections suivantes expliquent comment gérer les États de configuration non autorisés et autorisées de l’accès anonyme.

### <a name="disallow-anonymous-access"></a>Interdire l’accès anonyme

Lorsque l’authentification Windows est activée et l’accès anonyme est désactivé, le `[Authorize]` et `[AllowAnonymous]` attributs n’ont aucun effet. Si le site IIS (ou serveur HTTP.sys ou WebListener) est configuré pour interdire l’accès anonyme, la demande n’atteint jamais votre application. Pour cette raison, le `[AllowAnonymous]` attribut n’est pas applicable.

### <a name="allow-anonymous-access"></a>Autoriser l’accès anonyme

Lorsque l’authentification Windows et l’accès anonyme sont activées, utiliser le `[Authorize]` et `[AllowAnonymous]` attributs. Le `[Authorize]` attribut vous permet de sécuriser des parties de l’application qui a réellement requièrent l’authentification de Windows. Le `[AllowAnonymous]` les substitutions d’attributs `[Authorize]` attribut d’utilisation dans les applications qui autorisent l’accès anonyme. Consultez [une autorisation Simple](xref:security/authorization/simple) pour des détails d’utilisation de l’attribut.

Dans ASP.NET Core 2.x, le `[Authorize]` attribut nécessite une configuration supplémentaire dans *Startup.cs* contester les demandes anonymes pour l’authentification Windows. La configuration recommandée varie légèrement selon le serveur web utilisé.

#### <a name="iis"></a>IIS

Si vous utilisez IIS, ajoutez le code suivant à la `ConfigureServices` méthode : 

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a>HTTP.sys

Si l’aide de HTTP.sys, ajoutez le code suivant à la `ConfigureServices` méthode :

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a>Emprunt d'identité

ASP.NET Core n’implémente pas l’emprunt d’identité. Les applications s’exécutent avec l’identité de l’application pour toutes les demandes, à l’aide d’identité de processus ou pool d’application. Si vous avez besoin effectuer explicitement une action de la part d’un utilisateur, utilisez `WindowsIdentity.RunImpersonated`. Exécuter une action unique dans ce contexte, puis fermez le contexte.

[!code-csharp[](windowsauth/sample/Startup.cs?name=snippet_Impersonate&highlight=10-18)]

Notez que `RunImpersonated` ne prend pas en charge les opérations asynchrones et ne doivent pas être utilisés pour des scénarios complexes. Par exemple, habillage demandes entières ou des chaînes d’intergiciel (middleware) n’est pas pris en charge ou recommandé.

---

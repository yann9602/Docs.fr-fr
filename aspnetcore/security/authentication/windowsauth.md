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
ms.openlocfilehash: 008a647295334e957c33c6db7f80687645b3b928
ms.sourcegitcommit: 69b3255f8b6f5db9e7d21f391420602d7ba9f4db
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2017
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="82b76-104">Configurer l’authentification Windows dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82b76-104">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="82b76-105">Par [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="82b76-105">By [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="82b76-106">L’authentification Windows peut être configurée pour les applications ASP.NET Core hébergées par IIS ou WebListener.</span><span class="sxs-lookup"><span data-stu-id="82b76-106">Windows authentication can be configured for ASP.NET Core apps hosted with IIS or WebListener.</span></span>

## <a name="what-is-windows-authentication"></a><span data-ttu-id="82b76-107">Qu’est l’authentification Windows</span><span class="sxs-lookup"><span data-stu-id="82b76-107">What is Windows authentication</span></span>

<span data-ttu-id="82b76-108">L’authentification Windows s’appuie sur le système d’exploitation pour authentifier les utilisateurs d’applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82b76-108">Windows authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="82b76-109">Vous pouvez utiliser l’authentification Windows lorsque votre serveur s’exécute sur un réseau d’entreprise à l’aide des identités de domaine Active Directory ou d’autres comptes Windows pour identifier les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="82b76-109">You can use Windows authentication when your server runs on a corporate network using Active Directory domain identities or other Windows accounts to identify users.</span></span> <span data-ttu-id="82b76-110">L’authentification Windows est une forme sécurisée d’authentification meilleures adaptée aux environnements intranet où les utilisateurs, les applications clientes et les serveurs web appartiennent au même domaine Windows.</span><span class="sxs-lookup"><span data-stu-id="82b76-110">Windows authentication is a secure form of authentication best suited to intranet environments where users, client applications, and web servers belong to the same Windows domain.</span></span>

<span data-ttu-id="82b76-111">[En savoir plus sur l’authentification Windows et l’installation pour IIS](https://www.iis.net/configreference/system.webserver/security/authentication/windowsauthentication).</span><span class="sxs-lookup"><span data-stu-id="82b76-111">[Learn more about Windows Authentication and installing it for IIS](https://www.iis.net/configreference/system.webserver/security/authentication/windowsauthentication).</span></span>

## <a name="enabling-windows-authentication-in-an-aspnet-core-application"></a><span data-ttu-id="82b76-112">L’activation de l’authentification Windows dans une application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82b76-112">Enabling Windows authentication in an ASP.NET Core application</span></span>

<span data-ttu-id="82b76-113">Le modèle d’Application Web de Visual Studio peut être configuré pour prendre en charge l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="82b76-113">The Visual Studio Web Application template can be configured to support Windows authentication.</span></span>

### <a name="using-the-windows-authentication-app-template"></a><span data-ttu-id="82b76-114">À l’aide du modèle d’application de l’authentification Windows</span><span class="sxs-lookup"><span data-stu-id="82b76-114">Using the Windows authentication app template</span></span>

<span data-ttu-id="82b76-115">Dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="82b76-115">In Visual Studio:</span></span>
* <span data-ttu-id="82b76-116">Créer une Application Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82b76-116">Create a new ASP.NET Core Web Application.</span></span> 
* <span data-ttu-id="82b76-117">Sélectionnez l’Application Web à partir de la liste des modèles.</span><span class="sxs-lookup"><span data-stu-id="82b76-117">Select Web Application from the list of templates.</span></span>
* <span data-ttu-id="82b76-118">Sélectionnez le bouton Modifier l’authentification, sélectionnez **l’authentification Windows**.</span><span class="sxs-lookup"><span data-stu-id="82b76-118">Select the Change Authentication button and select **Windows Authentication**.</span></span> 

<span data-ttu-id="82b76-119">Exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="82b76-119">Run the app.</span></span> <span data-ttu-id="82b76-120">Le nom d’utilisateur s’affiche dans le coin supérieur droit de l’application.</span><span class="sxs-lookup"><span data-stu-id="82b76-120">The username appears in the top right of the app.</span></span>

![Capture d’écran de navigateur de l’authentification Windows](windowsauth/_static/browser-screenshot.png)

<span data-ttu-id="82b76-122">Pour le travail de développement à l’aide d’IIS Express, le modèle fournit la configuration nécessaire pour utiliser l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="82b76-122">For development work using IIS Express, the template provides all the configuration necessary to use Windows authentication.</span></span> <span data-ttu-id="82b76-123">La section suivante montre comment configurer une application ASP.NET Core manuellement pour l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="82b76-123">The next section shows how to configure an ASP.NET Core app manually for Windows authentication.</span></span>

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a><span data-ttu-id="82b76-124">Paramétrage de Visual Studio pour Windows et l’authentification anonyme</span><span class="sxs-lookup"><span data-stu-id="82b76-124">Visual Studio settings for Windows and anonymous authentication</span></span>

<span data-ttu-id="82b76-125">La page de propriétés de Visual Studio, onglet débogage fournit des cases à cocher pour l’authentification Windows et l’authentification anonyme.</span><span class="sxs-lookup"><span data-stu-id="82b76-125">The Visual Studio properties page, debug tab provides check boxes for Windows authentication and anonymous authentication.</span></span>

![Capture d’écran de navigateur de l’authentification Windows](windowsauth/_static/vs-auth-property-menu.png)

<span data-ttu-id="82b76-127">Vous pouvez également configurer ces propriétés dans le `launchSettings.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="82b76-127">You can also configure these properties in the `launchSettings.json` file:</span></span>

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

## <a name="enabling-windows-authentication-with-iis"></a><span data-ttu-id="82b76-128">L’activation de l’authentification Windows avec IIS</span><span class="sxs-lookup"><span data-stu-id="82b76-128">Enabling Windows Authentication with IIS</span></span>

<span data-ttu-id="82b76-129">IIS utilise le [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) (ANCM) pour héberger des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82b76-129">IIS uses the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) (ANCM) to host ASP.NET Core apps.</span></span> <span data-ttu-id="82b76-130">La ANCM flux l’authentification Windows sur le serveur IIS par défaut.</span><span class="sxs-lookup"><span data-stu-id="82b76-130">The ANCM flows Windows authentication to IIS by default.</span></span> <span data-ttu-id="82b76-131">Configuration de l’authentification Windows est effectuée dans les services IIS, pas le projet d’application.</span><span class="sxs-lookup"><span data-stu-id="82b76-131">Configuration of Windows authentication is done within IIS, not the application project.</span></span> <span data-ttu-id="82b76-132">Les sections suivantes montrent comment utiliser le Gestionnaire des services Internet pour configurer une application ASP.NET Core pour utiliser l’authentification Windows :</span><span class="sxs-lookup"><span data-stu-id="82b76-132">The following sections show how to use IIS Manager to configure an ASP.NET Core app to use Windows authentication:</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="82b76-133">Créer un nouveau site IIS</span><span class="sxs-lookup"><span data-stu-id="82b76-133">Create a new IIS site</span></span>

<span data-ttu-id="82b76-134">Spécifiez un nom et un dossier et lui permettre de créer un nouveau pool d’applications.</span><span class="sxs-lookup"><span data-stu-id="82b76-134">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="customize-authentication"></a><span data-ttu-id="82b76-135">Personnaliser l’authentification</span><span class="sxs-lookup"><span data-stu-id="82b76-135">Customize Authentication</span></span>

<span data-ttu-id="82b76-136">Ouvrez le menu de l’authentification pour le site.</span><span class="sxs-lookup"><span data-stu-id="82b76-136">Open the Authentication menu for the site.</span></span>

![Menu de l’authentification IIS](windowsauth/_static/iis-authentication-menu.png)

<span data-ttu-id="82b76-138">Désactivez l’authentification anonyme et activer l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="82b76-138">Disable Anonymous Authentication and enable Windows Authentication.</span></span>

![Paramètres d’authentification IIS](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a><span data-ttu-id="82b76-140">Publier votre projet dans le dossier de site IIS</span><span class="sxs-lookup"><span data-stu-id="82b76-140">Publish your project to the IIS site folder</span></span>

<span data-ttu-id="82b76-141">À l’aide de Visual Studio ou l’interface CLI de base .NET, *publier* votre application dans le dossier de destination.</span><span class="sxs-lookup"><span data-stu-id="82b76-141">Using Visual Studio or the .NET Core CLI, *publish* your app to the destination folder.</span></span>

![Boîte de dialogue Publier de Visual Studio](windowsauth/_static/vs-publish-app.png)

<span data-ttu-id="82b76-143">En savoir plus sur [publication sur IIS](https://docs.microsoft.com/aspnet/core/publishing/iis).</span><span class="sxs-lookup"><span data-stu-id="82b76-143">Learn more about [publishing to IIS](https://docs.microsoft.com/aspnet/core/publishing/iis).</span></span>

<span data-ttu-id="82b76-144">Lancez l’application pour vérifier l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="82b76-144">Launch the app to verify Windows authentication is working.</span></span>

## <a name="enabling-windows-authentication-with-weblistener"></a><span data-ttu-id="82b76-145">L’activation de l’authentification Windows avec WebListener</span><span class="sxs-lookup"><span data-stu-id="82b76-145">Enabling Windows authentication with WebListener</span></span>

<span data-ttu-id="82b76-146">Bien que Kestrel ne prend pas en charge l’authentification Windows, vous pouvez utiliser [WebListener](xref:fundamentals/servers/weblistener) pour prendre en charge les scénarios auto-hébergés sur Windows.</span><span class="sxs-lookup"><span data-stu-id="82b76-146">Although Kestrel doesn't support Windows authentication, you can use [WebListener](xref:fundamentals/servers/weblistener) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="82b76-147">L’exemple suivant configure l’hôte web de l’application pour utiliser WebListener avec l’authentification Windows :</span><span class="sxs-lookup"><span data-stu-id="82b76-147">The following example configures the app's web host to use WebListener with Windows authentication:</span></span>

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

## <a name="working-with-windows-authentication"></a><span data-ttu-id="82b76-148">Utilisation de l’authentification Windows</span><span class="sxs-lookup"><span data-stu-id="82b76-148">Working with Windows authentication</span></span>

<span data-ttu-id="82b76-149">Si votre application utilise l’authentification Windows et l’accès anonyme, vous pouvez utiliser la ``[Authorize]`` et ``[AllowAnonymous]`` attributs.</span><span class="sxs-lookup"><span data-stu-id="82b76-149">If your app uses Windows authentication and anonymous access, you can use the ``[Authorize]`` and ``[AllowAnonymous]`` attributes.</span></span> <span data-ttu-id="82b76-150">Les applications qui n’ont pas activé anonyme ne nécessitent ne pas ``[Authorize]``; l’application est considérée comme nécessitant une authentification, les demandes anonymes sont rejetées.</span><span class="sxs-lookup"><span data-stu-id="82b76-150">Apps that do not have anonymous enabled do not require ``[Authorize]``; the  app is treated as requiring authentication, anonymous requests are rejected.</span></span> <span data-ttu-id="82b76-151">Notez que si le site IIS est configuré **pas** pour autoriser l’accès anonyme, la ``[AllowAnonymous]`` attribut effectue **pas** autoriser les demandes anonymes.</span><span class="sxs-lookup"><span data-stu-id="82b76-151">Note, if the IIS site is configured **not** to allow anonymous access, the ``[AllowAnonymous]`` attribute does **not** allow anonymous requests.</span></span> <span data-ttu-id="82b76-152">Le ``[AllowAnonymous]`` les substitutions d’attributs ``[Authorize]`` attribut d’utilisation dans les applications qui autorisent l’accès anonyme.</span><span class="sxs-lookup"><span data-stu-id="82b76-152">The ``[AllowAnonymous]`` attribute overrides ``[Authorize]`` attribute usage within apps that allow anonymous access.</span></span>

### <a name="impersonation"></a><span data-ttu-id="82b76-153">Emprunt d'identité</span><span class="sxs-lookup"><span data-stu-id="82b76-153">Impersonation</span></span>

<span data-ttu-id="82b76-154">ASP.NET Core n’implémente pas l’emprunt d’identité.</span><span class="sxs-lookup"><span data-stu-id="82b76-154">ASP.NET Core does not implement impersonation.</span></span> <span data-ttu-id="82b76-155">Les applications s’exécutent avec l’identité de l’application pour toutes les demandes, à l’aide d’identité de processus ou pool d’application.</span><span class="sxs-lookup"><span data-stu-id="82b76-155">Apps run with the application identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="82b76-156">Si vous avez besoin effectuer explicitement une action de la part d’un utilisateur, utilisez ``WindowsIdentity.RunImpersonated``.</span><span class="sxs-lookup"><span data-stu-id="82b76-156">If you need to explicitly perform an action on behalf of a user, use ``WindowsIdentity.RunImpersonated``.</span></span> <span data-ttu-id="82b76-157">Exécuter une action unique dans ce contexte, puis fermez le contexte.</span><span class="sxs-lookup"><span data-stu-id="82b76-157">Run a single action in this context and then close the context.</span></span> <span data-ttu-id="82b76-158">Notez que ``RunImpersonated`` ne prend pas en charge asynchrone et ne doit pas être utilisé pour les scénarios complexes.</span><span class="sxs-lookup"><span data-stu-id="82b76-158">Note that ``RunImpersonated`` does not support async and should not be used for complex scenarios.</span></span> <span data-ttu-id="82b76-159">Par exemple, la habillage demandes entières ou des chaînes d’intergiciel (middleware) n'est pas pris en charge ou recommandé.</span><span class="sxs-lookup"><span data-stu-id="82b76-159">For example, wrapping entire requests or middleware chains is not supported or recommended.</span></span>

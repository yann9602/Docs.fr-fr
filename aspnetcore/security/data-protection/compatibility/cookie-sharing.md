---
title: Partage des cookies entre des applications
author: rick-anderson
description: 
keywords: Core,ASP.NET,cookies,Interop,cookie ASP.NET de partage
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 9a7aae45-8e21-4c54-950c-3c29df6c1082
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/compatibility/cookie-sharing
ms.openlocfilehash: dbf52b0a990a3627b8eded22db033c45d51ba6ad
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="sharing-cookies-between-applications"></a><span data-ttu-id="baa51-103">Partage des cookies entre des applications</span><span class="sxs-lookup"><span data-stu-id="baa51-103">Sharing cookies between applications</span></span>

<span data-ttu-id="baa51-104">Sites Web couramment se composent de nombreuses applications web individuelles, tout travail ensemble harmonieux.</span><span class="sxs-lookup"><span data-stu-id="baa51-104">Web sites commonly consist of many individual web applications, all working together harmoniously.</span></span> <span data-ttu-id="baa51-105">Si un développeur souhaite fournir une bonne expérience single-sign-on, il sont souvent nécessaire toutes les applications au sein du site web différent pour partager des tickets d’authentification entre eux.</span><span class="sxs-lookup"><span data-stu-id="baa51-105">If an application developer wants to provide a good single-sign-on experience, they'll often need all of the different web applications within the site to share authentication tickets between each other.</span></span>

<span data-ttu-id="baa51-106">Pour prendre en charge ce scénario, la pile de protection des données permet de partager une authentification de cookie Katana et tickets d’authentification par cookie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="baa51-106">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

## <a name="sharing-authentication-cookies-between-applications"></a><span data-ttu-id="baa51-107">Les cookies d’authentification entre les applications de partage</span><span class="sxs-lookup"><span data-stu-id="baa51-107">Sharing authentication cookies between applications</span></span>

<span data-ttu-id="baa51-108">Pour partager des cookies d’authentification entre deux applications ASP.NET Core différents, configurez chaque application qui doit partager les cookies comme suit.</span><span class="sxs-lookup"><span data-stu-id="baa51-108">To share authentication cookies between two different ASP.NET Core applications, configure each application that should share cookies as follows.</span></span>

<span data-ttu-id="baa51-109">Dans votre configuration méthode utilisent les CookieAuthenticationOptions pour configurer le service de protection des données pour les cookies et le AuthenticationScheme pour correspondre à ASP.NET 4.X.</span><span class="sxs-lookup"><span data-stu-id="baa51-109">In your configure method use the CookieAuthenticationOptions to set up the data protection service for cookies and the AuthenticationScheme to match ASP.NET 4.X.</span></span>

<span data-ttu-id="baa51-110">Si vous utilisez des identités :</span><span class="sxs-lookup"><span data-stu-id="baa51-110">If you're using identity:</span></span>

```csharp
app.AddIdentity<ApplicationUser, IdentityRole>(options =>
   {
       options.Cookies.ApplicationCookie.AuthenticationScheme = "ApplicationCookie";
       var protectionProvider = DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"));
       options.Cookies.ApplicationCookie.DataProtectionProvider = protectionProvider;
       options.Cookies.ApplicationCookie.TicketDataFormat = new TicketDataFormat(protectionProvider.CreateProtector("Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware", "Cookies", "v2"));
   });
   ```

<span data-ttu-id="baa51-111">Si vous utilisez des cookies directement :</span><span class="sxs-lookup"><span data-stu-id="baa51-111">If you're using cookies directly:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
   {
       DataProtectionProvider = DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"))
   });
   ```
   
<span data-ttu-id="baa51-112">Le `DataProtectionProvider` requiert le `Microsoft.AspNetCore.DataProtection.Extensions` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="baa51-112">The `DataProtectionProvider` requires the `Microsoft.AspNetCore.DataProtection.Extensions` NuGet package.</span></span>

<span data-ttu-id="baa51-113">Lorsqu’il est utilisé de cette manière, DirectoryInfo doit pointer vers un emplacement de stockage de clés aux cookies d’authentification.</span><span class="sxs-lookup"><span data-stu-id="baa51-113">When used in this manner, the DirectoryInfo should point to a key storage location specifically set aside for authentication cookies.</span></span> <span data-ttu-id="baa51-114">Le middleware du cookie d’authentification utilise l’implémentation fournie explicitement de DataProtectionProvider, ce qui est maintenant isolé à partir du système de protection des données utilisé par d’autres parties de l’application.</span><span class="sxs-lookup"><span data-stu-id="baa51-114">The cookie authentication middleware will use the explicitly provided implementation of the DataProtectionProvider, which is now isolated from the data protection system used by other parts of the application.</span></span> <span data-ttu-id="baa51-115">Le nom de l’application est ignoré (intentionnellement c’est le cas, étant donné que vous tentez d’obtenir plusieurs applications de partager les charges utiles).</span><span class="sxs-lookup"><span data-stu-id="baa51-115">The application name is ignored (intentionally so, since you're trying to get multiple applications to share payloads).</span></span>

>[!WARNING]
><span data-ttu-id="baa51-116">Vous devez envisager de configurer le DataProtectionProvider telles que les clés sont chiffrées au repos, comme dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="baa51-116">You should consider configuring the DataProtectionProvider such that keys are encrypted at rest, as in the below example.</span></span>
>
>
>  ```csharp
>  app.UseCookieAuthentication(new CookieAuthenticationOptions
>  {
>      DataProtectionProvider = DataProtectionProvider.Create(
>          new DirectoryInfo(@"c:\shared-auth-ticket-keys\"),
>          configure =>
>          {
>              configure.ProtectKeysWithCertificate("thumbprint");
>          })
>  });
>  ```

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-applications"></a><span data-ttu-id="baa51-117">Partage des cookies d’authentification entre ASP.NET 4.x et les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="baa51-117">Sharing authentication cookies between ASP.NET 4.x and ASP.NET Core applications</span></span>

<span data-ttu-id="baa51-118">4.x des applications ASP.NET qui utilisent l’intergiciel (middleware) d’authentification de cookie Katana peuvent être configurées pour générer des cookies d’authentification qui sont compatibles avec le middleware du cookie d’authentification ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="baa51-118">ASP.NET 4.x applications which use Katana cookie authentication middleware can be configured to generate authentication cookies which are compatible with the ASP.NET Core cookie authentication middleware.</span></span> <span data-ttu-id="baa51-119">Cela permet la mise à niveau des applications individuelles d’un site volumineux fragmentaire tout en offrant une authentification unique lisse sur l’expérience sur le site.</span><span class="sxs-lookup"><span data-stu-id="baa51-119">This allows upgrading a large site's individual applications piecemeal while still providing a smooth single sign on experience across the site.</span></span>

>[!TIP]
> <span data-ttu-id="baa51-120">Vous pouvez déterminer si votre application existante utilise intergiciel (middleware) d’authentification de cookie Katana par l’existence d’un appel à UseCookieAuthentication dans les Startup.Auth.cs de votre projet.</span><span class="sxs-lookup"><span data-stu-id="baa51-120">You can tell if your existing application uses Katana cookie authentication middleware by the existence of a call to UseCookieAuthentication in your project's Startup.Auth.cs.</span></span> <span data-ttu-id="baa51-121">Projets d’application web ASP.NET 4.x créées avec Visual Studio 2013 et utilisent ultérieurement l’intergiciel (middleware) d’authentification de cookie Katana par défaut.</span><span class="sxs-lookup"><span data-stu-id="baa51-121">ASP.NET 4.x web application projects created with Visual Studio 2013 and later use the Katana cookie authentication middleware by default.</span></span>

> [!NOTE]
> <span data-ttu-id="baa51-122">Votre application de 4.x ASP.NET doit cibler .NET Framework 4.5.1 ou version ultérieure, sinon les packages NuGet ne peut pas installer.</span><span class="sxs-lookup"><span data-stu-id="baa51-122">Your ASP.NET 4.x application must target .NET Framework 4.5.1 or higher, otherwise the necessary NuGet packages will fail to install.</span></span>

<span data-ttu-id="baa51-123">Pour partager des cookies d’authentification entre vos applications de 4.x ASP.NET et de vos applications ASP.NET Core, configurer l’application ASP.NET Core comme indiqué ci-dessus, puis configurer votre 4.x des applications ASP.NET en suivant les étapes ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="baa51-123">To share authentication cookies between your ASP.NET 4.x applications and your ASP.NET Core applications, configure the ASP.NET Core application as stated above, then configure your ASP.NET 4.x applications by following the steps below.</span></span>

1.  <span data-ttu-id="baa51-124">Installez le package Microsoft.Owin.Security.Interop dans chacune des applications ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="baa51-124">Install the package Microsoft.Owin.Security.Interop into each of your ASP.NET 4.x applications.</span></span>

2.   <span data-ttu-id="baa51-125">Dans Startup.Auth.cs, localisez l’appel à UseCookieAuthentication, qui est généralement l’aspect suivant.</span><span class="sxs-lookup"><span data-stu-id="baa51-125">In Startup.Auth.cs, locate the call to UseCookieAuthentication, which will generally look like the following.</span></span>

    ```csharp
    app.UseCookieAuthentication(new CookieAuthenticationOptions
    {
        // ...
    });
    ```
    
3.  <span data-ttu-id="baa51-126">Modifiez l’appel à UseCookieAuthentication comme suit, la modification de la CookieName pour correspondre au nom utilisé par l’intergiciel d’authentification de cookie ASP.NET Core, fournissant une instance d’un DataProtectionProvider qui a été initialisé avec un emplacement de stockage de clés, et valeur CookieManager interopérabilité ChunkingCookieManager le format de segmentation est compatible.</span><span class="sxs-lookup"><span data-stu-id="baa51-126">Modify the call to UseCookieAuthentication as follows, changing the CookieName to match the name used by the ASP.NET Core cookie authentication middleware, providing an instance of a DataProtectionProvider that has been initialized to a key storage location, and set CookieManager to interop ChunkingCookieManager so the chunking format is compatible.</span></span>

    ```csharp
    app.UseCookieAuthentication(new CookieAuthenticationOptions
       {
           AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
           CookieName = ".AspNetCore.Cookies",
           // CookieName = ".AspNetCore.ApplicationCookie", (if you're using identity)
           // CookiePath = "...", (if necessary)
           // ...
           TicketDataFormat = new AspNetTicketDataFormat(
               new DataProtectorShim(
                   DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"))
                   .CreateProtector("Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware",
                   "Cookies", "v2"))),
           CookieManager = new ChunkingCookieManager()
       });
       ```
    <span data-ttu-id="baa51-127">DirectoryInfo doit pointer vers le même emplacement de stockage que vous pointe de votre application ASP.NET Core et devez être configuré avec les mêmes paramètres.</span><span class="sxs-lookup"><span data-stu-id="baa51-127">The DirectoryInfo has to point to the same storage location that you pointed your ASP.NET Core application to and should be configured using the same settings.</span></span>

<span data-ttu-id="baa51-128">ASP.NET 4.x et les applications ASP.NET Core sont désormais configurées pour partager des cookies d’authentification.</span><span class="sxs-lookup"><span data-stu-id="baa51-128">The ASP.NET 4.x and ASP.NET Core applications are now configured to share authentication cookies.</span></span>

> [!NOTE]
> <span data-ttu-id="baa51-129">Vous devez vous assurer que le système d’identité pour chaque application pointe vers la même base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="baa51-129">You'll need to make sure that the identity system for each application is pointed at the same user database.</span></span> <span data-ttu-id="baa51-130">Sinon, le système d’identité génère les échecs lors de l’exécution lorsqu’il tente de faire correspondre les informations dans le cookie d’authentification contre les informations contenues dans sa base de données.</span><span class="sxs-lookup"><span data-stu-id="baa51-130">Otherwise the identity system will produce failures at runtime when it tries to match the information in the authentication cookie against the information in its database.</span></span>

---
title: Partage des cookies entre des applications
author: rick-anderson
description: "Ce document explique comment la pile de protection de données prend en charge le partage des cookies d’authentification entre ASP.NET 4.x et les applications ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/compatibility/cookie-sharing
ms.openlocfilehash: 0cbf5a3e9dfe8f99433800ac5c10ed36b4de6527
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="sharing-cookies-between-applications"></a>Partage des cookies entre des applications

Sites Web couramment se composent de nombreuses applications web individuelles, tout travail ensemble harmonieux. Si un développeur souhaite fournir une bonne expérience single-sign-on, il sont souvent nécessaire toutes les applications au sein du site web différent pour partager des tickets d’authentification entre eux.

Pour prendre en charge ce scénario, la pile de protection des données permet de partager une authentification de cookie Katana et tickets d’authentification par cookie ASP.NET Core.

## <a name="sharing-authentication-cookies-between-applications"></a>Les cookies d’authentification entre les applications de partage

Pour partager des cookies d’authentification entre deux applications ASP.NET Core différents, configurez chaque application qui doit partager les cookies comme suit.

Dans votre configuration de méthode, les CookieAuthenticationOptions permet de configurer le service de protection des données pour les cookies et le AuthenticationScheme pour correspondre à ASP.NET 4.x.

Si vous utilisez des identités :

```csharp
app.AddIdentity<ApplicationUser, IdentityRole>(options =>
{
    options.Cookies.ApplicationCookie.AuthenticationScheme = "ApplicationCookie";
    var protectionProvider = DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"));
    options.Cookies.ApplicationCookie.DataProtectionProvider = protectionProvider;
    options.Cookies.ApplicationCookie.TicketDataFormat = new TicketDataFormat(protectionProvider.CreateProtector("Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware", "Cookies", "v2"));
});
```

Si vous utilisez des cookies directement :

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"))
});
```
   
Le `DataProtectionProvider` requiert le `Microsoft.AspNetCore.DataProtection.Extensions` package NuGet.

Lorsqu’il est utilisé de cette manière, DirectoryInfo doit pointer vers un emplacement de stockage de clés aux cookies d’authentification. Le middleware du cookie d’authentification utilise l’implémentation fournie explicitement de DataProtectionProvider, ce qui est maintenant isolé à partir du système de protection des données utilisé par d’autres parties de l’application. Le nom de l’application est ignoré (intentionnellement c’est le cas, étant donné que vous tentez d’obtenir plusieurs applications de partager les charges utiles).

>[!WARNING]
>Vous devez envisager de configurer le DataProtectionProvider telles que les clés sont chiffrées au repos, comme dans l’exemple ci-dessous.
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

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-applications"></a>Partage des cookies d’authentification entre ASP.NET 4.x et les applications ASP.NET Core

4.x des applications ASP.NET qui utilisent l’intergiciel (middleware) d’authentification de cookie Katana peuvent être configurées pour générer des cookies d’authentification qui sont compatibles avec le middleware du cookie d’authentification ASP.NET Core. Cela permet la mise à niveau des applications individuelles d’un site volumineux fragmentaire tout en offrant une authentification unique lisse sur l’expérience sur le site.

>[!TIP]
> Vous pouvez déterminer si votre application existante utilise intergiciel (middleware) d’authentification de cookie Katana par l’existence d’un appel à UseCookieAuthentication dans les Startup.Auth.cs de votre projet. Projets d’application web ASP.NET 4.x créées avec Visual Studio 2013 et utilisent ultérieurement l’intergiciel (middleware) d’authentification de cookie Katana par défaut.

> [!NOTE]
> Votre application de 4.x ASP.NET doit cibler .NET Framework 4.5.1 ou version ultérieure, sinon les packages NuGet ne peut pas installer.

Pour partager des cookies d’authentification entre vos applications de 4.x ASP.NET et de vos applications ASP.NET Core, configurer l’application ASP.NET Core comme indiqué ci-dessus, puis configurer votre 4.x des applications ASP.NET en suivant les étapes ci-dessous.

1.  Installez le package Microsoft.Owin.Security.Interop dans chacune des applications ASP.NET 4.x.

2.   Dans Startup.Auth.cs, localisez l’appel à UseCookieAuthentication, qui est généralement l’aspect suivant.

    ```csharp
    app.UseCookieAuthentication(new CookieAuthenticationOptions
    {
        // ...
    });
    ```
    
3.  Modifiez l’appel à UseCookieAuthentication comme suit, la modification de la CookieName pour correspondre au nom utilisé par l’intergiciel d’authentification de cookie ASP.NET Core, fournissant une instance d’un DataProtectionProvider qui a été initialisé avec un emplacement de stockage de clés, et valeur CookieManager interopérabilité ChunkingCookieManager le format de segmentation est compatible.

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
    DirectoryInfo doit pointer vers le même emplacement de stockage que vous pointe de votre application ASP.NET Core et devez être configuré avec les mêmes paramètres.

ASP.NET 4.x et les applications ASP.NET Core sont désormais configurées pour partager des cookies d’authentification.

> [!NOTE]
> Vous devez vous assurer que le système d’identité pour chaque application pointe vers la même base de données utilisateur. Sinon, le système d’identité génère les échecs lors de l’exécution lorsqu’il tente de faire correspondre les informations dans le cookie d’authentification contre les informations contenues dans sa base de données.

---
title: "À l’aide de l’authentification de Cookie sans ASP.NET Core identité"
author: rick-anderson
description: "Une explication de l’utilisation de l’authentification de cookie sans ASP.NET Core Identity"
keywords: ASP.NET Core, les cookies
ms.author: riande
manager: wpickett
ms.date: 08/14/2017
ms.topic: article
ms.assetid: 2bdcbf95-8d9d-4537-a4a0-a5ee439dcb62
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/cookie
ms.openlocfilehash: e5c53a7044edb56e065b2dc1536343fdaf9fb007
ms.sourcegitcommit: 7d8f4e3443a2989a64343f8fec83e6a4c4ed2f97
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2017
---
# <a name="using-cookie-authentication-without-aspnet-core-identity"></a>À l’aide de l’authentification de Cookie sans ASP.NET Core identité

<a name="security-authentication-cookie-middleware"></a>

ASP.NET Core 1.x fournit le cookie [intergiciel (middleware)](../../fundamentals/middleware.md#fundamentals-middleware) qui sérialise un principal d’utilisateur dans un cookie chiffré et puis, sur les demandes suivantes, valide le cookie, recrée le principal et l’affecte à la `HttpContext.User` propriété . Si vous souhaitez fournir vos propres écrans d’ouverture de session et les bases de données utilisateur, vous pouvez utiliser le middleware du cookie comme une fonctionnalité autonome.

Un changement majeur dans ASP.NET Core 2.x est que le middleware du cookie est absent. Au lieu de cela, le `UseAuthentication` invocation de la méthode dans le `Configure` méthode de *Startup.cs* ajoute le AuthenticationMiddleware qui définit le `HttpContext.User` propriété.

<a name="security-authentication-cookie-middleware-configuring"></a>

## <a name="adding-and-configuring"></a>Ajout et configuration

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Procédez comme suit :

- Si vous N'utilisez pas le `Microsoft.AspNetCore.All` [metapackage](xref:fundamentals/metapackage), installer la version 2.0 + de le `Microsoft.AspNetCore.Authentication.Cookies` package NuGet dans votre projet.

- Appeler le `UseAuthentication` méthode dans le `Configure` méthode de la *Startup.cs* fichier :

    ```csharp
    app.UseAuthentication();
    ```

- Appeler le `AddAuthentication` et `AddCookie` méthodes dans le `ConfigureServices` méthode de la *Startup.cs* fichier :

    ```csharp
    services.AddAuthentication("MyCookieAuthenticationScheme")
            .AddCookie("MyCookieAuthenticationScheme", options => {
                options.AccessDeniedPath = "/Account/Forbidden/";
                options.LoginPath = "/Account/Unauthorized/";
            });
    ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Procédez comme suit :

- Installer le `Microsoft.AspNetCore.Authentication.Cookies` package NuGet dans votre projet. Ce package contient le middleware du cookie.

- Ajoutez les lignes suivantes à la `Configure` méthode dans votre *Startup.cs* fichier avant la `app.UseMvc()` instruction :

    ```csharp
    app.UseCookieAuthentication(new CookieAuthenticationOptions()
    {
        AccessDeniedPath = "/Account/Forbidden/",
        AuthenticationScheme = "MyCookieAuthenticationScheme",
        AutomaticAuthenticate = true,
        AutomaticChallenge = true,
        LoginPath = "/Account/Unauthorized/"
    });
    ```

---

Les extraits de code ci-dessus configurer tout ou partie des options suivantes :

* `AccessDeniedPath`-Il s’agit du chemin d’accès relatif vers laquelle les demandes de rediriger lorsqu’un utilisateur tente d’accéder à une ressource, mais ne remplit pas les [stratégies d’autorisation](xref:security/authorization/policies#security-authorization-policies-based) pour cette ressource.

* `AuthenticationScheme`-Il s’agit d’une valeur selon laquelle un schéma d’authentification de cookie particulier est connu. Cela est utile quand il existe plusieurs instances de l’authentification de cookie et vous souhaitez [limiter l’autorisation à une instance](xref:security/authorization/limitingidentitybyscheme#security-authorization-limiting-by-scheme).

* `AutomaticAuthenticate`-Cet indicateur s’applique uniquement aux ASP.NET Core 1.x. Il indique que l’authentification de cookie doit s’exécuter sur chaque demande et tente de valider et de reconstruire toute entité sérialisée de que sa création.

* `AutomaticChallenge`-Cet indicateur s’applique uniquement aux ASP.NET Core 1.x. Il indique que l’authentification de cookie 1.x doit rediriger le navigateur pour le `LoginPath` ou `AccessDeniedPath` lorsque l’autorisation échoue.

* `LoginPath`-Il s’agit du chemin d’accès relatif vers laquelle les demandes de rediriger lorsqu’un utilisateur tente d’accéder à une ressource, mais n’a pas été authentifié.

[Autres options](xref:security/authentication/cookie#security-authentication-cookie-options) incluent la possibilité de définir l’émetteur pour toutes les revendications de l’authentification de cookie crée, supprime du nom du cookie de l’authentification, le domaine pour le cookie et diverses propriétés de sécurité sur le cookie. Par défaut, l’authentification de cookie utilise les options de sécurité appropriées pour tous les cookies crée, telle que :
- Définition de l’indicateur HttpOnly pour empêcher l’accès de cookie dans JavaScript côté client
- Limiter le cookie à HTTPS si une demande de déplacement sur HTTPS

<a name="security-authentication-cookie-middleware-creating-a-cookie"></a>

## <a name="creating-an-identity-cookie"></a>Création d’un cookie d’identité

Pour créer un cookie contenant les informations de votre utilisateur, vous devez construire une [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) contenant les informations que vous souhaitez être sérialisé dans le cookie. Une fois que vous avez adéquat `ClaimsPrincipal` d’objet, appelez ce qui suit à l’intérieur de votre méthode de contrôleur :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync("MyCookieAuthenticationScheme", principal);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync("MyCookieAuthenticationScheme", principal);
```

---

Cela crée un cookie chiffré et l’ajoute à la réponse actuelle. Le `AuthenticationScheme` spécifié au cours de [configuration](xref:security/authentication/cookie#security-authentication-cookie-middleware-configuring) doit être utilisé lors de l’appel `SignInAsync`.

En arrière-plan, le chiffrement utilisé est de ASP.NET Core [Protection des données](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) système. Si vous hébergez sur plusieurs ordinateurs, équilibrage de charge ou à l’aide d’une batterie de serveurs web, de charge, vous devez [configurer la protection des données](xref:security/data-protection/configuration/overview#data-protection-configuring) à utiliser le même anneau de clé et l’identificateur de l’application.

## <a name="signing-out"></a>Déconnecter

Pour déconnecter l’utilisateur actuel et supprimer les cookies, appelez ce qui suit à l’intérieur de votre contrôleur :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignOutAsync("MyCookieAuthenticationScheme");
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignOutAsync("MyCookieAuthenticationScheme");
```

---

## <a name="reacting-to-back-end-changes"></a>Réagir aux modifications du serveur principal

>[!WARNING]
> Une fois qu’un cookie principal a été créé, il devient la seule source d’identité. Même si vous désactivez un utilisateur dans vos systèmes back-end, l’authentification de cookie n’a aucune connaissance de ce, et un utilisateur reste connecté en tant que cookie est valide.

L’authentification de cookie fournit une série d’événements dans sa classe de l’option. Le `ValidateAsync()` événement peut être utilisé pour intercepter et de remplacer la validation de l’identité de cookie.

Envisagez d’une base de données utilisateur du principal qui peut-être avoir une colonne « LastChanged ». Pour invalider un cookie lorsque la base de données change, vous devez d’abord, lorsque [créer le cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie), ajouter une revendication « LastChanged » qui contient la valeur actuelle. Lorsque la base de données change, la valeur « LastChanged » doit être mis à jour.

Pour implémenter un remplacement pour le `ValidateAsync()` événement, vous devez écrire une méthode avec la signature suivante :

```csharp
Task ValidateAsync(CookieValidatePrincipalContext context);
```

ASP.NET Core Identity implémente cette vérification dans le cadre de son `SecurityStampValidator`. Un exemple ressemble à ceci :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = context.HttpContext.RequestServices.GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        string lastChanged;
        lastChanged = (from c in userPrincipal.Claims
                        where c.Type == "LastUpdated"
                        select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(userPrincipal, lastChanged))
        {
            context.RejectPrincipal();
            await context.HttpContext.SignOutAsync("MyCookieAuthenticationScheme");
        }
    }
}
```

Cela serait puis être rattaché au cours de l’inscription du service de cookie dans le `ConfigureServices` méthode *Startup.cs*:

```csharp
services.AddAuthentication("MyCookieAuthenticationScheme")
        .AddCookie("MyCookieAuthenticationScheme", options =>
        {
            options.Events = new CookieAuthenticationEvents
            {
                OnValidatePrincipal = LastChangedValidator.ValidateAsync
            };
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = context.HttpContext.RequestServices.GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        string lastChanged;
        lastChanged = (from c in userPrincipal.Claims
                        where c.Type == "LastUpdated"
                        select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(userPrincipal, lastChanged))
        {
            context.RejectPrincipal();
            await context.HttpContext.Authentication.SignOutAsync("MyCookieAuthenticationScheme");
        }
    }
}
```

Cela serait ensuite être associé lors de la configuration de l’authentification de cookie dans le `Configure` méthode *Startup.cs*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    Events = new CookieAuthenticationEvents
    {
        OnValidatePrincipal = LastChangedValidator.ValidateAsync
    }
});
```

---

Prenons l’exemple dans lequel leur nom a été mis à jour &mdash; une décision qui n’affecte pas la sécurité de toute façon. Si vous souhaitez mettre à jour de façon non destructrice le principal de l’utilisateur, vous pouvez appeler `context.ReplacePrincipal()` et définir le `context.ShouldRenew` propriété `true`.

<a name="security-authentication-cookie-options"></a>

## <a name="controlling-cookie-options"></a>Contrôler les options du cookie

Le [CookieAuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) classe est fourni avec différentes options de configuration pour ajuster les cookies en cours de création.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

ASP.NET Core 2.x unifie les API utilisées pour configurer les cookies. Le 1.x API ont été marqués comme obsolètes et un nouveau `Cookie` propriété de type `CookieBuilder` a été introduit dans le `CookieAuthenticationOptions` classe. Il est recommandé que vous migrez vers l’API 2.x.

* `ClaimsIssuer`est de l’émetteur à utiliser pour le [émetteur](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) propriété sur toutes les revendications créés par l’authentification de cookie.

* `CookieBuilder.Domain`est le nom de domaine à laquelle le cookie est pris en charge. Par défaut, il est le nom d’hôte que la demande a été envoyée à. Le navigateur ne sert que le cookie à un nom d’hôte correspondant. Vous pouvez souhaiter ajuster ici pour que les cookies disponibles pour tous les hôtes de votre domaine. Par exemple, si le domaine du cookie à `.contoso.com` met à disposition `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`, etc.

* `CookieBuilder.HttpOnly`est un indicateur qui indique si le cookie doit être accessible uniquement aux serveurs. Par défaut est `true`. Modification de cette valeur peut s’ouvrir à votre application au vol de cookies de votre application doit avoir un un bogue de scripts entre sites.

* `CookieBuilder.Path`peut être utilisé pour isoler les applications en cours d’exécution sur le même nom d’hôte. Si vous avez une application en cours d’exécution `/app1` et souhaitez limiter les cookies émis juste à envoyer à cette application, vous devez définir le `CookiePath` propriété `/app1`. En procédant ainsi, le cookie est uniquement disponible pour les demandes vers `/app1` ou tout ce qu’il contient.

* `CookieBuilder.SameSite`Indique si le navigateur doit autoriser le cookie à attacher aux demandes d’un même site ou entre sites. Par défaut est `SameSiteMode.Lax`.

* `CookieBuilder.SecurePolicy`est un indicateur qui indique si le cookie créé doit être limité pour HTTPS, HTTP ou HTTPS ou le même protocole que la demande. Par défaut est `SameAsRequest`.

* `ExpireTimeSpan`est le `TimeSpan` issue de laquelle le cookie expire. Il est ajouté à la date et heure actuelles pour créer la date d’expiration du cookie.

* `SlidingExpiration`indicateur spécifiant si la date d’expiration du cookie réinitialise lorsque plus de la moitié de la `ExpireTimeSpan` est écoulé. La date d’expiration est déplacée vers la date actuelle plus la `ExpireTimespan`. Un [délai d’expiration absolue](xref:security/authentication/cookie#security-authentication-absolute-expiry) peut être définie à l’aide de la `AuthenticationProperties` lors de l’appel de la classe `SignInAsync`. Un délai d’expiration absolue peut améliorer la sécurité de votre application en limitant la quantité de la durée pendant laquelle le cookie d’authentification est valid.

Un exemple d’utilisation `CookieAuthenticationOptions` dans les `ConfigureServices` méthode *Startup.cs* suit :

```csharp
services.AddAuthentication("MyCookieAuthenticationScheme")
        .AddCookie("MyCookieAuthenticationScheme", options =>
        {
            options.Cookie.Name = "AuthCookie";
            options.Cookie.Domain = "contoso.com";
            options.Cookie.Path = "/";
            options.Cookie.HttpOnly = true;
            options.Cookie.SameSite = SameSiteMode.Lax;
            options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* `ClaimsIssuer`est de l’émetteur à utiliser pour le [émetteur](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) propriété sur toutes les revendications créés par l’intergiciel (middleware).

* `CookieDomain`est le nom de domaine à laquelle le cookie est pris en charge. Par défaut, il est le nom d’hôte que la demande a été envoyée à. Le navigateur ne sert que le cookie à un nom d’hôte correspondant. Vous pouvez souhaiter ajuster ici pour que les cookies disponibles pour tous les hôtes de votre domaine. Par exemple, si le domaine du cookie à `.contoso.com` met à disposition `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`, etc.

* `CookieHttpOnly`est un indicateur qui indique si le cookie doit être accessible uniquement aux serveurs. Par défaut est `true`. Modification de cette valeur peut s’ouvrir à votre application au vol de cookies de votre application doit avoir un un bogue de scripts entre sites.

* `CookiePath`peut être utilisé pour isoler les applications en cours d’exécution sur le même nom d’hôte. Si vous avez une application en cours d’exécution `/app1` et souhaitez limiter les cookies émis juste à envoyer à cette application, vous devez définir le `CookiePath` propriété `/app1`. En procédant ainsi, le cookie est uniquement disponible pour les demandes vers `/app1` ou tout ce qu’il contient.

* `CookieSecure`est un indicateur qui indique si le cookie créé doit être limité pour HTTPS, HTTP ou HTTPS ou le même protocole que la demande. Par défaut est `SameAsRequest`.

* `ExpireTimeSpan`est le `TimeSpan` issue de laquelle le cookie expire. Il est ajouté à la date et heure actuelles pour créer la date d’expiration du cookie.

* `SlidingExpiration`indicateur spécifiant si la date d’expiration du cookie réinitialise lorsque plus de la moitié de la `ExpireTimeSpan` est écoulé. La date d’expiration est déplacée vers la date actuelle plus la `ExpireTimespan`. Un [délai d’expiration absolue](xref:security/authentication/cookie#security-authentication-absolute-expiry) peut être définie à l’aide de la `AuthenticationProperties` lors de l’appel de la classe `SignInAsync`. Un délai d’expiration absolue peut améliorer la sécurité de votre application en limitant la quantité de la durée pendant laquelle le cookie d’authentification est valid.

Un exemple d’utilisation `CookieAuthenticationOptions` dans les `Configure` méthode *Startup.cs* suit :

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    CookieName = "AuthCookie",
    CookieDomain = "contoso.com",
    CookiePath = "/",
    CookieHttpOnly = true,
    CookieSecure = CookieSecurePolicy.Always
});
```

---

## <a name="persistent-cookies-and-absolute-expiry-times"></a>Les cookies persistants et les délais d’expiration absolue

Vous souhaiterez peut-être l’expiration du cookie à demeurent entre les sessions de navigateur que vous souhaitez un délai d’expiration absolue à l’identité et le cookie transportant. Cette persistance doit être activée avec le consentement explicite de l’utilisateur, via une case à cocher « Mémoriser mes informations » sur la connexion ou un mécanisme similaire. Vous pouvez effectuer les opérations suivantes à l’aide de la `AuthenticationProperties` paramètre sur le `SignInAsync` méthode appelée lorsque [d’une identité de signature et la création de cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie). Exemple :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

Le `AuthenticationProperties` (classe), utilisé dans l’extrait de code précédent, se trouve dans le `Microsoft.AspNetCore.Authentication` espace de noms.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

Le `AuthenticationProperties` (classe), utilisé dans l’extrait de code précédent, se trouve dans le `Microsoft.AspNetCore.Http.Authentication` espace de noms.

---

L’extrait de code précédent crée une identité et le cookie correspondant qui survit par le biais des fermetures de navigateur. Tous les paramètres d’expiration décalée configurés précédemment via [options cookie](xref:security/authentication/cookie#security-authentication-cookie-options) sont toujours respectées. Si le cookie expire alors que le navigateur est fermé, le navigateur efface une fois qu’il est redémarré.

<a name="security-authentication-absolute-expiry"></a>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

---

L’extrait de code précédent crée une identité et le cookie correspondant qui dure 20 minutes. Il ignore tous les paramètres d’expiration décalée configurés précédemment via [options cookie](xref:security/authentication/cookie#security-authentication-cookie-options).

Le `ExpiresUtc` et `IsPersistent` propriétés s’excluent mutuellement.

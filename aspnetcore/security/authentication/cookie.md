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
ms.openlocfilehash: 60ac318cb47b5a5b4c651c88e60d43772ce59958
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2017
---
# <a name="using-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="f8f7a-104">À l’aide de l’authentification de Cookie sans ASP.NET Core identité</span><span class="sxs-lookup"><span data-stu-id="f8f7a-104">Using Cookie Authentication without ASP.NET Core Identity</span></span>

<a name="security-authentication-cookie-middleware"></a>

<span data-ttu-id="f8f7a-105">ASP.NET Core 1.x fournit le cookie [intergiciel (middleware)](../../fundamentals/middleware.md#fundamentals-middleware) qui sérialise un principal d’utilisateur dans un cookie chiffré et puis, sur les demandes suivantes, valide le cookie, recrée le principal et l’affecte à la `HttpContext.User` propriété .</span><span class="sxs-lookup"><span data-stu-id="f8f7a-105">ASP.NET Core 1.x provides cookie [middleware](../../fundamentals/middleware.md#fundamentals-middleware) which serializes a user principal into an encrypted cookie and then, on subsequent requests, validates the cookie, recreates the principal, and assigns it to the `HttpContext.User` property.</span></span> <span data-ttu-id="f8f7a-106">Si vous souhaitez fournir vos propres écrans d’ouverture de session et les bases de données utilisateur, vous pouvez utiliser le middleware du cookie comme une fonctionnalité autonome.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-106">If you want to provide your own login screens and user databases, you can use the cookie middleware as a standalone feature.</span></span>

<span data-ttu-id="f8f7a-107">Un changement majeur dans ASP.NET Core 2.x est que le middleware du cookie est absent.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-107">A major change in ASP.NET Core 2.x is that the cookie middleware is absent.</span></span> <span data-ttu-id="f8f7a-108">Au lieu de cela, le `UseAuthentication` invocation de la méthode dans le `Configure` méthode de *Startup.cs* ajoute le AuthenticationMiddleware qui définit le `HttpContext.User` propriété.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-108">Instead, the `UseAuthentication` method invocation in the `Configure` method of *Startup.cs* adds the AuthenticationMiddleware which sets the `HttpContext.User` property.</span></span>

<a name="security-authentication-cookie-middleware-configuring"></a>

## <a name="adding-and-configuring"></a><span data-ttu-id="f8f7a-109">Ajout et configuration</span><span class="sxs-lookup"><span data-stu-id="f8f7a-109">Adding and configuring</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f8f7a-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="f8f7a-111">Procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-111">Complete the following steps:</span></span>

- <span data-ttu-id="f8f7a-112">Si vous N'utilisez pas le `Microsoft.AspNetCore.All` [metapackage](xref:fundamentals/metapackage), installer la version 2.0 + de le `Microsoft.AspNetCore.Authentication.Cookies` package NuGet dans votre projet.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-112">If not using the `Microsoft.AspNetCore.All` [metapackage](xref:fundamentals/metapackage), install version 2.0+ of the `Microsoft.AspNetCore.Authentication.Cookies` NuGet package in your project.</span></span>

- <span data-ttu-id="f8f7a-113">Appeler le `UseAuthentication` méthode dans le `Configure` méthode de la *Startup.cs* fichier :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-113">Invoke the `UseAuthentication` method in the `Configure` method of the *Startup.cs* file:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f8f7a-114">Appeler le `AddAuthentication` et `AddCookie` méthodes dans le `ConfigureServices` méthode de la *Startup.cs* fichier :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-114">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method of the *Startup.cs* file:</span></span>

    ```csharp
    services.AddAuthentication("MyCookieAuthenticationScheme")
            .AddCookie(options => {
                options.AccessDeniedPath = "/Account/Forbidden/";
                options.LoginPath = "/Account/Unauthorized/";
            });
    ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f8f7a-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="f8f7a-116">Procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-116">Complete the following steps:</span></span>

- <span data-ttu-id="f8f7a-117">Installer le `Microsoft.AspNetCore.Authentication.Cookies` package NuGet dans votre projet.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-117">Install the `Microsoft.AspNetCore.Authentication.Cookies` NuGet package in your project.</span></span> <span data-ttu-id="f8f7a-118">Ce package contient le middleware du cookie.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-118">This package contains the cookie middleware.</span></span>

- <span data-ttu-id="f8f7a-119">Ajoutez les lignes suivantes à la `Configure` méthode dans votre *Startup.cs* fichier avant la `app.UseMvc()` instruction :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-119">Add the following lines to the `Configure` method in your *Startup.cs* file before the `app.UseMvc()` statement:</span></span>

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

<span data-ttu-id="f8f7a-120">Les extraits de code ci-dessus configurer tout ou partie des options suivantes :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-120">The code snippets above configure some or all of the following options:</span></span>

* <span data-ttu-id="f8f7a-121">`AccessDeniedPath`-Il s’agit du chemin d’accès relatif vers laquelle les demandes de rediriger lorsqu’un utilisateur tente d’accéder à une ressource, mais ne remplit pas les [stratégies d’autorisation](xref:security/authorization/policies#security-authorization-policies-based) pour cette ressource.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-121">`AccessDeniedPath` - This is the relative path to which requests redirect when a user attempts to access a resource but does not pass any [authorization policies](xref:security/authorization/policies#security-authorization-policies-based) for that resource.</span></span>

* <span data-ttu-id="f8f7a-122">`AuthenticationScheme`-Il s’agit d’une valeur selon laquelle un schéma d’authentification de cookie particulier est connu.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-122">`AuthenticationScheme` - This is a value by which a particular cookie authentication scheme is known.</span></span> <span data-ttu-id="f8f7a-123">Cela est utile quand il existe plusieurs instances de l’authentification de cookie et vous souhaitez [limiter l’autorisation à une instance](xref:security/authorization/limitingidentitybyscheme#security-authorization-limiting-by-scheme).</span><span class="sxs-lookup"><span data-stu-id="f8f7a-123">This is useful when there are multiple instances of cookie authentication and you want to [limit authorization to one instance](xref:security/authorization/limitingidentitybyscheme#security-authorization-limiting-by-scheme).</span></span>

* <span data-ttu-id="f8f7a-124">`AutomaticAuthenticate`-Cet indicateur s’applique uniquement aux ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-124">`AutomaticAuthenticate` - This flag is relevant only for ASP.NET Core 1.x.</span></span> <span data-ttu-id="f8f7a-125">Il indique que l’authentification de cookie doit s’exécuter sur chaque demande et tente de valider et de reconstruire toute entité sérialisée de que sa création.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-125">It indicates that the cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span>

* <span data-ttu-id="f8f7a-126">`AutomaticChallenge`-Cet indicateur s’applique uniquement aux ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-126">`AutomaticChallenge` - This flag is relevant only for ASP.NET Core 1.x.</span></span> <span data-ttu-id="f8f7a-127">Il indique que l’authentification de cookie 1.x doit rediriger le navigateur pour le `LoginPath` ou `AccessDeniedPath` lorsque l’autorisation échoue.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-127">It indicates that the 1.x cookie authentication should redirect the browser to the `LoginPath` or `AccessDeniedPath` when authorization fails.</span></span>

* <span data-ttu-id="f8f7a-128">`LoginPath`-Il s’agit du chemin d’accès relatif vers laquelle les demandes de rediriger lorsqu’un utilisateur tente d’accéder à une ressource, mais n’a pas été authentifié.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-128">`LoginPath` - This is the relative path to which requests redirect when a user attempts to access a resource but has not been authenticated.</span></span>

<span data-ttu-id="f8f7a-129">[Autres options](xref:security/authentication/cookie#security-authentication-cookie-options) incluent la possibilité de définir l’émetteur pour toutes les revendications de l’authentification de cookie crée, supprime du nom du cookie de l’authentification, le domaine pour le cookie et diverses propriétés de sécurité sur le cookie.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-129">[Other options](xref:security/authentication/cookie#security-authentication-cookie-options) include the ability to set the issuer for any claims the cookie authentication creates, the name of the cookie the authentication drops, the domain for the cookie and various security properties on the cookie.</span></span> <span data-ttu-id="f8f7a-130">Par défaut, l’authentification de cookie utilise les options de sécurité appropriées pour tous les cookies crée, telle que :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-130">By default, the cookie authentication uses appropriate security options for any cookies it creates, such as:</span></span>
- <span data-ttu-id="f8f7a-131">Définition de l’indicateur HttpOnly pour empêcher l’accès de cookie dans JavaScript côté client</span><span class="sxs-lookup"><span data-stu-id="f8f7a-131">Setting the HttpOnly flag to prevent cookie access in client-side JavaScript</span></span>
- <span data-ttu-id="f8f7a-132">Limiter le cookie à HTTPS si une demande de déplacement sur HTTPS</span><span class="sxs-lookup"><span data-stu-id="f8f7a-132">Limiting the cookie to HTTPS if a request has traveled over HTTPS</span></span>

<a name="security-authentication-cookie-middleware-creating-a-cookie"></a>

## <a name="creating-an-identity-cookie"></a><span data-ttu-id="f8f7a-133">Création d’un cookie d’identité</span><span class="sxs-lookup"><span data-stu-id="f8f7a-133">Creating an Identity cookie</span></span>

<span data-ttu-id="f8f7a-134">Pour créer un cookie contenant les informations de votre utilisateur, vous devez construire une [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) contenant les informations que vous souhaitez être sérialisé dans le cookie.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-134">To create a cookie holding your user information, you must construct a [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) holding the information you wish to be serialized in the cookie.</span></span> <span data-ttu-id="f8f7a-135">Une fois que vous avez adéquat `ClaimsPrincipal` d’objet, appelez ce qui suit à l’intérieur de votre méthode de contrôleur :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-135">Once you have a suitable `ClaimsPrincipal` object, call the following inside your controller method:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f8f7a-136">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-136">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync("MyCookieAuthenticationScheme", principal);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f8f7a-137">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync("MyCookieAuthenticationScheme", principal);
```

---

<span data-ttu-id="f8f7a-138">Cela crée un cookie chiffré et l’ajoute à la réponse actuelle.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-138">This creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="f8f7a-139">Le `AuthenticationScheme` spécifié au cours de [configuration](xref:security/authentication/cookie#security-authentication-cookie-middleware-configuring) doit être utilisé lors de l’appel `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-139">The `AuthenticationScheme` specified during [configuration](xref:security/authentication/cookie#security-authentication-cookie-middleware-configuring) must be used when calling `SignInAsync`.</span></span>

<span data-ttu-id="f8f7a-140">En arrière-plan, le chiffrement utilisé est de ASP.NET Core [Protection des données](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) système.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-140">Under the covers, the encryption used is ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) system.</span></span> <span data-ttu-id="f8f7a-141">Si vous hébergez sur plusieurs ordinateurs, équilibrage de charge ou à l’aide d’une batterie de serveurs web, de charge, vous devez [configurer la protection des données](xref:security/data-protection/configuration/overview#data-protection-configuring) à utiliser le même anneau de clé et l’identificateur de l’application.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-141">If you are hosting on multiple machines, load balancing, or using a web farm, then you need to [configure data protection](xref:security/data-protection/configuration/overview#data-protection-configuring) to use the same key ring and application identifier.</span></span>

## <a name="signing-out"></a><span data-ttu-id="f8f7a-142">Déconnecter</span><span class="sxs-lookup"><span data-stu-id="f8f7a-142">Signing out</span></span>

<span data-ttu-id="f8f7a-143">Pour déconnecter l’utilisateur actuel et supprimer les cookies, appelez ce qui suit à l’intérieur de votre contrôleur :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-143">To sign out the current user and delete their cookie, call the following inside your controller:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f8f7a-144">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-144">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignOutAsync("MyCookieAuthenticationScheme");
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f8f7a-145">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-145">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignOutAsync("MyCookieAuthenticationScheme");
```

---

## <a name="reacting-to-back-end-changes"></a><span data-ttu-id="f8f7a-146">Réagir aux modifications du serveur principal</span><span class="sxs-lookup"><span data-stu-id="f8f7a-146">Reacting to back-end changes</span></span>

>[!WARNING]
> <span data-ttu-id="f8f7a-147">Une fois qu’un cookie principal a été créé, il devient la seule source d’identité.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-147">Once a principal cookie has been created, it becomes the single source of identity.</span></span> <span data-ttu-id="f8f7a-148">Même si vous désactivez un utilisateur dans vos systèmes back-end, l’authentification de cookie n’a aucune connaissance de ce, et un utilisateur reste connecté en tant que cookie est valide.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-148">Even if you disable a user in your back-end systems, the cookie authentication has no knowledge of this, and a user stays logged in as long as their cookie is valid.</span></span>

<span data-ttu-id="f8f7a-149">L’authentification de cookie fournit une série d’événements dans sa classe de l’option.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-149">The cookie authentication provides a series of events in its option class.</span></span> <span data-ttu-id="f8f7a-150">Le `ValidateAsync()` événement peut être utilisé pour intercepter et de remplacer la validation de l’identité de cookie.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-150">The `ValidateAsync()` event can be used to intercept and override validation of the cookie identity.</span></span>

<span data-ttu-id="f8f7a-151">Envisagez d’une base de données utilisateur du principal qui peut-être avoir une colonne « LastChanged ».</span><span class="sxs-lookup"><span data-stu-id="f8f7a-151">Consider a back-end user database that may have a "LastChanged" column.</span></span> <span data-ttu-id="f8f7a-152">Pour invalider un cookie lorsque la base de données change, vous devez d’abord, lorsque [créer le cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie), ajouter une revendication « LastChanged » qui contient la valeur actuelle.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-152">In order to invalidate a cookie when the database changes, you should first, when [creating the cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie), add a "LastChanged" claim containing the current value.</span></span> <span data-ttu-id="f8f7a-153">Lorsque la base de données change, la valeur « LastChanged » doit être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-153">When the database changes, the "LastChanged" value should be updated.</span></span>

<span data-ttu-id="f8f7a-154">Pour implémenter un remplacement pour le `ValidateAsync()` événement, vous devez écrire une méthode avec la signature suivante :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-154">To implement an override for the `ValidateAsync()` event, you must write a method with the following signature:</span></span>

```csharp
Task ValidateAsync(CookieValidatePrincipalContext context);
```

<span data-ttu-id="f8f7a-155">ASP.NET Core Identity implémente cette vérification dans le cadre de son `SecurityStampValidator`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-155">ASP.NET Core Identity implements this check as part of its `SecurityStampValidator`.</span></span> <span data-ttu-id="f8f7a-156">Un exemple ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-156">An example looks like the following:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f8f7a-157">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-157">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

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

<span data-ttu-id="f8f7a-158">Cela serait puis être rattaché au cours de l’inscription du service de cookie dans le `ConfigureServices` méthode *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8f7a-158">This would then be wired up during cookie service registration in the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication("MyCookieAuthenticationScheme")
        .AddCookie(options =>
        {
            options.Events = new CookieAuthenticationEvents
            {
                OnValidatePrincipal = LastChangedValidator.ValidateAsync
            };
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f8f7a-159">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-159">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

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

<span data-ttu-id="f8f7a-160">Cela serait ensuite être associé lors de la configuration de l’authentification de cookie dans le `Configure` méthode *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8f7a-160">This would then be wired up during cookie authentication configuration in the `Configure` method of *Startup.cs*:</span></span>

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

<span data-ttu-id="f8f7a-161">Prenons l’exemple dans lequel leur nom a été mis à jour &mdash; une décision qui n’affecte pas la sécurité de toute façon.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-161">Consider the example in which their name has been updated &mdash; a decision which doesn't affect security in any way.</span></span> <span data-ttu-id="f8f7a-162">Si vous souhaitez mettre à jour de façon non destructrice le principal de l’utilisateur, vous pouvez appeler `context.ReplacePrincipal()` et définir le `context.ShouldRenew` propriété `true`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-162">If you want to non-destructively update the user principal, you can call `context.ReplacePrincipal()` and set the `context.ShouldRenew` property to `true`.</span></span>

<a name="security-authentication-cookie-options"></a>

## <a name="controlling-cookie-options"></a><span data-ttu-id="f8f7a-163">Contrôler les options du cookie</span><span class="sxs-lookup"><span data-stu-id="f8f7a-163">Controlling cookie options</span></span>

<span data-ttu-id="f8f7a-164">Le [CookieAuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) classe est fourni avec différentes options de configuration pour ajuster les cookies en cours de création.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-164">The [CookieAuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) class comes with various configuration options to fine-tune the cookies being created.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f8f7a-165">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-165">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="f8f7a-166">ASP.NET Core 2.x unifie les API utilisées pour configurer les cookies.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-166">ASP.NET Core 2.x unifies the APIs used for configuring cookies.</span></span> <span data-ttu-id="f8f7a-167">Le 1.x API ont été marqués comme obsolètes et un nouveau `Cookie` propriété de type `CookieBuilder` a été introduit dans le `CookieAuthenticationOptions` classe.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-167">The 1.x APIs have been marked as obsolete, and a new `Cookie` property of type `CookieBuilder` has been introduced in the `CookieAuthenticationOptions` class.</span></span> <span data-ttu-id="f8f7a-168">Il est recommandé que vous migrez vers l’API 2.x.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-168">It's recommended that you migrate to the 2.x APIs.</span></span>

* <span data-ttu-id="f8f7a-169">`ClaimsIssuer`est de l’émetteur à utiliser pour le [émetteur](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) propriété sur toutes les revendications créés par l’authentification de cookie.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-169">`ClaimsIssuer` is the issuer to be used for the [Issuer](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication.</span></span>

* <span data-ttu-id="f8f7a-170">`CookieBuilder.Domain`est le nom de domaine à laquelle le cookie est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-170">`CookieBuilder.Domain` is the domain name to which the cookie is served.</span></span> <span data-ttu-id="f8f7a-171">Par défaut, il est le nom d’hôte que la demande a été envoyée à.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-171">By default, this is the host name the request was sent to.</span></span> <span data-ttu-id="f8f7a-172">Le navigateur ne sert que le cookie à un nom d’hôte correspondant.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-172">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="f8f7a-173">Vous pouvez souhaiter ajuster ici pour que les cookies disponibles pour tous les hôtes de votre domaine.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-173">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="f8f7a-174">Par exemple, si le domaine du cookie à `.contoso.com` met à disposition `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`, etc.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-174">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`, etc.</span></span>

* <span data-ttu-id="f8f7a-175">`CookieBuilder.HttpOnly`est un indicateur qui indique si le cookie doit être accessible uniquement aux serveurs.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-175">`CookieBuilder.HttpOnly` is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="f8f7a-176">Par défaut est `true`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-176">This defaults to `true`.</span></span> <span data-ttu-id="f8f7a-177">Modification de cette valeur peut s’ouvrir à votre application au vol de cookies de votre application doit avoir un un bogue de scripts entre sites.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-177">Changing this value may open your application to cookie theft should your application have a Cross-Site Scripting bug.</span></span>

* <span data-ttu-id="f8f7a-178">`CookieBuilder.Path`peut être utilisé pour isoler les applications en cours d’exécution sur le même nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-178">`CookieBuilder.Path` can be used to isolate applications running on the same host name.</span></span> <span data-ttu-id="f8f7a-179">Si vous avez une application en cours d’exécution `/app1` et souhaitez limiter les cookies émis juste à envoyer à cette application, vous devez définir le `CookiePath` propriété `/app1`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-179">If you have an app running in `/app1` and want to limit the cookies issued to just be sent to that application, then you should set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="f8f7a-180">En procédant ainsi, le cookie est uniquement disponible pour les demandes vers `/app1` ou tout ce qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-180">By doing so, the cookie is only available to requests to `/app1` or anything underneath it.</span></span>

* <span data-ttu-id="f8f7a-181">`CookieBuilder.SameSite`Indique si le navigateur doit autoriser le cookie à attacher aux demandes d’un même site ou entre sites.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-181">`CookieBuilder.SameSite` indicates whether the browser should allow the cookie to be attached to same-site or cross-site requests.</span></span> <span data-ttu-id="f8f7a-182">Par défaut est `SameSiteMode.Lax`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-182">This defaults to `SameSiteMode.Lax`.</span></span>

* <span data-ttu-id="f8f7a-183">`CookieBuilder.SecurePolicy`est un indicateur qui indique si le cookie créé doit être limité pour HTTPS, HTTP ou HTTPS ou le même protocole que la demande.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-183">`CookieBuilder.SecurePolicy` is a flag indicating if the cookie created should be limited to HTTPS, HTTP or HTTPS, or the same protocol as the request.</span></span> <span data-ttu-id="f8f7a-184">Par défaut est `SameAsRequest`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-184">This defaults to `SameAsRequest`.</span></span>

* <span data-ttu-id="f8f7a-185">`ExpireTimeSpan`est le `TimeSpan` issue de laquelle le cookie expire.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-185">`ExpireTimeSpan` is the `TimeSpan` after which the cookie expires.</span></span> <span data-ttu-id="f8f7a-186">Il est ajouté à la date et heure actuelles pour créer la date d’expiration du cookie.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-186">It's added to the current date and time to create the expiry date for the cookie.</span></span>

* <span data-ttu-id="f8f7a-187">`SlidingExpiration`indicateur spécifiant si la date d’expiration du cookie réinitialise lorsque plus de la moitié de la `ExpireTimeSpan` est écoulé.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-187">`SlidingExpiration` is a flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="f8f7a-188">La date d’expiration est déplacée vers la date actuelle plus la `ExpireTimespan`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-188">The new expiry date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="f8f7a-189">Un [délai d’expiration absolue](xref:security/authentication/cookie#security-authentication-absolute-expiry) peut être définie à l’aide de la `AuthenticationProperties` lors de l’appel de la classe `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-189">An [absolute expiry time](xref:security/authentication/cookie#security-authentication-absolute-expiry) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="f8f7a-190">Un délai d’expiration absolue peut améliorer la sécurité de votre application en limitant la quantité de la durée pendant laquelle le cookie d’authentification est valid.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-190">An absolute expiry can improve the security of your application by limiting the amount of time for which the authentication cookie is valid.</span></span>

<span data-ttu-id="f8f7a-191">Un exemple d’utilisation `CookieAuthenticationOptions` dans les `ConfigureServices` méthode *Startup.cs* suit :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-191">An example of using `CookieAuthenticationOptions` in the `ConfigureServices` method of *Startup.cs* follows:</span></span>

```csharp
services.AddAuthentication()
        .AddCookie(options =>
        {
            options.Cookie.Name = "AuthCookie";
            options.Cookie.Domain = "contoso.com";
            options.Cookie.Path = "/";
            options.Cookie.HttpOnly = true;
            options.Cookie.SameSite = SameSiteMode.Lax;
            options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f8f7a-192">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-192">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

* <span data-ttu-id="f8f7a-193">`ClaimsIssuer`est de l’émetteur à utiliser pour le [émetteur](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) propriété sur toutes les revendications créés par l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="f8f7a-193">`ClaimsIssuer` is the issuer to be used for the [Issuer](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the middleware.</span></span>

* <span data-ttu-id="f8f7a-194">`CookieDomain`est le nom de domaine à laquelle le cookie est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-194">`CookieDomain` is the domain name to which the cookie is served.</span></span> <span data-ttu-id="f8f7a-195">Par défaut, il est le nom d’hôte que la demande a été envoyée à.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-195">By default, this is the host name the request was sent to.</span></span> <span data-ttu-id="f8f7a-196">Le navigateur ne sert que le cookie à un nom d’hôte correspondant.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-196">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="f8f7a-197">Vous pouvez souhaiter ajuster ici pour que les cookies disponibles pour tous les hôtes de votre domaine.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-197">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="f8f7a-198">Par exemple, si le domaine du cookie à `.contoso.com` met à disposition `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`, etc.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-198">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`, etc.</span></span>

* <span data-ttu-id="f8f7a-199">`CookieHttpOnly`est un indicateur qui indique si le cookie doit être accessible uniquement aux serveurs.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-199">`CookieHttpOnly` is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="f8f7a-200">Par défaut est `true`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-200">This defaults to `true`.</span></span> <span data-ttu-id="f8f7a-201">Modification de cette valeur peut s’ouvrir à votre application au vol de cookies de votre application doit avoir un un bogue de scripts entre sites.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-201">Changing this value may open your application to cookie theft should your application have a Cross-Site Scripting bug.</span></span>

* <span data-ttu-id="f8f7a-202">`CookiePath`peut être utilisé pour isoler les applications en cours d’exécution sur le même nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-202">`CookiePath` can be used to isolate applications running on the same host name.</span></span> <span data-ttu-id="f8f7a-203">Si vous avez une application en cours d’exécution `/app1` et souhaitez limiter les cookies émis juste à envoyer à cette application, vous devez définir le `CookiePath` propriété `/app1`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-203">If you have an app running in `/app1` and want to limit the cookies issued to just be sent to that application, then you should set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="f8f7a-204">En procédant ainsi, le cookie est uniquement disponible pour les demandes vers `/app1` ou tout ce qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-204">By doing so, the cookie is only available to requests to `/app1` or anything underneath it.</span></span>

* <span data-ttu-id="f8f7a-205">`CookieSecure`est un indicateur qui indique si le cookie créé doit être limité pour HTTPS, HTTP ou HTTPS ou le même protocole que la demande.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-205">`CookieSecure` is a flag indicating if the cookie created should be limited to HTTPS, HTTP or HTTPS, or the same protocol as the request.</span></span> <span data-ttu-id="f8f7a-206">Par défaut est `SameAsRequest`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-206">This defaults to `SameAsRequest`.</span></span>

* <span data-ttu-id="f8f7a-207">`ExpireTimeSpan`est le `TimeSpan` issue de laquelle le cookie expire.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-207">`ExpireTimeSpan` is the `TimeSpan` after which the cookie expires.</span></span> <span data-ttu-id="f8f7a-208">Il est ajouté à la date et heure actuelles pour créer la date d’expiration du cookie.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-208">It's added to the current date and time to create the expiry date for the cookie.</span></span>

* <span data-ttu-id="f8f7a-209">`SlidingExpiration`indicateur spécifiant si la date d’expiration du cookie réinitialise lorsque plus de la moitié de la `ExpireTimeSpan` est écoulé.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-209">`SlidingExpiration` is a flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="f8f7a-210">La date d’expiration est déplacée vers la date actuelle plus la `ExpireTimespan`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-210">The new expiry date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="f8f7a-211">Un [délai d’expiration absolue](xref:security/authentication/cookie#security-authentication-absolute-expiry) peut être définie à l’aide de la `AuthenticationProperties` lors de l’appel de la classe `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-211">An [absolute expiry time](xref:security/authentication/cookie#security-authentication-absolute-expiry) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="f8f7a-212">Un délai d’expiration absolue peut améliorer la sécurité de votre application en limitant la quantité de la durée pendant laquelle le cookie d’authentification est valid.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-212">An absolute expiry can improve the security of your application by limiting the amount of time for which the authentication cookie is valid.</span></span>

<span data-ttu-id="f8f7a-213">Un exemple d’utilisation `CookieAuthenticationOptions` dans les `Configure` méthode *Startup.cs* suit :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-213">An example of using `CookieAuthenticationOptions` in the `Configure` method of *Startup.cs* follows:</span></span>

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

## <a name="persistent-cookies-and-absolute-expiry-times"></a><span data-ttu-id="f8f7a-214">Les cookies persistants et les délais d’expiration absolue</span><span class="sxs-lookup"><span data-stu-id="f8f7a-214">Persistent cookies and absolute expiry times</span></span>

<span data-ttu-id="f8f7a-215">Vous souhaiterez peut-être l’expiration du cookie à demeurent entre les sessions de navigateur que vous souhaitez un délai d’expiration absolue à l’identité et le cookie transportant.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-215">You may want the cookie expiry to persist across browser sessions and want an absolute expiry to the identity and the cookie transporting it.</span></span> <span data-ttu-id="f8f7a-216">Cette persistance doit être activée avec le consentement explicite de l’utilisateur, via une case à cocher « Mémoriser mes informations » sur la connexion ou un mécanisme similaire.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-216">This persistence should only be enabled with explicit user consent, via a "Remember Me" checkbox on login or a similar mechanism.</span></span> <span data-ttu-id="f8f7a-217">Vous pouvez effectuer les opérations suivantes à l’aide de la `AuthenticationProperties` paramètre sur le `SignInAsync` méthode appelée lorsque [d’une identité de signature et la création de cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie).</span><span class="sxs-lookup"><span data-stu-id="f8f7a-217">You can do these things by using the `AuthenticationProperties` parameter on the `SignInAsync` method called when [signing in an identity and creating the cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie).</span></span> <span data-ttu-id="f8f7a-218">Exemple :</span><span class="sxs-lookup"><span data-stu-id="f8f7a-218">For example:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f8f7a-219">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-219">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="f8f7a-220">Le `AuthenticationProperties` (classe), utilisé dans l’extrait de code précédent, se trouve dans le `Microsoft.AspNetCore.Authentication` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-220">The `AuthenticationProperties` class, used in the preceding code snippet, resides in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f8f7a-221">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-221">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="f8f7a-222">Le `AuthenticationProperties` (classe), utilisé dans l’extrait de code précédent, se trouve dans le `Microsoft.AspNetCore.Http.Authentication` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-222">The `AuthenticationProperties` class, used in the preceding code snippet, resides in the `Microsoft.AspNetCore.Http.Authentication` namespace.</span></span>

---

<span data-ttu-id="f8f7a-223">L’extrait de code précédent crée une identité et le cookie correspondant qui survit par le biais des fermetures de navigateur.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-223">The preceding code snippet creates an identity and corresponding cookie which survives through browser closures.</span></span> <span data-ttu-id="f8f7a-224">Tous les paramètres d’expiration décalée configurés précédemment via [options cookie](xref:security/authentication/cookie#security-authentication-cookie-options) sont toujours respectées.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-224">Any sliding expiration settings previously configured via [cookie options](xref:security/authentication/cookie#security-authentication-cookie-options) are still honored.</span></span> <span data-ttu-id="f8f7a-225">Si le cookie expire alors que le navigateur est fermé, le navigateur efface une fois qu’il est redémarré.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-225">If the cookie expires whilst the browser is closed, the browser clears it once it is restarted.</span></span>

<a name="security-authentication-absolute-expiry"></a>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f8f7a-226">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-226">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f8f7a-227">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f8f7a-227">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

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

<span data-ttu-id="f8f7a-228">L’extrait de code précédent crée une identité et le cookie correspondant qui dure 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-228">The preceding code snippet creates an identity and corresponding cookie which lasts for 20 minutes.</span></span> <span data-ttu-id="f8f7a-229">Il ignore tous les paramètres d’expiration décalée configurés précédemment via [options cookie](xref:security/authentication/cookie#security-authentication-cookie-options).</span><span class="sxs-lookup"><span data-stu-id="f8f7a-229">This ignores any sliding expiration settings previously configured via [cookie options](xref:security/authentication/cookie#security-authentication-cookie-options).</span></span>

<span data-ttu-id="f8f7a-230">Le `ExpiresUtc` et `IsPersistent` propriétés s’excluent mutuellement.</span><span class="sxs-lookup"><span data-stu-id="f8f7a-230">The `ExpiresUtc` and `IsPersistent` properties are mutually exclusive.</span></span>
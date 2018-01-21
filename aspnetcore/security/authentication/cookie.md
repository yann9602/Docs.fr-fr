---
title: "À l’aide de l’authentification de Cookie sans ASP.NET Core identité"
author: rick-anderson
description: "Une explication de l’utilisation de l’authentification de cookie sans ASP.NET Core Identity"
ms.author: riande
manager: wpickett
ms.date: 10/11/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/cookie
ms.openlocfilehash: 26921eb6af6629d821e57112a47b40146cb027f6
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="using-cookie-authentication-without-aspnet-core-identity"></a>À l’aide de l’authentification de Cookie sans ASP.NET Core identité

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Luke Latham](https://github.com/guardrex)

Comme vous l’avez vu dans les rubriques précédentes de l’authentification, [ASP.NET Core Identity](xref:security/authentication/identity) est un fournisseur d’authentification terminé et complet pour créer et maintenir les connexions. Toutefois, il pouvez que vous souhaitez utiliser votre propre logique d’authentification personnalisée avec l’authentification basée sur les cookies à des moments. Vous pouvez utiliser l’authentification par cookie comme un fournisseur d’authentification autonome sans ASP.NET Core Identity.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

Pour plus d’informations sur l’authentification basée sur le cookie de migration à partir de ASP.NET 1.x vers la version 2.0, consultez [migration de l’authentification et d’identité ASP.NET Core 2.0 rubrique (basée sur le Cookie d’authentification)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).

## <a name="configuration"></a>Configuration

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Si vous n’utilisez pas le [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), installez la version 2.0 + de le [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package NuGet.

Dans le `ConfigureServices` (méthode), créer le service de l’intergiciel (middleware) d’authentification avec le `AddAuthentication` et `AddCookie` méthodes :

[!code-csharp[Main](cookie/sample/Startup.cs?name=snippet1)]

`AuthenticationScheme`passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application. `AuthenticationScheme`est utile lorsqu’il existe plusieurs instances de l’authentification de cookie et vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme). Définition de la `AuthenticationScheme` à `CookieAuthenticationDefaults.AuthenticationScheme` fournit une valeur de « Cookies » pour le schéma. Vous pouvez fournir une valeur de chaîne qui distingue le schéma.

Dans le `Configure` méthode, utilisez la `UseAuthentication` méthode à appeler l’intergiciel (middleware) d’authentification qui définit la `HttpContext.User` propriété. Appelez le `UseAuthentication` méthode avant d’appeler `UseMvcWithDefaultRoute` ou `UseMvc`:

[!code-csharp[Main](cookie/sample/Startup.cs?name=snippet2)]

**Options de AddCookie**

Le [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) classe est utilisée pour configurer les options de fournisseur d’authentification.

| Option | Description |
| ------ | ----------- |
| [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | Fournit le chemin d’accès à fournir une détection 302 (redirection d’URL) lorsque déclenchée par `HttpContext.ForbidAsync`. La valeur par défaut est `/Account/AccessDenied`. |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | L’émetteur à utiliser pour le [émetteur](/dotnet/api/system.security.claims.claim.issuer) propriété sur toutes les revendications créés par le service d’authentification de cookie. |
| [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | Le nom de domaine dans lequel le cookie est pris en charge. Par défaut, il est le nom d’hôte de la demande. Le navigateur envoie uniquement le cookie dans les demandes à un nom d’hôte correspondant. Vous pouvez souhaiter ajuster ici pour que les cookies disponibles pour tous les hôtes de votre domaine. Par exemple, si le domaine du cookie à `.contoso.com` met à disposition `contoso.com`, `www.contoso.com`, et `staging.www.contoso.com`. |
| [Cookie.Expiration](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.expiration?view=aspnetcore-2.0) | Obtient ou définit la durée de vie d’un cookie. Actuellement, cette option non-ops et deviendra obsolète dans ASP.NET Core 2.1 +. Utilisez la `ExpireTimeSpan` option permettant de définir l’expiration du cookie. Pour plus d’informations, consultez [préciser le comportement de CookieAuthenticationOptions.Cookie.Expiration](https://github.com/aspnet/Security/issues/1293). |
| [Cookie.HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | Indicateur qui indique si le cookie doit être accessible uniquement aux serveurs. Modification de cette valeur à `false` autorise les scripts côté client pour accéder au cookie et peuvent s’ouvrir votre application au vol de cookie doit disposer de votre application un [inter-site (XSS)](xref:security/cross-site-scripting) vulnérabilité. La valeur par défaut est `true`. |
| [Cookie.Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | Définit le nom du cookie. |
| [Cookie.Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | Permet d’isoler les applications qui s’exécutent sur le même nom d’hôte. Si vous avez une application en cours d’exécution à `/app1` et vous souhaitez restreindre les cookies pour cette application, définissez la `CookiePath` propriété `/app1`. En procédant ainsi, le cookie est uniquement disponible sur les demandes à `/app1` et n’importe quelle application situés en dessous. |
| [Cookie.SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | Indique si le navigateur doit autoriser le cookie à attacher aux demandes du même site uniquement (`SameSiteMode.Strict`) ou les demandes entre sites à l’aide des méthodes HTTP sécurisés et les demandes du même site (`SameSiteMode.Lax`). Lorsque la valeur `SameSiteMode.None`, la valeur d’en-tête cookie n’est pas définie. Notez que [intergiciel (middleware) de Cookie stratégie](#cookie-policy-middleware) peut remplacer la valeur que vous fournissez. Pour prendre en charge l’authentification OAuth, la valeur par défaut est `SameSiteMode.Lax`. Pour plus d’informations, consultez [authentification OAuth interrompue en raison d’une stratégie de cookies SameSite](https://github.com/aspnet/Security/issues/1231). |
| [Cookie.SecurePolicy](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | Un indicateur qui indique si le cookie créé doit être limité à HTTPS (`CookieSecurePolicy.Always`), HTTP ou HTTPS (`CookieSecurePolicy.None`), ou le même protocole que la demande (`CookieSecurePolicy.SameAsRequest`). La valeur par défaut est `CookieSecurePolicy.SameAsRequest`. |
| [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | Définit le `DataProtectionProvider` qui est utilisé pour créer la valeur par défaut `TicketDataFormat`. Si le `TicketDataFormat` propriété est définie, la `DataProtectionProvider` option n’est pas utilisée. Si n’est fourni, le fournisseur de protection des données de l’application par défaut est utilisé. |
| [Événements](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | Le gestionnaire appelle des méthodes sur le fournisseur qui donnent le contrôle de l’application à certains points de traitement. Si `Events` ne sont pas fournies, une instance par défaut est fournie qui ne fait rien lorsque les méthodes sont appelées. |
| [EventsType](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | Utilisé en tant que le type de service pour obtenir le `Events` instance au lieu de la propriété. |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | Le `TimeSpan` après laquelle le ticket d’authentification stocké dans le cookie expire. `ExpireTimeSpan`est ajouté à l’heure actuelle pour créer le délai d’expiration pour le ticket. Le `ExpiredTimeSpan` la valeur est toujours en AuthTicket chiffrée vérifié par le serveur. Il peut également aller dans le [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) en-tête, mais uniquement si `IsPersistent` est défini. Pour définir `IsPersistent` à `true`, configurer le [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) passé à `SignInAsync`. La valeur par défaut de `ExpireTimeSpan` est de 14 jours. |
| [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | Fournit le chemin d’accès à fournir une détection 302 (redirection d’URL) lorsque déclenchée par `HttpContext.ChallengeAsync`. L’URL actuelle qui a généré le code 401 est ajoutée à la `LoginPath` comme paramètre de chaîne de requête nommé par le `ReturnUrlParameter`. Une fois une demande pour le `LoginPath` accorde une nouvelle identité de connexion, le `ReturnUrlParameter` valeur est utilisée pour rediriger le navigateur vers l’URL qui a provoqué le code d’état non autorisé d’origine. La valeur par défaut est `/Account/Login`. |
| [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | Si le `LogoutPath` est fournie pour le gestionnaire, ensuite une demande à ce chemin d’accès redirige en fonction de la valeur de la `ReturnUrlParameter`. La valeur par défaut est `/Account/Logout`. |
| [ReturnUrlParameter](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | Détermine le nom du paramètre de chaîne de requête qui est ajouté par le Gestionnaire de réponse 302 trouvé (redirection d’URL). `ReturnUrlParameter`est utilisé lorsqu’une demande arrive sur le `LoginPath` ou `LogoutPath` pour renvoyer le navigateur vers l’URL d’origine après l’exécution de l’action de connexion ou de déconnexion. La valeur par défaut est `ReturnUrl`. |
| [SessionStore](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | Conteneur facultatif utilisé pour stocker l’identité entre les demandes. Lorsqu’il est utilisé, uniquement un identificateur de session est envoyé au client. `SessionStore`peut être utilisé pour atténuer les problèmes potentiels avec les identités de grande taille. |
| [SlidingExpiration](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | Indicateur signalant si un nouveau cookie avec un délai d’expiration de mise à jour doit être émis de manière dynamique. Cela peut se produire sur toute demande dont le délai d’expiration du cookie actuel est supérieur à 50 % expiré. La nouvelle date d’expiration est déplacée vers la date actuelle plus la `ExpireTimespan`. Un [délai d’expiration de cookie absolu](xref:security/authentication/cookie#absolute-cookie-expiration) peut être définie à l’aide de la `AuthenticationProperties` lors de l’appel de la classe `SignInAsync`. Un délai d’expiration absolue peut améliorer la sécurité de votre application en limitant la durée pendant laquelle le cookie d’authentification est valide. La valeur par défaut est `true`. |
| [TicketDataFormat](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | Le `TicketDataFormat` est utilisé pour protéger et déprotéger l’identité et autres propriétés qui sont stockées dans la valeur du cookie. Si n’est fourni, un `TicketDataFormat` est créé à l’aide de la [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0). |
| [Validate](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | Méthode qui vérifie que les options sont valides. |

Définissez `CookieAuthenticationOptions` dans la configuration du service pour l’authentification dans le `ConfigureServices` méthode :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

ASP.NET Core 1.x utilise cookie [intergiciel (middleware)](xref:fundamentals/middleware) qui sérialise un principal d’utilisateur dans un cookie chiffré. Pour les demandes suivantes, le cookie est validé, et le principal est recréé et affecté à la `HttpContext.User` propriété.

Installer le [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package NuGet dans votre projet. Ce package contient le middleware du cookie.

Utilisez le `UseCookieAuthentication` méthode dans le `Configure` méthode dans votre *Startup.cs* avant de fichiers `UseMvc` ou `UseMvcWithDefaultRoute`:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AccessDeniedPath = "/Account/Forbidden/",
    AuthenticationScheme = CookieAuthenticationDefaults.AuthenticationScheme,
    AutomaticAuthenticate = true,
    AutomaticChallenge = true,
    LoginPath = "/Account/Unauthorized/"
});
```

**Options de CookieAuthenticationOptions**

Le [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) classe est utilisée pour configurer les options de fournisseur d’authentification.

| Option | Description |
| ------ | ----------- |
| [AuthenticationScheme](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | Définit le schéma d’authentification. `AuthenticationScheme`est utile lorsqu’il existe plusieurs instances de l’authentification et vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme). Définition de la `AuthenticationScheme` à `CookieAuthenticationDefaults.AuthenticationScheme` fournit une valeur de « Cookies » pour le schéma. Vous pouvez fournir une valeur de chaîne qui distingue le schéma. |
| [AutomaticAuthenticate](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | Définit une valeur pour indiquer que l’authentification de cookie doit s’exécuter sur chaque demande et tentez de valider et de reconstruire toute entité sérialisée de que sa création. |
| [AutomaticChallenge](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | Si la valeur est true, l’intergiciel (middleware) d’authentification gère les défis automatique. Si la valeur est false, l’intergiciel (middleware) d’authentification modifie uniquement les réponses lorsque explicitement indiqué par le `AuthenticationScheme`. |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | L’émetteur à utiliser pour le [émetteur](/dotnet/api/system.security.claims.claim.issuer) propriété sur toutes les revendications créés par l’intergiciel (middleware) d’authentification de cookie. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | Le nom de domaine dans lequel le cookie est pris en charge. Par défaut, il est le nom d’hôte de la demande. Le navigateur ne sert que le cookie à un nom d’hôte correspondant. Vous pouvez souhaiter ajuster ici pour que les cookies disponibles pour tous les hôtes de votre domaine. Par exemple, si le domaine du cookie à `.contoso.com` met à disposition `contoso.com`, `www.contoso.com`, et `staging.www.contoso.com`. |
| [CookieHttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | Indicateur qui indique si le cookie doit être accessible uniquement aux serveurs. Modification de cette valeur à `false` autorise les scripts côté client pour accéder au cookie et peuvent s’ouvrir votre application au vol de cookie doit disposer de votre application un [inter-site (XSS)](xref:security/cross-site-scripting) vulnérabilité. La valeur par défaut est `true`. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | Permet d’isoler les applications qui s’exécutent sur le même nom d’hôte. Si vous avez une application en cours d’exécution à `/app1` et vous souhaitez restreindre les cookies pour cette application, définissez la `CookiePath` propriété `/app1`. En procédant ainsi, le cookie est uniquement disponible sur les demandes à `/app1` et n’importe quelle application situés en dessous. |
| [CookieSecure](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | Un indicateur qui indique si le cookie créé doit être limité à HTTPS (`CookieSecurePolicy.Always`), HTTP ou HTTPS (`CookieSecurePolicy.None`), ou le même protocole que la demande (`CookieSecurePolicy.SameAsRequest`). La valeur par défaut est `CookieSecurePolicy.SameAsRequest`. |
| [Description](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | Informations supplémentaires sur le type d’authentification qui est rendue disponible à l’application. |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | Le `TimeSpan` après laquelle le ticket d’authentification expire. Il est ajouté à l’heure actuelle pour créer le délai d’expiration pour le ticket. Pour utiliser `ExpireTimeSpan`, vous devez définir `IsPersistent` à `true` dans les `AuthenticationProperties` passé à `SignInAsync`. La valeur par défaut est de 14 jours. |
| [SlidingExpiration](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | Indicateur spécifiant si la date d’expiration du cookie réinitialise lorsque plus de la moitié de la `ExpireTimeSpan` est écoulé. La nouvelle heure exipiration est déplacée vers la date actuelle plus la `ExpireTimespan`. Un [délai d’expiration de cookie absolu](xref:security/authentication/cookie#absolute-cookie-expiration) peut être définie à l’aide de la `AuthenticationProperties` lors de l’appel de la classe `SignInAsync`. Un délai d’expiration absolue peut améliorer la sécurité de votre application en limitant la durée pendant laquelle le cookie d’authentification est valide. La valeur par défaut est `true`. |

Définissez `CookieAuthenticationOptions` pour l’intergiciel (middleware) d’authentification de Cookie dans le `Configure` méthode :

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

---

## <a name="cookie-policy-middleware"></a>Intergiciel (middleware) de cookie stratégie

[Intergiciel (middleware) de cookie stratégie](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) Active les fonctionnalités de stratégie de cookie dans une application. Ajout de l’intergiciel (middleware) au pipeline de traitement d’application est l’ordre de la casse ; Il affecte uniquement les composants inscrits après lui dans le pipeline.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 Le [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) fourni à l’intergiciel de stratégie de Cookie vous permettent de contrôler des caractéristiques globales du traitement du cookie et le crochet en gestionnaires de traitement du cookie lorsque les cookies sont ajoutées ou supprimées.

| Propriété | Description |
| -------- | ----------- |
| [HttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | Affecte si les cookies doivent être HttpOnly, qui est un indicateur indiquant si le cookie doit être accessible uniquement aux serveurs. La valeur par défaut est `HttpOnlyPolicy.None`. |
| [MinimumSameSitePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | Affecte un attribut du même site du cookie (voir ci-dessous). La valeur par défaut est `SameSiteMode.Lax`. Cette option est disponible pour les principaux d’ASP.NET 2.0 +. |
| [OnAppendCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | Appelé lorsqu’un cookie est ajouté. |
| [OnDeleteCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | Appelé lorsqu’un cookie est supprimé. |
| [Secure](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | Détermine si les cookies doivent être sécurisés. La valeur par défaut est `CookieSecurePolicy.None`. |

**MinimumSameSitePolicy** (ASP.NET Core 2.0 + uniquement)

La valeur par défaut `MinimumSameSitePolicy` valeur est `SameSiteMode.Lax` pour permettre l’authentification OAuth2. Pour impose une stratégie du même site de `SameSiteMode.Strict`, définissez le `MinimumSameSitePolicy`. Bien que ce paramètre s’arrête OAuth2 et autres schémas d’authentification de cross-origine, elle élève le niveau de sécurité des cookies pour d’autres types d’applications qui ne reposent sur le traitement de la demande cross-origin.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Le paramètre de stratégie Middleware du Cookie de `MinimumSameSitePolicy` peuvent affecter votre paramètre de `Cookie.SameSite` dans `CookieAuthenticationOptions` paramètres en fonction de la matrice ci-dessous.

| MinimumSameSitePolicy | Cookie.SameSite | Paramètre Cookie.SameSite résultante |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="creating-an-authentication-cookie"></a>Création d’un cookie d’authentification

Pour créer un cookie contenant les informations de l’utilisateur, vous devez construire une [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). Les informations de l’utilisateur sont sérialisées et stockées dans le cookie. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Appelez [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) connecter l’utilisateur :

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Appelez [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) connecter l’utilisateur :

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

---

`SignInAsync`Crée un cookie chiffré et l’ajoute à la réponse actuelle. Si vous ne spécifiez pas un `AuthenticationScheme`, le schéma par défaut est utilisé.

En arrière-plan, le chiffrement utilisé est de ASP.NET Core [Protection des données](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) système. Si vous hébergez une application sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou à l’aide d’une batterie de serveurs web, vous devez [configurer la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur de l’application.

## <a name="signing-out"></a>Déconnecter

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Pour déconnecter l’utilisateur actuel et supprimer les cookies, appelez [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):

```csharp
await HttpContext.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Pour déconnecter l’utilisateur actuel et supprimer les cookies, appelez [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

---

Si vous n’utilisez pas `CookieAuthenticationDefaults.AuthenticationScheme` (ou « Cookies ») que le schéma (par exemple, « ContosoCookie »), indiquez le schéma que vous avez utilisé lors de la configuration du fournisseur d’authentification. Dans le cas contraire, le schéma par défaut est utilisé.

## <a name="reacting-to-back-end-changes"></a>Réagir aux modifications du serveur principal

Une fois qu’un cookie est créé, il devient la seule source d’identité. Même si vous désactivez un utilisateur dans vos systèmes principaux, le système d’authentification de cookie n’a aucune connaissance de ce, et un utilisateur reste connecté en tant que cookie est valide.

Le [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) événement dans ASP.NET Core 2.x ou [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) méthode dans ASP.NET Core 1.x peut être utilisé pour intercepter et de remplacer la validation de l’identité de cookie. Cette approche réduit le risque d’utilisateurs révoqués l’accès à l’application.

Une méthode de validation de cookie est basée sur le suivi de lorsque la base de données utilisateur a été modifiée. Si la base de données n’a pas été modifié depuis que le cookie d’utilisateur a été émis, il n’est pas nécessaire pour authentifier l’utilisateur si le cookie est toujours valide. Pour implémenter ce scénario, la base de données, qui est implémenté dans `IUserRepository` pour cet exemple, stocke un `LastChanged` valeur. Lorsqu’un utilisateur est mise à jour dans la base de données, le `LastChanged` a la valeur à l’heure actuelle.

Pour invalider un cookie lorsque les modifications de la base de données basée sur le `LastChanged` valeur, créer le cookie avec une `LastChanged` contenant actuel de la revendication `LastChanged` valeur à partir de la base de données :

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Pour implémenter un remplacement pour le `ValidatePrincipal` événement, écriture, une méthode avec la signature suivante dans une classe que vous dérivez de [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Un exemple ressemble à ceci :

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Enregistrer l’instance d’événements lors de l’inscription du service de cookie dans le `ConfigureServices` (méthode). Fournir l’inscription d’une service avec étendue pour votre `CustomCookieAuthenticationEvents` classe :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Pour implémenter un remplacement pour le `ValidateAsync` événement, écriture, une méthode avec la signature suivante :

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

ASP.NET Core Identity implémente cette vérification dans le cadre de son [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync). Un exemple ressemble à ceci :

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = 
            context.HttpContext.RequestServices
                .GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Inscrire l’événement lors de la configuration de l’authentification de cookie dans le `Configure` méthode :

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

Prenons le cas dans lequel le nom d’utilisateur est mise à jour &mdash; une décision qui n’affecte pas la sécurité de toute façon. Si vous souhaitez mettre à jour de façon non destructrice le principal de l’utilisateur, appelez `context.ReplacePrincipal` et définir le `context.ShouldRenew` propriété `true`.

> [!WARNING]
> L’approche décrite ici est déclenché à chaque demande. Cela peut entraîner une baisse des performances de l’application.

## <a name="persistent-cookies"></a>Cookies persistants

Vous souhaiterez peut-être le cookie pour conserver dans les sessions de navigateur. Cette persistance doit être activée avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes informations » sur la connexion ou un mécanisme similaire. 

L’extrait de code suivant crée une identité et le cookie correspondant qui persiste via la fermeture du navigateur. Tous les paramètres d’expiration décalée précédemment configurées sont respectées. Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie s’est arrêté.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

Le [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) classe se trouve dans le `Microsoft.AspNetCore.Authentication` espace de noms.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

Le [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) classe se trouve dans le `Microsoft.AspNetCore.Http.Authentication` espace de noms.

---

## <a name="absolute-cookie-expiration"></a>Expiration du cookie absolu

Vous pouvez définir une heure d’expiration absolue avec `ExpiresUtc`. Vous devez également définir `IsPersistent`; sinon, `ExpiresUtc` est ignorée et un cookie de session unique est créé. Lorsque `ExpiresUtc` est définie sur `SignInAsync`, il remplace la valeur de la `ExpireTimeSpan` option de `CookieAuthenticationOptions`, si définie.

L’extrait de code suivant crée une identité et le cookie correspondant qui dure 20 minutes. Il ignore tous les paramètres d’expiration décalée précédemment configurés.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

---

## <a name="see-also"></a>Voir aussi

* [Les modifications d’authentification 2.0 / annonce de Migration](https://github.com/aspnet/Announcements/issues/262)
* [Limitation d’identité par schéma](xref:security/authorization/limitingidentitybyscheme)

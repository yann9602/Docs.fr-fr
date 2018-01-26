---
title: "Migration d’authentification et de l’identité au cœur d’ASP.NET 2.0"
author: scottaddie
description: "Cet article présente les étapes les plus courantes pour l’authentification de migration ASP.NET Core 1.x et l’identité pour ASP.NET 2.0 de base."
ms.author: scaddie
manager: wpickett
ms.date: 10/26/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 72ad31438a344fb5fa2b357c709b923b8077e742
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="migrating-authentication-and-identity-to-aspnet-core-20"></a>Migration de l’authentification et identité au cœur d’ASP.NET 2.0

Par [Scott Addie](https://github.com/scottaddie) et [arts martiaux de Hao](https://github.com/HaoK)

ASP.NET Core 2.0 a un nouveau modèle pour l’authentification et [identité](xref:security/authentication/identity) qui simplifie la configuration à l’aide des services. Les applications ASP.NET Core 1.x qui utilisent l’authentification ou l’identité peuvent être mis à jour pour utiliser le nouveau modèle, comme indiqué ci-dessous.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Intergiciel (middleware) d’authentification et services
Dans les projets 1.x, l’authentification est configurée via l’intergiciel (middleware). Une méthode de l’intergiciel (middleware) est appelée pour chaque schéma d’authentification que vous souhaitez prendre en charge.

L’exemple 1.x suivant configure l’authentification Facebook avec l’identité dans *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions { 
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
} 
```

Dans les 2.0 projets, l’authentification est configurée via les services. Chaque schéma d’authentification est enregistré dans le `ConfigureServices` méthode *Startup.cs*. Le `UseIdentity` (méthode) est remplacée par `UseAuthentication`.

L’exemple 2.0 suivant configure l’authentification Facebook avec l’identité dans *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options => 
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

Le `UseAuthentication` méthode ajoute un composant d’intergiciel (middleware) d’authentification unique qui est responsable de l’authentification automatique et la gestion des demandes d’authentification à distance. Il remplace tous les composants individuels intergiciel (middleware) avec un composant d’intergiciel (middleware) unique et commun.

Vous trouverez ci-dessous les instructions de migration 2.0 pour chaque schéma d’authentification principales.

### <a name="cookie-based-authentication"></a>Authentification par cookie
Sélectionnez une des deux options ci-dessous et apportez les modifications nécessaires dans *Startup.cs*:

1. Utilisation des cookies avec l’identité
    - Remplacez `UseIdentity` avec `UseAuthentication` dans le `Configure` méthode :

        ```csharp
        app.UseAuthentication();
        ```

    - Appeler le `AddIdentity` méthode dans le `ConfigureServices` méthode pour ajouter les services d’authentification de cookie.
    - Si vous le souhaitez, appelez le `ConfigureApplicationCookie` ou `ConfigureExternalCookie` méthode dans le `ConfigureServices` méthode pour modifier les paramètres de cookies d’identité.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();
    
        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Utilisation des cookies sans identité
    - Remplacez le `UseCookieAuthentication` l’appel de méthode le `Configure` méthode avec `UseAuthentication`:
  
        ```csharp
        app.UseAuthentication();
        ```
 
    - Appeler le `AddAuthentication` et `AddCookie` méthodes dans le `ConfigureServices` méthode :

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User, 
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options => 
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>Authentification du support JSON
Apportez les modifications suivantes dans *Startup.cs*:
- Remplacez le `UseJwtBearerAuthentication` l’appel de méthode le `Configure` méthode avec `UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- Appeler le `AddJwtBearer` méthode dans le `ConfigureServices` méthode :

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options => 
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Cet extrait de code n’utilise pas l’identité, afin du schéma par défaut doit être défini en passant `JwtBearerDefaults.AuthenticationScheme` à la `AddAuthentication` (méthode).

### <a name="openid-connect-oidc-authentication"></a>OpenID Connect (OIDC) authentification
Apportez les modifications suivantes dans *Startup.cs*:

- Remplacez le `UseOpenIdConnectAuthentication` l’appel de méthode le `Configure` méthode avec `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Appeler le `AddOpenIdConnect` méthode dans le `ConfigureServices` méthode :

    ```csharp
    services.AddAuthentication(options => 
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options => 
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

### <a name="facebook-authentication"></a>Authentification Facebook
Apportez les modifications suivantes dans *Startup.cs*:
- Remplacez le `UseFacebookAuthentication` l’appel de méthode le `Configure` méthode avec `UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- Appeler le `AddFacebook` méthode dans le `ConfigureServices` méthode :
    
    ```csharp
    services.AddAuthentication()
            .AddFacebook(options => 
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Authentification Google
Apportez les modifications suivantes dans *Startup.cs*:
- Remplacez le `UseGoogleAuthentication` l’appel de méthode le `Configure` méthode avec `UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- Appeler le `AddGoogle` méthode dans le `ConfigureServices` méthode :

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options => 
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });    
    ```

### <a name="microsoft-account-authentication"></a>Authentification de compte Microsoft
Apportez les modifications suivantes dans *Startup.cs*:
- Remplacez le `UseMicrosoftAccountAuthentication` l’appel de méthode le `Configure` méthode avec `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Appeler le `AddMicrosoftAccount` méthode dans le `ConfigureServices` méthode :

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options => 
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ``` 

### <a name="twitter-authentication"></a>Authentification Twitter
Apportez les modifications suivantes dans *Startup.cs*:
- Remplacez le `UseTwitterAuthentication` l’appel de méthode le `Configure` méthode avec `UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- Appeler le `AddTwitter` méthode dans le `ConfigureServices` méthode :

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options => 
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Définition des schémas d’authentification par défaut
Dans 1.x, le `AutomaticAuthenticate` et `AutomaticChallenge` propriétés de la [AuthenticationOptions](https://docs.microsoft.com/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) classe de base a été conçue pour être définie sur un schéma d’authentification unique. Il n’existait aucun moyen de bonne pour appliquer ceci.

Dans la version 2.0, ces deux propriétés ont été supprimées en tant que propriétés sur chaque `AuthenticationOptions` instance. Ils peuvent être configurés dans le `AddAuthentication` appel de méthode dans le `ConfigureServices` méthode de *Startup.cs*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

Dans l’extrait de code précédent, le schéma par défaut est défini sur `CookieAuthenticationDefaults.AuthenticationScheme` (« Cookies »).

Vous pouvez également utiliser une version surchargée de la `AddAuthentication` pour définir plusieurs propriétés. Dans l’exemple suivant de la méthode surchargée, le schéma par défaut est défini `CookieAuthenticationDefaults.AuthenticationScheme`. Le schéma d’authentification peut également être spécifié au sein de votre personne `[Authorize]` attributs ou des stratégies d’autorisation.

```csharp
services.AddAuthentication(options => 
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Définir un schéma par défaut dans la version 2.0 si une des conditions suivantes est remplie :
- Vous souhaitez que l’utilisateur d’être automatiquement connectés
- Vous utilisez la `[Authorize]` stratégies d’attribut ou d’autorisation sans spécifier de schémas

Une exception à cette règle est le `AddIdentity` (méthode). Cette méthode ajoute des cookies pour vous et définit la valeur par défaut s’authentifier et stimulation des schémas dans le cookie d’application `IdentityConstants.ApplicationScheme`. En outre, il définit le schéma de connexion par défaut pour le cookie externe `IdentityConstants.ExternalScheme`.

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Utiliser les Extensions d’authentification HttpContext
Le `IAuthenticationManager` interface est le point d’entrée principal dans le système d’authentification 1.x. Elle a été remplacée par un nouvel ensemble de `HttpContext` les méthodes d’extension dans le `Microsoft.AspNetCore.Authentication` espace de noms.

Par exemple, les projets 1.x référence un `Authentication` propriété :

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Dans les 2.0 projets, vous devez importer le `Microsoft.AspNetCore.Authentication` espace de noms et supprimer la `Authentication` références de propriété :

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>L’authentification Windows (HTTP.sys / IISIntegration)
Il existe deux variantes de l’authentification Windows :
1. L’hôte autorise uniquement les utilisateurs authentifiés
2. L’ordinateur hôte permet à la fois anonymes et les utilisateurs authentifiés

La première variante décrite ci-dessus n’est pas affectée par les modifications de 2.0.

La deuxième variante décrite ci-dessus est affectée par les modifications de 2.0. Par exemple, vous pouvez autoriser les utilisateurs anonymes à votre application sur IIS ou [HTTP.sys](xref:fundamentals/servers/weblistener) mais autorisant des utilisateurs au niveau du contrôleur de couche. Dans ce scénario, définissez le schéma par défaut sur `IISDefaults.AuthenticationScheme` dans les `ConfigureServices` méthode *Startup.cs*:

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

Échec de définition de schéma par défaut empêche en conséquence la demande authorize au défi de fonctionner.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>Instances de IdentityCookieOptions
Un effet secondaire des 2.0 modifications est le commutateur à l’aide de ces options au lieu d’instances d’options de cookie. La possibilité de personnaliser les noms de schéma identité cookie est supprimée.

Par exemple, des projets utilisent 1.x [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) pour passer un `IdentityCookieOptions` paramètre dans *AccountController.cs*. Le schéma d’authentification externe cookie est accessible à partir de l’instance fournie :

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

L’injection de constructeur susmentionnés devienne inutile dans les 2.0 projets et le `_externalCookieScheme` champ peut être supprimé :

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

Le `IdentityConstants.ExternalScheme` constante peut être utilisée directement :

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>Ajouter des propriétés de Navigation IdentityUser POCO
Les propriétés de navigation de base d’Entity Framework (EF) de la base de `IdentityUser` POCO (Plain objet CLR) ont été supprimées. Si votre projet 1.x utilisé ces propriétés, les ajouter manuellement au projet 2.0 :

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

Pour empêcher les clés étrangères en double lors de l’exécution de Migrations de noyaux EF, ajoutez le code suivant à votre `IdentityDbContext` classe `OnModelCreating` (méthode) (une fois la `base.OnModelCreating();` appeler) :

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>Remplacez GetExternalAuthenticationSchemes
La méthode synchrone `GetExternalAuthenticationSchemes` a été supprimé en faveur d’une version asynchrone. les projets de 1.x ont le code suivant dans *ManageController.cs*:

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Cette méthode s’affiche dans *Login.cshtml* trop :

[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

Dans les 2.0 projets, utilisez la `GetExternalAuthenticationSchemesAsync` méthode :

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

Dans *Login.cshtml*, le `AuthenticationScheme` propriété accédée dans le `foreach` boucle devient `Name`:

[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Modification de la propriété de ManageLoginsViewModel
A `ManageLoginsViewModel` objet est utilisé dans le `ManageLogins` action de *ManageController.cs*. Dans les projets 1.x, l’objet `OtherLogins` propriété type de retour est `IList<AuthenticationDescription>`. Ce type de retour nécessite une importation de `Microsoft.AspNetCore.Http.Authentication`:

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

Dans les 2.0 projets, le type de retour devient `IList<AuthenticationScheme>`. Ce nouveau type de retour nécessite en remplaçant le `Microsoft.AspNetCore.Http.Authentication` importer avec un `Microsoft.AspNetCore.Authentication` importer.

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Ressources supplémentaires
Pour plus d’informations et la description, consultez le [discussion sur la version 2.0 d’authentification](https://github.com/aspnet/Security/issues/1338) problème sur GitHub.

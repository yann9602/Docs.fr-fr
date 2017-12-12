---
title: "Les fournisseurs de stockage personnalisés pour ASP.NET Core Identity | Documents Microsoft"
author: ardalis
description: "Comment configurer les fournisseurs de stockage personnalisés pour ASP.NET Core Identity."
keywords: "Fournisseurs de stockage personnalisés ASP.NET Core, identité,"
ms.author: riande
manager: wpickett
ms.date: 05/24/2017
ms.topic: article
ms.assetid: b2ace545-ecf6-4664-b31e-b65bd4a6b025
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 687ca96be5121502e816bdc856e17dcd5923fe05
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>Fournisseurs de stockage personnalisés pour ASP.NET Core Identity

Par [Steve Smith](https://ardalis.com/)

Identité de ASP.NET Core est un système extensible qui vous permet de créer un fournisseur de stockage personnalisé et le connecter à votre application. Cette rubrique décrit comment créer un fournisseur de stockage personnalisé pour ASP.NET Core Identity. Il aborde des concepts importants pour la création de votre propre fournisseur de stockage, mais n’est pas une procédure pas à pas.

[Afficher ou télécharger l’exemple à partir de GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample).

## <a name="introduction"></a>Introduction

Par défaut, le système d’identité de base ASP.NET stocke les informations utilisateur dans une base de données SQL Server à l’aide d’Entity Framework Core. Pour de nombreuses applications, cette approche fonctionne bien. Toutefois, vous pouvez utiliser un mécanisme de persistance différent ou un schéma de données. Exemple :

* Vous utilisez [Azure Table Storage](https://docs.microsoft.com/azure/storage/) ou une autre banque de données.
* Votre base de données ont une structure différente. 
* Vous souhaiterez peut-être utiliser une approche de l’accès des données différentes, telles que [Dapper](https://github.com/StackExchange/Dapper). 

Dans chacun de ces cas, vous pouvez écrire un fournisseur personnalisé pour votre mécanisme de stockage et connectez ce fournisseur de votre application.

Identité de ASP.NET Core est incluse dans les modèles de projet dans Visual Studio avec l’option « Comptes d’utilisateur individuels ».

Lorsque vous utilisez l’interface de ligne de base de .NET, ajoutez `-au Individual`:

```console
dotnet new mvc -au Individual
dotnet new webapi -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>L’architecture ASP.NET Core Identity

ASP.NET Core identité se compose de classes appelées des responsables et des magasins. *Gestionnaires de* sont des classes de haut niveau qui utilise des développeurs d’application pour effectuer des opérations, telles que la création d’un utilisateur de l’identité. *Magasins* sont des classes de niveau inférieur qui spécifient comment les entités, telles que les utilisateurs et les rôles, sont conservées. Magasins de suivent le [modèle de référentiel](http://deviq.com/repository-pattern/) et sont étroitement couplées avec le mécanisme de persistance. Gestionnaires sont dissociés de magasins, ce qui signifie que vous pouvez remplacer le mécanisme de persistance sans modifier votre code d’application (à l’exception de la configuration).

Le diagramme suivant montre comment une application web interagit avec les gestionnaires, tandis que les magasins d’interagissent avec la couche d’accès aux données.

![Applications ASP.NET Core fonctionnent avec les gestionnaires (par exemple, 'UserManager', « RoleManager »). Gestionnaires fonctionnent avec des magasins (par exemple, « UserStore ») qui communiquent avec une Source de données à l’aide d’une bibliothèque comme Entity Framework Core.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Pour créer un fournisseur de stockage personnalisé, créez la source de données, la couche d’accès aux données et les classes de magasin qui interagissent avec cette couche d’accès aux données (zones grises et verts dans le diagramme ci-dessus). Vous n’avez pas besoin de personnaliser les gestionnaires ou code de votre application qui interagit avec eux (zones bleu ci-dessus).

Lorsque vous créez une nouvelle instance de `UserManager` ou `RoleManager` vous fournissez le type de la classe d’utilisateur et que vous passez une instance de la classe du magasin en tant qu’argument. Cette approche vous permet de brancher vos classes personnalisées ASP.NET Core. 

[Reconfigurer l’application pour utiliser le nouveau fournisseur de stockage](#reconfigure-app-to-use-new-storage-provider) montre comment instancier `UserManager` et `RoleManager` avec un magasin personnalisé.

## <a name="aspnet-core-identity-stores-data-types"></a>Identité de ASP.NET Core stocke des types de données

[ASP.NET Core Identity](https://github.com/aspnet/identity) des types de données sont décrites en détail dans les sections suivantes :

### <a name="users"></a>Utilisateurs

Utilisateurs enregistrés de votre site web. Le [IdentityUser](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuser) type peut être étendu ou utilisé comme un exemple pour votre propre type personnalisé. Vous n’avez pas besoin d’hériter d’un type particulier d’implémenter votre propre solution de stockage d’identité personnalisé.

### <a name="user-claims"></a>Revendications d’utilisateur

Un ensemble d’instructions (ou [revendications](https://docs.microsoft.com//dotnet/api/system.security.claims.claim)) sur l’utilisateur qui représentent l’identité de l’utilisateur. Permet la plus grande expression d’identité de l’utilisateur que peut être obtenue via des rôles.

### <a name="user-logins"></a>Connexions utilisateur

Informations sur le fournisseur d’authentification externe (tels que Facebook ou un compte Microsoft) à utiliser lors de la connexion d’un utilisateur. [Exemple](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>Rôles

Groupes d’autorisation pour votre site. Inclut le nom de rôle Id et le rôle (par exemple, « Admin » ou « Employee »). [Exemple](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>La couche d’accès aux données

Cette rubrique suppose que vous êtes familiarisé avec le mécanisme de persistance que vous vous apprêtez à utiliser et comment créer des entités pour ce mécanisme. Cette rubrique ne fournit pas de détails sur la façon de créer des référentiels ou classes d’accès aux données ; Il fournit des suggestions sur les décisions de conception lorsque vous travaillez avec ASP.NET Core Identity.

Vous avez beaucoup de liberté lors de la conception de la couche d’accès aux données pour un fournisseur de magasin personnalisé. Vous devez uniquement créer des mécanismes de persistance pour les fonctionnalités que vous souhaitez utiliser dans votre application. Par exemple, si vous n’utilisez pas les rôles dans votre application, il est inutile créer le stockage pour les rôles ou les associations de rôle d’utilisateur. Votre infrastructure existante et des technologies peuvent nécessiter une structure est très différente de l’implémentation par défaut de l’identité de ASP.NET Core. Dans votre couche d’accès aux données, vous fournir la logique permettant de travailler sur la structure de votre implémentation de stockage.

La couche d’accès aux données fournit la logique pour enregistrer les données d’identité de ASP.NET Core pour une source de données. La couche d’accès aux données pour votre fournisseur de stockage personnalisé peut inclure les classes suivantes pour stocker les informations utilisateur et le rôle.

### <a name="context-class"></a>Context (classe)

Encapsule les informations pour vous connecter à votre mécanisme de persistance et d’exécuter des requêtes. Plusieurs classes de données nécessitent une instance de cette classe, en général, fournie par le biais d’injection de dépendance. [Exemple](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).

### <a name="user-storage"></a>Stockage de l’utilisateur

Stocke et récupère les informations de l’utilisateur (par exemple, le hachage du nom et mot de passe utilisateur). [Exemple](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>Stockage de rôle

Stocke et récupère les informations de rôle (par exemple, le nom de rôle). [Exemple](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>Stockage d’userclaims.

Stocke et récupère les informations de revendication d’utilisateur (par exemple, le type de revendication et la valeur). [Exemple](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>Stockage d’userlogins.

Stocke et récupère les informations de connexion d’utilisateur (par exemple, un fournisseur d’authentification externe). [Exemple](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>Stockage du rôle d’utilisateur

Stocke et récupère les rôles sont attribués à quels utilisateurs. [Exemple](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

**Conseil :** uniquement implémenter les classes que vous souhaitez utiliser dans votre application.

Dans les classes d’accès aux données, fournissez le code pour effectuer des opérations de données pour votre mécanisme de persistance. Par exemple, au sein d’un fournisseur personnalisé, vous pouvez avoir le code suivant pour créer un nouvel utilisateur dans le *stocker* classe :

[!code-csharp[Main](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

La logique d’implémentation pour la création de l’utilisateur est dans le ``_usersTable.CreateAsync`` méthode, illustré ci-dessous.

## <a name="customize-the-user-class"></a>Personnaliser la classe d’utilisateur

Lorsque vous implémentez un fournisseur de stockage, créez une classe d’utilisateur qui est équivalente à la [ `IdentityUser` classe](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuser).

Au minimum, votre classe d’utilisateur doit inclure un `Id` et un `UserName` propriété.

Le `IdentityUser` classe définit les propriétés qui la ``UserManager`` appels lors de l’exécution des opérations demandées. Le type par défaut de la `Id` propriété est une chaîne, mais vous pouvez hériter `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` et spécifiez un autre type. Le framework attend l’implémentation du stockage pour gérer les conversions de types de données.

## <a name="customize-the-user-store"></a>Personnaliser le magasin de l’utilisateur

Créer un `UserStore` classe qui fournit les méthodes pour toutes les opérations de données sur l’utilisateur. Cette classe est équivalente à la [UserStore<TUser> ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) classe. Dans votre `UserStore` classe, implémentez `IUserStore<TUser>` et interfaces facultatives requis. Vous sélectionnez les interfaces facultatives à implémenter basées sur les fonctionnalités offertes dans votre application.

### <a name="optional-interfaces"></a>Interfaces facultatives

- IUserRoleStore https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserrolestore-1
- IUserClaimStore https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserclaimstore-1
- IUserPasswordStore https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserpasswordstore-1
- IUserSecurityStampStore<!-- make these all links and remove / -->
- IUserEmailStore
- IPhoneNumberStore
- IQueryableUserStore
- IUserLoginStore
- IUserTwoFactorStore
- IUserLockoutStore

Héritent des interfaces facultatives `IUserStore`. Vous pouvez voir un exemple partiellement implémentée utilisateur stocker [ici](https://github.com/aspnet/Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).

Dans le `UserStore` (classe), vous utilisez les classes d’accès aux données que vous avez créé pour effectuer des opérations. Ils sont passés à l’aide d’injection de dépendances. Par exemple, dans le serveur SQL Server avec une implémentation Dapper, le `UserStore` classe a la `CreateAsync` méthode qui utilise une instance de `DapperUsersTable` pour insérer un nouvel enregistrement :

[!code-csharp[Main](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Interfaces à implémenter lors de la personnalisation du magasin de l’utilisateur

- **IUserStore**  
 Le [IUserStore&lt;TUser&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserstore-1) interface est la seule interface, vous devez implémenter dans le magasin de l’utilisateur. Elle définit des méthodes pour la création, la mise à jour, la suppression et la récupération des utilisateurs.
- **IUserClaimStore**  
 Le [IUserClaimStore&lt;TUser&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserclaimstore-1) interface définit les méthodes que vous implémentez pour activer les revendications d’utilisateur. Il contient des méthodes pour ajouter, supprimer et la récupération des revendications d’utilisateur.
- **IUserLoginStore**  
 Le [IUserLoginStore&lt;TUser&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserloginstore-1) définit les méthodes que vous implémentez pour permettre aux fournisseurs d’authentification externe. Il contient des méthodes pour ajouter, supprimer et la récupération des connexions utilisateur et une méthode pour récupérer un utilisateur selon les informations de connexion.
- **IUserRoleStore**  
 Le [IUserRoleStore&lt;TUser&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserrolestore-1) interface définit les méthodes que vous implémentez pour mapper un utilisateur à un rôle. Il contient des méthodes pour ajouter, supprimer et récupérer des rôles d’un utilisateur et une méthode pour vérifier si un utilisateur est assigné à un rôle.
- **IUserPasswordStore**  
 Le [IUserPasswordStore&lt;TUser&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) interface définit les méthodes que vous implémentez pour conserver les mots de passe hachés. Il contient des méthodes pour obtenir et définir le mot de passe haché et une méthode qui indique si l’utilisateur a défini un mot de passe.
- **IUserSecurityStampStore**  
 Le [IUserSecurityStampStore&lt;TUser&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) interface définit les méthodes que vous implémentez pour utiliser un tampon de sécurité permettant d’indiquer si les informations de compte de l’utilisateur a changé. Cet horodatage est mis à jour lorsqu’un utilisateur modifie le mot de passe, ou ajoute ou supprime des connexions. Il contient des méthodes pour obtenir et définir l’horodatage de sécurité.
- **IUserTwoFactorStore**  
 Le [IUserTwoFactorStore&lt;TUser&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) interface définit les méthodes que vous implémentez pour prendre en charge l’authentification à deux facteurs. Il contient des méthodes pour obtenir et définir si l’authentification à deux facteurs est activée pour un utilisateur.
- **IUserPhoneNumberStore**  
 Le [IUserPhoneNumberStore&lt;TUser&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) interface définit les méthodes que vous implémentez pour stocker les numéros de téléphone des utilisateurs. Il contient des méthodes pour obtenir et définir le numéro de téléphone et indique si le numéro de téléphone est confirmé.
- **IUserEmailStore**  
 Le [IUserEmailStore&lt;TUser&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuseremailstore-1) interface définit les méthodes que vous implémentez pour stocker les adresses de messagerie d’utilisateur. Il contient des méthodes pour obtenir et définir l’adresse de messagerie et si l’adresse de messagerie est confirmée.
- **IUserLockoutStore**  
 Le [IUserLockoutStore&lt;TUser&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) interface définit les méthodes que vous implémentez pour stocker des informations sur le verrouillage d’un compte. Il contient des méthodes pour le suivi des tentatives d’accès ayant échoué et de verrouillages.
- **IQueryableUserStore**  
 Le [IQueryableUserStore&lt;TUser&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) interface définit l’implémentation de membres pour fournir un magasin d’utilisateurs utilisable.

Vous implémentez uniquement les interfaces qui sont nécessaires dans votre application. Exemple :

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim, IdentityUserLogin et IdentityUserRole

Le ``Microsoft.AspNet.Identity.EntityFramework`` espace de noms contient des implémentations de la [IdentityUserClaim](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuserlogin), et [IdentityUserRole](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) classes. Si vous utilisez ces fonctionnalités, vous souhaiterez créer vos propres versions de ces classes et de définir les propriétés de votre application. Toutefois, il est parfois plus efficace pour ne pas charger ces entités dans la mémoire lors de l’exécution des opérations de base (par exemple, ajout ou suppression de revendication de l’utilisateur). Au lieu de cela, les classes de magasin principal peuvent exécuter ces opérations directement sur la source de données. Par exemple, le ``UserStore.GetClaimsAsync`` méthode peut appeler le ``userClaimTable.FindByUserId(user.Id)`` méthode pour exécuter une requête sur table directement et retourner une liste de revendications.

## <a name="customize-the-role-class"></a>Personnaliser la classe de rôle

Lorsque vous implémentez un fournisseur de stockage de rôle, vous pouvez créer un type de rôle personnalisé. Il ne doive pas implémenter une interface particulière, mais il doit avoir un `Id` et en général, elle aura un `Name` propriété.

Voici un exemple de classe de rôle :

[!code-csharp[Main](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Personnaliser le magasin de rôles

Vous pouvez créer un ``RoleStore`` classe qui fournit les méthodes pour toutes les opérations de données sur des rôles. Cette classe est équivalente à la [RoleStore<TRole> ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) classe. Dans le `RoleStore` (classe), vous implémentez le ``IRoleStore<TRole>`` et éventuellement le ``IQueryableRoleStore<TRole>`` interface.

- **IRoleStore&lt;TRole&gt;**  
 Le [IRoleStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.irolestore-1) interface définit les méthodes à implémenter dans la classe de rôle du magasin. Il contient des méthodes pour la création, la mise à jour, suppression et la récupération des rôles.
- **RoleStore&lt;TRole&gt;**  
 Pour personnaliser `RoleStore`, créez une classe qui implémente le `IRoleStore` interface. 

## <a name="reconfigure-app-to-use-new-storage-provider"></a>Reconfigurer l’application pour utiliser le nouveau fournisseur de stockage

Une fois que vous avez implémenté un fournisseur de stockage, vous configurez votre application pour l’utiliser. Si votre application le fournisseur par défaut, remplacez-le par votre fournisseur personnalisé.

1. Supprimer le `Microsoft.AspNetCore.EntityFramework.Identity` package NuGet.
1. Si le fournisseur de stockage se trouve dans un package ou d’un projet distinct, ajoutez une référence à celui-ci.
1. Remplacez toutes les références à `Microsoft.AspNetCore.EntityFramework.Identity` avec un à l’aide de l’instruction pour l’espace de noms de votre fournisseur de stockage.
1. Dans le ``ConfigureServices`` méthode, change la `AddIdentity` méthode à utiliser vos types personnalisés. Vous pouvez créer vos propres méthodes d’extension à cet effet. Consultez [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) pour obtenir un exemple.
1. Si vous utilisez des rôles, mettre à jour le `RoleManager` à utiliser votre `RoleStore` classe.
1. Mettre à jour la chaîne de connexion et les informations d’identification pour la configuration de votre application.

Exemple :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>Références

- [Fournisseurs de stockage personnalisés pour ASP.NET Identity](https://docs.microsoft.com/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
- [ASP.NET Core Identity](https://github.com/aspnet/identity) -ce référentiel contient des liens vers Communauté conservée les fournisseurs de magasins.

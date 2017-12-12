---
title: "Configurer l’identité de ASP.NET Core"
author: AdrienTorris
description: "Comprendre les valeurs par défaut de ASP.NET Core Identity et configurer les propriétés d’identité différents pour utiliser des valeurs personnalisées."
keywords: "Authentification ASP.NET Core, identité, sécurité"
ms.author: scaddie
manager: wpickett
ms.date: 09/18/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-configuration
ms.openlocfilehash: 2861ca474e7e82da81943966394a92040ce96ab8
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="configure-identity"></a>Configurer l’identité

ASP.NET Core Identity a certains comportements par défaut que vous pouvez remplacer facilement dans votre application `Startup` classe.

## <a name="passwords-policy"></a>Stratégie de mots de passe

Par défaut, identité requiert que les mots de passe contient un caractère majuscule, minuscule, un chiffre et un caractère non alphanumérique. Il existe également quelques autres restrictions. Si vous souhaitez simplifier les restrictions de mot de passe, vous pouvez le faire la `Startup` classe de votre application.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Base d’ASP.NET 2.0 a ajouté le `RequiredUniqueChars` propriété. Dans le cas contraire, les options sont les mêmes à partir de ASP.NET 1.x.

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

---

`IdentityOptions.Password`a les propriétés suivantes :
* `RequireDigit`: Nécessite un nombre compris entre 0 et 9 dans le mot de passe. La valeur par défaut est true.
* `RequiredLength`: La longueur minimale du mot de passe. La valeur par défaut est 6.
* `RequireNonAlphanumeric`: Nécessite un caractère non alphanumérique dans le mot de passe. La valeur par défaut est true.
* `RequireUppercase`: Nécessite une lettre majuscule dans le mot de passe. La valeur par défaut est true.
* `RequireLowercase`: Nécessite un caractère minuscule dans le mot de passe. La valeur par défaut est true.
* `RequiredUniqueChars`: Requiert le nombre de caractères distincts dans le mot de passe. La valeur par défaut est 1.


## <a name="users-lockout"></a>Verrouillage de l’utilisateur

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,39-42,50-52)]

`IdentityOptions.Lockout`a les propriétés suivantes :
* `DefaultLockoutTimeSpan`: La durée pendant laquelle un utilisateur est verrouillé lorsqu’un verrouillage se produit. La valeur par défaut est 5 minutes.
* `MaxFailedAccessAttempts`: Nombre de tentatives d’accès ayant échoué jusqu'à ce qu’un utilisateur est verrouillé, si le verrouillage est activé. La valeur par défaut est 5.
* `AllowedForNewUsers`: Détermine si un utilisateur peut être verrouillé. La valeur par défaut est true.


## <a name="sign-in-settings"></a>Paramètres de connexion

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)]

`IdentityOptions.SignIn`a les propriétés suivantes :
* `RequireConfirmedEmail`: Nécessite un message électronique de confirmation pour vous connecter. La valeur par défaut est false.
* `RequireConfirmedPhoneNumber`: Nécessite un numéro de téléphone confirmé pour vous connecter. La valeur par défaut est false.


## <a name="user-validation-settings"></a>Paramètres de validation d’utilisateur

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,48-52)]

`IdentityOptions.User`a les propriétés suivantes :
* `RequireUniqueEmail`: Nécessite que chaque utilisateur à un message électronique unique. La valeur par défaut est false.
* `AllowedUserNameCharacters`: Caractères autorisés dans le nom d’utilisateur. La valeur par défaut est abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+.

## <a name="applications-cookie-settings"></a>Paramètres de cookies pour l’application

Comme la stratégie de mots de passe, tous les paramètres du cookie de l’application peuvent être modifiés dans la `Startup` classe.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Sous `ConfigureServices` dans le `Startup` (classe), vous pouvez configurer le cookie de l’application.

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

--- 

`CookieAuthenticationOptions`a les propriétés suivantes :
* `Cookie.Name`: Le nom du cookie. Par défaut. AspNetCore.Cookies.
* `Cookie.HttpOnly`: Lors de la valeur est true, le cookie n’est pas accessible à partir de scripts côté client. La valeur par défaut est true.
* `ExpireTimeSpan`: Détermine combien de temps le ticket d’authentification stocké dans le cookie reste valide à partir du point de que sa création. La valeur par défaut est 14 jours.
* `LoginPath`: Lorsqu’un utilisateur n’est pas autorisé, il seront redirigé à ce chemin d’accès à la connexion. La valeur par défaut est/Account/connexion.
* `LogoutPath`: Lorsqu’un utilisateur est déconnecté, il seront redirigé vers ce chemin d’accès. La valeur par défaut est/Account/déconnexion.
* `AccessDeniedPath`: Quand un utilisateur échoue une vérification d’autorisation, il seront redirigé vers ce chemin d’accès. La valeur par défaut est/Account/accès refusé.
* `SlidingExpiration`: Lors de la valeur est true, un nouveau cookie est émis avec une nouvelle heure d’expiration lorsque le cookie actuel est plus de milieu de la fenêtre d’expiration. La valeur par défaut est true.
* `ReturnUrlParameter`: La valeur ReturnUrlParameter détermine le nom du paramètre de chaîne de requête qui est ajouté par l’intergiciel (middleware) quand un code d’état non autorisé 401 est remplacée par une redirection 302 sur le chemin d’accès de connexion.
* `AuthenticationScheme`: Cela concerne uniquement les ASP.NET Core 1.x. Le nom logique pour un schéma d’authentification particulier.
* `AutomaticAuthenticate`: Cet indicateur est uniquement pertinent pour ASP.NET Core 1.x. Lorsque la valeur est true, l’authentification par cookie doit s’exécuter sur chaque demande et tenter de valider et de reconstruire toute entité sérialisée de que sa création.


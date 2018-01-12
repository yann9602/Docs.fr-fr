---
title: "Configurer l’identité de ASP.NET Core"
author: AdrienTorris
description: "Comprendre les valeurs par défaut de ASP.NET Core Identity et configurer les propriétés d’identité différents pour utiliser des valeurs personnalisées."
keywords: "Authentification ASP.NET Core, identité, sécurité"
ms.author: scaddie
manager: wpickett
ms.date: 01/11/2018
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-configuration
ms.openlocfilehash: ac204cb89aac1f90adc64c4f0bec4e946cb8c4d9
ms.sourcegitcommit: 12e5194936b7e820efc5505a2d5d4f84e88eb5ef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="configure-identity"></a>Configurer l’identité

ASP.NET Core Identity a des comportements courants dans les applications telles que la stratégie de mot de passe, l’heure de verrouillage et les paramètres de cookies que vous pouvez remplacer facilement dans votre application `Startup` classe.

## <a name="passwords-policy"></a>Stratégie de mots de passe

Par défaut, identité requiert que les mots de passe contient un caractère majuscule, minuscule, un chiffre et un caractère non alphanumérique. Il existe également quelques autres restrictions. Pour simplifier les restrictions de mot de passe, vous devez modifier le `ConfigureServices` méthode de la `Startup` classe de votre application.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Base d’ASP.NET 2.0 a ajouté le `RequiredUniqueChars` propriété. Dans le cas contraire, les options sont les mêmes à partir de ASP.NET 1.x.

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

---

`IdentityOptions.Password`a les propriétés suivantes :

| Propriété                | Description                       | Par défaut |
| ----------------------- | --------------------------------- | ------- |
| `RequireDigit`          | Nécessite un nombre compris entre 0 et 9 dans le mot de passe. | true |
| `RequiredLength`        | La longueur minimale du mot de passe. | 6 |
| `RequireNonAlphanumeric`| Nécessite un caractère non alphanumérique dans le mot de passe. | true |
| `RequireUppercase`      | Nécessite une lettre majuscule dans le mot de passe. | true |
| `RequireLowercase`      | Nécessite un caractère minuscule dans le mot de passe. | true |
| `RequiredUniqueChars`   | Requiert un nombre de caractères distincts dans le mot de passe. | 1 |


## <a name="users-lockout"></a>Verrouillage de l’utilisateur

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,39-42,50-52)]

`IdentityOptions.Lockout`a les propriétés suivantes :

| Propriété                | Description                       | Par défaut |
| ----------------------- | --------------------------------- | ------- |
| `DefaultLockoutTimeSpan` | La durée pendant laquelle un utilisateur est verrouillé lorsqu’un verrouillage se produit.  | 5 minutes  |
| `MaxFailedAccessAttempts` | Le nombre de tentatives d’accès ayant échoué jusqu'à ce qu’un utilisateur est verrouillé, si le verrouillage est activé.  | 5 |
| `AllowedForNewUsers` | Détermine si un utilisateur peut être verrouillé.  | true |

## <a name="sign-in-settings"></a>Paramètres de connexion

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)]

`IdentityOptions.SignIn`a les propriétés suivantes :

| Propriété                | Description                       | Par défaut |
| ----------------------- | --------------------------------- | ------- |
| `RequireConfirmedEmail` | Nécessite un message électronique de confirmation pour vous connecter. | False  |
| `RequireConfirmedPhoneNumber` |  Nécessite un numéro de téléphone confirmé pour vous connecter. | False  |

## <a name="user-validation-settings"></a>Paramètres de validation d’utilisateur

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,48-52)]

`IdentityOptions.User`a les propriétés suivantes :

| Propriété                | Description                       | Par défaut |
| ----------------------- | --------------------------------- | ------- |
| `RequireUniqueEmail`  | Nécessite que chaque utilisateur à un message électronique unique. | False  |
| `AllowedUserNameCharacters`  | Caractères autorisés dans le nom d’utilisateur. | abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+ |


## <a name="applications-cookie-settings"></a>Paramètres de cookies pour l’application

Comme la stratégie de mots de passe, tous les paramètres du cookie de l’application peuvent être modifiés dans la `Startup` classe.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Sous `ConfigureServices` dans le `Startup` (classe), vous pouvez configurer le cookie de l’application.

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

---

`CookieAuthenticationOptions`a les propriétés suivantes :

| Propriété                | Description                       | Par défaut |
| ----------------------- | --------------------------------- | ------- |
| `Cookie.Name`  | Le nom du cookie.  | . AspNetCore.Cookies.  |
| `Cookie.HttpOnly`  | Lorsque la valeur est true, le cookie n’est pas accessible à partir de scripts côté client.  |  true |
| `ExpireTimeSpan`  | Détermine combien de temps le ticket d’authentification stocké dans le cookie reste valide à partir du point de que sa création.  | 14 jours  |
| `LoginPath`  | Lorsqu’un utilisateur n’est pas autorisé, il seront redirigé à ce chemin d’accès à la connexion. | / / Connexion au compte  |
| `LogoutPath`  | Lorsqu’un utilisateur est déconnecté, il seront redirigé vers ce chemin d’accès.  | / Compte/déconnexion  |
| `AccessDeniedPath`  | Lorsqu’un utilisateur échoue une vérification d’autorisation, il seront redirigé vers ce chemin d’accès.  |   |
| `SlidingExpiration`  | Lorsque la valeur est true, un nouveau cookie est émis avec une nouvelle heure d’expiration lorsque le cookie actuel est plus de milieu de la fenêtre d’expiration.  | / Compte/accès refusé |
| `ReturnUrlParameter`  | Détermine le nom du paramètre de chaîne de requête qui est ajouté par l’intergiciel (middleware) quand un code d’état non autorisé 401 est remplacée par une redirection 302 sur le chemin d’accès de connexion.  |  true |
| `AuthenticationScheme`  | Cela concerne uniquement les ASP.NET Core 1.x. Le nom logique pour un schéma d’authentification particulier. |  |
| `AutomaticAuthenticate`  | Cet indicateur est uniquement pertinent pour ASP.NET Core 1.x. Lorsque la valeur est true, l’authentification par cookie doit s’exécuter sur chaque demande et tenter de valider et de reconstruire toute entité sérialisée de que sa création.  |  |
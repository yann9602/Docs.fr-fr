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
ms.openlocfilehash: adf577ae1e1c752c3b1a332ec94a7a7627a7a4b4
ms.sourcegitcommit: 76d42f09f3e0dd2f2105493eca6b29994aa47706
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2017
---
# <a name="configure-identity"></a><span data-ttu-id="3dd6c-104">Configurer l’identité</span><span class="sxs-lookup"><span data-stu-id="3dd6c-104">Configure Identity</span></span>

<span data-ttu-id="3dd6c-105">ASP.NET Core Identity a certains comportements par défaut que vous pouvez remplacer facilement dans votre application `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-105">ASP.NET Core Identity has some default behaviors that you can override easily in your application's `Startup` class.</span></span>

## <a name="passwords-policy"></a><span data-ttu-id="3dd6c-106">Stratégie de mots de passe</span><span class="sxs-lookup"><span data-stu-id="3dd6c-106">Passwords policy</span></span>

<span data-ttu-id="3dd6c-107">Par défaut, identité requiert que les mots de passe contient un caractère majuscule, minuscule, un chiffre et un caractère alphanumérique.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-107">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and an alphanumeric character.</span></span> <span data-ttu-id="3dd6c-108">Il existe également quelques autres restrictions.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-108">There are also some other restrictions.</span></span> <span data-ttu-id="3dd6c-109">Si vous souhaitez simplifier les restrictions de mot de passe, vous pouvez le faire la `Startup` classe de votre application.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-109">If you want to simplify password restrictions, you can do that in the `Startup` class of your application.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="3dd6c-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="3dd6c-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="3dd6c-111">Base d’ASP.NET 2.0 a ajouté le `RequiredUniqueChars` propriété.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-111">ASP.NET Core 2.0 added the `RequiredUniqueChars` property.</span></span> <span data-ttu-id="3dd6c-112">Dans le cas contraire, les options sont les mêmes à partir de ASP.NET 1.x.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-112">Otherwise, the options are the same from ASP.NET Core 1.x.</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="3dd6c-113">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="3dd6c-113">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

---

<span data-ttu-id="3dd6c-114">`IdentityOptions.Password`a les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="3dd6c-114">`IdentityOptions.Password` has the following properties:</span></span>
* <span data-ttu-id="3dd6c-115">`RequireDigit`: Nécessite un nombre compris entre 0 et 9 dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-115">`RequireDigit`: Requires a number between 0-9 in the password.</span></span> <span data-ttu-id="3dd6c-116">La valeur par défaut est true.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-116">Defaults to true.</span></span>
* <span data-ttu-id="3dd6c-117">`RequiredLength`: La longueur minimale du mot de passe.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-117">`RequiredLength`: The minimum length of the password.</span></span> <span data-ttu-id="3dd6c-118">La valeur par défaut est 6.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-118">Defaults to 6.</span></span>
* <span data-ttu-id="3dd6c-119">`RequireNonAlphanumeric`: Nécessite un caractère non alphanumérique dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-119">`RequireNonAlphanumeric`: Requires a non-alphanumeric character in the password.</span></span> <span data-ttu-id="3dd6c-120">La valeur par défaut est true.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-120">Defaults to true.</span></span>
* <span data-ttu-id="3dd6c-121">`RequireUppercase`: Nécessite une lettre majuscule dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-121">`RequireUppercase`: Requires an upper case character in the password.</span></span> <span data-ttu-id="3dd6c-122">La valeur par défaut est true.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-122">Defaults to true.</span></span>
* <span data-ttu-id="3dd6c-123">`RequireLowercase`: Nécessite un caractère minuscule dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-123">`RequireLowercase`: Requires a lower case character in the password.</span></span> <span data-ttu-id="3dd6c-124">La valeur par défaut est true.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-124">Defaults to true.</span></span>
* <span data-ttu-id="3dd6c-125">`RequiredUniqueChars`: Requiert le nombre de caractères distincts dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-125">`RequiredUniqueChars`: Requires the number of distinct characters in the password.</span></span> <span data-ttu-id="3dd6c-126">La valeur par défaut est 1.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-126">Defaults to 1.</span></span>


## <a name="users-lockout"></a><span data-ttu-id="3dd6c-127">Verrouillage de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="3dd6c-127">User's lockout</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,39-42,50-52)]

<span data-ttu-id="3dd6c-128">`IdentityOptions.Lockout`a les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="3dd6c-128">`IdentityOptions.Lockout` has the following properties:</span></span>
* <span data-ttu-id="3dd6c-129">`DefaultLockoutTimeSpan`: La durée pendant laquelle un utilisateur est verrouillé lorsqu’un verrouillage se produit.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-129">`DefaultLockoutTimeSpan`: The amount of time a user is locked out when a lockout occurs.</span></span> <span data-ttu-id="3dd6c-130">La valeur par défaut est 5 minutes.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-130">Defaults to 5 minutes.</span></span>
* <span data-ttu-id="3dd6c-131">`MaxFailedAccessAttempts`: Nombre de tentatives d’accès ayant échoué jusqu'à ce qu’un utilisateur est verrouillé, si le verrouillage est activé.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-131">`MaxFailedAccessAttempts`: The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> <span data-ttu-id="3dd6c-132">La valeur par défaut est 5.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-132">Defaults to 5.</span></span>
* <span data-ttu-id="3dd6c-133">`AllowedForNewUsers`: Détermine si un utilisateur peut être verrouillé. La valeur par défaut est true.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-133">`AllowedForNewUsers`: Determines if a new user can be locked out. Defaults to true.</span></span>


## <a name="sign-in-settings"></a><span data-ttu-id="3dd6c-134">Paramètres de connexion</span><span class="sxs-lookup"><span data-stu-id="3dd6c-134">Sign in settings</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)]

<span data-ttu-id="3dd6c-135">`IdentityOptions.SignIn`a les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="3dd6c-135">`IdentityOptions.SignIn` has the following properties:</span></span>
* <span data-ttu-id="3dd6c-136">`RequireConfirmedEmail`: Nécessite un message électronique de confirmation pour vous connecter.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-136">`RequireConfirmedEmail`: Requires a confirmed email to sign in.</span></span> <span data-ttu-id="3dd6c-137">La valeur par défaut est false.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-137">Defaults to false.</span></span>
* <span data-ttu-id="3dd6c-138">`RequireConfirmedPhoneNumber`: Nécessite un numéro de téléphone confirmé pour vous connecter.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-138">`RequireConfirmedPhoneNumber`: Requires a confirmed phone number to sign in.</span></span> <span data-ttu-id="3dd6c-139">La valeur par défaut est false.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-139">Defaults to false.</span></span>


## <a name="user-validation-settings"></a><span data-ttu-id="3dd6c-140">Paramètres de validation d’utilisateur</span><span class="sxs-lookup"><span data-stu-id="3dd6c-140">User validation settings</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,48-52)]

<span data-ttu-id="3dd6c-141">`IdentityOptions.User`a les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="3dd6c-141">`IdentityOptions.User` has the following properties:</span></span>
* <span data-ttu-id="3dd6c-142">`RequireUniqueEmail`: Nécessite que chaque utilisateur à un message électronique unique.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-142">`RequireUniqueEmail`: Requires each User to have a unique email.</span></span> <span data-ttu-id="3dd6c-143">La valeur par défaut est false.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-143">Defaults to false.</span></span>
* <span data-ttu-id="3dd6c-144">`AllowedUserNameCharacters`: Caractères autorisés dans le nom d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-144">`AllowedUserNameCharacters`: Allowed characters in the username.</span></span> <span data-ttu-id="3dd6c-145">La valeur par défaut est abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-145">Defaults to abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+.</span></span>

## <a name="applications-cookie-settings"></a><span data-ttu-id="3dd6c-146">Paramètres de cookies pour l’application</span><span class="sxs-lookup"><span data-stu-id="3dd6c-146">Application's cookie settings</span></span>

<span data-ttu-id="3dd6c-147">Comme la stratégie de mots de passe, tous les paramètres du cookie de l’application peuvent être modifiés dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-147">Like the passwords policy, all the settings of the application's cookie can be changed in the `Startup` class.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="3dd6c-148">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="3dd6c-148">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="3dd6c-149">Sous `ConfigureServices` dans le `Startup` (classe), vous pouvez configurer le cookie de l’application.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-149">Under `ConfigureServices` in the `Startup` class, you can configure the application's cookie.</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="3dd6c-150">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="3dd6c-150">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

--- 

<span data-ttu-id="3dd6c-151">`CookieAuthenticationOptions`a les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="3dd6c-151">`CookieAuthenticationOptions` has the following properties:</span></span>
* <span data-ttu-id="3dd6c-152">`Cookie.Name`: Le nom du cookie.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-152">`Cookie.Name`: The name of the cookie.</span></span> <span data-ttu-id="3dd6c-153">Par défaut. AspNetCore.Cookies.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-153">Defaults to .AspNetCore.Cookies.</span></span>
* <span data-ttu-id="3dd6c-154">`Cookie.HttpOnly`: Lors de la valeur est true, le cookie n’est pas accessible à partir de scripts côté client.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-154">`Cookie.HttpOnly`: When true, the cookie is not accessible from client-side scripts.</span></span> <span data-ttu-id="3dd6c-155">La valeur par défaut est true.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-155">Defaults to true.</span></span>
* <span data-ttu-id="3dd6c-156">`ExpireTimeSpan`: Détermine combien de temps le ticket d’authentification stocké dans le cookie reste valide à partir du point de que sa création.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-156">`ExpireTimeSpan`: Controls how much time the authentication ticket stored in the cookie will remain valid from the point it is created.</span></span> <span data-ttu-id="3dd6c-157">La valeur par défaut est 14 jours.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-157">Defaults to 14 days.</span></span>
* <span data-ttu-id="3dd6c-158">`LoginPath`: Lorsqu’un utilisateur n’est pas autorisé, il seront redirigé à ce chemin d’accès à la connexion.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-158">`LoginPath`: When a user is unauthorized, they will be redirected to this path to login.</span></span> <span data-ttu-id="3dd6c-159">La valeur par défaut est/Account/connexion.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-159">Defaults to /Account/Login.</span></span>
* <span data-ttu-id="3dd6c-160">`LogoutPath`: Lorsqu’un utilisateur est déconnecté, il seront redirigé vers ce chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-160">`LogoutPath`: When a user is logged out, they will be redirected to this path.</span></span> <span data-ttu-id="3dd6c-161">La valeur par défaut est/Account/déconnexion.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-161">Defaults to /Account/Logout.</span></span>
* <span data-ttu-id="3dd6c-162">`AccessDeniedPath`: Quand un utilisateur échoue une vérification d’autorisation, il seront redirigé vers ce chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-162">`AccessDeniedPath`: When a user fails an authorization check, they will be redirected to this path.</span></span> <span data-ttu-id="3dd6c-163">La valeur par défaut est/Account/accès refusé.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-163">Defaults to /Account/AccessDenied.</span></span>
* <span data-ttu-id="3dd6c-164">`SlidingExpiration`: Lors de la valeur est true, un nouveau cookie est émis avec une nouvelle heure d’expiration lorsque le cookie actuel est plus de milieu de la fenêtre d’expiration.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-164">`SlidingExpiration`: When true, a new cookie will be issued with a new expiration time when the current cookie is more than halfway through the expiration window.</span></span> <span data-ttu-id="3dd6c-165">La valeur par défaut est true.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-165">Defaults to true.</span></span>
* <span data-ttu-id="3dd6c-166">`ReturnUrlParameter`: La valeur ReturnUrlParameter détermine le nom du paramètre de chaîne de requête qui est ajouté par l’intergiciel (middleware) quand un code d’état non autorisé 401 est remplacée par une redirection 302 sur le chemin d’accès de connexion.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-166">`ReturnUrlParameter`: The ReturnUrlParameter determines the name of the query string parameter which is appended by the middleware when a 401 Unauthorized status code is changed to a 302 redirect onto the login path.</span></span>
* <span data-ttu-id="3dd6c-167">`AuthenticationScheme`: Cela concerne uniquement les ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-167">`AuthenticationScheme`: This is only relevant for ASP.NET Core 1.x.</span></span> <span data-ttu-id="3dd6c-168">Le nom logique pour un schéma d’authentification particulier.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-168">The logical name for a particular authentication scheme.</span></span>
* <span data-ttu-id="3dd6c-169">`AutomaticAuthenticate`: Cet indicateur est uniquement pertinent pour ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-169">`AutomaticAuthenticate`: This flag is only relevant for ASP.NET Core 1.x.</span></span> <span data-ttu-id="3dd6c-170">Lorsque la valeur est true, l’authentification par cookie doit s’exécuter sur chaque demande et tenter de valider et de reconstruire toute entité sérialisée de que sa création.</span><span class="sxs-lookup"><span data-stu-id="3dd6c-170">When true, cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span>


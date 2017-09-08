---
title: Hachage de mot de passe
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 982a1eb2-1e6f-4909-896f-82784364472d
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 262eed1f310ff3216c893fdc8a738f2ba6ec6729
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="password-hashing"></a><span data-ttu-id="114d0-103">Hachage de mot de passe</span><span class="sxs-lookup"><span data-stu-id="114d0-103">Password Hashing</span></span>

<span data-ttu-id="114d0-104">La base de code de protection de données inclut un package *Microsoft.AspNetCore.Cryptography.KeyDerivation* qui contient les fonctions de dérivation de clé de chiffrement.</span><span class="sxs-lookup"><span data-stu-id="114d0-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="114d0-105">Ce package est un composant autonome et n’a aucune dépendance sur le reste du système de protection des données.</span><span class="sxs-lookup"><span data-stu-id="114d0-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="114d0-106">Il peut être utilisé totalement indépendants.</span><span class="sxs-lookup"><span data-stu-id="114d0-106">It can be used completely independently.</span></span> <span data-ttu-id="114d0-107">La source existe en même temps que le code de protection des données base pour des raisons pratiques.</span><span class="sxs-lookup"><span data-stu-id="114d0-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="114d0-108">Le package actuellement propose une méthode `KeyDerivation.Pbkdf2` qui permet de hachage d’un mot de passe à l’aide de la [PBKDF2 algorithme](https://tools.ietf.org/html/rfc2898#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="114d0-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="114d0-109">Cette API méthode est très similaire à .NET Framework existant [Rfc2898DeriveBytes type](https://msdn.microsoft.com/library/System.Security.Cryptography.Rfc2898DeriveBytes(v=vs.110).aspx), mais il existe trois des différences importantes :</span><span class="sxs-lookup"><span data-stu-id="114d0-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](https://msdn.microsoft.com/library/System.Security.Cryptography.Rfc2898DeriveBytes(v=vs.110).aspx), but there are three important distinctions:</span></span>

1. <span data-ttu-id="114d0-110">Le `KeyDerivation.Pbkdf2` méthode prend en charge la consommation de plusieurs PRF (actuellement `HMACSHA1`, `HMACSHA256`, et `HMACSHA512`), alors que le `Rfc2898DeriveBytes` prend uniquement en charge de type `HMACSHA1`.</span><span class="sxs-lookup"><span data-stu-id="114d0-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="114d0-111">Le `KeyDerivation.Pbkdf2` méthode détecte le système d’exploitation actuel et tente de choisir l’implémentation plus optimisée de la routine, en fournissant de bien meilleures performances dans certains cas.</span><span class="sxs-lookup"><span data-stu-id="114d0-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="114d0-112">(Sous Windows 8, il offre environ 10 x le débit de `Rfc2898DeriveBytes`.)</span><span class="sxs-lookup"><span data-stu-id="114d0-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="114d0-113">Le `KeyDerivation.Pbkdf2` méthode exige de l’appelant spécifier tous les paramètres (SEL, PRF et nombre d’itérations).</span><span class="sxs-lookup"><span data-stu-id="114d0-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="114d0-114">Le `Rfc2898DeriveBytes` type fournit les valeurs par défaut pour ces.</span><span class="sxs-lookup"><span data-stu-id="114d0-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

<span data-ttu-id="114d0-115">[!code-csharp[Main](password-hashing/samples/passwordhasher.cs)]</span><span class="sxs-lookup"><span data-stu-id="114d0-115">[!code-csharp[Main](password-hashing/samples/passwordhasher.cs)]</span></span>

<span data-ttu-id="114d0-116">Consultez le code source pour ASP.NET Core Identity `PasswordHasher` cas d’utilisation de type pour un monde réel.</span><span class="sxs-lookup"><span data-stu-id="114d0-116">See the source code for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>

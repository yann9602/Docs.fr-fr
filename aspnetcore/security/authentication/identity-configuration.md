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
ms.openlocfilehash: 629fcc2941b2d2fda9604a3eac04be3d0f5294b2
ms.sourcegitcommit: ddefc78270bd9b5ae0b1bd8de6c45f6977e7dceb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2017
---
# <a name="configure-identity"></a><span data-ttu-id="4cd60-104">Configurer l’identité</span><span class="sxs-lookup"><span data-stu-id="4cd60-104">Configure Identity</span></span>

<span data-ttu-id="4cd60-105">Identité de ASP.NET Core a certains comportements par défaut que vous pouvez remplacer facilement dans la classe de démarrage de votre application.</span><span class="sxs-lookup"><span data-stu-id="4cd60-105">ASP.NET Core Identity has some default behaviors that you can override easily in your application's startup class.</span></span>

## <a name="passwords-policy"></a><span data-ttu-id="4cd60-106">Stratégie de mots de passe</span><span class="sxs-lookup"><span data-stu-id="4cd60-106">Passwords policy</span></span>

<span data-ttu-id="4cd60-107">Par défaut, identité requiert que les mots de passe contient un caractère majuscule, minuscule et des chiffres.</span><span class="sxs-lookup"><span data-stu-id="4cd60-107">By default, Identity requires that passwords contain an uppercase character, lowercase character, and digits.</span></span> <span data-ttu-id="4cd60-108">Il existe également quelques autres restrictions.</span><span class="sxs-lookup"><span data-stu-id="4cd60-108">There are also some other restrictions.</span></span> <span data-ttu-id="4cd60-109">Si vous souhaitez simplifier les restrictions de mot de passe, vous pouvez le faire dans la classe de démarrage de votre application.</span><span class="sxs-lookup"><span data-stu-id="4cd60-109">If you want to simplify password restrictions, you can do that in the startup class of your application.</span></span>

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]

## <a name="applications-cookie-settings"></a><span data-ttu-id="4cd60-110">Paramètres de cookies pour l’application</span><span class="sxs-lookup"><span data-stu-id="4cd60-110">Application's cookie settings</span></span>

<span data-ttu-id="4cd60-111">Comme la stratégie de mots de passe, tous les paramètres du cookie de l’application peuvent être modifiés dans la classe de démarrage.</span><span class="sxs-lookup"><span data-stu-id="4cd60-111">Like the passwords policy, all the settings of the application's cookie can be changed in the startup class.</span></span>

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]

## <a name="users-lockout"></a><span data-ttu-id="4cd60-112">Verrouillage de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="4cd60-112">User's lockout</span></span>

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]

---
title: "Configurer l’identité"
uid: security/authentication/identity-configuration
ms.openlocfilehash: 7ccd89360a8c7f5c8c6dfac76df42898e18a116a
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="configure-identity"></a><span data-ttu-id="445e0-102">Configurer l’identité</span><span class="sxs-lookup"><span data-stu-id="445e0-102">Configure Identity</span></span>

<span data-ttu-id="445e0-103">Identité de ASP.NET Core a certains comportements par défaut que vous pouvez remplacer facilement dans la classe de démarrage de votre application.</span><span class="sxs-lookup"><span data-stu-id="445e0-103">ASP.NET Core Identity has some default behaviors that you can override easily in your application's startup class.</span></span>

## <a name="passwords-policy"></a><span data-ttu-id="445e0-104">Stratégie de mots de passe</span><span class="sxs-lookup"><span data-stu-id="445e0-104">Passwords policy</span></span>

<span data-ttu-id="445e0-105">Par défaut, identité requiert que les mots de passe contient un caractère majuscule, minuscule et des chiffres.</span><span class="sxs-lookup"><span data-stu-id="445e0-105">By default, Identity requires that passwords contain an uppercase character, lowercase character, and digits.</span></span> <span data-ttu-id="445e0-106">Il existe également quelques autres restrictions.</span><span class="sxs-lookup"><span data-stu-id="445e0-106">There are also some other restrictions.</span></span> <span data-ttu-id="445e0-107">Si vous souhaitez simplifier les restrictions de mot de passe, vous pouvez le faire dans la classe de démarrage de votre application.</span><span class="sxs-lookup"><span data-stu-id="445e0-107">If you want to simplify password restrictions, you can do that in the startup class of your application.</span></span>

<span data-ttu-id="445e0-108">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]</span><span class="sxs-lookup"><span data-stu-id="445e0-108">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]</span></span>

## <a name="applications-cookie-settings"></a><span data-ttu-id="445e0-109">Paramètres de cookies pour l’application</span><span class="sxs-lookup"><span data-stu-id="445e0-109">Application's cookie settings</span></span>

<span data-ttu-id="445e0-110">Comme la stratégie de mots de passe, tous les paramètres du cookie de l’application peuvent être modifiés dans la classe de démarrage.</span><span class="sxs-lookup"><span data-stu-id="445e0-110">Like the passwords policy, all the settings of the application's cookie can be changed in the startup class.</span></span>

<span data-ttu-id="445e0-111">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]</span><span class="sxs-lookup"><span data-stu-id="445e0-111">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]</span></span>

## <a name="users-lockout"></a><span data-ttu-id="445e0-112">Verrouillage de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="445e0-112">User's lockout</span></span>

<span data-ttu-id="445e0-113">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]</span><span class="sxs-lookup"><span data-stu-id="445e0-113">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]</span></span>

---
title: "Configurer l’identité"
uid: security/authentication/identity-configuration
ms.openlocfilehash: 7ccd89360a8c7f5c8c6dfac76df42898e18a116a
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="configure-identity"></a>Configurer l’identité

Identité de ASP.NET Core a certains comportements par défaut que vous pouvez remplacer facilement dans la classe de démarrage de votre application.

## <a name="passwords-policy"></a>Stratégie de mots de passe

Par défaut, identité requiert que les mots de passe contient un caractère majuscule, minuscule et des chiffres. Il existe également quelques autres restrictions. Si vous souhaitez simplifier les restrictions de mot de passe, vous pouvez le faire dans la classe de démarrage de votre application.

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]

## <a name="applications-cookie-settings"></a>Paramètres de cookies pour l’application

Comme la stratégie de mots de passe, tous les paramètres du cookie de l’application peuvent être modifiés dans la classe de démarrage.

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]

## <a name="users-lockout"></a>Verrouillage de l’utilisateur

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]

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

---
title: "Configurer le type de données d’identité clés primaires"
uid: security/authentication/identity-primary-key-configuration
ms.openlocfilehash: 2afcdf89b2a39d82a4ba72dc780be71ac98ab664
ms.sourcegitcommit: 76d42f09f3e0dd2f2105493eca6b29994aa47706
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2017
---
# <a name="configure-identity-primary-keys-data-type"></a>Configurer le type de données d’identité clés primaires

Identité de ASP.NET Core vous permet de configurer aisément le type de données pour les clés primaires. Par défaut, utilise des identités de type de données chaîne, mais vous pouvez remplacer très rapidement de ce comportement.

## <a name="how-to"></a>Procédure

1.  La première étape consiste à implémenter le modèle de l’identité et de remplacer le type de chaîne avec le type de données.

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4-6&range=7-13)]

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3-5&range=7-12)]
    
2.  Implémenter le contexte de base de données d’identité avec vos modèles et le type de données pour les clés primaires

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]
    
3.  Utilisez vos modèles et le type de données pour les clés primaires lorsque vous déclarez le service d’identité dans la classe de démarrage de votre application

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-79)]

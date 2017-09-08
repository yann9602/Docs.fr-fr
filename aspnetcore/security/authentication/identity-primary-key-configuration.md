---
title: "Configurer le type de données d’identité clés primaires"
uid: security/authentication/identity-primary-key-configuration
ms.openlocfilehash: e6661708d003aa50204e7f79d3070442a3440391
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="configure-identity-primary-keys-data-type"></a>Configurer le type de données d’identité clés primaires

Identité de ASP.NET Core vous permet de configurer aisément le type de données pour les clés primaires. Par défaut, utilise des identités de type de données chaîne, mais vous pouvez remplacer très rapidement de ce comportement.

## <a name="how-to"></a>Procédure

1.  La première étape consiste à implémenter le modèle de l’identité et de remplacer le type de chaîne avec le type de données.

    [!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4-6&range=7-13)]

    [!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3-5&range=7-12)]
    
2.  Implémenter le contexte de base de données d’identité avec vos modèles et le type de données pour les clés primaires

    [!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]
    
3.  Utilisez vos modèles et le type de données pour les clés primaires lorsque vous déclarez le service d’identité dans la classe de démarrage de votre application

    [!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-79)]

---
title: "Configurer le type de données d’identité clés primaires"
uid: security/authentication/identity-primary-key-configuration
ms.openlocfilehash: 2afcdf89b2a39d82a4ba72dc780be71ac98ab664
ms.sourcegitcommit: 76d42f09f3e0dd2f2105493eca6b29994aa47706
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2017
---
# <a name="configure-identity-primary-keys-data-type"></a><span data-ttu-id="458a6-102">Configurer le type de données d’identité clés primaires</span><span class="sxs-lookup"><span data-stu-id="458a6-102">Configure Identity primary keys data type</span></span>

<span data-ttu-id="458a6-103">Identité de ASP.NET Core vous permet de configurer aisément le type de données pour les clés primaires.</span><span class="sxs-lookup"><span data-stu-id="458a6-103">ASP.NET Core Identity allows you to easily configure the data type you want for the primary keys.</span></span> <span data-ttu-id="458a6-104">Par défaut, utilise des identités de type de données chaîne, mais vous pouvez remplacer très rapidement de ce comportement.</span><span class="sxs-lookup"><span data-stu-id="458a6-104">By default, Identity uses string data type but you can very quickly override this behavior.</span></span>

## <a name="how-to"></a><span data-ttu-id="458a6-105">Procédure</span><span class="sxs-lookup"><span data-stu-id="458a6-105">How to</span></span>

1.  <span data-ttu-id="458a6-106">La première étape consiste à implémenter le modèle de l’identité et de remplacer le type de chaîne avec le type de données.</span><span class="sxs-lookup"><span data-stu-id="458a6-106">The first step is to implement the Identity's model, and override the string type with the data type you want.</span></span>

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4-6&range=7-13)]

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3-5&range=7-12)]
    
2.  <span data-ttu-id="458a6-107">Implémenter le contexte de base de données d’identité avec vos modèles et le type de données pour les clés primaires</span><span class="sxs-lookup"><span data-stu-id="458a6-107">Implement the database context of Identity with your models and the data type you want for primary keys</span></span>

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]
    
3.  <span data-ttu-id="458a6-108">Utilisez vos modèles et le type de données pour les clés primaires lorsque vous déclarez le service d’identité dans la classe de démarrage de votre application</span><span class="sxs-lookup"><span data-stu-id="458a6-108">Use your models and the data type you want for primary keys when you declare the identity service in your application's startup class</span></span>

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-79)]

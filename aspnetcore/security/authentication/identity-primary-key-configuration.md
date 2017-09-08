---
title: "Configurer le type de données d’identité clés primaires"
uid: security/authentication/identity-primary-key-configuration
ms.openlocfilehash: e6661708d003aa50204e7f79d3070442a3440391
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="configure-identity-primary-keys-data-type"></a><span data-ttu-id="647ed-102">Configurer le type de données d’identité clés primaires</span><span class="sxs-lookup"><span data-stu-id="647ed-102">Configure Identity primary keys data type</span></span>

<span data-ttu-id="647ed-103">Identité de ASP.NET Core vous permet de configurer aisément le type de données pour les clés primaires.</span><span class="sxs-lookup"><span data-stu-id="647ed-103">ASP.NET Core Identity allows you to easily configure the data type you want for the primary keys.</span></span> <span data-ttu-id="647ed-104">Par défaut, utilise des identités de type de données chaîne, mais vous pouvez remplacer très rapidement de ce comportement.</span><span class="sxs-lookup"><span data-stu-id="647ed-104">By default, Identity uses string data type but you can very quickly override this behavior.</span></span>

## <a name="how-to"></a><span data-ttu-id="647ed-105">Procédure</span><span class="sxs-lookup"><span data-stu-id="647ed-105">How to</span></span>

1.  <span data-ttu-id="647ed-106">La première étape consiste à implémenter le modèle de l’identité et de remplacer le type de chaîne avec le type de données.</span><span class="sxs-lookup"><span data-stu-id="647ed-106">The first step is to implement the Identity's model, and override the string type with the data type you want.</span></span>

    <span data-ttu-id="647ed-107">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4-6&range=7-13)]</span><span class="sxs-lookup"><span data-stu-id="647ed-107">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4-6&range=7-13)]</span></span>

    <span data-ttu-id="647ed-108">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3-5&range=7-12)]</span><span class="sxs-lookup"><span data-stu-id="647ed-108">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3-5&range=7-12)]</span></span>
    
2.  <span data-ttu-id="647ed-109">Implémenter le contexte de base de données d’identité avec vos modèles et le type de données pour les clés primaires</span><span class="sxs-lookup"><span data-stu-id="647ed-109">Implement the database context of Identity with your models and the data type you want for primary keys</span></span>

    <span data-ttu-id="647ed-110">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]</span><span class="sxs-lookup"><span data-stu-id="647ed-110">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]</span></span>
    
3.  <span data-ttu-id="647ed-111">Utilisez vos modèles et le type de données pour les clés primaires lorsque vous déclarez le service d’identité dans la classe de démarrage de votre application</span><span class="sxs-lookup"><span data-stu-id="647ed-111">Use your models and the data type you want for primary keys when you declare the identity service in your application's startup class</span></span>

    <span data-ttu-id="647ed-112">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-79)]</span><span class="sxs-lookup"><span data-stu-id="647ed-112">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-79)]</span></span>

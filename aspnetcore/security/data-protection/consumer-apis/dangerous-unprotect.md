---
title: "Ôter la protection de charges utiles dont les clés ont été révoqués."
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 6c4e6591-45d2-4d25-855e-062ad352d648
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 44f21f380b994f46a8bb7368bca0cfc6e438ec4d
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="unprotecting-payloads-whose-keys-have-been-revoked"></a><span data-ttu-id="df812-103">Ôter la protection de charges utiles dont les clés ont été révoqués.</span><span class="sxs-lookup"><span data-stu-id="df812-103">Unprotecting payloads whose keys have been revoked</span></span>

<a name=data-protection-consumer-apis-dangerous-unprotect></a>

<span data-ttu-id="df812-104">L’API de protection des données ASP.NET Core ne sont pas destinées principalement pour la persistance indéfini de charges utiles confidentielles.</span><span class="sxs-lookup"><span data-stu-id="df812-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="df812-105">Autres technologies telles que [DPAPI CNG de Windows](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) et [Azure Rights Management](https://technet.microsoft.com/library/jj585024.aspx) les plus adaptés au scénario de stockage illimité, et ils ont des fonctionnalités de gestion de clé forte en conséquence.</span><span class="sxs-lookup"><span data-stu-id="df812-105">Other technologies like [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) and [Azure Rights Management](https://technet.microsoft.com/library/jj585024.aspx) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="df812-106">Ceci dit, rien interdire à un développeur à l’aide de l’API de protection des données ASP.NET Core pour la protection à long terme des données confidentielles.</span><span class="sxs-lookup"><span data-stu-id="df812-106">That said, there is nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="df812-107">Les clés ne sont jamais supprimés à partir de l’anneau de clé, afin de IDataProtector.Unprotect peut toujours de récupérer des charges utiles existants tant que les clés sont disponibles et valides.</span><span class="sxs-lookup"><span data-stu-id="df812-107">Keys are never removed from the key ring, so IDataProtector.Unprotect can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="df812-108">Toutefois, un problème survient lorsque le développeur tente d’ôter la protection des données qui a été protégées avec une clé révoquée, comme IDataProtector.Unprotect lève une exception dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="df812-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as IDataProtector.Unprotect will throw an exception in this case.</span></span> <span data-ttu-id="df812-109">Cela peut être préci pour les charges temporaires (par exemple, des jetons d’authentification), car ces types de charges utiles peuvent facilement être recréés par le système, et au pire le visiteur du site peut être requise pour vous reconnecter.</span><span class="sxs-lookup"><span data-stu-id="df812-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="df812-110">Mais pour les charges persistantes, ayant Unprotect lever peut entraîner une perte de données inacceptable.</span><span class="sxs-lookup"><span data-stu-id="df812-110">But for persisted payloads, having Unprotect throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="df812-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="df812-111">IPersistedDataProtector</span></span>

<span data-ttu-id="df812-112">Pour prendre en charge le scénario de permettre à des charges utiles d’être protégé, même en cas de clés révoqués, le système de protection des données contient un type de IPersistedDataProtector.</span><span class="sxs-lookup"><span data-stu-id="df812-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an IPersistedDataProtector type.</span></span> <span data-ttu-id="df812-113">Pour obtenir une instance de IPersistedDataProtector, simplement obtenir une instance de IDataProtector normalement et essayez de convertir le IDataProtector à IPersistedDataProtector.</span><span class="sxs-lookup"><span data-stu-id="df812-113">To get an instance of IPersistedDataProtector, simply get an instance of IDataProtector in the normal fashion and try casting the IDataProtector to IPersistedDataProtector.</span></span>

> [!NOTE]
> <span data-ttu-id="df812-114">Pas de toutes les instances de IDataProtector peuvent être converties en IPersistedDataProtector.</span><span class="sxs-lookup"><span data-stu-id="df812-114">Not all IDataProtector instances can be cast to IPersistedDataProtector.</span></span> <span data-ttu-id="df812-115">Les développeurs doivent utiliser le langage c# en tant qu’opérateur ou préparé similaire pour éviter les exceptions du runtime due à des casts non valides, et qu’ils doivent disposer gérer le cas de défaillance de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="df812-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="df812-116">IPersistedDataProtector expose la surface API suivante :</span><span class="sxs-lookup"><span data-stu-id="df812-116">IPersistedDataProtector exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
   ```

<span data-ttu-id="df812-117">Cette API prend la charge utile de protégé (en tant que tableau d’octets) et retourne la charge utile non protégée.</span><span class="sxs-lookup"><span data-stu-id="df812-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="df812-118">Il n’existe aucune surcharge basé sur chaîne.</span><span class="sxs-lookup"><span data-stu-id="df812-118">There is no string-based overload.</span></span> <span data-ttu-id="df812-119">Les deux paramètres de sortie sont les suivantes.</span><span class="sxs-lookup"><span data-stu-id="df812-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="df812-120">requiresMigration : sera être possède la valeur true si la clé utilisée pour protéger cette charge utile n’est plus la clé active par défaut, par exemple, la clé utilisée pour protéger cette charge utile est ancien et une clé d’opération de restauration a eu lieu.</span><span class="sxs-lookup"><span data-stu-id="df812-120">requiresMigration: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="df812-121">L’appelant peut souhaiter prendre en compte la reprotection de la charge utile selon leurs besoins.</span><span class="sxs-lookup"><span data-stu-id="df812-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="df812-122">wasRevoked : valeur de la valeur true si la clé utilisée pour protéger cette charge utile a été révoquée.</span><span class="sxs-lookup"><span data-stu-id="df812-122">wasRevoked: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="df812-123">Soyez extrêmement prudent lorsque vous passez ignoreRevocationErrors : true pour la méthode DangerousUnprotect.</span><span class="sxs-lookup"><span data-stu-id="df812-123">Exercise extreme caution when passing ignoreRevocationErrors: true to the DangerousUnprotect method.</span></span> <span data-ttu-id="df812-124">Si après avoir appelé cette méthode, la valeur de wasRevoked est true, puis la clé utilisée pour protéger cette charge utile a été révoquée et authenticité de la charge utile doit être traitée comme étant suspecte.</span><span class="sxs-lookup"><span data-stu-id="df812-124">If after calling this method the wasRevoked value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="df812-125">Dans ce cas uniquement fonctionner sur la charge utile non protégée si vous avez une garantie distincte qu’il est authentique, par exemple, qu’il le provenant d’une base de données sécurisée, plutôt que d’envoyées par un client web non fiable.</span><span class="sxs-lookup"><span data-stu-id="df812-125">In this case only continue operating on the unprotected payload if you have some separate assurance that it is authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

<span data-ttu-id="df812-126">[!code-none[Main](dangerous-unprotect/samples/dangerous-unprotect.cs)]</span><span class="sxs-lookup"><span data-stu-id="df812-126">[!code-none[Main](dangerous-unprotect/samples/dangerous-unprotect.cs)]</span></span>

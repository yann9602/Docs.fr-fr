---
title: "Stratégie ordinateur à l’échelle"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 285ae47d-e0bf-4b03-b0a8-2b1fb18bc3a1
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: fde8f75422c9dd84311a65b21e1e38b47fbe0306
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="machine-wide-policy"></a><span data-ttu-id="377db-103">Stratégie ordinateur à l’échelle</span><span class="sxs-lookup"><span data-stu-id="377db-103">Machine Wide Policy</span></span>

<a name="data-protection-configuration-machinewidepolicy"></a>

<span data-ttu-id="377db-104">Lors de l’exécution sur Windows, le système de protection de données prend en charge limitée pour définir une stratégie d’ordinateur à l’échelle par défaut pour toutes les applications qui utilisent la protection des données.</span><span class="sxs-lookup"><span data-stu-id="377db-104">When running on Windows, the data protection system has limited support for setting default machine-wide policy for all applications which consume data protection.</span></span> <span data-ttu-id="377db-105">L’idée générale est qu’un administrateur peut souhaiter de modifier certains paramètres par défaut (par exemple, les algorithmes utilisés ou clé durée de vie) sans avoir à mettre à jour manuellement toutes les applications sur l’ordinateur.</span><span class="sxs-lookup"><span data-stu-id="377db-105">The general idea is that an administrator might wish to change some default setting (such as algorithms used or key lifetime) without needing to manually update every application on the machine.</span></span>

>[!WARNING]
> <span data-ttu-id="377db-106">L’administrateur système peut définir la stratégie par défaut, mais ils ne peuvent pas l’appliquer.</span><span class="sxs-lookup"><span data-stu-id="377db-106">The system administrator can set default policy, but they cannot enforce it.</span></span> <span data-ttu-id="377db-107">Le développeur d’applications peut toujours remplacer n’importe quelle valeur avec l’un de leurs propres choix.</span><span class="sxs-lookup"><span data-stu-id="377db-107">The application developer can always override any value with one of their own choosing.</span></span> <span data-ttu-id="377db-108">La stratégie par défaut affecte uniquement les applications où le développeur n’a pas spécifié une valeur explicite pour un paramètre particulier.</span><span class="sxs-lookup"><span data-stu-id="377db-108">The default policy only affects applications where the developer has not specified an explicit value for some particular setting.</span></span>

## <a name="setting-default-policy"></a><span data-ttu-id="377db-109">Définition de la stratégie par défaut</span><span class="sxs-lookup"><span data-stu-id="377db-109">Setting default policy</span></span>

<span data-ttu-id="377db-110">Pour définir la stratégie par défaut, un administrateur peut définir les valeurs connues dans le Registre système sous la clé suivante.</span><span class="sxs-lookup"><span data-stu-id="377db-110">To set default policy, an administrator can set known values in the system registry under the following key.</span></span>

<span data-ttu-id="377db-111">La clé de Registre :`HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection`</span><span class="sxs-lookup"><span data-stu-id="377db-111">Reg key: `HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection`</span></span>

<span data-ttu-id="377db-112">Si vous êtes sur un système d’exploitation de 64 bits et que vous souhaitez affecter le comportement des applications 32 bits, vous devez également configurer l’équivalent Wow6432Node de la clé ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="377db-112">If you're on a 64-bit operating system and want to affect the behavior of 32-bit applications, remember also to configure the Wow6432Node equivalent of the above key.</span></span>

<span data-ttu-id="377db-113">Les valeurs prises en charge sont :</span><span class="sxs-lookup"><span data-stu-id="377db-113">The supported values are:</span></span>

* <span data-ttu-id="377db-114">EncryptionType [chaîne] - Spécifie les algorithmes qui doivent être utilisés pour la protection des données.</span><span class="sxs-lookup"><span data-stu-id="377db-114">EncryptionType [string] - specifies which algorithms should be used for data protection.</span></span> <span data-ttu-id="377db-115">Cette valeur doit être « CNG-CBC », « CNG-GCM » ou « Géré » et est décrite plus en détail [ci-dessous](#data-protection-encryption-types).</span><span class="sxs-lookup"><span data-stu-id="377db-115">This value must be "CNG-CBC", "CNG-GCM", or "Managed" and is described in more detail [below](#data-protection-encryption-types).</span></span>

* <span data-ttu-id="377db-116">DefaultKeyLifetime [DWORD] - Spécifie la durée de vie des clés qui vient d’être générées.</span><span class="sxs-lookup"><span data-stu-id="377db-116">DefaultKeyLifetime [DWORD] - specifies the lifetime for newly-generated keys.</span></span> <span data-ttu-id="377db-117">Cette valeur est spécifiée en jours et doit être ≥ 7.</span><span class="sxs-lookup"><span data-stu-id="377db-117">This value is specified in days and must be ≥ 7.</span></span>

* <span data-ttu-id="377db-118">KeyEscrowSinks [chaîne] - Spécifie les types qui seront utilisés pour le dépôt de clé.</span><span class="sxs-lookup"><span data-stu-id="377db-118">KeyEscrowSinks [string] - specifies the types which will be used for key escrow.</span></span> <span data-ttu-id="377db-119">Cette valeur est une liste délimitée par des points-virgules de récepteurs de clé (Key escrow), où chaque élément dans la liste est le nom qualifié d’assembly d’un type qui implémente IKeyEscrowSink.</span><span class="sxs-lookup"><span data-stu-id="377db-119">This value is a semicolon-delimited list of key escrow sinks, where each element in the list is the assembly qualified name of a type which implements IKeyEscrowSink.</span></span>

<a name="data-protection-encryption-types"></a>

### <a name="encryption-types"></a><span data-ttu-id="377db-120">Types de chiffrement</span><span class="sxs-lookup"><span data-stu-id="377db-120">Encryption types</span></span>

<span data-ttu-id="377db-121">Si EncryptionType est « CNG-CBC », le système sera être configuré pour utiliser un chiffrement par blocs symétrique en mode CBC à la confidentialité et HMAC authenticité avec les services fournis par CNG de Windows (voir [spécifiant les algorithmes CNG de Windows personnalisés](overview.md#data-protection-changing-algorithms-cng)pour plus d’informations).</span><span class="sxs-lookup"><span data-stu-id="377db-121">If EncryptionType is "CNG-CBC", the system will be configured to use a CBC-mode symmetric block cipher for confidentiality and HMAC for authenticity with services provided by Windows CNG (see [Specifying custom Windows CNG algorithms](overview.md#data-protection-changing-algorithms-cng) for more details).</span></span> <span data-ttu-id="377db-122">Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété sur le type CngCbcAuthenticatedEncryptionSettings :</span><span class="sxs-lookup"><span data-stu-id="377db-122">The following additional values are supported, each of which corresponds to a property on the CngCbcAuthenticatedEncryptionSettings type:</span></span>

* <span data-ttu-id="377db-123">EncryptionAlgorithm [chaîne] - le nom d’un algorithme de chiffrement symétrique compris par CNG.</span><span class="sxs-lookup"><span data-stu-id="377db-123">EncryptionAlgorithm [string] - the name of a symmetric block cipher algorithm understood by CNG.</span></span> <span data-ttu-id="377db-124">Cet algorithme est ouverte en mode CBC.</span><span class="sxs-lookup"><span data-stu-id="377db-124">This algorithm will be opened in CBC mode.</span></span>

* <span data-ttu-id="377db-125">EncryptionAlgorithmProvider [chaîne] - le nom de l’implémentation du fournisseur CNG qui peut produire l’algorithme EncryptionAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="377db-125">EncryptionAlgorithmProvider [string] - the name of the CNG provider implementation which can produce the algorithm EncryptionAlgorithm.</span></span>

* <span data-ttu-id="377db-126">EncryptionAlgorithmKeySize [DWORD] - la longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement symétrique.</span><span class="sxs-lookup"><span data-stu-id="377db-126">EncryptionAlgorithmKeySize [DWORD] - the length (in bits) of the key to derive for the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="377db-127">HashAlgorithm [chaîne] - le nom d’un algorithme de hachage compris par CNG.</span><span class="sxs-lookup"><span data-stu-id="377db-127">HashAlgorithm [string] - the name of a hash algorithm understood by CNG.</span></span> <span data-ttu-id="377db-128">Cet algorithme est ouverte en mode HMAC.</span><span class="sxs-lookup"><span data-stu-id="377db-128">This algorithm will be opened in HMAC mode.</span></span>

* <span data-ttu-id="377db-129">HashAlgorithmProvider [chaîne] - le nom de l’implémentation du fournisseur CNG qui peut produire l’algorithme HashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="377db-129">HashAlgorithmProvider [string] - the name of the CNG provider implementation which can produce the algorithm HashAlgorithm.</span></span>

<span data-ttu-id="377db-130">Si EncryptionType est « CNG-GCM », le système sera être configuré pour utiliser un chiffrement par blocs symétrique Mode Galois/compteur pour la confidentialité et l’authenticité avec les services fournis par CNG de Windows (voir [spécifiant les algorithmes CNG de Windows personnalisés](overview.md#data-protection-changing-algorithms-cng)pour plus d’informations).</span><span class="sxs-lookup"><span data-stu-id="377db-130">If EncryptionType is "CNG-GCM", the system will be configured to use a Galois/Counter Mode symmetric block cipher for confidentiality and authenticity with services provided by Windows CNG (see [Specifying custom Windows CNG algorithms](overview.md#data-protection-changing-algorithms-cng) for more details).</span></span> <span data-ttu-id="377db-131">Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété sur le type CngGcmAuthenticatedEncryptionSettings :</span><span class="sxs-lookup"><span data-stu-id="377db-131">The following additional values are supported, each of which corresponds to a property on the CngGcmAuthenticatedEncryptionSettings type:</span></span>

* <span data-ttu-id="377db-132">EncryptionAlgorithm [chaîne] - le nom d’un algorithme de chiffrement symétrique compris par CNG.</span><span class="sxs-lookup"><span data-stu-id="377db-132">EncryptionAlgorithm [string] - the name of a symmetric block cipher algorithm understood by CNG.</span></span> <span data-ttu-id="377db-133">Cet algorithme est ouverte en Mode de compteur/Galois.</span><span class="sxs-lookup"><span data-stu-id="377db-133">This algorithm will be opened in Galois/Counter Mode.</span></span>

* <span data-ttu-id="377db-134">EncryptionAlgorithmProvider [chaîne] - le nom de l’implémentation du fournisseur CNG qui peut produire l’algorithme EncryptionAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="377db-134">EncryptionAlgorithmProvider [string] - the name of the CNG provider implementation which can produce the algorithm EncryptionAlgorithm.</span></span>

* <span data-ttu-id="377db-135">EncryptionAlgorithmKeySize [DWORD] - la longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement symétrique.</span><span class="sxs-lookup"><span data-stu-id="377db-135">EncryptionAlgorithmKeySize [DWORD] - the length (in bits) of the key to derive for the symmetric block cipher algorithm.</span></span>

<span data-ttu-id="377db-136">Si EncryptionType est « géré », le système sera être configuré pour utiliser un SymmetricAlgorithm managé pour la confidentialité et KeyedHashAlgorithm authenticité (voir [spécification personnalisé géré algorithmes](overview.md#data-protection-changing-algorithms-custom-managed) pour plus d’informations).</span><span class="sxs-lookup"><span data-stu-id="377db-136">If EncryptionType is "Managed", the system will be configured to use a managed SymmetricAlgorithm for confidentiality and KeyedHashAlgorithm for authenticity (see [Specifying custom managed algorithms](overview.md#data-protection-changing-algorithms-custom-managed) for more details).</span></span> <span data-ttu-id="377db-137">Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété sur le type ManagedAuthenticatedEncryptionSettings :</span><span class="sxs-lookup"><span data-stu-id="377db-137">The following additional values are supported, each of which corresponds to a property on the ManagedAuthenticatedEncryptionSettings type:</span></span>

* <span data-ttu-id="377db-138">EncryptionAlgorithmType [chaîne] - le nom qualifié d’assembly d’un type qui implémente SymmetricAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="377db-138">EncryptionAlgorithmType [string] - the assembly-qualified name of a type which implements SymmetricAlgorithm.</span></span>

* <span data-ttu-id="377db-139">EncryptionAlgorithmKeySize [DWORD] - la longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement symétrique.</span><span class="sxs-lookup"><span data-stu-id="377db-139">EncryptionAlgorithmKeySize [DWORD] - the length (in bits) of the key to derive for the symmetric encryption algorithm.</span></span>

* <span data-ttu-id="377db-140">ValidationAlgorithmType [chaîne] - le nom qualifié d’assembly d’un type qui implémente l’élément KeyedHashAlgorithm impossible.</span><span class="sxs-lookup"><span data-stu-id="377db-140">ValidationAlgorithmType [string] - the assembly-qualified name of a type which implements KeyedHashAlgorithm.</span></span>

<span data-ttu-id="377db-141">Si EncryptionType a toute autre valeur (autres que null / vide), le système de protection de données lève une exception au démarrage.</span><span class="sxs-lookup"><span data-stu-id="377db-141">If EncryptionType has any other value (other than null / empty), the data protection system will throw an exception at startup.</span></span>

>[!WARNING]
> <span data-ttu-id="377db-142">Lorsque vous configurez un paramètre de stratégie par défaut qui implique des noms de type (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), les types doivent être accessibles à l’application.</span><span class="sxs-lookup"><span data-stu-id="377db-142">When configuring a default policy setting that involves type names (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), the types must be available to the application.</span></span> <span data-ttu-id="377db-143">Dans la pratique, cela signifie que pour les applications qui s’exécutent sur le CLR de bureau, les assemblys qui contiennent ces types doivent être GACed.</span><span class="sxs-lookup"><span data-stu-id="377db-143">In practice, this means that for applications running on Desktop CLR, the assemblies which contain these types should be GACed.</span></span> <span data-ttu-id="377db-144">Pour les applications ASP.NET Core s’exécutant sur [.NET Core](https://www.microsoft.com/net/core), les packages qui contiennent ces types doivent être installés.</span><span class="sxs-lookup"><span data-stu-id="377db-144">For ASP.NET Core applications running on [.NET Core](https://www.microsoft.com/net/core), the packages which contain these types should be installed.</span></span>

---
title: "Règle de Protection des données à l’échelle de l’ordinateur prend en charge dans ASP.NET Core"
author: rick-anderson
description: "En savoir plus sur la prise en charge pour la définition d’une stratégie d’ordinateur à l’échelle par défaut pour toutes les applications qui utilisent la Protection des données ASP.NET Core."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 285ae47d-e0bf-4b03-b0a8-2b1fb18bc3a1
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 692e120f13882be594afc5fb926b96b82d9609e2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Règle de Protection des données à l’échelle de l’ordinateur prend en charge dans ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Lors de l’exécution sur Windows, le système de Protection des données prend en charge limitée pour la définition d’une stratégie d’ordinateur à l’échelle par défaut pour toutes les applications qui utilisent la Protection des données ASP.NET Core. L’idée générale est qu’un administrateur peut souhaiter de modifier un paramètre par défaut, tels que les algorithmes utilisés ou la durée de vie, sans avoir besoin de mettre à jour manuellement chaque application sur l’ordinateur.

> [!WARNING]
> L’administrateur système peut définir la stratégie par défaut, mais ils ne peuvent pas l’appliquer. Le développeur d’application peut toujours remplacer n’importe quelle valeur avec l’un de leurs propres choix. La stratégie par défaut affecte uniquement les applications où le développeur n’a pas encore spécifié une valeur explicite pour un paramètre.

## <a name="setting-default-policy"></a>Définition de la stratégie par défaut

Pour définir la stratégie par défaut, un administrateur peut définir les valeurs connues dans le Registre système sous la clé de Registre suivante :

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

Si vous êtes sur un système d’exploitation de 64 bits et que vous souhaitez affecter le comportement des applications 32 bits, n’oubliez pas de configurer l’équivalent Wow6432Node de la clé ci-dessus.

Les valeurs prises en charge sont indiquées ci-dessous.

| Valeur              | Type   | Description |
| ------------------ | :----: | ----------- |
| EncryptionType     | chaîne | Spécifie les algorithmes qui doivent être utilisés pour la protection des données. La valeur doit être CNG-CBC, GCM-CNG ou géré et est décrite plus en détail ci-dessous. |
| DefaultKeyLifetime | DWORD  | Spécifie la durée de vie des clés qui vient d’être générées. La valeur est spécifiée en jours et doit être > = 7. |
| KeyEscrowSinks     | chaîne | Spécifie les types qui sont utilisés pour le dépôt de clé. La valeur est une liste délimitée par des points-virgules de récepteurs de clé (Key escrow), où chaque élément dans la liste est le nom qualifié d’assembly d’un type qui implémente [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink). |

## <a name="encryption-types"></a>Types de chiffrement

Si EncryptionType est CNG-CBC, le système est configuré pour utiliser un chiffrement par blocs symétrique en mode CBC à la confidentialité et HMAC authenticité avec les services fournis par CNG de Windows (voir [spécifiant les algorithmes CNG de Windows personnalisés](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) pour plus de détails.) Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété sur le type CngCbcAuthenticatedEncryptionSettings.

| Valeur                       | Type   | Description |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | chaîne | Le nom d’un algorithme de chiffrement symétrique compris par CNG. Cet algorithme est ouvert en mode CBC. |
| EncryptionAlgorithmProvider | chaîne | Le nom de l’implémentation du fournisseur CNG qui permet de créer l’algorithme EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | La longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement symétrique. |
| Élément HashAlgorithm Impossible               | chaîne | Le nom d’un algorithme de hachage compris par CNG. Cet algorithme est ouvert en mode HMAC. |
| HashAlgorithmProvider       | chaîne | Le nom de l’implémentation du fournisseur CNG qui permet de créer l’algorithme HashAlgorithm. |

Si EncryptionType est CNG-GCM, le système est configuré pour utiliser un chiffrement par blocs symétrique Mode Galois/compteur pour la confidentialité et l’authenticité avec les services fournis par CNG de Windows (voir [spécifiant les algorithmes CNG de Windows personnalisés](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) Pour plus d’informations). Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété sur le type CngGcmAuthenticatedEncryptionSettings.

| Valeur                       | Type   | Description |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | chaîne | Le nom d’un algorithme de chiffrement symétrique compris par CNG. Cet algorithme est ouvert en Mode de compteur/Galois. |
| EncryptionAlgorithmProvider | chaîne | Le nom de l’implémentation du fournisseur CNG qui permet de créer l’algorithme EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | La longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement symétrique. |

Si EncryptionType est géré, le système est configuré pour utiliser un SymmetricAlgorithm managé pour la confidentialité et KeyedHashAlgorithm authenticité (voir [spécification personnalisé géré algorithmes](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) pour plus d’informations). Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété sur le type ManagedAuthenticatedEncryptionSettings.

| Valeur                      | Type   | Description |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | chaîne | Le nom qualifié d’assembly d’un type qui implémente SymmetricAlgorithm. |
| EncryptionAlgorithmKeySize | DWORD  | La longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement symétrique. |
| ValidationAlgorithmType    | chaîne | Le nom qualifié d’assembly d’un type qui implémente l’élément KeyedHashAlgorithm impossible. |

Si EncryptionType a toute autre valeur différente de null ou vide, le système de Protection de données lève une exception au démarrage.

> [!WARNING]
> Lorsque vous configurez un paramètre de stratégie par défaut qui implique des noms de type (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), les types doivent être disponibles pour l’application. Cela signifie que pour les applications qui s’exécutent sur le CLR de bureau, les assemblys qui contiennent ces types doivent figurer dans le Global Assembly Cache (GAC). Pour les applications ASP.NET Core s’exécutant sur [.NET Core](https://www.microsoft.com/net/core), les packages qui contiennent ces types doivent être installés.

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
ms.openlocfilehash: 513726e209401d158ac98d5874942765751ac07d
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="machine-wide-policy"></a>Stratégie ordinateur à l’échelle

<a name=data-protection-configuration-machinewidepolicy></a>

Lors de l’exécution sur Windows, le système de protection de données prend en charge limitée pour définir une stratégie d’ordinateur à l’échelle par défaut pour toutes les applications qui utilisent la protection des données. L’idée générale est qu’un administrateur peut souhaiter de modifier certains paramètres par défaut (par exemple, les algorithmes utilisés ou clé durée de vie) sans avoir à mettre à jour manuellement toutes les applications sur l’ordinateur.

>[!WARNING]
> L’administrateur système peut définir la stratégie par défaut, mais ils ne peuvent pas l’appliquer. Le développeur d’applications peut toujours remplacer n’importe quelle valeur avec l’un de leurs propres choix. La stratégie par défaut affecte uniquement les applications où le développeur n’a pas spécifié une valeur explicite pour un paramètre particulier.

## <a name="setting-default-policy"></a>Définition de la stratégie par défaut

Pour définir la stratégie par défaut, un administrateur peut définir les valeurs connues dans le Registre système sous la clé suivante.

La clé de Registre :`HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection`

Si vous êtes sur un système d’exploitation de 64 bits et que vous souhaitez affecter le comportement des applications 32 bits, vous devez également configurer l’équivalent Wow6432Node de la clé ci-dessus.

Les valeurs prises en charge sont :

* EncryptionType [chaîne] - Spécifie les algorithmes qui doivent être utilisés pour la protection des données. Cette valeur doit être « CNG-CBC », « CNG-GCM » ou « Géré » et est décrite plus en détail [ci-dessous](#data-protection-encryption-types).

* DefaultKeyLifetime [DWORD] - Spécifie la durée de vie des clés qui vient d’être générées. Cette valeur est spécifiée en jours et doit être ≥ 7.

* KeyEscrowSinks [chaîne] - Spécifie les types qui seront utilisés pour le dépôt de clé. Cette valeur est une liste délimitée par des points-virgules de récepteurs de clé (Key escrow), où chaque élément dans la liste est le nom qualifié d’assembly d’un type qui implémente IKeyEscrowSink.

<a name=data-protection-encryption-types></a>

### <a name="encryption-types"></a>Types de chiffrement

Si EncryptionType est « CNG-CBC », le système sera être configuré pour utiliser un chiffrement par blocs symétrique en mode CBC à la confidentialité et HMAC authenticité avec les services fournis par CNG de Windows (voir [spécifiant les algorithmes CNG de Windows personnalisés](overview.md#data-protection-changing-algorithms-cng)pour plus d’informations). Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété sur le type CngCbcAuthenticatedEncryptionSettings :

* EncryptionAlgorithm [chaîne] - le nom d’un algorithme de chiffrement symétrique compris par CNG. Cet algorithme est ouverte en mode CBC.

* EncryptionAlgorithmProvider [chaîne] - le nom de l’implémentation du fournisseur CNG qui peut produire l’algorithme EncryptionAlgorithm.

* EncryptionAlgorithmKeySize [DWORD] - la longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement symétrique.

* HashAlgorithm [chaîne] - le nom d’un algorithme de hachage compris par CNG. Cet algorithme est ouverte en mode HMAC.

* HashAlgorithmProvider [chaîne] - le nom de l’implémentation du fournisseur CNG qui peut produire l’algorithme HashAlgorithm.

Si EncryptionType est « CNG-GCM », le système sera être configuré pour utiliser un chiffrement par blocs symétrique Mode Galois/compteur pour la confidentialité et l’authenticité avec les services fournis par CNG de Windows (voir [spécifiant les algorithmes CNG de Windows personnalisés](overview.md#data-protection-changing-algorithms-cng)pour plus d’informations). Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété sur le type CngGcmAuthenticatedEncryptionSettings :

* EncryptionAlgorithm [chaîne] - le nom d’un algorithme de chiffrement symétrique compris par CNG. Cet algorithme est ouverte en Mode de compteur/Galois.

* EncryptionAlgorithmProvider [chaîne] - le nom de l’implémentation du fournisseur CNG qui peut produire l’algorithme EncryptionAlgorithm.

* EncryptionAlgorithmKeySize [DWORD] - la longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement symétrique.

Si EncryptionType est « géré », le système sera être configuré pour utiliser un SymmetricAlgorithm managé pour la confidentialité et KeyedHashAlgorithm authenticité (voir [spécification personnalisé géré algorithmes](overview.md#data-protection-changing-algorithms-custom-managed) pour plus d’informations). Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété sur le type ManagedAuthenticatedEncryptionSettings :

* EncryptionAlgorithmType [chaîne] - le nom qualifié d’assembly d’un type qui implémente SymmetricAlgorithm.

* EncryptionAlgorithmKeySize [DWORD] - la longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement symétrique.

* ValidationAlgorithmType [chaîne] - le nom qualifié d’assembly d’un type qui implémente l’élément KeyedHashAlgorithm impossible.

Si EncryptionType a toute autre valeur (autres que null / vide), le système de protection de données lève une exception au démarrage.

>[!WARNING]
> Lorsque vous configurez un paramètre de stratégie par défaut qui implique des noms de type (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), les types doivent être accessibles à l’application. Dans la pratique, cela signifie que pour les applications qui s’exécutent sur le CLR de bureau, les assemblys qui contiennent ces types doivent être GACed. Pour les applications ASP.NET Core s’exécutant sur [.NET Core](https://microsoft.com/net/core), les packages qui contiennent ces types doivent être installés.

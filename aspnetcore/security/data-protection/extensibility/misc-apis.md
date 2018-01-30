---
title: API diverses
author: rick-anderson
description: "Ce document décrit l’interface de ISecret de protection de données ASP.NET Core."
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 26474de3a1c681a8c7f8b7812e9ef859f5d448bb
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="miscellaneous-apis"></a>API diverses

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Les types qui implémentent des interfaces suivantes doivent être thread-safe pour les appelants plusieurs.

## <a name="isecret"></a>ISecret

Le `ISecret` interface représente une valeur secrète, tels que du matériel de clé de chiffrement. Elle contient la surface API suivante :

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

Le `WriteSecretIntoBuffer` méthode remplit la mémoire tampon fournie avec la valeur brute de secret principal. La raison pour laquelle cette API prend la mémoire tampon en tant que paramètre plutôt que de retourner un `byte[]` directement est que cela permet l’appelant pour épingler l’objet de la mémoire tampon, limiter l’exposition de secret principal pour le RÉCUPÉRATEUR de mémoire managée.

Le `Secret` type est une implémentation concrète de `ISecret` où la valeur secrète est stockée dans la mémoire d’en cours. Sur les plateformes Windows, la valeur de clé secrète est chiffrée [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).

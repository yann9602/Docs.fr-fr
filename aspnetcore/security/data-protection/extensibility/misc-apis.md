---
title: API diverses
author: rick-anderson
description: "Ce document décrit l’interface de ISecret de protection de données ASP.NET Core."
keywords: "ASP.NET Core, protection des données, ISecret"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 512c6ba7-88ec-47e4-a656-6b30350b34e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: f5d6920f9f229bd480a76c952dab30efb7d9eff5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
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

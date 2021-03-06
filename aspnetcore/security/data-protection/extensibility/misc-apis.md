---
title: API diverses
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 512c6ba7-88ec-47e4-a656-6b30350b34e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 541dd721a00495632f0d633577b55933c9be03fa
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="miscellaneous-apis"></a>API diverses

<a name=data-protection-extensibility-mics-apis></a>

>[!WARNING]
> Les types qui implémentent des interfaces suivantes doivent être thread-safe pour les appelants plusieurs.

## <a name="isecret"></a>ISecret

L’interface ISecret représente une valeur secrète, tels que du matériel de clé de chiffrement. Il contient la surface API suivante.

* Longueur : int

* Dispose() : void

* WriteSecretIntoBuffer (ArraySegment<byte> tampon) : void

La méthode WriteSecretIntoBuffer remplit la mémoire tampon fournie avec la valeur brute de secret principal. La raison de que cette API prend la mémoire tampon comme un paramètre au lieu de retourner qu'un byte [] est directement que cela permet l’appelant pour épingler l’objet de la mémoire tampon, limiter l’exposition de secret principal pour le RÉCUPÉRATEUR de mémoire managée.

Le type de secret principal est une implémentation concrète de ISecret où la valeur secrète est stockée dans la mémoire d’en cours. Sur les plateformes Windows, la valeur de clé secrète est chiffrée [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).

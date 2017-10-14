---
title: "Détails de chiffrement authentifié."
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 826e6d5d-9620-44e6-ad93-3b1d9969b70b
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 6229adaa7145ff192104e2243daefb77f516c2c5
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="authenticated-encryption-details"></a>Détails de chiffrement authentifié.

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Les appels à IDataProtector.Protect sont des opérations de chiffrement authentifié. La méthode Protect propose à la fois la confidentialité et l’authenticité, et elle est liée à la chaîne de l’objectif qui a été utilisée pour dériver de cette instance IDataProtector particulier à partir de sa racine IDataProtectionProvider.

IDataProtector.Protect prend un paramètre de texte en clair de byte [] et génère une charge byte [] protégé, dont le format est décrit ci-dessous. (Il existe également une surcharge de méthode d’extension qui prend un paramètre de chaîne de texte en clair et retourne une charge utile de chaîne protégé. Si cette API est utilisée le format de charge utile protégé auront toujours le ci-dessous structure, mais il sera [codée au format base64url](https://tools.ietf.org/html/rfc4648#section-5).)

## <a name="protected-payload-format"></a>Format de charge utile protégé

Le format de charge utile protégé est constitué de trois composants principaux :

* Un en-tête magique de 32 bits qui identifie la version du système de protection des données.

* Id de clé de 128 bits qui identifie la clé utilisée pour protéger cette charge utile particulier.

* Le reste de la charge utile protégé est [spécifique pour le chiffreur encapsulé par cette clé](subkeyderivation.md#data-protection-implementation-subkey-derivation). Dans l’exemple ci-dessous, la clé représente un AES-256-CBC + le chiffreur de HMACSHA256, et la charge utile est subdivisée comme suit : * modificateur de la touche A 128 bits. * Un vecteur d’initialisation de 128 bits. * 48 d’octets de sortie de AES-256-CBC. * Une balise d’authentification HMACSHA256.

Une charge utile d’exemple protégé est illustrée ci-dessous.

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

À partir du format de charge utile ci-dessus les 32 premiers bits ou 4 octets sont l’en-tête magic identifiant la version (09 F0 C9 F0)

La prochaine 128 bits, ou 16 octets est l’identificateur de clé (AA de F8 des 0C 19 66 19 40 95 36 53 de 80 9C 81 FF EE 57)

Le reste contient la charge utile et est spécifique au format utilisé.

>[!WARNING]
> Toutes les charges utiles de protéger une clé donnée commence avec le même en-tête de 20 octets (valeur magique, id de clé). Les administrateurs peuvent utiliser cela pour établir un diagnostic pour se rapprocher lorsqu’une charge utile a été générée. Par exemple, la charge utile ci-dessus correspond à la clé {0c819c80-6619-4019-9536-53f8aaffee57}. Si après la vérification de l’espace de stockage de clés que date de l’activation de cette clé spécifique a été 2015-01-01 et sa date d’expiration a été 2015-03-01, puis il est raisonnable de penser que la charge utile (si ne pas falsifié) a été générée dans cette fenêtre, accordez ou prendre un petit manœuvre de chaque côté.

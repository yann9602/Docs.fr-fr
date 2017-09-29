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
ms.openlocfilehash: 4d0e63d7722071ab8806a217e96ee7ad7bf10286
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="authenticated-encryption-details"></a><span data-ttu-id="f09c5-103">Détails de chiffrement authentifié.</span><span class="sxs-lookup"><span data-stu-id="f09c5-103">Authenticated encryption details.</span></span>

<a name=data-protection-implementation-authenticated-encryption-details></a>

<span data-ttu-id="f09c5-104">Les appels à IDataProtector.Protect sont des opérations de chiffrement authentifié.</span><span class="sxs-lookup"><span data-stu-id="f09c5-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="f09c5-105">La méthode Protect propose à la fois la confidentialité et l’authenticité, et elle est liée à la chaîne de l’objectif qui a été utilisée pour dériver de cette instance IDataProtector particulier à partir de sa racine IDataProtectionProvider.</span><span class="sxs-lookup"><span data-stu-id="f09c5-105">The Protect method offers both confidentiality and authenticity, and it is tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="f09c5-106">IDataProtector.Protect prend un paramètre de texte en clair de byte [] et génère une charge byte [] protégé, dont le format est décrit ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="f09c5-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="f09c5-107">(Il existe également une surcharge de méthode d’extension qui prend un paramètre de chaîne de texte en clair et retourne une charge utile de chaîne protégé.</span><span class="sxs-lookup"><span data-stu-id="f09c5-107">(There is also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="f09c5-108">Si cette API est utilisée le format de charge utile protégé auront toujours le ci-dessous structure, mais il sera [codée au format base64url](https://tools.ietf.org/html/rfc4648#section-5).)</span><span class="sxs-lookup"><span data-stu-id="f09c5-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="f09c5-109">Format de charge utile protégé</span><span class="sxs-lookup"><span data-stu-id="f09c5-109">Protected payload format</span></span>

<span data-ttu-id="f09c5-110">Le format de charge utile protégé est constitué de trois composants principaux :</span><span class="sxs-lookup"><span data-stu-id="f09c5-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="f09c5-111">Un en-tête magique de 32 bits qui identifie la version du système de protection des données.</span><span class="sxs-lookup"><span data-stu-id="f09c5-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="f09c5-112">Id de clé de 128 bits qui identifie la clé utilisée pour protéger cette charge utile particulier.</span><span class="sxs-lookup"><span data-stu-id="f09c5-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="f09c5-113">Le reste de la charge utile protégé est [spécifique pour le chiffreur encapsulé par cette clé](subkeyderivation.md#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="f09c5-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](subkeyderivation.md#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="f09c5-114">Dans l’exemple ci-dessous, la clé représente un AES-256-CBC + le chiffreur de HMACSHA256, et la charge utile est subdivisée comme suit : * modificateur de la touche A 128 bits.</span><span class="sxs-lookup"><span data-stu-id="f09c5-114">In the example below the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows: * A 128-bit key modifier.</span></span> <span data-ttu-id="f09c5-115">* Un vecteur d’initialisation de 128 bits.</span><span class="sxs-lookup"><span data-stu-id="f09c5-115">* A 128-bit initialization vector.</span></span> <span data-ttu-id="f09c5-116">* 48 d’octets de sortie de AES-256-CBC.</span><span class="sxs-lookup"><span data-stu-id="f09c5-116">* 48 bytes of AES-256-CBC output.</span></span> <span data-ttu-id="f09c5-117">* Une balise d’authentification HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="f09c5-117">* An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="f09c5-118">Une charge utile d’exemple protégé est illustrée ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="f09c5-118">A sample protected payload is illustrated below.</span></span>

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

<span data-ttu-id="f09c5-119">À partir du format de charge utile ci-dessus les 32 premiers bits ou 4 octets sont l’en-tête magic identifiant la version (09 F0 C9 F0)</span><span class="sxs-lookup"><span data-stu-id="f09c5-119">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="f09c5-120">La prochaine 128 bits, ou 16 octets est l’identificateur de clé (AA de F8 des 0C 19 66 19 40 95 36 53 de 80 9C 81 FF EE 57)</span><span class="sxs-lookup"><span data-stu-id="f09c5-120">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="f09c5-121">Le reste contient la charge utile et est spécifique au format utilisé.</span><span class="sxs-lookup"><span data-stu-id="f09c5-121">The remainder contains the payload and is specific to the format used.</span></span>

>[!WARNING]
> <span data-ttu-id="f09c5-122">Toutes les charges utiles de protéger une clé donnée commence avec le même en-tête de 20 octets (valeur magique, id de clé).</span><span class="sxs-lookup"><span data-stu-id="f09c5-122">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="f09c5-123">Les administrateurs peuvent utiliser cela pour établir un diagnostic pour se rapprocher lorsqu’une charge utile a été générée.</span><span class="sxs-lookup"><span data-stu-id="f09c5-123">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="f09c5-124">Par exemple, la charge utile ci-dessus correspond à la clé {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="f09c5-124">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="f09c5-125">Si après la vérification de l’espace de stockage de clés que date de l’activation de cette clé spécifique a été 2015-01-01 et sa date d’expiration a été 2015-03-01, puis il est raisonnable de penser que la charge utile (si ne pas falsifié) a été générée dans cette fenêtre, accordez ou prendre un petit manœuvre de chaque côté.</span><span class="sxs-lookup"><span data-stu-id="f09c5-125">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it is reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>

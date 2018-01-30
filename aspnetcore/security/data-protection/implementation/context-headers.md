---
title: "En-têtes de contexte"
author: rick-anderson
description: "Ce document décrit les détails d’implémentation des en-têtes de contexte de protection de données ASP.NET Core."
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: c047c54efdcdb6192e4d38d2822c1077ee0a73e1
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="context-headers"></a>En-têtes de contexte

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>La théorie et arrière-plan

Dans le système de protection de données, une « clé » signifie un objet qui peut fournir authentifié de services de chiffrement. Chaque clé est identifiée par un id unique (GUID), et elle comporte des informations algorithmiques et matériels entropic. Il vise que chaque clé contenir entropie unique, mais le système ne peut pas appliquer qui, et nous devons également prendre en compte pour les développeurs qui peuvent changer l’anneau de clé manuellement en modifiant les informations algorithmiques d’une clé existante dans l’anneau de clé. Pour atteindre nos exigences de sécurité étant donnés ces cas, le système de protection de données a un concept de [agilité de chiffrement](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), ce qui permet en toute sécurité à l’aide d’une seule valeur entropic entre plusieurs algorithmes de chiffrement.

Pour cela, la plupart des systèmes qui prennent en charge agilité de chiffrement, y compris des informations d’identification sur l’algorithme à l’intérieur de la charge utile. Les OID de l’algorithme est généralement un bon candidat pour cela. Toutefois, nous avons rencontré un problème est qu’il existe plusieurs manières de spécifier le même algorithme : « AES » (CNG) et gérés Aes, AesManaged, AesCryptoServiceProvider, AesCng et RijndaelManaged (si les paramètres spécifiques) classes sont toutes en fait les mêmes chose et nous devez mettre à jour un mappage de tous ces éléments à l’identificateur d’objet correct. Si un développeur souhaitiez fournir un algorithme personnalisé (ou même une autre implémentation d’AES), ils auraient pas pour nous dire son OID. Cette étape d’inscription supplémentaires rend la configuration du système particulièrement difficile.

Exécution pas à pas précédent, nous avons décidé que nous avons approche consistant à partir d’une direction incorrecte. Un OID vous indique ce que l’algorithme, mais nous ne se soucient réellement à ce sujet. Si nous avons besoin d’utiliser une seule valeur entropic en toute sécurité dans les deux algorithmes différents, il n’est pas nécessaire pour que nous puissions savoir quels sont les algorithmes. Ce que nous importantes est leur comportement. N’importe quel algorithme de chiffrement symétrique acceptable est également une permutation pseudo-aléatoire fort (PRP) : corrigez les entrées (clé, en texte clair, IV, le mode de chaînage des propriétés) et la sortie de texte chiffré sera différente de n’importe quel autre chiffrement symétrique avec la probabilité est surchargé algorithme étant donné les mêmes entrées. De même, n’importe quelle fonction de hachage à clé correcte est également une fonction pseudo-aléatoire fort (PRF), et un ensemble d’entrée fixe sa sortie sera extrêmement différente de toute autre fonction de hachage à clé.

Ce concept de nom fort PRPs et PRF nous permet de créer un en-tête de contexte. Cet en-tête de contexte sert essentiellement une empreinte numérique stable sur les algorithmes utilisés pour toute opération donnée, et il fournit l’agilité de chiffrement requise par le système de protection des données. Cet en-tête est reproductible et qu’il est utilisé ultérieurement dans le cadre de la [processus de dérivation de la sous-clé](subkeyderivation.md#data-protection-implementation-subkey-derivation). Il existe deux façons de générer l’en-tête de contexte selon les modes de fonctionnement des algorithmes sous-jacent.

## <a name="cbc-mode-encryption--hmac-authentication"></a>Le chiffrement en mode CBC + l’authentification HMAC

<a name="data-protection-implementation-context-headers-cbc-components"></a>

L’en-tête de contexte se compose des éléments suivants :

* [16 bits] La valeur 00 00, qui est un marqueur de ce qui signifie que « le chiffrement CBC + l’authentification HMAC ».

* [32 bits] La longueur de clé (en octets, big-endian) de l’algorithme de chiffrement symétrique.

* [32 bits] La taille de bloc (en octets, big-endian) de l’algorithme de chiffrement symétrique.

* [32 bits] La longueur de clé (en octets, big-endian) de l’algorithme HMAC. (Actuellement la taille de la clé correspond toujours à la taille du résumé.)

* [32 bits] La taille de condensat (en octets, big-endian) de l’algorithme HMAC.

* EncCBC (K_E, IV, « »), qui est la sortie de l’algorithme de chiffrement symétrique donné d’une entrée de chaîne vide et où le vecteur d’initialisation est un vecteur de zéros. La construction de K_E est décrite ci-dessous.

* MAC (K_H, « »), qui est la sortie de l’algorithme HMAC donné d’une entrée de chaîne vide. La construction de K_H est décrite ci-dessous.

Dans l’idéal, nous pourrions passer des vecteurs de zéros pour K_E et K_H. Toutefois, nous voulons éviter les situations où l’algorithme sous-jacent vérifie l’existence des clés faibles avant d’effectuer des opérations (notamment DES et 3DES), ce qui empêche à l’aide d’un modèle simple ou répétée comme un vecteur de zéros.

Au lieu de cela, nous utilisons le KDF SP800-108 NIST en Mode de compteur (consultez [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), s 5.1) avec une clé de longueur nulle, étiquette, contexte et des HMACSHA512 comme le PRF sous-jacent. Nous utilisons | K_E | + | K_H | octets de sortie, puis décomposer le résultat K_E et K_H eux-mêmes. Mathématiquement, cela est représenté comme suit.

(K_E || K_H) = SP800_108_CTR (prf = HMACSHA512, clé = « », étiquette = « », contexte = « »)

### <a name="example-aes-192-cbc--hmacsha256"></a>Exemple : AES-192-CBC + HMACSHA256

Par exemple, prenons le cas où l’algorithme de chiffrement symétrique est AES-192-CBC et l’algorithme de validation est HMACSHA256. Le système peut générer l’en-tête de contexte en procédant comme suit.

Commençons (K_E || K_H) = SP800_108_CTR (prf = HMACSHA512, clé = « », étiquette = « », contexte = « »), où | K_E | = 192 bits et | K_H | = 256 bits par les algorithmes spécifiés. Cela nous mène à K_E = 5BB6... 21DD et K_H = A04A... 00A9 dans l’exemple ci-dessous :

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

Ensuite, calcul Enc_CBC (K_E, IV, « ») pour AES-192-CBC donné IV = 0 * et K_E comme indiqué ci-dessus.

result := F474B1872B3B53E4721DE19C0841DB6F

Ensuite, calcul MAC (K_H, « ») pour HMACSHA256 donné K_H comme indiqué ci-dessus.

result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C

Cela génère l’en-tête de contexte complet ci-dessous :

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Cet en-tête de contexte est l’empreinte numérique de la paire d’algorithme de chiffrement authentifié (le chiffrement AES-192-CBC + HMACSHA256 validation). Les composants, comme décrit [ci-dessus](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) sont :

* le marqueur (00 00)

* la longueur de clé de chiffrement de blocs (00 00 00 18)

* la taille de bloc de chiffrement de blocs (00 00 00 10)

* la longueur de clé HMAC (00 00 00 20)

* la taille de condensat HMAC (00 00 00 20)

* le chiffrement par blocs sortie PRP (F4 74 - DB 6F) et

* la sortie HMAC PRF (D4 79 - fin).

> [!NOTE]
> Le chiffrement du mode CBC + HMAC en-tête de contexte de l’authentification est généré de la même façon quel que soit l’indique si les implémentations des algorithmes sont fournies par CNG de Windows ou par les types managés SymmetricAlgorithm et élément KeyedHashAlgorithm impossible. Ainsi, les applications qui s’exécutent sur différents systèmes d’exploitation produire de manière fiable le même en-tête de contexte même si les implémentations des algorithmes diffèrent entre les systèmes d’exploitation. (Dans la pratique, l’élément KeyedHashAlgorithm impossible ne doit être un code HMAC approprié. Il peut être n’importe quel type d’algorithme de hachage à clé.)

### <a name="example-3des-192-cbc--hmacsha1"></a>Exemple : 3DES-192-CBC + HMACSHA1

Commençons (K_E || K_H) = SP800_108_CTR (prf = HMACSHA512, clé = « », étiquette = « », contexte = « »), où | K_E | = 192 bits et | K_H | = 160 bits par les algorithmes spécifiés. Cela nous mène à K_E = A219... E2BB et K_H = DC4A... B464 dans l’exemple ci-dessous :

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

Ensuite, calcul Enc_CBC (K_E, IV, « ») pour 3DES-192-CBC donné IV = 0 * et K_E comme indiqué ci-dessus.

result := ABB100F81E53E10E

Ensuite, calcul MAC (K_H, « ») pour HMACSHA1 donné K_H comme indiqué ci-dessus.

result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555

Cela génère l’en-tête de contexte complet qui est une empreinte numérique de l’authentifié paire d’algorithme de chiffrement (le chiffrement 3DES-192-CBC + HMACSHA1 validation), illustré ci-dessous :

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Les composants se répartissent comme suit :

* le marqueur (00 00)

* la longueur de clé de chiffrement de blocs (00 00 00 18)

* la taille de bloc de chiffrement de blocs (00 00 00 08)

* la longueur de clé HMAC (00 00 00 14)

* la taille de condensat HMAC (00 00 00 14)

* le chiffrement par blocs sortie PRP (B1 AB - E1 0E) et

* la sortie HMAC PRF (76 EB - fin).

## <a name="galoiscounter-mode-encryption--authentication"></a>Le chiffrement du Mode de compteur/GALOIS + de l’authentification

L’en-tête de contexte se compose des éléments suivants :

* [16 bits] La valeur 00 01, ce qui constitue un marqueur de ce qui signifie « chiffrement de GCM + authentification ».

* [32 bits] La longueur de clé (en octets, big-endian) de l’algorithme de chiffrement symétrique.

* [32 bits] Taille de valeur à usage unique (en octets, big-endian) utilisée pendant les opérations de chiffrement authentifié. (Pour notre système, il est fixé à taille nonce = 96 bits.)

* [32 bits] La taille de bloc (en octets, big-endian) de l’algorithme de chiffrement symétrique. (Pour GCM, cela est fixé à la taille de bloc = 128 bits.)

* [32 bits] Authentification balise taille (en octets, big-endian) générée par la fonction de chiffrement authentifié. (Pour notre système, cela est fixé à la taille de la balise = 128 bits.)

* [128 bits] La balise de Enc_GCM (K_E, la valeur à usage unique, « »), qui est la sortie de l’algorithme de chiffrement symétrique donné d’une entrée de chaîne vide et où la valeur à usage unique est un vecteur de zéros 96 bits.

K_E est dérivée à l’aide du même mécanisme que dans les le chiffrement CBC + d’un scénario d’authentification HMAC. Toutefois, comme il n’existe aucun K_H en jeu ici, nous disposent essentiellement | K_H | = 0, et l’algorithme est réduit à la sous la forme.

K_E = SP800_108_CTR (prf = HMACSHA512, clé = « », étiquette = « », contexte = « »)

### <a name="example-aes-256-gcm"></a>Exemple : AES-256-GCM

Commençons K_E = SP800_108_CTR (prf = HMACSHA512, clé = « », étiquette = « », contexte = « »), où | K_E | = 256 bits.

K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8

Ensuite, la balise d’authentification de Enc_GCM de calcul (K_E, la valeur à usage unique, « ») pour AES-256-GCM donné nonce = 096 et K_E comme indiqué ci-dessus.

result := E7DCCE66DF855A323A6BB7BD7A59BE45

Cela génère l’en-tête de contexte complet ci-dessous :

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Les composants se répartissent comme suit :

   * le marqueur (00 01)

   * la longueur de clé de chiffrement de blocs (00 00 00 20)

   * la taille de la valeur à usage unique (00 00 00 0 C)

   * la taille de bloc de chiffrement de blocs (00 00 00 10)

   * la taille de balise d’authentification (00 00 00 10) et

   * la balise d’authentification de l’exécution du chiffrement par blocs (contrôleur de domaine E7 - fin).

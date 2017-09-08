---
title: "Format de stockage de clés"
author: tdykstra
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: e8996478-f7bf-4b58-bab4-7fdb5d8556c5
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: e761eaa406a9691e3fa36881d42c1a0c1bd8a206
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="key-storage-format"></a>Format de stockage de clés

<a name=data-protection-implementation-key-storage-format></a>

Les objets sont stockés au repos dans la représentation XML. Le répertoire par défaut pour le stockage de clés est % LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.

## <a name="the-key-element"></a>Le \<clé > élément

Clés existent en tant qu’objets de niveau supérieur dans le référentiel de clé. Par convention clés ont le nom de fichier **-clé {guid} .xml**, où {guid} est l’id de la clé. Chacun de ces fichiers contient une clé unique. Le format du fichier est comme suit.

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

Le \<clé > élément contient les attributs suivants et les éléments enfants :

* L’id de clé. Cette valeur est traitée comme ne faisant pas autorité ; le nom de fichier est simplement ne de lisibilité.

* La version de la \<clé > élément, actuellement fixée à 1.

* Dates de création, l’activation et l’expiration de la clé.

* A \<descripteur > élément, qui contient des informations sur l’implémentation de chiffrement authentifié contenue dans cette clé.

Dans l’exemple ci-dessus, l’id de la clé est {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, il a été créé et activé le 19 mars 2015, et il a une durée de vie de 90 jours. (Il peut arriver que la date d’activation peut être légèrement avant la date de création, comme dans cet exemple. Cela est dû à un CD/m² dans les API de travail et ne présente aucun danger dans la pratique.)

## <a name="the-descriptor-element"></a>Le \<descripteur > élément

Externe \<descripteur > élément contient un attribut deserializerType, qui est le nom qualifié d’assembly d’un type qui implémente IAuthenticatedEncryptorDescriptorDeserializer. Ce type est responsable de lire interne \<descripteur > élément et pour l’analyse les informations contenues dans.

Le format de la \<descripteur > élément dépend de l’implémentation de chiffreur authentifié encapsulée par la clé, et chaque type de désérialiseur attend un format légèrement différent pour cela. En général, toutefois, cet élément contient algorithmiques informations (les noms, types, identificateurs d’objet, ou similaire) et le matériel de clé secrète. Dans l’exemple ci-dessus, le descripteur spécifie que cette clé encapsule le chiffrement AES-256-CBC + HMACSHA256 validation.

## <a name="the-encryptedsecret-element"></a>Le \<encryptedSecret > élément

Un <encryptedSecret> élément qui contient le formulaire chiffrée de la clé secrète peut être présent si [le chiffrement de clés secrètes au repos est activé](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest). L’attribut decryptorType sera le nom qualifié d’assembly d’un type qui implémente IXmlDecryptor. Ce type est responsable de lire interne <encryptedKey> élément et le déchiffrement pour récupérer le texte brut d’origine.

Comme avec \<descripteur >, le format de la <encryptedSecret> élément varie selon le mécanisme de chiffrement au repos en cours d’utilisation. Dans l’exemple ci-dessus, la clé principale est chiffrée à l’aide de DPAPI Windows par le commentaire.

## <a name="the-revocation-element"></a>Le \<révocation > élément

Révocations existent en tant qu’objets de niveau supérieur dans le référentiel de clé. Par convention révocations ont le nom de fichier **révocation-{timestamp} .xml** (pour la révocation de toutes les clés avant une date spécifique) ou **révocation-{guid} .xml** (pour la révocation d’une clé spécifique). Chaque fichier contienne un seul \<révocation > élément.

Pour des révocations de clés individuelles, le contenu du fichier sera comme ci-dessous.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

Dans ce cas, seule la clé spécifiée est révoquée. Si l’id de clé est « * », toutefois, comme dans l’exemple ci-dessous, toutes les clés dont la date de création est antérieure à la date de révocation spécifiée sont considérés comme révoqués.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

Le \<raison > élément n’est jamais lu par le système. Il est simplement un emplacement pratique pour stocker une raison contrôlable de visu de la révocation.

---
title: "Immuabilité clée et la modification des paramètres"
author: rick-anderson
description: "Ce document décrit les détails d’implémentation de l’ASP.NET Core protection clée immuabilité des données API."
keywords: "ASP.NET Core, protection des données, la clé que l’immuabilité"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: fc911ae3-feca-4ffe-8b43-362bc632fe04
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 96860b44b64f241a1bbff2ac8366e0863b1ac10c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="key-immutability-and-changing-settings"></a>Immuabilité et la modification des paramètres de clé

Une fois qu’un objet est persistant dans le magasin de sauvegarde, sa représentation est toujours fixe. Nouvelles données peuvent être ajoutées au magasin de stockage, mais les données existantes ne peuvent jamais être mutées. L’objectif principal de ce comportement est afin d’éviter une altération des données.

Une conséquence de ce comportement est qu’une fois qu’une clé est écrite dans le magasin de stockage, il est immuable. Ses dates de création, d’activation et d’expiration ne sont pas modifiables, si elle peut révoquées à l’aide de `IKeyManager`. En outre, ses informations algorithmiques sous-jacentes, support de gestion et le chiffrement sur les propriétés rest sont également immuables.

Si le développeur modifie un paramètre qui affecte la persistance des clés, ces modifications ne prendront effet jusqu'à la prochaine génération d’une clé, soit via un appel explicite à `IKeyManager.CreateNewKey` ou via de la protection système de données propre [clé automatique génération](key-management.md#data-protection-implementation-key-management) comportement. Les paramètres qui affectent la persistance des clés sont les suivantes :

* [La durée de vie de clé par défaut](key-management.md#data-protection-implementation-key-management)

* [Le chiffrement à clé au mécanisme de rest](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)

* [Les informations algorithmiques contenues dans la clé](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Si vous avez besoin de ces paramètres à bannir antérieures à la clé automatique suivante propagée temps, envisagez de faire un appel explicite à `IKeyManager.CreateNewKey` pour forcer la création d’une nouvelle clé. N’oubliez pas de fournir une date de l’activation explicite ({maintenant + 2 jours} est une règle empirique prévoir un délai pour propager les modifications) et la date d’expiration dans l’appel.

>[!TIP]
> Toutes les applications toucher le référentiel doivent spécifier les mêmes paramètres avec le `IDataProtectionBuilder` les méthodes d’extension. Dans le cas contraire, les propriétés de la clé persistante seront dépendantes de l’application spécifique qui a appelé les routines de la génération de clés.

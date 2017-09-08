---
title: "Immuabilité clée et la modification des paramètres"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: fc911ae3-feca-4ffe-8b43-362bc632fe04
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 3afce8f84ebe3b709ea169c7db27f99829f157ab
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="key-immutability-and-changing-settings"></a>Immuabilité et la modification des paramètres de clé

Une fois qu’un objet est persistant dans le magasin de sauvegarde, sa représentation est toujours fixe. Nouvelles données peuvent être ajoutées au magasin de stockage, mais les données existantes ne peuvent jamais être mutées. L’objectif principal de ce comportement est afin d’éviter une altération des données.

Une conséquence de ce comportement est qu’une fois qu’une clé est écrite dans le magasin de stockage, il est immuable. Ses dates de création, d’activation et d’expiration ne peuvent être modifiés, si elle peut révoquées à l’aide de IKeyManager. En outre, ses informations algorithmiques sous-jacentes, support de gestion et le chiffrement sur les propriétés rest sont également immuables.

Si le développeur modifie un paramètre qui affecte la persistance des clés, ces modifications ne prendront effet la prochaine fois qu’une clé est générée, via un appel explicite à IKeyManager.CreateNewKey ou données protection du système propre que [automatique génération de clé](key-management.md#data-protection-implementation-key-management) comportement. Les paramètres qui affectent la persistance des clés sont les suivantes :

* [La durée de vie de clé par défaut](key-management.md#data-protection-implementation-key-management)

* [Le chiffrement à clé au mécanisme de rest](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)

* [Les informations algorithmiques contenues dans la clé](../configuration/overview.md#data-protection-changing-algorithms)

Si vous avez besoin de ces paramètres à bannir antérieures à la clé automatique suivante propagée temps, envisagez de le faire un appel explicite à IKeyManager.CreateNewKey pour forcer la création d’une nouvelle clé. N’oubliez pas de fournir une date de l’activation explicite ({maintenant + 2 jours} est une règle empirique prévoir un délai pour propager les modifications) et la date d’expiration dans l’appel.

>[!TIP]
> Toutes les applications toucher le référentiel doivent spécifier les mêmes paramètres avec les méthodes d’extension IDataProtectionBuilder, sinon, les propriétés de la clé persistante sera dépendantes de l’application spécifique qui a appelé les routines de la génération de clés .

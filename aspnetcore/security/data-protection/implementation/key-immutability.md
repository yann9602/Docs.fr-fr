---
title: "Immuabilité clée et la modification des paramètres"
author: rick-anderson
description: "Ce document décrit les détails d’implémentation de l’ASP.NET Core protection clée immuabilité des données API."
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 98727c7a0c525edcda4fd8d004e0ac584cf0a5e5
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="key-immutability-and-changing-settings"></a>Immuabilité et la modification des paramètres de clé

Une fois qu’un objet est persistant dans le magasin de sauvegarde, sa représentation est toujours fixe. Nouvelles données peuvent être ajoutées au magasin de stockage, mais les données existantes ne peuvent jamais être mutées. L’objectif principal de ce comportement est afin d’éviter une altération des données.

Une conséquence de ce comportement est qu’une fois qu’une clé est écrite dans le magasin de stockage, il est immuable. Ses dates de création, d’activation et d’expiration ne sont pas modifiables, si elle peut révoquées à l’aide de `IKeyManager`. En outre, ses informations algorithmiques sous-jacentes, support de gestion et le chiffrement sur les propriétés rest sont également immuables.

Si le développeur modifie un paramètre qui affecte la persistance des clés, ces modifications ne sont pas en vigueur jusqu'à la prochaine génération d’une clé, soit via un appel explicite à `IKeyManager.CreateNewKey` ou via de la protection système de données propre [clé automatique génération](key-management.md#data-protection-implementation-key-management) comportement. Les paramètres qui affectent la persistance des clés sont les suivantes :

* [La durée de vie de clé par défaut](key-management.md#data-protection-implementation-key-management)

* [Le chiffrement à clé au mécanisme de rest](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)

* [Les informations algorithmiques contenues dans la clé](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Si vous avez besoin de ces paramètres à bannir antérieures à la clé automatique suivante propagée temps, envisagez de faire un appel explicite à `IKeyManager.CreateNewKey` pour forcer la création d’une nouvelle clé. N’oubliez pas de fournir une date de l’activation explicite ({maintenant + 2 jours} est une règle empirique prévoir un délai pour propager les modifications) et la date d’expiration dans l’appel.

>[!TIP]
> Toutes les applications toucher le référentiel doivent spécifier les mêmes paramètres avec le `IDataProtectionBuilder` les méthodes d’extension. Dans le cas contraire, les propriétés de la clé persistante seront dépendantes de l’application spécifique qui a appelé les routines de la génération de clés.

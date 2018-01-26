---
title: "Protection des données dans ASP.NET Core"
author: rick-anderson
description: "Ce document constitue la table des matières des différentes rubriques relatives à la protection des données ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/index
ms.openlocfilehash: 151385964d877fc9eadaa219320e5f5a195164e4
ms.sourcegitcommit: 3f491f887074310fc0f145cd01a670aa63b969e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="data-protection-in-aspnet-core-consumer-apis-configuration-extensibility-apis-and-implementation"></a>Protection des données dans ASP.NET Core : API de contrôle serveur consommateur, configuration, API d’extensibilité et implémentation

* [Présentation de la protection des données](introduction.md)

* [Bien démarrer avec les API de protection des données](using-data-protection.md)

* [API de contrôle serveur consommateur](consumer-apis/index.md)

  * [Vue d’ensemble des API de contrôle serveur consommateur](consumer-apis/overview.md)

  * [Chaînes d’objectifs](consumer-apis/purpose-strings.md)

  * [Hiérarchie d’objectifs et architecture mutualisée](consumer-apis/purpose-strings-multitenancy.md)

  * [Hachage de mot de passe](consumer-apis/password-hashing.md)

  * [Limitation de la durée de vie des charges utiles protégées](consumer-apis/limited-lifetime-payloads.md)

  * [Retrait de la protection des charges utiles dont les clés ont été révoquées](consumer-apis/dangerous-unprotect.md)

* [Configuration](configuration/index.md)

  * [Configuration de la protection des données](configuration/overview.md)

  * [Paramètres par défaut](configuration/default-settings.md)

  * [Stratégie à l’échelle de l’ordinateur](configuration/machine-wide-policy.md)

  * [Scénarios non compatibles avec l’injection de dépendances](configuration/non-di-scenarios.md)

* [API d’extensibilité](extensibility/index.md)

  * [Extensibilité du chiffrement de base](extensibility/core-crypto.md)

  * [Extensibilité de la gestion de clés](extensibility/key-management.md)

  * [API diverses](extensibility/misc-apis.md)

* [Implémentation](implementation/index.md)

  * [Détails du chiffrement authentifié](implementation/authenticated-encryption-details.md)

  * [Dérivation de sous-clé et chiffrement authentifié](implementation/subkeyderivation.md)

  * [En-têtes de contexte](implementation/context-headers.md)

  * [Gestion des clés](implementation/key-management.md)

  * [Fournisseurs de stockage de clés](implementation/key-storage-providers.md)

  * [Chiffrement à clé au repos](implementation/key-encryption-at-rest.md)

  * [Immuabilité des clés et modification des paramètres](implementation/key-immutability.md)

  * [Format de stockage des clés](implementation/key-storage-format.md)

  * [Fournisseurs de protection des données éphémères](implementation/key-storage-ephemeral.md)

* [Compatibilité](compatibility/index.md)

  * [Partage de cookies entre applications](xref:security/data-protection/compatibility/cookie-sharing)

  * [Remplacement de <machineKey> dans ASP.NET](xref:security/data-protection/compatibility/replacing-machinekey)

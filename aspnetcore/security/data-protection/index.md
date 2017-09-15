---
title: "Protection des données dans ASP.NET Core"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 1f402da8-1052-4970-9835-9f9f16a02dbc
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/index
ms.openlocfilehash: 39f2ca96f8542de033274ea957b5c7736948c981
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="data-protection-in-aspnet-core-consumer-apis-configuration-extensibility-apis-and-implementation"></a><span data-ttu-id="c895b-103">Protection des données dans ASP.NET Core : API de contrôle serveur consommateur, configuration, API d’extensibilité et implémentation</span><span class="sxs-lookup"><span data-stu-id="c895b-103">Data Protection in ASP.NET Core: Consumer APIs, configuration, extensibility APIs and implementation</span></span>

* [<span data-ttu-id="c895b-104">Présentation de la protection des données</span><span class="sxs-lookup"><span data-stu-id="c895b-104">Introduction to Data Protection</span></span>](introduction.md)

* [<span data-ttu-id="c895b-105">Bien démarrer avec les API de protection des données</span><span class="sxs-lookup"><span data-stu-id="c895b-105">Getting Started with the Data Protection APIs</span></span>](using-data-protection.md)

* [<span data-ttu-id="c895b-106">API de contrôle serveur consommateur</span><span class="sxs-lookup"><span data-stu-id="c895b-106">Consumer APIs</span></span>](consumer-apis/index.md)

  * [<span data-ttu-id="c895b-107">Vue d’ensemble des API de contrôle serveur consommateur</span><span class="sxs-lookup"><span data-stu-id="c895b-107">Consumer APIs Overview</span></span>](consumer-apis/overview.md)

  * [<span data-ttu-id="c895b-108">Chaînes d’objectifs</span><span class="sxs-lookup"><span data-stu-id="c895b-108">Purpose Strings</span></span>](consumer-apis/purpose-strings.md)

  * [<span data-ttu-id="c895b-109">Hiérarchie d’objectifs et architecture mutualisée</span><span class="sxs-lookup"><span data-stu-id="c895b-109">Purpose hierarchy and multi-tenancy</span></span>](consumer-apis/purpose-strings-multitenancy.md)

  * [<span data-ttu-id="c895b-110">Hachage de mot de passe</span><span class="sxs-lookup"><span data-stu-id="c895b-110">Password Hashing</span></span>](consumer-apis/password-hashing.md)

  * [<span data-ttu-id="c895b-111">Limitation de la durée de vie des charges utiles protégées</span><span class="sxs-lookup"><span data-stu-id="c895b-111">Limiting the lifetime of protected payloads</span></span>](consumer-apis/limited-lifetime-payloads.md)

  * [<span data-ttu-id="c895b-112">Retrait de la protection des charges utiles dont les clés ont été révoquées</span><span class="sxs-lookup"><span data-stu-id="c895b-112">Unprotecting payloads whose keys have been revoked</span></span>](consumer-apis/dangerous-unprotect.md)

* [<span data-ttu-id="c895b-113">Configuration</span><span class="sxs-lookup"><span data-stu-id="c895b-113">Configuration</span></span>](configuration/index.md)

  * [<span data-ttu-id="c895b-114">Configuration de la protection des données</span><span class="sxs-lookup"><span data-stu-id="c895b-114">Configuring Data Protection</span></span>](configuration/overview.md)

  * [<span data-ttu-id="c895b-115">Paramètres par défaut</span><span class="sxs-lookup"><span data-stu-id="c895b-115">Default Settings</span></span>](configuration/default-settings.md)

  * [<span data-ttu-id="c895b-116">Stratégie à l’échelle de la machine</span><span class="sxs-lookup"><span data-stu-id="c895b-116">Machine Wide Policy</span></span>](configuration/machine-wide-policy.md)

  * [<span data-ttu-id="c895b-117">Scénarios non compatibles avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="c895b-117">Non DI Aware Scenarios</span></span>](configuration/non-di-scenarios.md)

* [<span data-ttu-id="c895b-118">API d’extensibilité</span><span class="sxs-lookup"><span data-stu-id="c895b-118">Extensibility APIs</span></span>](extensibility/index.md)

  * [<span data-ttu-id="c895b-119">Extensibilité du chiffrement de base</span><span class="sxs-lookup"><span data-stu-id="c895b-119">Core cryptography extensibility</span></span>](extensibility/core-crypto.md)

  * [<span data-ttu-id="c895b-120">Extensibilité de la gestion de clés</span><span class="sxs-lookup"><span data-stu-id="c895b-120">Key management extensibility</span></span>](extensibility/key-management.md)

  * [<span data-ttu-id="c895b-121">API diverses</span><span class="sxs-lookup"><span data-stu-id="c895b-121">Miscellaneous APIs</span></span>](extensibility/misc-apis.md)

* [<span data-ttu-id="c895b-122">Implémentation</span><span class="sxs-lookup"><span data-stu-id="c895b-122">Implementation</span></span>](implementation/index.md)

  * [<span data-ttu-id="c895b-123">Détails du chiffrement authentifié.</span><span class="sxs-lookup"><span data-stu-id="c895b-123">Authenticated encryption details.</span></span>](implementation/authenticated-encryption-details.md)

  * [<span data-ttu-id="c895b-124">Dérivation de sous-clé et chiffrement authentifié</span><span class="sxs-lookup"><span data-stu-id="c895b-124">Subkey Derivation and Authenticated Encryption</span></span>](implementation/subkeyderivation.md)

  * [<span data-ttu-id="c895b-125">En-têtes de contexte</span><span class="sxs-lookup"><span data-stu-id="c895b-125">Context headers</span></span>](implementation/context-headers.md)

  * [<span data-ttu-id="c895b-126">Gestion des clés</span><span class="sxs-lookup"><span data-stu-id="c895b-126">Key Management</span></span>](implementation/key-management.md)

  * [<span data-ttu-id="c895b-127">Fournisseurs de stockage de clés</span><span class="sxs-lookup"><span data-stu-id="c895b-127">Key Storage Providers</span></span>](implementation/key-storage-providers.md)

  * [<span data-ttu-id="c895b-128">Chiffrement à clé au repos</span><span class="sxs-lookup"><span data-stu-id="c895b-128">Key Encryption At Rest</span></span>](implementation/key-encryption-at-rest.md)

  * [<span data-ttu-id="c895b-129">Immuabilité des clés et modification des paramètres</span><span class="sxs-lookup"><span data-stu-id="c895b-129">Key Immutability and Changing Settings</span></span>](implementation/key-immutability.md)

  * [<span data-ttu-id="c895b-130">Format de stockage des clés</span><span class="sxs-lookup"><span data-stu-id="c895b-130">Key Storage Format</span></span>](implementation/key-storage-format.md)

  * [<span data-ttu-id="c895b-131">Fournisseurs de protection des données éphémères</span><span class="sxs-lookup"><span data-stu-id="c895b-131">Ephemeral data protection providers</span></span>](implementation/key-storage-ephemeral.md)

* [<span data-ttu-id="c895b-132">Compatibilité</span><span class="sxs-lookup"><span data-stu-id="c895b-132">Compatibility</span></span>](compatibility/index.md)

  * [<span data-ttu-id="c895b-133">Partage des cookies entre les applications</span><span class="sxs-lookup"><span data-stu-id="c895b-133">Sharing cookies between applications</span></span>](compatibility/cookie-sharing.md)

  * [<span data-ttu-id="c895b-134">Remplacement de <machineKey> dans ASP.NET</span><span class="sxs-lookup"><span data-stu-id="c895b-134">Replacing <machineKey> in ASP.NET</span></span>](compatibility/replacing-machinekey.md)

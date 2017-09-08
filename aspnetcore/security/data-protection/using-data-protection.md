---
title: "Prise en main de l’API de Protection des données"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 39b7a73c-29d4-4137-b311-49529adcf3cb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 2a381acf5faa7071fe4a22641037fcee11f6d362
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="getting-started-with-the-data-protection-apis"></a><span data-ttu-id="1ce55-103">Prise en main de l’API de Protection des données</span><span class="sxs-lookup"><span data-stu-id="1ce55-103">Getting Started with the Data Protection APIs</span></span>

<a name=security-data-protection-getting-started></a>

<span data-ttu-id="1ce55-104">Aux protection des données la plus simple se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="1ce55-104">At its simplest protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="1ce55-105">Créer un données protecteur à partir d’un fournisseur de protection des données.</span><span class="sxs-lookup"><span data-stu-id="1ce55-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="1ce55-106">Appelez la méthode de protection avec les données que vous souhaitez protéger.</span><span class="sxs-lookup"><span data-stu-id="1ce55-106">Call the Protect method with the data you want to protect.</span></span>

3. <span data-ttu-id="1ce55-107">Appelez la méthode de suppression de la protection avec les données que vous souhaitez reconvertir en texte brut.</span><span class="sxs-lookup"><span data-stu-id="1ce55-107">Call the Unprotect method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="1ce55-108">La plupart des infrastructures telles que ASP.NET ou SignalR déjà configurer le système de protection des données et l’ajouter à un conteneur de service que vous pouvez accéder via l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="1ce55-108">Most frameworks such as ASP.NET or SignalR already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="1ce55-109">L’exemple suivant montre comment configurer un conteneur de service pour l’injection de dépendance et l’inscription de la pile de protection des données, recevoir le fournisseur de protection des données via DI, création d’un protecteur et la protection puis ôter la protection des données</span><span class="sxs-lookup"><span data-stu-id="1ce55-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data</span></span>

<span data-ttu-id="1ce55-110">[!code-csharp[Main](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]</span><span class="sxs-lookup"><span data-stu-id="1ce55-110">[!code-csharp[Main](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]</span></span>

<span data-ttu-id="1ce55-111">Lorsque vous créez un logiciel de protection, vous devez fournir un ou plusieurs [objectif chaînes](consumer-apis/purpose-strings.md).</span><span class="sxs-lookup"><span data-stu-id="1ce55-111">When you create a protector you must provide one or more [Purpose Strings](consumer-apis/purpose-strings.md).</span></span> <span data-ttu-id="1ce55-112">Une chaîne de l’objet fournit une isolation entre les consommateurs, par exemple un protecteur créé avec une chaîne de l’objectif de « vert » ne serait pas en mesure de déprotéger les données fournies par un protecteur dans un but de « violet ».</span><span class="sxs-lookup"><span data-stu-id="1ce55-112">A purpose string provides isolation between consumers, for example a protector created with a purpose string of "green" would not be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="1ce55-113">Instances de IDataProtectionProvider et IDataProtector sont thread-safe pour les appelants plusieurs.</span><span class="sxs-lookup"><span data-stu-id="1ce55-113">Instances of IDataProtectionProvider and IDataProtector are thread-safe for multiple callers.</span></span> <span data-ttu-id="1ce55-114">Il est prévu qu’une fois qu’un composant obtient une référence à un IDataProtector via un appel à CreateProtector, il utilisera cette référence pour plusieurs appels à protéger et de suppression de la protection.</span><span class="sxs-lookup"><span data-stu-id="1ce55-114">It is intended that once a component gets a reference to an IDataProtector via a call to CreateProtector, it will use that reference for multiple calls to Protect and Unprotect.</span></span>
>
><span data-ttu-id="1ce55-115">Un appel à Unprotect lève CryptographicException si la charge utile protégée ne peut pas être vérifiée ou déchiffrée.</span><span class="sxs-lookup"><span data-stu-id="1ce55-115">A call to Unprotect will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="1ce55-116">Certains composants peuvent souhaiter ignorer les erreurs pendant les opérations ; ôter la protection un composant qui lit les cookies d’authentification peut gérer cette erreur et traiter la demande comme s’il n’avait aucun cookie tout plutôt qu’échouer la requête ferme.</span><span class="sxs-lookup"><span data-stu-id="1ce55-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="1ce55-117">Les composants dont vous souhaitez que ce comportement doivent spécifiquement intercepter CryptographicException au lieu d’absorber toutes les exceptions.</span><span class="sxs-lookup"><span data-stu-id="1ce55-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>

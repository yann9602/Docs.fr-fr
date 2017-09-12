---
title: "Extensibilité de la gestion de clés"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3606b251-8324-4485-8d52-582a2cd5cffb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: ed84b6dc257d5fd9e4c1cf6106df3c8bd6e14f64
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="key-management-extensibility"></a><span data-ttu-id="7cb37-103">Extensibilité de la gestion de clés</span><span class="sxs-lookup"><span data-stu-id="7cb37-103">Key management extensibility</span></span>

<a name=data-protection-extensibility-key-management></a>

>[!TIP]
> <span data-ttu-id="7cb37-104">Lecture la [gestion de clés](../implementation/key-management.md#data-protection-implementation-key-management) section avant de lire cette section, car il décrit certains des concepts fondamentaux de ces API.</span><span class="sxs-lookup"><span data-stu-id="7cb37-104">Read the [key management](../implementation/key-management.md#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

>[!WARNING]
> <span data-ttu-id="7cb37-105">Les types qui implémentent des interfaces suivantes doivent être thread-safe pour les appelants plusieurs.</span><span class="sxs-lookup"><span data-stu-id="7cb37-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="7cb37-106">Touche</span><span class="sxs-lookup"><span data-stu-id="7cb37-106">Key</span></span>

<span data-ttu-id="7cb37-107">L’interface IKey est la représentation sous forme de base d’une clé dans le système de cryptage par.</span><span class="sxs-lookup"><span data-stu-id="7cb37-107">The IKey interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="7cb37-108">L’expression clé est utilisé ici dans le sens abstrait, pas dans le sens littéral « chiffrement de matériel de clé ».</span><span class="sxs-lookup"><span data-stu-id="7cb37-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="7cb37-109">Une clé a les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="7cb37-109">A key has the following properties:</span></span>

* <span data-ttu-id="7cb37-110">Dates d’expiration, la création et l’activation</span><span class="sxs-lookup"><span data-stu-id="7cb37-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="7cb37-111">État de révocation</span><span class="sxs-lookup"><span data-stu-id="7cb37-111">Revocation status</span></span>

* <span data-ttu-id="7cb37-112">Identificateur de clé (GUID)</span><span class="sxs-lookup"><span data-stu-id="7cb37-112">Key identifier (a GUID)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7cb37-113">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7cb37-113">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="7cb37-114">En outre, IKey expose une méthode CreateEncryptor qui peut être utilisée pour créer un [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance liée à cette clé.</span><span class="sxs-lookup"><span data-stu-id="7cb37-114">Additionally, IKey exposes a CreateEncryptor method which can be used to create an [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7cb37-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7cb37-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="7cb37-116">En outre, IKey expose une méthode CreateEncryptorInstance qui peut être utilisée pour créer un [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance liée à cette clé.</span><span class="sxs-lookup"><span data-stu-id="7cb37-116">Additionally, IKey exposes a CreateEncryptorInstance method which can be used to create an [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

---

> [!NOTE]
> <span data-ttu-id="7cb37-117">Il n’existe aucune API pour récupérer les matières premières de chiffrement à partir d’une instance IKey.</span><span class="sxs-lookup"><span data-stu-id="7cb37-117">There is no API to retrieve the raw cryptographic material from an IKey instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="7cb37-118">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="7cb37-118">IKeyManager</span></span>

<span data-ttu-id="7cb37-119">L’interface IKeyManager représente un objet responsable de la manipulation, l’extraction et stockage de clés général.</span><span class="sxs-lookup"><span data-stu-id="7cb37-119">The IKeyManager interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="7cb37-120">Il expose trois opérations de niveau supérieur :</span><span class="sxs-lookup"><span data-stu-id="7cb37-120">It exposes three high-level operations:</span></span>

* <span data-ttu-id="7cb37-121">Créer une nouvelle clé et la rendre persistante dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="7cb37-121">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="7cb37-122">Obtenir toutes les clés de stockage.</span><span class="sxs-lookup"><span data-stu-id="7cb37-122">Get all keys from storage.</span></span>

* <span data-ttu-id="7cb37-123">Révoquer une ou plusieurs clés et rendre persistantes les informations de révocation pour le stockage.</span><span class="sxs-lookup"><span data-stu-id="7cb37-123">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="7cb37-124">L’écriture d’un IKeyManager est une tâche très avancée et la plupart des développeurs ne doit pas essayer il.</span><span class="sxs-lookup"><span data-stu-id="7cb37-124">Writing an IKeyManager is a very advanced task, and the majority of developers should not attempt it.</span></span> <span data-ttu-id="7cb37-125">Au lieu de cela, la plupart des développeurs doit bénéficier des installations offertes par le [XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager) classe.</span><span class="sxs-lookup"><span data-stu-id="7cb37-125">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager) class.</span></span>

<a name=data-protection-extensibility-key-management-xmlkeymanager></a>

## <a name="xmlkeymanager"></a><span data-ttu-id="7cb37-126">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="7cb37-126">XmlKeyManager</span></span>

<span data-ttu-id="7cb37-127">Le type de XmlKeyManager est l’implémentation concrète de la boîte de réception de IKeyManager.</span><span class="sxs-lookup"><span data-stu-id="7cb37-127">The XmlKeyManager type is the in-box concrete implementation of IKeyManager.</span></span> <span data-ttu-id="7cb37-128">Il fournit plusieurs installations utiles, y compris le dépôt de clé et le chiffrement des clés au repos.</span><span class="sxs-lookup"><span data-stu-id="7cb37-128">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="7cb37-129">Dans ce système, les clés sont représentées comme des éléments XML (en particulier, [XElement](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview).</span><span class="sxs-lookup"><span data-stu-id="7cb37-129">Keys in this system are represented as XML elements (specifically, [XElement](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview).</span></span>

<span data-ttu-id="7cb37-130">XmlKeyManager dépend de plusieurs autres composants au cours de l’exécution de ses tâches :</span><span class="sxs-lookup"><span data-stu-id="7cb37-130">XmlKeyManager depends on several other components in the course of fulfilling its tasks:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7cb37-131">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7cb37-131">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

* <span data-ttu-id="7cb37-132">AlgorithmConfiguration, qui détermine les algorithmes utilisés par les nouvelles clés.</span><span class="sxs-lookup"><span data-stu-id="7cb37-132">AlgorithmConfiguration, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="7cb37-133">IXmlRepository, qui contrôle où les clés sont rendues persistantes dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="7cb37-133">IXmlRepository, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="7cb37-134">IXmlEncryptor de [facultatif], ce qui permet de chiffrer les clés au repos.</span><span class="sxs-lookup"><span data-stu-id="7cb37-134">IXmlEncryptor [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="7cb37-135">IKeyEscrowSink de [facultatif], qui fournit des services de clé (Key escrow).</span><span class="sxs-lookup"><span data-stu-id="7cb37-135">IKeyEscrowSink [optional], which provides key escrow services.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7cb37-136">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7cb37-136">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

* <span data-ttu-id="7cb37-137">IXmlRepository, qui contrôle où les clés sont rendues persistantes dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="7cb37-137">IXmlRepository, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="7cb37-138">IXmlEncryptor de [facultatif], ce qui permet de chiffrer les clés au repos.</span><span class="sxs-lookup"><span data-stu-id="7cb37-138">IXmlEncryptor [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="7cb37-139">IKeyEscrowSink de [facultatif], qui fournit des services de clé (Key escrow).</span><span class="sxs-lookup"><span data-stu-id="7cb37-139">IKeyEscrowSink [optional], which provides key escrow services.</span></span>

---

<span data-ttu-id="7cb37-140">Vous trouverez ci-dessous des diagrammes de haut niveau qui indiquent la façon dont ces composants sont reliés dans XmlKeyManager.</span><span class="sxs-lookup"><span data-stu-id="7cb37-140">Below are high-level diagrams which indicate how these components are wired together within XmlKeyManager.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7cb37-141">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7cb37-141">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

   ![Création de la clé](key-management/_static/keycreation2.png)

   <span data-ttu-id="7cb37-143">*La création de clé / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="7cb37-143">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="7cb37-144">Dans l’implémentation de CreateNewKey, le composant AlgorithmConfiguration est utilisé pour créer un IAuthenticatedEncryptorDescriptor unique, qui est ensuite sérialisé au format XML.</span><span class="sxs-lookup"><span data-stu-id="7cb37-144">In the implementation of CreateNewKey, the AlgorithmConfiguration component is used to create a unique IAuthenticatedEncryptorDescriptor, which is then serialized as XML.</span></span> <span data-ttu-id="7cb37-145">Si un récepteur de clé (Key escrow) est présent, les données XML brutes (non chiffré) est fourni pour le récepteur pour le stockage à long terme.</span><span class="sxs-lookup"><span data-stu-id="7cb37-145">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="7cb37-146">Le code XML non chiffré est alors exécuté via un IXmlEncryptor (si nécessaire) pour générer le document XML chiffré.</span><span class="sxs-lookup"><span data-stu-id="7cb37-146">The unencrypted XML is then run through an IXmlEncryptor (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="7cb37-147">Ce document chiffré est enregistré sur le stockage à long terme via la IXmlRepository.</span><span class="sxs-lookup"><span data-stu-id="7cb37-147">This encrypted document is persisted to long-term storage via the IXmlRepository.</span></span> <span data-ttu-id="7cb37-148">(Si aucun IXmlEncryptor n’est configuré, le document non chiffré est persistante dans le IXmlRepository).</span><span class="sxs-lookup"><span data-stu-id="7cb37-148">(If no IXmlEncryptor is configured, the unencrypted document is persisted in the IXmlRepository.)</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7cb37-149">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7cb37-149">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

   ![Création de la clé](key-management/_static/keycreation1.png)

   <span data-ttu-id="7cb37-151">*La création de clé / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="7cb37-151">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="7cb37-152">Dans l’implémentation de CreateNewKey, le composant IAuthenticatedEncryptorConfiguration est utilisé pour créer un IAuthenticatedEncryptorDescriptor unique, qui est ensuite sérialisé au format XML.</span><span class="sxs-lookup"><span data-stu-id="7cb37-152">In the implementation of CreateNewKey, the IAuthenticatedEncryptorConfiguration component is used to create a unique IAuthenticatedEncryptorDescriptor, which is then serialized as XML.</span></span> <span data-ttu-id="7cb37-153">Si un récepteur de clé (Key escrow) est présent, les données XML brutes (non chiffré) est fourni pour le récepteur pour le stockage à long terme.</span><span class="sxs-lookup"><span data-stu-id="7cb37-153">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="7cb37-154">Le code XML non chiffré est alors exécuté via un IXmlEncryptor (si nécessaire) pour générer le document XML chiffré.</span><span class="sxs-lookup"><span data-stu-id="7cb37-154">The unencrypted XML is then run through an IXmlEncryptor (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="7cb37-155">Ce document chiffré est enregistré sur le stockage à long terme via la IXmlRepository.</span><span class="sxs-lookup"><span data-stu-id="7cb37-155">This encrypted document is persisted to long-term storage via the IXmlRepository.</span></span> <span data-ttu-id="7cb37-156">(Si aucun IXmlEncryptor n’est configuré, le document non chiffré est persistante dans le IXmlRepository).</span><span class="sxs-lookup"><span data-stu-id="7cb37-156">(If no IXmlEncryptor is configured, the unencrypted document is persisted in the IXmlRepository.)</span></span>

---

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7cb37-157">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7cb37-157">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

   ![Récupération de clés](key-management/_static/keyretrieval2.png)
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7cb37-159">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7cb37-159">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

   ![Récupération de clés](key-management/_static/keyretrieval1.png)

---

   <span data-ttu-id="7cb37-161">*Récupération de clé / GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="7cb37-161">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="7cb37-162">Dans l’implémentation de GetAllKeys, les documents XML représentant des clés et des révocations sont lus à partir de la IXmlRepository sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="7cb37-162">In the implementation of GetAllKeys, the XML documents representing keys and revocations are read from the underlying IXmlRepository.</span></span> <span data-ttu-id="7cb37-163">Si ces documents sont chiffrées, le système sera automatiquement les déchiffrer.</span><span class="sxs-lookup"><span data-stu-id="7cb37-163">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="7cb37-164">XmlKeyManager crée les instances IAuthenticatedEncryptorDescriptorDeserializer appropriés pour désérialiser les documents en instances IAuthenticatedEncryptorDescriptor, puis enveloppés dans des instances IKey individuelles.</span><span class="sxs-lookup"><span data-stu-id="7cb37-164">XmlKeyManager creates the appropriate IAuthenticatedEncryptorDescriptorDeserializer instances to deserialize the documents back into IAuthenticatedEncryptorDescriptor instances, which are then wrapped in individual IKey instances.</span></span> <span data-ttu-id="7cb37-165">Cette collection d’instances de IKey est retournée à l’appelant.</span><span class="sxs-lookup"><span data-stu-id="7cb37-165">This collection of IKey instances is returned to the caller.</span></span>

<span data-ttu-id="7cb37-166">Vous trouverez plus d’informations sur les éléments XML particuliers dans le [document au format de stockage de clés](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="7cb37-166">Further information on the particular XML elements can be found in the [key storage format document](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="7cb37-167">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="7cb37-167">IXmlRepository</span></span>

<span data-ttu-id="7cb37-168">L’interface IXmlRepository représente un type qui peut faire persister XML à et récupérer des données XML à partir d’un magasin de stockage.</span><span class="sxs-lookup"><span data-stu-id="7cb37-168">The IXmlRepository interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="7cb37-169">Il expose deux API :</span><span class="sxs-lookup"><span data-stu-id="7cb37-169">It exposes two APIs:</span></span>

* <span data-ttu-id="7cb37-170">GetAllElements() : IReadOnlyCollection<XElement></span><span class="sxs-lookup"><span data-stu-id="7cb37-170">GetAllElements() : IReadOnlyCollection<XElement></span></span>

* <span data-ttu-id="7cb37-171">StoreElement (élément de XElement, chaîne friendlyName)</span><span class="sxs-lookup"><span data-stu-id="7cb37-171">StoreElement(XElement element, string friendlyName)</span></span>

<span data-ttu-id="7cb37-172">Les implémentations de IXmlRepository n’avez pas besoin analyser le XML en passant par leur intermédiaire.</span><span class="sxs-lookup"><span data-stu-id="7cb37-172">Implementations of IXmlRepository don't need to parse the XML passing through them.</span></span> <span data-ttu-id="7cb37-173">Qu’ils doivent traiter les documents XML comme opaque et laisser les couches supérieures vous inquiétez pas sur la génération et l’analyse de documents.</span><span class="sxs-lookup"><span data-stu-id="7cb37-173">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="7cb37-174">Il existe deux types intégrés concrètes qui implémentent IXmlRepository : FileSystemXmlRepository et RegistryXmlRepository.</span><span class="sxs-lookup"><span data-stu-id="7cb37-174">There are two built-in concrete types which implement IXmlRepository: FileSystemXmlRepository and RegistryXmlRepository.</span></span> <span data-ttu-id="7cb37-175">Consultez le [document de fournisseurs de stockage de clés](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="7cb37-175">See the [key storage providers document](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers) for more information.</span></span> <span data-ttu-id="7cb37-176">L’inscription d’un IXmlRepository personnalisé serait être la manière appropriée pour utiliser un autre magasin de stockage, par exemple, stockage d’objets Blob Azure.</span><span class="sxs-lookup"><span data-stu-id="7cb37-176">Registering a custom IXmlRepository would be the appropriate manner to use a different backing store, e.g., Azure Blob Storage.</span></span> <span data-ttu-id="7cb37-177">Pour modifier l’échelle application du référentiel par défaut, inscrivez un singleton personnalisé IXmlRepository dans le fournisseur de services.</span><span class="sxs-lookup"><span data-stu-id="7cb37-177">To change the default repository application-wide, register a custom singleton IXmlRepository in the service provider.</span></span>

<a name=data-protection-extensibility-key-management-ixmlencryptor></a>

## <a name="ixmlencryptor"></a><span data-ttu-id="7cb37-178">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="7cb37-178">IXmlEncryptor</span></span>

<span data-ttu-id="7cb37-179">L’interface IXmlEncryptor représente un type qui peut chiffrer un élément XML de texte en clair.</span><span class="sxs-lookup"><span data-stu-id="7cb37-179">The IXmlEncryptor interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="7cb37-180">Il expose une API unique :</span><span class="sxs-lookup"><span data-stu-id="7cb37-180">It exposes a single API:</span></span>

* <span data-ttu-id="7cb37-181">Chiffrer (plaintextElement XElement) : EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="7cb37-181">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="7cb37-182">Si un IAuthenticatedEncryptorDescriptor sérialisé contient des éléments marqués comme « requiert le chiffrement », puis XmlKeyManager sera exécuté ces éléments via la méthode de chiffrer du IXmlEncryptor configuré et il persistera plutôt l’élément enciphered à l’élément de texte en clair à l’IXmlRepository.</span><span class="sxs-lookup"><span data-stu-id="7cb37-182">If a serialized IAuthenticatedEncryptorDescriptor contains any elements marked as "requires encryption", then XmlKeyManager will run those elements through the configured IXmlEncryptor's Encrypt method, and it will persist the enciphered element rather than the plaintext element to the IXmlRepository.</span></span> <span data-ttu-id="7cb37-183">La sortie de la méthode de chiffrement est un objet EncryptedXmlInfo.</span><span class="sxs-lookup"><span data-stu-id="7cb37-183">The output of the Encrypt method is an EncryptedXmlInfo object.</span></span> <span data-ttu-id="7cb37-184">Cet objet est un wrapper qui contient le XElement enciphered résultant et le Type qui représente un IXmlDecryptor qui peut être utilisé pour déchiffrer l’élément correspondant.</span><span class="sxs-lookup"><span data-stu-id="7cb37-184">This object is a wrapper which contains both the resultant enciphered XElement and the Type which represents an IXmlDecryptor which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="7cb37-185">Il existe quatre types intégrés concrètes qui implémentent IXmlEncryptor : CertificateXmlEncryptor, DpapiNGXmlEncryptor, DpapiXmlEncryptor et NullXmlEncryptor.</span><span class="sxs-lookup"><span data-stu-id="7cb37-185">There are four built-in concrete types which implement IXmlEncryptor: CertificateXmlEncryptor, DpapiNGXmlEncryptor, DpapiXmlEncryptor, and NullXmlEncryptor.</span></span> <span data-ttu-id="7cb37-186">Consultez le [chiffrement à clé au niveau du document de rest](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="7cb37-186">See the [key encryption at rest document](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) for more information.</span></span> <span data-ttu-id="7cb37-187">Pour modifier le mécanisme de clé chiffrement au repos à l’échelle de l’application par défaut, inscrivez un singleton personnalisé IXmlEncryptor dans le fournisseur de services.</span><span class="sxs-lookup"><span data-stu-id="7cb37-187">To change the default key-encryption-at-rest mechanism application-wide, register a custom singleton IXmlEncryptor in the service provider.</span></span>

## <a name="ixmldecryptor"></a><span data-ttu-id="7cb37-188">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="7cb37-188">IXmlDecryptor</span></span>

<span data-ttu-id="7cb37-189">L’interface IXmlDecryptor représente un type qui sait comment déchiffrer un XElement qui a été enciphered via un IXmlEncryptor.</span><span class="sxs-lookup"><span data-stu-id="7cb37-189">The IXmlDecryptor interface represents a type that knows how to decrypt an XElement that was enciphered via an IXmlEncryptor.</span></span> <span data-ttu-id="7cb37-190">Il expose une API unique :</span><span class="sxs-lookup"><span data-stu-id="7cb37-190">It exposes a single API:</span></span>

* <span data-ttu-id="7cb37-191">Déchiffrer (encryptedElement XElement) : XElement</span><span class="sxs-lookup"><span data-stu-id="7cb37-191">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="7cb37-192">La méthode Decrypt annule le chiffrement effectué par IXmlEncryptor.Encrypt.</span><span class="sxs-lookup"><span data-stu-id="7cb37-192">The Decrypt method undoes the encryption performed by IXmlEncryptor.Encrypt.</span></span> <span data-ttu-id="7cb37-193">Chaque implémentation concrète de IXmlEncryptor ont généralement une implémentation IXmlDecryptor concrète correspondante.</span><span class="sxs-lookup"><span data-stu-id="7cb37-193">Generally each concrete IXmlEncryptor implementation will have a corresponding concrete IXmlDecryptor implementation.</span></span>

<span data-ttu-id="7cb37-194">Les types qui implémentent IXmlDecryptor doivent avoir la valeur de l’une des deux constructeurs publics suivants :</span><span class="sxs-lookup"><span data-stu-id="7cb37-194">Types which implement IXmlDecryptor should have one of the following two public constructors:</span></span>

* <span data-ttu-id="7cb37-195">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="7cb37-195">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="7cb37-196">.ctor()</span><span class="sxs-lookup"><span data-stu-id="7cb37-196">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="7cb37-197">Le IServiceProvider passé au constructeur peut être null.</span><span class="sxs-lookup"><span data-stu-id="7cb37-197">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="7cb37-198">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="7cb37-198">IKeyEscrowSink</span></span>

<span data-ttu-id="7cb37-199">L’interface IKeyEscrowSink représente un type qui peut effectuer le dépôt d’informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="7cb37-199">The IKeyEscrowSink interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="7cb37-200">N’oubliez pas que les descripteurs sérialisés peuvent contenir des informations sensibles (telles que le matériel de chiffrement), c’est ce qui a conduit à l’introduction de la [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor) tapez en premier lieu.</span><span class="sxs-lookup"><span data-stu-id="7cb37-200">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="7cb37-201">Toutefois, les accidents se produisent et keyrings peut être supprimé ou endommagé.</span><span class="sxs-lookup"><span data-stu-id="7cb37-201">However, accidents happen, and keyrings can be deleted or become corrupted.</span></span>

<span data-ttu-id="7cb37-202">L’interface (Key escrow) offre une urgence, autoriser l’accès pour le XML sérialisé brut avant sa transformation par aucune configuré [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="7cb37-202">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it is transformed by any configured [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor).</span></span> <span data-ttu-id="7cb37-203">L’interface expose une API unique :</span><span class="sxs-lookup"><span data-stu-id="7cb37-203">The interface exposes a single API:</span></span>

* <span data-ttu-id="7cb37-204">Magasin (Guid keyId, élément de XElement)</span><span class="sxs-lookup"><span data-stu-id="7cb37-204">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="7cb37-205">C’est à l’implémentation de IKeyEscrowSink pour gérer l’élément fourni de manière sécurisée cohérente avec la stratégie d’entreprise.</span><span class="sxs-lookup"><span data-stu-id="7cb37-205">It is up to the IKeyEscrowSink implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="7cb37-206">Une implémentation possible peut être pour le récepteur tiers de confiance chiffrer l’élément XML à l’aide d’un certificat X.509 d’entreprise connu où la clé du certificat privé a été déposée ; le type CertificateXmlEncryptor peut aider à cela.</span><span class="sxs-lookup"><span data-stu-id="7cb37-206">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the CertificateXmlEncryptor type can assist with this.</span></span> <span data-ttu-id="7cb37-207">L’implémentation de IKeyEscrowSink est également chargée de rendre l’élément fourni de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="7cb37-207">The IKeyEscrowSink implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="7cb37-208">Par défaut aucun mécanisme de tiers de confiance n’est activé, bien que les administrateurs de serveur peuvent [cette configuration globalement](../configuration/machine-wide-policy.md#data-protection-configuration-machinewidepolicy).</span><span class="sxs-lookup"><span data-stu-id="7cb37-208">By default no escrow mechanism is enabled, though server administrators can [configure this globally](../configuration/machine-wide-policy.md#data-protection-configuration-machinewidepolicy).</span></span> <span data-ttu-id="7cb37-209">Il peut également être configuré par programmation la *IDataProtectionBuilder.AddKeyEscrowSink* méthode comme indiqué dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="7cb37-209">It can also be configured programmatically via the *IDataProtectionBuilder.AddKeyEscrowSink* method as shown in the sample below.</span></span> <span data-ttu-id="7cb37-210">Le *AddKeyEscrowSink* mise en miroir des surcharges de méthode le *IServiceCollection.AddSingleton* et *IServiceCollection.AddInstance* surcharges, comme IKeyEscrowSink instances sont destinées à être singletons.</span><span class="sxs-lookup"><span data-stu-id="7cb37-210">The *AddKeyEscrowSink* method overloads mirror the *IServiceCollection.AddSingleton* and *IServiceCollection.AddInstance* overloads, as IKeyEscrowSink instances are intended to be singletons.</span></span> <span data-ttu-id="7cb37-211">Si plusieurs instances de IKeyEscrowSink sont inscrits, chacun d’eux sera appelée lors de la génération de clés, donc les clés peuvent être déposées simultanément plusieurs mécanismes.</span><span class="sxs-lookup"><span data-stu-id="7cb37-211">If multiple IKeyEscrowSink instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="7cb37-212">Il n’existe aucune API pour la lecture des documents à partir d’une instance IKeyEscrowSink.</span><span class="sxs-lookup"><span data-stu-id="7cb37-212">There is no API to read material from an IKeyEscrowSink instance.</span></span> <span data-ttu-id="7cb37-213">Cela est cohérent avec la théorie de conception du mécanisme de tiers de confiance : il a conçu améliorer le matériel de clé pour une autorité approuvée, et étant donné que l’application n’est lui-même pas une autorité approuvée, il ne doit pas avoir accès à son propre matériel de clés.</span><span class="sxs-lookup"><span data-stu-id="7cb37-213">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="7cb37-214">L’exemple de code suivant illustre la création et de l’inscription d’un IKeyEscrowSink où les clés sont déposées telles que seuls les membres du « CONTOSODomain Admins » pour les récupérer.</span><span class="sxs-lookup"><span data-stu-id="7cb37-214">The following sample code demonstrates creating and registering an IKeyEscrowSink where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="7cb37-215">Pour exécuter cet exemple, vous devez être sur un domaine Windows 8 / machine Windows Server 2012 et le contrôleur de domaine doivent être Windows Server 2012 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="7cb37-215">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

<span data-ttu-id="7cb37-216">[!code-none[Main](key-management/samples/key-management-extensibility.cs)]</span><span class="sxs-lookup"><span data-stu-id="7cb37-216">[!code-none[Main](key-management/samples/key-management-extensibility.cs)]</span></span>

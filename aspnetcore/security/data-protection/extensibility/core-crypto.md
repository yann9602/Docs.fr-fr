---
title: "Extensibilité de chiffrement de base"
author: rick-anderson
description: "Explique les IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer et la fabrique de niveau supérieur."
keywords: ASP.NET Core, IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer
ms.author: riande
manager: wpickett
ms.date: 8/11/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 738bf6841b5364f89fa1bcdd11fc71d9be83c23b
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="core-cryptography-extensibility"></a><span data-ttu-id="83954-104">Extensibilité de chiffrement de base</span><span class="sxs-lookup"><span data-stu-id="83954-104">Core cryptography extensibility</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="83954-105">Les types qui implémentent des interfaces suivantes doivent être thread-safe pour les appelants plusieurs.</span><span class="sxs-lookup"><span data-stu-id="83954-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="83954-106">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="83954-106">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="83954-107">Le **IAuthenticatedEncryptor** interface est le bloc de construction de base du sous-système de chiffrement.</span><span class="sxs-lookup"><span data-stu-id="83954-107">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="83954-108">Il y a généralement une IAuthenticatedEncryptor par clé et l’instance IAuthenticatedEncryptor encapsule tous les services de chiffrement matériel de clé et algorithmiques informations nécessaires pour effectuer des opérations de chiffrement.</span><span class="sxs-lookup"><span data-stu-id="83954-108">There is generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="83954-109">Comme son nom l’indique, le type est chargé de fournir des services de chiffrement et déchiffrement authentifiés.</span><span class="sxs-lookup"><span data-stu-id="83954-109">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="83954-110">Il expose deux API suivantes.</span><span class="sxs-lookup"><span data-stu-id="83954-110">It exposes the following two APIs.</span></span>

* <span data-ttu-id="83954-111">Déchiffrer (ArraySegment<byte> texte chiffré, ArraySegment<byte> additionalAuthenticatedData) : byte]</span><span class="sxs-lookup"><span data-stu-id="83954-111">Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

* <span data-ttu-id="83954-112">Chiffrer (ArraySegment<byte> en texte clair, ArraySegment<byte> additionalAuthenticatedData) : byte]</span><span class="sxs-lookup"><span data-stu-id="83954-112">Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

<span data-ttu-id="83954-113">La méthode Encrypt retourne un objet blob qui inclut le texte en clair enciphered et une balise d’authentification.</span><span class="sxs-lookup"><span data-stu-id="83954-113">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="83954-114">La balise d’authentification doit englober les données authentifiées supplémentaires (AAD), bien que le AAD lui-même pas besoin d’être récupéré à partir de la charge utile finale.</span><span class="sxs-lookup"><span data-stu-id="83954-114">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="83954-115">La méthode Decrypt valide de la balise d’authentification et retourne la charge utile à déchiffrer.</span><span class="sxs-lookup"><span data-stu-id="83954-115">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="83954-116">Tous les échecs (à l’exception ArgumentNullException et similaire) doivent être homogénéisés à CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="83954-116">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="83954-117">L’instance IAuthenticatedEncryptor elle-même n’a pas réellement besoin contenir le matériel de clé.</span><span class="sxs-lookup"><span data-stu-id="83954-117">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="83954-118">Par exemple, l’implémentation peut déléguer à un module HSM pour toutes les opérations.</span><span class="sxs-lookup"><span data-stu-id="83954-118">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="83954-119">Comment créer un IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="83954-119">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="83954-120">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="83954-120">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="83954-121">Le **IAuthenticatedEncryptorFactory** interface représente un type qui sait comment créer un [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span><span class="sxs-lookup"><span data-stu-id="83954-121">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="83954-122">L’API est la suivante.</span><span class="sxs-lookup"><span data-stu-id="83954-122">Its API is as follows.</span></span>

* <span data-ttu-id="83954-123">CreateEncryptorInstance (clé IKey) : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="83954-123">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="83954-124">Pour n’importe quelle instance IKey donnée, tout chiffreur authentifié créé par sa méthode CreateEncryptorInstance doit-elle être considérées comme équivalentes, comme dans l’exemple de code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="83954-124">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="83954-125">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="83954-125">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="83954-126">Le **IAuthenticatedEncryptorDescriptor** interface représente un type qui sait comment créer un [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span><span class="sxs-lookup"><span data-stu-id="83954-126">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="83954-127">L’API est la suivante.</span><span class="sxs-lookup"><span data-stu-id="83954-127">Its API is as follows.</span></span>

* <span data-ttu-id="83954-128">CreateEncryptorInstance() : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="83954-128">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="83954-129">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="83954-129">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="83954-130">Comme IAuthenticatedEncryptor, une instance de IAuthenticatedEncryptorDescriptor est utilisée pour encapsuler une clé spécifique.</span><span class="sxs-lookup"><span data-stu-id="83954-130">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="83954-131">Cela signifie que pour n’importe quelle instance IAuthenticatedEncryptorDescriptor donnée, tout chiffreur authentifié créé par la méthode CreateEncryptorInstance doit être considérées comme équivalentes, comme dans l’exemple de code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="83954-131">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="83954-132">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x uniquement)</span><span class="sxs-lookup"><span data-stu-id="83954-132">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="83954-133">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="83954-133">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="83954-134">Le **IAuthenticatedEncryptorDescriptor** interface représente un type qui sait comment exporter au format XML.</span><span class="sxs-lookup"><span data-stu-id="83954-134">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="83954-135">L’API est la suivante.</span><span class="sxs-lookup"><span data-stu-id="83954-135">Its API is as follows.</span></span>

* <span data-ttu-id="83954-136">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="83954-136">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="83954-137">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="83954-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="83954-138">Sérialisation XML</span><span class="sxs-lookup"><span data-stu-id="83954-138">XML Serialization</span></span>

<span data-ttu-id="83954-139">La principale différence entre IAuthenticatedEncryptor et IAuthenticatedEncryptorDescriptor est que le descripteur sait comment créer le chiffreur et lui fournir les arguments valides.</span><span class="sxs-lookup"><span data-stu-id="83954-139">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="83954-140">Envisagez un IAuthenticatedEncryptor dont l’implémentation s’appuie sur SymmetricAlgorithm et élément KeyedHashAlgorithm impossible.</span><span class="sxs-lookup"><span data-stu-id="83954-140">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="83954-141">Les travaux de chiffreur sont d’utiliser ces types, mais il ne connaissez pas forcément ces types de provenant, afin qu’il ne peut pas écrire réellement une description appropriée de la création d’elle-même si l’application redémarre.</span><span class="sxs-lookup"><span data-stu-id="83954-141">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="83954-142">Le descripteur agit comme un niveau plus élevé en plus de cela.</span><span class="sxs-lookup"><span data-stu-id="83954-142">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="83954-143">Étant donné que le descripteur sait comment créer l’instance de chiffreur (par exemple, il sait comment créer les algorithmes requis), il peut sérialiser ces connaissances au format XML afin que l’instance de chiffreur peut être recréé après la réinitialisation d’une application.</span><span class="sxs-lookup"><span data-stu-id="83954-143">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="83954-144">Le descripteur peut être sérialisé via sa routine ExportToXml.</span><span class="sxs-lookup"><span data-stu-id="83954-144">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="83954-145">Cette routine retourne un XmlSerializedDescriptorInfo qui contient deux propriétés : la représentation sous forme de XElement de descripteur et le Type qui représente un [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) qui peut être permet de réactiver réactiver ce descripteur donné XElement correspondant.</span><span class="sxs-lookup"><span data-stu-id="83954-145">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="83954-146">Le descripteur sérialisé peut contenir des informations sensibles telles que du matériel de clé de chiffrement.</span><span class="sxs-lookup"><span data-stu-id="83954-146">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="83954-147">Le système de protection de données a une prise en charge intégrée pour le chiffrement des informations avant qu’il a rendu persistant dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="83954-147">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="83954-148">Pour tirer parti de cela, le descripteur doit marquer l’élément qui contient des informations sensibles avec le nom d’attribut « requiresEncryption » (xmlns « http://schemas.asp.net/2015/03/dataProtection »), valeur « true ».</span><span class="sxs-lookup"><span data-stu-id="83954-148">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "http://schemas.asp.net/2015/03/dataProtection"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="83954-149">Il existe une API d’assistance pour définir cet attribut.</span><span class="sxs-lookup"><span data-stu-id="83954-149">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="83954-150">Appelez la méthode d’extension que XElement.markasrequiresencryption() situé dans l’espace de noms Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span><span class="sxs-lookup"><span data-stu-id="83954-150">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="83954-151">Il peut également être cas où le descripteur sérialisé ne contient pas d’informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="83954-151">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="83954-152">Prenez de nouveau le cas d’une clé de chiffrement stockée dans un HSM.</span><span class="sxs-lookup"><span data-stu-id="83954-152">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="83954-153">Le matériel de clé ne peut pas écrire le descripteur lors de la sérialisation lui-même, car le HSM expose pas le matériel sous forme de texte en clair.</span><span class="sxs-lookup"><span data-stu-id="83954-153">The descriptor cannot write out the key material when serializing itself since the HSM will not expose the material in plaintext form.</span></span> <span data-ttu-id="83954-154">Au lieu de cela, le descripteur peut écrire la version de la clé encapsulée de la clé (si le module HSM autorise l’exportation de cette manière) ou l’identificateur unique du HSM pour la clé.</span><span class="sxs-lookup"><span data-stu-id="83954-154">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="83954-155">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="83954-155">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="83954-156">Le **IAuthenticatedEncryptorDescriptorDeserializer** interface représente un type qui sait comment désérialiser une instance IAuthenticatedEncryptorDescriptor à partir d’un XElement.</span><span class="sxs-lookup"><span data-stu-id="83954-156">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="83954-157">Il expose une méthode unique :</span><span class="sxs-lookup"><span data-stu-id="83954-157">It exposes a single method:</span></span>

* <span data-ttu-id="83954-158">ImportFromXml (élément XElement) : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="83954-158">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="83954-159">La méthode ImportFromXml prend XElement qui a été retourné par [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) et crée un équivalent de la IAuthenticatedEncryptorDescriptor d’origine.</span><span class="sxs-lookup"><span data-stu-id="83954-159">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="83954-160">Les types qui implémentent IAuthenticatedEncryptorDescriptorDeserializer doivent avoir la valeur de l’une des deux constructeurs publics suivants :</span><span class="sxs-lookup"><span data-stu-id="83954-160">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="83954-161">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="83954-161">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="83954-162">.ctor()</span><span class="sxs-lookup"><span data-stu-id="83954-162">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="83954-163">Le IServiceProvider passé au constructeur peut être null.</span><span class="sxs-lookup"><span data-stu-id="83954-163">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="83954-164">La fabrique de niveau supérieur</span><span class="sxs-lookup"><span data-stu-id="83954-164">The top-level factory</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="83954-165">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="83954-165">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="83954-166">Le **AlgorithmConfiguration** classe représente un type qui sait comment créer [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span><span class="sxs-lookup"><span data-stu-id="83954-166">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="83954-167">Il expose une API unique.</span><span class="sxs-lookup"><span data-stu-id="83954-167">It exposes a single API.</span></span>

* <span data-ttu-id="83954-168">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="83954-168">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="83954-169">Considérez AlgorithmConfiguration comme la fabrique de niveau supérieur.</span><span class="sxs-lookup"><span data-stu-id="83954-169">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="83954-170">La configuration sert de modèle.</span><span class="sxs-lookup"><span data-stu-id="83954-170">The configuration serves as a template.</span></span> <span data-ttu-id="83954-171">Elle encapsule les informations algorithmiques (par exemple, cette configuration génère descripteurs avec une clé principale de AES-128-GCM), mais il n’est pas encore associé à une clé spécifique.</span><span class="sxs-lookup"><span data-stu-id="83954-171">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="83954-172">Lorsque CreateNewDescriptor est appelée, nouvelle clé est créée uniquement pour cet appel et un nouveau IAuthenticatedEncryptorDescriptor est générée qui encapsule cette clé et les informations algorithmiques nécessaire pour utiliser le matériel.</span><span class="sxs-lookup"><span data-stu-id="83954-172">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="83954-173">Le matériel de clé peut être créé dans le logiciel (et dans la mémoire), il peut être créé et conservée dans un HSM et ainsi de suite.</span><span class="sxs-lookup"><span data-stu-id="83954-173">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="83954-174">Le point essentiel est que les deux appels à CreateNewDescriptor ne devraient jamais créer des instances IAuthenticatedEncryptorDescriptor équivalents.</span><span class="sxs-lookup"><span data-stu-id="83954-174">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="83954-175">Le type AlgorithmConfiguration sert tels que le point d’entrée pour les routines de création de la clé [substitution automatique de la clé](../implementation/key-management.md#data-protection-implementation-key-management).</span><span class="sxs-lookup"><span data-stu-id="83954-175">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#data-protection-implementation-key-management).</span></span> <span data-ttu-id="83954-176">Pour modifier l’implémentation pour toutes les futures clés, définir la propriété AuthenticatedEncryptorConfiguration dans KeyManagementOptions.</span><span class="sxs-lookup"><span data-stu-id="83954-176">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="83954-177">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="83954-177">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="83954-178">Le **IAuthenticatedEncryptorConfiguration** interface représente un type qui sait comment créer [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span><span class="sxs-lookup"><span data-stu-id="83954-178">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="83954-179">Il expose une API unique.</span><span class="sxs-lookup"><span data-stu-id="83954-179">It exposes a single API.</span></span>

* <span data-ttu-id="83954-180">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="83954-180">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="83954-181">Considérez IAuthenticatedEncryptorConfiguration comme la fabrique de niveau supérieur.</span><span class="sxs-lookup"><span data-stu-id="83954-181">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="83954-182">La configuration sert de modèle.</span><span class="sxs-lookup"><span data-stu-id="83954-182">The configuration serves as a template.</span></span> <span data-ttu-id="83954-183">Elle encapsule les informations algorithmiques (par exemple, cette configuration génère descripteurs avec une clé principale de AES-128-GCM), mais il n’est pas encore associé à une clé spécifique.</span><span class="sxs-lookup"><span data-stu-id="83954-183">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="83954-184">Lorsque CreateNewDescriptor est appelée, nouvelle clé est créée uniquement pour cet appel et un nouveau IAuthenticatedEncryptorDescriptor est générée qui encapsule cette clé et les informations algorithmiques nécessaire pour utiliser le matériel.</span><span class="sxs-lookup"><span data-stu-id="83954-184">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="83954-185">Le matériel de clé peut être créé dans le logiciel (et dans la mémoire), il peut être créé et conservée dans un HSM et ainsi de suite.</span><span class="sxs-lookup"><span data-stu-id="83954-185">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="83954-186">Le point essentiel est que les deux appels à CreateNewDescriptor ne devraient jamais créer des instances IAuthenticatedEncryptorDescriptor équivalents.</span><span class="sxs-lookup"><span data-stu-id="83954-186">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="83954-187">Le type IAuthenticatedEncryptorConfiguration sert tels que le point d’entrée pour les routines de création de la clé [substitution automatique de la clé](../implementation/key-management.md#data-protection-implementation-key-management).</span><span class="sxs-lookup"><span data-stu-id="83954-187">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#data-protection-implementation-key-management).</span></span> <span data-ttu-id="83954-188">Pour modifier l’implémentation pour toutes les futures clés, inscrivez un singleton IAuthenticatedEncryptorConfiguration dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="83954-188">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---

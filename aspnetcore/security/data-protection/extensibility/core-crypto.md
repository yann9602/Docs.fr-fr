---
title: "Extensibilité de chiffrement de base"
author: rick-anderson
description: "Explique les IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer et la fabrique de niveau supérieur."
manager: wpickett
ms.author: riande
ms.date: 8/11/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: ead4012236244d88cff0b0520d000d89f93f3355
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="core-cryptography-extensibility"></a>Extensibilité de chiffrement de base

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Les types qui implémentent des interfaces suivantes doivent être thread-safe pour les appelants plusieurs.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

Le **IAuthenticatedEncryptor** interface est le bloc de construction de base du sous-système de chiffrement. Il y a généralement une IAuthenticatedEncryptor par clé et l’instance IAuthenticatedEncryptor encapsule tous les services de chiffrement matériel de clé et algorithmiques informations nécessaires pour effectuer des opérations de chiffrement.

Comme son nom l’indique, le type est chargé de fournir des services de chiffrement et déchiffrement authentifiés. Il expose deux API suivantes.

* Déchiffrer (ArraySegment<byte> texte chiffré, ArraySegment<byte> additionalAuthenticatedData) : byte]

* Chiffrer (ArraySegment<byte> en texte clair, ArraySegment<byte> additionalAuthenticatedData) : byte]

La méthode Encrypt retourne un objet blob qui inclut le texte en clair enciphered et une balise d’authentification. La balise d’authentification doit englober les données authentifiées supplémentaires (AAD), bien que le AAD lui-même pas besoin d’être récupéré à partir de la charge utile finale. La méthode Decrypt valide de la balise d’authentification et retourne la charge utile à déchiffrer. Tous les échecs (à l’exception ArgumentNullException et similaire) doivent être homogénéisés à CryptographicException.

> [!NOTE]
> L’instance IAuthenticatedEncryptor elle-même n’a pas réellement besoin contenir le matériel de clé. Par exemple, l’implémentation peut déléguer à un module HSM pour toutes les opérations.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Comment créer un IAuthenticatedEncryptor

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Le **IAuthenticatedEncryptorFactory** interface représente un type qui sait comment créer un [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance. L’API est la suivante.

* CreateEncryptorInstance (clé IKey) : IAuthenticatedEncryptor

Pour n’importe quelle instance IKey donnée, tout chiffreur authentifié créé par sa méthode CreateEncryptorInstance doit-elle être considérées comme équivalentes, comme dans l’exemple de code ci-dessous.

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Le **IAuthenticatedEncryptorDescriptor** interface représente un type qui sait comment créer un [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance. L’API est la suivante.

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

Comme IAuthenticatedEncryptor, une instance de IAuthenticatedEncryptorDescriptor est utilisée pour encapsuler une clé spécifique. Cela signifie que pour n’importe quelle instance IAuthenticatedEncryptorDescriptor donnée, tout chiffreur authentifié créé par la méthode CreateEncryptorInstance doit être considérées comme équivalentes, comme dans l’exemple de code ci-dessous.

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x uniquement)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Le **IAuthenticatedEncryptorDescriptor** interface représente un type qui sait comment exporter au format XML. L’API est la suivante.

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>Sérialisation XML

La principale différence entre IAuthenticatedEncryptor et IAuthenticatedEncryptorDescriptor est que le descripteur sait comment créer le chiffreur et lui fournir les arguments valides. Envisagez un IAuthenticatedEncryptor dont l’implémentation s’appuie sur SymmetricAlgorithm et élément KeyedHashAlgorithm impossible. Les travaux de chiffreur sont d’utiliser ces types, mais il ne connaissez pas forcément ces types de provenant, afin qu’il ne peut pas écrire réellement une description appropriée de la création d’elle-même si l’application redémarre. Le descripteur agit comme un niveau plus élevé en plus de cela. Étant donné que le descripteur sait comment créer l’instance de chiffreur (par exemple, il sait comment créer les algorithmes requis), il peut sérialiser ces connaissances au format XML afin que l’instance de chiffreur peut être recréé après la réinitialisation d’une application.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

Le descripteur peut être sérialisé via sa routine ExportToXml. Cette routine retourne un XmlSerializedDescriptorInfo qui contient deux propriétés : la représentation sous forme de XElement de descripteur et le Type qui représente un [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) qui peut être permet de réactiver réactiver ce descripteur donné XElement correspondant.

Le descripteur sérialisé peut contenir des informations sensibles telles que du matériel de clé de chiffrement. Le système de protection de données a une prise en charge intégrée pour le chiffrement des informations avant qu’il a rendu persistant dans le stockage. Pour tirer parti de cela, le descripteur doit marquer l’élément qui contient des informations sensibles avec le nom d’attribut « requiresEncryption » (xmlns « http://schemas.asp.net/2015/03/dataProtection »), valeur « true ».

>[!TIP]
> Il existe une API d’assistance pour définir cet attribut. Appelez la méthode d’extension que XElement.markasrequiresencryption() situé dans l’espace de noms Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.

Il peut également être cas où le descripteur sérialisé ne contient pas d’informations sensibles. Prenez de nouveau le cas d’une clé de chiffrement stockée dans un HSM. Le matériel de clé ne peut pas écrire le descripteur lors de la sérialisation lui-même, car le HSM ne sera pas exposer les informations sous forme de texte en clair. Au lieu de cela, le descripteur peut écrire la version de la clé encapsulée de la clé (si le module HSM autorise l’exportation de cette manière) ou l’identificateur unique du HSM pour la clé.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

Le **IAuthenticatedEncryptorDescriptorDeserializer** interface représente un type qui sait comment désérialiser une instance IAuthenticatedEncryptorDescriptor à partir d’un XElement. Il expose une méthode unique :

* ImportFromXml (élément XElement) : IAuthenticatedEncryptorDescriptor

La méthode ImportFromXml prend XElement qui a été retourné par [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) et crée un équivalent de la IAuthenticatedEncryptorDescriptor d’origine.

Les types qui implémentent IAuthenticatedEncryptorDescriptorDeserializer doivent avoir la valeur de l’une des deux constructeurs publics suivants :

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> Le IServiceProvider passé au constructeur peut être null.

## <a name="the-top-level-factory"></a>La fabrique de niveau supérieur

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Le **AlgorithmConfiguration** classe représente un type qui sait comment créer [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances. Il expose une API unique.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Considérez AlgorithmConfiguration comme la fabrique de niveau supérieur. La configuration sert de modèle. Elle encapsule les informations algorithmiques (par exemple, cette configuration génère descripteurs avec une clé principale de AES-128-GCM), mais il n’a pas encore associé à une clé spécifique.

Lorsque CreateNewDescriptor est appelée, nouvelle clé est créée uniquement pour cet appel et un nouveau IAuthenticatedEncryptorDescriptor est générée qui encapsule cette clé et les informations algorithmiques nécessaire pour utiliser le matériel. Le matériel de clé peut être créé dans le logiciel (et dans la mémoire), il peut être créé et conservée dans un HSM et ainsi de suite. Le point essentiel est que les deux appels à CreateNewDescriptor ne devraient jamais créer des instances IAuthenticatedEncryptorDescriptor équivalents.

Le type AlgorithmConfiguration sert tels que le point d’entrée pour les routines de création de la clé [substitution automatique de la clé](../implementation/key-management.md#key-expiration-and-rolling). Pour modifier l’implémentation pour toutes les futures clés, définir la propriété AuthenticatedEncryptorConfiguration dans KeyManagementOptions.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Le **IAuthenticatedEncryptorConfiguration** interface représente un type qui sait comment créer [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances. Il expose une API unique.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Considérez IAuthenticatedEncryptorConfiguration comme la fabrique de niveau supérieur. La configuration sert de modèle. Elle encapsule les informations algorithmiques (par exemple, cette configuration génère descripteurs avec une clé principale de AES-128-GCM), mais il n’a pas encore associé à une clé spécifique.

Lorsque CreateNewDescriptor est appelée, nouvelle clé est créée uniquement pour cet appel et un nouveau IAuthenticatedEncryptorDescriptor est générée qui encapsule cette clé et les informations algorithmiques nécessaire pour utiliser le matériel. Le matériel de clé peut être créé dans le logiciel (et dans la mémoire), il peut être créé et conservée dans un HSM et ainsi de suite. Le point essentiel est que les deux appels à CreateNewDescriptor ne devraient jamais créer des instances IAuthenticatedEncryptorDescriptor équivalents.

Le type IAuthenticatedEncryptorConfiguration sert tels que le point d’entrée pour les routines de création de la clé [substitution automatique de la clé](../implementation/key-management.md#key-expiration-and-rolling). Pour modifier l’implémentation pour toutes les futures clés, inscrivez un singleton IAuthenticatedEncryptorConfiguration dans le conteneur de service.

---

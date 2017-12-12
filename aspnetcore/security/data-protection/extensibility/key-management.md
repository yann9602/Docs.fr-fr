---
title: "Extensibilité de la gestion de clés"
author: rick-anderson
description: "Ce document décrit l’extensibilité de la gestion de clés de protection de données ASP.NET Core."
keywords: "ASP.NET Core, protection des données, la gestion de clés"
ms.author: riande
manager: wpickett
ms.date: 11/22/2017
ms.topic: article
ms.assetid: 3606b251-8324-4485-8d52-582a2cd5cffb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 0702e13163c0208e9d2863e711b02ffb257f6260
ms.sourcegitcommit: e641c5794525f983485621860926d8ab4e7360c8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="key-management-extensibility"></a>Extensibilité de la gestion de clés

<a name="data-protection-extensibility-key-management"></a>

>[!TIP]
> Lecture la [gestion de clés](../implementation/key-management.md#data-protection-implementation-key-management) section avant de lire cette section, car il décrit certains des concepts fondamentaux de ces API.

>[!WARNING]
> Les types qui implémentent des interfaces suivantes doivent être thread-safe pour les appelants plusieurs.

## <a name="key"></a>Touche

Le `IKey` interface est la représentation sous forme de base d’une clé dans le système de cryptage par. L’expression clé est utilisé ici dans le sens abstrait, pas dans le sens littéral « chiffrement de matériel de clé ». Une clé a les propriétés suivantes :

* Dates d’expiration, la création et l’activation

* État de révocation

* Identificateur de clé (GUID)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

En outre, `IKey` expose un `CreateEncryptor` méthode qui peut être utilisé pour créer un [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance liée à cette clé.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

En outre, `IKey` expose un `CreateEncryptorInstance` méthode qui peut être utilisé pour créer un [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance liée à cette clé.

---

> [!NOTE]
> Il n’existe aucune API pour extraire le matériel de chiffrement brut à partir d’un `IKey` instance.

## <a name="ikeymanager"></a>IKeyManager

Le `IKeyManager` interface représente un objet chargé de stockage général de la clé, la récupération et la manipulation. Il expose trois opérations de niveau supérieur :

* Créer une nouvelle clé et la rendre persistante dans le stockage.

* Obtenir toutes les clés de stockage.

* Révoquer une ou plusieurs clés et rendre persistantes les informations de révocation pour le stockage.

>[!WARNING]
> L’écriture d’un `IKeyManager` est une tâche très avancée, et la plupart des développeurs ne doit pas essayer il. Au lieu de cela, la plupart des développeurs doit bénéficier des installations offertes par le [XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager) classe.

<a name="data-protection-extensibility-key-management-xmlkeymanager"></a>

## <a name="xmlkeymanager"></a>XmlKeyManager

Le `XmlKeyManager` type est l’implémentation concrète de la boîte de réception de `IKeyManager`. Il fournit plusieurs installations utiles, y compris le dépôt de clé et le chiffrement des clés au repos. Dans ce système, les clés sont représentées comme des éléments XML (en particulier, [XElement](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager`dépend de plusieurs autres composants au cours de l’exécution de ses tâches :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

* `AlgorithmConfiguration`, qui déterminent les algorithmes utilisés par les nouvelles clés.

* `IXmlRepository`, qui contrôle où les clés sont rendues persistantes dans le stockage.

* `IXmlEncryptor`[facultatif], ce qui permet de chiffrer les clés au repos.

* `IKeyEscrowSink`[facultatif], qui fournit des services de clé (Key escrow).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* `IXmlRepository`, qui contrôle où les clés sont rendues persistantes dans le stockage.

* `IXmlEncryptor`[facultatif], ce qui permet de chiffrer les clés au repos.

* `IKeyEscrowSink`[facultatif], qui fournit des services de clé (Key escrow).

---

Vous trouverez ci-dessous des diagrammes de haut niveau qui indiquent la façon dont ces composants sont reliées entre elles au sein de `XmlKeyManager`.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ![Création de la clé](key-management/_static/keycreation2.png)

   *La création de clé / CreateNewKey*

Dans l’implémentation de `CreateNewKey`, le `AlgorithmConfiguration` composant est utilisé pour créer un unique `IAuthenticatedEncryptorDescriptor`, lequel est ensuite sérialisé au format XML. Si un récepteur de clé (Key escrow) est présent, les données XML brutes (non chiffré) est fourni pour le récepteur pour le stockage à long terme. Le code XML non chiffré est ensuite exécuter via un `IXmlEncryptor` (si nécessaire) pour générer le document XML chiffré. Ce document chiffré est rendue persistante dans un stockage à long terme via la `IXmlRepository`. (Si aucun `IXmlEncryptor` est configuré, le document non chiffré est conservé dans le `IXmlRepository`.)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ![Création de la clé](key-management/_static/keycreation1.png)

   *La création de clé / CreateNewKey*

Dans l’implémentation de `CreateNewKey`, le `IAuthenticatedEncryptorConfiguration` composant est utilisé pour créer un unique `IAuthenticatedEncryptorDescriptor`, lequel est ensuite sérialisé au format XML. Si un récepteur de clé (Key escrow) est présent, les données XML brutes (non chiffré) est fourni pour le récepteur pour le stockage à long terme. Le code XML non chiffré est ensuite exécuter via un `IXmlEncryptor` (si nécessaire) pour générer le document XML chiffré. Ce document chiffré est rendue persistante dans un stockage à long terme via la `IXmlRepository`. (Si aucun `IXmlEncryptor` est configuré, le document non chiffré est conservé dans le `IXmlRepository`.)

---

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ![Récupération de clés](key-management/_static/keyretrieval2.png)
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ![Récupération de clés](key-management/_static/keyretrieval1.png)

---

   *Récupération de clé / GetAllKeys*

Dans l’implémentation de `GetAllKeys`, représentant les clés des documents XML et révocations sont lus à partir de l’objet sous-jacent `IXmlRepository`. Si ces documents sont chiffrées, le système sera automatiquement les déchiffrer. `XmlKeyManager`crée le `IAuthenticatedEncryptorDescriptorDeserializer` instances pour désérialiser les documents de nouveau `IAuthenticatedEncryptorDescriptor` instances, qui sont ensuite encapsulées dans individuels `IKey` instances. Cette collection de `IKey` instances est retourné à l’appelant.

Vous trouverez plus d’informations sur les éléments XML particuliers dans le [document au format de stockage de clés](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

Le `IXmlRepository` interface représente un type qui peut faire persister XML à et récupérer des données XML à partir d’un magasin de stockage. Il expose deux API :

* GetAllElements() : IReadOnlyCollection<XElement>

* StoreElement (élément de XElement, chaîne friendlyName)

Les implémentations de `IXmlRepository` n’avez pas besoin analyser le XML en passant par leur intermédiaire. Qu’ils doivent traiter les documents XML comme opaque et laisser les couches supérieures vous inquiétez pas sur la génération et l’analyse de documents.

Il existe deux types intégrés concrètes qui implémentent `IXmlRepository`: `FileSystemXmlRepository` et `RegistryXmlRepository`. Consultez le [document de fournisseurs de stockage de clés](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers) pour plus d’informations. Enregistrement personnalisé `IXmlRepository` serait de manière appropriée à utiliser un magasin de stockage, par exemple, stockage d’objets Blob Azure.

Pour modifier le référentiel par défaut à l’échelle de l’application, enregistrer personnalisé `IXmlRepository` instance :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ```csharp
   services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
   ```
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ```csharp
   services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
   ```

---

<a name="data-protection-extensibility-key-management-ixmlencryptor"></a>

## <a name="ixmlencryptor"></a>IXmlEncryptor

Le `IXmlEncryptor` interface représente un type qui peut chiffrer un élément XML de texte en clair. Il expose une API unique :

* Chiffrer (plaintextElement XElement) : EncryptedXmlInfo

Si elle est sérialisée `IAuthenticatedEncryptorDescriptor` contient des éléments de la mention « exige un chiffrement », puis `XmlKeyManager` exécute ces éléments configuré `IXmlEncryptor`de `Encrypt` (méthode), qui rendront l’élément enciphered plutôt que élément de texte en clair à le `IXmlRepository`. La sortie de la `Encrypt` méthode est un `EncryptedXmlInfo` objet. Cet objet est un wrapper qui contient les deux résultant enciphered `XElement` et le Type qui représente un `IXmlDecryptor` qui peut être utilisé pour déchiffrer l’élément correspondant.

Il existe quatre types intégrés concrètes qui implémentent `IXmlEncryptor`:
* `CertificateXmlEncryptor`
* `DpapiNGXmlEncryptor`
* `DpapiXmlEncryptor`
* `NullXmlEncryptor`

Consultez le [chiffrement à clé au niveau du document de rest](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) pour plus d’informations.

Pour modifier le mécanisme de clé chiffrement au repos à l’échelle de l’application par défaut, enregistrer personnalisé `IXmlEncryptor` instance :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ```csharp
   services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
   ```
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ```csharp
   services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
   ```

---

## <a name="ixmldecryptor"></a>IXmlDecryptor

Le `IXmlDecryptor` interface représente un type qui sait comment déchiffrer un `XElement` qui a été enciphered via un `IXmlEncryptor`. Il expose une API unique :

* Déchiffrer (encryptedElement XElement) : XElement

Le `Decrypt` méthode annule le chiffrement effectué par `IXmlEncryptor.Encrypt`. En règle générale, chaque concrète `IXmlEncryptor` implémentation aura un concrète correspondante `IXmlDecryptor` implémentation.

Les types qui implémentent `IXmlDecryptor` doivent avoir l’une des deux constructeurs publics suivants :

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> Le `IServiceProvider` passé au constructeur peut être null.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

Le `IKeyEscrowSink` interface représente un type qui peut effectuer le dépôt d’informations sensibles. N’oubliez pas que les descripteurs sérialisés peuvent contenir des informations sensibles (telles que le matériel de chiffrement), c’est ce qui a conduit à l’introduction de la [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor) tapez en premier lieu. Toutefois, les accidents se produisent et anneaux de clé peut être supprimés ou endommagés.

L’interface (Key escrow) offre une urgence, autoriser l’accès pour le XML sérialisé brut avant sa transformation par aucune configuré [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor). L’interface expose une API unique :

* Magasin (Guid keyId, élément de XElement)

C’est à le `IKeyEscrowSink` implémentation pour gérer l’élément fourni de manière sécurisée cohérente avec la stratégie d’entreprise. Une implémentation possible peut être pour le récepteur tiers de confiance chiffrer l’élément XML à l’aide d’un certificat X.509 d’entreprise connu où la clé du certificat privé a été déposée ; le `CertificateXmlEncryptor` type peut vous aider à cela. Le `IKeyEscrowSink` implémentation est également chargée de rendre l’élément fourni de manière appropriée.

Par défaut aucun mécanisme de tiers de confiance n’est activé, bien que les administrateurs de serveur peuvent [cette configuration globalement](xref:security/data-protection/configuration/machine-wide-policy). Il peut également être configuré par programmation la `IDataProtectionBuilder.AddKeyEscrowSink` méthode comme indiqué dans l’exemple ci-dessous. Le `AddKeyEscrowSink` mise en miroir des surcharges de méthode le `IServiceCollection.AddSingleton` et `IServiceCollection.AddInstance` surcharges, comme `IKeyEscrowSink` instances sont destinées à être singletons. Si plusieurs `IKeyEscrowSink` inscrit des instances, chacun d’eux sera appelée lors de la génération de clés, donc les clés peuvent être déposées simultanément plusieurs mécanismes.

Il n’existe aucune API pour la lecture de documents à partir d’un `IKeyEscrowSink` instance. Cela est cohérent avec la théorie de conception du mécanisme de tiers de confiance : il a conçu améliorer le matériel de clé pour une autorité approuvée, et étant donné que l’application n’est lui-même pas une autorité approuvée, il ne doit pas avoir accès à son propre matériel de clés.

L’exemple de code suivant montre comment créer et inscrire un `IKeyEscrowSink` où les clés sont déposées telles que seuls les membres de « Administrateurs CONTOSODomain » pour les récupérer.

> [!NOTE]
> Pour exécuter cet exemple, vous devez être sur un domaine Windows 8 / machine Windows Server 2012 et le contrôleur de domaine doivent être Windows Server 2012 ou version ultérieure.

[!code-csharp[Main](key-management/samples/key-management-extensibility.cs)]

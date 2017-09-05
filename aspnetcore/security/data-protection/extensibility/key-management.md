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
ms.openlocfilehash: fb74905660015b9a83503e1f74b25c66ae9df9e3
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2017
---
# <a name="key-management-extensibility"></a>Extensibilité de la gestion de clés

<a name=data-protection-extensibility-key-management></a>

>[!TIP]
> Lecture la [gestion de clés](../implementation/key-management.md#data-protection-implementation-key-management) section avant de lire cette section, car il décrit certains des concepts fondamentaux de ces API.

>[!WARNING]
> Les types qui implémentent des interfaces suivantes doivent être thread-safe pour les appelants plusieurs.

## <a name="key"></a>Touche

L’interface IKey est la représentation sous forme de base d’une clé dans le système de cryptage par. L’expression clé est utilisé ici dans le sens abstrait, pas dans le sens littéral « chiffrement de matériel de clé ». Une clé a les propriétés suivantes :

* Dates d’expiration, la création et l’activation

* État de révocation

* Identificateur de clé (GUID)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

En outre, IKey expose une méthode CreateEncryptor qui peut être utilisée pour créer un [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance liée à cette clé.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

En outre, IKey expose une méthode CreateEncryptorInstance qui peut être utilisée pour créer un [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance liée à cette clé.

---

> [!NOTE]
> Il n’existe aucune API pour récupérer les matières premières de chiffrement à partir d’une instance IKey.

## <a name="ikeymanager"></a>IKeyManager

L’interface IKeyManager représente un objet responsable de la manipulation, l’extraction et stockage de clés général. Il expose trois opérations de niveau supérieur :

* Créer une nouvelle clé et la rendre persistante dans le stockage.

* Obtenir toutes les clés de stockage.

* Révoquer une ou plusieurs clés et rendre persistantes les informations de révocation pour le stockage.

>[!WARNING]
> L’écriture d’un IKeyManager est une tâche très avancée et la plupart des développeurs ne doit pas essayer il. Au lieu de cela, la plupart des développeurs doit bénéficier des installations offertes par le [XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager) classe.

<a name=data-protection-extensibility-key-management-xmlkeymanager></a>

## <a name="xmlkeymanager"></a>XmlKeyManager

Le type de XmlKeyManager est l’implémentation concrète de la boîte de réception de IKeyManager. Il fournit plusieurs installations utiles, y compris le dépôt de clé et le chiffrement des clés au repos. Dans ce système, les clés sont représentées comme des éléments XML (en particulier, [XElement](https://msdn.microsoft.com/library/system.xml.linq.xelement(v=vs.110).aspx)).

XmlKeyManager dépend de plusieurs autres composants au cours de l’exécution de ses tâches :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

* AlgorithmConfiguration, qui détermine les algorithmes utilisés par les nouvelles clés.

* IXmlRepository, qui contrôle où les clés sont rendues persistantes dans le stockage.

* IXmlEncryptor de [facultatif], ce qui permet de chiffrer les clés au repos.

* IKeyEscrowSink de [facultatif], qui fournit des services de clé (Key escrow).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* IXmlRepository, qui contrôle où les clés sont rendues persistantes dans le stockage.

* IXmlEncryptor de [facultatif], ce qui permet de chiffrer les clés au repos.

* IKeyEscrowSink de [facultatif], qui fournit des services de clé (Key escrow).

---

Vous trouverez ci-dessous des diagrammes de haut niveau qui indiquent la façon dont ces composants sont reliés dans XmlKeyManager.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ![Création de la clé](key-management/_static/keycreation2.png)

   *La création de clé / CreateNewKey*

Dans l’implémentation de CreateNewKey, le composant AlgorithmConfiguration est utilisé pour créer un IAuthenticatedEncryptorDescriptor unique, qui est ensuite sérialisé au format XML. Si un récepteur de clé (Key escrow) est présent, les données XML brutes (non chiffré) est fourni pour le récepteur pour le stockage à long terme. Le code XML non chiffré est alors exécuté via un IXmlEncryptor (si nécessaire) pour générer le document XML chiffré. Ce document chiffré est enregistré sur le stockage à long terme via la IXmlRepository. (Si aucun IXmlEncryptor n’est configuré, le document non chiffré est persistante dans le IXmlRepository).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ![Création de la clé](key-management/_static/keycreation1.png)

   *La création de clé / CreateNewKey*

Dans l’implémentation de CreateNewKey, le composant IAuthenticatedEncryptorConfiguration est utilisé pour créer un IAuthenticatedEncryptorDescriptor unique, qui est ensuite sérialisé au format XML. Si un récepteur de clé (Key escrow) est présent, les données XML brutes (non chiffré) est fourni pour le récepteur pour le stockage à long terme. Le code XML non chiffré est alors exécuté via un IXmlEncryptor (si nécessaire) pour générer le document XML chiffré. Ce document chiffré est enregistré sur le stockage à long terme via la IXmlRepository. (Si aucun IXmlEncryptor n’est configuré, le document non chiffré est persistante dans le IXmlRepository).

---

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ![Récupération de clés](key-management/_static/keyretrieval2.png)
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ![Récupération de clés](key-management/_static/keyretrieval1.png)

---

   *Récupération de clé / GetAllKeys*

Dans l’implémentation de GetAllKeys, les documents XML représentant des clés et des révocations sont lus à partir de la IXmlRepository sous-jacente. Si ces documents sont chiffrées, le système sera automatiquement les déchiffrer. XmlKeyManager crée les instances IAuthenticatedEncryptorDescriptorDeserializer appropriés pour désérialiser les documents en instances IAuthenticatedEncryptorDescriptor, puis enveloppés dans des instances IKey individuelles. Cette collection d’instances de IKey est retournée à l’appelant.

Vous trouverez plus d’informations sur les éléments XML particuliers dans le [document au format de stockage de clés](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

L’interface IXmlRepository représente un type qui peut faire persister XML à et récupérer des données XML à partir d’un magasin de stockage. Il expose deux API :

* GetAllElements() : IReadOnlyCollection<XElement>

* StoreElement (élément de XElement, chaîne friendlyName)

Les implémentations de IXmlRepository n’avez pas besoin analyser le XML en passant par leur intermédiaire. Qu’ils doivent traiter les documents XML comme opaque et laisser les couches supérieures vous inquiétez pas sur la génération et l’analyse de documents.

Il existe deux types intégrés concrètes qui implémentent IXmlRepository : FileSystemXmlRepository et RegistryXmlRepository. Consultez le [document de fournisseurs de stockage de clés](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers) pour plus d’informations. L’inscription d’un IXmlRepository personnalisé serait être la manière appropriée pour utiliser un autre magasin de stockage, par exemple, stockage d’objets Blob Azure. Pour modifier l’échelle application du référentiel par défaut, inscrivez un singleton personnalisé IXmlRepository dans le fournisseur de services.

<a name=data-protection-extensibility-key-management-ixmlencryptor></a>

## <a name="ixmlencryptor"></a>IXmlEncryptor

L’interface IXmlEncryptor représente un type qui peut chiffrer un élément XML de texte en clair. Il expose une API unique :

* Chiffrer (plaintextElement XElement) : EncryptedXmlInfo

Si un IAuthenticatedEncryptorDescriptor sérialisé contient des éléments marqués comme « requiert le chiffrement », puis XmlKeyManager sera exécuté ces éléments via la méthode de chiffrer du IXmlEncryptor configuré et il persistera plutôt l’élément enciphered à l’élément de texte en clair à l’IXmlRepository. La sortie de la méthode de chiffrement est un objet EncryptedXmlInfo. Cet objet est un wrapper qui contient le XElement enciphered résultant et le Type qui représente un IXmlDecryptor qui peut être utilisé pour déchiffrer l’élément correspondant.

Il existe quatre types intégrés concrètes qui implémentent IXmlEncryptor : CertificateXmlEncryptor, DpapiNGXmlEncryptor, DpapiXmlEncryptor et NullXmlEncryptor. Consultez le [chiffrement à clé au niveau du document de rest](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) pour plus d’informations. Pour modifier le mécanisme de clé chiffrement au repos à l’échelle de l’application par défaut, inscrivez un singleton personnalisé IXmlEncryptor dans le fournisseur de services.

## <a name="ixmldecryptor"></a>IXmlDecryptor

L’interface IXmlDecryptor représente un type qui sait comment déchiffrer un XElement qui a été enciphered via un IXmlEncryptor. Il expose une API unique :

* Déchiffrer (encryptedElement XElement) : XElement

La méthode Decrypt annule le chiffrement effectué par IXmlEncryptor.Encrypt. Chaque implémentation concrète de IXmlEncryptor ont généralement une implémentation IXmlDecryptor concrète correspondante.

Les types qui implémentent IXmlDecryptor doivent avoir la valeur de l’une des deux constructeurs publics suivants :

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> Le IServiceProvider passé au constructeur peut être null.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

L’interface IKeyEscrowSink représente un type qui peut effectuer le dépôt d’informations sensibles. N’oubliez pas que les descripteurs sérialisés peuvent contenir des informations sensibles (telles que le matériel de chiffrement), c’est ce qui a conduit à l’introduction de la [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor) tapez en premier lieu. Toutefois, les accidents se produisent et keyrings peut être supprimé ou endommagé.

L’interface (Key escrow) offre une urgence, autoriser l’accès pour le XML sérialisé brut avant sa transformation par aucune configuré [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor). L’interface expose une API unique :

* Magasin (Guid keyId, élément de XElement)

C’est à l’implémentation de IKeyEscrowSink pour gérer l’élément fourni de manière sécurisée cohérente avec la stratégie d’entreprise. Une implémentation possible peut être pour le récepteur tiers de confiance chiffrer l’élément XML à l’aide d’un certificat X.509 d’entreprise connu où la clé du certificat privé a été déposée ; le type CertificateXmlEncryptor peut aider à cela. L’implémentation de IKeyEscrowSink est également chargée de rendre l’élément fourni de manière appropriée.

Par défaut aucun mécanisme de tiers de confiance n’est activé, bien que les administrateurs de serveur peuvent [cette configuration globalement](../configuration/machine-wide-policy.md#data-protection-configuration-machinewidepolicy). Il peut également être configuré par programmation la *IDataProtectionBuilder.AddKeyEscrowSink* méthode comme indiqué dans l’exemple ci-dessous. Le *AddKeyEscrowSink* mise en miroir des surcharges de méthode le *IServiceCollection.AddSingleton* et *IServiceCollection.AddInstance* surcharges, comme IKeyEscrowSink instances sont destinées à être singletons. Si plusieurs instances de IKeyEscrowSink sont inscrits, chacun d’eux sera appelée lors de la génération de clés, donc les clés peuvent être déposées simultanément plusieurs mécanismes.

Il n’existe aucune API pour la lecture des documents à partir d’une instance IKeyEscrowSink. Cela est cohérent avec la théorie de conception du mécanisme de tiers de confiance : il a conçu améliorer le matériel de clé pour une autorité approuvée, et étant donné que l’application n’est lui-même pas une autorité approuvée, il ne doit pas avoir accès à son propre matériel de clés.

L’exemple de code suivant illustre la création et de l’inscription d’un IKeyEscrowSink où les clés sont déposées telles que seuls les membres du « CONTOSODomain Admins » pour les récupérer.

> [!NOTE]
> Pour exécuter cet exemple, vous devez être sur un domaine Windows 8 / machine Windows Server 2012 et le contrôleur de domaine doivent être Windows Server 2012 ou version ultérieure.

[!code-none[Main](key-management/samples/key-management-extensibility.cs)]

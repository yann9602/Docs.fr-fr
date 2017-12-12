---
title: "Configuration de Protection des données dans ASP.NET Core"
author: rick-anderson
description: "Découvrez comment configurer la Protection des données dans ASP.NET Core."
keywords: "ASP.NET Core, protection des données, la configuration"
ms.author: riande
manager: wpickett
ms.date: 07/17/2017
ms.topic: article
ms.assetid: 0e4881a3-a94d-4e35-9c1c-f025d65dcff0
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 4713c2bed04af784e74586daa10ec847262a1345
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="configuring-data-protection-in-aspnet-core"></a>Configuration de Protection des données dans ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Lorsque le système de Protection des données est initialisé, il s’applique [paramètres par défaut](xref:security/data-protection/configuration/default-settings) en fonction de l’environnement d’exploitation. Ces paramètres sont généralement appropriés pour les applications qui s’exécutent sur un seul ordinateur. Il existe des cas où un développeur peut souhaiter modifier les paramètres par défaut, peut-être parce que son application est répartie sur plusieurs ordinateurs ou pour des raisons de compatibilité. Pour ces scénarios, le système de Protection des données offre une API de configuration complet.

Il existe une méthode d’extension [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) qui retourne un [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder`expose des méthodes d’extension que vous pouvez chaîner des options pour configurer la Protection des données.

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Pour stocker les clés sur un partage UNC plutôt qu’à la *%LocalAppData%* emplacement par défaut, configurez le système avec [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Si vous modifiez l’emplacement de la persistance des clés, le système n’est plus automatiquement chiffre au repos, car il ne sait pas si DPAPI est un mécanisme de chiffrement appropriés.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

Vous pouvez configurer le système pour protéger les clés au repos en appelant une de le [ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) API de configuration. Prenons l’exemple ci-dessous, qui stocke les clés sur un partage UNC et chiffre ces clés au repos avec un certificat X.509 spécifique :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

Consultez [clé de chiffrement à l’arrêt](xref:security/data-protection/implementation/key-encryption-at-rest) pour plus d’exemples et une discussion sur les mécanismes de chiffrement à clé intégrés.

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

Permet de configurer le système pour utiliser une durée de vie de clé de 14 jours au lieu de la valeur par défaut de 90 jours, [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

Par défaut, le système de Protection des données isole les applications à partir d’une autre, même si elles partagent le même référentiel clé physique. Cela empêche les applications à partir de la présentation des charges protégé de l’autre. Pour partager les charges utiles de protégé entre deux applications, utilisez [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) avec la même valeur pour chaque application :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Vous pouvez avoir un scénario dans lequel vous souhaitez une application pour restaurer automatiquement les clés (créer des clés) qu’ils approchent d’expiration. Un exemple peut être définies dans une relation principal/secondaire, où le principal de l’application est responsable de problèmes de gestion de clés et des applications secondaires ont simplement une vue en lecture seule de l’anneau de clé des applications. Les applications secondaires peuvent être configurées pour traiter l’anneau de clé comme étant en lecture seule en configurant le système avec [DisableAutomaticKeyGeneration](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.disableautomatickeygeneration):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Isolation d’application

Lorsque le système de Protection des données est fourni par un hôte ASP.NET Core, elle isole automatiquement les applications à partir d’une autre, même si ces applications sont en cours d’exécution sous le même compte de processus de travail et utilisent le même matériel de gestion de clés maître. Cela est quelque peu similaire au modificateur IsolateApps à partir de System.Web  **\<machineKey >** élément.

Le mécanisme d’isolation fonctionne en tenant compte de chaque application sur l’ordinateur local en tant qu’un client unique, donc le [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) racine pour une application donnée inclut automatiquement l’ID d’application en tant que discriminateur. ID unique de l’application provient d’un des deux emplacements :

1. Si l’application est hébergée dans IIS, l’identificateur unique est le chemin d’accès de l’application configuration. Si une application est déployée dans un environnement de batterie de serveurs web, cette valeur doit être stable, en supposant que les environnements d’IIS sont configurés de la même manière sur tous les ordinateurs dans la batterie de serveurs web.

2. Si l’application n’est pas hébergée dans IIS, l’identificateur unique est le chemin d’accès physique de l’application.

L’identificateur unique est conçu pour résister aux réinitialisations &mdash; à la fois de l’application individuelle et de l’ordinateur lui-même.

Ce mécanisme d’isolation suppose que les applications ne sont pas malveillantes. Une application malveillante peut toujours avoir un impact sur toute autre application en cours d’exécution sous le même compte de processus de travail. Dans un environnement d’hébergement partagé où les applications sont mutuellement non approuvées, le fournisseur d’hébergement doit prennent des mesures pour garantir une isolation au niveau du système d’exploitation entre les applications, y compris la séparation des applications sous-jacent référentiels clés.

Si le système de Protection des données n’est pas fourni par un hôte ASP.NET Core (par exemple, si vous instanciez via la `DataProtectionProvider` type concret) le niveau d’isolement d’application est désactivé par défaut. Lorsque le niveau d’isolement d’application est désactivé, soutenues par le même jeu de clés de toutes les applications peuvent partager les charges utiles tant qu’ils fournissent les [à des fins](xref:security/data-protection/consumer-apis/purpose-strings). Pour assurer l’isolation d’application dans cet environnement, appelez le [SetApplicationName](#setapplicationname) méthode sur la configuration de l’objet et fournissez un nom unique pour chaque application.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Modification des algorithmes avec UseCryptographicAlgorithms

La pile de Protection des données vous permet de modifier l’algorithme par défaut utilisé par les clés qui vient d’être générées. La façon la plus simple pour ce faire consiste à appeler [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) à partir du rappel de configuration :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

---

La valeur par défaut EncryptionAlgorithm est AES-256-CBC, et la valeur par défaut ValidationAlgorithm est HMACSHA256. La stratégie par défaut peut être définie par un administrateur système via une [stratégie au niveau machine](xref:security/data-protection/configuration/machine-wide-policy), mais un appel explicite à `UseCryptographicAlgorithms` remplace la stratégie par défaut.

Appel de `UseCryptographicAlgorithms` permet de spécifier l’algorithme de votre choix dans une liste prédéfinie d’intégrés. Vous n’avez pas besoin de vous soucier de l’implémentation de l’algorithme. Dans le scénario ci-dessus, le système de Protection de données tente d’utiliser l’implémentation CNG de AES s’exécutant sur Windows. Sinon, il revient à managé [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) classe.

Vous pouvez spécifier manuellement une implémentation via un appel à [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> Algorithmes de modification n’affecte pas les clés existantes dans l’anneau de clé. Il affecte uniquement les clés qui vient d’être générées.

### <a name="specifying-custom-managed-algorithms"></a>Spécification des algorithmes personnalisés gérés

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Pour spécifier les algorithmes managés personnalisés, créez un [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance qui pointe vers les types d’implémentation :

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Pour spécifier les algorithmes managés personnalisés, créez un [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance qui pointe vers les types d’implémentation :

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

---

En général le \*les propriétés de Type doivent pointer vers concrète, implémentations instanciables (via un constructeur sans paramètre public) de [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) et [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), même si le système spéciaux-cas des valeurs telles que `typeof(Aes)` pour des raisons pratiques.

> [!NOTE]
> Le `SymmetricAlgorithm` doit avoir une longueur de clé > = 128 bits, une taille de bloc de > = 64 bits, et il doit prendre en charge le chiffrement en mode CBC avec le remplissage PKCS #7. Le `KeyedHashAlgorithm` doit avoir une taille de condensat de > = 128 bits, et il doit prendre en charge les clés de longueur égale à la longueur de résumé de l’algorithme de hachage. Le `KeyedHashAlgorithm` n’est pas strictement nécessaire pour être HMAC.
> Le SymmetricAlgorithm doit avoir une longueur de clé de ≥ 128 bits et une taille de bloc de ≥ 64 bits, et il doit prendre en charge le chiffrement en mode CBC avec le remplissage PKCS #7. L’élément KeyedHashAlgorithm impossible doit avoir une taille de condensat de > = 128 bits, et il doit prendre en charge les clés de longueur égale à la longueur de résumé de l’algorithme de hachage. L’élément KeyedHashAlgorithm impossible ne soit pas strictement obligatoire pour être HMAC.

### <a name="specifying-custom-windows-cng-algorithms"></a>En spécifiant les algorithmes CNG de Windows personnalisés

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Pour spécifier un algorithme CNG de Windows personnalisé avec le chiffrement d’en mode CBC et validation de HMAC, créez un [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance qui contient les informations algorithmiques :

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Pour spécifier un algorithme CNG de Windows personnalisé avec le chiffrement d’en mode CBC et validation de HMAC, créez un [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance qui contient les informations algorithmiques :

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

---

> [!NOTE]
> L’algorithme de chiffrement symétrique doit avoir une longueur de clé > = 128 bits, une taille de bloc de > = 64 bits, et il doit prendre en charge le chiffrement en mode CBC avec le remplissage PKCS #7. L’algorithme de hachage doit avoir une taille de condensat de > = 128 bits et doivent prendre en charge en cours d’ouverture avec le BCRYPT\_ALG\_gérer\_HMAC\_drapeau. Le \*propriétés du fournisseur peuvent être définies sur la valeur null pour utiliser le fournisseur par défaut pour l’algorithme spécifié. Consultez le [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation pour plus d’informations.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Pour spécifier un algorithme CNG de Windows personnalisé à l’aide de chiffrement de compteur/Galois Mode avec validation, créez un [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance qui contient les informations algorithmiques :

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Pour spécifier un algorithme CNG de Windows personnalisé à l’aide de chiffrement de compteur/Galois Mode avec validation, créez un [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance qui contient les informations algorithmiques :

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

---

> [!NOTE]
> L’algorithme de chiffrement symétrique doit avoir une longueur de clé > = 128 bits, une taille de bloc de 128 bits exactement, et il doit prendre en charge le chiffrement GCM. Vous pouvez définir le [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) propriété null pour utiliser le fournisseur par défaut pour l’algorithme spécifié. Consultez le [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation pour plus d’informations.

### <a name="specifying-other-custom-algorithms"></a>Spécification d’autres algorithmes personnalisés

Bien que ne pas exposées en tant qu’une API de première classe, le système de Protection des données est suffisamment extensible pour autoriser la spécification de presque n’importe quel type d’algorithme. Par exemple, il est possible de conserver toutes les clés contenues dans un Module de sécurité matériel (HSM) et pour fournir une implémentation personnalisée de la base de routines de chiffrement et le déchiffrement. Consultez [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) dans [extensibilité de chiffrement de base](xref:security/data-protection/extensibility/core-crypto) pour plus d’informations.

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Clés de persistance lors de l’hébergement dans un conteneur Docker

Lors de l’hébergement dans un [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) , conteneur de clés doivent être conservées, que ce soit :

* Un dossier qui est un volume de Docker qui persiste au-delà de la durée de vie du conteneur, comme un volume partagé ou un volume monté d’hôte.
* Un fournisseur externe, tel que [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ou [Redis](https://redis.io/).

## <a name="see-also"></a>Voir aussi

* [Scénarios non compatibles avec l’injection de dépendances](xref:security/data-protection/configuration/non-di-scenarios)
* [Stratégie à l’échelle de la machine](xref:security/data-protection/configuration/machine-wide-policy)

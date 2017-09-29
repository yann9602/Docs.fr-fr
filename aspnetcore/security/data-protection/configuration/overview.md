---
title: "Configuration de la Protection des données"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 0e4881a3-a94d-4e35-9c1c-f025d65dcff0
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 9361dcec89a0f35067181523cc56637d629614ff
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="configuring-data-protection"></a>Configuration de la protection des données

<a name=data-protection-configuring></a>

Lorsque le système de protection des données est initialisé il applique certains [paramètres par défaut](default-settings.md#data-protection-default-settings) en fonction de l’environnement d’exploitation. Ces paramètres sont généralement bénéfique pour les applications qui s’exécutent sur un seul ordinateur. Il existe certains cas où un développeur peut souhaiter modifier ces (par exemple, car leur application est répartie sur plusieurs ordinateurs ou pour des raisons de conformité), et pour ces scénarios, le système de protection des données offre une API riche de configuration.

<a name=data-protection-configuration-callback></a>

Il existe une méthode d’extension AddDataProtection qui retourne un IDataProtectionBuilder qui lui-même expose des méthodes d’extension que vous pouvez chaîner pour configurer la protection des données différentes options. Par exemple, pour stocker les clés sur un partage UNC au lieu de % LocalAppData% (la valeur par défaut), configurez le système comme suit :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

>[!WARNING]
> Si vous modifiez l’emplacement de la persistance des clés, le système n’est plus automatiquement chiffrera clés au repos, car il ne sait pas si DPAPI est un mécanisme de chiffrement appropriés.

<a name=configuring-x509-certificate></a>

Vous pouvez configurer le système pour protéger les clés au repos en appelant une de le ProtectKeysWith\* API de configuration. Prenons l’exemple ci-dessous, qui stocke les clés sur un partage UNC et chiffre ces clés au repos avec un certificat X.509 spécifique.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

Consultez [clé chiffrement au repos](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) pour plus d’exemples et pour savoir plus sur les mécanismes de chiffrement à clé intégrés.

Pour configurer le système pour utiliser une durée de vie de clé de valeur par défaut de 14 jours au lieu de 90 jours, prenons l’exemple suivant :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

Par défaut du système de protection des données isole les applications à partir d’une autre, même si elles partagent le même référentiel clé physique. Cela empêche les applications à partir de la présentation des charges protégé de l’autre. Pour partager les charges utiles de protégé entre deux applications différentes, configurer le système en passant le même nom d’application pour les deux applications, comme dans l’exemple ci-dessous :

<a name=data-protection-code-sample-application-name></a>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("my application");
}
```

<a name=data-protection-configuring-disable-automatic-key-generation></a>

Enfin, vous pouvez avoir un scénario où vous ne souhaitez pas une application pour restaurer automatiquement les clés qu’ils approchent d’expiration. Un exemple peut être définies dans une relation principale / secondaire, où seulement le principal de l’application est responsable de problèmes de gestion de clés, et de toutes les applications secondaire simplement une vue en lecture seule de l’anneau de clé des applications. Les applications secondaire peuvent être configurées pour traiter l’anneau de clé comme étant en lecture seule en configurant le système comme indiqué ci-dessous :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

<a name=data-protection-configuration-per-app-isolation></a>

## <a name="per-application-isolation"></a>Isolation d’application

Lorsque le système de protection des données est fourni par un hôte ASP.NET Core, elle sera automatiquement isoler des applications à partir d’une autre, même si ces applications sont en cours d’exécution sous le même compte de processus de travail et utilisent le même matériel de gestion de clés maître. Cela est quelque peu similaire au modificateur IsolateApps à partir de System.Web <machineKey> élément.

Le fonctionnement du mécanisme d’isolation en tenant compte de chaque application sur l’ordinateur local en tant qu’un client unique, donc le IDataProtector enracinée automatiquement pour une application donnée inclut l’ID d’application en tant que discriminateur. ID unique de l’application provient d’un des deux emplacements.

1. Si l’application est hébergée dans IIS, l’identificateur unique est le chemin d’accès de l’application configuration. Si une application est déployée dans un environnement de batterie de serveurs, cette valeur doit être stable, en supposant que les environnements d’IIS sont configurés de la même manière sur tous les ordinateurs dans la batterie de serveurs.

2. Si l’application n’est pas hébergée dans IIS, l’identificateur unique est le chemin d’accès physique de l’application.

L’identificateur unique est conçu pour survivre réinitialise - de l’application et de l’ordinateur lui-même.

Ce mécanisme d’isolation suppose que les applications ne sont pas malveillantes. Une application malveillante peut toujours avoir un impact sur toute autre application en cours d’exécution sous le même compte de processus de travail. Dans un environnement d’hébergement partagé dans lequel les applications sont mutuellement non approuvées, le fournisseur d’hébergement doit prennent des mesures pour garantir une isolation au niveau du système d’exploitation entre les applications, y compris la séparation des applications sous-jacent référentiels de clé.

Si le système de protection des données n’est pas fourni par un hôte ASP.NET Core (par exemple, si le développeur instancie lui-même via le type concret DataProtectionProvider), le niveau d’isolement d’application est désactivé par défaut et toutes les applications sauvegardé par le codage même matériel peut partager les charges utiles tant qu’ils fournissent les besoins appropriés. Pour assurer l’isolation d’application dans cet environnement, appelez la méthode SetApplicationName sur l’objet de configuration, consultez le [exemple de code](#data-protection-code-sample-application-name) ci-dessus.

<a name=data-protection-changing-algorithms></a>

## <a name="changing-algorithms"></a>Modification des algorithmes

La pile de protection des données permet de modifier l’algorithme par défaut utilisé par les clés qui vient d’être générées. La façon la plus simple pour ce faire consiste à appeler UseCryptographicAlgorithms à partir du rappel de configuration, comme dans l’exemple ci-dessous.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

---

La valeur par défaut EncryptionAlgorithm et ValidationAlgorithm sont AES-256-CBC et HMACSHA256, respectivement. La stratégie par défaut peut être définie par un administrateur système via [stratégie ordinateur à l’échelle](machine-wide-policy.md), mais un appel explicite à UseCryptographicAlgorithms remplace la stratégie par défaut.

L’appel UseCryptographicAlgorithms permet au développeur de spécifier l’algorithme de votre choix (à partir d’une liste prédéfinie d’intégrés), et le développeur ne doit pas à vous soucier de l’implémentation de l’algorithme. Par exemple, dans le scénario ci-dessus, le système de protection de données tentera d’utiliser l’implémentation CNG de AES s’exécutant sur Windows, sinon il revient à la classe System.Security.Cryptography.Aes managée.

Le développeur peut spécifier manuellement une implémentation si vous le souhaitez via un appel à UseCustomCryptographicAlgorithms, comme indiqué dans les exemples ci-dessous.

>[!TIP]
> Modification des algorithmes n’affecte pas les clés existantes dans l’anneau de clé. Il affecte uniquement les clés qui vient d’être générées.

<a name=data-protection-changing-algorithms-custom-managed></a>

### <a name="specifying-custom-managed-algorithms"></a>Spécification des algorithmes personnalisés gérés

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Pour spécifier les algorithmes managés personnalisés, créez une instance de ManagedAuthenticatedEncryptorConfiguration qui pointe vers les types d’implémentation.

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new ManagedAuthenticatedEncryptorConfiguration()
    {
        // a type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // specified in bits
        EncryptionAlgorithmKeySize = 256,

        // a type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Pour spécifier les algorithmes managés personnalisés, créez une instance de ManagedAuthenticatedEncryptionSettings qui pointe vers les types d’implémentation.

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new ManagedAuthenticatedEncryptionSettings()
    {
        // a type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // specified in bits
        EncryptionAlgorithmKeySize = 256,

        // a type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

---

En règle générale le \*les propriétés de Type doivent pointer vers concrète, instanciables (via un constructeur sans paramètre public) implémentations de SymmetricAlgorithm et KeyedHashAlgorithm, si les cas spéciaux système certaines valeurs telles que typeof(Aes) pour des raisons pratiques .

> [!NOTE]
> Le SymmetricAlgorithm doit avoir une longueur de clé de ≥ 128 bits et une taille de bloc de ≥ 64 bits, et il doit prendre en charge le chiffrement en mode CBC avec le remplissage PKCS #7. L’élément KeyedHashAlgorithm impossible doit avoir une taille de condensat de > = 128 bits, et il doit prendre en charge les clés de longueur égale à la longueur de résumé de l’algorithme de hachage. L’élément KeyedHashAlgorithm impossible ne soit pas strictement obligatoire pour être HMAC.

<a name=data-protection-changing-algorithms-cng></a>

### <a name="specifying-custom-windows-cng-algorithms"></a>En spécifiant les algorithmes CNG de Windows personnalisés

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Pour spécifier un algorithme CNG de Windows personnalisé en utilisant le chiffrement en mode CBC + validation de HMAC, créez une instance de CngCbcAuthenticatedEncryptorConfiguration qui contient les informations algorithmiques.

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // specified in bits
        EncryptionAlgorithmKeySize = 256,

        // passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Pour spécifier un algorithme CNG de Windows personnalisé en utilisant le chiffrement en mode CBC + validation de HMAC, créez une instance de CngCbcAuthenticatedEncryptionSettings qui contient les informations algorithmiques.

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new CngCbcAuthenticatedEncryptionSettings()
    {
        // passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // specified in bits
        EncryptionAlgorithmKeySize = 256,

        // passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

---

> [!NOTE]
> L’algorithme de chiffrement symétrique doit avoir une longueur de clé de ≥ 128 bits et une taille de bloc de ≥ 64 bits, et il doit prendre en charge le chiffrement en mode CBC avec le remplissage PKCS #7. L’algorithme de hachage doit avoir une taille de condensat de > = 128 bits et doivent prendre en charge en cours d’ouverture avec l’indicateur BCRYPT_ALG_HANDLE_HMAC_FLAG. Le \*propriétés du fournisseur peuvent être définies sur la valeur null pour utiliser le fournisseur par défaut pour l’algorithme spécifié. Consultez le [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation pour plus d’informations.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Pour spécifier un algorithme CNG de Windows personnalisé en utilisant le chiffrement du Mode de compteur/Galois + validation, créez une instance de CngGcmAuthenticatedEncryptorConfiguration qui contient les informations algorithmiques.

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Pour spécifier un algorithme CNG de Windows personnalisé en utilisant le chiffrement du Mode de compteur/Galois + validation, créez une instance de CngGcmAuthenticatedEncryptionSettings qui contient les informations algorithmiques.

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new CngGcmAuthenticatedEncryptionSettings()
    {
        // passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

---

> [!NOTE]
> L’algorithme de chiffrement symétrique doit avoir une longueur de clé de ≥ 128 bits et une taille de bloc de 128 bits exactement, et il doit prendre en charge le chiffrement GCM. La propriété EncryptionAlgorithmProvider peut être définie à null pour utiliser le fournisseur par défaut pour l’algorithme spécifié. Consultez le [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation pour plus d’informations.

### <a name="specifying-other-custom-algorithms"></a>Spécification d’autres algorithmes personnalisés

Bien que ne pas exposées en tant qu’une API de première classe, le système de protection des données est suffisamment extensible pour autoriser la spécification de presque n’importe quel type d’algorithme. Par exemple, il est possible de conserver toutes les clés contenues dans un module HSM et pour fournir une implémentation personnalisée de la base de routines de chiffrement et le déchiffrement. IAuthenticatedEncryptorConfiguration dans la section d’extensibilité de chiffrement core pour plus d’informations, consultez.

### <a name="see-also"></a>Voir aussi

* [Scénarios non compatibles avec l’injection de dépendances](non-di-scenarios.md)
* [Stratégie à l’échelle de la machine](machine-wide-policy.md)

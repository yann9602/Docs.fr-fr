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
ms.openlocfilehash: 39fab796c24456d61a6a103c4a3f7a8722b4718c
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2017
---
# <a name="configuring-data-protection"></a><span data-ttu-id="c1848-103">Configuration de la protection des données</span><span class="sxs-lookup"><span data-stu-id="c1848-103">Configuring data protection</span></span>

<a name=data-protection-configuring></a>

<span data-ttu-id="c1848-104">Lorsque le système de protection des données est initialisé il applique certains [paramètres par défaut](default-settings.md#data-protection-default-settings) en fonction de l’environnement d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="c1848-104">When the data protection system is initialized it applies some [default settings](default-settings.md#data-protection-default-settings) based on the operational environment.</span></span> <span data-ttu-id="c1848-105">Ces paramètres sont généralement bénéfique pour les applications qui s’exécutent sur un seul ordinateur.</span><span class="sxs-lookup"><span data-stu-id="c1848-105">These settings are generally good for applications running on a single machine.</span></span> <span data-ttu-id="c1848-106">Il existe certains cas où un développeur peut souhaiter modifier ces (par exemple, car leur application est répartie sur plusieurs ordinateurs ou pour des raisons de conformité), et pour ces scénarios, le système de protection des données offre une API riche de configuration.</span><span class="sxs-lookup"><span data-stu-id="c1848-106">There are some cases where a developer may want to change these (perhaps because their application is spread across multiple machines or for compliance reasons), and for these scenarios the data protection system offers a rich configuration API.</span></span>

<a name=data-protection-configuration-callback></a>

<span data-ttu-id="c1848-107">Il existe une méthode d’extension AddDataProtection qui retourne un IDataProtectionBuilder qui lui-même expose des méthodes d’extension que vous pouvez chaîner pour configurer la protection des données différentes options.</span><span class="sxs-lookup"><span data-stu-id="c1848-107">There is an extension method AddDataProtection which returns an IDataProtectionBuilder which itself exposes extension methods that you can chain together to configure various data protection options.</span></span> <span data-ttu-id="c1848-108">Par exemple, pour stocker les clés sur un partage UNC au lieu de % LocalAppData% (la valeur par défaut), configurez le système comme suit :</span><span class="sxs-lookup"><span data-stu-id="c1848-108">For instance, to store keys at a UNC share instead of %LOCALAPPDATA% (the default), configure the system as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

>[!WARNING]
> <span data-ttu-id="c1848-109">Si vous modifiez l’emplacement de la persistance des clés, le système n’est plus automatiquement chiffrera clés au repos, car il ne sait pas si DPAPI est un mécanisme de chiffrement appropriés.</span><span class="sxs-lookup"><span data-stu-id="c1848-109">If you change the key persistence location, the system will no longer automatically encrypt keys at rest since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

<a name=configuring-x509-certificate></a>

<span data-ttu-id="c1848-110">Vous pouvez configurer le système pour protéger les clés au repos en appelant une de le ProtectKeysWith\* API de configuration.</span><span class="sxs-lookup"><span data-stu-id="c1848-110">You can configure the system to protect keys at rest by calling any of the ProtectKeysWith\* configuration APIs.</span></span> <span data-ttu-id="c1848-111">Prenons l’exemple ci-dessous, qui stocke les clés sur un partage UNC et chiffre ces clés au repos avec un certificat X.509 spécifique.</span><span class="sxs-lookup"><span data-stu-id="c1848-111">Consider the example below, which stores keys at a UNC share and encrypts those keys at rest with a specific X.509 certificate.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

<span data-ttu-id="c1848-112">Consultez [clé chiffrement au repos](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) pour plus d’exemples et pour savoir plus sur les mécanismes de chiffrement à clé intégrés.</span><span class="sxs-lookup"><span data-stu-id="c1848-112">See [key encryption at rest](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) for more examples and for discussion on the built-in key encryption mechanisms.</span></span>

<span data-ttu-id="c1848-113">Pour configurer le système pour utiliser une durée de vie de clé de valeur par défaut de 14 jours au lieu de 90 jours, prenons l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="c1848-113">To configure the system to use a default key lifetime of 14 days instead of 90 days, consider the following example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

<span data-ttu-id="c1848-114">Par défaut du système de protection des données isole les applications à partir d’une autre, même si elles partagent le même référentiel clé physique.</span><span class="sxs-lookup"><span data-stu-id="c1848-114">By default the data protection system isolates applications from one another, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="c1848-115">Cela empêche les applications à partir de la présentation des charges protégé de l’autre.</span><span class="sxs-lookup"><span data-stu-id="c1848-115">This prevents the applications from understanding each other's protected payloads.</span></span> <span data-ttu-id="c1848-116">Pour partager les charges utiles de protégé entre deux applications différentes, configurer le système en passant le même nom d’application pour les deux applications, comme dans l’exemple ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="c1848-116">To share protected payloads between two different applications, configure the system passing in the same application name for both applications as in the below example:</span></span>

<a name=data-protection-code-sample-application-name></a>

<!-- literal_block {"ids": ["data-protection-code-sample-application-name"], "linenos": false, "names": ["data-protection-code-sample-application-name"], "xml:space": "preserve", "language": "csharp"} -->

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("my application");
}
```

<a name=data-protection-configuring-disable-automatic-key-generation></a>

<span data-ttu-id="c1848-117">Enfin, vous pouvez avoir un scénario où vous ne souhaitez pas une application pour restaurer automatiquement les clés qu’ils approchent d’expiration.</span><span class="sxs-lookup"><span data-stu-id="c1848-117">Finally, you may have a scenario where you do not want an application to automatically roll keys as they approach expiration.</span></span> <span data-ttu-id="c1848-118">Un exemple peut être définies dans une relation principale / secondaire, où seulement le principal de l’application est responsable de problèmes de gestion de clés, et de toutes les applications secondaire simplement une vue en lecture seule de l’anneau de clé des applications.</span><span class="sxs-lookup"><span data-stu-id="c1848-118">One example of this might be applications set up in a primary / secondary relationship, where only the primary application is responsible for key management concerns, and all secondary applications simply have a read-only view of the key ring.</span></span> <span data-ttu-id="c1848-119">Les applications secondaire peuvent être configurées pour traiter l’anneau de clé comme étant en lecture seule en configurant le système comme indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="c1848-119">The secondary applications can be configured to treat the key ring as read-only by configuring the system as below:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

<a name=data-protection-configuration-per-app-isolation></a>

## <a name="per-application-isolation"></a><span data-ttu-id="c1848-120">Isolation d’application</span><span class="sxs-lookup"><span data-stu-id="c1848-120">Per-application isolation</span></span>

<span data-ttu-id="c1848-121">Lorsque le système de protection des données est fourni par un hôte ASP.NET Core, elle sera automatiquement isoler des applications à partir d’une autre, même si ces applications sont en cours d’exécution sous le même compte de processus de travail et utilisent le même matériel de gestion de clés maître.</span><span class="sxs-lookup"><span data-stu-id="c1848-121">When the data protection system is provided by an ASP.NET Core host, it will automatically isolate applications from one another, even if those applications are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="c1848-122">Cela est quelque peu similaire au modificateur IsolateApps à partir de System.Web <machineKey> élément.</span><span class="sxs-lookup"><span data-stu-id="c1848-122">This is somewhat similar to the IsolateApps modifier from System.Web's <machineKey> element.</span></span>

<span data-ttu-id="c1848-123">Le fonctionnement du mécanisme d’isolation en tenant compte de chaque application sur l’ordinateur local en tant qu’un client unique, donc le IDataProtector enracinée automatiquement pour une application donnée inclut l’ID d’application en tant que discriminateur.</span><span class="sxs-lookup"><span data-stu-id="c1848-123">The isolation mechanism works by considering each application on the local machine as a unique tenant, thus the IDataProtector rooted for any given application automatically includes the application ID as a discriminator.</span></span> <span data-ttu-id="c1848-124">ID unique de l’application provient d’un des deux emplacements.</span><span class="sxs-lookup"><span data-stu-id="c1848-124">The application's unique ID comes from one of two places.</span></span>

1. <span data-ttu-id="c1848-125">Si l’application est hébergée dans IIS, l’identificateur unique est le chemin d’accès de l’application configuration.</span><span class="sxs-lookup"><span data-stu-id="c1848-125">If the application is hosted in IIS, the unique identifier is the application's configuration path.</span></span> <span data-ttu-id="c1848-126">Si une application est déployée dans un environnement de batterie de serveurs, cette valeur doit être stable, en supposant que les environnements d’IIS sont configurés de la même manière sur tous les ordinateurs dans la batterie de serveurs.</span><span class="sxs-lookup"><span data-stu-id="c1848-126">If an application is deployed in a farm environment, this value should be stable assuming that the IIS environments are configured similarly across all machines in the farm.</span></span>

2. <span data-ttu-id="c1848-127">Si l’application n’est pas hébergée dans IIS, l’identificateur unique est le chemin d’accès physique de l’application.</span><span class="sxs-lookup"><span data-stu-id="c1848-127">If the application is not hosted in IIS, the unique identifier is the physical path of the application.</span></span>

<span data-ttu-id="c1848-128">L’identificateur unique est conçu pour survivre réinitialise - de l’application et de l’ordinateur lui-même.</span><span class="sxs-lookup"><span data-stu-id="c1848-128">The unique identifier is designed to survive resets - both of the individual application and of the machine itself.</span></span>

<span data-ttu-id="c1848-129">Ce mécanisme d’isolation suppose que les applications ne sont pas malveillantes.</span><span class="sxs-lookup"><span data-stu-id="c1848-129">This isolation mechanism assumes that the applications are not malicious.</span></span> <span data-ttu-id="c1848-130">Une application malveillante peut toujours avoir un impact sur toute autre application en cours d’exécution sous le même compte de processus de travail.</span><span class="sxs-lookup"><span data-stu-id="c1848-130">A malicious application can always impact any other application running under the same worker process account.</span></span> <span data-ttu-id="c1848-131">Dans un environnement d’hébergement partagé dans lequel les applications sont mutuellement non approuvées, le fournisseur d’hébergement doit prennent des mesures pour garantir une isolation au niveau du système d’exploitation entre les applications, y compris la séparation des applications sous-jacent référentiels de clé.</span><span class="sxs-lookup"><span data-stu-id="c1848-131">In a shared hosting environment where applications are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between applications, including separating the applications' underlying key repositories.</span></span>

<span data-ttu-id="c1848-132">Si le système de protection des données n’est pas fourni par un hôte ASP.NET Core (par exemple, si le développeur instancie lui-même via le type concret DataProtectionProvider), le niveau d’isolement d’application est désactivé par défaut et toutes les applications sauvegardé par le codage même matériel peut partager les charges utiles tant qu’ils fournissent les besoins appropriés.</span><span class="sxs-lookup"><span data-stu-id="c1848-132">If the data protection system is not provided by an ASP.NET Core host (e.g., if the developer instantiates it himself via the DataProtectionProvider concrete type), application isolation is disabled by default, and all applications backed by the same keying material can share payloads as long as they provide the appropriate purposes.</span></span> <span data-ttu-id="c1848-133">Pour assurer l’isolation d’application dans cet environnement, appelez la méthode SetApplicationName sur l’objet de configuration, consultez le [exemple de code](#data-protection-code-sample-application-name) ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="c1848-133">To provide application isolation in this environment, call the SetApplicationName method on the configuration object, see the [code sample](#data-protection-code-sample-application-name) above.</span></span>

<a name=data-protection-changing-algorithms></a>

## <a name="changing-algorithms"></a><span data-ttu-id="c1848-134">Modification des algorithmes</span><span class="sxs-lookup"><span data-stu-id="c1848-134">Changing algorithms</span></span>

<span data-ttu-id="c1848-135">La pile de protection des données permet de modifier l’algorithme par défaut utilisé par les clés qui vient d’être générées.</span><span class="sxs-lookup"><span data-stu-id="c1848-135">The data protection stack allows changing the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="c1848-136">La façon la plus simple pour ce faire consiste à appeler UseCryptographicAlgorithms à partir du rappel de configuration, comme dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="c1848-136">The simplest way to do this is to call UseCryptographicAlgorithms from the configuration callback, as in the below example.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c1848-137">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c1848-137">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c1848-138">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c1848-138">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

---

<span data-ttu-id="c1848-139">La valeur par défaut EncryptionAlgorithm et ValidationAlgorithm sont AES-256-CBC et HMACSHA256, respectivement.</span><span class="sxs-lookup"><span data-stu-id="c1848-139">The default EncryptionAlgorithm and ValidationAlgorithm are AES-256-CBC and HMACSHA256, respectively.</span></span> <span data-ttu-id="c1848-140">La stratégie par défaut peut être définie par un administrateur système via [stratégie ordinateur à l’échelle](machine-wide-policy.md), mais un appel explicite à UseCryptographicAlgorithms remplace la stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="c1848-140">The default policy can be set by a system administrator via [Machine Wide Policy](machine-wide-policy.md), but an explicit call to UseCryptographicAlgorithms will override the default policy.</span></span>

<span data-ttu-id="c1848-141">L’appel UseCryptographicAlgorithms permet au développeur de spécifier l’algorithme de votre choix (à partir d’une liste prédéfinie d’intégrés), et le développeur ne doit pas à vous soucier de l’implémentation de l’algorithme.</span><span class="sxs-lookup"><span data-stu-id="c1848-141">Calling UseCryptographicAlgorithms will allow the developer to specify the desired algorithm (from a predefined built-in list), and the developer does not need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="c1848-142">Par exemple, dans le scénario ci-dessus, le système de protection de données tentera d’utiliser l’implémentation CNG de AES s’exécutant sur Windows, sinon il revient à la classe System.Security.Cryptography.Aes managée.</span><span class="sxs-lookup"><span data-stu-id="c1848-142">For instance, in the scenario above the data protection system will attempt to use the CNG implementation of AES if running on Windows, otherwise it will fall back to the managed System.Security.Cryptography.Aes class.</span></span>

<span data-ttu-id="c1848-143">Le développeur peut spécifier manuellement une implémentation si vous le souhaitez via un appel à UseCustomCryptographicAlgorithms, comme indiqué dans les exemples ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="c1848-143">The developer can manually specify an implementation if desired via a call to UseCustomCryptographicAlgorithms, as show in the below examples.</span></span>

>[!TIP]
> <span data-ttu-id="c1848-144">Modification des algorithmes n’affecte pas les clés existantes dans l’anneau de clé.</span><span class="sxs-lookup"><span data-stu-id="c1848-144">Changing algorithms does not affect existing keys in the key ring.</span></span> <span data-ttu-id="c1848-145">Il affecte uniquement les clés qui vient d’être générées.</span><span class="sxs-lookup"><span data-stu-id="c1848-145">It only affects newly-generated keys.</span></span>

<a name=data-protection-changing-algorithms-custom-managed></a>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="c1848-146">Spécification des algorithmes personnalisés gérés</span><span class="sxs-lookup"><span data-stu-id="c1848-146">Specifying custom managed algorithms</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c1848-147">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c1848-147">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="c1848-148">Pour spécifier les algorithmes managés personnalisés, créez une instance de ManagedAuthenticatedEncryptorConfiguration qui pointe vers les types d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="c1848-148">To specify custom managed algorithms, create a ManagedAuthenticatedEncryptorConfiguration instance that points to the implementation types.</span></span>

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c1848-149">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c1848-149">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="c1848-150">Pour spécifier les algorithmes managés personnalisés, créez une instance de ManagedAuthenticatedEncryptionSettings qui pointe vers les types d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="c1848-150">To specify custom managed algorithms, create a ManagedAuthenticatedEncryptionSettings instance that points to the implementation types.</span></span>

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

<span data-ttu-id="c1848-151">En règle générale le \*les propriétés de Type doivent pointer vers concrète, instanciables (via un constructeur sans paramètre public) implémentations de SymmetricAlgorithm et KeyedHashAlgorithm, si les cas spéciaux système certaines valeurs telles que typeof(Aes) pour des raisons pratiques .</span><span class="sxs-lookup"><span data-stu-id="c1848-151">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of SymmetricAlgorithm and KeyedHashAlgorithm, though the system special-cases some values like typeof(Aes) for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="c1848-152">Le SymmetricAlgorithm doit avoir une longueur de clé de ≥ 128 bits et une taille de bloc de ≥ 64 bits, et il doit prendre en charge le chiffrement en mode CBC avec le remplissage PKCS #7.</span><span class="sxs-lookup"><span data-stu-id="c1848-152">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="c1848-153">L’élément KeyedHashAlgorithm impossible doit avoir une taille de condensat de > = 128 bits, et il doit prendre en charge les clés de longueur égale à la longueur de résumé de l’algorithme de hachage.</span><span class="sxs-lookup"><span data-stu-id="c1848-153">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="c1848-154">L’élément KeyedHashAlgorithm impossible ne soit pas strictement obligatoire pour être HMAC.</span><span class="sxs-lookup"><span data-stu-id="c1848-154">The KeyedHashAlgorithm is not strictly required to be HMAC.</span></span>

<a name=data-protection-changing-algorithms-cng></a>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="c1848-155">En spécifiant les algorithmes CNG de Windows personnalisés</span><span class="sxs-lookup"><span data-stu-id="c1848-155">Specifying custom Windows CNG algorithms</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c1848-156">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c1848-156">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="c1848-157">Pour spécifier un algorithme CNG de Windows personnalisé en utilisant le chiffrement en mode CBC + validation de HMAC, créez une instance de CngCbcAuthenticatedEncryptorConfiguration qui contient les informations algorithmiques.</span><span class="sxs-lookup"><span data-stu-id="c1848-157">To specify a custom Windows CNG algorithm using CBC-mode encryption + HMAC validation, create a CngCbcAuthenticatedEncryptorConfiguration instance that contains the algorithmic information.</span></span>

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c1848-158">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c1848-158">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="c1848-159">Pour spécifier un algorithme CNG de Windows personnalisé en utilisant le chiffrement en mode CBC + validation de HMAC, créez une instance de CngCbcAuthenticatedEncryptionSettings qui contient les informations algorithmiques.</span><span class="sxs-lookup"><span data-stu-id="c1848-159">To specify a custom Windows CNG algorithm using CBC-mode encryption + HMAC validation, create a CngCbcAuthenticatedEncryptionSettings instance that contains the algorithmic information.</span></span>

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
> <span data-ttu-id="c1848-160">L’algorithme de chiffrement symétrique doit avoir une longueur de clé de ≥ 128 bits et une taille de bloc de ≥ 64 bits, et il doit prendre en charge le chiffrement en mode CBC avec le remplissage PKCS #7.</span><span class="sxs-lookup"><span data-stu-id="c1848-160">The symmetric block cipher algorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="c1848-161">L’algorithme de hachage doit avoir une taille de condensat de > = 128 bits et doivent prendre en charge en cours d’ouverture avec l’indicateur BCRYPT_ALG_HANDLE_HMAC_FLAG.</span><span class="sxs-lookup"><span data-stu-id="c1848-161">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT_ALG_HANDLE_HMAC_FLAG flag.</span></span> <span data-ttu-id="c1848-162">Le \*propriétés du fournisseur peuvent être définies sur la valeur null pour utiliser le fournisseur par défaut pour l’algorithme spécifié.</span><span class="sxs-lookup"><span data-stu-id="c1848-162">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="c1848-163">Consultez le [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="c1848-163">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c1848-164">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c1848-164">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="c1848-165">Pour spécifier un algorithme CNG de Windows personnalisé en utilisant le chiffrement du Mode de compteur/Galois + validation, créez une instance de CngGcmAuthenticatedEncryptorConfiguration qui contient les informations algorithmiques.</span><span class="sxs-lookup"><span data-stu-id="c1848-165">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption + validation, create a CngGcmAuthenticatedEncryptorConfiguration instance that contains the algorithmic information.</span></span>

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c1848-166">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c1848-166">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="c1848-167">Pour spécifier un algorithme CNG de Windows personnalisé en utilisant le chiffrement du Mode de compteur/Galois + validation, créez une instance de CngGcmAuthenticatedEncryptionSettings qui contient les informations algorithmiques.</span><span class="sxs-lookup"><span data-stu-id="c1848-167">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption + validation, create a CngGcmAuthenticatedEncryptionSettings instance that contains the algorithmic information.</span></span>

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
> <span data-ttu-id="c1848-168">L’algorithme de chiffrement symétrique doit avoir une longueur de clé de ≥ 128 bits et une taille de bloc de 128 bits exactement, et il doit prendre en charge le chiffrement GCM.</span><span class="sxs-lookup"><span data-stu-id="c1848-168">The symmetric block cipher algorithm must have a key length of ≥ 128 bits and a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="c1848-169">La propriété EncryptionAlgorithmProvider peut être définie à null pour utiliser le fournisseur par défaut pour l’algorithme spécifié.</span><span class="sxs-lookup"><span data-stu-id="c1848-169">The EncryptionAlgorithmProvider property can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="c1848-170">Consultez le [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="c1848-170">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="c1848-171">Spécification d’autres algorithmes personnalisés</span><span class="sxs-lookup"><span data-stu-id="c1848-171">Specifying other custom algorithms</span></span>

<span data-ttu-id="c1848-172">Bien que ne pas exposées en tant qu’une API de première classe, le système de protection des données est suffisamment extensible pour autoriser la spécification de presque n’importe quel type d’algorithme.</span><span class="sxs-lookup"><span data-stu-id="c1848-172">Though not exposed as a first-class API, the data protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="c1848-173">Par exemple, il est possible de conserver toutes les clés contenues dans un module HSM et pour fournir une implémentation personnalisée de la base de routines de chiffrement et le déchiffrement.</span><span class="sxs-lookup"><span data-stu-id="c1848-173">For example, it is possible to keep all keys contained within an HSM and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="c1848-174">IAuthenticatedEncryptorConfiguration dans la section d’extensibilité de chiffrement core pour plus d’informations, consultez.</span><span class="sxs-lookup"><span data-stu-id="c1848-174">See IAuthenticatedEncryptorConfiguration in the core cryptography extensibility section for more information.</span></span>

### <a name="see-also"></a><span data-ttu-id="c1848-175">Voir aussi</span><span class="sxs-lookup"><span data-stu-id="c1848-175">See also</span></span>

* [<span data-ttu-id="c1848-176">Scénarios non DI</span><span class="sxs-lookup"><span data-stu-id="c1848-176">Non DI Aware Scenarios</span></span>](non-di-scenarios.md)
* [<span data-ttu-id="c1848-177">Stratégie ordinateur à l’échelle</span><span class="sxs-lookup"><span data-stu-id="c1848-177">Machine Wide Policy</span></span>](machine-wide-policy.md)

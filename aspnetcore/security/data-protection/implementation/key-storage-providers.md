---
title: "Fournisseurs de stockage de clés"
author: rick-anderson
description: "Fournisseurs de stockage de clés"
keywords: Encryption,ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.assetid: 423e0a79-2f34-44c4-aaf3-146a53c39251
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: d4b286dc47f8d66e6d09c3e0f48e6326139c8e1e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="key-storage-providers"></a>Fournisseurs de stockage de clés

<a name="data-protection-implementation-key-storage-providers"></a>

Par défaut, le système de protection des données [utilise une heuristique](xref:security/data-protection/configuration/default-settings) pour déterminer où le matériel de clé de chiffrement doit être persistante. Le développeur peut substituer l’heuristique et spécifier manuellement l’emplacement.

> [!NOTE]
> Si vous spécifiez un emplacement de persistance clé explicite, le système de protection des données sera annuler l’inscription au chiffrement à clé par défaut au mécanisme de rest l’heuristique fournie, afin de clés n’est plus seront chiffrées au repos. Il est recommandé que vous avez en outre [spécifient un mécanisme de chiffrement à clé explicite](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest-providers) pour les applications de production.

Le système de protection des données est fourni avec plusieurs fournisseurs de stockage de clés de boîte aux lettres.

## <a name="file-system"></a>Système de fichiers

Nous estimons que de nombreuses applications utilisent un dépôt de clé basée sur le système de fichiers. Pour configurer cela, appelez le [PersistKeysToFileSystem](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) routine de configuration comme indiqué ci-dessous. Fournir un `DirectoryInfo` pointant vers le référentiel dans lequel les clés doivent être stockées.

```csharp
sc.AddDataProtection()
       // persist keys to a specific directory
       .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
   ```

Le `DirectoryInfo` peut pointer vers un répertoire sur l’ordinateur local, ou il peut pointer vers un dossier sur un partage réseau. Si vous pointez vers un répertoire sur l’ordinateur local (et le scénario est que seules les applications sur l’ordinateur local devrez utiliser ce référentiel), envisagez d’utiliser [DPAPI Windows](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) pour chiffrer les clés au repos. Sinon, envisagez d’utiliser un [certificat X.509](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) pour chiffrer les clés au repos.

## <a name="azure-and-redis"></a>Azure et Redis

Le `Microsoft.AspNetCore.DataProtection.AzureStorage` et `Microsoft.AspNetCore.DataProtection.Redis` packages permettent le stockage de vos clés de protection des données dans le stockage Azure ou un cache Redis. Les clés peuvent être partagées entre plusieurs instances d’une application web. Votre application ASP.NET Core peut partager des cookies d’authentification ou de la protection de CSRF sur plusieurs serveurs. Pour configurer sur Azure, appelez l’une de le [PersistKeysToAzureBlobStorage](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.AzureStorage/AzureDataProtectionBuilderExtensions.cs) les surcharges comme indiqué ci-dessous.

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));

    services.AddMvc();
}
```

Voir aussi la [code de test Azure](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/AzureBlob/Program.cs).

Pour configurer sur Redis, appelez l’une de le [PersistKeysToRedis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.Redis/RedisDataProtectionBuilderExtensions.cs) les surcharges comme indiqué ci-dessous.

```
public void ConfigureServices(IServiceCollection services)
{
    // Connect to Redis database.
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");

    services.AddMvc();
}
```

Pour plus d’informations, consultez :

- [StackExchange.Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
- [Cache Redis Azure](https://docs.microsoft.com/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
- [Code de test de redis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/Redis/Program.cs).

## <a name="registry"></a>Registre

Parfois, l’application peut-être pas accès en écriture au système de fichiers. Considérez un scénario dans lequel une application s’exécute sous un compte de service virtuel (telles que l’identité du pool d’application de w3wp.exe). Dans ce cas, l’administrateur peut vous avoir fourni une clé de Registre est tiennent approprié pour l’identité de compte de service. Appelez le [PersistKeysToRegistry](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) routine de configuration comme indiqué ci-dessous. Fournir un `RegistryKey` pointant vers l’emplacement de stockage des services de chiffrement de clés/valeurs.

```csharp
   sc.AddDataProtection()
       // persist keys to a specific location in the system registry
       .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
   ```

Si vous utilisez le Registre du système comme un mécanisme de persistance, envisagez d’utiliser [DPAPI Windows](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) pour chiffrer les clés au repos.

## <a name="custom-key-repository"></a>Référentiel de clés personnalisé

Si les mécanismes de boîte aux lettres ne sont pas appropriées, le développeur peut spécifier leur propre mécanisme de persistance de clé en fournissant une personnalisée `IXmlRepository`.

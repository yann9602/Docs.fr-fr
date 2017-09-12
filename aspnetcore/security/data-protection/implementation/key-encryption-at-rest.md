---
title: "Chiffrement à clé au repos"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: f2bbbf4e-0945-43ce-be59-8bf19e448798
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 16a9385630d88c4c9f33954f83fce2bbce5be719
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="key-encryption-at-rest"></a>Chiffrement à clé au repos

<a name=data-protection-implementation-key-encryption-at-rest></a>

Par défaut, le système de protection des données [utilise une heuristique](../configuration/default-settings.md#data-protection-default-settings) pour déterminer le mode de chiffrement matériel de clé doivent être chiffrées au repos. Le développeur peut substituer l’heuristique et spécifier manuellement la façon dont les clés doivent être chiffrées au repos.

> [!NOTE]
> Si vous spécifiez un chiffrement à clé explicit au mécanisme de rest, le système de protection de données sera annuler l’inscription le mécanisme de stockage de clés par défaut que l’heuristique fourni. Vous devez [spécifier un mécanisme de stockage de clés explicites](key-storage-providers.md#data-protection-implementation-key-storage-providers), sinon le système de protection des données ne démarre pas.

<a name=data-protection-implementation-key-encryption-at-rest-providers></a>

Le système de protection des données est fourni avec trois mécanismes de chiffrement à clé dans la zone.

## <a name="windows-dpapi"></a>Windows DPAPI

*Ce mécanisme est disponible uniquement sur Windows.*

Windows DPAPI est utilisé, le matériel de clé est chiffré [CryptProtectData](https://msdn.microsoft.com/library/windows/desktop/aa380261(v=vs.85).aspx) avant d’être rendues persistantes dans le stockage. DPAPI est un mécanisme de chiffrement appropriés pour les données qui ne seront jamais lue en dehors de l’ordinateur actuel (Toutefois, il est possible de sauvegarder ces clés à Active Directory ; voir [DPAPI et les profils itinérants](https://support.microsoft.com/kb/309408/#6)). Par exemple configurer le chiffrement de clé au repos DPAPI.

```csharp
sc.AddDataProtection()
       // only the local user account can decrypt the keys
       .ProtectKeysWithDpapi();
   ```

Si ProtectKeysWithDpapi est appelée sans paramètres, seul le compte d’utilisateur Windows actuel peut déchiffrer le matériel de clé persistant. Vous pouvez éventuellement spécifier un compte d’utilisateur sur l’ordinateur (pas seulement le compte d’utilisateur en cours) doit être en mesure de déchiffrer la clé, comme indiqué dans l’exemple ci-dessous.

```csharp
sc.AddDataProtection()
       // all user accounts on the machine can decrypt the keys
       .ProtectKeysWithDpapi(protectToLocalMachine: true);
   ```

## <a name="x509-certificate"></a>Certificat X.509

*Ce mécanisme n’est pas disponible sur `.NET Core 1.0` ou `1.1`.*

Si votre application est répartie sur plusieurs ordinateurs, il peut être pratique pour distribuer un certificat X.509 partagé sur les ordinateurs et pour configurer des applications pour utiliser ce certificat pour le chiffrement des clés au repos. Voir ci-dessous pour obtenir un exemple.

```csharp
sc.AddDataProtection()
       // searches the cert store for the cert with this thumbprint
       .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
   ```

Seuls les certificats avec des clés privées de CAPI sont pris en charge en raison des limitations de .NET Framework. Consultez [basée sur certificat de chiffrement avec Windows DPAPI-NG](#data-protection-implementation-key-encryption-at-rest-dpapi-ng) ci-dessous pour connaître les solutions possibles à ces limitations.

<a name=data-protection-implementation-key-encryption-at-rest-dpapi-ng></a>

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

*Ce mécanisme est disponible uniquement sur Windows 8 / Windows Server 2012 et versions ultérieur.*

À compter de Windows 8, le système d’exploitation prend en charge DPAPI-NG (également appelé CNG DPAPI). Microsoft est disposé son scénario d’utilisation comme suit.

   Informatique en nuage, toutefois, requiert souvent des que contenu chiffré sur l’ordinateur être déchiffrée sur un autre. Par conséquent, Microsoft à partir de Windows 8, étendu l’idée de l’utilisation d’une API relativement simple pour englober les scénarios de cloud. Cette nouvelle API, appelée DPAPI-NG, permet de partager en toute sécurité les clés secrètes (clés, les mots de passe, le matériel de clé) et les messages en les protégeant à un ensemble d’entités de sécurité qui peut être utilisé pour déprotéger les sur des ordinateurs différents après authentification réussie et autorisation.

   À partir de [sur DPAPI de CNG](https://msdn.microsoft.com/library/windows/desktop/hh706794(v=vs.85).aspx)

Le principal est encodé sous la règle de protection d’un descripteur. Considérez l’exemple ci-dessous, qui chiffre matériel de clé de telle sorte que seul l’utilisateur appartenant au domaine avec le SID spécifié peut déchiffrer le matériel de clé.

```csharp
sc.AddDataProtection()
     // uses the descriptor rule "SID=S-1-5-21-..."
     .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
       flags: DpapiNGProtectionDescriptorFlags.None);
   ```

Il existe également une surcharge sans paramètre de ProtectKeysWithDpapiNG. Il s’agit d’une méthode pratique pour la spécification de la règle « SID = mien », où mien est le SID du compte d’utilisateur Windows actuel.

```csharp
sc.AddDataProtection()
     // uses the descriptor rule "SID={current account SID}"
     .ProtectKeysWithDpapiNG();
   ```

Dans ce scénario, le contrôleur de domaine Active Directory est responsable de la distribution de clés de chiffrement utilisées par les opérations de DPAPI-NG. L’utilisateur cible sera en mesure de déchiffrer la charge utile chiffré à partir de n’importe quel ordinateur joint au domaine (à condition que le processus s’exécute sous leur identité).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Basée sur certificat de chiffrement avec Windows DPAPI-NG.

Si vous êtes en cours d’exécution sur Windows 8.1 / Windows Server 2012 R2 ou version ultérieure, vous pouvez utiliser Windows DPAPI-NG pour effectuer un chiffrement basée sur certificat, même si l’application est en cours d’exécution [.NET Core](https://www.microsoft.com/net/core). Pour tirer parti de cela, utilisez la chaîne de descripteur de règle « certificat = HashId:thumbprint », où l’empreinte numérique est l’empreinte numérique SHA1 codé en hexadécimal du certificat à utiliser. Voir ci-dessous pour obtenir un exemple.

```csharp
sc.AddDataProtection()
       // searches the cert store for the cert with this thumbprint
       .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0",
           flags: DpapiNGProtectionDescriptorFlags.None);
   ```

Toute application qui pointe vers ce référentiel doit s’exécuter sur Windows 8.1 / Windows Server 2012 R2 ou version ultérieure pour pouvoir déchiffrer cette clé.

## <a name="custom-key-encryption"></a>Chiffrement à clé personnalisé

Si les mécanismes de boîte aux lettres ne sont pas appropriées, le développeur peut spécifier leur propre mécanisme de chiffrement à clé en fournissant un IXmlEncryptor personnalisé.

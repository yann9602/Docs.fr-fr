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
ms.openlocfilehash: 5d0eb4036a3d491336cbe9357779c150b5cbb236
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="key-encryption-at-rest"></a><span data-ttu-id="92dd8-103">Chiffrement à clé au repos</span><span class="sxs-lookup"><span data-stu-id="92dd8-103">Key Encryption At Rest</span></span>

<a name="data-protection-implementation-key-encryption-at-rest"></a>

<span data-ttu-id="92dd8-104">Par défaut, le système de protection des données [utilise une heuristique](../configuration/default-settings.md#data-protection-default-settings) pour déterminer le mode de chiffrement matériel de clé doivent être chiffrées au repos.</span><span class="sxs-lookup"><span data-stu-id="92dd8-104">By default the data protection system [employs a heuristic](../configuration/default-settings.md#data-protection-default-settings) to determine how cryptographic key material should be encrypted at rest.</span></span> <span data-ttu-id="92dd8-105">Le développeur peut substituer l’heuristique et spécifier manuellement la façon dont les clés doivent être chiffrées au repos.</span><span class="sxs-lookup"><span data-stu-id="92dd8-105">The developer can override the heuristic and manually specify how keys should be encrypted at rest.</span></span>

> [!NOTE]
> <span data-ttu-id="92dd8-106">Si vous spécifiez un chiffrement à clé explicit au mécanisme de rest, le système de protection de données sera annuler l’inscription le mécanisme de stockage de clés par défaut que l’heuristique fourni.</span><span class="sxs-lookup"><span data-stu-id="92dd8-106">If you specify an explicit key encryption at rest mechanism, the data protection system will deregister the default key storage mechanism that the heuristic provided.</span></span> <span data-ttu-id="92dd8-107">Vous devez [spécifier un mécanisme de stockage de clés explicites](key-storage-providers.md#data-protection-implementation-key-storage-providers), sinon le système de protection des données ne démarre pas.</span><span class="sxs-lookup"><span data-stu-id="92dd8-107">You must [specify an explicit key storage mechanism](key-storage-providers.md#data-protection-implementation-key-storage-providers), otherwise the data protection system will fail to start.</span></span>

<a name="data-protection-implementation-key-encryption-at-rest-providers"></a>

<span data-ttu-id="92dd8-108">Le système de protection des données est fourni avec trois mécanismes de chiffrement à clé dans la zone.</span><span class="sxs-lookup"><span data-stu-id="92dd8-108">The data protection system ships with three in-box key encryption mechanisms.</span></span>

## <a name="windows-dpapi"></a><span data-ttu-id="92dd8-109">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="92dd8-109">Windows DPAPI</span></span>

<span data-ttu-id="92dd8-110">*Ce mécanisme est disponible uniquement sur Windows.*</span><span class="sxs-lookup"><span data-stu-id="92dd8-110">*This mechanism is available only on Windows.*</span></span>

<span data-ttu-id="92dd8-111">Windows DPAPI est utilisé, le matériel de clé est chiffré [CryptProtectData](https://msdn.microsoft.com/library/windows/desktop/aa380261(v=vs.85).aspx) avant d’être rendues persistantes dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="92dd8-111">When Windows DPAPI is used, key material will be encrypted via [CryptProtectData](https://msdn.microsoft.com/library/windows/desktop/aa380261(v=vs.85).aspx) before being persisted to storage.</span></span> <span data-ttu-id="92dd8-112">DPAPI est un mécanisme de chiffrement appropriés pour les données qui ne seront jamais lue en dehors de l’ordinateur actuel (Toutefois, il est possible de sauvegarder ces clés à Active Directory ; voir [DPAPI et les profils itinérants](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="92dd8-112">DPAPI is an appropriate encryption mechanism for data that will never be read outside of the current machine (though it is possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="92dd8-113">Par exemple configurer le chiffrement de clé au repos DPAPI.</span><span class="sxs-lookup"><span data-stu-id="92dd8-113">For example to configure DPAPI key-at-rest encryption.</span></span>

```csharp
sc.AddDataProtection()
       // only the local user account can decrypt the keys
       .ProtectKeysWithDpapi();
   ```

<span data-ttu-id="92dd8-114">Si ProtectKeysWithDpapi est appelée sans paramètres, seul le compte d’utilisateur Windows actuel peut déchiffrer le matériel de clé persistant.</span><span class="sxs-lookup"><span data-stu-id="92dd8-114">If ProtectKeysWithDpapi is called with no parameters, only the current Windows user account can decipher the persisted key material.</span></span> <span data-ttu-id="92dd8-115">Vous pouvez éventuellement spécifier un compte d’utilisateur sur l’ordinateur (pas seulement le compte d’utilisateur en cours) doit être en mesure de déchiffrer la clé, comme indiqué dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="92dd8-115">You can optionally specify that any user account on the machine (not just the current user account) should be able to decipher the key material, as shown in the below example.</span></span>

```csharp
sc.AddDataProtection()
       // all user accounts on the machine can decrypt the keys
       .ProtectKeysWithDpapi(protectToLocalMachine: true);
   ```

## <a name="x509-certificate"></a><span data-ttu-id="92dd8-116">Certificat X.509</span><span class="sxs-lookup"><span data-stu-id="92dd8-116">X.509 certificate</span></span>

<span data-ttu-id="92dd8-117">*Ce mécanisme n’est pas disponible sur `.NET Core 1.0` ou `1.1`.*</span><span class="sxs-lookup"><span data-stu-id="92dd8-117">*This mechanism is not available on `.NET Core 1.0` or `1.1`.*</span></span>

<span data-ttu-id="92dd8-118">Si votre application est répartie sur plusieurs ordinateurs, il peut être pratique pour distribuer un certificat X.509 partagé sur les ordinateurs et pour configurer des applications pour utiliser ce certificat pour le chiffrement des clés au repos.</span><span class="sxs-lookup"><span data-stu-id="92dd8-118">If your application is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and to configure applications to use this certificate for encryption of keys at rest.</span></span> <span data-ttu-id="92dd8-119">Voir ci-dessous pour obtenir un exemple.</span><span class="sxs-lookup"><span data-stu-id="92dd8-119">See below for an example.</span></span>

```csharp
sc.AddDataProtection()
       // searches the cert store for the cert with this thumbprint
       .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
   ```

<span data-ttu-id="92dd8-120">Seuls les certificats avec des clés privées de CAPI sont pris en charge en raison des limitations de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="92dd8-120">Due to .NET Framework limitations only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="92dd8-121">Consultez [basée sur certificat de chiffrement avec Windows DPAPI-NG](#data-protection-implementation-key-encryption-at-rest-dpapi-ng) ci-dessous pour connaître les solutions possibles à ces limitations.</span><span class="sxs-lookup"><span data-stu-id="92dd8-121">See [Certificate-based encryption with Windows DPAPI-NG](#data-protection-implementation-key-encryption-at-rest-dpapi-ng) below for possible workarounds to these limitations.</span></span>

<a name="data-protection-implementation-key-encryption-at-rest-dpapi-ng"></a>

## <a name="windows-dpapi-ng"></a><span data-ttu-id="92dd8-122">Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="92dd8-122">Windows DPAPI-NG</span></span>

<span data-ttu-id="92dd8-123">*Ce mécanisme est disponible uniquement sur Windows 8 / Windows Server 2012 et versions ultérieur.*</span><span class="sxs-lookup"><span data-stu-id="92dd8-123">*This mechanism is available only on Windows 8 / Windows Server 2012 and later.*</span></span>

<span data-ttu-id="92dd8-124">À compter de Windows 8, le système d’exploitation prend en charge DPAPI-NG (également appelé CNG DPAPI).</span><span class="sxs-lookup"><span data-stu-id="92dd8-124">Beginning with Windows 8, the operating system supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="92dd8-125">Microsoft est disposé son scénario d’utilisation comme suit.</span><span class="sxs-lookup"><span data-stu-id="92dd8-125">Microsoft lays out its usage scenario as follows.</span></span>

   <span data-ttu-id="92dd8-126">Informatique en nuage, toutefois, requiert souvent des que contenu chiffré sur l’ordinateur être déchiffrée sur un autre.</span><span class="sxs-lookup"><span data-stu-id="92dd8-126">Cloud computing, however, often requires that content encrypted on one computer be decrypted on another.</span></span> <span data-ttu-id="92dd8-127">Par conséquent, Microsoft à partir de Windows 8, étendu l’idée de l’utilisation d’une API relativement simple pour englober les scénarios de cloud.</span><span class="sxs-lookup"><span data-stu-id="92dd8-127">Therefore, beginning with Windows 8, Microsoft extended the idea of using a relatively straightforward API to encompass cloud scenarios.</span></span> <span data-ttu-id="92dd8-128">Cette nouvelle API, appelée DPAPI-NG, permet de partager en toute sécurité les clés secrètes (clés, les mots de passe, le matériel de clé) et les messages en les protégeant à un ensemble d’entités de sécurité qui peut être utilisé pour déprotéger les sur des ordinateurs différents après authentification réussie et autorisation.</span><span class="sxs-lookup"><span data-stu-id="92dd8-128">This new API, called DPAPI-NG, enables you to securely share secrets (keys, passwords, key material) and messages by protecting them to a set of principals that can be used to unprotect them on different computers after proper authentication and authorization.</span></span>

   <span data-ttu-id="92dd8-129">À partir de [sur DPAPI de CNG](https://msdn.microsoft.com/library/windows/desktop/hh706794(v=vs.85).aspx)</span><span class="sxs-lookup"><span data-stu-id="92dd8-129">From [About CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794(v=vs.85).aspx)</span></span>

<span data-ttu-id="92dd8-130">Le principal est encodé sous la règle de protection d’un descripteur.</span><span class="sxs-lookup"><span data-stu-id="92dd8-130">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="92dd8-131">Considérez l’exemple ci-dessous, qui chiffre matériel de clé de telle sorte que seul l’utilisateur appartenant au domaine avec le SID spécifié peut déchiffrer le matériel de clé.</span><span class="sxs-lookup"><span data-stu-id="92dd8-131">Consider the below example, which encrypts key material such that only the domain-joined user with the specified SID can decrypt the key material.</span></span>

```csharp
sc.AddDataProtection()
     // uses the descriptor rule "SID=S-1-5-21-..."
     .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
       flags: DpapiNGProtectionDescriptorFlags.None);
   ```

<span data-ttu-id="92dd8-132">Il existe également une surcharge sans paramètre de ProtectKeysWithDpapiNG.</span><span class="sxs-lookup"><span data-stu-id="92dd8-132">There is also a parameterless overload of ProtectKeysWithDpapiNG.</span></span> <span data-ttu-id="92dd8-133">Il s’agit d’une méthode pratique pour la spécification de la règle « SID = mien », où mien est le SID du compte d’utilisateur Windows actuel.</span><span class="sxs-lookup"><span data-stu-id="92dd8-133">This is a convenience method for specifying the rule "SID=mine", where mine is the SID of the current Windows user account.</span></span>

```csharp
sc.AddDataProtection()
     // uses the descriptor rule "SID={current account SID}"
     .ProtectKeysWithDpapiNG();
   ```

<span data-ttu-id="92dd8-134">Dans ce scénario, le contrôleur de domaine Active Directory est responsable de la distribution de clés de chiffrement utilisées par les opérations de DPAPI-NG.</span><span class="sxs-lookup"><span data-stu-id="92dd8-134">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="92dd8-135">L’utilisateur cible sera en mesure de déchiffrer la charge utile chiffré à partir de n’importe quel ordinateur joint au domaine (à condition que le processus s’exécute sous leur identité).</span><span class="sxs-lookup"><span data-stu-id="92dd8-135">The target user will be able to decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="92dd8-136">Basée sur certificat de chiffrement avec Windows DPAPI-NG.</span><span class="sxs-lookup"><span data-stu-id="92dd8-136">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="92dd8-137">Si vous êtes en cours d’exécution sur Windows 8.1 / Windows Server 2012 R2 ou version ultérieure, vous pouvez utiliser Windows DPAPI-NG pour effectuer un chiffrement basée sur certificat, même si l’application est en cours d’exécution [.NET Core](https://www.microsoft.com/net/core).</span><span class="sxs-lookup"><span data-stu-id="92dd8-137">If you're running on Windows 8.1 / Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption, even if the application is running on [.NET Core](https://www.microsoft.com/net/core).</span></span> <span data-ttu-id="92dd8-138">Pour tirer parti de cela, utilisez la chaîne de descripteur de règle « certificat = HashId:thumbprint », où l’empreinte numérique est l’empreinte numérique SHA1 codé en hexadécimal du certificat à utiliser.</span><span class="sxs-lookup"><span data-stu-id="92dd8-138">To take advantage of this, use the rule descriptor string "CERTIFICATE=HashId:thumbprint", where thumbprint is the hex-encoded SHA1 thumbprint of the certificate to use.</span></span> <span data-ttu-id="92dd8-139">Voir ci-dessous pour obtenir un exemple.</span><span class="sxs-lookup"><span data-stu-id="92dd8-139">See below for an example.</span></span>

```csharp
sc.AddDataProtection()
       // searches the cert store for the cert with this thumbprint
       .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0",
           flags: DpapiNGProtectionDescriptorFlags.None);
   ```

<span data-ttu-id="92dd8-140">Toute application qui pointe vers ce référentiel doit s’exécuter sur Windows 8.1 / Windows Server 2012 R2 ou version ultérieure pour pouvoir déchiffrer cette clé.</span><span class="sxs-lookup"><span data-stu-id="92dd8-140">Any application which is pointed at this repository must be running on Windows 8.1 / Windows Server 2012 R2 or later to be able to decipher this key.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="92dd8-141">Chiffrement à clé personnalisé</span><span class="sxs-lookup"><span data-stu-id="92dd8-141">Custom key encryption</span></span>

<span data-ttu-id="92dd8-142">Si les mécanismes de boîte aux lettres ne sont pas appropriées, le développeur peut spécifier leur propre mécanisme de chiffrement à clé en fournissant un IXmlEncryptor personnalisé.</span><span class="sxs-lookup"><span data-stu-id="92dd8-142">If the in-box mechanisms are not appropriate, the developer can specify their own key encryption mechanism by providing a custom IXmlEncryptor.</span></span>

---
title: "Fournisseurs de protection des données éphémères"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: af6ea1d0-0d9d-41df-a870-5dda24978e2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: 6b564f082eefad993ac938407e0f3b657d83fb52
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="ephemeral-data-protection-providers"></a><span data-ttu-id="64377-103">Fournisseurs de protection des données éphémères</span><span class="sxs-lookup"><span data-stu-id="64377-103">Ephemeral data protection providers</span></span>

<a name=data-protection-implementation-key-storage-ephemeral></a>

<span data-ttu-id="64377-104">Il existe des scénarios où une application exige un throwaway IDataProtectionProvider.</span><span class="sxs-lookup"><span data-stu-id="64377-104">There are scenarios where an application needs a throwaway IDataProtectionProvider.</span></span> <span data-ttu-id="64377-105">Par exemple, le développeur peut simplement être expérimentation dans une application console uniques, ou l’application elle-même est transitoire (elle est basée sur un script ou un test unitaire projet).</span><span class="sxs-lookup"><span data-stu-id="64377-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="64377-106">Pour prendre en charge ces scénarios, le package Microsoft.AspNetCore.DataProtection inclut un type EphemeralDataProtectionProvider.</span><span class="sxs-lookup"><span data-stu-id="64377-106">To support these scenarios the package Microsoft.AspNetCore.DataProtection includes a type EphemeralDataProtectionProvider.</span></span> <span data-ttu-id="64377-107">Ce type fournit une implémentation de base de IDataProtectionProvider clé dont le référentiel est maintenu en mémoire uniquement et n’est pas écrit dans n’importe quel magasin de stockage.</span><span class="sxs-lookup"><span data-stu-id="64377-107">This type provides a basic implementation of IDataProtectionProvider whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="64377-108">Chaque instance EphemeralDataProtectionProvider utilise sa propre clé principale unique.</span><span class="sxs-lookup"><span data-stu-id="64377-108">Each instance of EphemeralDataProtectionProvider uses its own unique master key.</span></span> <span data-ttu-id="64377-109">Par conséquent, si un IDataProtector rattachée à une EphemeralDataProtectionProvider génère une charge utile protégée, cette charge utile peut uniquement être protégée par un équivalent IDataProtector (les données sont identiques [objectif](../consumer-apis/purpose-strings.md#data-protection-consumer-apis-purposes) chaîne) à la même racine Instance de EphemeralDataProtectionProvider.</span><span class="sxs-lookup"><span data-stu-id="64377-109">Therefore, if an IDataProtector rooted at an EphemeralDataProtectionProvider generates a protected payload, that payload can only be unprotected by an equivalent IDataProtector (given the same [purpose](../consumer-apis/purpose-strings.md#data-protection-consumer-apis-purposes) chain) rooted at the same EphemeralDataProtectionProvider instance.</span></span>

<span data-ttu-id="64377-110">L’exemple suivant montre comment instancier un EphemeralDataProtectionProvider et son utilisation pour protéger et déprotéger les données.</span><span class="sxs-lookup"><span data-stu-id="64377-110">The following sample demonstrates instantiating an EphemeralDataProtectionProvider and using it to protect and unprotect data.</span></span>

```none
using System;
   using Microsoft.AspNetCore.DataProtection;

   public class Program
   {
       public static void Main(string[] args)
       {
           const string purpose = "Ephemeral.App.v1";

           // create an ephemeral provider and demonstrate that it can round-trip a payload
           var provider = new EphemeralDataProtectionProvider();
           var protector = provider.CreateProtector(purpose);
           Console.Write("Enter input: ");
           string input = Console.ReadLine();

           // protect the payload
           string protectedPayload = protector.Protect(input);
           Console.WriteLine($"Protect returned: {protectedPayload}");

           // unprotect the payload
           string unprotectedPayload = protector.Unprotect(protectedPayload);
           Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

           // if I create a new ephemeral provider, it won't be able to unprotect existing
           // payloads, even if I specify the same purpose
           provider = new EphemeralDataProtectionProvider();
           protector = provider.CreateProtector(purpose);
           unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
       }
   }

   /*
    * SAMPLE OUTPUT
    *
    * Enter input: Hello!
    * Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
    * Unprotect returned: Hello!
    * << throws CryptographicException >>
    */
   ```

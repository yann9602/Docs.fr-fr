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
ms.openlocfilehash: ee8dccac3ba990b110758042192779426b01fc53
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="ephemeral-data-protection-providers"></a>Fournisseurs de protection des données éphémères

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Il existe des scénarios où une application exige un throwaway IDataProtectionProvider. Par exemple, le développeur peut simplement être expérimentation dans une application console uniques, ou l’application elle-même est transitoire (elle est basée sur un script ou un test unitaire projet). Pour prendre en charge ces scénarios, le package Microsoft.AspNetCore.DataProtection inclut un type EphemeralDataProtectionProvider. Ce type fournit une implémentation de base de IDataProtectionProvider clé dont le référentiel est maintenu en mémoire uniquement et n’est pas écrit dans n’importe quel magasin de stockage.

Chaque instance EphemeralDataProtectionProvider utilise sa propre clé principale unique. Par conséquent, si un IDataProtector rattachée à une EphemeralDataProtectionProvider génère une charge utile protégée, cette charge utile peut uniquement être protégée par un équivalent IDataProtector (les données sont identiques [objectif](../consumer-apis/purpose-strings.md#data-protection-consumer-apis-purposes) chaîne) à la même racine Instance de EphemeralDataProtectionProvider.

L’exemple suivant montre comment instancier un EphemeralDataProtectionProvider et son utilisation pour protéger et déprotéger les données.

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

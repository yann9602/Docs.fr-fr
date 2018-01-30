---
title: "Fournisseurs de protection des données éphémères"
author: rick-anderson
description: "Ce document explique les détails d’implémentation des fournisseurs de protection des données éphémères ASP.NET Core."
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: fe8a020c77bfe614691bfd0f5d403c7d25a0df56
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="ephemeral-data-protection-providers"></a>Fournisseurs de protection des données éphémères

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Il existe des scénarios où une application a besoin d’un throwaway `IDataProtectionProvider`. Par exemple, le développeur peut simplement être expérimentation dans une application console uniques, ou l’application elle-même est transitoire (elle est basée sur un script ou un test unitaire projet). Pour prendre en charge ces scénarios le [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package inclut un type `EphemeralDataProtectionProvider`. Ce type fournit une implémentation de base de `IDataProtectionProvider` clé dont le référentiel est maintenu en mémoire uniquement et n’est pas écrit dans n’importe quel magasin de stockage.

Chaque instance de `EphemeralDataProtectionProvider` utilise sa propre clé principale unique. Par conséquent, si un `IDataProtector` enracinée à un `EphemeralDataProtectionProvider` génère une charge utile protégée, cette charge utile peut uniquement être protégée par un équivalent `IDataProtector` (les données sont identiques [objectif](../consumer-apis/purpose-strings.md#data-protection-consumer-apis-purposes) chaîne) à la même racine `EphemeralDataProtectionProvider` instance.

L’exemple suivant montre comment instancier un `EphemeralDataProtectionProvider` et son utilisation pour protéger et déprotéger les données.

```csharp
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

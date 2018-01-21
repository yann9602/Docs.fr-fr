---
title: "Limiter la durée de vie de charges utiles protégés"
author: rick-anderson
description: "Ce document explique comment limiter la durée de vie d’un contenu protégé à l’aide de l’API de protection des données ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 144097cd1551c1d0aece5df20ce01e14146a41d1
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="limiting-the-lifetime-of-protected-payloads"></a>Limiter la durée de vie de charges utiles protégés

Il existe des scénarios où le développeur souhaite créer une charge utile protégée qui expire après un laps de temps défini. Par exemple, la charge utile protégée peut représenter un jeton de réinitialisation de mot de passe qui ne doit être valid pendant une heure. Il est tout à fait possible aux développeurs de créer leur propres format de charge utile qui contient une date d’expiration incorporé, et les développeurs expérimentés peuvent utile quand même, mais pour la majorité des développeurs de la gestion de ces délais d’expiration peut atteindre fastidieuse.

Pour faciliter cette opération pour notre public de développeur, le package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contient les API de l’utilitaire pour la création de charges utiles qui expirent automatiquement après un laps de temps défini. Ces API de blocage de le `ITimeLimitedDataProtector` type.

## <a name="api-usage"></a>Utilisation de l’API

Le `ITimeLimitedDataProtector` interface est l’interface de base pour protéger et déprotéger les charges utiles de durée limitée / automatique qui arrive à expiration. Pour créer une instance d’un `ITimeLimitedDataProtector`, vous devez tout d’abord une instance d’une expression régulière [IDataProtector](overview.md) construit avec un objet spécifique. Une fois la `IDataProtector` instance n’est disponible, appelez le `IDataProtector.ToTimeLimitedDataProtector` méthode d’extension pour retourner un protecteur d’avec les fonctionnalités d’expiration prédéfinie.

`ITimeLimitedDataProtector`expose les méthodes de surface et les extensions d’API suivantes :

* CreateProtector (fin de chaîne) : ITimeLimitedDataProtector - cette API méthode est similaire à l’objet existant `IDataProtectionProvider.CreateProtector` car il peut être utilisé pour créer [objectif chaînes](purpose-strings.md) à partir d’un protecteur de durée limitée racine.

* Protéger (byte [] texte brut, d’expiration de DateTimeOffset) : byte]

* Protéger (texte en clair de byte [], durée de vie TimeSpan) : byte]

* Protéger (texte en clair de byte []) : byte]

* Protéger (texte en clair chaîne, d’expiration de DateTimeOffset) : chaîne

* Protéger (texte en clair de chaîne, durée de vie TimeSpan) : chaîne

* Protéger (texte en clair de chaîne) : chaîne

En plus du noyau `Protect` les méthodes qui prennent uniquement le texte en clair, il existe de nouvelles surcharges qui permettent de spécifier la date d’expiration de la charge utile. La date d’expiration peut être spécifiée comme une date absolue (via une `DateTimeOffset`) ou sous la forme d’une heure relative (à partir du système actuel time, via un `TimeSpan`). Si une surcharge qui n’accepte pas un délai d’expiration est appelée, la charge utile est censée ne jamais pour expirer.

* Ôter la protection (protectedData [] octets, délai d’expiration de DateTimeOffset) : byte]

* Ôter la protection (protectedData de [] octets) : byte]

* Ôter la protection (chaîne protectedData, délai d’expiration de DateTimeOffset) : chaîne

* Ôter la protection (chaîne protectedData) : chaîne

Le `Unprotect` méthodes retournent les données non protégées d’origine. Si la charge utile n’a pas encore expiré, l’expiration absolue est retournée en tant que paramètre, ainsi que les données non protégées d’origine de sortie facultatif. Si la charge utile est expirée, toutes les surcharges de la méthode Unprotect lèvera CryptographicException.

>[!WARNING]
> Il est conseillé de ne pas utiliser ces API pour protéger les charges utiles qui nécessitent la persistance à long terme ou indéfinie. « Mes moyens pour les charges utiles protégés d’être sont définitivement irrécupérables après un mois ? » peut servir de règle empirique ; Si la réponse est aucune développeurs puis n’envisagez autre API.

L’exemple ci-dessous utilise le [chemins de code de non-DI](../configuration/non-di-scenarios.md) pour instancier le système de protection des données. Pour exécuter cet exemple, assurez-vous que vous avez tout d’abord ajouté une référence au package Microsoft.AspNetCore.DataProtection.Extensions.

[!code-csharp[Main](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]

---
title: "Limiter la durée de vie de charges utiles protégés"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 000175e2-10fc-43dd-bfc2-51e004b97b44
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 4ff13803b328c1e9dd2934c38c88b43f5798de03
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="limiting-the-lifetime-of-protected-payloads"></a>Limiter la durée de vie de charges utiles protégés

Il existe des scénarios où le développeur souhaite créer une charge utile protégée qui expire après un laps de temps défini. Par exemple, la charge utile protégée peut représenter un jeton de réinitialisation de mot de passe qui ne doit être valid pendant une heure. Il est tout à fait possible aux développeurs de créer leur propres format de charge utile qui contient une date d’expiration incorporé, et les développeurs expérimentés peuvent utile quand même, mais pour la majorité des développeurs de la gestion de ces délais d’expiration peut atteindre fastidieuse.

Pour faciliter cette opération pour notre public de développeur, le package Microsoft.AspNetCore.DataProtection.Extensions contient les API de l’utilitaire pour la création de charges utiles qui expirent automatiquement après un laps de temps défini. Ces API de blocage sur le type ITimeLimitedDataProtector.

## <a name="api-usage"></a>Utilisation de l’API

L’interface ITimeLimitedDataProtector est l’interface principale pour protéger et déprotéger limitée dans le temps / expiration automatique des charges utiles. Pour créer une instance d’un ITimeLimitedDataProtector, vous devez tout d’abord une instance d’une expression régulière [IDataProtector](overview.md) construit avec un objet spécifique. Une fois que l’instance de IDataProtector est disponible, appelez la méthode d’extension IDataProtector.ToTimeLimitedDataProtector pour retourner un protecteur d’avec les fonctionnalités d’expiration prédéfinie.

ITimeLimitedDataProtector expose les méthodes de surface et les extensions d’API suivantes :

* CreateProtector (fin de chaîne) : ITimeLimitedDataProtector cette API est similaire à la IDataProtectionProvider.CreateProtector existant car il peut être utilisé pour créer [objectif chaînes](purpose-strings.md) à partir d’un protecteur de durée limitée racine.

* Protéger (byte [] texte brut, d’expiration de DateTimeOffset) : byte]

* Protéger (texte en clair de byte [], durée de vie TimeSpan) : byte]

* Protéger (texte en clair de byte []) : byte]

* Protéger (texte en clair chaîne, d’expiration de DateTimeOffset) : chaîne

* Protéger (texte en clair de chaîne, durée de vie TimeSpan) : chaîne

* Protéger (texte en clair de chaîne) : chaîne

Outre les méthodes de Protect cœur qui prennent uniquement le texte en clair, il existe de nouvelles surcharges qui permettent de spécifier la date d’expiration de la charge utile. La date d’expiration peut être spécifiée sous la forme d’une date absolue (via un DateTimeOffset) ou une heure relative (à partir de l’heure système actuelle, via un intervalle de temps). Si une surcharge qui n’accepte pas un délai d’expiration est appelée, la charge utile est censée ne jamais pour expirer.

* Ôter la protection (protectedData [] octets, délai d’expiration de DateTimeOffset) : byte]

* Ôter la protection (protectedData de [] octets) : byte]

* Ôter la protection (chaîne protectedData, délai d’expiration de DateTimeOffset) : chaîne

* Ôter la protection (chaîne protectedData) : chaîne

Les méthodes de suppression de la protection retournent les données non protégées d’origine. Si la charge utile n’a pas encore expiré, l’expiration absolue est retournée en tant que paramètre, ainsi que les données non protégées d’origine de sortie facultatif. Si la charge utile est expirée, toutes les surcharges de la méthode Unprotect lèvera CryptographicException.

>[!WARNING]
> Il est conseillé de ne pas utiliser ces API pour protéger les charges utiles qui nécessitent la persistance à long terme ou indéfinie. « Mes moyens pour les charges utiles protégés d’être sont définitivement irrécupérables après un mois ? » peut servir de règle empirique ; Si la réponse est aucune développeurs puis n’envisagez autre API.

L’exemple ci-dessous utilise le [chemins de code de non-DI](../configuration/non-di-scenarios.md) pour instancier le système de protection des données. Pour exécuter cet exemple, assurez-vous que vous avez tout d’abord ajouté une référence au package Microsoft.AspNetCore.DataProtection.Extensions.

[!code-none[Main](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]

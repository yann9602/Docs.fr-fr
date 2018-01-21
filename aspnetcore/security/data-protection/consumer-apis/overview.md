---
title: "Vue d’ensemble des API de consommateur"
author: rick-anderson
description: "Ce document fournit une vue d’ensemble du consommateur diverses API disponibles dans la bibliothèque ASP.NET Core data protection."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 5ec11dce3ba485a84b6ce5f7ddaf16430162659c
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="consumer-apis-overview"></a>Vue d’ensemble des API de consommateur

Le `IDataProtectionProvider` et `IDataProtector` les interfaces sont les interfaces de base par le biais duquel les consommateurs utilisent le système de protection des données. Ils se trouvent dans le [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) package.

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

L’interface du fournisseur représente la racine du système de protection des données. Il ne peut pas être utilisé directement pour protéger ou ôter la protection des données. Au lieu de cela, le consommateur doit obtenir une référence à un `IDataProtector` en appelant `IDataProtectionProvider.CreateProtector(purpose)`, où le but est une chaîne qui décrit le cas d’utilisation prévue de consommateur. Consultez [objectif chaînes](purpose-strings.md) pour plus d’informations sur l’intention de ce paramètre et comment choisir une valeur appropriée.

## <a name="idataprotector"></a>IDataProtector

L’interface de protecteur est retourné par un appel à `CreateProtector`, et il est cette interface dans laquelle les utilisateurs peuvent utiliser pour effectuer protéger et déprotéger les opérations.

Pour protéger une partie des données, passer les données à le `Protect` (méthode). L’interface de base définit une méthode qui convertit byte [-] -> byte [], mais il existe également une surcharge (fournie en tant qu’une méthode d’extension) qui convertit la chaîne -> chaîne. La sécurité offerte par les deux méthodes est identique. le développeur doit choisir quelle que soit la surcharge est plus pratique pour les cas d’utilisation. Quelle que soit la surcharge choisie, la valeur retournée par la protection méthode est désormais protégée (enciphered et répondre à tous les systèmes), et l’application peut envoyer à un client non fiable.

Pour ôter la protection d’une donnée précédemment protégé, passer les données protégées sur le `Unprotect` (méthode). (Il n’y byte []-basé sur chaîne et en fonction des surcharges pour des raisons pratiques de développement.) Si la charge utile protégée a été générée par un appel précédent à `Protect` sur ce même `IDataProtector`, le `Unprotect` méthode retournera la charge non protégée d’origine. Si la charge utile protégée a été falsifiée ou a été créée par un autre `IDataProtector`, le `Unprotect` méthode lèvera CryptographicException.

Le concept d’identiques ou différents `IDataProtector` ties sauvegarder sur le concept d’objectif. Si deux `IDataProtector` les instances ont été générées à partir de la même racine `IDataProtectionProvider` mais via des chaînes d’objectif différent dans l’appel à `IDataProtectionProvider.CreateProtector`, puis ils sont considérés comme [protecteurs différentes](purpose-strings.md), et une ne pourrez pas ôter la protection charges utiles générées par l’autre.

## <a name="consuming-these-interfaces"></a>Consommation de ces interfaces.

Pour un composant prenant en charge DI, l’utilisation prévue est que le composant prend un `IDataProtectionProvider` dans son constructeur et que le système DI fournit automatiquement ce service lorsque le composant est instancié.

> [!NOTE]
> Certaines applications (telles que les applications console ou les applications ASP.NET 4.x) ne peuvent pas être DI prenant en charge ne pouvez pas utiliser le mécanisme décrit ici. Pour ces scénarios, consultez le [Non scénarios DI](../configuration/non-di-scenarios.md) document pour plus d’informations sur l’obtention d’une instance d’un `IDataProtection` fournisseur sans passer par DI.

L’exemple suivant illustre trois concepts :

1. [Ajout du système de protection des données](../configuration/overview.md) au conteneur de service,

2. À l’aide de DI pour recevoir une instance d’un `IDataProtectionProvider`, et

3. Création d’un `IDataProtector` d’un `IDataProtectionProvider` et son utilisation pour protéger et déprotéger les données.

[!code-csharp[Main](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Le package Microsoft.AspNetCore.DataProtection.Abstractions contient une méthode d’extension `IServiceProvider.GetDataProtector` en tant que développeur pour des raisons pratiques. Comme une seule opération, qu’il encapsule à la fois la récupération une `IDataProtectionProvider` à partir du fournisseur de services et l’appel `IDataProtectionProvider.CreateProtector`. L’exemple suivant illustre son utilisation.

[!code-csharp[Main](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Instances de `IDataProtectionProvider` et `IDataProtector` sont thread-safe pour les appelants plusieurs. Il est prévu que, une fois qu’un composant obtient une référence à une `IDataProtector` via un appel à `CreateProtector`, il utilisera cette référence pour les appels multiples à `Protect` et `Unprotect`. Un appel à `Unprotect` lève CryptographicException si la charge utile protégée ne peut pas être vérifiée ou déchiffrée. Certains composants peuvent souhaiter ignorer les erreurs pendant les opérations ; ôter la protection un composant qui lit les cookies d’authentification peut gérer cette erreur et traiter la demande comme s’il n’avait aucun cookie tout plutôt qu’échouer la requête ferme. Les composants dont vous souhaitez que ce comportement doivent spécifiquement intercepter CryptographicException au lieu d’absorber toutes les exceptions.

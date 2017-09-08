---
title: "Vue d’ensemble des API de consommateur"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: f69beb9d-a519-43a8-857c-f6b01886a903
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: d23a6ce50eef71f393124b9420f4ba473904d8b4
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="consumer-apis-overview"></a>Vue d’ensemble des API de consommateur

Les interfaces IDataProtectionProvider et IDataProtector sont les interfaces de base par le biais duquel les consommateurs utilisent le système de protection des données. Ils se trouvent dans le package Microsoft.AspNetCore.DataProtection.Abstractions.

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

L’interface du fournisseur représente la racine du système de protection des données. Il ne peut pas être utilisé directement pour protéger ou ôter la protection des données. Au lieu de cela, le consommateur doit obtenir une référence à un IDataProtector en appelant IDataProtectionProvider.CreateProtector(purpose), où objectif est une chaîne qui décrit le cas d’utilisation prévue de consommateur. Consultez [objectif chaînes](purpose-strings.md) pour plus d’informations sur l’intention de ce paramètre et comment choisir une valeur appropriée.

## <a name="idataprotector"></a>IDataProtector

L’interface de protecteur est retourné par un appel à CreateProtector, et c’est cette interface dans laquelle les utilisateurs peuvent utiliser pour effectuer protéger et déprotéger les opérations.

Pour protéger une partie des données, passer les données à la méthode de protection. L’interface de base définit une méthode qui convertit byte [-] -> byte [], mais il existe également une surcharge (fournie en tant qu’une méthode d’extension) qui convertit la chaîne -> chaîne. La sécurité offerte par les deux méthodes est identique. le développeur doit choisir quelle que soit la surcharge est plus pratique pour les cas d’utilisation. Quelle que soit la surcharge choisie, la valeur retournée par la protection méthode est désormais protégée (enciphered et répondre à tous les systèmes), et l’application peut envoyer à un client non fiable.

Pour ôter la protection d’une donnée précédemment protégé, passer les données protégées à la méthode de suppression de la protection. (Il n’y byte []-basé sur chaîne et en fonction des surcharges pour des raisons pratiques de développement.) Si la charge utile protégée a été générée par un appel précédent à protéger sur ce même IDataProtector, la méthode Unprotect retournera la charge non protégée d’origine. Si la charge utile protégée a été falsifiée ou a été créée par un IDataProtector différents, la méthode Unprotect lèvera CryptographicException.

Le concept de même et différents IDataProtector se réfère au concept d’objectif. Si deux instances de IDataProtector ont été générés à partir de la même racine IDataProtectionProvider mais via des chaînes d’objectif différent dans l’appel à IDataProtectionProvider.CreateProtector, ils sont considérés comme [différents protecteurs](purpose-strings.md), et une ne pourrez pas ôter la protection des charges utiles générées par l’autre.

## <a name="consuming-these-interfaces"></a>Consommation de ces interfaces.

Pour un composant prenant en charge DI, l’utilisation prévue est que le composant prendre un paramètre de IDataProtectionProvider dans son constructeur, et que le système DI fournit automatiquement ce service lorsque le composant est instancié.

> [!NOTE]
> Certaines applications (telles que les applications console ou les applications ASP.NET 4.x) ne peuvent pas être DI prenant en charge ne pouvez pas utiliser le mécanisme décrit ici. Pour ces scénarios, consultez le [Non scénarios DI](../configuration/non-di-scenarios.md) document pour plus d’informations sur l’obtention d’une instance d’un fournisseur d’IDataProtection sans passer par DI.

L’exemple suivant illustre trois concepts :

1. [Ajout du système de protection des données](../configuration/overview.md) au conteneur de service,

2. À l’aide de DI pour recevoir une instance d’un IDataProtectionProvider, et

3. Création d’un IDataProtector à partir d’un IDataProtectionProvider et son utilisation pour protéger et déprotéger les données.

[!code-csharp[Main](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Le package Microsoft.AspNetCore.DataProtection.Abstractions contient une méthode d’extension IServiceProvider.GetDataProtector en tant que développeur pour des raisons pratiques. Elle encapsule comme une seule opération à la fois la récupération d’un IDataProtectionProvider à partir du fournisseur de service et en appelant IDataProtectionProvider.CreateProtector. L’exemple suivant illustre son utilisation.

[!code-csharp[Main](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Instances de IDataProtectionProvider et IDataProtector sont thread-safe pour les appelants plusieurs. Il est prévu qu’une fois qu’un composant obtient une référence à un IDataProtector via un appel à CreateProtector, il utilisera cette référence pour les appels multiples à protéger et Unprotect Unprotect.A appel lève CryptographicException si la charge utile protégée ne peut pas être vérifié ou déchiffrés. Certains composants peuvent souhaiter ignorer les erreurs pendant les opérations ; ôter la protection un composant qui lit les cookies d’authentification peut gérer cette erreur et traiter la demande comme s’il n’avait aucun cookie tout plutôt qu’échouer la requête ferme. Les composants dont vous souhaitez que ce comportement doivent spécifiquement intercepter CryptographicException au lieu d’absorber toutes les exceptions.

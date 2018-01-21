---
title: "Gestion de clés"
author: rick-anderson
description: "Ce document décrit les détails d’implémentation de la gestion de clés d’ASP.NET Core données protection API."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: 53adb067751917a9539a310bb7d91e599696f213
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="key-management"></a>Gestion de clés

<a name="data-protection-implementation-key-management"></a>

Le système de protection de données gère automatiquement la durée de vie des clés principales utilisées pour protéger et déprotéger les charges utiles. Chaque clé peut exister dans un des quatre étapes :

* Créé - la clé existe dans l’anneau de clé, mais n’a pas encore été activée. La clé ne doit pas être utilisée pour les nouvelles opérations de protéger jusqu'à ce que suffisamment de temps écoulé que la clé a eu l’occasion de se propager à tous les ordinateurs qui utilisent cette boucle de clé.

* Active : la clé existe dans l’anneau de clé et doit être utilisé pour toutes les opérations de protéger de nouveau.

* Expiré - la clé a été exécuté sa durée de vie naturelle et ne doit plus être utilisée pour les nouvelles opérations de protection.

* Révoqué - la clé est compromise et ne doit pas être utilisée pour les nouvelles opérations de protection.

Clés créées, actifs et expirés peuvent utilisés pour la protection des charges utiles entrants. Clés révoqués par défaut ne sont pas utilisable pour ôter la protection des charges utiles, mais le développeur d’applications peut [remplacer ce comportement](../consumer-apis/dangerous-unprotect.md#data-protection-consumer-apis-dangerous-unprotect) si nécessaire.

>[!WARNING]
> Le développeur peut être tenté de supprimer une clé à partir de l’anneau de clé (par exemple, en supprimant le fichier correspondant du système de fichiers). À ce stade, toutes les données protégées par la clé est définitivement indéchiffrables et aucun remplacement en cas d’urgence, comme avec des clés révoqués. Suppression d’une clé est comportement destructeur réellement et, par conséquent, le système de protection de données n’expose aucune API de première classe pour effectuer cette opération.

## <a name="default-key-selection"></a>Sélection de la clé par défaut

Lorsque le système de protection de données lit l’anneau de clé à partir du référentiel de stockage, il va tenter de rechercher une clé « default » à partir de l’anneau de clé. La clé par défaut est utilisée pour les nouvelles opérations de protection.

L’heuristique générale est que le système de protection de données choisit la clé avec la dernière date d’activation en tant que la clé par défaut. (Il existe un petit manœuvre pour autoriser l’inclinaison d’horloge de serveur à serveur.) Si la clé est expirée ou révoquée, et si l’application n’a pas désactivé automatique de génération de clé, une nouvelle clé est générée avec l’activation immédiate par le [d’expiration et la restauration de clé](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) stratégie ci-dessous.

La raison pour laquelle le système de protection de données génère une nouvelle clé immédiatement au lieu de revenir à une autre clé est qu’une nouvelle génération de clé doit être traitée comme un délai d’expiration implicite de toutes les clés qui ont été activés avant la nouvelle clé. L’idée générale est que les nouvelles clés ont été configurés avec des algorithmes différents ou de mécanismes de chiffrement au repos que les anciennes clés, et le système doit préférer le retour de la configuration actuelle.

Il existe une exception. Si le développeur a [désactivé la génération automatique de clés](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), le système de protection des données doit choisir un élément en tant que la clé par défaut. Dans ce scénario de secours, le système choisira la clé non révoqué avec la dernière date d’activation, de préférence vers les clés qui ont eu le temps de se propager à d’autres ordinateurs du cluster. Le système de secours peut se retrouver en choisissant une clé par défaut a expiré en conséquence. Le système de secours ne sera jamais choisir une clé révoquée en tant que la clé par défaut, et si l’anneau de clé est vide ou chaque clé a été révoqué le système produira une erreur lors de l’initialisation.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Expiration de clés et de restauration

Lorsqu’une clé est créée, elle reçoit automatiquement une date d’activation {now + 2 jours} et une date d’expiration de {now + 90 jours}. Le délai de 2 jours avant l’activation donne le temps clé se propagent dans le système. Autrement dit, il permet aux autres applications pointant vers le magasin de stockage observer la clé à leur prochaine période d’actualisation automatique, optimisant ainsi le risque que lorsque la clé anneau active fait deviennent s’il est propagée à toutes les applications devront peut-être l’utiliser.

Si la clé par défaut va expirer dans les 2 jours, et si la boucle de clé n’a pas déjà une clé qui sera active lors de l’expiration de la clé par défaut, le système de protection des données est conservées automatiquement une nouvelle clé dans l’anneau de clé. Cette nouvelle clé a une date d’activation {date d’expiration de la clé par défaut} et une date d’expiration de {now + 90 jours}. Ainsi, le système restaurer automatiquement les clés sur une base régulière sans aucune interruption de service.

Il peut y avoir des circonstances où une clé sera créée avec l’activation immédiate. Un exemple serait lorsque l’application n’a pas exécuté pendant une période et toutes les clés dans l’anneau de clé ont expiré. Dans ce cas, la clé reçoit une date d’activation de {maintenant} sans le délai d’activation de 2 jours normal.

La durée de vie de clé par défaut est 90 jours, mais ce n’est configurable dans l’exemple suivant.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Un administrateur peut également modifier la valeur par défaut au niveau du système, si un appel explicite à `SetDefaultKeyLifetime` remplace toute stratégie à l’échelle du système. La durée de vie de clé par défaut ne peut pas être inférieure à 7 jours.

## <a name="automatic-key-ring-refresh"></a>Actualisation de l’anneau de clé automatique

Lorsque le système de protection des données s’initialise, il lit l’anneau de clé à partir du référentiel sous-jacent et met en cache en mémoire. Ce cache permet les opérations de protection et de suppression de la protection continuer sans atteindre le magasin de stockage. Le système vérifie automatiquement le magasin de stockage pour les modifications environ toutes les 24 heures ou de l’expiration de la clé par défaut en cours, selon ce qui se produit en premier.

>[!WARNING]
> Les développeurs doivent rarement (le cas échéant) à utiliser directement les API de gestion des clés. Le système de protection de données effectue la gestion automatique des clés comme décrit ci-dessus.

Le système de protection de données expose une interface `IKeyManager` qui peut être utilisé pour inspecter et apporter des modifications à l’anneau de clé. Le système DI qui a fourni l’instance de `IDataProtectionProvider` peut également fournir une instance de `IKeyManager` pour votre consommation. Ou bien, vous pouvez extraire le `IKeyManager` directement à partir de la `IServiceProvider` comme dans l’exemple ci-dessous.

Toute opération qui modifie l’anneau de clé (création d’une nouvelle clé explicitement ou effectuer une révocation) invalide le cache en mémoire. L’appel suivant à `Protect` ou `Unprotect` entraîne le système de protection des données à relire l’anneau de clé et à recréer le cache.

L’exemple ci-dessous montre comment utiliser le `IKeyManager` interface pour inspecter et de manipuler l’anneau de clé, y compris la révocation existant de clés et de la génération d’une nouvelle clé manuellement.

[!code-csharp[Main](key-management/samples/key-management.cs)]

## <a name="key-storage"></a>Stockage de clés

Le système de protection de données a une heuristique dans laquelle il essaie automatiquement de déduire un emplacement de stockage de clé approprié et le chiffrement au mécanisme de rest. Cela est également configurable par le développeur de l’application. Les documents suivants décrivent les implémentations de boîte aux lettres de ces mécanismes :

* [Fournisseurs de stockage de clés de boîte aux lettres](key-storage-providers.md#data-protection-implementation-key-storage-providers)

* [Boîte de chiffrement à clé auprès des fournisseurs de rest](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest-providers)

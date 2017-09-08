---
title: "Scénarios non DI"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a7d8a962-80ff-48e3-96f6-8472b7ba2df9
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 54a930c26f9f48ea0e6f7865e2927bcde0f4d6c0
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="non-di-aware-scenarios"></a>Scénarios non DI

Le système de protection des données est généralement conçu [à ajouter à un conteneur de service](../consumer-apis/overview.md) et doivent être fournies pour les composants dépendants via un mécanisme DI. Toutefois, il peut être certains cas où cela n’est pas possible, en particulier lors de l’importation du système dans une application existante.

Pour prendre en charge ces scénarios le package Microsoft.AspNetCore.DataProtection.Extensions fournit un type concret DataProtectionProvider qui offre un moyen simple d’utiliser le système de protection des données sans passer par les chemins de code DI spécifiques. Le type lui-même implémente IDataProtectionProvider, et la construction d’elle est aussi simple que de fournir un DirectoryInfo où les clés de chiffrement de ce fournisseur doivent être stockées.

Exemple :

[!code-none[Main](non-di-scenarios/_static/nodisample1.cs)]

>[!WARNING]
> Par défaut le DataProtectionProvider type concret ne chiffre pas les matières premières de clés avant de le rendre au système de fichiers. Il s’agit pour prendre en charge les scénarios où les points de développeur à un réseau partagent, auquel cas le système de protection des données ne peut pas déduire automatiquement un mécanisme de chiffrement de clé approprié au repos.
>
>En outre, le type concret DataProtectionProvider n’est pas [isoler les applications](overview.md#data-protection-configuration-per-app-isolation) par défaut, par conséquent, toutes les applications de pointer vers le même répertoire clé peuvent partager les charges utiles tant que leurs objectif les paramètres correspondent aux.

Le développeur d’applications peut traiter ces deux si vous le souhaitez. Le constructeur DataProtectionProvider accepte un [rappel de configuration facultatives](overview.md#data-protection-configuration-callback) qui peut être utilisé pour modifier les comportements du système. L’exemple ci-dessous illustre la restauration d’isolement via un appel explicite à SetApplicationName, et il montre également la configuration du système pour chiffrer automatiquement des clés persistantes à l’aide de DPAPI Windows. Si le répertoire pointe vers un partage UNC, vous pouvez d’à distribuer un certificat partagé sur tous les ordinateurs appropriés et pour configurer le système pour utiliser le chiffrement de certificat à la place via un appel à [ProtectKeysWithCertificate](overview.md#configuring-x509-certificate).

[!code-none[Main](non-di-scenarios/_static/nodisample2.cs)]

>[!TIP]
> Les instances du type concret DataProtectionProvider sont coûteuses à créer. Si une application gère plusieurs instances de ce type, et si elles êtes pointe sur le même répertoire de stockage de clés, les performances de l’application peuvent se dégrader. L’utilisation prévue est que le développeur d’applications d’instancier une fois ce type de conserver la réutilisation de cette référence unique autant que possible. Le type DataProtectionProvider et toutes les instances de IDataProtector créés à partir de celui-ci sont thread-safe pour les appelants plusieurs.

---
title: "Scénarios pour la Protection des données dans ASP.NET Core non-DI"
author: rick-anderson
description: "Découvrez comment prendre en charge les scénarios de protection de données où vous ne peut pas ou ne souhaitez pas utiliser un service fourni par injection de dépendances."
keywords: "ASP.NET Core, protection des données, l’injection de dépendance, DataProtectionProvider"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a7d8a962-80ff-48e3-96f6-8472b7ba2df9
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 375eecf649819dce8f1c2ba30e1cb6451d1c1253
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Scénarios pour la Protection des données dans ASP.NET Core non-DI

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Le système de Protection des données ASP.NET Core est normalement [ajouté à un conteneur de service](xref:security/data-protection/consumer-apis/overview) et consommé par les composants dépendants via l’injection de dépendance (DI). Toutefois, il existe des cas où cela n’est pas possible ou souhaitée, en particulier lors de l’importation du système dans une application existante.

Pour prendre en charge ces scénarios, le [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) package fournit un type concret, [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), qui offre un moyen simple d’utiliser la Protection des données sans se baser sur DI. Le `DataProtectionProvider` type implémente [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). Construction `DataProtectionProvider` requiert l’installation en fournissant un [DirectoryInfo](/dotnet/api/system.io.directoryinfo) instance pour indiquer où les clés de chiffrement du fournisseur doivent être stockées, comme dans l’exemple de code suivant :

[!code-none[Main](non-di-scenarios/_static/nodisample1.cs)]

Par défaut, le `DataProtectionProvider` type concret ne chiffre pas les matières premières de clés avant de le rendre au système de fichiers. Il s’agit pour prendre en charge les scénarios où les points de développeur à un partage réseau et le système de Protection des données ne peut pas déduire automatiquement un mécanisme de chiffrement de clé approprié au repos.

En outre, le `DataProtectionProvider` type concret ne [isoler les applications](xref:security/data-protection/configuration/overview#per-application-isolation) par défaut. Toutes les applications en utilisant le même répertoire clé peuvent partager les charges utiles tant que leurs [objectif paramètres](xref:security/data-protection/consumer-apis/purpose-strings) correspondent.

Le [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) constructeur accepte un rappel de configuration facultative qui peut être utilisé pour ajuster les comportements du système. L’exemple ci-dessous illustre la restauration d’isolation avec un appel explicite à [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname). L’exemple illustre également la configuration du système pour chiffrer automatiquement des clés persistantes à l’aide de DPAPI Windows. Si le répertoire pointe vers un partage UNC, vous pouvez d’à distribuer un certificat partagé sur tous les ordinateurs appropriés et pour configurer le système pour utiliser le chiffrement de base de certificat avec un appel à [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).

[!code-none[Main](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> Instances de la `DataProtectionProvider` type concret sont coûteuses à créer. Si une application conserve plusieurs instances de ce type et ils tous utilisent le même répertoire de stockage de clés, peut dégrader les performances de l’application. Si vous utilisez la `DataProtectionProvider` type, nous recommandons que vous créez ce type une seule fois et réutilisez autant que possible. Le `DataProtectionProvider` type et tous les [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) instances créées à partir de celui-ci sont thread-safe pour les appelants plusieurs.

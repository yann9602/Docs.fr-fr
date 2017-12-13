---
title: "Configuration de la protection des données dans ASP.NET Core"
author: rick-anderson
description: "Consultez les rubriques qui expliquent comment configurer la protection des données dans ASP.NET Core."
keywords: "ASP.NET Core,protection des données"
ms.author: riande
manager: wpickett
ms.date: 10/12/2017
ms.topic: article
ms.assetid: 517047b3-b5a9-4b5a-9084-01dae4c74cef
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/index
ms.openlocfilehash: a502de94a0f7177f29c95f6a6a4468a715e66983
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="data-protection-configuration-in-aspnet-core"></a>Configuration de la protection des données dans ASP.NET Core

Consultez les rubriques suivantes pour en savoir plus sur la configuration de la protection des données dans ASP.NET Core :

* [Configuration de la protection des données](xref:security/data-protection/configuration/overview)  
  Vue d’ensemble de la configuration de la protection des données ASP.NET Core.

* [Gestion et durée de vie des clés de protection des données](xref:security/data-protection/configuration/default-settings)  
  Informations sur la gestion et la durée de vie des clés de protection des données.

* [Prise en charge de la stratégie au niveau de l’ordinateur de protection des données](xref:security/data-protection/configuration/machine-wide-policy)  
  Informations sur la définition d’une stratégie par défaut au niveau de l’ordinateur pour toutes les applications qui utilisent la protection des données.

* [Scénarios non compatibles avec l’injection de dépendances pour la protection des données dans ASP.NET Core](xref:security/data-protection/configuration/non-di-scenarios)  
  Indique comment utiliser le type concret [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider) pour utiliser la protection des données sans passer par des chemins de code spécifiques à l’injection de dépendances.

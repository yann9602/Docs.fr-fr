---
title: "Chaînes d’objectif dans ASP.NET Core"
author: rick-anderson
description: "Ce document décrit la hiérarchie de chaîne de fin et une architecture mutualisée par rapport à l’API de protection des données ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: e7b08df32069cf1243630accda82eb6250594c53
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Hiérarchie d’objet et une architecture mutualisée dans ASP.NET Core

Dans la mesure où un `IDataProtector` est également implicitement un `IDataProtectionProvider`, à des fins peuvent être chaînés ensemble. Dans ce sens, `provider.CreateProtector([ "purpose1", "purpose2" ])` équivaut à `provider.CreateProtector("purpose1").CreateProtector("purpose2")`.

Ainsi, pour certaines relations hiérarchiques intéressantes via le système de protection des données. Dans l’exemple précédent de [Contoso.Messaging.SecureMessage](purpose-strings.md#data-protection-contoso-purpose), le composant SecureMessage peut appeler `provider.CreateProtector("Contoso.Messaging.SecureMessage")` fasse une seule fois et mettre en cache le résultat dans un privé `_myProvide` champ. Les protecteurs de futurs peuvent ensuite être créées via des appels à `_myProvider.CreateProtector("User: username")`, et ces protecteurs sont utilisées pour la sécurisation des messages individuels.

Cela peut également être inversé. Envisagez une seule application logique qui héberge plusieurs clients (un CMS semble raisonnable) et chaque client peuvent être configuré avec son propre système de gestion de l’authentification et l’état. L’application couvrant possède un seul fournisseur de master, et il appelle `provider.CreateProtector("Tenant 1")` et `provider.CreateProtector("Tenant 2")` pour donner à chaque client de sa propre tranche isolé du système de protection des données. Les locataires peuvent ensuite dériver leurs propres protecteurs individuels en fonction de leurs propres besoins, mais quel que soit le manuels ils tentent ne peut pas créer protecteurs qui entrent en conflit avec n’importe quel autre client dans le système. Sous forme de graphique, il est représenté comme ci-dessous.

![À des fins de location de multiples](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Cela suppose que le cadre des contrôles d’application les API sont disponibles pour les locataires individuels et que les clients ne peuvent pas exécuter du code arbitraire sur le serveur. Si un client peut exécuter du code arbitraire, qu’ils puissent exécuter réflexion privée pour interrompre la garantie d’isolation ou elles Impossible de lire directement la gestion de clés principales et dériver les sous-clés simplement qu’il souhaite.

Le système de protection de données utilise en fait une sorte d’une architecture mutualisée dans sa configuration de l’emploi par défaut. Par défaut, gestion des clés principales sont stockées dans le dossier du profil utilisateur du compte de processus de travail (ou le Registre, pour les identités du pool d’applications IIS). Mais il est réellement assez courant d’utiliser un seul compte pour exécuter plusieurs applications, et par conséquent, toutes ces applications risque d’entraîner un partage le maître de support de gestion. Pour résoudre ce problème, le système de protection des données insère automatiquement un identificateur unique par application en tant que le premier élément dans la chaîne de l’objectif global. Cet effet implicit qui sert à [isoler les applications individuelles](xref:security/data-protection/configuration/overview#per-application-isolation) entre eux en traitant efficacement chaque application comme un client unique dans le système et le processus de création de protecteur est identique à l’image ci-dessus.

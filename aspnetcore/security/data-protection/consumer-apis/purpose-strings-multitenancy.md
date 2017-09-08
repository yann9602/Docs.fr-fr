---
title: "Chaînes d’objectif dans ASP.NET Core"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 9d18c287-e0e6-4541-b09c-7fed45c902d9
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: dd87d8bcaf0056b322908e9a3ef75678f603e1e6
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Hiérarchie d’objet et une architecture mutualisée dans ASP.NET Core

Étant un IDataProtector également implicitement un IDataProtectionProvider, à des fins peuvent être chaînés ensemble. Dans ce fournisseur de détection. CreateProtector ([« purpose1 », « purpose2 »]) équivaut au fournisseur. CreateProtector("purpose1"). CreateProtector("purpose2").

Ainsi, pour certaines relations hiérarchiques intéressantes via le système de protection des données. Dans l’exemple précédent de [Contoso.Messaging.SecureMessage](purpose-strings.md#data-protection-contoso-purpose), le composant SecureMessage peut appeler le fournisseur. CreateProtector("Contoso.Messaging.SecureMessage") une fois qu’initial et le résultat dans un champ privé _myProvider de cache. Les protecteurs de futurs peuvent ensuite être créées via des appels à _myProvider.CreateProtector (« utilisateur : nom d’utilisateur »), et ces protecteurs sont utilisées pour la sécurisation des messages individuels.

Cela peut également être inversé. Envisagez une seule application logique qui héberge plusieurs clients (un CMS semble raisonnable) et chaque client peuvent être configuré avec son propre système de gestion de l’authentification et l’état. L’application couvrant possède un seul fournisseur de master, et il appelle le fournisseur. CreateProtector (« Tenant 1 ») et le fournisseur. CreateProtector (« Tenant 2 ») pour donner à chaque client de sa propre tranche isolé du système de protection des données. Les locataires peuvent ensuite dériver leurs propres protecteurs individuels en fonction de leurs propres besoins, mais quel que soit le manuels ils tentent ne peut pas créer protecteurs qui entrent en conflit avec n’importe quel autre client dans le système. Graphiquement, cela est représenté comme indiqué ci-dessous.

![À des fins de location de multiples](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Cela suppose que le cadre des contrôles d’application les API sont disponibles pour les locataires individuels et que les clients ne peuvent pas exécuter du code arbitraire sur le serveur. Si un client peut exécuter du code arbitraire, qu’ils puissent exécuter réflexion privée pour interrompre la garantie d’isolation ou elles Impossible de lire directement la gestion de clés principales et dériver les sous-clés simplement qu’il souhaite.

Le système de protection de données utilise en fait une sorte d’une architecture mutualisée dans sa configuration de l’emploi par défaut. Par défaut, gestion des clés principales sont stockées dans le dossier du profil utilisateur du compte de processus de travail (ou le Registre, pour les identités du pool d’applications IIS). Mais il est réellement assez courant d’utiliser un seul compte pour exécuter plusieurs applications, et par conséquent, toutes ces applications risque d’entraîner un partage le maître de support de gestion. Pour résoudre ce problème, le système de protection des données insère automatiquement un identificateur unique par application en tant que le premier élément dans la chaîne de l’objectif global. Cet effet implicit qui sert à [isoler les applications individuelles](../configuration/overview.md#data-protection-configuration-per-app-isolation) entre eux en traitant efficacement chaque application comme un client unique dans le système et le processus de création de protecteur est identique à l’image ci-dessus.

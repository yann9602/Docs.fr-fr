---
title: "Articles basés sur les projets créés avec des comptes d’utilisateur individuels"
author: rick-anderson
description: "Ce document répertorie les articles basés sur les projets créés avec des comptes d’utilisateur individuels."
keywords: "ASP.NET Core, d’autorisation, IAuthorizationService"
ms.author: riande
manager: wpickett
ms.date: 11/30/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/individual
ms.openlocfilehash: 1864625e0ad6b4ec6fc2ada3fa7d93edec91b633
ms.sourcegitcommit: 037d3900f739dbaa2ba14158e3d7dc81478952ad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="articles-based-on-projects-created-with-individual-user-accounts"></a>Articles basés sur les projets créés avec des comptes d’utilisateur individuels

Identité de ASP.NET Core est incluse dans les modèles de projet dans Visual Studio avec l’option « Comptes d’utilisateur individuels ».

Les modèles d’authentification sont disponibles dans l’interface CLI de base .NET avec `-au Individual`:

```console
dotnet new mvc -au Individual
dotnet new webapi -au Individual
dotnet new razor -au Individual
```

Les articles suivants montrent comment utiliser le code généré dans les modèles ASP.NET Core qui utilisent des comptes d’utilisateur individuels :

* [Authentification à deux facteurs avec SMS](xref:security/authentication/2fa)
* [Account confirmation and password recovery in ASP.NET Core (Confirmation de compte et récupération de mot de passe dans ASP.NET Core)](xref:security/authentication/accconfirm)
* [Créer une application ASP.NET Core et de données utilisateur protégées par l’autorisation](xref:security/authorization/secure-data)
---
title: Introduction
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a6a556ed-ba59-4107-9358-44cf20e5931b
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/introduction
ms.openlocfilehash: fa6dcbc0627181cd1aca0926fa008f3db907742f
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="introduction"></a>Introduction

<a name="security-authorization-introduction"></a>

Autorisation désigne le processus qui détermine ce qu’un utilisateur est en mesure d’effectuer. Par exemple, un utilisateur administratif est autorisé à créer une bibliothèque de documents, ajoutez les documents, modifier des documents et les supprimer. Un utilisateur non-administrateur, utilisation de la bibliothèque est uniquement autorisé à lire les documents.

L’autorisation est orthogonales et indépendante de l’authentification, qui est le processus d’évaluation qui est un utilisateur. L’authentification peut créer une ou plusieurs identités pour l’utilisateur actuel.

## <a name="authorization-types"></a>Types d’autorisation

Autorisation de ASP.NET Core fournit un simple déclaratif [rôle](roles.md#security-authorization-role-based) et un [enrichie basée sur la stratégie](policies.md#security-authorization-policies-based) modèle. L’autorisation est exprimée dans la configuration requise et gestionnaires d’évaluent les revendications d’un utilisateur par rapport aux exigences. Vérifications impératives peuvent reposer sur des stratégies simples ou qui évaluent l’identité de l’utilisateur et les propriétés de la ressource à laquelle l’utilisateur tente d’accéder.

## <a name="namespaces"></a>Espaces de noms

Les composants d’autorisation, y compris le `AuthorizeAttribute` et `AllowAnonymousAttribute` attributs sont trouvent dans le `Microsoft.AspNetCore.Authorization` espace de noms.

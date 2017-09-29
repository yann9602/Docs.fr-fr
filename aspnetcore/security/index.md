---
title: "Sécurité"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a8fb7eb7-e0e5-4394-84f3-1f1dbe012345
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/index
ms.openlocfilehash: f173d03f55a1ce52222a75c023f9e8a20d5c60dc
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="security"></a>Sécurité

*   [Authentification](authentication/index.md)
    *   [Présentation d’Identity](authentication/identity.md)
    *   [Activation de l’authentification via Facebook, Google et d’autres fournisseurs externes](authentication/social/index.md)
    * [Configurer l’authentification Windows](authentication/windowsauth.md)
    *   [Confirmation de compte et récupération de mot de passe](authentication/accconfirm.md)
    *   [Authentification à 2 facteurs avec SMS](authentication/2fa.md) 
    *   [Utilisation de l’authentification par cookie sans ASP.NET Core Identity](authentication/cookie.md)
    *   [Azure Active Directory](authentication/azure-active-directory/index.md)
        *   [Intégration d’Azure AD dans une application web ASP.NET Core](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-openidconnect-aspnetcore/)
        *   [Appel d’une API web ASP.NET Core à partir d’une application WPF via Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-native-aspnetcore/)
        *   [Appel d’une API web dans une application web ASP.NET Core via Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore/)
        *   [Une application web ASP.NET Core Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapp/)
    *   [Sécurisation des applications ASP.NET Core avec IdentityServer4](https://identityserver4.readthedocs.io)
*   [Autorisation](authorization/index.md)
    *   [Introduction](authorization/introduction.md)
    *   [Créer une application avec des données utilisateur protégées par une autorisation](xref:security/authorization/secure-data)
    *   [Autorisation simple](authorization/simple.md)
    *   [Autorisation basée sur les rôles](authorization/roles.md)
    *   [Autorisation basée sur des revendications](authorization/claims.md)
    *   [Autorisation basée sur une stratégie personnalisée](authorization/policies.md)
    *   [Injection de dépendances dans les gestionnaires d’exigences](authorization/dependencyinjection.md)
    *   [Autorisation basée sur les ressources](authorization/resourcebased.md)
    *   [Autorisation basée sur les vues](authorization/views.md)
    *   [Limitation de l’identité selon le schéma](authorization/limitingidentitybyscheme.md)
*   [Protection des données](data-protection/index.md)
    *   [Présentation de la protection des données](data-protection/introduction.md)
    *   [Bien démarrer avec les API de protection des données](data-protection/using-data-protection.md)
    *   [API de contrôle serveur consommateur](data-protection/consumer-apis/index.md)
        *   [Vue d’ensemble des API de contrôle serveur consommateur](data-protection/consumer-apis/overview.md)
        *   [Chaînes d’objectifs](data-protection/consumer-apis/purpose-strings.md)
        *   [Hiérarchie d’objectifs et architecture mutualisée](data-protection/consumer-apis/purpose-strings-multitenancy.md)
        *   [Hachage de mot de passe](data-protection/consumer-apis/password-hashing.md)
        *   [Limitation de la durée de vie des charges utiles protégées](data-protection/consumer-apis/limited-lifetime-payloads.md)
        *   [Retrait de la protection des charges utiles dont les clés ont été révoquées](data-protection/consumer-apis/dangerous-unprotect.md)
    *   [Configuration](data-protection/configuration/index.md)
        *   [Configuration de la protection des données](data-protection/configuration/overview.md)
        *   [Paramètres par défaut](data-protection/configuration/default-settings.md)
        *   [Stratégie à l’échelle de la machine](data-protection/configuration/machine-wide-policy.md)
        *   [Scénarios non compatibles avec l’injection de dépendances](data-protection/configuration/non-di-scenarios.md)
    *   [API d’extensibilité](data-protection/extensibility/index.md)
        *   [Extensibilité du chiffrement de base](data-protection/extensibility/core-crypto.md)
        *   [Extensibilité de la gestion de clés](data-protection/extensibility/key-management.md)
        *   [API diverses](data-protection/extensibility/misc-apis.md)
    *   [Implémentation](data-protection/implementation/index.md)
        *   [Détails du chiffrement authentifié](data-protection/implementation/authenticated-encryption-details.md)
        *   [Dérivation de sous-clé et chiffrement authentifié](data-protection/implementation/subkeyderivation.md)
        *   [En-têtes de contexte](data-protection/implementation/context-headers.md)
        *   [Gestion des clés](data-protection/implementation/key-management.md)
        *   [Fournisseurs de stockage de clés](data-protection/implementation/key-storage-providers.md)
        *   [Chiffrement à clé au repos](data-protection/implementation/key-encryption-at-rest.md)
        *   [Immuabilité des clés et modification des paramètres](data-protection/implementation/key-immutability.md)
        *   [Format de stockage des clés](data-protection/implementation/key-storage-format.md)
        *   [Fournisseurs de protection des données éphémères](data-protection/implementation/key-storage-ephemeral.md)
    *   [Compatibilité](data-protection/compatibility/index.md)
        *   [Partage des cookies entre les applications](data-protection/compatibility/cookie-sharing.md)
        *   [Remplacement de <machineKey> dans ASP.NET](data-protection/compatibility/replacing-machinekey.md)
*   [Créer une application avec des données utilisateur protégées par une autorisation](xref:security/authorization/secure-data)
*   [Stockage sécurisé des secrets de l’application durant le développement](app-secrets.md)
*   [Fournisseur de configuration Azure Key Vault](key-vault-configuration.md)
*   [Mise en œuvre de SSL](enforcing-ssl.md)
*   [Protection contre la falsification de requête](anti-request-forgery.md)
*   [Blocage des attaques par redirection ouverte](preventing-open-redirects.md)
*   [Blocage des scripts intersites](cross-site-scripting.md)
*   [Activation des requêtes d’origines différentes](cors.md)

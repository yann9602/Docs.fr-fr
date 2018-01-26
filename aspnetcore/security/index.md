---
title: "Vue d’ensemble de la sécurité ASP.NET Core | Microsoft Docs"
author: rachelappel
description: "Découvrir les concepts de base de l’authentification, de l’autorisation et de la sécurité dans ASP.NET Core"
ms.author: rachelap
manager: wpickett
ms.date: 11/01/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/index
ms.openlocfilehash: 3f0f1c402aeac388c2fcabb509aa8aa3a46e95f5
ms.sourcegitcommit: 3f491f887074310fc0f145cd01a670aa63b969e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="aspnet-core-security-overview"></a>Vue d’ensemble de la sécurité ASP.NET Core

ASP.NET Core permet aux développeurs de configurer et de gérer facilement la sécurité de leurs applications. ASP.NET Core contient des fonctionnalités permettant de gérer l’authentification, l’autorisation, la protection des données, l’implémentation de SSL, les secrets des applications, la protection contre la falsification de requêtes et la gestion CORS. Ces fonctionnalités de sécurité vous permettent de générer des applications ASP.NET Core robustes et néanmoins sécurisées. 

## <a name="aspnet-core-security-features"></a>Fonctionnalités de sécurité ASP.NET Core

ASP.NET Core offre un grand nombre d’outils et de bibliothèques pour sécuriser vos applications, notamment des fournisseurs d’identité intégrés. Cela dit, vous pouvez utiliser des services d’identité tiers tels que Facebook, Twitter ou LinkedIn. Avec ASP.NET Core, vous pouvez facilement gérer les secrets des applications, qui sont un moyen de stocker et d’utiliser des informations confidentielles sans avoir à les exposer dans le code. 

## <a name="authentication-vs-authorization"></a>Authentification et Autorisation

L’authentification est un processus selon lequel un utilisateur fournit des informations d’identification qui sont ensuite comparées à celles stockées dans un système d’exploitation, une base de données, une application ou une ressource. Si elles correspondent, les utilisateurs sont authentifiés et peuvent alors effectuer les actions pour lesquelles ils disposent d’autorisations pendant un processus d’autorisation. L’autorisation désigne le processus qui détermine ce qu’un utilisateur est autorisé à faire. 

Vous pouvez aussi vous représenter l’authentification comme un moyen d’entrer dans un espace, tel qu’un serveur, une base de données, une application ou une ressource, tandis que l’autorisation consiste à définir quelles actions l’utilisateur peut effectuer sur quels objets à l’intérieur de cet espace (serveur, base de données ou application).

## <a name="common-vulnerabilities-in-software"></a>Failles de sécurité courantes dans les logiciels

ASP.NET Core et Entity Framework contiennent des fonctionnalités qui vous aident à sécuriser vos applications et à empêcher les violations de sécurité. La liste de liens ci-après vous permet d’accéder à une documentation décrivant en détail des techniques destinées à éviter les failles de sécurité les plus courantes dans les applications web :

* [Attaques par exécution de scripts de site à site](https://docs.microsoft.com/aspnet/core/security/cross-site-scripting)
* [Attaques par injection de code SQL](https://docs.microsoft.com/ef/core/querying/raw-sql)
* [Falsification de requête intersites (CSRF, Cross Site Request Forgery)](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery)
* [Attaques par redirection ouverte](https://docs.microsoft.com/aspnet/core/security/preventing-open-redirects)

Il existe d’autres failles de sécurité que vous devez connaître. Pour plus d’informations, consultez la section de ce document relative à la *Documentation sur la sécurité ASP.NET*. 

## <a name="aspnet-security-documentation"></a>Documentation sur la sécurité ASP.NET

*   [Authentification](authentication/index.md)
    *   [Présentation d’Identity](authentication/identity.md)
    *   [Activer l’authentification à l’aide de Facebook, Google et d’autres fournisseurs externes](authentication/social/index.md)
    * [Configurer l’authentification Windows](authentication/windowsauth.md)
    *   [Confirmation de compte et récupération de mot de passe](authentication/accconfirm.md)
    *   [Authentification à deux facteurs avec SMS](authentication/2fa.md) 
    *   [Utiliser l’authentification par cookie sans Identity](authentication/cookie.md)
    *   [Azure Active Directory](authentication/azure-active-directory/index.md)
        *   [Intégrer Azure AD dans une application web ASP.NET Core](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-openidconnect-aspnetcore/)
        *   [Appeler une API web ASP.NET Core à partir d’une application WPF via Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-native-aspnetcore/)
        *   [Appeler une API web dans une application web ASP.NET Core via Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore/)
        *   [Une application web ASP.NET Core Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapp/)
    *   [Sécuriser les applications ASP.NET Core avec IdentityServer4](https://identityserver4.readthedocs.io)
*   [Autorisation](authorization/index.md)
    *   [Introduction](authorization/introduction.md)
    *   [Créer une application avec des données utilisateur protégées par une autorisation](xref:security/authorization/secure-data)
    *   [Autorisation simple](authorization/simple.md)
    *   [Autorisation basée sur des rôles](authorization/roles.md)
    *   [Autorisation basée sur des revendications](authorization/claims.md)
    *   [Autorisation basée sur une stratégie](authorization/policies.md)
    *   [Injection de dépendances dans les gestionnaires d’exigences](authorization/dependencyinjection.md)
    *   [Autorisation basée sur les ressources](authorization/resourcebased.md)
    *   [Autorisation basée sur les vues](authorization/views.md)
    *   [Limiter une identité par schéma](authorization/limitingidentitybyscheme.md)
*   [Protection des données](data-protection/index.md)
    *   [Présentation de la protection des données](data-protection/introduction.md)
    *   [Bien démarrer avec les API de protection des données](data-protection/using-data-protection.md)
    *   [API de contrôle serveur consommateur](data-protection/consumer-apis/index.md)
        *   [Vue d’ensemble des API de contrôle serveur consommateur](data-protection/consumer-apis/overview.md)
        *   [Chaînes d’objectifs](data-protection/consumer-apis/purpose-strings.md)
        *   [Hiérarchie d’objectifs et architecture mutualisée](data-protection/consumer-apis/purpose-strings-multitenancy.md)
        *   [Hachage de mot de passe](data-protection/consumer-apis/password-hashing.md)
        *   [Limiter la durée de vie des charges utiles protégées](data-protection/consumer-apis/limited-lifetime-payloads.md)
        *   [Retirer la protection des charges utiles dont les clés ont été révoquées](data-protection/consumer-apis/dangerous-unprotect.md)
    *   [Configuration](data-protection/configuration/index.md)
        *   [Configurer la protection des données](data-protection/configuration/overview.md)
        *   [Paramètres par défaut](data-protection/configuration/default-settings.md)
        *   [Stratégie à l’échelle de l’ordinateur](data-protection/configuration/machine-wide-policy.md)
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
        *   [Partager des cookies entre applications](data-protection/compatibility/cookie-sharing.md)
        *   [Remplacer <machineKey> dans ASP.NET](data-protection/compatibility/replacing-machinekey.md)
*   [Créer une application avec des données utilisateur protégées par une autorisation](xref:security/authorization/secure-data)
*   [Stockage sécurisé des secrets de l’application durant le développement](app-secrets.md)
*   [Fournisseur de configuration Azure Key Vault](key-vault-configuration.md)
*   [Appliquer SSL](enforcing-ssl.md)
*   [Protection contre la falsification de requête](anti-request-forgery.md)
*   [Bloquer les attaques par redirection ouverte](preventing-open-redirects.md)
*   [Bloquer les scripts intersites](cross-site-scripting.md)
*   [Activer les requêtes d’origines différentes](cors.md)

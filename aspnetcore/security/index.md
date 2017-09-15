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
ms.openlocfilehash: e8045b8804d1e7915cd81d697d43a173e33a7119
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="security"></a><span data-ttu-id="a724a-103">Sécurité</span><span class="sxs-lookup"><span data-stu-id="a724a-103">Security</span></span>

*   [<span data-ttu-id="a724a-104">Authentification</span><span class="sxs-lookup"><span data-stu-id="a724a-104">Authentication</span></span>](authentication/index.md)
    *   [<span data-ttu-id="a724a-105">Présentation d’Identity</span><span class="sxs-lookup"><span data-stu-id="a724a-105">Introduction to Identity</span></span>](authentication/identity.md)
    *   [<span data-ttu-id="a724a-106">Activation de l’authentification via Facebook, Google et d’autres fournisseurs externes</span><span class="sxs-lookup"><span data-stu-id="a724a-106">Enabling authentication using Facebook, Google and other external providers</span></span>](authentication/social/index.md)
    * [<span data-ttu-id="a724a-107">Configurer l’authentification Windows</span><span class="sxs-lookup"><span data-stu-id="a724a-107">Configure Windows Authentication</span></span>](authentication/windowsauth.md)
    *   [<span data-ttu-id="a724a-108">Confirmation de compte et récupération de mot de passe</span><span class="sxs-lookup"><span data-stu-id="a724a-108">Account Confirmation and Password Recovery</span></span>](authentication/accconfirm.md)
    *   [<span data-ttu-id="a724a-109">Authentification à 2 facteurs avec SMS</span><span class="sxs-lookup"><span data-stu-id="a724a-109">Two-factor authentication with SMS</span></span>](authentication/2fa.md) 
    *   [<span data-ttu-id="a724a-110">Utilisation de l’authentification par cookie sans ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="a724a-110">Using Cookie Authentication without ASP.NET Core Identity</span></span>](authentication/cookie.md)
    *   [<span data-ttu-id="a724a-111">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="a724a-111">Azure Active Directory</span></span>](authentication/azure-active-directory/index.md)
        *   [<span data-ttu-id="a724a-112">Intégration d’Azure AD dans une application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a724a-112">Integrating Azure AD Into an ASP.NET Core Web App</span></span>](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-openidconnect-aspnetcore/)
        *   [<span data-ttu-id="a724a-113">Appel d’une API web ASP.NET Core à partir d’une application WPF via Azure AD</span><span class="sxs-lookup"><span data-stu-id="a724a-113">Calling a ASP.NET Core Web API From a WPF Application Using Azure AD</span></span>](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-native-aspnetcore/)
        *   [<span data-ttu-id="a724a-114">Appel d’une API web dans une application web ASP.NET Core via Azure AD</span><span class="sxs-lookup"><span data-stu-id="a724a-114">Calling a Web API in an ASP.NET Core Web Application Using Azure AD</span></span>](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore/)
        *   [<span data-ttu-id="a724a-115">Une application web ASP.NET Core Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="a724a-115">An ASP.NET Core web app with Azure AD B2C</span></span>](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapp/)
    *   [<span data-ttu-id="a724a-116">Sécurisation des applications ASP.NET Core avec IdentityServer4</span><span class="sxs-lookup"><span data-stu-id="a724a-116">Securing ASP.NET Core apps with IdentityServer4</span></span>](https://identityserver4.readthedocs.io)
*   [<span data-ttu-id="a724a-117">Autorisation</span><span class="sxs-lookup"><span data-stu-id="a724a-117">Authorization</span></span>](authorization/index.md)
    *   [<span data-ttu-id="a724a-118">Introduction</span><span class="sxs-lookup"><span data-stu-id="a724a-118">Introduction</span></span>](authorization/introduction.md)
    *   [<span data-ttu-id="a724a-119">Créer une application avec des données utilisateur protégées par une autorisation</span><span class="sxs-lookup"><span data-stu-id="a724a-119">Create an app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
    *   [<span data-ttu-id="a724a-120">Autorisation simple</span><span class="sxs-lookup"><span data-stu-id="a724a-120">Simple Authorization</span></span>](authorization/simple.md)
    *   [<span data-ttu-id="a724a-121">Autorisation basée sur les rôles</span><span class="sxs-lookup"><span data-stu-id="a724a-121">Role based Authorization</span></span>](authorization/roles.md)
    *   [<span data-ttu-id="a724a-122">Autorisation basée sur des revendications</span><span class="sxs-lookup"><span data-stu-id="a724a-122">Claims-Based Authorization</span></span>](authorization/claims.md)
    *   [<span data-ttu-id="a724a-123">Autorisation basée sur une stratégie personnalisée</span><span class="sxs-lookup"><span data-stu-id="a724a-123">Custom Policy-Based Authorization</span></span>](authorization/policies.md)
    *   [<span data-ttu-id="a724a-124">Injection de dépendances dans les gestionnaires d’exigences</span><span class="sxs-lookup"><span data-stu-id="a724a-124">Dependency Injection in requirement handlers</span></span>](authorization/dependencyinjection.md)
    *   [<span data-ttu-id="a724a-125">Autorisation basée sur les ressources</span><span class="sxs-lookup"><span data-stu-id="a724a-125">Resource Based Authorization</span></span>](authorization/resourcebased.md)
    *   [<span data-ttu-id="a724a-126">Autorisation basée sur les vues</span><span class="sxs-lookup"><span data-stu-id="a724a-126">View Based Authorization</span></span>](authorization/views.md)
    *   [<span data-ttu-id="a724a-127">Limitation de l’identité selon le schéma</span><span class="sxs-lookup"><span data-stu-id="a724a-127">Limiting identity by scheme</span></span>](authorization/limitingidentitybyscheme.md)
*   [<span data-ttu-id="a724a-128">Protection des données</span><span class="sxs-lookup"><span data-stu-id="a724a-128">Data Protection</span></span>](data-protection/index.md)
    *   [<span data-ttu-id="a724a-129">Présentation de la protection des données</span><span class="sxs-lookup"><span data-stu-id="a724a-129">Introduction to Data Protection</span></span>](data-protection/introduction.md)
    *   [<span data-ttu-id="a724a-130">Bien démarrer avec les API de protection des données</span><span class="sxs-lookup"><span data-stu-id="a724a-130">Getting Started with the Data Protection APIs</span></span>](data-protection/using-data-protection.md)
    *   [<span data-ttu-id="a724a-131">API de contrôle serveur consommateur</span><span class="sxs-lookup"><span data-stu-id="a724a-131">Consumer APIs</span></span>](data-protection/consumer-apis/index.md)
        *   [<span data-ttu-id="a724a-132">Vue d’ensemble des API de contrôle serveur consommateur</span><span class="sxs-lookup"><span data-stu-id="a724a-132">Consumer APIs Overview</span></span>](data-protection/consumer-apis/overview.md)
        *   [<span data-ttu-id="a724a-133">Chaînes d’objectifs</span><span class="sxs-lookup"><span data-stu-id="a724a-133">Purpose Strings</span></span>](data-protection/consumer-apis/purpose-strings.md)
        *   [<span data-ttu-id="a724a-134">Hiérarchie d’objectifs et architecture mutualisée</span><span class="sxs-lookup"><span data-stu-id="a724a-134">Purpose hierarchy and multi-tenancy</span></span>](data-protection/consumer-apis/purpose-strings-multitenancy.md)
        *   [<span data-ttu-id="a724a-135">Hachage de mot de passe</span><span class="sxs-lookup"><span data-stu-id="a724a-135">Password Hashing</span></span>](data-protection/consumer-apis/password-hashing.md)
        *   [<span data-ttu-id="a724a-136">Limitation de la durée de vie des charges utiles protégées</span><span class="sxs-lookup"><span data-stu-id="a724a-136">Limiting the lifetime of protected payloads</span></span>](data-protection/consumer-apis/limited-lifetime-payloads.md)
        *   [<span data-ttu-id="a724a-137">Retrait de la protection des charges utiles dont les clés ont été révoquées</span><span class="sxs-lookup"><span data-stu-id="a724a-137">Unprotecting payloads whose keys have been revoked</span></span>](data-protection/consumer-apis/dangerous-unprotect.md)
    *   [<span data-ttu-id="a724a-138">Configuration</span><span class="sxs-lookup"><span data-stu-id="a724a-138">Configuration</span></span>](data-protection/configuration/index.md)
        *   [<span data-ttu-id="a724a-139">Configuration de la protection des données</span><span class="sxs-lookup"><span data-stu-id="a724a-139">Configuring Data Protection</span></span>](data-protection/configuration/overview.md)
        *   [<span data-ttu-id="a724a-140">Paramètres par défaut</span><span class="sxs-lookup"><span data-stu-id="a724a-140">Default Settings</span></span>](data-protection/configuration/default-settings.md)
        *   [<span data-ttu-id="a724a-141">Stratégie à l’échelle de la machine</span><span class="sxs-lookup"><span data-stu-id="a724a-141">Machine Wide Policy</span></span>](data-protection/configuration/machine-wide-policy.md)
        *   [<span data-ttu-id="a724a-142">Scénarios non compatibles avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="a724a-142">Non DI Aware Scenarios</span></span>](data-protection/configuration/non-di-scenarios.md)
    *   [<span data-ttu-id="a724a-143">API d’extensibilité</span><span class="sxs-lookup"><span data-stu-id="a724a-143">Extensibility APIs</span></span>](data-protection/extensibility/index.md)
        *   [<span data-ttu-id="a724a-144">Extensibilité du chiffrement de base</span><span class="sxs-lookup"><span data-stu-id="a724a-144">Core cryptography extensibility</span></span>](data-protection/extensibility/core-crypto.md)
        *   [<span data-ttu-id="a724a-145">Extensibilité de la gestion de clés</span><span class="sxs-lookup"><span data-stu-id="a724a-145">Key management extensibility</span></span>](data-protection/extensibility/key-management.md)
        *   [<span data-ttu-id="a724a-146">API diverses</span><span class="sxs-lookup"><span data-stu-id="a724a-146">Miscellaneous APIs</span></span>](data-protection/extensibility/misc-apis.md)
    *   [<span data-ttu-id="a724a-147">Implémentation</span><span class="sxs-lookup"><span data-stu-id="a724a-147">Implementation</span></span>](data-protection/implementation/index.md)
        *   [<span data-ttu-id="a724a-148">Détails du chiffrement authentifié</span><span class="sxs-lookup"><span data-stu-id="a724a-148">Authenticated encryption details.</span></span>](data-protection/implementation/authenticated-encryption-details.md)
        *   [<span data-ttu-id="a724a-149">Dérivation de sous-clé et chiffrement authentifié</span><span class="sxs-lookup"><span data-stu-id="a724a-149">Subkey Derivation and Authenticated Encryption</span></span>](data-protection/implementation/subkeyderivation.md)
        *   [<span data-ttu-id="a724a-150">En-têtes de contexte</span><span class="sxs-lookup"><span data-stu-id="a724a-150">Context headers</span></span>](data-protection/implementation/context-headers.md)
        *   [<span data-ttu-id="a724a-151">Gestion des clés</span><span class="sxs-lookup"><span data-stu-id="a724a-151">Key Management</span></span>](data-protection/implementation/key-management.md)
        *   [<span data-ttu-id="a724a-152">Fournisseurs de stockage de clés</span><span class="sxs-lookup"><span data-stu-id="a724a-152">Key Storage Providers</span></span>](data-protection/implementation/key-storage-providers.md)
        *   [<span data-ttu-id="a724a-153">Chiffrement à clé au repos</span><span class="sxs-lookup"><span data-stu-id="a724a-153">Key Encryption At Rest</span></span>](data-protection/implementation/key-encryption-at-rest.md)
        *   [<span data-ttu-id="a724a-154">Immuabilité des clés et modification des paramètres</span><span class="sxs-lookup"><span data-stu-id="a724a-154">Key Immutability and Changing Settings</span></span>](data-protection/implementation/key-immutability.md)
        *   [<span data-ttu-id="a724a-155">Format de stockage des clés</span><span class="sxs-lookup"><span data-stu-id="a724a-155">Key Storage Format</span></span>](data-protection/implementation/key-storage-format.md)
        *   [<span data-ttu-id="a724a-156">Fournisseurs de protection des données éphémères</span><span class="sxs-lookup"><span data-stu-id="a724a-156">Ephemeral data protection providers</span></span>](data-protection/implementation/key-storage-ephemeral.md)
    *   [<span data-ttu-id="a724a-157">Compatibilité</span><span class="sxs-lookup"><span data-stu-id="a724a-157">Compatibility</span></span>](data-protection/compatibility/index.md)
        *   [<span data-ttu-id="a724a-158">Partage des cookies entre les applications</span><span class="sxs-lookup"><span data-stu-id="a724a-158">Sharing cookies between applications</span></span>](data-protection/compatibility/cookie-sharing.md)
        *   [<span data-ttu-id="a724a-159">Remplacement de <machineKey> dans ASP.NET</span><span class="sxs-lookup"><span data-stu-id="a724a-159">Replacing <machineKey> in ASP.NET</span></span>](data-protection/compatibility/replacing-machinekey.md)
*   [<span data-ttu-id="a724a-160">Créer une application avec des données utilisateur protégées par une autorisation</span><span class="sxs-lookup"><span data-stu-id="a724a-160">Create an app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
*   [<span data-ttu-id="a724a-161">Stockage sécurisé des secrets de l’application durant le développement</span><span class="sxs-lookup"><span data-stu-id="a724a-161">Safe storage of app secrets during development</span></span>](app-secrets.md)
*   [<span data-ttu-id="a724a-162">Fournisseur de configuration Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a724a-162">Azure Key Vault configuration provider</span></span>](key-vault-configuration.md)
*   [<span data-ttu-id="a724a-163">Mise en œuvre de SSL</span><span class="sxs-lookup"><span data-stu-id="a724a-163">Enforcing SSL</span></span>](enforcing-ssl.md)
*   [<span data-ttu-id="a724a-164">Configuration du protocole HTTPS pour le développement</span><span class="sxs-lookup"><span data-stu-id="a724a-164">Setting up HTTPS for development</span></span>](https.md)
*   [<span data-ttu-id="a724a-165">Protection contre la falsification de requête</span><span class="sxs-lookup"><span data-stu-id="a724a-165">Anti-Request Forgery</span></span>](anti-request-forgery.md)
*   [<span data-ttu-id="a724a-166">Blocage des attaques par redirection ouverte</span><span class="sxs-lookup"><span data-stu-id="a724a-166">Preventing Open Redirect Attacks</span></span>](preventing-open-redirects.md)
*   [<span data-ttu-id="a724a-167">Blocage des scripts intersites</span><span class="sxs-lookup"><span data-stu-id="a724a-167">Preventing Cross-Site Scripting</span></span>](cross-site-scripting.md)
*   [<span data-ttu-id="a724a-168">Activation des requêtes d’origines différentes</span><span class="sxs-lookup"><span data-stu-id="a724a-168">Enabling Cross-Origin Requests (CORS)</span></span>](cors.md)

---
title: "Limitation d’identité par schéma"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: d3d6ca1b-b4b5-4bf7-898e-dcd90ec1bf8c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 2483c441da317a5c29b611b3a4910eae3c01fd7a
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="limiting-identity-by-scheme"></a><span data-ttu-id="fd99c-103">Limitation d’identité par schéma</span><span class="sxs-lookup"><span data-stu-id="fd99c-103">Limiting identity by scheme</span></span>

<a name=security-authorization-limiting-by-scheme></a>

<span data-ttu-id="fd99c-104">Dans certains scénarios, tels que des Applications à Page unique il est possible de se retrouver avec plusieurs méthodes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="fd99c-104">In some scenarios, such as Single Page Applications it is possible to end up with multiple authentication methods.</span></span> <span data-ttu-id="fd99c-105">Par exemple, votre application peut utiliser l’authentification par cookie pour vous connecter et l’authentification du support pour les demandes de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fd99c-105">For example, your application may use cookie-based authentication to log in and bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="fd99c-106">Dans certains cas, vous pouvez avoir plusieurs instances d’un intergiciel (middleware) d’authentification.</span><span class="sxs-lookup"><span data-stu-id="fd99c-106">In some cases you may have multiple instances of an authentication middleware.</span></span> <span data-ttu-id="fd99c-107">Par exemple, deux cookies middlewares où un contient une identité de base et est créé lorsqu’une authentification multifacteur a déclenché, car l’utilisateur a demandé une opération qui requiert une sécurité supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="fd99c-107">For example, two cookie middlewares where one contains a basic identity and one is created when a multi-factor authentication has triggered because the user requested an operation that requires extra security.</span></span>

<span data-ttu-id="fd99c-108">Schémas d’authentification sont nommés lors de l’intergiciel (middleware) d’authentification est configuré lors de l’authentification, par exemple</span><span class="sxs-lookup"><span data-stu-id="fd99c-108">Authentication schemes are named when authentication middleware is configured during authentication, for example</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AuthenticationScheme = "Cookie",
    LoginPath = new PathString("/Account/Unauthorized/"),
    AccessDeniedPath = new PathString("/Account/Forbidden/"),
    AutomaticAuthenticate = false
});

app.UseBearerAuthentication(options =>
{
    options.AuthenticationScheme = "Bearer";
    options.AutomaticAuthenticate = false;
});
```

<span data-ttu-id="fd99c-109">Dans cette configuration deux middlewares d’authentification ont été ajoutés, un pour les cookies et l’autre pour le support.</span><span class="sxs-lookup"><span data-stu-id="fd99c-109">In this configuration two authentication middlewares have been added, one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="fd99c-110">Lorsque vous ajoutez plusieurs intergiciel (middleware) d’authentification, vous devez vous assurer qu’aucun intergiciel (middleware) n’est configuré pour s’exécuter automatiquement.</span><span class="sxs-lookup"><span data-stu-id="fd99c-110">When adding multiple authentication middleware you should ensure that no middleware is configured to run automatically.</span></span> <span data-ttu-id="fd99c-111">Pour cela, définissez la `AutomaticAuthenticate` options de propriété sur false.</span><span class="sxs-lookup"><span data-stu-id="fd99c-111">You do this by setting the `AutomaticAuthenticate` options property to false.</span></span> <span data-ttu-id="fd99c-112">Si vous ne pouvez pas effectuer ce filtrage par schéma ne fonctionnera pas.</span><span class="sxs-lookup"><span data-stu-id="fd99c-112">If you fail to do this filtering by scheme will not work.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="fd99c-113">Sélection du schéma avec l’attribut Authorize</span><span class="sxs-lookup"><span data-stu-id="fd99c-113">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="fd99c-114">Aucune authentification intergiciel (middleware) est configuré pour exécuter automatiquement et de créer une identité, que vous devez, dans la phase d’autorisation choisir intergiciel (middleware) sera utilisé.</span><span class="sxs-lookup"><span data-stu-id="fd99c-114">As no authentication middleware is configured to automatically run and create an identity you must, at the point of authorization choose which middleware will be used.</span></span> <span data-ttu-id="fd99c-115">Pour sélectionner l’intergiciel (middleware) que vous souhaitez autoriser le plus simple consiste à utiliser le `ActiveAuthenticationSchemes` propriété.</span><span class="sxs-lookup"><span data-stu-id="fd99c-115">The simplest way to select the middleware you wish to authorize with is to use the `ActiveAuthenticationSchemes` property.</span></span> <span data-ttu-id="fd99c-116">Cette propriété accepte une liste séparée par des virgules des schémas d’authentification à utiliser.</span><span class="sxs-lookup"><span data-stu-id="fd99c-116">This property accepts a comma delimited list of Authentication Schemes to use.</span></span> <span data-ttu-id="fd99c-117">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="fd99c-117">For example;</span></span>

```csharp
[Authorize(ActiveAuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

<span data-ttu-id="fd99c-118">Dans l’exemple ci-dessus le cookie et le support middlewares exécute et ont la possibilité de créer et ajouter une identité pour l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="fd99c-118">In the example above both the cookie and bearer middlewares will run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="fd99c-119">En spécifiant un schéma unique uniquement l’intergiciel spécifié s’exécute ;</span><span class="sxs-lookup"><span data-stu-id="fd99c-119">By specifying a single scheme only the specified middleware will run;</span></span>

```csharp
[Authorize(ActiveAuthenticationSchemes = "Bearer")]
```

<span data-ttu-id="fd99c-120">Dans ce cas s’exécute uniquement l’intergiciel (middleware) avec le schéma de support, et toutes les identités basé sur un cookie sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="fd99c-120">In this case only the middleware with the Bearer scheme would run, and any cookie based identities would be ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="fd99c-121">Sélection du schéma avec des stratégies</span><span class="sxs-lookup"><span data-stu-id="fd99c-121">Selecting the scheme with policies</span></span>

<span data-ttu-id="fd99c-122">Si vous souhaitez spécifier les schémas souhaités dans [stratégie](policies.md#security-authorization-policies-based) que vous pouvez définir le `AuthenticationSchemes` collection lors de l’ajout de votre stratégie.</span><span class="sxs-lookup"><span data-stu-id="fd99c-122">If you prefer to specify the desired schemes in [policy](policies.md#security-authorization-policies-based) you can set the `AuthenticationSchemes` collection when adding your policy.</span></span>

```csharp
options.AddPolicy("Over18", policy =>
{
    policy.AuthenticationSchemes.Add("Bearer");
    policy.RequireAuthenticatedUser();
    policy.Requirements.Add(new Over18Requirement());
});
```

<span data-ttu-id="fd99c-123">Dans cet exemple le Over18 stratégie n’exécutée par rapport à l’identité créée par le `Bearer` intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="fd99c-123">In this example the Over18 policy will only run against the identity created by the `Bearer` middleware.</span></span>

---
title: "D’autorisation personnalisée basée sur des stratégies"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: e422a1b2-dc4a-4bcc-b8d9-7ee62009b6a3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/policies
ms.openlocfilehash: 24585ed5b4c21a357fc0eed4de6ccedf9fa50d3e
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="custom-policy-based-authorization"></a><span data-ttu-id="e1870-103">D’autorisation personnalisée basée sur des stratégies</span><span class="sxs-lookup"><span data-stu-id="e1870-103">Custom Policy-Based Authorization</span></span>

<a name="security-authorization-policies-based"></a>

<span data-ttu-id="e1870-104">Dans les coulisses du [l’autorisation de rôle](roles.md) et [d’autorisation des revendications](claims.md) rendre l’utilisation d’une spécification, un gestionnaire pour la demande et une stratégie préconfigurée.</span><span class="sxs-lookup"><span data-stu-id="e1870-104">Underneath the covers the [role authorization](roles.md) and [claims authorization](claims.md) make use of a requirement, a handler for the requirement and a pre-configured policy.</span></span> <span data-ttu-id="e1870-105">Ces blocs de construction vous permet d’exprimer les évaluations d’autorisation dans le code, ce qui permet une plus riche, réutilisables et une structure d’autorisation faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="e1870-105">These building blocks allow you to express authorization evaluations in code, allowing for a richer, reusable, and easily testable authorization structure.</span></span>

<span data-ttu-id="e1870-106">Une stratégie d’autorisation est composée d’une ou plusieurs conditions et inscrit au démarrage de l’application dans le cadre de la configuration du service d’autorisation dans `ConfigureServices` dans les *Startup.cs* fichier.</span><span class="sxs-lookup"><span data-stu-id="e1870-106">An authorization policy is made up of one or more requirements and registered at application startup as part of the Authorization service configuration, in `ConfigureServices` in the *Startup.cs* file.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Over21",
                          policy => policy.Requirements.Add(new MinimumAgeRequirement(21)));
    });
}
```

<span data-ttu-id="e1870-107">Vous trouverez ici qu'une stratégie de « Over21 » est créée avec une seule exigence, que d’un minimum d’ancienneté, qui est transmis en tant que paramètre à la spécification.</span><span class="sxs-lookup"><span data-stu-id="e1870-107">Here you can see an "Over21" policy is created with a single requirement, that of a minimum age, which is passed as a parameter to the requirement.</span></span>

<span data-ttu-id="e1870-108">Les stratégies sont appliquées à l’aide de la `Authorize` attribut en spécifiant le nom de la stratégie, par exemple ;</span><span class="sxs-lookup"><span data-stu-id="e1870-108">Policies are applied using the `Authorize` attribute by specifying the policy name, for example;</span></span>

```csharp
[Authorize(Policy="Over21")]
public class AlcoholPurchaseRequirementsController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

## <a name="requirements"></a><span data-ttu-id="e1870-109">Spécifications</span><span class="sxs-lookup"><span data-stu-id="e1870-109">Requirements</span></span>

<span data-ttu-id="e1870-110">Une demande d’autorisation est une collection de paramètres de données une stratégie peut utiliser pour évaluer le principal utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="e1870-110">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="e1870-111">Dans notre stratégie d’âge minimal l’exigence est un paramètre unique, l’âge minimal.</span><span class="sxs-lookup"><span data-stu-id="e1870-111">In our Minimum Age policy the requirement we have is a single parameter, the minimum age.</span></span> <span data-ttu-id="e1870-112">Une exigence doit implémenter `IAuthorizationRequirement`.</span><span class="sxs-lookup"><span data-stu-id="e1870-112">A requirement must implement `IAuthorizationRequirement`.</span></span> <span data-ttu-id="e1870-113">Il s’agit d’une interface de marqueur vide.</span><span class="sxs-lookup"><span data-stu-id="e1870-113">This is an empty, marker interface.</span></span> <span data-ttu-id="e1870-114">Une spécification de l’ancienneté minimale paramétrable peut être implémentée comme suit :</span><span class="sxs-lookup"><span data-stu-id="e1870-114">A parameterized minimum age requirement might be implemented as follows;</span></span>

```csharp
public class MinimumAgeRequirement : IAuthorizationRequirement
{
    public int MinimumAge { get; private set; }
    
    public MinimumAgeRequirement(int minimumAge)
    {
        MinimumAge = minimumAge;
    }
}
```

<span data-ttu-id="e1870-115">Une exigence n’a pas besoin d’avoir des données ou des propriétés.</span><span class="sxs-lookup"><span data-stu-id="e1870-115">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="e1870-116">Gestionnaires d’autorisation</span><span class="sxs-lookup"><span data-stu-id="e1870-116">Authorization Handlers</span></span>

<span data-ttu-id="e1870-117">Un gestionnaire d’autorisation est responsable de l’évaluation de toutes les propriétés d’une exigence.</span><span class="sxs-lookup"><span data-stu-id="e1870-117">An authorization handler is responsible for the evaluation of any properties of a requirement.</span></span> <span data-ttu-id="e1870-118">Le Gestionnaire d’autorisation doit les évaluer par rapport à un `AuthorizationHandlerContext` pour décider si l’autorisation est autorisée.</span><span class="sxs-lookup"><span data-stu-id="e1870-118">The  authorization handler must evaluate them against a provided `AuthorizationHandlerContext` to decide if authorization is allowed.</span></span> <span data-ttu-id="e1870-119">Une condition peut avoir [plusieurs gestionnaires](policies.md#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="e1870-119">A requirement can have [multiple handlers](policies.md#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="e1870-120">Gestionnaires doivent hériter `AuthorizationHandler<T>` où T est l’exigence il gère.</span><span class="sxs-lookup"><span data-stu-id="e1870-120">Handlers must inherit `AuthorizationHandler<T>` where T is the requirement it handles.</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="e1870-121">Le Gestionnaire de durée de vie minimale peut ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="e1870-121">The minimum age handler might look like this:</span></span>

```csharp
public class MinimumAgeHandler : AuthorizationHandler<MinimumAgeRequirement>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MinimumAgeRequirement requirement)
    {
        if (!context.User.HasClaim(c => c.Type == ClaimTypes.DateOfBirth &&
                                   c.Issuer == "http://contoso.com"))
        {
            // .NET 4.x -> return Task.FromResult(0);
            return Task.CompletedTask;
        }

        var dateOfBirth = Convert.ToDateTime(context.User.FindFirst(
            c => c.Type == ClaimTypes.DateOfBirth && c.Issuer == "http://contoso.com").Value);

        int calculatedAge = DateTime.Today.Year - dateOfBirth.Year;
        if (dateOfBirth > DateTime.Today.AddYears(-calculatedAge))
        {
            calculatedAge--;
        }

        if (calculatedAge >= requirement.MinimumAge)
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="e1870-122">Dans le code ci-dessus nous allons tout d’abord pour voir si l’utilisateur principal a une date de naissance qui a été émis par un émetteur que nous savons et l’approbation de la revendication.</span><span class="sxs-lookup"><span data-stu-id="e1870-122">In the code above we first look to see if the current user principal has a date of birth claim which has been issued by an Issuer we know and trust.</span></span> <span data-ttu-id="e1870-123">Si la revendication est manquante, nous ne pouvons pas autoriser afin que nous retourner.</span><span class="sxs-lookup"><span data-stu-id="e1870-123">If the claim is missing we can't authorize so we return.</span></span> <span data-ttu-id="e1870-124">Si nous avons une revendication, nous identifier l’ancienneté est de l’utilisateur, et si elles répondent à l’âge minimal passé par l’exigence ensuite l’autorisation a réussi.</span><span class="sxs-lookup"><span data-stu-id="e1870-124">If we have a claim, we figure out how old the user is, and if they meet the minimum age passed in by the requirement then authorization has been successful.</span></span> <span data-ttu-id="e1870-125">Une fois que l’autorisation est réussie, nous appelons `context.Succeed()` en passant dans la demande a réussi en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="e1870-125">Once authorization is successful we call `context.Succeed()` passing in the requirement that has been successful as a parameter.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

<span data-ttu-id="e1870-126">Gestionnaires doivent être enregistrés dans la collection de services lors de la configuration, par exemple ;</span><span class="sxs-lookup"><span data-stu-id="e1870-126">Handlers must be registered in the services collection during configuration, for example;</span></span>

```csharp

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Over21",
                          policy => policy.Requirements.Add(new MinimumAgeRequirement(21)));
    });

    services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();
}
```

<span data-ttu-id="e1870-127">Chaque gestionnaire est ajouté à la collection de services à l’aide de `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();` en passant dans votre classe de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="e1870-127">Each handler is added to the services collection by using `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();` passing in your handler class.</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="e1870-128">Ce qui doit retourner un gestionnaire ?</span><span class="sxs-lookup"><span data-stu-id="e1870-128">What should a handler return?</span></span>

<span data-ttu-id="e1870-129">Vous pouvez voir dans notre [exemple de gestionnaire](policies.md#security-authorization-handler-example) qui le `Handle()` méthode n’a aucune valeur de retour, comment nous indiquer réussite ou l’échec ?</span><span class="sxs-lookup"><span data-stu-id="e1870-129">You can see in our [handler example](policies.md#security-authorization-handler-example) that the `Handle()` method has no return value, so how do we indicate success or failure?</span></span>

* <span data-ttu-id="e1870-130">Un gestionnaire indique la réussite en appelant `context.Succeed(IAuthorizationRequirement requirement)`, en passant à la demande qui a été validé.</span><span class="sxs-lookup"><span data-stu-id="e1870-130">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="e1870-131">Un gestionnaire n’a pas besoin de gérer les défaillances en règle générale, comme les autres gestionnaires pour la même exigence peuvent réussir.</span><span class="sxs-lookup"><span data-stu-id="e1870-131">A handler does not need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="e1870-132">Pour garantir la défaillance même si d’autres gestionnaires d’une spécification réussissent, appelez `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="e1870-132">To guarantee failure even if other handlers for a requirement succeed, call `context.Fail`.</span></span>

<span data-ttu-id="e1870-133">Indépendamment de ce que vous appelez à l’intérieur de votre gestionnaire de tous les gestionnaires d’une spécification seront appelées lorsqu’une stratégie requiert la spécification.</span><span class="sxs-lookup"><span data-stu-id="e1870-133">Regardless of what you call inside your handler all handlers for a requirement will be called when a policy requires the requirement.</span></span> <span data-ttu-id="e1870-134">Ainsi, les conditions requises pour des effets secondaires, tel que la journalisation, ce qui aura toujours lieu même si `context.Fail()` a été appelée dans un autre gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="e1870-134">This allows requirements to have side effects, such as logging, which will always take place even if `context.Fail()` has been called in another handler.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="e1870-135">Pourquoi voudrais-je plusieurs gestionnaires pour une spécification ?</span><span class="sxs-lookup"><span data-stu-id="e1870-135">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="e1870-136">Dans le cas où vous souhaitez d’évaluation sur une **ou** vous implémentez plusieurs gestionnaires pour une seule exigence de base.</span><span class="sxs-lookup"><span data-stu-id="e1870-136">In cases where you want evaluation to be on an **OR** basis you implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="e1870-137">Par exemple, Microsoft a portes ouvre uniquement avec les cartes de clé.</span><span class="sxs-lookup"><span data-stu-id="e1870-137">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="e1870-138">Si vous laissez votre carte de clé chez le réceptionniste imprime un autocollant temporaire et ouvre la porte pour vous.</span><span class="sxs-lookup"><span data-stu-id="e1870-138">If you leave your key card at home the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="e1870-139">Dans ce scénario, vous aurait une seule exigence, *EnterBuilding*, mais plusieurs gestionnaires, chacun d'entre eux examinant une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="e1870-139">In this scenario you'd have a single requirement, *EnterBuilding*, but multiple handlers, each one examining a single requirement.</span></span>

```csharp
public class EnterBuildingRequirement : IAuthorizationRequirement
{
}

public class BadgeEntryHandler : AuthorizationHandler<EnterBuildingRequirement>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, EnterBuildingRequirement requirement)
    {
        if (context.User.HasClaim(c => c.Type == ClaimTypes.BadgeId &&
                                       c.Issuer == "http://microsoftsecurity"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }
}

public class HasTemporaryStickerHandler : AuthorizationHandler<EnterBuildingRequirement>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, EnterBuildingRequirement requirement)
    {
        if (context.User.HasClaim(c => c.Type == ClaimTypes.TemporaryBadgeId &&
                                       c.Issuer == "https://microsoftsecurity"))
        {
            // We'd also check the expiration date on the sticker.
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="e1870-140">En supposant que les deux gestionnaires sont maintenant [inscrit](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) quand une stratégie prend la valeur du `EnterBuildingRequirement` si soit Gestionnaire réussit l’évaluation de stratégie réussira.</span><span class="sxs-lookup"><span data-stu-id="e1870-140">Now, assuming both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) when a policy evaluates the `EnterBuildingRequirement` if either handler succeeds the policy evaluation will succeed.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="e1870-141">À l’aide d’une func pour répondre à une stratégie</span><span class="sxs-lookup"><span data-stu-id="e1870-141">Using a func to fulfill a policy</span></span>

<span data-ttu-id="e1870-142">Il peut arriver où il est simple pour exprimer dans le code de répondre à une stratégie.</span><span class="sxs-lookup"><span data-stu-id="e1870-142">There may be occasions where fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="e1870-143">Il est possible de simplement fournir un `Func<AuthorizationHandlerContext, bool>` lors de la configuration de votre stratégie avec le `RequireAssertion` le Générateur de stratégie.</span><span class="sxs-lookup"><span data-stu-id="e1870-143">It is possible to simply supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="e1870-144">Par exemple précédent `BadgeEntryHandler` peut être réécrit comme suit :</span><span class="sxs-lookup"><span data-stu-id="e1870-144">For example the previous `BadgeEntryHandler` could be rewritten as follows;</span></span>

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("BadgeEntry",
                          policy => policy.RequireAssertion(context =>
                                  context.User.HasClaim(c =>
                                     (c.Type == ClaimTypes.BadgeId ||
                                      c.Type == ClaimTypes.TemporaryBadgeId)
                                      && c.Issuer == "https://microsoftsecurity"));
                          }));
    }
 }
```

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="e1870-145">Accès au contexte de demande MVC dans les gestionnaires</span><span class="sxs-lookup"><span data-stu-id="e1870-145">Accessing MVC Request Context In Handlers</span></span>

<span data-ttu-id="e1870-146">Le `Handle` méthode, vous devez implémenter dans un gestionnaire d’autorisation possède deux paramètres, un `AuthorizationContext` et `Requirement` vous gérez.</span><span class="sxs-lookup"><span data-stu-id="e1870-146">The `Handle` method you must implement in an authorization handler has two parameters, an `AuthorizationContext` and the `Requirement` you are handling.</span></span> <span data-ttu-id="e1870-147">Infrastructures telles que MVC ou Jabbr sont libres d’ajouter n’importe quel objet pour le `Resource` propriété sur le `AuthorizationContext` à passer des informations supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="e1870-147">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationContext` to pass through extra information.</span></span>

<span data-ttu-id="e1870-148">Par exemple MVC passe une instance de `Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext` dans la propriété de ressource qui est utilisée pour accéder à HttpContext, RouteData et tout autre MVC fournit.</span><span class="sxs-lookup"><span data-stu-id="e1870-148">For example MVC passes an instance of `Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext` in the resource property which is used to access HttpContext, RouteData and everything else MVC provides.</span></span>

<span data-ttu-id="e1870-149">L’utilisation de la `Resource` propriété est spécifiques de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="e1870-149">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="e1870-150">À l’aide des informations contenues dans le `Resource` propriété limite vos stratégies d’autorisation pour les infrastructures particulières.</span><span class="sxs-lookup"><span data-stu-id="e1870-150">Using information in the `Resource` property will limit your authorization policies to particular frameworks.</span></span> <span data-ttu-id="e1870-151">Vous devez effectuer un cast du `Resource` à l’aide de la propriété du `as` (mot clé), puis vérifiez que le cast a réussissent pour vérifier votre code ne se bloquer avec `InvalidCastExceptions` sur les autres infrastructures ;</span><span class="sxs-lookup"><span data-stu-id="e1870-151">You should cast the `Resource` property using the `as` keyword, and then check the cast has succeed to ensure your code doesn't crash with `InvalidCastExceptions` when run on other frameworks;</span></span>

```csharp
if (context.Resource is Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext mvcContext)
{
    // Examine MVC specific things like routing data.
}
```

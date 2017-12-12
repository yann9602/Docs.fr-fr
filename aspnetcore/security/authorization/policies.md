---
title: "D’autorisation personnalisée basée sur des stratégies"
author: rick-anderson
description: "Ce document explique comment créer et utiliser des gestionnaires de stratégie d’autorisation personnalisée dans une application ASP.NET Core."
keywords: "ASP.NET Core, d’autorisation, de stratégie personnalisée, de stratégie d’autorisation"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: e422a1b2-dc4a-4bcc-b8d9-7ee62009b6a3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/policies
ms.openlocfilehash: 0281d054204a11acc2cf11cf5fca23a8f70aad8e
ms.sourcegitcommit: 037d3900f739dbaa2ba14158e3d7dc81478952ad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="custom-policy-based-authorization"></a>D’autorisation personnalisée basée sur des stratégies

<a name="security-authorization-policies-based"></a>

Dans les coulisses, la [l’autorisation de rôle](roles.md) et [d’autorisation des revendications](claims.md) rendre l’utilisation d’une spécification, un gestionnaire pour la demande et une stratégie préconfigurée. Ces blocs de construction vous permet d’exprimer les évaluations d’autorisation dans le code, ce qui permet une plus riche, réutilisables et une structure d’autorisation faciles à tester.

Une stratégie d’autorisation est composée d’une ou plusieurs conditions et inscrit au démarrage de l’application dans le cadre de la configuration du service d’autorisation dans `ConfigureServices` dans les *Startup.cs* fichier.

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

Vous trouverez ici qu'une stratégie de « Over21 » est créée avec une seule exigence, que d’un minimum d’ancienneté, qui est transmis en tant que paramètre à la spécification.

Les stratégies sont appliquées à l’aide de la `Authorize` attribut en spécifiant le nom de la stratégie, par exemple ;

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

## <a name="requirements"></a>Spécifications

Une demande d’autorisation est une collection de paramètres de données une stratégie peut utiliser pour évaluer le principal utilisateur actuel. Dans notre stratégie d’âge minimal, l’exigence est un paramètre unique, l’âge minimal. Une exigence doit implémenter `IAuthorizationRequirement`. Il s’agit d’une interface de marqueur vide. Une spécification de l’ancienneté minimale paramétrable peut être implémentée comme suit :

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

Une exigence n’a pas besoin d’avoir des données ou des propriétés.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Gestionnaires d’autorisation

Un gestionnaire d’autorisation est responsable de l’évaluation de toutes les propriétés d’une exigence. Le Gestionnaire d’autorisation doit les évaluer par rapport à un `AuthorizationHandlerContext` pour décider si l’autorisation est autorisée. Une condition peut avoir [plusieurs gestionnaires](policies.md#security-authorization-policies-based-multiple-handlers). Gestionnaires doivent hériter `AuthorizationHandler<T>` où T est l’exigence il gère.

<a name="security-authorization-handler-example"></a>

Le Gestionnaire de durée de vie minimale peut ressembler à ceci :

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

Dans le code ci-dessus, nous allons tout d’abord pour voir si l’utilisateur principal a une date de naissance qui a été émis par un émetteur que nous savons et l’approbation de la revendication. Si la revendication est manquante, nous ne pouvons pas autoriser afin que nous retourner. Si nous avons une revendication, nous identifier l’ancienneté est de l’utilisateur, et si elles répondent à l’âge minimal passé par l’exigence ensuite l’autorisation a réussi. Une fois que l’autorisation est réussie, nous appelons `context.Succeed()` en passant dans la demande a réussi en tant que paramètre.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Inscription du Gestionnaire
Gestionnaires doivent être enregistrés dans la collection de services lors de la configuration, par exemple ;

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

Chaque gestionnaire est ajouté à la collection de services à l’aide de `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();` en passant dans votre classe de gestionnaire.

## <a name="what-should-a-handler-return"></a>Ce qui doit retourner un gestionnaire ?

Vous pouvez voir dans notre [exemple de gestionnaire](policies.md#security-authorization-handler-example) qui le `Handle()` méthode n’a aucune valeur de retour, comment nous indiquer réussite ou l’échec ?

* Un gestionnaire indique la réussite en appelant `context.Succeed(IAuthorizationRequirement requirement)`, en passant à la demande qui a été validé.

* Un gestionnaire n’a pas besoin de gérer les défaillances en règle générale, comme les autres gestionnaires pour la même exigence peuvent réussir.

* Pour garantir la défaillance même si d’autres gestionnaires d’une spécification réussissent, appelez `context.Fail`.

Indépendamment de ce que vous appelez à l’intérieur de votre gestionnaire, tous les gestionnaires d’une spécification seront appelées lorsqu’une stratégie requiert la spécification. Ainsi, les conditions requises pour des effets secondaires, tel que la journalisation, ce qui aura toujours lieu même si `context.Fail()` a été appelée dans un autre gestionnaire.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Pourquoi voudrais-je plusieurs gestionnaires pour une spécification ?

Dans le cas où vous souhaitez d’évaluation sur une **ou** vous implémentez plusieurs gestionnaires pour une seule exigence de base. Par exemple, Microsoft a portes ouvre uniquement avec les cartes de clé. Si vous laissez votre carte de clé chez le réceptionniste imprime un autocollant temporaire et ouvre la porte pour vous. Dans ce scénario, vous aurait une seule exigence, *EnterBuilding*, mais plusieurs gestionnaires, chacun d'entre eux examinant une seule exigence.

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

En supposant que les deux gestionnaires sont maintenant [inscrit](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) quand une stratégie prend la valeur du `EnterBuildingRequirement` si soit Gestionnaire réussit l’évaluation de stratégie réussira.

## <a name="using-a-func-to-fulfill-a-policy"></a>À l’aide d’une func pour répondre à une stratégie

Il peut arriver où il est simple pour exprimer dans le code de répondre à une stratégie. Il est possible de simplement fournir un `Func<AuthorizationHandlerContext, bool>` lors de la configuration de votre stratégie avec le `RequireAssertion` le Générateur de stratégie.

Par exemple précédent `BadgeEntryHandler` peut être réécrit comme suit :

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

## <a name="accessing-mvc-request-context-in-handlers"></a>Accès au contexte de demande MVC dans les gestionnaires

Le `Handle` méthode, vous devez implémenter dans un gestionnaire d’autorisation possède deux paramètres, un `AuthorizationContext` et `Requirement` vous gérez. Infrastructures telles que MVC ou Jabbr sont libres d’ajouter n’importe quel objet pour le `Resource` propriété sur le `AuthorizationContext` à passer des informations supplémentaires.

Par exemple, MVC passe une instance de `Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext` dans la propriété de ressource qui est utilisée pour accéder à HttpContext, RouteData et tout autre MVC fournit.

L’utilisation de la `Resource` propriété est spécifiques de l’infrastructure. À l’aide des informations contenues dans le `Resource` propriété limite vos stratégies d’autorisation pour les infrastructures particulières. Vous devez effectuer un cast du `Resource` à l’aide de la propriété du `as` (mot clé), puis vérifiez que le cast a réussissent pour vérifier votre code ne se bloquer avec `InvalidCastExceptions` sur les autres infrastructures ;

```csharp
if (context.Resource is Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext mvcContext)
{
    // Examine MVC specific things like routing data.
}
```

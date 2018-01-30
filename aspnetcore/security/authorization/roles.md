---
title: "Autorisation basée sur les rôles"
author: rick-anderson
description: "Ce document montre comment restreindre l’accès de contrôleur et d’action ASP.NET Core en passant des rôles à l’attribut Authorize."
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authorization/roles
ms.openlocfilehash: 764d1fcc384fc8370d1a536f9609333de6bd4357
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="role-based-authorization"></a>Autorisation basée sur les rôles

<a name="security-authorization-role-based"></a>

Lorsqu’une identité est créée, elle peut appartenir à un ou plusieurs rôles. Par exemple, Tracy mon peut appartenir aux rôles administrateur et utilisateur tandis que Scott peut uniquement appartenir au rôle d’utilisateur. Comment ces rôles sont créés et gérés varient selon le magasin de stockage du processus d’autorisation. Les rôles sont exposés au développeur via les [IsInRole](https://docs.microsoft.com/dotnet/api/system.security.principal.genericprincipal.isinrole) méthode sur le [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) classe.

## <a name="adding-role-checks"></a>Ajout de contrôles de rôle

Les vérifications d’autorisation basée sur les rôles sont déclaratives&mdash;le développeur les incorpore dans leur code, par rapport à un contrôleur ou une action dans un contrôleur, en spécifiant des rôles auxquels l’utilisateur actuel doit être un membre d’accéder à la ressource demandée.

Par exemple, le code suivant limite l’accès à toutes les actions sur le `AdministrationController` aux utilisateurs qui sont membres de la `Administrator` rôle :

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Vous pouvez spécifier plusieurs rôles comme une liste séparée par des virgules :

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Ce contrôleur étaient accessibles uniquement par les utilisateurs qui sont membres de la `HRManager` rôle ou le `Finance` rôle.

Si vous appliquez plusieurs attributs, un utilisateur doit être un membre de tous les rôles spécifiés ; l’exemple suivant nécessite qu’un utilisateur doit être membre des groupes le `PowerUser` et `ControlPanelUser` rôle.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Vous pouvez également limiter l’accès en appliquant des attributs d’autorisation de rôle supplémentaires au niveau de l’action :

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

Dans les membres d’extrait de code précédent de la `Administrator` rôle ou le `PowerUser` rôle peut accéder au contrôleur et le `SetTime` action, mais seuls les membres du `Administrator` rôle peut accéder le `ShutDown` action.

Vous pouvez également verrouiller un contrôleur et autoriser l’accès anonyme, non authentifié à des actions individuelles.

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Contrôles de rôle basée sur la stratégie

Conditions de rôle peuvent également être exprimées à l’aide de la syntaxe de la nouvelle stratégie, où un développeur inscrit une stratégie au démarrage dans le cadre de la configuration du service d’autorisation. Ceci se produit normalement dans `ConfigureServices()` dans votre *Startup.cs* fichier.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole", policy => policy.RequireRole("Administrator"));
    });
}
```

Les stratégies sont appliquées à l’aide de la `Policy` propriété sur le `AuthorizeAttribute` attribut :

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Si vous souhaitez spécifier plusieurs rôles autorisés dans une spécification, vous pouvez les spécifier en tant que paramètres à la `RequireRole` méthode :

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Cet exemple autorise les utilisateurs qui appartiennent à la `Administrator`, `PowerUser` ou `BackupAdministrator` rôles.

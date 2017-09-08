---
title: "Autorisation basée sur les rôles"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5e014da1-8bc0-409b-951a-88b92c661fdf
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/roles
ms.openlocfilehash: d8dfcbb16ee7977d197b019c4e5e1b30fff17755
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="role-based-authorization"></a><span data-ttu-id="9b09e-103">Autorisation basée sur les rôles</span><span class="sxs-lookup"><span data-stu-id="9b09e-103">Role based Authorization</span></span>

<a name=security-authorization-role-based></a>

<span data-ttu-id="9b09e-104">Lors de la création d’une identité, elle peut appartenir à un ou plusieurs rôles, par exemple Tracy mon peut appartenir aux rôles administrateur et utilisateur tandis que Scott peut uniquement appartenir au rôle d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9b09e-104">When an identity is created it may belong to one or more roles, for example Tracy may belong to the Administrator and User roles whilst Scott may only belong to the user role.</span></span> <span data-ttu-id="9b09e-105">Comment ces rôles sont créés et gérés varient selon le magasin de stockage du processus d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="9b09e-105">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="9b09e-106">Les rôles sont exposés au développeur via les [IsInRole](https://msdn.microsoft.com/library/system.security.claims.claimsprincipal.isinrole(v=vs.110).aspx) propriété sur le [ClaimsPrincipal](https://msdn.microsoft.com/library/system.security.claims.claimsprincipal(v=vs.110).aspx) classe.</span><span class="sxs-lookup"><span data-stu-id="9b09e-106">Roles are exposed to the developer through the [IsInRole](https://msdn.microsoft.com/library/system.security.claims.claimsprincipal.isinrole(v=vs.110).aspx) property on the [ClaimsPrincipal](https://msdn.microsoft.com/library/system.security.claims.claimsprincipal(v=vs.110).aspx) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="9b09e-107">Ajout de contrôles de rôle</span><span class="sxs-lookup"><span data-stu-id="9b09e-107">Adding role checks</span></span>

<span data-ttu-id="9b09e-108">Vérifications d’autorisation basé sur les rôles sont déclaratives - le développeur les incorpore dans leur code, par rapport à un contrôleur ou une action dans un contrôleur, en spécifiant des rôles auxquels l’utilisateur actuel doit être un membre d’accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="9b09e-108">Role based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="9b09e-109">Par exemple le code suivant limite l’accès à toutes les actions sur le `AdministrationController` aux utilisateurs qui sont membres de le `Administrator` groupe.</span><span class="sxs-lookup"><span data-stu-id="9b09e-109">For example the following code would limit access to any actions on the `AdministrationController` to users who are a member of the `Administrator` group.</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="9b09e-110">Vous pouvez spécifier plusieurs rôles comme une liste séparée par des virgules ;</span><span class="sxs-lookup"><span data-stu-id="9b09e-110">You can specify multiple roles as a comma separated list;</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="9b09e-111">Ce contrôleur étaient accessibles uniquement par les utilisateurs qui sont membres de la `HRManager` rôle ou le `Finance` rôle.</span><span class="sxs-lookup"><span data-stu-id="9b09e-111">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="9b09e-112">Si vous appliquez plusieurs attributs, un utilisateur doit être un membre de tous les rôles spécifiés ; l’exemple suivant nécessite qu’un utilisateur doit être membre des groupes le `PowerUser` et `ControlPanelUser` rôle.</span><span class="sxs-lookup"><span data-stu-id="9b09e-112">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="9b09e-113">Vous pouvez également limiter l’accès en appliquant des attributs d’autorisation de rôle supplémentaires au niveau action ;</span><span class="sxs-lookup"><span data-stu-id="9b09e-113">You can further limit access by applying additional role authorization attributes at the action level;</span></span>

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

<span data-ttu-id="9b09e-114">Dans les membres d’extrait de code précédent de la `Administrator` rôle ou le `PowerUser` rôle peut accéder au contrôleur et le `SetTime` action, mais seuls les membres du `Administrator` rôle peut accéder le `ShutDown` action.</span><span class="sxs-lookup"><span data-stu-id="9b09e-114">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="9b09e-115">Vous pouvez également verrouiller un contrôleur et autoriser l’accès anonyme, non authentifié à des actions individuelles.</span><span class="sxs-lookup"><span data-stu-id="9b09e-115">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

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

<a name=security-authorization-role-policy></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="9b09e-116">Contrôles de rôle basée sur la stratégie</span><span class="sxs-lookup"><span data-stu-id="9b09e-116">Policy based role checks</span></span>

<span data-ttu-id="9b09e-117">Conditions de rôle peuvent également être exprimées à l’aide de la syntaxe de la nouvelle stratégie, où un développeur inscrit une stratégie au démarrage dans le cadre de la configuration du service d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="9b09e-117">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="9b09e-118">Ceci se produit normalement dans `ConfigureServices()` dans votre *Startup.cs* fichier.</span><span class="sxs-lookup"><span data-stu-id="9b09e-118">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="9b09e-119">Les stratégies sont appliquées à l’aide de la `Policy` propriété sur le `AuthorizeAttribute` d’attribut ;</span><span class="sxs-lookup"><span data-stu-id="9b09e-119">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute;</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="9b09e-120">Si vous souhaitez spécifier plusieurs rôles autorisés dans une spécification, vous pouvez les spécifier en tant que paramètres à la `RequireRole` méthode ;</span><span class="sxs-lookup"><span data-stu-id="9b09e-120">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method;</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="9b09e-121">Cet exemple autorise les utilisateurs qui appartiennent à la `Administrator`, `PowerUser` ou `BackupAdministrator` rôles.</span><span class="sxs-lookup"><span data-stu-id="9b09e-121">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

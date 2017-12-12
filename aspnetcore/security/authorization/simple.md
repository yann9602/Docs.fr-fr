---
title: Autorisation simple
author: rick-anderson
description: "Ce document explique comment utiliser l’attribut Authorize pour restreindre l’accès aux actions et les contrôleurs ASP.NET Core."
keywords: "ASP.NET Core, d’autorisation, d’AuthorizeAttribute"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 391bcaad-205f-43e4-badc-fa592d6f79f3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/simple
ms.openlocfilehash: f2dad58ffa17259412077d31f512b561e79ac595
ms.sourcegitcommit: b38796ea3806bf39b89806adfa681b2a33762907
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="simple-authorization"></a>Autorisation simple

<a name="security-authorization-simple"></a>

Dans MVC est contrôlée par le biais du `AuthorizeAttribute` attribut et ses paramètres différents. Dans le bloc-notes, appliquer la `AuthorizeAttribute` d’attribut à une limite l’accès contrôleur ou d’action au contrôleur ou une action à n’importe quel utilisateur authentifié.

Par exemple, le code suivant limite l’accès à la `AccountController` à tout utilisateur authentifié.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Si vous souhaitez appliquer l’autorisation à une action plutôt que le contrôleur, appliquer la `AuthorizeAttribute` d’attribut pour l’action proprement dite :

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

Maintenant seulement les utilisateurs authentifiés peuvent accéder le `Logout` (fonction).

Vous pouvez également utiliser le `AllowAnonymousAttribute` attribut pour permettre l’accès des utilisateurs non authentifiés à chacune des actions. Exemple :

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Ainsi, seuls les utilisateurs authentifiés pour le `AccountController`, à l’exception de la `Login` action, qui est accessible par tout le monde, quelle que soit leur état authentifié ou non authentifié / anonyme.

>[!WARNING]
> `[AllowAnonymous]`ignore toutes les instructions d’autorisation. Si vous appliquez combiner `[AllowAnonymous]` et n’importe quel `[Authorize]` attribut puis les attributs Authorize seront toujours ignorés. Par exemple, si vous appliquez `[AllowAnonymous]` au niveau du contrôleur de niveau les `[Authorize]` attributs sur le même contrôleur, ou sur toute action qu’il contient seront ignorés.

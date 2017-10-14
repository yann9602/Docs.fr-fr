---
title: Autorisation simple
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 391bcaad-205f-43e4-badc-fa592d6f79f3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/simple
ms.openlocfilehash: 91f402b1cbf73e212418d197a8a7230ce22e9e1d
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="simple-authorization"></a>Autorisation simple

<a name="security-authorization-simple"></a>

Dans MVC est contrôlée par le biais du `AuthorizeAttribute` attribut et ses paramètres différents. À son application la plus simple de la `AuthorizeAttribute` d’attribut à une limite l’accès contrôleur ou d’action au contrôleur ou une action à n’importe quel utilisateur authentifié.

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

Si vous souhaitez appliquer l’autorisation à une action plutôt que le contrôleur simplement appliquer le `AuthorizeAttribute` d’attribut à l’action proprement dit.

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

Maintenant que les utilisateurs authentifiés peuvent accéder à la fonction de déconnexion.

Vous pouvez également utiliser le `AllowAnonymousAttribute` attribut pour permettre l’accès des utilisateurs non authentifiés à des actions individuelles ; par exemple

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

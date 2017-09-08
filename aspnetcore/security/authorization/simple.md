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
ms.openlocfilehash: 013ce0d9ac1e9c1b6bb541b9fa66218c3fd799bb
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="simple-authorization"></a><span data-ttu-id="cb014-103">Autorisation simple</span><span class="sxs-lookup"><span data-stu-id="cb014-103">Simple Authorization</span></span>

<a name=security-authorization-simple></a>

<span data-ttu-id="cb014-104">Dans MVC est contrôlée par le biais du `AuthorizeAttribute` attribut et ses paramètres différents.</span><span class="sxs-lookup"><span data-stu-id="cb014-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="cb014-105">À son application la plus simple de la `AuthorizeAttribute` d’attribut à une limite l’accès contrôleur ou d’action au contrôleur ou une action à n’importe quel utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="cb014-105">At its simplest applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="cb014-106">Par exemple, le code suivant limite l’accès à la `AccountController` à tout utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="cb014-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="cb014-107">Si vous souhaitez appliquer l’autorisation à une action plutôt que le contrôleur simplement appliquer le `AuthorizeAttribute` d’attribut à l’action proprement dit.</span><span class="sxs-lookup"><span data-stu-id="cb014-107">If you want to apply authorization to an action rather than the controller simply apply the `AuthorizeAttribute` attribute to the action itself;</span></span>

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

<span data-ttu-id="cb014-108">Maintenant que les utilisateurs authentifiés peuvent accéder à la fonction de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="cb014-108">Now only authenticated users can access the logout function.</span></span>

<span data-ttu-id="cb014-109">Vous pouvez également utiliser le `AllowAnonymousAttribute` attribut pour permettre l’accès des utilisateurs non authentifiés à des actions individuelles ; par exemple</span><span class="sxs-lookup"><span data-stu-id="cb014-109">You can also use the `AllowAnonymousAttribute` attribute to allow access by non-authenticated users to individual actions; for example</span></span>

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

<span data-ttu-id="cb014-110">Ainsi, seuls les utilisateurs authentifiés pour le `AccountController`, à l’exception de la `Login` action, qui est accessible par tout le monde, quelle que soit leur état authentifié ou non authentifié / anonyme.</span><span class="sxs-lookup"><span data-stu-id="cb014-110">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

>[!WARNING]
> <span data-ttu-id="cb014-111">`[AllowAnonymous]`ignore toutes les instructions d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="cb014-111">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="cb014-112">Si vous appliquez combiner `[AllowAnonymous]` et n’importe quel `[Authorize]` attribut puis les attributs Authorize seront toujours ignorés.</span><span class="sxs-lookup"><span data-stu-id="cb014-112">If you apply combine `[AllowAnonymous]` and any `[Authorize]` attribute then the Authorize attributes will always be ignored.</span></span> <span data-ttu-id="cb014-113">Par exemple, si vous appliquez `[AllowAnonymous]` au niveau du contrôleur de niveau les `[Authorize]` attributs sur le même contrôleur, ou sur toute action qu’il contient seront ignorés.</span><span class="sxs-lookup"><span data-stu-id="cb014-113">For example if you apply `[AllowAnonymous]` at the controller level any `[Authorize]` attributes on the same controller, or on any action within it will be ignored.</span></span>

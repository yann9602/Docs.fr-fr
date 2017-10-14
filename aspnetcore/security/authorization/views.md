---
title: "Autorisation basée sur la vue"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 24ce40d8-9b83-4bae-9d4c-a66350fcc8f8
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/views
ms.openlocfilehash: 58cafcfdc7946e82d1e0ea5de95e0e497b1b6bcf
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="view-based-authorization"></a><span data-ttu-id="c9336-103">Autorisation basée sur la vue</span><span class="sxs-lookup"><span data-stu-id="c9336-103">View Based Authorization</span></span>

<a name="security-authorization-views"></a>

<span data-ttu-id="c9336-104">Un développeur sera est souvent nécessaire d’afficher, masquer ou de modifier une interface utilisateur basée sur l’identité de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="c9336-104">Often a developer will want to show, hide or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="c9336-105">Vous pouvez accéder au service de l’autorisation au sein de vues MVC via [injection de dépendance](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c9336-105">You can access the authorization service within MVC views via [dependency injection](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection).</span></span> <span data-ttu-id="c9336-106">Le service d’autorisation d’injecter dans une utilisation de vue Razor le `@inject` directive, par exemple `@inject IAuthorizationService AuthorizationService` (nécessite `@using Microsoft.AspNetCore.Authorization`).</span><span class="sxs-lookup"><span data-stu-id="c9336-106">To inject the authorization service into a Razor view use the `@inject` directive, for example `@inject IAuthorizationService AuthorizationService` (requires `@using Microsoft.AspNetCore.Authorization`).</span></span> <span data-ttu-id="c9336-107">Si vous souhaitez que le service d’autorisation dans chaque vue puis placez le `@inject` directive dans le `_ViewImports.cshtml` de fichiers dans le `Views` active.</span><span class="sxs-lookup"><span data-stu-id="c9336-107">If you want the authorization service in every view then place the `@inject` directive into the `_ViewImports.cshtml` file in the `Views` directory.</span></span> <span data-ttu-id="c9336-108">Pour plus d’informations sur l’injection de dépendances dans les vues, consultez [injection de dépendances dans les vues](../../mvc/views/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="c9336-108">For more information on dependency injection into views see [Dependency injection into views](../../mvc/views/dependency-injection.md).</span></span>

<span data-ttu-id="c9336-109">Une fois que vous avez injectées le service d’autorisation vous l’utilisez en appelant le `AuthorizeAsync` méthode dans exactement la même façon que vous devez vérifier pendant [d’autorisation basée sur les ressources](resourcebased.md#security-authorization-resource-based-imperative).</span><span class="sxs-lookup"><span data-stu-id="c9336-109">Once you have injected the authorization service you use it by calling the `AuthorizeAsync` method in exactly the same way as you would check during [resource based authorization](resourcebased.md#security-authorization-resource-based-imperative).</span></span>

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
   {
       <p>This paragraph is displayed because you fulfilled PolicyName.</p>
   }
   ```

<span data-ttu-id="c9336-110">Dans certains cas, la ressource sera votre modèle d’affichage, et vous pouvez appeler `AuthorizeAsync` dans exactement la même façon que vous devez vérifier pendant [d’autorisation basée sur les ressources](resourcebased.md#security-authorization-resource-based-imperative);</span><span class="sxs-lookup"><span data-stu-id="c9336-110">In some cases the resource will be your view model, and you can call `AuthorizeAsync` in exactly the same way as you would check during [resource based authorization](resourcebased.md#security-authorization-resource-based-imperative);</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c9336-111">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c9336-111">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```cshtml
   @if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c9336-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c9336-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```cshtml
   @if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```
---

<span data-ttu-id="c9336-113">Ici, vous pouvez voir que le modèle est transmis comme l’autorisation de ressource à prendre en considération.</span><span class="sxs-lookup"><span data-stu-id="c9336-113">Here you can see the model is passed as the resource authorization should take into consideration.</span></span>

>[!WARNING]
><span data-ttu-id="c9336-114">Ne comptez pas sur l’affichage ou masquage de parties de votre interface utilisateur comme seule méthode d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="c9336-114">Do not rely on showing or hiding parts of your UI as your only authorization method.</span></span> <span data-ttu-id="c9336-115">Le masquage d’une interface utilisateur élément ne signifie pas qu'un utilisateur ne peut pas y accéder.</span><span class="sxs-lookup"><span data-stu-id="c9336-115">Hiding a UI element does not mean a user cannot access it.</span></span> <span data-ttu-id="c9336-116">Vous devez également autoriser l’utilisateur dans votre code du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c9336-116">You must also authorize the user within your controller code.</span></span>

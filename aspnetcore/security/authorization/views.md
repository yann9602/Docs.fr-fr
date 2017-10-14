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
# <a name="view-based-authorization"></a>Autorisation basée sur la vue

<a name="security-authorization-views"></a>

Un développeur sera est souvent nécessaire d’afficher, masquer ou de modifier une interface utilisateur basée sur l’identité de l’utilisateur actuel. Vous pouvez accéder au service de l’autorisation au sein de vues MVC via [injection de dépendance](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection). Le service d’autorisation d’injecter dans une utilisation de vue Razor le `@inject` directive, par exemple `@inject IAuthorizationService AuthorizationService` (nécessite `@using Microsoft.AspNetCore.Authorization`). Si vous souhaitez que le service d’autorisation dans chaque vue puis placez le `@inject` directive dans le `_ViewImports.cshtml` de fichiers dans le `Views` active. Pour plus d’informations sur l’injection de dépendances dans les vues, consultez [injection de dépendances dans les vues](../../mvc/views/dependency-injection.md).

Une fois que vous avez injectées le service d’autorisation vous l’utilisez en appelant le `AuthorizeAsync` méthode dans exactement la même façon que vous devez vérifier pendant [d’autorisation basée sur les ressources](resourcebased.md#security-authorization-resource-based-imperative).

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
   {
       <p>This paragraph is displayed because you fulfilled PolicyName.</p>
   }
   ```

Dans certains cas, la ressource sera votre modèle d’affichage, et vous pouvez appeler `AuthorizeAsync` dans exactement la même façon que vous devez vérifier pendant [d’autorisation basée sur les ressources](resourcebased.md#security-authorization-resource-based-imperative);

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
   @if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
   @if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```
---

Ici, vous pouvez voir que le modèle est transmis comme l’autorisation de ressource à prendre en considération.

>[!WARNING]
>Ne comptez pas sur l’affichage ou masquage de parties de votre interface utilisateur comme seule méthode d’autorisation. Le masquage d’une interface utilisateur élément ne signifie pas qu'un utilisateur ne peut pas y accéder. Vous devez également autoriser l’utilisateur dans votre code du contrôleur.

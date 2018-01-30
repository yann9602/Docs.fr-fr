---
title: "Autorisation basée sur le mode dans ASP.NET MVC de base"
author: rick-anderson
description: "Ce document montre comment injecter et utiliser le service d’autorisation à l’intérieur d’une vue ASP.NET Core Razor."
manager: wpickett
ms.author: riande
ms.date: 10/30/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authorization/views
ms.openlocfilehash: 22754d07882cd704309a4e1a28ad0bf6f69432ea
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="view-based-authorization"></a>Autorisation basée sur une vue

Un développeur souhaite souvent afficher, masquer ou modifier une interface utilisateur basée sur l’identité de l’utilisateur actuel. Vous pouvez accéder au service de l’autorisation au sein de vues MVC via [injection de dépendance](xref:fundamentals/dependency-injection#fundamentals-dependency-injection). Pour insérer le service d’autorisation dans une vue Razor, utilisez la `@inject` directive :

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Si vous souhaitez que le service d’autorisation dans chaque vue, placez le `@inject` directive dans le *_ViewImports.cshtml* fichier de la *vues* active. Pour plus d’informations, consultez [injection de dépendances dans les vues](xref:mvc/views/dependency-injection).

Permet d’appeler le service d’autorisation injecté `AuthorizeAsync` dans la même façon, vous devez vérifier pendant [d’autorisation basée sur les ressources](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

---

Dans certains cas, la ressource sera votre modèle d’affichage. Appeler `AuthorizeAsync` dans la même façon, vous devez vérifier pendant [d’autorisation basée sur les ressources](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

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

Dans le code précédent, le modèle est transmis en tant que ressource que l’évaluation de stratégie doit prendre en considération.

> [!WARNING]
> Ne vous fiez pas bascule de visibilité des éléments d’interface utilisateur de votre application en tant que le contrôle d’autorisation unique. Le masquage d’un élément d’interface utilisateur peuvent ne pas complètement empêche l’accès à son action de contrôleur associé. Par exemple, considérez le bouton dans l’extrait de code précédent. Un utilisateur peut appeler le `Edit` URL de la méthode d’action si il connaît la ressource relative est */Document/Edit/1*. Pour cette raison, la `Edit` méthode d’action doit effectuer son propre contrôle d’autorisation.

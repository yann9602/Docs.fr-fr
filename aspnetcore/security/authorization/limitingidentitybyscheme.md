---
title: "Limitation d’identité par schéma"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: d3d6ca1b-b4b5-4bf7-898e-dcd90ec1bf8c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 2483c441da317a5c29b611b3a4910eae3c01fd7a
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="limiting-identity-by-scheme"></a>Limitation d’identité par schéma

<a name=security-authorization-limiting-by-scheme></a>

Dans certains scénarios, tels que des Applications à Page unique il est possible de se retrouver avec plusieurs méthodes d’authentification. Par exemple, votre application peut utiliser l’authentification par cookie pour vous connecter et l’authentification du support pour les demandes de JavaScript. Dans certains cas, vous pouvez avoir plusieurs instances d’un intergiciel (middleware) d’authentification. Par exemple, deux cookies middlewares où un contient une identité de base et est créé lorsqu’une authentification multifacteur a déclenché, car l’utilisateur a demandé une opération qui requiert une sécurité supplémentaire.

Schémas d’authentification sont nommés lors de l’intergiciel (middleware) d’authentification est configuré lors de l’authentification, par exemple

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AuthenticationScheme = "Cookie",
    LoginPath = new PathString("/Account/Unauthorized/"),
    AccessDeniedPath = new PathString("/Account/Forbidden/"),
    AutomaticAuthenticate = false
});

app.UseBearerAuthentication(options =>
{
    options.AuthenticationScheme = "Bearer";
    options.AutomaticAuthenticate = false;
});
```

Dans cette configuration deux middlewares d’authentification ont été ajoutés, un pour les cookies et l’autre pour le support.

>[!NOTE]
>Lorsque vous ajoutez plusieurs intergiciel (middleware) d’authentification, vous devez vous assurer qu’aucun intergiciel (middleware) n’est configuré pour s’exécuter automatiquement. Pour cela, définissez la `AutomaticAuthenticate` options de propriété sur false. Si vous ne pouvez pas effectuer ce filtrage par schéma ne fonctionnera pas.

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Sélection du schéma avec l’attribut Authorize

Aucune authentification intergiciel (middleware) est configuré pour exécuter automatiquement et de créer une identité, que vous devez, dans la phase d’autorisation choisir intergiciel (middleware) sera utilisé. Pour sélectionner l’intergiciel (middleware) que vous souhaitez autoriser le plus simple consiste à utiliser le `ActiveAuthenticationSchemes` propriété. Cette propriété accepte une liste séparée par des virgules des schémas d’authentification à utiliser. Par exemple :

```csharp
[Authorize(ActiveAuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

Dans l’exemple ci-dessus le cookie et le support middlewares exécute et ont la possibilité de créer et ajouter une identité pour l’utilisateur actuel. En spécifiant un schéma unique uniquement l’intergiciel spécifié s’exécute ;

```csharp
[Authorize(ActiveAuthenticationSchemes = "Bearer")]
```

Dans ce cas s’exécute uniquement l’intergiciel (middleware) avec le schéma de support, et toutes les identités basé sur un cookie sont ignorées.

## <a name="selecting-the-scheme-with-policies"></a>Sélection du schéma avec des stratégies

Si vous souhaitez spécifier les schémas souhaités dans [stratégie](policies.md#security-authorization-policies-based) que vous pouvez définir le `AuthenticationSchemes` collection lors de l’ajout de votre stratégie.

```csharp
options.AddPolicy("Over18", policy =>
{
    policy.AuthenticationSchemes.Add("Bearer");
    policy.RequireAuthenticatedUser();
    policy.Requirements.Add(new Over18Requirement());
});
```

Dans cet exemple le Over18 stratégie n’exécutée par rapport à l’identité créée par le `Bearer` intergiciel (middleware).

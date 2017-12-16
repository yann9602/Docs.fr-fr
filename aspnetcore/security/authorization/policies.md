---
title: "Autorisation personnalisée basée sur des stratégies dans ASP.NET Core"
author: rick-anderson
description: "Découvrez comment créer et utiliser des gestionnaires de stratégie d’autorisation personnalisée pour mettre en œuvre les spécifications d’autorisation dans une application ASP.NET Core."
keywords: "ASP.NET Core, d’autorisation, de stratégie personnalisée, de stratégie d’autorisation"
ms.author: riande
ms.custom: mvc
manager: wpickett
ms.date: 11/21/2017
ms.topic: article
ms.assetid: e422a1b2-dc4a-4bcc-b8d9-7ee62009b6a3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/policies
ms.openlocfilehash: 280dd72b75e39546061d8455931f597f50c829fe
ms.sourcegitcommit: f1436107b4c022b26f5235dddef103cec5aa6bff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2017
---
# <a name="custom-policy-based-authorization"></a>D’autorisation personnalisée basée sur des stratégies

Dans les coulisses, [d’autorisation basée sur le rôle](xref:security/authorization/roles) et [d’autorisation basée sur les revendications](xref:security/authorization/claims) utilisent une spécification, un gestionnaire de condition et une stratégie préconfigurée. Ces blocs de construction prend en charge l’expression d’évaluations d’autorisation dans le code. Le résultat est une structure d’autorisation plus riches, réutilisables, testable.

Une stratégie d’autorisation se compose d’une ou plusieurs conditions. Il est inscrit dans le cadre de la configuration du service d’autorisation, dans le `ConfigureServices` méthode de la `Startup` classe :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63,72)]

Dans l’exemple précédent, une stratégie de « AtLeast21 » est créée. Il a une seule exigence, que d’un minimum d’ancienneté, qui est fournie en tant que paramètre à la spécification.

Les stratégies sont appliquées à l’aide de la `[Authorize]` attribut avec le nom de la stratégie. Exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="requirements"></a>Spécifications

Une demande d’autorisation est une collection de paramètres de données une stratégie peut utiliser pour évaluer le principal utilisateur actuel. Dans notre stratégie « AtLeast21 », la spécification est un paramètre unique&mdash;l’ancienneté minimale. Une exigence implémente `IAuthorizationRequirement`, qui est une interface de marqueur vide. Une spécification de l’ancienneté minimale paramétrable peut être implémentée comme suit :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

> [!NOTE]
> Une exigence n’a pas besoin d’avoir des données ou des propriétés.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Gestionnaires d’autorisation

Un gestionnaire d’autorisation est responsable de l’évaluation des propriétés d’une spécification. Le Gestionnaire d’autorisation évalue la configuration requise sur un `AuthorizationHandlerContext` pour déterminer si l’accès est autorisé. Une condition peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers). Gestionnaires d’héritent `AuthorizationHandler<T>`, où `T` est la nécessité d’être géré.

<a name="security-authorization-handler-example"></a>

Le Gestionnaire de durée de vie minimale peut ressembler à ceci :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Le code précédent détermine si l’utilisateur principal a une date de naissance de revendications qui a été émis par un émetteur connu et approuvé. L’autorisation ne peut pas se produire lors de la revendication est manquante, auquel cas une tâche terminée est renvoyée. Lorsqu’une revendication est présente, l’âge de l’utilisateur est calculée. Si l’utilisateur répond à la durée de vie minimale définie par la spécification, l’autorisation est considérée comme réussie. Lorsqu’une autorisation est réussie, `context.Succeed` est appelé avec la spécification satisfaite en tant que paramètre.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Inscription du Gestionnaire

Les gestionnaires sont enregistrés dans la collection de services lors de la configuration. Exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63-65,72)]

Chaque gestionnaire est ajouté à la collection de services en appelant `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();`.

## <a name="what-should-a-handler-return"></a>Ce qui doit retourner un gestionnaire ?

Notez que la `Handle` méthode dans le [exemple de gestionnaire](#security-authorization-handler-example) ne retourne aucune valeur. Comment est un état de réussite ou échec indiqué ?

* Un gestionnaire indique la réussite en appelant `context.Succeed(IAuthorizationRequirement requirement)`, en passant à la demande qui a été validé.

* Un gestionnaire n’a pas besoin de gérer les défaillances en règle générale, comme les autres gestionnaires pour la même exigence peuvent réussir.

* Pour garantir la défaillance, même si d’autres gestionnaires de spécification réussissent, appelez `context.Fail`.

Indépendamment de ce que vous appelez à l’intérieur de votre gestionnaire, tous les gestionnaires d’une spécification seront appelées lorsqu’une stratégie requiert la spécification. Ainsi, les conditions requises pour des effets secondaires, tel que la journalisation, ce qui aura toujours lieu même si `context.Fail()` a été appelée dans un autre gestionnaire.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Pourquoi voudrais-je plusieurs gestionnaires pour une spécification ?

Dans le cas où vous souhaitez d’évaluation sur une **ou** à la fois, implémenter plusieurs gestionnaires pour une spécification unique. Par exemple, Microsoft a portes ouvre uniquement avec les cartes de clé. Si vous laissez votre carte de clé à la maison, la réceptionniste imprime un autocollant temporaire et ouvre la porte pour vous. Dans ce scénario, vous aurait une seule exigence, *BuildingEntry*, mais plusieurs gestionnaires, chacun d'entre eux examinant une seule exigence.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Assurez-vous que les deux gestionnaires sont [inscrit](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Si un gestionnaire réussit lorsqu’une stratégie évalue la `BuildingEntryRequirement`, l’évaluation de stratégie a réussi.

## <a name="using-a-func-to-fulfill-a-policy"></a>À l’aide d’une func pour répondre à une stratégie

Il peut arriver en les remplissant une stratégie est simple pour exprimer dans le code. Il est possible de fournir un `Func<AuthorizationHandlerContext, bool>` lors de la configuration de votre stratégie avec le `RequireAssertion` le Générateur de stratégie.

Par exemple, le précédent `BadgeEntryHandler` peut être réécrit comme suit :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=52-53,57-63)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Accès au contexte de demande MVC dans les gestionnaires

Le `HandleRequirementAsync` méthode que vous implémentez dans un gestionnaire d’autorisation possède deux paramètres : un `AuthorizationHandlerContext` et `TRequirement` vous gérez. Infrastructures telles que MVC ou Jabbr sont libres d’ajouter n’importe quel objet pour le `Resource` propriété sur le `AuthorizationHandlerContext` pour passer des informations supplémentaires.

Par exemple, MVC passe une instance de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) dans le `Resource` propriété. Cette propriété fournit l’accès aux `HttpContext`, `RouteData`et tout autre fournie par MVC et les Pages Razor.

L’utilisation de la `Resource` propriété est spécifiques de l’infrastructure. À l’aide des informations contenues dans le `Resource` propriété limite vos stratégies d’autorisation pour les infrastructures particulières. Vous devez effectuer un cast du `Resource` à l’aide de la propriété le `as` (mot clé), puis confirmez le cast a réussir pour vérifier votre code ne se bloquer avec une `InvalidCastException` sur les autres infrastructures :

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

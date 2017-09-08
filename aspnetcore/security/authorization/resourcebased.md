---
title: "Autorisation basée sur les ressources"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 0902ba17-5304-4a12-a2d4-e0904569e988
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/resourcebased
ms.openlocfilehash: 2f799588ba4aca4664e1679e4c34657e7ca121fb
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="resource-based-authorization"></a>Autorisation basée sur les ressources

<a name=security-authorization-resource-based></a>

Fréquence à laquelle l’autorisation dépend de la ressource sollicitée. Par exemple, un document peut avoir une propriété de l’auteur. Seul l’auteur du document serait autorisé à mettre à jour, afin de la ressource doit être chargée à partir du référentiel de document avant d’effectuer une évaluation d’autorisation. Cela n’est pas possible avec un attribut Authorize, comme l’évaluation de l’attribut a lieu avant la liaison de données et avant l’exécution de votre propre code pour charger une ressource à l’intérieur d’une action. Au lieu de l’autorisation déclarative, la méthode de l’attribut, nous devons utiliser d’autorisation impérative, où un développeur appelle une fonction d’Autoriser au sein de leur propre code.

## <a name="authorizing-within-your-code"></a>Autorisation dans votre code

L’autorisation est implémentée en tant que service, `IAuthorizationService`, enregistré dans la collection de service et disponibles via [injection de dépendance](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection) pour les contrôleurs pour accéder à.

```csharp
public class DocumentController : Controller
{
    IAuthorizationService _authorizationService;

    public DocumentController(IAuthorizationService authorizationService)
    {
        _authorizationService = authorizationService;
    }
}
```

`IAuthorizationService`a deux méthodes, une où vous passez la ressource et le nom de la stratégie et l’autre où vous passez une liste des conditions requises pour évaluer et la ressource.

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

<a name=security-authorization-resource-based-imperative></a>

Pour appeler la charge du service de votre ressource au sein de votre action puis appelez le `AuthorizeAsync` surcharge que vous avez besoin. Exemple :

```csharp
public async Task<IActionResult> Edit(Guid documentId)
{
    Document document = documentRepository.Find(documentId);

    if (document == null)
    {
        return new HttpNotFoundResult();
    }

    if (await _authorizationService.AuthorizeAsync(User, document, "EditPolicy"))
    {
        return View(document);
    }
    else
    {
        return new ChallengeResult();
    }
}
```

## <a name="writing-a-resource-based-handler"></a>Écriture d’un gestionnaire de ressources

Écriture d’un gestionnaire pour l’autorisation de ressource en fonction pas qui est très différent de [écriture d’un gestionnaire d’exigences brut](policies.md#security-authorization-policies-based-authorization-handler). Vous créez une spécification et ensuite implémentez un gestionnaire pour la demande, en spécifiant la configuration requise comme avant, ainsi que le type de ressource. Par exemple, un gestionnaire qui peut accepter une ressource Document ressemble comme suit :

```csharp
public class DocumentAuthorizationHandler : AuthorizationHandler<MyRequirement, Document>
{
    public override Task HandleRequirementAsync(AuthorizationHandlerContext context,
                                                MyRequirement requirement,
                                                Document resource)
    {
        // Validate the requirement against the resource and identity.

        return Task.CompletedTask;
    }
}
```

N’oubliez pas vous devez également inscrire votre gestionnaire dans le `ConfigureServices` méthode ;

```csharp
services.AddSingleton<IAuthorizationHandler, DocumentAuthorizationHandler>();
```

### <a name="operational-requirements"></a>Spécifications opérationnelles

Si vous prenez des décisions basées sur les opérations de lecture, écriture, mise à jour et suppression, vous pouvez utiliser la `OperationAuthorizationRequirement` classe dans le `Microsoft.AspNetCore.Authorization.Infrastructure` espace de noms. Cette classe d’exigence prégénérées permet d’écrire un seul gestionnaire qui porte un nom d’opération paramétrées, plutôt que de créer des classes individuelles pour chaque opération. Pour utiliser, il fournit des noms d’opération :

```csharp
public static class Operations
{
    public static OperationAuthorizationRequirement Create =
        new OperationAuthorizationRequirement { Name = "Create" };
    public static OperationAuthorizationRequirement Read =
        new OperationAuthorizationRequirement   { Name = "Read" };
    public static OperationAuthorizationRequirement Update =
        new OperationAuthorizationRequirement { Name = "Update" };
    public static OperationAuthorizationRequirement Delete =
        new OperationAuthorizationRequirement { Name = "Delete" };
}
```

Votre gestionnaire peut ensuite être implémenté, procédez comme suit à l’aide d’un hypothétique `Document` classe en tant que la ressource ;

```csharp
public class DocumentAuthorizationHandler :
    AuthorizationHandler<OperationAuthorizationRequirement, Document>
{
    public override Task HandleRequirementAsync(AuthorizationHandlerContext context,
                                                OperationAuthorizationRequirement requirement,
                                                Document resource)
    {
        // Validate the operation using the resource, the identity and
        // the Name property value from the requirement.

        return Task.CompletedTask;
    }
}
```

Vous pouvez voir le fonctionnement du gestionnaire sur `OperationAuthorizationRequirement`. Le code dans le gestionnaire doit prendre la propriété de nom de la spécification fournie en compte lors de l’établissement de ses évaluations.

Pour appeler un gestionnaire de ressources opérationnels que vous devez spécifier l’opération lors de l’appel `AuthorizeAsync` dans l’action. Exemple :

```csharp
if (await _authorizationService.AuthorizeAsync(User, document, Operations.Read))
{
    return View(document);
}
else
{
    return new ChallengeResult();
}
```

Cet exemple vérifie si l’utilisateur est en mesure d’effectuer l’opération de lecture en cours `document` instance. Si l’autorisation réussit, l’affichage pour le document s’affichera. Si l’autorisation échoue renvoyant `ChallengeResult` informe Échec de l’autorisation de l’intergiciel (middleware) et de l’intergiciel (middleware) peut prendre la réponse adéquate, par exemple renvoie un code d’état 401 ou 403 ou rediriger l’utilisateur vers une page de connexion d’authentification clients de navigateur interactif.

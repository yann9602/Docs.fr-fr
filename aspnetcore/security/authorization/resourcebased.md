---
title: "Autorisation basée sur les ressources dans ASP.NET Core"
author: scottaddie
description: "Découvrez comment implémenter l’autorisation basée sur les ressources dans une application ASP.NET Core lorsqu’un attribut Authorize ne suffit."
manager: wpickett
ms.author: scaddie
ms.custom: mvc
ms.date: 11/07/2017
ms.devlang: csharp
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authorization/resourcebased
ms.openlocfilehash: 708f306da740870b106cbeeb96879480f8745439
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="resource-based-authorization"></a>Autorisation basée sur les ressources

Par [Scott Addie](https://twitter.com/Scott_Addie)

Stratégie d’autorisation dépend de la ressource sollicitée. Envisagez d’un document qui possède une propriété de l’auteur. Seul l’auteur est autorisé à mettre à jour le document. Par conséquent, le document doit être récupéré à partir du magasin de données avant de l’évaluation de l’autorisation peut se produire.

Évaluation de l’attribut se produit avant la liaison de données et avant l’exécution de l’action qui charge le document ou le Gestionnaire de page. Pour ces raisons, l’autorisation déclarative avec un `[Authorize]` attribut ne suffit pas. Au lieu de cela, vous pouvez appeler une méthode d’autorisation personnalisée&mdash;un style appelé impératif d’autorisation.

Utilisez le [exemples d’applications](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample)) pour Explorer les fonctionnalités décrites dans cette rubrique.

## <a name="use-imperative-authorization"></a>Utiliser l’autorisation impérative

L’autorisation est implémentée comme un [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) de service et est enregistré dans la collection de service dans la `Startup` classe. Le service est rendu disponible via [injection de dépendance](xref:fundamentals/dependency-injection#fundamentals-dependency-injection) aux gestionnaires de page ou aux actions.

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService`a deux `AuthorizeAsync` surcharges de méthode : une acceptation de la ressource et le nom de la stratégie et l’autre accepte la ressource et une liste d’exigences à évaluer.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

---

<a name="security-authorization-resource-based-imperative"></a>

Dans l’exemple suivant, la ressource à sécuriser chargée personnalisé `Document` objet. Un `AuthorizeAsync` surcharge est appelée pour déterminer si l’utilisateur actuel est autorisé à modifier le document fourni. Une stratégie d’autorisation « EditPolicy » personnalisée est factorisée dans l’arbre de décision. Consultez [autorisation basée sur des stratégies de personnalisée](xref:security/authorization/policies) pour plus d’informations sur la création de stratégies d’autorisation.

> [!NOTE]
> Le code suivant exemples supposent que l’authentification a été exécuté et le jeu le `User` propriété.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

---

## <a name="write-a-resource-based-handler"></a>Écrire un gestionnaire de ressources

Écriture d’un gestionnaire pour l’autorisation basée sur la ressource n’est pas très différente de [écriture d’un gestionnaire d’exigences brut](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler). Créer une classe de demande personnalisée et implémenter une classe de gestionnaire de condition. La classe de gestionnaire spécifie l’exigence et le type de ressource. Par exemple, un gestionnaire utilisant un `SameAuthorRequirement` exigence et un `Document` ressource se présente comme suit :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

---

Enregistrer la configuration requise, le gestionnaire dans le `Startup.ConfigureServices` méthode :

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a>Spécifications opérationnelles

Si vous apportez des décisions basées sur les résultats de CRUD (**C**réer, **R**IRE, **U**mettre à jour, **D**supprim) opérations, utilisez le [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) classe d’assistance. Cette classe vous permet d’écrire un gestionnaire unique au lieu d’une classe individuelle pour chaque type d’opération. Pour l’utiliser, fournir des noms d’opération :

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

Le gestionnaire est implémenté comme suit, à l’aide un `OperationAuthorizationRequirement` exigence et un `Document` ressource :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

---

Le gestionnaire précédent valide l’opération à l’aide de la ressource, l’identité d’utilisateur et l’exigence de `Name` propriété.

Pour appeler un gestionnaire de ressources opérationnelles, spécifiez l’opération lors de l’appel `AuthorizeAsync` dans votre gestionnaire de page ou une action. L’exemple suivant détermine si l’utilisateur authentifié est autorisé à afficher le document fourni.

> [!NOTE]
> Le code suivant exemples supposent que l’authentification a été exécuté et le jeu le `User` propriété.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

Si l’autorisation réussit, la page d’affichage du document est retournée. Si l’autorisation échoue mais que l’utilisateur est authentifié, retour `ForbidResult` informe tout intergiciel d’authentification qui a l’autorisation a échoué. A `ChallengeResult` est retourné lorsque l’authentification doit être effectuée. Pour les clients de navigateur interactive, il peut être approprié rediriger l’utilisateur vers une page de connexion.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

Si l’autorisation réussit, la vue du document est retournée. Si l’autorisation échoue, retour `ChallengeResult` informe un intergiciel (middleware) d’authentification que l’autorisation a échoué, et l’intergiciel (middleware) peut prendre la réponse appropriée. Un code d’état 401 ou 403 peut renvoyer une réponse appropriée. Pour les clients de navigateur interactif, cela peut signifier redirigeant l’utilisateur vers une page de connexion.

---

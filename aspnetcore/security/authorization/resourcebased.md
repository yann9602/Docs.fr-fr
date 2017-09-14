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
ms.openlocfilehash: 7f7df52bf51a81558818836450997281a21b5839
ms.sourcegitcommit: f303a457644ed034a49aa89edecb4e79d9028cb1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="resource-based-authorization"></a><span data-ttu-id="1ecb2-103">Autorisation basée sur les ressources</span><span class="sxs-lookup"><span data-stu-id="1ecb2-103">Resource Based Authorization</span></span>

<a name=security-authorization-resource-based></a>

<span data-ttu-id="1ecb2-104">Fréquence à laquelle l’autorisation dépend de la ressource sollicitée.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-104">Often authorization depends upon the resource being accessed.</span></span> <span data-ttu-id="1ecb2-105">Par exemple, un document peut avoir une propriété de l’auteur.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-105">For example, a document may have an author property.</span></span> <span data-ttu-id="1ecb2-106">Seul l’auteur du document serait autorisé à mettre à jour, afin de la ressource doit être chargée à partir du référentiel de document avant d’effectuer une évaluation d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-106">Only the document author would be allowed to update it, so the resource must be loaded from the document repository before an authorization evaluation can be made.</span></span> <span data-ttu-id="1ecb2-107">Cela n’est pas possible avec un attribut Authorize, comme l’évaluation de l’attribut a lieu avant la liaison de données et avant l’exécution de votre propre code pour charger une ressource à l’intérieur d’une action.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-107">This cannot be done with an Authorize attribute, as attribute evaluation takes place before data binding and before your own code to load a resource runs inside an action.</span></span> <span data-ttu-id="1ecb2-108">Au lieu de l’autorisation déclarative, la méthode de l’attribut, nous devons utiliser d’autorisation impérative, où un développeur appelle une fonction d’Autoriser au sein de leur propre code.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-108">Instead of declarative authorization, the attribute method, we must use imperative authorization, where a developer calls an authorize function within their own code.</span></span>

## <a name="authorizing-within-your-code"></a><span data-ttu-id="1ecb2-109">Autorisation dans votre code</span><span class="sxs-lookup"><span data-stu-id="1ecb2-109">Authorizing within your code</span></span>

<span data-ttu-id="1ecb2-110">L’autorisation est implémentée en tant que service, `IAuthorizationService`, enregistré dans la collection de service et disponibles via [injection de dépendance](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection) pour les contrôleurs pour accéder à.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-110">Authorization is implemented as a service, `IAuthorizationService`, registered in the service collection and available via [dependency injection](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection) for Controllers to access.</span></span>

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

<span data-ttu-id="1ecb2-111">`IAuthorizationService`a deux méthodes, une où vous passez la ressource et le nom de la stratégie et l’autre où vous passez une liste des conditions requises pour évaluer et la ressource.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-111">`IAuthorizationService` has two methods, one where you pass the resource and the policy name and the other where you pass the resource and a list of requirements to evaluate.</span></span>

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

<a name=security-authorization-resource-based-imperative></a>

<span data-ttu-id="1ecb2-112">Pour appeler le service, charger votre ressource au sein de votre action puis appelez le `AuthorizeAsync` surcharge que vous avez besoin.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-112">To call the service, load your resource within your action then call the `AuthorizeAsync` overload you require.</span></span> <span data-ttu-id="1ecb2-113">Exemple :</span><span class="sxs-lookup"><span data-stu-id="1ecb2-113">For example:</span></span>

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

## <a name="writing-a-resource-based-handler"></a><span data-ttu-id="1ecb2-114">Écriture d’un gestionnaire de ressources</span><span class="sxs-lookup"><span data-stu-id="1ecb2-114">Writing a resource based handler</span></span>

<span data-ttu-id="1ecb2-115">Écriture d’un gestionnaire pour l’autorisation de ressource en fonction pas qui est très différent de [écriture d’un gestionnaire d’exigences brut](policies.md#security-authorization-policies-based-authorization-handler).</span><span class="sxs-lookup"><span data-stu-id="1ecb2-115">Writing a handler for resource based authorization is not that much different to [writing a plain requirements handler](policies.md#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="1ecb2-116">Vous créez une spécification et ensuite implémentez un gestionnaire pour la demande, en spécifiant la configuration requise comme avant, ainsi que le type de ressource.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-116">You create a requirement, and then implement a handler for the requirement, specifying the requirement as before and also the resource type.</span></span> <span data-ttu-id="1ecb2-117">Par exemple, un gestionnaire qui peut accepter une ressource Document se présenterait comme suit :</span><span class="sxs-lookup"><span data-stu-id="1ecb2-117">For example, a handler which might accept a Document resource would look as follows:</span></span>

```csharp
public class DocumentAuthorizationHandler : AuthorizationHandler<MyRequirement, Document>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
                                                MyRequirement requirement,
                                                Document resource)
    {
        // Validate the requirement against the resource and identity.

        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="1ecb2-118">N’oubliez pas vous devez également inscrire votre gestionnaire dans le `ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="1ecb2-118">Don't forget you also need to register your handler in the `ConfigureServices` method:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, DocumentAuthorizationHandler>();
```

### <a name="operational-requirements"></a><span data-ttu-id="1ecb2-119">Spécifications opérationnelles</span><span class="sxs-lookup"><span data-stu-id="1ecb2-119">Operational Requirements</span></span>

<span data-ttu-id="1ecb2-120">Si vous prenez des décisions basées sur les opérations de lecture, écriture, mise à jour et suppression, vous pouvez utiliser la `OperationAuthorizationRequirement` classe dans le `Microsoft.AspNetCore.Authorization.Infrastructure` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-120">If you are making decisions based on operations such as read, write, update and delete, you can use the `OperationAuthorizationRequirement` class in the `Microsoft.AspNetCore.Authorization.Infrastructure` namespace.</span></span> <span data-ttu-id="1ecb2-121">Cette classe d’exigence prégénérées permet d’écrire un seul gestionnaire qui porte un nom d’opération paramétrées, plutôt que de créer des classes individuelles pour chaque opération.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-121">This prebuilt requirement class enables you to write a single handler which has a parameterized operation name, rather than create individual classes for each operation.</span></span> <span data-ttu-id="1ecb2-122">Pour l’utiliser, fournir des noms d’opération :</span><span class="sxs-lookup"><span data-stu-id="1ecb2-122">To use it, provide some operation names:</span></span>

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

<span data-ttu-id="1ecb2-123">Votre gestionnaire peut ensuite être implémenté, procédez comme suit à l’aide d’un hypothétique `Document` classe en tant que la ressource :</span><span class="sxs-lookup"><span data-stu-id="1ecb2-123">Your handler could then be implemented as follows, using a hypothetical `Document` class as the resource:</span></span>

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

<span data-ttu-id="1ecb2-124">Vous pouvez voir le fonctionnement du gestionnaire sur `OperationAuthorizationRequirement`.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-124">You can see the handler works on `OperationAuthorizationRequirement`.</span></span> <span data-ttu-id="1ecb2-125">Le code dans le gestionnaire doit prendre la propriété de nom de la spécification fournie en compte lors de l’établissement de ses évaluations.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-125">The code inside the handler must take the Name property of the supplied requirement into account when making its evaluations.</span></span>

<span data-ttu-id="1ecb2-126">Pour appeler un gestionnaire de ressources opérationnels que vous devez spécifier l’opération lors de l’appel `AuthorizeAsync` dans l’action.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-126">To call an operational resource handler you need to specify the operation when calling `AuthorizeAsync` in your action.</span></span> <span data-ttu-id="1ecb2-127">Exemple :</span><span class="sxs-lookup"><span data-stu-id="1ecb2-127">For example:</span></span>

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

<span data-ttu-id="1ecb2-128">Cet exemple vérifie si l’utilisateur est en mesure d’effectuer l’opération de lecture en cours `document` instance.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-128">This example checks if the User is able to perform the Read operation for the current `document` instance.</span></span> <span data-ttu-id="1ecb2-129">Si l’autorisation réussit, l’affichage pour le document s’affichera.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-129">If authorization succeeds the view for the document will be returned.</span></span> <span data-ttu-id="1ecb2-130">Si l’autorisation échoue renvoyant `ChallengeResult` informe Échec de l’autorisation de l’intergiciel (middleware) et de l’intergiciel (middleware) peut prendre la réponse adéquate, par exemple renvoie un code d’état 401 ou 403 ou rediriger l’utilisateur vers une page de connexion d’authentification clients de navigateur interactif.</span><span class="sxs-lookup"><span data-stu-id="1ecb2-130">If authorization fails returning `ChallengeResult` will inform any authentication middleware authorization has failed and the middleware can take the appropriate response, for example returning a 401 or 403 status code, or redirecting the user to a login page for interactive browser clients.</span></span>

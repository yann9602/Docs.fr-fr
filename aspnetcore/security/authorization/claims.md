---
title: "Autorisation basée sur les revendications"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 737be5cd-3511-4f1c-b0ce-65403fb5eed3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/claims
ms.openlocfilehash: fca75952429d48b02c2c4350b79e29a1957599dc
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="claims-based-authorization"></a><span data-ttu-id="a8f5b-103">Autorisation basée sur les revendications</span><span class="sxs-lookup"><span data-stu-id="a8f5b-103">Claims-Based Authorization</span></span>

<a name=security-authorization-claims-based></a>

<span data-ttu-id="a8f5b-104">Lors de la création d’une identité qu’il peut être affecté à une ou plusieurs revendications émises par une partie de confiance.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="a8f5b-105">Une revendication est le nom de valeur paire qui représente le sujet est, pas le sujet peut le faire.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-105">A claim is name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="a8f5b-106">Par exemple, vous pouvez avoir un permis de conduire émis par une autorité de licence conduite local.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-106">For example you may have a Drivers License, issued by a local driving license authority.</span></span> <span data-ttu-id="a8f5b-107">Conduire votre permis d’a votre date de naissance.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="a8f5b-108">Dans ce cas le nom de la revendication serait `DateOfBirth`, la valeur de revendication est votre date de naissance, par exemple `8th June 1970` et l’émetteur est l’autorité déterminant de la licence.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="a8f5b-109">Autorisation basée sur les revendications, à son la plus simple, vérifie la valeur de revendication et autorise l’accès à une ressource en fonction de cette valeur.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="a8f5b-110">Pour exemple, si vous souhaitez que l’accès à un club nuit le processus d’autorisation peut être :</span><span class="sxs-lookup"><span data-stu-id="a8f5b-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="a8f5b-111">Le responsable de la sécurité porte serait évaluer la valeur de votre date de naissance revendication et qu’elles s’approuvent l’émetteur (l’autorité de licence conduite) avant de qui que vous donne accès.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="a8f5b-112">Une identité peut contenir plusieurs revendications avec plusieurs valeurs et peut contenir plusieurs revendications du même type.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="a8f5b-113">Ajout de contrôles de revendications</span><span class="sxs-lookup"><span data-stu-id="a8f5b-113">Adding claims checks</span></span>

<span data-ttu-id="a8f5b-114">Revendication les vérifications d’autorisations déclaratives ; le développeur les incorpore dans leur code, par rapport à un contrôleur ou une action dans un contrôleur, en spécifiant les revendications qui l’utilisateur actuel doit posséder et éventuellement la valeur de la revendication doit détenir pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="a8f5b-115">Configuration requise est basée sur la stratégie de revendications, le développeur doit créer et enregistrer une stratégie d’exprimer les exigences de revendications.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="a8f5b-116">Le type le plus simple de stratégie recherche la présence d’une revendication de revendication et ne vérifie pas la valeur.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-116">The simplest type of claim policy looks for the presence of a claim and does not check the value.</span></span>

<span data-ttu-id="a8f5b-117">Vous devez d’abord créer et enregistrer la stratégie.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-117">First you need to build and register the policy.</span></span> <span data-ttu-id="a8f5b-118">Cette opération a lieu dans le cadre de la configuration du service d’autorisation, qui dure généralement partie dans `ConfigureServices()` dans votre *Startup.cs* fichier.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

<span data-ttu-id="a8f5b-119">Dans ce cas le `EmployeeOnly` stratégie vérifie la présence d’un `EmployeeNumber` de revendication de l’identité actuelle.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="a8f5b-120">Vous appliquez ensuite la stratégie à l’aide de la `Policy` propriété sur le `AuthorizeAttribute` attribut pour spécifier le nom de la stratégie ;</span><span class="sxs-lookup"><span data-stu-id="a8f5b-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="a8f5b-121">Le `AuthorizeAttribute` attribut peut être appliqué à un contrôleur entier, dans cette instance uniquement la stratégie de correspondance des identités peut accéder à une Action sur le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="a8f5b-122">Si vous disposez d’un contrôleur qui est protégé par le `AuthorizeAttribute` d’attribut, mais souhaitez autoriser l’accès anonyme aux actions particulières que vous appliquez le `AllowAnonymousAttribute` attribut.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-122">If you have a controller that is protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

<span data-ttu-id="a8f5b-123">La plupart des revendications sont fournies avec une valeur.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-123">Most claims come with a value.</span></span> <span data-ttu-id="a8f5b-124">Vous pouvez spécifier une liste de valeurs autorisées lors de la création de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="a8f5b-125">L’exemple suivant aboutirait uniquement pour les employés dont le numéro employé a été 1, 2, 3, 4 ou 5.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="a8f5b-126">Évaluation des stratégies</span><span class="sxs-lookup"><span data-stu-id="a8f5b-126">Multiple Policy Evaluation</span></span>

<span data-ttu-id="a8f5b-127">Si vous appliquez plusieurs stratégies à un contrôleur ou d’action toutes les stratégies doivent passer avant que l’accès est accordé.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-127">If you apply multiple policies to a controller or action then all policies must pass before access is granted.</span></span> <span data-ttu-id="a8f5b-128">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a8f5b-128">For example:</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

<span data-ttu-id="a8f5b-129">Dans l’exemple ci-dessus n’importe quelle identité ce qui répond à la `EmployeeOnly` stratégie peut accéder à la `Payslip` action en tant que cette stratégie est appliquée sur le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-129">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="a8f5b-130">Toutefois afin d’appeler le `UpdateSalary` action doit répondre à l’identité *les deux* le `EmployeeOnly` stratégie et le `HumanResources` stratégie.</span><span class="sxs-lookup"><span data-stu-id="a8f5b-130">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="a8f5b-131">Si vous souhaitez que les stratégies plus complexes, tels que prend une date de naissance revendication, calculer un âge à partir de celui-ci, puis la vérification de la durée de vie est 21 ou antérieure, vous devez écrire [gestionnaires de stratégie personnalisée](policies.md#security-authorization-policies-based).</span><span class="sxs-lookup"><span data-stu-id="a8f5b-131">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](policies.md#security-authorization-policies-based).</span></span>

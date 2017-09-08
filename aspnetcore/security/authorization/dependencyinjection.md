---
title: "Injection de dépendances dans les gestionnaires d’exigence"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5fb6625c-173a-4feb-8380-73c9844dc23c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 37d197d7696a6e91fa236b2defc577959c95c49f
ms.sourcegitcommit: 0a70706a3814d2684f3ff96095d1e8291d559cc7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2017
---
# <a name="dependency-injection-in-requirement-handlers"></a><span data-ttu-id="96022-103">Injection de dépendances dans les gestionnaires d’exigence</span><span class="sxs-lookup"><span data-stu-id="96022-103">Dependency Injection in requirement handlers</span></span>

<a name=security-authorization-di></a>

<span data-ttu-id="96022-104">[Les gestionnaires d’autorisation doivent être inscrit](policies.md#security-authorization-policies-based-handler-registration) dans la collection de service lors de la configuration (à l’aide de [injection de dépendance](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="96022-104">[Authorization handlers must be registered](policies.md#security-authorization-policies-based-handler-registration) in the service collection during configuration (using [dependency injection](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)).</span></span>

<span data-ttu-id="96022-105">Supposons que vous disposiez d’un référentiel de règles que vous souhaitez évaluer à l’intérieur d’un gestionnaire d’autorisation, et ce référentiel a été inscrit dans la collection de service.</span><span class="sxs-lookup"><span data-stu-id="96022-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span>  <span data-ttu-id="96022-106">L’autorisation sera résoudre et qui injecter dans votre constructeur.</span><span class="sxs-lookup"><span data-stu-id="96022-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="96022-107">Par exemple, si vous souhaitez utiliser ASP. NET de journalisation de l’infrastructure que vous ne souhaitez pas injecter `ILoggerFactory` dans votre gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="96022-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="96022-108">Un gestionnaire de ce type peut ressembler à :</span><span class="sxs-lookup"><span data-stu-id="96022-108">Such a handler might look like:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="96022-109">Vous devez inscrire le gestionnaire avec `services.AddSingleton()`:</span><span class="sxs-lookup"><span data-stu-id="96022-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
   ```

<span data-ttu-id="96022-110">Une instance du gestionnaire va être créé au démarrage de votre application et qui seront DI injecter inscrit `ILoggerFactory` dans votre constructeur.</span><span class="sxs-lookup"><span data-stu-id="96022-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="96022-111">Gestionnaires d’utilisent Entity Framework ne doit pas être enregistrés comme singletons.</span><span class="sxs-lookup"><span data-stu-id="96022-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>

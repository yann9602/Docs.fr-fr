---
title: "Injection de dépendances dans les gestionnaires d’exigence"
author: rick-anderson
description: "Ce document décrit comment injecter des gestionnaires de demande d’autorisation dans une application ASP.NET Core en utilisant l’injection de dépendance."
keywords: "ASP.NET Core, injection de dépendances, le Gestionnaire d’autorisation"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5fb6625c-173a-4feb-8380-73c9844dc23c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/dependencyinjection
ms.openlocfilehash: b5e590cc63387553af7385b611cdf8cd6b255db7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="dependency-injection-in-requirement-handlers"></a>Injection de dépendances dans les gestionnaires d’exigence

<a name="security-authorization-di"></a>

[Les gestionnaires d’autorisation doivent être inscrit](policies.md#handler-registration) dans la collection de service lors de la configuration (à l’aide de [injection de dépendance](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)).

Supposons que vous disposiez d’un référentiel de règles que vous souhaitez évaluer à l’intérieur d’un gestionnaire d’autorisation, et ce référentiel a été inscrit dans la collection de service. L’autorisation sera résoudre et qui injecter dans votre constructeur.

Par exemple, si vous souhaitez utiliser ASP. NET de journalisation de l’infrastructure que vous ne souhaitez pas injecter `ILoggerFactory` dans votre gestionnaire. Un gestionnaire de ce type peut ressembler à :

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

Vous devez inscrire le gestionnaire avec `services.AddSingleton()`:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Une instance du gestionnaire va être créé au démarrage de votre application et qui seront DI injecter inscrit `ILoggerFactory` dans votre constructeur.

> [!NOTE]
> Gestionnaires d’utilisent Entity Framework ne doit pas être enregistrés comme singletons.

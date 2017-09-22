---
title: Gestion des erreurs dans ASP.NET Core
author: ardalis
description: "Explique comment gérer les erreurs dans les applications ASP.NET Core"
keywords: ASP.NET Core, gestion des erreurs, la gestion des exceptions
ms.author: tdykstra
manager: wpickett
ms.date: 11/30/2016
ms.topic: article
ms.assetid: 4db51023-c8a6-4119-bbbe-3917e272c260
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/error-handling
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 93f0724dbe98316e2b5a0af0ac1760c3aac2f1d0
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-error-handling-in-aspnet-core"></a>Introduction à la gestion des erreurs dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/) et [Tom Dykstra](https://github.com/tdykstra/)

Cet article traite des appoaches commune pour la gestion des erreurs dans les applications ASP.NET Core.

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/sample)

## <a name="the-developer-exception-page"></a>La page d’exception de développeur

Pour configurer une application pour afficher une page qui affiche des informations détaillées sur les exceptions, vous devez installer le `Microsoft.AspNetCore.Diagnostics` NuGet package et ajouter une ligne à la [configurer la méthode dans la classe de démarrage](startup.md):

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]

Put `UseDeveloperExceptionPage` avant tout intergiciel (middleware) que vous souhaitez intercepter les exceptions, telles que `app.UseMvc`.

>[!WARNING]
> Activer la page d’exception développeur **uniquement lorsque l’application est en cours d’exécution dans l’environnement de développement**. Vous ne souhaitez pas partager publiquement les informations sur les exceptions détaillées lors de l’exécution de l’application en production. [En savoir plus sur la configuration d’environnements](environments.md).

Pour afficher la page d’exception de développeur, exécutez l’exemple d’application avec l’environnement de la valeur `Development`et ajoutez `?throw=true` à l’URL de base de l’application. Cette page inclut plusieurs onglets avec des informations sur l’exception et la demande. Le premier onglet inclut une trace de pile. 

![Trace de la pile](error-handling/_static/developer-exception-page.png)

L’onglet suivant illustre les paramètres de chaîne, de la requête le cas échéant.

![Paramètres de chaîne de requête](error-handling/_static/developer-exception-page-query.png)

Cette requête ne contenaient pas tous les cookies, mais si c’était le cas, ils apparaissent sur le **Cookies** onglet. Vous pouvez voir les en-têtes qui ont été passés dans le dernier onglet.

![En-têtes](error-handling/_static/developer-exception-page-headers.png)

## <a name="configuring-a-custom-exception-handling-page"></a>Configuration d’une page d’exceptions personnalisées

Il est judicieux de configurer une page Gestionnaire d’exceptions à utiliser lors de l’application n’est pas en cours d’exécution le `Development` environnement.

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]

Dans une application MVC, ne pas explicitement décorer la méthode d’action d’erreur gestionnaire avec les attributs de méthode HTTP, tel que `HttpGet`. À l’aide de verbes explicites peut empêcher certaines demandes la méthode.

```csharp
[Route("/Error")]
public IActionResult Index()
{
    // Handle error here
}
```

## <a name="configuring-status-code-pages"></a>Configuration des pages de codes d’état

Par défaut, votre application ne permet pas d’une page de codes d’état complètes pour les codes d’état HTTP tel que 500 (erreur interne du serveur) ou 404 (introuvable). Vous pouvez configurer le `StatusCodePagesMiddleware` en ajoutant une ligne à la `Configure` méthode :

```csharp
app.UseStatusCodePages();
```

Par défaut, cet intergiciel (middleware) ajoute des gestionnaires simples, texte uniquement pour les codes d’état courants, tels que 404 :

![page 404](error-handling/_static/default-404-status-code.png)

L’intergiciel (middleware) prend en charge plusieurs méthodes d’extension différentes. L’une prend une expression lambda, une autre qui prend une chaîne de format et de type de contenu.

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePages)]

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

Il existe également des méthodes d’extension de redirection. Un envoie un code de 302 état au client, et une retourne le code d’état d’origine au client, mais exécute également le gestionnaire pour l’URL de redirection.

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

Si vous devez désactiver les pages de codes d’état pour certaines requêtes, vous pouvez le faire :

```csharp
var statusCodePagesFeature = context.Features.Get<IStatusCodePagesFeature>();
if (statusCodePagesFeature != null)
{
  statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Code de gestion des exceptions

Le code dans les pages de gestion des exceptions peut lever des exceptions. Il est souvent judicieux de pages d’erreur de production sont constitués du contenu purement statique.

En outre, sachez qu’une fois que les en-têtes de réponse ont été envoyés, vous ne pouvez pas modifier le code d’état de la réponse, ni peuvent exécuter toutes les pages d’exception ou les gestionnaires. La réponse doit être terminée ou la connexion est abandonnée.

## <a name="server-exception-handling"></a>Gestion des exceptions de serveur

En plus de la logique de votre application, de gestion des exceptions la [server](servers/index.md) qui héberge votre application effectue certaines exceptions. Si le serveur intercepte une exception avant des en-têtes sont envoyés, le serveur envoie une réponse d’erreur de serveur interne 500 sans corps. Si le serveur intercepte une exception une fois que les en-têtes ont été envoyés, le serveur ferme la connexion. Les demandes qui ne sont pas gérés par votre application sont gérées par le serveur. Toute exception qui se produit est gérée par l’exception de celle du serveur gestion. Une configuré des pages d’erreurs personnalisées ou d’intergiciel (middleware) de la gestion des exceptions ou des filtres n’affectent pas ce comportement.

## <a name="startup-exception-handling"></a>Gestion des exceptions de démarrage

Uniquement la couche d’hébergement peut gérer les exceptions qui se produisent lors du démarrage de l’application. Vous pouvez [configurer le comporte de l’ordinateur hôte en réponse aux erreurs lors du démarrage de](hosting.md#detailed-errors) à l’aide de `captureStartupErrors` et `detailedErrors` clé.

Hébergement peut uniquement afficher une page d’erreur pour une erreur de démarrage capturé si l’erreur se produit une fois l’hôte adresse/port de liaison. Si n’importe quelle liaison échoue pour une raison quelconque, la couche d’hébergement enregistre une exception critique, les blocages de processus dotnet, et aucune page d’erreur ne s’affiche.

## <a name="aspnet-mvc-error-handling"></a>Gestion des erreurs de ASP.NET MVC

[MVC](../mvc/index.md) applications ont des options supplémentaires pour la gestion des erreurs, telles que la configuration des filtres d’exception et l’exécution de la validation de modèle.

### <a name="exception-filters"></a>Filtres d’exception

Filtres d’exception peuvent être configurés globalement ou sur une base par contrôleur ou par l’action dans une application MVC. Ces filtres gèrent les exceptions non gérées qui se produit pendant l’exécution d’une action de contrôleur ou un autre filtre et ne sont pas appelés dans le cas contraire. En savoir plus sur les filtres d’exception dans [filtres](../mvc/controllers/filters.md).

>[!TIP]
> Filtres d’exception sont adaptés pour intercepter les exceptions qui se produisent dans les actions de MVC, mais ils ne sont pas aussi flexibles que l’intergiciel (middleware) de gestion des erreurs. Préférez l’intergiciel (middleware) pour le cas général et utiliser des filtres uniquement où vous avez besoin pour gérer les erreurs *différemment* en fonction de l’action MVC a été choisie.

### <a name="handling-model-state-errors"></a>État de modèle de gestion des erreurs

[Validation des modèles](../mvc/models/validation.md) se produit avant chaque action du contrôleur qui est appelée, et il est responsable de la méthode d’action à inspecter `ModelState.IsValid` et réagir de façon appropriée.

Certaines applications choisit de suivre une convention standard pour traiter les erreurs de validation de modèle, auquel cas un [filtre](../mvc/controllers/filters.md) peut être un emplacement approprié pour implémenter une telle stratégie. Vous devez tester le comportement de vos actions avec les États de modèles non valide. Pour en savoir plus [logique du contrôleur de test](../mvc/controllers/testing.md).




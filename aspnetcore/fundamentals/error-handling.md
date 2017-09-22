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
# <a name="introduction-to-error-handling-in-aspnet-core"></a><span data-ttu-id="21318-104">Introduction à la gestion des erreurs dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21318-104">Introduction to Error Handling in ASP.NET Core</span></span>

<span data-ttu-id="21318-105">Par [Steve Smith](https://ardalis.com/) et [Tom Dykstra](https://github.com/tdykstra/)</span><span class="sxs-lookup"><span data-stu-id="21318-105">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra/)</span></span>

<span data-ttu-id="21318-106">Cet article traite des appoaches commune pour la gestion des erreurs dans les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="21318-106">This article covers common appoaches to handling errors in ASP.NET Core apps.</span></span>

[<span data-ttu-id="21318-107">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="21318-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/sample)

## <a name="the-developer-exception-page"></a><span data-ttu-id="21318-108">La page d’exception de développeur</span><span class="sxs-lookup"><span data-stu-id="21318-108">The developer exception page</span></span>

<span data-ttu-id="21318-109">Pour configurer une application pour afficher une page qui affiche des informations détaillées sur les exceptions, vous devez installer le `Microsoft.AspNetCore.Diagnostics` NuGet package et ajouter une ligne à la [configurer la méthode dans la classe de démarrage](startup.md):</span><span class="sxs-lookup"><span data-stu-id="21318-109">To configure an app to display a page that shows detailed information about exceptions, install the `Microsoft.AspNetCore.Diagnostics` NuGet package and add a line to the [Configure method in the Startup class](startup.md):</span></span>

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]

<span data-ttu-id="21318-110">Put `UseDeveloperExceptionPage` avant tout intergiciel (middleware) que vous souhaitez intercepter les exceptions, telles que `app.UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="21318-110">Put `UseDeveloperExceptionPage` before any middleware you want to catch exceptions in, such as `app.UseMvc`.</span></span>

>[!WARNING]
> <span data-ttu-id="21318-111">Activer la page d’exception développeur **uniquement lorsque l’application est en cours d’exécution dans l’environnement de développement**.</span><span class="sxs-lookup"><span data-stu-id="21318-111">Enable the developer exception page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="21318-112">Vous ne souhaitez pas partager publiquement les informations sur les exceptions détaillées lors de l’exécution de l’application en production.</span><span class="sxs-lookup"><span data-stu-id="21318-112">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="21318-113">[En savoir plus sur la configuration d’environnements](environments.md).</span><span class="sxs-lookup"><span data-stu-id="21318-113">[Learn more about configuring environments](environments.md).</span></span>

<span data-ttu-id="21318-114">Pour afficher la page d’exception de développeur, exécutez l’exemple d’application avec l’environnement de la valeur `Development`et ajoutez `?throw=true` à l’URL de base de l’application.</span><span class="sxs-lookup"><span data-stu-id="21318-114">To see the developer exception page, run the sample application with the environment set to `Development`, and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="21318-115">Cette page inclut plusieurs onglets avec des informations sur l’exception et la demande.</span><span class="sxs-lookup"><span data-stu-id="21318-115">The page includes several tabs with information about the exception and the request.</span></span> <span data-ttu-id="21318-116">Le premier onglet inclut une trace de pile.</span><span class="sxs-lookup"><span data-stu-id="21318-116">The first tab includes a stack trace.</span></span> 

![Trace de la pile](error-handling/_static/developer-exception-page.png)

<span data-ttu-id="21318-118">L’onglet suivant illustre les paramètres de chaîne, de la requête le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="21318-118">The next tab shows the query string parameters, if any.</span></span>

![Paramètres de chaîne de requête](error-handling/_static/developer-exception-page-query.png)

<span data-ttu-id="21318-120">Cette requête ne contenaient pas tous les cookies, mais si c’était le cas, ils apparaissent sur le **Cookies** onglet. Vous pouvez voir les en-têtes qui ont été passés dans le dernier onglet.</span><span class="sxs-lookup"><span data-stu-id="21318-120">This request didn't have any cookies, but if it did, they would appear on the **Cookies** tab. You can see the headers that were passed in the last tab.</span></span>

![En-têtes](error-handling/_static/developer-exception-page-headers.png)

## <a name="configuring-a-custom-exception-handling-page"></a><span data-ttu-id="21318-122">Configuration d’une page d’exceptions personnalisées</span><span class="sxs-lookup"><span data-stu-id="21318-122">Configuring a custom exception handling page</span></span>

<span data-ttu-id="21318-123">Il est judicieux de configurer une page Gestionnaire d’exceptions à utiliser lors de l’application n’est pas en cours d’exécution le `Development` environnement.</span><span class="sxs-lookup"><span data-stu-id="21318-123">It's a good idea to configure an exception handler page to use when the app is not running in the `Development` environment.</span></span>

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]

<span data-ttu-id="21318-124">Dans une application MVC, ne pas explicitement décorer la méthode d’action d’erreur gestionnaire avec les attributs de méthode HTTP, tel que `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="21318-124">In an MVC app, don't explicitly decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="21318-125">À l’aide de verbes explicites peut empêcher certaines demandes la méthode.</span><span class="sxs-lookup"><span data-stu-id="21318-125">Using explicit verbs could prevent some requests from reaching the method.</span></span>

```csharp
[Route("/Error")]
public IActionResult Index()
{
    // Handle error here
}
```

## <a name="configuring-status-code-pages"></a><span data-ttu-id="21318-126">Configuration des pages de codes d’état</span><span class="sxs-lookup"><span data-stu-id="21318-126">Configuring status code pages</span></span>

<span data-ttu-id="21318-127">Par défaut, votre application ne permet pas d’une page de codes d’état complètes pour les codes d’état HTTP tel que 500 (erreur interne du serveur) ou 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="21318-127">By default, your app will not provide a rich status code page for HTTP status codes such as 500 (Internal Server Error) or 404 (Not Found).</span></span> <span data-ttu-id="21318-128">Vous pouvez configurer le `StatusCodePagesMiddleware` en ajoutant une ligne à la `Configure` méthode :</span><span class="sxs-lookup"><span data-stu-id="21318-128">You can configure the `StatusCodePagesMiddleware` by adding a line to the `Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="21318-129">Par défaut, cet intergiciel (middleware) ajoute des gestionnaires simples, texte uniquement pour les codes d’état courants, tels que 404 :</span><span class="sxs-lookup"><span data-stu-id="21318-129">By default, this middleware adds simple, text-only handlers for common status codes, such as 404:</span></span>

![page 404](error-handling/_static/default-404-status-code.png)

<span data-ttu-id="21318-131">L’intergiciel (middleware) prend en charge plusieurs méthodes d’extension différentes.</span><span class="sxs-lookup"><span data-stu-id="21318-131">The middleware supports several different extension methods.</span></span> <span data-ttu-id="21318-132">L’une prend une expression lambda, une autre qui prend une chaîne de format et de type de contenu.</span><span class="sxs-lookup"><span data-stu-id="21318-132">One takes a lambda expression, another takes a content type and format string.</span></span>

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePages)]

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

<span data-ttu-id="21318-133">Il existe également des méthodes d’extension de redirection.</span><span class="sxs-lookup"><span data-stu-id="21318-133">There are also redirect extension methods.</span></span> <span data-ttu-id="21318-134">Un envoie un code de 302 état au client, et une retourne le code d’état d’origine au client, mais exécute également le gestionnaire pour l’URL de redirection.</span><span class="sxs-lookup"><span data-stu-id="21318-134">One sends a 302 status code to the client, and one returns the original status code to the client but also executes the handler for the redirect URL.</span></span>

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

<span data-ttu-id="21318-135">Si vous devez désactiver les pages de codes d’état pour certaines requêtes, vous pouvez le faire :</span><span class="sxs-lookup"><span data-stu-id="21318-135">If you need to disable status code pages for certain requests, you can do so:</span></span>

```csharp
var statusCodePagesFeature = context.Features.Get<IStatusCodePagesFeature>();
if (statusCodePagesFeature != null)
{
  statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="21318-136">Code de gestion des exceptions</span><span class="sxs-lookup"><span data-stu-id="21318-136">Exception-handling code</span></span>

<span data-ttu-id="21318-137">Le code dans les pages de gestion des exceptions peut lever des exceptions.</span><span class="sxs-lookup"><span data-stu-id="21318-137">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="21318-138">Il est souvent judicieux de pages d’erreur de production sont constitués du contenu purement statique.</span><span class="sxs-lookup"><span data-stu-id="21318-138">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="21318-139">En outre, sachez qu’une fois que les en-têtes de réponse ont été envoyés, vous ne pouvez pas modifier le code d’état de la réponse, ni peuvent exécuter toutes les pages d’exception ou les gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="21318-139">Also, be aware that once the headers for a response have been sent, you can't change the response's status code, nor can any exception pages or handlers run.</span></span> <span data-ttu-id="21318-140">La réponse doit être terminée ou la connexion est abandonnée.</span><span class="sxs-lookup"><span data-stu-id="21318-140">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="21318-141">Gestion des exceptions de serveur</span><span class="sxs-lookup"><span data-stu-id="21318-141">Server exception handling</span></span>

<span data-ttu-id="21318-142">En plus de la logique de votre application, de gestion des exceptions la [server](servers/index.md) qui héberge votre application effectue certaines exceptions.</span><span class="sxs-lookup"><span data-stu-id="21318-142">In addition to the exception handling logic in your app, the [server](servers/index.md) hosting your app performs some exception handling.</span></span> <span data-ttu-id="21318-143">Si le serveur intercepte une exception avant des en-têtes sont envoyés, le serveur envoie une réponse d’erreur de serveur interne 500 sans corps.</span><span class="sxs-lookup"><span data-stu-id="21318-143">If the server catches an exception before the headers are sent, the server sends a 500 Internal Server Error response with no body.</span></span> <span data-ttu-id="21318-144">Si le serveur intercepte une exception une fois que les en-têtes ont été envoyés, le serveur ferme la connexion.</span><span class="sxs-lookup"><span data-stu-id="21318-144">If the server catches an exception after the headers have been sent, the server closes the connection.</span></span> <span data-ttu-id="21318-145">Les demandes qui ne sont pas gérés par votre application sont gérées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="21318-145">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="21318-146">Toute exception qui se produit est gérée par l’exception de celle du serveur gestion.</span><span class="sxs-lookup"><span data-stu-id="21318-146">Any exception that occurs is handled by the server's exception handling.</span></span> <span data-ttu-id="21318-147">Une configuré des pages d’erreurs personnalisées ou d’intergiciel (middleware) de la gestion des exceptions ou des filtres n’affectent pas ce comportement.</span><span class="sxs-lookup"><span data-stu-id="21318-147">Any configured custom error pages or exception handling middleware or filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="21318-148">Gestion des exceptions de démarrage</span><span class="sxs-lookup"><span data-stu-id="21318-148">Startup exception handling</span></span>

<span data-ttu-id="21318-149">Uniquement la couche d’hébergement peut gérer les exceptions qui se produisent lors du démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="21318-149">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="21318-150">Vous pouvez [configurer le comporte de l’ordinateur hôte en réponse aux erreurs lors du démarrage de](hosting.md#detailed-errors) à l’aide de `captureStartupErrors` et `detailedErrors` clé.</span><span class="sxs-lookup"><span data-stu-id="21318-150">You can [configure how the host behaves in response to errors during startup](hosting.md#detailed-errors) using `captureStartupErrors` and the `detailedErrors` key.</span></span>

<span data-ttu-id="21318-151">Hébergement peut uniquement afficher une page d’erreur pour une erreur de démarrage capturé si l’erreur se produit une fois l’hôte adresse/port de liaison.</span><span class="sxs-lookup"><span data-stu-id="21318-151">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="21318-152">Si n’importe quelle liaison échoue pour une raison quelconque, la couche d’hébergement enregistre une exception critique, les blocages de processus dotnet, et aucune page d’erreur ne s’affiche.</span><span class="sxs-lookup"><span data-stu-id="21318-152">If any binding fails for any reason, the hosting layer logs a critical exception, the dotnet process crashes, and no error page is displayed.</span></span>

## <a name="aspnet-mvc-error-handling"></a><span data-ttu-id="21318-153">Gestion des erreurs de ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="21318-153">ASP.NET MVC error handling</span></span>

<span data-ttu-id="21318-154">[MVC](../mvc/index.md) applications ont des options supplémentaires pour la gestion des erreurs, telles que la configuration des filtres d’exception et l’exécution de la validation de modèle.</span><span class="sxs-lookup"><span data-stu-id="21318-154">[MVC](../mvc/index.md) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="21318-155">Filtres d’exception</span><span class="sxs-lookup"><span data-stu-id="21318-155">Exception Filters</span></span>

<span data-ttu-id="21318-156">Filtres d’exception peuvent être configurés globalement ou sur une base par contrôleur ou par l’action dans une application MVC.</span><span class="sxs-lookup"><span data-stu-id="21318-156">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="21318-157">Ces filtres gèrent les exceptions non gérées qui se produit pendant l’exécution d’une action de contrôleur ou un autre filtre et ne sont pas appelés dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="21318-157">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter, and are not called otherwise.</span></span> <span data-ttu-id="21318-158">En savoir plus sur les filtres d’exception dans [filtres](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="21318-158">Learn more about exception filters in [Filters](../mvc/controllers/filters.md).</span></span>

>[!TIP]
> <span data-ttu-id="21318-159">Filtres d’exception sont adaptés pour intercepter les exceptions qui se produisent dans les actions de MVC, mais ils ne sont pas aussi flexibles que l’intergiciel (middleware) de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="21318-159">Exception filters are good for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="21318-160">Préférez l’intergiciel (middleware) pour le cas général et utiliser des filtres uniquement où vous avez besoin pour gérer les erreurs *différemment* en fonction de l’action MVC a été choisie.</span><span class="sxs-lookup"><span data-stu-id="21318-160">Prefer middleware for the general case, and use filters only where you need to do error handling *differently* based on which MVC action was chosen.</span></span>

### <a name="handling-model-state-errors"></a><span data-ttu-id="21318-161">État de modèle de gestion des erreurs</span><span class="sxs-lookup"><span data-stu-id="21318-161">Handling Model State Errors</span></span>

<span data-ttu-id="21318-162">[Validation des modèles](../mvc/models/validation.md) se produit avant chaque action du contrôleur qui est appelée, et il est responsable de la méthode d’action à inspecter `ModelState.IsValid` et réagir de façon appropriée.</span><span class="sxs-lookup"><span data-stu-id="21318-162">[Model validation](../mvc/models/validation.md) occurs prior to each controller action being invoked, and it is the action method’s responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span>

<span data-ttu-id="21318-163">Certaines applications choisit de suivre une convention standard pour traiter les erreurs de validation de modèle, auquel cas un [filtre](../mvc/controllers/filters.md) peut être un emplacement approprié pour implémenter une telle stratégie.</span><span class="sxs-lookup"><span data-stu-id="21318-163">Some apps will choose to follow a standard convention for dealing with model validation errors, in which case a [filter](../mvc/controllers/filters.md) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="21318-164">Vous devez tester le comportement de vos actions avec les États de modèles non valide.</span><span class="sxs-lookup"><span data-stu-id="21318-164">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="21318-165">Pour en savoir plus [logique du contrôleur de test](../mvc/controllers/testing.md).</span><span class="sxs-lookup"><span data-stu-id="21318-165">Learn more in [Testing controller logic](../mvc/controllers/testing.md).</span></span>




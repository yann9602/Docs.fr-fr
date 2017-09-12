---
title: Intergiciel (middleware) ASP.NET Core
author: rick-anderson
description: "Explique l’intergiciel (middleware) et le pipeline de requête."
keywords: "ASP.NET Core, intergiciel (middleware), pipeline, délégué"
ms.author: riande
manager: wpickett
ms.date: 08/14/2017
ms.topic: article
ms.assetid: db9a86ab-46c2-40e0-baed-86e38c16af1f
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/middleware
ms.openlocfilehash: 80e27c94b3c60a181c45f8e006126a7e7dd3d425
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="aspnet-core-middleware-fundamentals"></a><span data-ttu-id="5dddc-104">Notions de base ASP.NET Core intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="5dddc-104">ASP.NET Core Middleware Fundamentals</span></span>

<a name=fundamentals-middleware></a>

<span data-ttu-id="5dddc-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5dddc-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

[<span data-ttu-id="5dddc-106">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="5dddc-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)

## <a name="what-is-middleware"></a><span data-ttu-id="5dddc-107">Nouveautés d’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="5dddc-107">What is middleware</span></span>

<span data-ttu-id="5dddc-108">Intergiciel (middleware) est un logiciel qui est intégré à un pipeline d’application pour gérer les demandes et réponses.</span><span class="sxs-lookup"><span data-stu-id="5dddc-108">Middleware is software that is assembled into an application pipeline to handle requests and responses.</span></span> <span data-ttu-id="5dddc-109">Chaque composant :</span><span class="sxs-lookup"><span data-stu-id="5dddc-109">Each component:</span></span>

* <span data-ttu-id="5dddc-110">Peut décider de transmettre la demande au composant suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="5dddc-110">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="5dddc-111">Peut effectuer un travail avant et après que le composant suivant dans le pipeline est appelé.</span><span class="sxs-lookup"><span data-stu-id="5dddc-111">Can perform work before and after the next component in the pipeline is invoked.</span></span> 

<span data-ttu-id="5dddc-112">Les délégués de demande sont utilisés pour construire le pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="5dddc-112">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="5dddc-113">Les délégués de la demande gérer chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="5dddc-113">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="5dddc-114">Demander les délégués sont configurés à l’aide de [exécuter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [carte](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions), et [utilisation](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions) les méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="5dddc-114">Request delegates are configured using [Run](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [Map](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions), and [Use](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions) extension methods.</span></span> <span data-ttu-id="5dddc-115">Un délégué de demande individuelle peut être spécifié en ligne en tant qu’une méthode anonyme (appelée intergiciel (middleware) en ligne), ou elle peut être définie dans une classe réutilisable.</span><span class="sxs-lookup"><span data-stu-id="5dddc-115">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="5dddc-116">Ces classes réutilisables et des méthodes anonymes d’en ligne sont *intergiciel (middleware)*, ou *composants d’intergiciel (middleware)*.</span><span class="sxs-lookup"><span data-stu-id="5dddc-116">These reusable classes and in-line anonymous methods are *middleware*, or *middleware components*.</span></span> <span data-ttu-id="5dddc-117">Chaque composant d’intergiciel (middleware) dans le pipeline de demande est responsable de l’appel de composant suivant dans le pipeline ou la chaîne de court-circuit si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="5dddc-117">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline, or short-circuiting the chain if appropriate.</span></span>

<span data-ttu-id="5dddc-118">[Migration de Modules HTTP à un intergiciel (middleware)](../migration/http-modules.md) explique la différence entre la demande des pipelines dans ASP.NET Core et les versions précédentes et fournit d’autres exemples d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="5dddc-118">[Migrating HTTP Modules to Middleware](../migration/http-modules.md) explains the difference between request pipelines in ASP.NET Core and the previous versions and provides more middleware samples.</span></span>

## <a name="creating-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="5dddc-119">Création d’un pipeline de l’intergiciel (middleware) avec IApplicationBuilder</span><span class="sxs-lookup"><span data-stu-id="5dddc-119">Creating a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="5dddc-120">Le pipeline de demande ASP.NET Core se compose d’une séquence de délégués de la demande, appelé une après l’autre, comme le montre ce diagramme (le thread d’exécution suit les flèches noires) :</span><span class="sxs-lookup"><span data-stu-id="5dddc-120">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other, as this diagram shows (the thread of execution follows the black arrows):</span></span>

![Modèle de traitement de requête montrant une demande qui arrivent, via trois middlewares et la réponse en laissant l’application de traitement.](middleware/_static/request-delegate-pipeline.png)

<span data-ttu-id="5dddc-124">Chaque délégué peut effectuer des opérations avant et après le délégué suivant.</span><span class="sxs-lookup"><span data-stu-id="5dddc-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="5dddc-125">Un délégué peut également décider de ne pas transmettre une demande pour le délégué suivant, qui est appelé le pipeline de demande de court-circuit.</span><span class="sxs-lookup"><span data-stu-id="5dddc-125">A delegate can also decide to not pass a request to the next delegate, which is called short-circuiting the request pipeline.</span></span> <span data-ttu-id="5dddc-126">Court-circuit est souvent souhaitable car elle permet de travail inutile être évitée.</span><span class="sxs-lookup"><span data-stu-id="5dddc-126">Short-circuiting is often desirable because it allows unnecessary work to be avoided.</span></span> <span data-ttu-id="5dddc-127">Par exemple, l’intergiciel (middleware) de fichiers statiques peut retourner une demande pour un fichier statique et le reste du pipeline de court-circuit.</span><span class="sxs-lookup"><span data-stu-id="5dddc-127">For example, the static file middleware can return a request for a static file and short-circuit the rest of the pipeline.</span></span> <span data-ttu-id="5dddc-128">Gestion des exceptions délégués doivent être appelés tôt dans le pipeline, afin qu’ils peuvent intercepter les exceptions qui se produisent dans les phases ultérieures du pipeline.</span><span class="sxs-lookup"><span data-stu-id="5dddc-128">Exception-handling delegates need to be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="5dddc-129">L’application de ASP.NET Core la plus simple possible définit un délégué de requête unique qui gère toutes les demandes.</span><span class="sxs-lookup"><span data-stu-id="5dddc-129">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="5dddc-130">Ce cas n’inclut pas un pipeline de demande réelle.</span><span class="sxs-lookup"><span data-stu-id="5dddc-130">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="5dddc-131">Au lieu de cela, une fonction anonyme unique est appelée en réponse à chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="5dddc-131">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

<span data-ttu-id="5dddc-132">[!code-csharp[Main](middleware/sample/Middleware/Startup.cs)]</span><span class="sxs-lookup"><span data-stu-id="5dddc-132">[!code-csharp[Main](middleware/sample/Middleware/Startup.cs)]</span></span>

<span data-ttu-id="5dddc-133">Le premier [application. Exécutez](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) délégué termine le pipeline.</span><span class="sxs-lookup"><span data-stu-id="5dddc-133">The first [app.Run](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) delegate terminates the pipeline.</span></span>

<span data-ttu-id="5dddc-134">Vous pouvez chaîner plusieurs délégués demande avec [application. Utilisez](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions).</span><span class="sxs-lookup"><span data-stu-id="5dddc-134">You can chain multiple request delegates together with [app.Use](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions).</span></span> <span data-ttu-id="5dddc-135">Le `next` paramètre représente le délégué suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="5dddc-135">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="5dddc-136">(Souvenez-vous que vous pouvez court-circuit le pipeline par *pas* appelant le *suivant* paramètre.) Vous pouvez généralement effectuer actions avant et après le délégué suivant, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="5dddc-136">(Remember that you can short-circuit the pipeline by *not* calling the *next* parameter.) You can typically perform actions both before and after the next delegate, as this example demonstrates:</span></span>

<span data-ttu-id="5dddc-137">[!code-csharp[Main](middleware/sample/Chain/Startup.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="5dddc-137">[!code-csharp[Main](middleware/sample/Chain/Startup.cs?name=snippet1)]</span></span>

>[!WARNING]
> <span data-ttu-id="5dddc-138">N’appelez pas `next.Invoke` une fois que la réponse a été envoyée au client.</span><span class="sxs-lookup"><span data-stu-id="5dddc-138">Do not call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="5dddc-139">Modifications apportées à `HttpResponse` après le démarrage de la réponse lève une exception.</span><span class="sxs-lookup"><span data-stu-id="5dddc-139">Changes to `HttpResponse` after the response has started will throw an exception.</span></span> <span data-ttu-id="5dddc-140">Par exemple, les modifications telles que la définition des en-têtes, code d’état, etc., lève une exception.</span><span class="sxs-lookup"><span data-stu-id="5dddc-140">For example, changes such as setting headers, status code, etc,  will throw an exception.</span></span> <span data-ttu-id="5dddc-141">Écrire dans le corps de réponse après avoir appelé `next`:</span><span class="sxs-lookup"><span data-stu-id="5dddc-141">Writing to the response body after calling `next`:</span></span>
> - <span data-ttu-id="5dddc-142">Peut entraîner une violation du protocole.</span><span class="sxs-lookup"><span data-stu-id="5dddc-142">May cause a protocol violation.</span></span> <span data-ttu-id="5dddc-143">Par exemple, l’écriture de plus de la manière indiquée `content-length`.</span><span class="sxs-lookup"><span data-stu-id="5dddc-143">For example, writing more than the stated `content-length`.</span></span>
> - <span data-ttu-id="5dddc-144">Peut altérer le format du corps.</span><span class="sxs-lookup"><span data-stu-id="5dddc-144">May corrupt the body format.</span></span> <span data-ttu-id="5dddc-145">Par exemple, l’écriture d’un pied de page HTML dans un fichier CSS.</span><span class="sxs-lookup"><span data-stu-id="5dddc-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="5dddc-146">[HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) est un indicateur utile pour indiquer si les en-têtes ont été envoyés et/ou le corps a été écrit.</span><span class="sxs-lookup"><span data-stu-id="5dddc-146">[HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) is a useful hint to indicate if headers have been sent and/or the body has been written to.</span></span>

## <a name="ordering"></a><span data-ttu-id="5dddc-147">Classement</span><span class="sxs-lookup"><span data-stu-id="5dddc-147">Ordering</span></span>

<span data-ttu-id="5dddc-148">Composants d’intergiciel (middleware) sont ajoutés dans l’ordre de la `Configure` méthode définit l’ordre dans lequel ils sont appelés sur les demandes et l’ordre inverse pour la réponse.</span><span class="sxs-lookup"><span data-stu-id="5dddc-148">The order that middleware components are added in the `Configure` method defines the order in which they are invoked on requests, and the reverse order for the response.</span></span> <span data-ttu-id="5dddc-149">Ce classement est critique de sécurité, performances et fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="5dddc-149">This ordering is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="5dddc-150">La méthode de configuration (voir ci-dessous) ajoute les composants d’intergiciel (middleware) suivant :</span><span class="sxs-lookup"><span data-stu-id="5dddc-150">The Configure method (shown below) adds the following middleware components:</span></span>

1. <span data-ttu-id="5dddc-151">Gestion d’erreur d’exception</span><span class="sxs-lookup"><span data-stu-id="5dddc-151">Exception/error handling</span></span>
2. <span data-ttu-id="5dddc-152">Serveur de fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="5dddc-152">Static file server</span></span>
3. <span data-ttu-id="5dddc-153">Authentification</span><span class="sxs-lookup"><span data-stu-id="5dddc-153">Authentication</span></span>
4. <span data-ttu-id="5dddc-154">MVC</span><span class="sxs-lookup"><span data-stu-id="5dddc-154">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseIdentity();                     // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

<span data-ttu-id="5dddc-155">Dans le code ci-dessus, `UseExceptionHandler` est le premier composant d’intergiciel (middleware) ajouté au pipeline, par conséquent, elle intercepte toutes les exceptions qui se produisent dans les appels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="5dddc-155">In the code above, `UseExceptionHandler` is the first middleware component added to the pipeline—therefore, it catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="5dddc-156">L’intergiciel (middleware) fichier statique est appelée très tôt dans le pipeline afin de gérer les demandes et de court-circuit sans passer par les autres composants.</span><span class="sxs-lookup"><span data-stu-id="5dddc-156">The static file middleware is called early in the pipeline so it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="5dddc-157">Fournit de l’intergiciel (middleware) de fichiers statiques **aucune** vérifications d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="5dddc-157">The static file middleware provides **no** authorization checks.</span></span> <span data-ttu-id="5dddc-158">Tous les fichiers pris en charge par, notamment ceux de *wwwroot*, sont accessibles.</span><span class="sxs-lookup"><span data-stu-id="5dddc-158">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="5dddc-159">Consultez [utilisation des fichiers statiques](xref:fundamentals/static-files) pour une approche de sécuriser les fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="5dddc-159">See [Working with static files](xref:fundamentals/static-files) for an approach to secure static files.</span></span>

<span data-ttu-id="5dddc-160">Si la demande n’est pas gérée par l’intergiciel (middleware) fichier statique, il est transmis à l’intergiciel (middleware) identité (`app.UseIdentity`), qui effectue l’authentification.</span><span class="sxs-lookup"><span data-stu-id="5dddc-160">If the request is not handled by the static file middleware, it's passed on to the Identity middleware (`app.UseIdentity`), which performs authentication.</span></span> <span data-ttu-id="5dddc-161">Identité n’effectue pas de court-circuit les demandes non authentifiées.</span><span class="sxs-lookup"><span data-stu-id="5dddc-161">Identity does not short-circuit unauthenticated requests.</span></span> <span data-ttu-id="5dddc-162">Bien que l’identité authentifie les requêtes, d’autorisation (et rejet) se produit uniquement après que MVC sélectionne un contrôleur spécifique et une action.</span><span class="sxs-lookup"><span data-stu-id="5dddc-162">Although Identity authenticates requests,  authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

<span data-ttu-id="5dddc-163">L’exemple suivant montre un intergiciel (middleware) classement où les demandes de fichiers statiques sont gérées par l’intergiciel (middleware) de fichiers statiques avant l’intergiciel de compression de la réponse.</span><span class="sxs-lookup"><span data-stu-id="5dddc-163">The following example demonstrates a middleware ordering where requests for static files are handled by the static file middleware before the response compression middleware.</span></span> <span data-ttu-id="5dddc-164">Fichiers statiques ne sont pas compressés avec ce classement de l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="5dddc-164">Static files are not compressed with this ordering of the middleware.</span></span> <span data-ttu-id="5dddc-165">Les réponses MVC de [UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) peuvent être compressées.</span><span class="sxs-lookup"><span data-stu-id="5dddc-165">The MVC responses from [UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseStaticFiles();         // Static files not compressed
                                  // by middleware.
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

<a name=middleware-run-map-use></a>

### <a name="use-run-and-map"></a><span data-ttu-id="5dddc-166">Utilisez, exécuter et carte</span><span class="sxs-lookup"><span data-stu-id="5dddc-166">Use, Run, and Map</span></span>

<span data-ttu-id="5dddc-167">Vous configurez le pipeline HTTP à l’aide `Use`, `Run`, et `Map`.</span><span class="sxs-lookup"><span data-stu-id="5dddc-167">You configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="5dddc-168">Le `Use` méthode pouvez court-circuit le pipeline (autrement dit, si elle n’appelle pas une `next` délégué de la demande).</span><span class="sxs-lookup"><span data-stu-id="5dddc-168">The `Use` method can short-circuit the pipeline (that is, if it does not call a `next` request delegate).</span></span> <span data-ttu-id="5dddc-169">`Run`est une convention, et certains composants d’intergiciel (middleware) peuvent exposer `Run[Middleware]` méthodes qui s’exécutent à la fin du pipeline.</span><span class="sxs-lookup"><span data-stu-id="5dddc-169">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="5dddc-170">`Map*`les extensions sont utilisées comme une convention pour créer une branche le pipeline.</span><span class="sxs-lookup"><span data-stu-id="5dddc-170">`Map*` extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="5dddc-171">[Carte](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) branches le pipeline de requête en fonction des correspondances de chemin de la demande donnée.</span><span class="sxs-lookup"><span data-stu-id="5dddc-171">[Map](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="5dddc-172">Si le chemin d’accès de requête commence par le chemin d’accès donné, la branche est exécutée.</span><span class="sxs-lookup"><span data-stu-id="5dddc-172">If the request path starts with the given path, the branch is executed.</span></span>

<span data-ttu-id="5dddc-173">[!code-csharp[Main](middleware/sample/Chain/StartupMap.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="5dddc-173">[!code-csharp[Main](middleware/sample/Chain/StartupMap.cs?name=snippet1)]</span></span>

<span data-ttu-id="5dddc-174">Le tableau suivant présente les demandes et réponses de `http://localhost:1234` en utilisant le code précédent :</span><span class="sxs-lookup"><span data-stu-id="5dddc-174">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="5dddc-175">Requête</span><span class="sxs-lookup"><span data-stu-id="5dddc-175">Request</span></span> | <span data-ttu-id="5dddc-176">Réponse</span><span class="sxs-lookup"><span data-stu-id="5dddc-176">Response</span></span> |
| --- | --- |
| <span data-ttu-id="5dddc-177">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="5dddc-177">localhost:1234</span></span> | <span data-ttu-id="5dddc-178">Bonjour à partir de la table non délégué.</span><span class="sxs-lookup"><span data-stu-id="5dddc-178">Hello from non-Map delegate.</span></span>  |
| <span data-ttu-id="5dddc-179">localhost:1234 / map1</span><span class="sxs-lookup"><span data-stu-id="5dddc-179">localhost:1234/map1</span></span> | <span data-ttu-id="5dddc-180">Test de mappage 1</span><span class="sxs-lookup"><span data-stu-id="5dddc-180">Map Test 1</span></span> |
| <span data-ttu-id="5dddc-181">localhost:1234 / map2</span><span class="sxs-lookup"><span data-stu-id="5dddc-181">localhost:1234/map2</span></span> | <span data-ttu-id="5dddc-182">Test de mappage 2</span><span class="sxs-lookup"><span data-stu-id="5dddc-182">Map Test 2</span></span> |
| <span data-ttu-id="5dddc-183">localhost:1234 / map3</span><span class="sxs-lookup"><span data-stu-id="5dddc-183">localhost:1234/map3</span></span> | <span data-ttu-id="5dddc-184">Bonjour à partir de la table non délégué.</span><span class="sxs-lookup"><span data-stu-id="5dddc-184">Hello from non-Map delegate.</span></span>  |

<span data-ttu-id="5dddc-185">Lorsque `Map` est utilisé, les segments de chemin d’accès de mise en correspondance sont supprimés de `HttpRequest.Path` et ajouté à `HttpRequest.PathBase` pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="5dddc-185">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="5dddc-186">[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) branches le pipeline de requête en fonction du résultat du prédicat donné.</span><span class="sxs-lookup"><span data-stu-id="5dddc-186">[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="5dddc-187">Un prédicat de type `Func<HttpContext, bool>` peut être utilisé pour mapper les requêtes avec une nouvelle branche du pipeline.</span><span class="sxs-lookup"><span data-stu-id="5dddc-187">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="5dddc-188">Dans l’exemple suivant, un prédicat est utilisé pour détecter la présence d’une variable de chaîne de requête `branch`:</span><span class="sxs-lookup"><span data-stu-id="5dddc-188">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

<span data-ttu-id="5dddc-189">[!code-csharp[Main](middleware/sample/Chain/StartupMapWhen.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="5dddc-189">[!code-csharp[Main](middleware/sample/Chain/StartupMapWhen.cs?name=snippet1)]</span></span>

<span data-ttu-id="5dddc-190">Le tableau suivant présente les demandes et réponses de `http://localhost:1234` en utilisant le code précédent :</span><span class="sxs-lookup"><span data-stu-id="5dddc-190">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="5dddc-191">Requête</span><span class="sxs-lookup"><span data-stu-id="5dddc-191">Request</span></span> | <span data-ttu-id="5dddc-192">Réponse</span><span class="sxs-lookup"><span data-stu-id="5dddc-192">Response</span></span> |
| --- | --- |
| <span data-ttu-id="5dddc-193">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="5dddc-193">localhost:1234</span></span> | <span data-ttu-id="5dddc-194">Bonjour à partir de la table non délégué.</span><span class="sxs-lookup"><span data-stu-id="5dddc-194">Hello from non-Map delegate.</span></span>  |
| <span data-ttu-id="5dddc-195">localhost:1234 / ? branche = principale</span><span class="sxs-lookup"><span data-stu-id="5dddc-195">localhost:1234/?branch=master</span></span> | <span data-ttu-id="5dddc-196">Branche utilisé = principale</span><span class="sxs-lookup"><span data-stu-id="5dddc-196">Branch used = master</span></span>|

<span data-ttu-id="5dddc-197">`Map`prend en charge l’imbrication, par exemple :</span><span class="sxs-lookup"><span data-stu-id="5dddc-197">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
       level1App.Map("/level2a", level2AApp => {
           // "/level1/level2a"
           //...
       });
       level1App.Map("/level2b", level2BApp => {
           // "/level1/level2b"
           //...
       });
   });
   ```

<span data-ttu-id="5dddc-198">`Map`peut également correspondre à plusieurs segments à la fois, par exemple :</span><span class="sxs-lookup"><span data-stu-id="5dddc-198">`Map` can also match multiple segments at once, for example:</span></span>

 ```csharp
app.Map("/level1/level2", HandleMultiSeg);
```

## <a name="built-in-middleware"></a><span data-ttu-id="5dddc-199">Intergiciel (middleware) intégré</span><span class="sxs-lookup"><span data-stu-id="5dddc-199">Built-in middleware</span></span>

<span data-ttu-id="5dddc-200">ASP.NET Core est fourni avec les composants d’intergiciel (middleware) suivant :</span><span class="sxs-lookup"><span data-stu-id="5dddc-200">ASP.NET Core ships with the following middleware components:</span></span>

| <span data-ttu-id="5dddc-201">Intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="5dddc-201">Middleware</span></span> | <span data-ttu-id="5dddc-202">Description</span><span class="sxs-lookup"><span data-stu-id="5dddc-202">Description</span></span> |
| ----- | ------- |
| [<span data-ttu-id="5dddc-203">Authentification</span><span class="sxs-lookup"><span data-stu-id="5dddc-203">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="5dddc-204">Fournit la prise en charge de l’authentification.</span><span class="sxs-lookup"><span data-stu-id="5dddc-204">Provides authentication support.</span></span> |
| [<span data-ttu-id="5dddc-205">CORS</span><span class="sxs-lookup"><span data-stu-id="5dddc-205">CORS</span></span>](xref:security/cors) | <span data-ttu-id="5dddc-206">Configure le partage de ressources Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="5dddc-206">Configures Cross-Origin Resource Sharing.</span></span> |
| [<span data-ttu-id="5dddc-207">Mise en cache des réponses</span><span class="sxs-lookup"><span data-stu-id="5dddc-207">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="5dddc-208">Prend en charge la mise en cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="5dddc-208">Provides support for caching responses.</span></span> |
| [<span data-ttu-id="5dddc-209">Compression de la réponse</span><span class="sxs-lookup"><span data-stu-id="5dddc-209">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="5dddc-210">Prend en charge la compression des réponses.</span><span class="sxs-lookup"><span data-stu-id="5dddc-210">Provides support for compressing responses.</span></span> |
| [<span data-ttu-id="5dddc-211">Routage</span><span class="sxs-lookup"><span data-stu-id="5dddc-211">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="5dddc-212">Définit et contraint les itinéraires de la demande.</span><span class="sxs-lookup"><span data-stu-id="5dddc-212">Defines and constrains request routes.</span></span> |
| [<span data-ttu-id="5dddc-213">Session</span><span class="sxs-lookup"><span data-stu-id="5dddc-213">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="5dddc-214">Prend en charge la gestion des sessions utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5dddc-214">Provides support for managing user sessions.</span></span> |
| [<span data-ttu-id="5dddc-215">Fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="5dddc-215">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="5dddc-216">Fournit la prise en charge pour traiter les fichiers statiques et l’exploration des répertoires.</span><span class="sxs-lookup"><span data-stu-id="5dddc-216">Provides support for serving static files and directory browsing.</span></span> |
| [<span data-ttu-id="5dddc-217">Intergiciel (middleware) de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="5dddc-217">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="5dddc-218">Prend en charge la réécriture d’URL et la redirection des demandes.</span><span class="sxs-lookup"><span data-stu-id="5dddc-218">Provides support for rewriting URLs and redirecting requests.</span></span> |

<a name=middleware-writing-middleware></a>

## <a name="writing-middleware"></a><span data-ttu-id="5dddc-219">Intergiciel (middleware) de l’écriture</span><span class="sxs-lookup"><span data-stu-id="5dddc-219">Writing middleware</span></span>

<span data-ttu-id="5dddc-220">Intergiciel (middleware) est généralement encapsulé dans une classe et exposée avec une méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="5dddc-220">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="5dddc-221">Prenez en compte l’intergiciel (middleware) suivant, qui définit la culture pour la requête actuelle à partir de la chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="5dddc-221">Consider the following middleware, which sets the culture for the current request from the query string:</span></span>

<span data-ttu-id="5dddc-222">[!code-csharp[Main](middleware/sample/Culture/StartupCulture.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="5dddc-222">[!code-csharp[Main](middleware/sample/Culture/StartupCulture.cs?name=snippet1)]</span></span>

<span data-ttu-id="5dddc-223">Remarque : L’exemple de code ci-dessus est utilisé pour illustrer la création d’un composant d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="5dddc-223">Note: The sample code above is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="5dddc-224">Consultez [ globalisation et localisation](xref:fundamentals/localization) pour la prise en charge de la localisation intégré d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5dddc-224">See [ Globalization and localization](xref:fundamentals/localization) for ASP.NET Core's built-in localization support.</span></span>

<span data-ttu-id="5dddc-225">Vous pouvez tester l’intergiciel (middleware) en passant dans la culture, par exemple `http://localhost:7997/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="5dddc-225">You can test the middleware by passing in the culture, for example `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="5dddc-226">Le code suivant déplace le délégué de l’intergiciel (middleware) à une classe :</span><span class="sxs-lookup"><span data-stu-id="5dddc-226">The following code moves the middleware delegate to a class:</span></span>

<span data-ttu-id="5dddc-227">[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddleware.cs)]</span><span class="sxs-lookup"><span data-stu-id="5dddc-227">[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddleware.cs)]</span></span>

<span data-ttu-id="5dddc-228">La méthode d’extension suivant expose l’intergiciel (middleware) via [IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):</span><span class="sxs-lookup"><span data-stu-id="5dddc-228">The following extension method exposes the middleware through [IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):</span></span>

<span data-ttu-id="5dddc-229">[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddlewareExtensions.cs)]</span><span class="sxs-lookup"><span data-stu-id="5dddc-229">[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddlewareExtensions.cs)]</span></span>

<span data-ttu-id="5dddc-230">Le code suivant appelle l’intergiciel (middleware) à partir de `Configure`:</span><span class="sxs-lookup"><span data-stu-id="5dddc-230">The following code calls the middleware from `Configure`:</span></span>

<span data-ttu-id="5dddc-231">[!code-csharp[Main](middleware/sample/Culture/Startup.cs?name=snippet1&highlight=5)]</span><span class="sxs-lookup"><span data-stu-id="5dddc-231">[!code-csharp[Main](middleware/sample/Culture/Startup.cs?name=snippet1&highlight=5)]</span></span>

<span data-ttu-id="5dddc-232">Intergiciel (middleware) doit suivre le [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/) en exposant ses dépendances dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="5dddc-232">Middleware should follow the [Explicit Dependencies Principle](http://deviq.com/explicit-dependencies-principle/) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="5dddc-233">Intergiciel (middleware) est construit en une seule fois par *durée de vie application*.</span><span class="sxs-lookup"><span data-stu-id="5dddc-233">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="5dddc-234">Consultez *dépendances par demande* ci-dessous si vous avez besoin de partager des services avec un intergiciel (middleware) au sein d’une requête.</span><span class="sxs-lookup"><span data-stu-id="5dddc-234">See *Per-request dependencies* below if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="5dddc-235">Composants d’intergiciel (middleware) peuvent résoudre leurs dépendances à partir de l’injection de dépendances via les paramètres du constructeur.</span><span class="sxs-lookup"><span data-stu-id="5dddc-235">Middleware components can resolve their dependencies from dependency injection through constructor parameters.</span></span> <span data-ttu-id="5dddc-236">[`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary)peut également accepter des paramètres supplémentaires directement.</span><span class="sxs-lookup"><span data-stu-id="5dddc-236">[`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary) can also accept additional parameters directly.</span></span>

### <a name="per-request-dependencies"></a><span data-ttu-id="5dddc-237">Dépendances par demande</span><span class="sxs-lookup"><span data-stu-id="5dddc-237">Per-request dependencies</span></span>

<span data-ttu-id="5dddc-238">Étant donné que l’intergiciel (middleware) est créée au démarrage de l’application, pas à la demande, *étendue* les services de durée de vie utilisés par les constructeurs de l’intergiciel (middleware) ne sont pas partagés avec d’autres types d’injection de dépendance lors de chaque demande.</span><span class="sxs-lookup"><span data-stu-id="5dddc-238">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors are not  shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="5dddc-239">Si vous devez partager une *étendue* entre votre intergiciel (middleware) et d’autres types de service, ajoutez ces services pour le `Invoke` signature de la méthode.</span><span class="sxs-lookup"><span data-stu-id="5dddc-239">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="5dddc-240">Le `Invoke` méthode peut accepter des paramètres supplémentaires sont remplies par injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="5dddc-240">The `Invoke` method can accept additional parameters that are populated by dependency injection.</span></span> <span data-ttu-id="5dddc-241">Exemple :</span><span class="sxs-lookup"><span data-stu-id="5dddc-241">For example:</span></span>

```c#
public class MyMiddleware
{
    private readonly RequestDelegate _next;

    public MyMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="resources"></a><span data-ttu-id="5dddc-242">Ressources</span><span class="sxs-lookup"><span data-stu-id="5dddc-242">Resources</span></span>

* [<span data-ttu-id="5dddc-243">Exemple de code utilisé dans ce document.</span><span class="sxs-lookup"><span data-stu-id="5dddc-243">Sample code used in this doc</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)
* [<span data-ttu-id="5dddc-244">Migration des modules HTTP vers les intergiciels (middleware)</span><span class="sxs-lookup"><span data-stu-id="5dddc-244">Migrating HTTP Modules to Middleware</span></span>](../migration/http-modules.md)
* [<span data-ttu-id="5dddc-245">Démarrage d’une application</span><span class="sxs-lookup"><span data-stu-id="5dddc-245">Application Startup</span></span>](startup.md)
* [<span data-ttu-id="5dddc-246">Fonctionnalités de requête</span><span class="sxs-lookup"><span data-stu-id="5dddc-246">Request Features</span></span>](request-features.md)

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
ms.openlocfilehash: 881cabdbb7814b36d97a977b30389506b99d16b9
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="aspnet-core-middleware-fundamentals"></a><span data-ttu-id="fff75-104">Notions de base ASP.NET Core intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="fff75-104">ASP.NET Core Middleware Fundamentals</span></span>

<a name=fundamentals-middleware></a>

<span data-ttu-id="fff75-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="fff75-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

[<span data-ttu-id="fff75-106">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="fff75-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)

## <a name="what-is-middleware"></a><span data-ttu-id="fff75-107">Nouveautés d’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="fff75-107">What is middleware</span></span>

<span data-ttu-id="fff75-108">Intergiciel (middleware) est un logiciel qui est intégré à un pipeline d’application pour gérer les demandes et réponses.</span><span class="sxs-lookup"><span data-stu-id="fff75-108">Middleware is software that is assembled into an application pipeline to handle requests and responses.</span></span> <span data-ttu-id="fff75-109">Chaque composant :</span><span class="sxs-lookup"><span data-stu-id="fff75-109">Each component:</span></span>

* <span data-ttu-id="fff75-110">Peut décider de transmettre la demande au composant suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="fff75-110">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="fff75-111">Peut effectuer un travail avant et après que le composant suivant dans le pipeline est appelé.</span><span class="sxs-lookup"><span data-stu-id="fff75-111">Can perform work before and after the next component in the pipeline is invoked.</span></span> 

<span data-ttu-id="fff75-112">Les délégués de demande sont utilisés pour construire le pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="fff75-112">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="fff75-113">Les délégués de la demande gérer chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="fff75-113">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="fff75-114">Demander les délégués sont configurés à l’aide de [exécuter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [carte](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions), et [utilisation](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions) les méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="fff75-114">Request delegates are configured using [Run](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [Map](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions), and [Use](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions) extension methods.</span></span> <span data-ttu-id="fff75-115">Un délégué de demande individuelle peut être spécifié en ligne en tant qu’une méthode anonyme (appelée intergiciel (middleware) en ligne), ou elle peut être définie dans une classe réutilisable.</span><span class="sxs-lookup"><span data-stu-id="fff75-115">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="fff75-116">Ces classes réutilisables et des méthodes anonymes d’en ligne sont *intergiciel (middleware)*, ou *composants d’intergiciel (middleware)*.</span><span class="sxs-lookup"><span data-stu-id="fff75-116">These reusable classes and in-line anonymous methods are *middleware*, or *middleware components*.</span></span> <span data-ttu-id="fff75-117">Chaque composant d’intergiciel (middleware) dans le pipeline de demande est responsable de l’appel de composant suivant dans le pipeline ou la chaîne de court-circuit si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="fff75-117">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline, or short-circuiting the chain if appropriate.</span></span>

<span data-ttu-id="fff75-118">[Migration de Modules HTTP à un intergiciel (middleware)](../migration/http-modules.md) explique la différence entre la demande des pipelines dans ASP.NET Core et les versions précédentes et fournit d’autres exemples d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="fff75-118">[Migrating HTTP Modules to Middleware](../migration/http-modules.md) explains the difference between request pipelines in ASP.NET Core and the previous versions and provides more middleware samples.</span></span>

## <a name="creating-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="fff75-119">Création d’un pipeline de l’intergiciel (middleware) avec IApplicationBuilder</span><span class="sxs-lookup"><span data-stu-id="fff75-119">Creating a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="fff75-120">Le pipeline de demande ASP.NET Core se compose d’une séquence de délégués de la demande, appelé une après l’autre, comme le montre ce diagramme (le thread d’exécution suit les flèches noires) :</span><span class="sxs-lookup"><span data-stu-id="fff75-120">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other, as this diagram shows (the thread of execution follows the black arrows):</span></span>

![Modèle de traitement de requête montrant une demande qui arrivent, via trois middlewares et la réponse en laissant l’application de traitement.](middleware/_static/request-delegate-pipeline.png)

<span data-ttu-id="fff75-124">Chaque délégué peut effectuer des opérations avant et après le délégué suivant.</span><span class="sxs-lookup"><span data-stu-id="fff75-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="fff75-125">Un délégué peut également décider de ne pas transmettre une demande pour le délégué suivant, qui est appelé le pipeline de demande de court-circuit.</span><span class="sxs-lookup"><span data-stu-id="fff75-125">A delegate can also decide to not pass a request to the next delegate, which is called short-circuiting the request pipeline.</span></span> <span data-ttu-id="fff75-126">Court-circuit est souvent souhaitable car elle évite le travail inutile.</span><span class="sxs-lookup"><span data-stu-id="fff75-126">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="fff75-127">Par exemple, l’intergiciel (middleware) de fichiers statiques peut retourner une demande pour un fichier statique et le reste du pipeline de court-circuit.</span><span class="sxs-lookup"><span data-stu-id="fff75-127">For example, the static file middleware can return a request for a static file and short-circuit the rest of the pipeline.</span></span> <span data-ttu-id="fff75-128">Gestion des exceptions délégués doivent être appelés tôt dans le pipeline, afin qu’ils peuvent intercepter les exceptions qui se produisent dans les phases ultérieures du pipeline.</span><span class="sxs-lookup"><span data-stu-id="fff75-128">Exception-handling delegates need to be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="fff75-129">L’application de ASP.NET Core la plus simple possible définit un délégué de requête unique qui gère toutes les demandes.</span><span class="sxs-lookup"><span data-stu-id="fff75-129">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="fff75-130">Ce cas n’inclut pas un pipeline de demande réelle.</span><span class="sxs-lookup"><span data-stu-id="fff75-130">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="fff75-131">Au lieu de cela, une fonction anonyme unique est appelée en réponse à chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="fff75-131">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[Main](middleware/sample/Middleware/Startup.cs)]

<span data-ttu-id="fff75-132">Le premier [application. Exécutez](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) délégué termine le pipeline.</span><span class="sxs-lookup"><span data-stu-id="fff75-132">The first [app.Run](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) delegate terminates the pipeline.</span></span>

<span data-ttu-id="fff75-133">Vous pouvez chaîner plusieurs délégués demande avec [application. Utilisez](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions).</span><span class="sxs-lookup"><span data-stu-id="fff75-133">You can chain multiple request delegates together with [app.Use](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions).</span></span> <span data-ttu-id="fff75-134">Le `next` paramètre représente le délégué suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="fff75-134">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="fff75-135">(Souvenez-vous que vous pouvez court-circuit le pipeline par *pas* appelant le *suivant* paramètre.) Vous pouvez généralement effectuer actions avant et après le délégué suivant, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="fff75-135">(Remember that you can short-circuit the pipeline by *not* calling the *next* parameter.) You can typically perform actions both before and after the next delegate, as this example demonstrates:</span></span>

[!code-csharp[Main](middleware/sample/Chain/Startup.cs?name=snippet1)]

>[!WARNING]
> <span data-ttu-id="fff75-136">N’appelez pas `next.Invoke` une fois que la réponse a été envoyée au client.</span><span class="sxs-lookup"><span data-stu-id="fff75-136">Do not call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="fff75-137">Modifications apportées à `HttpResponse` après le démarrage de la réponse lève une exception.</span><span class="sxs-lookup"><span data-stu-id="fff75-137">Changes to `HttpResponse` after the response has started will throw an exception.</span></span> <span data-ttu-id="fff75-138">Par exemple, les modifications telles que la définition des en-têtes, code d’état, etc., lève une exception.</span><span class="sxs-lookup"><span data-stu-id="fff75-138">For example, changes such as setting headers, status code, etc,  will throw an exception.</span></span> <span data-ttu-id="fff75-139">Écrire dans le corps de réponse après avoir appelé `next`:</span><span class="sxs-lookup"><span data-stu-id="fff75-139">Writing to the response body after calling `next`:</span></span>
> - <span data-ttu-id="fff75-140">Peut entraîner une violation du protocole.</span><span class="sxs-lookup"><span data-stu-id="fff75-140">May cause a protocol violation.</span></span> <span data-ttu-id="fff75-141">Par exemple, l’écriture de plus de la manière indiquée `content-length`.</span><span class="sxs-lookup"><span data-stu-id="fff75-141">For example, writing more than the stated `content-length`.</span></span>
> - <span data-ttu-id="fff75-142">Peut altérer le format du corps.</span><span class="sxs-lookup"><span data-stu-id="fff75-142">May corrupt the body format.</span></span> <span data-ttu-id="fff75-143">Par exemple, l’écriture d’un pied de page HTML dans un fichier CSS.</span><span class="sxs-lookup"><span data-stu-id="fff75-143">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="fff75-144">[HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) est un indicateur utile pour indiquer si les en-têtes ont été envoyés et/ou le corps a été écrit.</span><span class="sxs-lookup"><span data-stu-id="fff75-144">[HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) is a useful hint to indicate if headers have been sent and/or the body has been written to.</span></span>

## <a name="ordering"></a><span data-ttu-id="fff75-145">Classement</span><span class="sxs-lookup"><span data-stu-id="fff75-145">Ordering</span></span>

<span data-ttu-id="fff75-146">Composants d’intergiciel (middleware) sont ajoutés dans l’ordre de la `Configure` méthode définit l’ordre dans lequel ils sont appelés sur les demandes et l’ordre inverse pour la réponse.</span><span class="sxs-lookup"><span data-stu-id="fff75-146">The order that middleware components are added in the `Configure` method defines the order in which they are invoked on requests, and the reverse order for the response.</span></span> <span data-ttu-id="fff75-147">Ce classement est critique de sécurité, performances et fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="fff75-147">This ordering is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="fff75-148">La méthode de configuration (voir ci-dessous) ajoute les composants d’intergiciel (middleware) suivant :</span><span class="sxs-lookup"><span data-stu-id="fff75-148">The Configure method (shown below) adds the following middleware components:</span></span>

1. <span data-ttu-id="fff75-149">Gestion d’erreur d’exception</span><span class="sxs-lookup"><span data-stu-id="fff75-149">Exception/error handling</span></span>
2. <span data-ttu-id="fff75-150">Serveur de fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="fff75-150">Static file server</span></span>
3. <span data-ttu-id="fff75-151">Authentification</span><span class="sxs-lookup"><span data-stu-id="fff75-151">Authentication</span></span>
4. <span data-ttu-id="fff75-152">MVC</span><span class="sxs-lookup"><span data-stu-id="fff75-152">MVC</span></span>

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

<span data-ttu-id="fff75-153">Dans le code ci-dessus, `UseExceptionHandler` est le premier composant d’intergiciel (middleware) ajouté au pipeline, par conséquent, elle intercepte toutes les exceptions qui se produisent dans les appels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="fff75-153">In the code above, `UseExceptionHandler` is the first middleware component added to the pipeline—therefore, it catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="fff75-154">L’intergiciel (middleware) fichier statique est appelée très tôt dans le pipeline afin de gérer les demandes et de court-circuit sans passer par les autres composants.</span><span class="sxs-lookup"><span data-stu-id="fff75-154">The static file middleware is called early in the pipeline so it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="fff75-155">Fournit de l’intergiciel (middleware) de fichiers statiques **aucune** vérifications d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="fff75-155">The static file middleware provides **no** authorization checks.</span></span> <span data-ttu-id="fff75-156">Tous les fichiers pris en charge par, notamment ceux de *wwwroot*, sont accessibles.</span><span class="sxs-lookup"><span data-stu-id="fff75-156">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="fff75-157">Consultez [utilisation des fichiers statiques](xref:fundamentals/static-files) pour une approche de sécuriser les fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="fff75-157">See [Working with static files](xref:fundamentals/static-files) for an approach to secure static files.</span></span>

<span data-ttu-id="fff75-158">Si la demande n’est pas gérée par l’intergiciel (middleware) fichier statique, il est transmis à l’intergiciel (middleware) identité (`app.UseIdentity`), qui effectue l’authentification.</span><span class="sxs-lookup"><span data-stu-id="fff75-158">If the request is not handled by the static file middleware, it's passed on to the Identity middleware (`app.UseIdentity`), which performs authentication.</span></span> <span data-ttu-id="fff75-159">Identité n’effectue pas de court-circuit les demandes non authentifiées.</span><span class="sxs-lookup"><span data-stu-id="fff75-159">Identity does not short-circuit unauthenticated requests.</span></span> <span data-ttu-id="fff75-160">Bien que l’identité authentifie les requêtes, d’autorisation (et rejet) se produit uniquement après que MVC sélectionne un contrôleur spécifique et une action.</span><span class="sxs-lookup"><span data-stu-id="fff75-160">Although Identity authenticates requests,  authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

<span data-ttu-id="fff75-161">L’exemple suivant montre un intergiciel (middleware) classement où les demandes de fichiers statiques sont gérées par l’intergiciel (middleware) de fichiers statiques avant l’intergiciel de compression de la réponse.</span><span class="sxs-lookup"><span data-stu-id="fff75-161">The following example demonstrates a middleware ordering where requests for static files are handled by the static file middleware before the response compression middleware.</span></span> <span data-ttu-id="fff75-162">Fichiers statiques ne sont pas compressés avec ce classement de l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="fff75-162">Static files are not compressed with this ordering of the middleware.</span></span> <span data-ttu-id="fff75-163">Les réponses MVC de [UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) peuvent être compressées.</span><span class="sxs-lookup"><span data-stu-id="fff75-163">The MVC responses from [UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) can be compressed.</span></span>

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

### <a name="use-run-and-map"></a><span data-ttu-id="fff75-164">Utilisez, exécuter et carte</span><span class="sxs-lookup"><span data-stu-id="fff75-164">Use, Run, and Map</span></span>

<span data-ttu-id="fff75-165">Vous configurez le pipeline HTTP à l’aide `Use`, `Run`, et `Map`.</span><span class="sxs-lookup"><span data-stu-id="fff75-165">You configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="fff75-166">Le `Use` méthode pouvez court-circuit le pipeline (autrement dit, si elle n’appelle pas une `next` délégué de la demande).</span><span class="sxs-lookup"><span data-stu-id="fff75-166">The `Use` method can short-circuit the pipeline (that is, if it does not call a `next` request delegate).</span></span> <span data-ttu-id="fff75-167">`Run`est une convention, et certains composants d’intergiciel (middleware) peuvent exposer `Run[Middleware]` méthodes qui s’exécutent à la fin du pipeline.</span><span class="sxs-lookup"><span data-stu-id="fff75-167">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="fff75-168">`Map*`les extensions sont utilisées comme une convention pour créer une branche le pipeline.</span><span class="sxs-lookup"><span data-stu-id="fff75-168">`Map*` extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="fff75-169">[Carte](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) branches le pipeline de requête en fonction des correspondances de chemin de la demande donnée.</span><span class="sxs-lookup"><span data-stu-id="fff75-169">[Map](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="fff75-170">Si le chemin d’accès de requête commence par le chemin d’accès donné, la branche est exécutée.</span><span class="sxs-lookup"><span data-stu-id="fff75-170">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[Main](middleware/sample/Chain/StartupMap.cs?name=snippet1)]

<span data-ttu-id="fff75-171">Le tableau suivant présente les demandes et réponses de `http://localhost:1234` en utilisant le code précédent :</span><span class="sxs-lookup"><span data-stu-id="fff75-171">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="fff75-172">Requête</span><span class="sxs-lookup"><span data-stu-id="fff75-172">Request</span></span> | <span data-ttu-id="fff75-173">Réponse</span><span class="sxs-lookup"><span data-stu-id="fff75-173">Response</span></span> |
| --- | --- |
| <span data-ttu-id="fff75-174">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="fff75-174">localhost:1234</span></span> | <span data-ttu-id="fff75-175">Bonjour à partir de la table non délégué.</span><span class="sxs-lookup"><span data-stu-id="fff75-175">Hello from non-Map delegate.</span></span>  |
| <span data-ttu-id="fff75-176">localhost:1234 / map1</span><span class="sxs-lookup"><span data-stu-id="fff75-176">localhost:1234/map1</span></span> | <span data-ttu-id="fff75-177">Test de mappage 1</span><span class="sxs-lookup"><span data-stu-id="fff75-177">Map Test 1</span></span> |
| <span data-ttu-id="fff75-178">localhost:1234 / map2</span><span class="sxs-lookup"><span data-stu-id="fff75-178">localhost:1234/map2</span></span> | <span data-ttu-id="fff75-179">Test de mappage 2</span><span class="sxs-lookup"><span data-stu-id="fff75-179">Map Test 2</span></span> |
| <span data-ttu-id="fff75-180">localhost:1234 / map3</span><span class="sxs-lookup"><span data-stu-id="fff75-180">localhost:1234/map3</span></span> | <span data-ttu-id="fff75-181">Bonjour à partir de la table non délégué.</span><span class="sxs-lookup"><span data-stu-id="fff75-181">Hello from non-Map delegate.</span></span>  |

<span data-ttu-id="fff75-182">Lorsque `Map` est utilisé, les segments de chemin d’accès de mise en correspondance sont supprimés de `HttpRequest.Path` et ajouté à `HttpRequest.PathBase` pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="fff75-182">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="fff75-183">[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) branches le pipeline de requête en fonction du résultat du prédicat donné.</span><span class="sxs-lookup"><span data-stu-id="fff75-183">[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="fff75-184">Un prédicat de type `Func<HttpContext, bool>` peut être utilisé pour mapper les requêtes avec une nouvelle branche du pipeline.</span><span class="sxs-lookup"><span data-stu-id="fff75-184">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="fff75-185">Dans l’exemple suivant, un prédicat est utilisé pour détecter la présence d’une variable de chaîne de requête `branch`:</span><span class="sxs-lookup"><span data-stu-id="fff75-185">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[Main](middleware/sample/Chain/StartupMapWhen.cs?name=snippet1)]

<span data-ttu-id="fff75-186">Le tableau suivant présente les demandes et réponses de `http://localhost:1234` en utilisant le code précédent :</span><span class="sxs-lookup"><span data-stu-id="fff75-186">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="fff75-187">Requête</span><span class="sxs-lookup"><span data-stu-id="fff75-187">Request</span></span> | <span data-ttu-id="fff75-188">Réponse</span><span class="sxs-lookup"><span data-stu-id="fff75-188">Response</span></span> |
| --- | --- |
| <span data-ttu-id="fff75-189">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="fff75-189">localhost:1234</span></span> | <span data-ttu-id="fff75-190">Bonjour à partir de la table non délégué.</span><span class="sxs-lookup"><span data-stu-id="fff75-190">Hello from non-Map delegate.</span></span>  |
| <span data-ttu-id="fff75-191">localhost:1234 / ? branche = principale</span><span class="sxs-lookup"><span data-stu-id="fff75-191">localhost:1234/?branch=master</span></span> | <span data-ttu-id="fff75-192">Branche utilisé = principale</span><span class="sxs-lookup"><span data-stu-id="fff75-192">Branch used = master</span></span>|

<span data-ttu-id="fff75-193">`Map`prend en charge l’imbrication, par exemple :</span><span class="sxs-lookup"><span data-stu-id="fff75-193">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="fff75-194">`Map`peut également correspondre à plusieurs segments à la fois, par exemple :</span><span class="sxs-lookup"><span data-stu-id="fff75-194">`Map` can also match multiple segments at once, for example:</span></span>

 ```csharp
app.Map("/level1/level2", HandleMultiSeg);
```

## <a name="built-in-middleware"></a><span data-ttu-id="fff75-195">Intergiciel (middleware) intégré</span><span class="sxs-lookup"><span data-stu-id="fff75-195">Built-in middleware</span></span>

<span data-ttu-id="fff75-196">ASP.NET Core est fourni avec les composants d’intergiciel (middleware) suivant :</span><span class="sxs-lookup"><span data-stu-id="fff75-196">ASP.NET Core ships with the following middleware components:</span></span>

| <span data-ttu-id="fff75-197">Intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="fff75-197">Middleware</span></span> | <span data-ttu-id="fff75-198">Description</span><span class="sxs-lookup"><span data-stu-id="fff75-198">Description</span></span> |
| ----- | ------- |
| [<span data-ttu-id="fff75-199">Authentification</span><span class="sxs-lookup"><span data-stu-id="fff75-199">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="fff75-200">Fournit la prise en charge de l’authentification.</span><span class="sxs-lookup"><span data-stu-id="fff75-200">Provides authentication support.</span></span> |
| [<span data-ttu-id="fff75-201">CORS</span><span class="sxs-lookup"><span data-stu-id="fff75-201">CORS</span></span>](xref:security/cors) | <span data-ttu-id="fff75-202">Configure le partage de ressources Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="fff75-202">Configures Cross-Origin Resource Sharing.</span></span> |
| [<span data-ttu-id="fff75-203">Mise en cache des réponses</span><span class="sxs-lookup"><span data-stu-id="fff75-203">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="fff75-204">Prend en charge la mise en cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="fff75-204">Provides support for caching responses.</span></span> |
| [<span data-ttu-id="fff75-205">Compression de la réponse</span><span class="sxs-lookup"><span data-stu-id="fff75-205">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="fff75-206">Prend en charge la compression des réponses.</span><span class="sxs-lookup"><span data-stu-id="fff75-206">Provides support for compressing responses.</span></span> |
| [<span data-ttu-id="fff75-207">Routage</span><span class="sxs-lookup"><span data-stu-id="fff75-207">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="fff75-208">Définit et contraint les itinéraires de la demande.</span><span class="sxs-lookup"><span data-stu-id="fff75-208">Defines and constrains request routes.</span></span> |
| [<span data-ttu-id="fff75-209">Session</span><span class="sxs-lookup"><span data-stu-id="fff75-209">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="fff75-210">Prend en charge la gestion des sessions utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fff75-210">Provides support for managing user sessions.</span></span> |
| [<span data-ttu-id="fff75-211">Fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="fff75-211">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="fff75-212">Fournit la prise en charge pour traiter les fichiers statiques et l’exploration des répertoires.</span><span class="sxs-lookup"><span data-stu-id="fff75-212">Provides support for serving static files and directory browsing.</span></span> |
| [<span data-ttu-id="fff75-213">Intergiciel de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="fff75-213">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="fff75-214">Prend en charge la réécriture d’URL et la redirection des demandes.</span><span class="sxs-lookup"><span data-stu-id="fff75-214">Provides support for rewriting URLs and redirecting requests.</span></span> |

<a name=middleware-writing-middleware></a>

## <a name="writing-middleware"></a><span data-ttu-id="fff75-215">Intergiciel (middleware) de l’écriture</span><span class="sxs-lookup"><span data-stu-id="fff75-215">Writing middleware</span></span>

<span data-ttu-id="fff75-216">Intergiciel (middleware) est généralement encapsulé dans une classe et exposée avec une méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="fff75-216">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="fff75-217">Prenez en compte l’intergiciel (middleware) suivant, qui définit la culture pour la requête actuelle à partir de la chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="fff75-217">Consider the following middleware, which sets the culture for the current request from the query string:</span></span>

[!code-csharp[Main](middleware/sample/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="fff75-218">Remarque : L’exemple de code ci-dessus est utilisé pour illustrer la création d’un composant d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="fff75-218">Note: The sample code above is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="fff75-219">Consultez [ globalisation et localisation](xref:fundamentals/localization) pour la prise en charge de la localisation intégré d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fff75-219">See [ Globalization and localization](xref:fundamentals/localization) for ASP.NET Core's built-in localization support.</span></span>

<span data-ttu-id="fff75-220">Vous pouvez tester l’intergiciel (middleware) en passant dans la culture, par exemple `http://localhost:7997/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="fff75-220">You can test the middleware by passing in the culture, for example `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="fff75-221">Le code suivant déplace le délégué de l’intergiciel (middleware) à une classe :</span><span class="sxs-lookup"><span data-stu-id="fff75-221">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddleware.cs)]

<span data-ttu-id="fff75-222">La méthode d’extension suivant expose l’intergiciel (middleware) via [IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):</span><span class="sxs-lookup"><span data-stu-id="fff75-222">The following extension method exposes the middleware through [IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):</span></span>

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="fff75-223">Le code suivant appelle l’intergiciel (middleware) à partir de `Configure`:</span><span class="sxs-lookup"><span data-stu-id="fff75-223">The following code calls the middleware from `Configure`:</span></span>

[!code-csharp[Main](middleware/sample/Culture/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="fff75-224">Intergiciel (middleware) doit suivre le [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/) en exposant ses dépendances dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="fff75-224">Middleware should follow the [Explicit Dependencies Principle](http://deviq.com/explicit-dependencies-principle/) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="fff75-225">Intergiciel (middleware) est construit en une seule fois par *durée de vie application*.</span><span class="sxs-lookup"><span data-stu-id="fff75-225">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="fff75-226">Consultez *dépendances par demande* ci-dessous si vous avez besoin de partager des services avec un intergiciel (middleware) au sein d’une requête.</span><span class="sxs-lookup"><span data-stu-id="fff75-226">See *Per-request dependencies* below if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="fff75-227">Composants d’intergiciel (middleware) peuvent résoudre leurs dépendances à partir de l’injection de dépendances via les paramètres du constructeur.</span><span class="sxs-lookup"><span data-stu-id="fff75-227">Middleware components can resolve their dependencies from dependency injection through constructor parameters.</span></span> <span data-ttu-id="fff75-228">[`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary)peut également accepter des paramètres supplémentaires directement.</span><span class="sxs-lookup"><span data-stu-id="fff75-228">[`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary) can also accept additional parameters directly.</span></span>

### <a name="per-request-dependencies"></a><span data-ttu-id="fff75-229">Dépendances par demande</span><span class="sxs-lookup"><span data-stu-id="fff75-229">Per-request dependencies</span></span>

<span data-ttu-id="fff75-230">Étant donné que l’intergiciel (middleware) est créée au démarrage de l’application, pas à la demande, *étendue* les services de durée de vie utilisés par les constructeurs de l’intergiciel (middleware) ne sont pas partagés avec d’autres types d’injection de dépendance lors de chaque demande.</span><span class="sxs-lookup"><span data-stu-id="fff75-230">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors are not  shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="fff75-231">Si vous devez partager une *étendue* entre votre intergiciel (middleware) et d’autres types de service, ajoutez ces services pour le `Invoke` signature de la méthode.</span><span class="sxs-lookup"><span data-stu-id="fff75-231">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="fff75-232">Le `Invoke` méthode peut accepter des paramètres supplémentaires sont remplies par injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="fff75-232">The `Invoke` method can accept additional parameters that are populated by dependency injection.</span></span> <span data-ttu-id="fff75-233">Exemple :</span><span class="sxs-lookup"><span data-stu-id="fff75-233">For example:</span></span>

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

## <a name="resources"></a><span data-ttu-id="fff75-234">Ressources</span><span class="sxs-lookup"><span data-stu-id="fff75-234">Resources</span></span>

* [<span data-ttu-id="fff75-235">Exemple de code utilisé dans ce document.</span><span class="sxs-lookup"><span data-stu-id="fff75-235">Sample code used in this doc</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)
* [<span data-ttu-id="fff75-236">Migration des modules HTTP vers les intergiciels (middleware)</span><span class="sxs-lookup"><span data-stu-id="fff75-236">Migrating HTTP Modules to Middleware</span></span>](../migration/http-modules.md)
* [<span data-ttu-id="fff75-237">Démarrage d’une application</span><span class="sxs-lookup"><span data-stu-id="fff75-237">Application Startup</span></span>](startup.md)
* [<span data-ttu-id="fff75-238">Fonctionnalités de requête</span><span class="sxs-lookup"><span data-stu-id="fff75-238">Request Features</span></span>](request-features.md)

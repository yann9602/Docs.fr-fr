---
title: Migration des gestionnaires HTTP et des modules pour ASP.NET Core intergiciel (middleware)
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: tdykstra
manager: wpickett
ms.date: 12/07/2016
ms.topic: article
ms.assetid: 9c826a76-fbd2-46b5-978d-6ca6df53531a
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/http-modules
ms.openlocfilehash: e14664133abf010b80374036e4855fdff71d1d5f
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="migrating-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="e26fc-103">Migration des gestionnaires HTTP et des modules pour ASP.NET Core intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="e26fc-103">Migrating HTTP handlers and modules to ASP.NET Core middleware</span></span> 

<span data-ttu-id="e26fc-104">Par [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span><span class="sxs-lookup"><span data-stu-id="e26fc-104">By [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span></span>

<span data-ttu-id="e26fc-105">Cet article explique comment migrer ASP.NET existants [modules HTTP et des gestionnaires à partir de system.webserver](https://docs.microsoft.com/iis/configuration/system.webserver/) à ASP.NET Core [intergiciel (middleware)](../fundamentals/middleware.md).</span><span class="sxs-lookup"><span data-stu-id="e26fc-105">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](https://docs.microsoft.com/iis/configuration/system.webserver/) to ASP.NET Core [middleware](../fundamentals/middleware.md).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="e26fc-106">Modules et les gestionnaires revisitées</span><span class="sxs-lookup"><span data-stu-id="e26fc-106">Modules and handlers revisited</span></span>

<span data-ttu-id="e26fc-107">Avant de procéder à un intergiciel (middleware) ASP.NET Core, tout d’abord récapitulons le fonctionnement des gestionnaires et des modules HTTP :</span><span class="sxs-lookup"><span data-stu-id="e26fc-107">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Gestionnaire de modules](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="e26fc-109">**Les gestionnaires sont :**</span><span class="sxs-lookup"><span data-stu-id="e26fc-109">**Handlers are:**</span></span>

   * <span data-ttu-id="e26fc-110">Classes qui implémentent [IHttpHandler](https://docs.microsoft.com/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="e26fc-110">Classes that implement [IHttpHandler](https://docs.microsoft.com/dotnet/api/system.web.ihttphandler)</span></span>

   * <span data-ttu-id="e26fc-111">Permet de gérer les demandes avec un nom de fichier donné ou une extension, tel que *report*</span><span class="sxs-lookup"><span data-stu-id="e26fc-111">Used to handle requests with a given file name or extension, such as *.report*</span></span>

   * <span data-ttu-id="e26fc-112">[Configuré](https://docs.microsoft.com//iis/configuration/system.webserver/handlers/) dans *Web.config*</span><span class="sxs-lookup"><span data-stu-id="e26fc-112">[Configured](https://docs.microsoft.com//iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="e26fc-113">**Les modules sont :**</span><span class="sxs-lookup"><span data-stu-id="e26fc-113">**Modules are:**</span></span>

   * <span data-ttu-id="e26fc-114">Classes qui implémentent [IHttpModule](https://docs.microsoft.com/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="e26fc-114">Classes that implement [IHttpModule](https://docs.microsoft.com/dotnet/api/system.web.ihttpmodule)</span></span>

   * <span data-ttu-id="e26fc-115">Appelé pour chaque demande</span><span class="sxs-lookup"><span data-stu-id="e26fc-115">Invoked for every request</span></span>

   * <span data-ttu-id="e26fc-116">La possibilité de court-circuit (interrompre le traitement d’une demande)</span><span class="sxs-lookup"><span data-stu-id="e26fc-116">Able to short-circuit (stop further processing of a request)</span></span>

   * <span data-ttu-id="e26fc-117">En mesure d’ajouter à la réponse HTTP, ou créer leurs propres</span><span class="sxs-lookup"><span data-stu-id="e26fc-117">Able to add to the HTTP response, or create their own</span></span>

   * <span data-ttu-id="e26fc-118">[Configuré](https://docs.microsoft.com//iis/configuration/system.webserver/modules/) dans *Web.config*</span><span class="sxs-lookup"><span data-stu-id="e26fc-118">[Configured](https://docs.microsoft.com//iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="e26fc-119">**L’ordre dans lequel les modules de traitent les demandes entrantes est déterminé par :**</span><span class="sxs-lookup"><span data-stu-id="e26fc-119">**The order in which modules process incoming requests is determined by:**</span></span>

   1. <span data-ttu-id="e26fc-120">Le [cycle de vie d’application](https://msdn.microsoft.com/library/ms227673.aspx), qui est représenté par une série déclenchés par ASP.NET : [BeginRequest](https://docs.microsoft.com/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](https://docs.microsoft.com/dotnet/api/system.web.httpapplication.authenticaterequest), etc.. Chaque module peut créer un gestionnaire pour un ou plusieurs événements.</span><span class="sxs-lookup"><span data-stu-id="e26fc-120">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](https://docs.microsoft.com/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](https://docs.microsoft.com/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

   2. <span data-ttu-id="e26fc-121">Pour le même événement, l’ordre dans lequel ils sont configurés dans *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="e26fc-121">For the same event, the order in which they are configured in *Web.config*.</span></span>

<span data-ttu-id="e26fc-122">En plus des modules, vous pouvez ajouter des gestionnaires pour les événements de cycle de vie à votre *Global.asax.cs* fichier.</span><span class="sxs-lookup"><span data-stu-id="e26fc-122">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="e26fc-123">Ces gestionnaires exécutés après les gestionnaires dans les modules configurés.</span><span class="sxs-lookup"><span data-stu-id="e26fc-123">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="e26fc-124">À partir des gestionnaires et des modules pour intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="e26fc-124">From handlers and modules to middleware</span></span>

<span data-ttu-id="e26fc-125">**Intergiciel (middleware) sont plus simples que les gestionnaires et modules HTTP :**</span><span class="sxs-lookup"><span data-stu-id="e26fc-125">**Middleware are simpler than HTTP modules and handlers:**</span></span>

   * <span data-ttu-id="e26fc-126">Modules, gestionnaires, *Global.asax.cs*, *Web.config* (à l’exception de la configuration d’IIS) et le cycle de vie d’application ont disparu</span><span class="sxs-lookup"><span data-stu-id="e26fc-126">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

   * <span data-ttu-id="e26fc-127">Les rôles des modules et des gestionnaires ont été prises en charge par l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="e26fc-127">The roles of both modules and handlers have been taken over by middleware</span></span>

   * <span data-ttu-id="e26fc-128">Intergiciel (middleware) sont configurés à l’aide de code plutôt que dans *Web.config*</span><span class="sxs-lookup"><span data-stu-id="e26fc-128">Middleware are configured using code rather than in *Web.config*</span></span>

   * <span data-ttu-id="e26fc-129">[Créer une branche de pipeline](../fundamentals/middleware.md#middleware-run-map-use) vous permet d’envoyer les demandes à un intergiciel (middleware) de spécifique, en fonction de non seulement l’URL, mais également sur les en-têtes de demande, les chaînes de requête, etc..</span><span class="sxs-lookup"><span data-stu-id="e26fc-129">[Pipeline branching](../fundamentals/middleware.md#middleware-run-map-use) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="e26fc-130">**Intergiciel (middleware) sont très semblables aux modules :**</span><span class="sxs-lookup"><span data-stu-id="e26fc-130">**Middleware are very similar to modules:**</span></span>

   * <span data-ttu-id="e26fc-131">Appelé en principe pour chaque demande</span><span class="sxs-lookup"><span data-stu-id="e26fc-131">Invoked in principle for every request</span></span>

   * <span data-ttu-id="e26fc-132">La possibilité de court-circuit une demande, par [ne pas passer la demande à l’intergiciel (middleware) suivant](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="e26fc-132">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

   * <span data-ttu-id="e26fc-133">La possibilité de créer leur propres réponse HTTP</span><span class="sxs-lookup"><span data-stu-id="e26fc-133">Able to create their own HTTP response</span></span>

<span data-ttu-id="e26fc-134">**Intergiciel (middleware) et les modules sont traités dans un ordre différent :**</span><span class="sxs-lookup"><span data-stu-id="e26fc-134">**Middleware and modules are processed in a different order:**</span></span>

   * <span data-ttu-id="e26fc-135">Ordre des intergiciels (middleware) est basé sur l’ordre dans lequel ils sont insérés dans le pipeline de demande, tandis que l’ordre des modules est principalement basé sur [cycle de vie d’application](https://msdn.microsoft.com/library/ms227673.aspx) événements</span><span class="sxs-lookup"><span data-stu-id="e26fc-135">Order of middleware is based on the order in which they are inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

   * <span data-ttu-id="e26fc-136">Ordre de l’intergiciel (middleware) pour les réponses est l’inverse de celui pour les demandes, tandis que l’ordre des modules est le même pour les demandes et réponses</span><span class="sxs-lookup"><span data-stu-id="e26fc-136">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

   * <span data-ttu-id="e26fc-137">Consultez [création d’un pipeline de l’intergiciel (middleware) avec IApplicationBuilder](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="e26fc-137">See [Creating a middleware pipeline with IApplicationBuilder](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Intergiciel (middleware)](http-modules/_static/middleware.png)

<span data-ttu-id="e26fc-139">Notez comment, dans l’image ci-dessus, l’intergiciel (middleware) d’authentification court-circuité la demande.</span><span class="sxs-lookup"><span data-stu-id="e26fc-139">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="e26fc-140">Migration de code de module pour un intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="e26fc-140">Migrating module code to middleware</span></span>

<span data-ttu-id="e26fc-141">Un module HTTP existant doit ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="e26fc-141">An existing HTTP module will look similar to this:</span></span>

<span data-ttu-id="e26fc-142">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-142">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]</span></span>

<span data-ttu-id="e26fc-143">Comme indiqué dans le [intergiciel (middleware)](../fundamentals/middleware.md) page, un intergiciel (middleware) ASP.NET Core est une classe qui expose un `Invoke` prise de méthode un `HttpContext` et en retournant un `Task`.</span><span class="sxs-lookup"><span data-stu-id="e26fc-143">As shown in the [Middleware](../fundamentals/middleware.md) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="e26fc-144">Votre nouveau middleware doit ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="e26fc-144">Your new middleware will look like this:</span></span>

<a name=http-modules-usemiddleware></a>

<span data-ttu-id="e26fc-145">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-145">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]</span></span>

<span data-ttu-id="e26fc-146">Le modèle d’intergiciel (middleware) ci-dessus a été effectuée à partir de la section sur [l’écriture d’intergiciel (middleware)](../fundamentals/middleware.md#middleware-writing-middleware).</span><span class="sxs-lookup"><span data-stu-id="e26fc-146">The above middleware template was taken from the section on [writing middleware](../fundamentals/middleware.md#middleware-writing-middleware).</span></span>

<span data-ttu-id="e26fc-147">Le *MyMiddlewareExtensions* classe d’assistance rend plus facile à configurer votre intergiciel (middleware) dans votre `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="e26fc-147">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="e26fc-148">Le `UseMyMiddleware` méthode ajoute votre classe d’intergiciel (middleware) au pipeline de demande.</span><span class="sxs-lookup"><span data-stu-id="e26fc-148">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="e26fc-149">Les services requis par l’intergiciel (middleware) injectés dans le constructeur de d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="e26fc-149">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name=http-modules-shortcircuiting-middleware></a>

<span data-ttu-id="e26fc-150">Votre module peut mettre fin à une demande, par exemple, si l’utilisateur n’est pas autorisé :</span><span class="sxs-lookup"><span data-stu-id="e26fc-150">Your module might terminate a request, for example if the user is not authorized:</span></span>

<span data-ttu-id="e26fc-151">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-151">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]</span></span>

<span data-ttu-id="e26fc-152">Un intergiciel (middleware) gère cela en appelant ne pas `Invoke` sur l’intergiciel (middleware) suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="e26fc-152">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="e26fc-153">Gardez à l’esprit que cela ne termine pas entièrement la demande, car middlewares précédente est toujours appelé lorsque la réponse effectue sa manière via le pipeline.</span><span class="sxs-lookup"><span data-stu-id="e26fc-153">Keep in mind that this does not fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

<span data-ttu-id="e26fc-154">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-154">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]</span></span>

<span data-ttu-id="e26fc-155">Lorsque vous migrez des fonctionnalités de votre module vers votre intergiciel (middleware) de nouveau, vous souhaiterez peut-être que votre code ne se compile pas, car la `HttpContext` classe a été considérablement modifiée dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e26fc-155">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="e26fc-156">[Plus tard](#migrating-to-the-new-httpcontext), vous allez apprendre à migrer à nouveau ASP.NET Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="e26fc-156">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="e26fc-157">Migration insertion de module dans le pipeline de demande</span><span class="sxs-lookup"><span data-stu-id="e26fc-157">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="e26fc-158">Les modules HTTP sont généralement ajoutés au pipeline de demande à l’aide de *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="e26fc-158">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

<span data-ttu-id="e26fc-159">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-159">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]</span></span>

<span data-ttu-id="e26fc-160">Convertir en [ajoutant votre intergiciel (middleware) nouvelle](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder) vers le pipeline de demande dans votre `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="e26fc-160">Convert this by [adding your new middleware](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

<span data-ttu-id="e26fc-161">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-161">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]</span></span>

<span data-ttu-id="e26fc-162">La position exacte dans le pipeline où vous insérez votre intergiciel (middleware) nouvelle dépend de l’événement qu’il est géré en tant que module (`BeginRequest`, `EndRequest`, etc.) et son ordre dans la liste des modules dans *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="e26fc-162">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="e26fc-163">Comme précédemment indiqué, il n’est aucun cycle de vie des applications dans ASP.NET Core et l’ordre dans lequel les réponses sont traitées par un intergiciel (middleware) diffère de l’ordre utilisé par les modules.</span><span class="sxs-lookup"><span data-stu-id="e26fc-163">As previously stated, there is no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="e26fc-164">Cela pourrait rendre votre décision de tri plus complexe.</span><span class="sxs-lookup"><span data-stu-id="e26fc-164">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="e26fc-165">Si le tri devient un problème, vous pourriez fractionner votre module dans plusieurs composants d’intergiciel (middleware) qui peuvent être classés de manière indépendante.</span><span class="sxs-lookup"><span data-stu-id="e26fc-165">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="e26fc-166">Migration de code de gestionnaire pour l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="e26fc-166">Migrating handler code to middleware</span></span>

<span data-ttu-id="e26fc-167">Un gestionnaire HTTP ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="e26fc-167">An HTTP handler looks something like this:</span></span>

<span data-ttu-id="e26fc-168">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-168">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]</span></span>

<span data-ttu-id="e26fc-169">Dans votre projet ASP.NET Core, vous serez traduire à un intergiciel (middleware) similaire à ceci :</span><span class="sxs-lookup"><span data-stu-id="e26fc-169">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

<span data-ttu-id="e26fc-170">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-170">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]</span></span>

<span data-ttu-id="e26fc-171">Cet intergiciel (middleware) est très similaire à l’intergiciel (middleware) correspondant aux modules.</span><span class="sxs-lookup"><span data-stu-id="e26fc-171">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="e26fc-172">La seule différence est qu’ici n’est pas appelée pour `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="e26fc-172">The only real difference is that here there is no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="e26fc-173">C’est logique, car le gestionnaire n’est à la fin du pipeline de demande, il y aura aucune intergiciel (middleware) suivant à appeler.</span><span class="sxs-lookup"><span data-stu-id="e26fc-173">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="e26fc-174">Migration insertion de gestionnaire dans le pipeline de demande</span><span class="sxs-lookup"><span data-stu-id="e26fc-174">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="e26fc-175">Configuration d’un gestionnaire HTTP est effectuée dans *Web.config* et ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="e26fc-175">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

<span data-ttu-id="e26fc-176">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-176">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]</span></span>

<span data-ttu-id="e26fc-177">Vous pouvez le convertir en ajoutant votre nouvelle intergiciel (middleware) gestionnaire au pipeline de demande dans votre `Startup` (classe), similaire à un intergiciel (middleware) converti à partir de modules.</span><span class="sxs-lookup"><span data-stu-id="e26fc-177">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="e26fc-178">Le problème avec cette approche est qu’il envoie toutes les demandes à votre nouvelle intergiciel (middleware) gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="e26fc-178">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="e26fc-179">Toutefois, vous seulement souhaitez que les demandes avec une extension donnée pour atteindre votre intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="e26fc-179">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="e26fc-180">Qui permet d’obtenir les mêmes fonctionnalités que vous aviez avec votre gestionnaire HTTP.</span><span class="sxs-lookup"><span data-stu-id="e26fc-180">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="e26fc-181">Une solution consiste à créer une branche le pipeline pour les demandes avec une extension donnée, à l’aide de la `MapWhen` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="e26fc-181">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="e26fc-182">Cela dans le même `Configure` méthode dans laquelle vous ajoutez l’autres intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="e26fc-182">You do this in the same `Configure` method where you add the other middleware:</span></span>

<span data-ttu-id="e26fc-183">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-183">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]</span></span>

<span data-ttu-id="e26fc-184">`MapWhen`utilise ces paramètres :</span><span class="sxs-lookup"><span data-stu-id="e26fc-184">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="e26fc-185">Une expression lambda qui prend le `HttpContext` et retourne `true` si la demande doit être transmis vers le bas de la branche.</span><span class="sxs-lookup"><span data-stu-id="e26fc-185">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="e26fc-186">Cela signifie que vous pouvez créer une branche de demandes non seulement en fonction de leur extension, mais également sur les en-têtes de demande, les paramètres de chaîne de requête, etc..</span><span class="sxs-lookup"><span data-stu-id="e26fc-186">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="e26fc-187">Une expression lambda qui prend un `IApplicationBuilder` et ajoute tous les intergiciels (middleware) de la branche.</span><span class="sxs-lookup"><span data-stu-id="e26fc-187">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="e26fc-188">Cela signifie que vous pouvez ajouter des intergiciels (middleware) supplémentaire à la branche devant votre intergiciel (middleware) gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="e26fc-188">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="e26fc-189">Intergiciel (middleware) sont ajoutés au pipeline avant la branche sera appelée sur toutes les demandes ; la branche aura aucun impact sur ces derniers.</span><span class="sxs-lookup"><span data-stu-id="e26fc-189">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="e26fc-190">Options d’intergiciel (middleware) à l’aide du modèle d’options de chargement</span><span class="sxs-lookup"><span data-stu-id="e26fc-190">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="e26fc-191">Certains modules et les gestionnaires offrent des options de configuration qui sont stockées dans *Web.config*. Toutefois, un nouveau modèle de configuration dans ASP.NET Core est utilisé à la place de *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="e26fc-191">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="e26fc-192">La nouvelle [système de configuration](../fundamentals/configuration.md) vous offre des options suivantes pour résoudre ce problème :</span><span class="sxs-lookup"><span data-stu-id="e26fc-192">The new [configuration system](../fundamentals/configuration.md) gives you these options to solve this:</span></span>

* <span data-ttu-id="e26fc-193">Injecter directement les options dans l’intergiciel (middleware), comme indiqué dans le [section suivante](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="e26fc-193">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="e26fc-194">Utilisez le [modèle d’options](../fundamentals/configuration.md#options-config-objects):</span><span class="sxs-lookup"><span data-stu-id="e26fc-194">Use the [options pattern](../fundamentals/configuration.md#options-config-objects):</span></span>

1.  <span data-ttu-id="e26fc-195">Créez une classe pour contenir les options de l’intergiciel (middleware), par exemple :</span><span class="sxs-lookup"><span data-stu-id="e26fc-195">Create a class to hold your middleware options, for example:</span></span>

    <span data-ttu-id="e26fc-196">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-196">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]</span></span>

2.  <span data-ttu-id="e26fc-197">Stocker les valeurs d’option</span><span class="sxs-lookup"><span data-stu-id="e26fc-197">Store the option values</span></span>

    <span data-ttu-id="e26fc-198">Le système de configuration vous permet de vous permet de stocker des valeurs d’option partout où que vous souhaitez.</span><span class="sxs-lookup"><span data-stu-id="e26fc-198">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="e26fc-199">Toutefois, les sites plus utiliser *appsettings.json*, donc nous allons cette approche :</span><span class="sxs-lookup"><span data-stu-id="e26fc-199">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

    <span data-ttu-id="e26fc-200">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-200">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]</span></span>

    <span data-ttu-id="e26fc-201">*MyMiddlewareOptionsSection* ici est un nom de section.</span><span class="sxs-lookup"><span data-stu-id="e26fc-201">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="e26fc-202">Il ne doit pas être le même que le nom de votre classe d’options.</span><span class="sxs-lookup"><span data-stu-id="e26fc-202">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="e26fc-203">Associer les valeurs d’option de la classe d’options</span><span class="sxs-lookup"><span data-stu-id="e26fc-203">Associate the option values with the options class</span></span>

    <span data-ttu-id="e26fc-204">Le modèle d’options utilise infrastructure d’injection de dépendance de ASP.NET Core pour associer le type options (telles que `MyMiddlewareOptions`) avec un `MyMiddlewareOptions` présentant les options de.</span><span class="sxs-lookup"><span data-stu-id="e26fc-204">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="e26fc-205">Mise à jour votre `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="e26fc-205">Update your `Startup` class:</span></span>

    1.  <span data-ttu-id="e26fc-206">Si vous utilisez *appsettings.json*, ajoutez-le au Générateur de configuration dans le `Startup` constructeur :</span><span class="sxs-lookup"><span data-stu-id="e26fc-206">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      <span data-ttu-id="e26fc-207">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-207">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]</span></span>

    2.  <span data-ttu-id="e26fc-208">Configurer le service d’options :</span><span class="sxs-lookup"><span data-stu-id="e26fc-208">Configure the options service:</span></span>

      <span data-ttu-id="e26fc-209">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-209">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]</span></span>

    3.  <span data-ttu-id="e26fc-210">Associer vos options à votre classe d’options :</span><span class="sxs-lookup"><span data-stu-id="e26fc-210">Associate your options with your options class:</span></span>

      <span data-ttu-id="e26fc-211">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-211">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]</span></span>

4.  <span data-ttu-id="e26fc-212">Injecter les options dans votre constructeur d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="e26fc-212">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="e26fc-213">Cela est similaire à l’injection d’options dans un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="e26fc-213">This is similar to injecting options into a controller.</span></span>

  <span data-ttu-id="e26fc-214">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-214">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]</span></span>

  <span data-ttu-id="e26fc-215">Le [UseMiddleware](#http-modules-usemiddleware) méthode d’extension qui ajoute votre intergiciel (middleware) pour le `IApplicationBuilder` prend en charge l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e26fc-215">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

  <span data-ttu-id="e26fc-216">Cela n’est pas limitée à `IOptions` objets.</span><span class="sxs-lookup"><span data-stu-id="e26fc-216">This is not limited to `IOptions` objects.</span></span> <span data-ttu-id="e26fc-217">De cette manière peut être ajoutée à n’importe quel autre objet nécessitant votre intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="e26fc-217">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="e26fc-218">Chargement des options d’intergiciel (middleware) par le biais d’injection directe</span><span class="sxs-lookup"><span data-stu-id="e26fc-218">Loading middleware options through direct injection</span></span>

<span data-ttu-id="e26fc-219">Le modèle d’options présente l’avantage qu’il crée le faible couplage entre les valeurs des options et leurs consommateurs.</span><span class="sxs-lookup"><span data-stu-id="e26fc-219">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="e26fc-220">Une fois que vous avez associé une classe d’options avec les valeurs d’options réel, toute autre classe peut accéder aux options via l’infrastructure d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="e26fc-220">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="e26fc-221">Il n’est pas nécessaire de passer autour des valeurs d’options.</span><span class="sxs-lookup"><span data-stu-id="e26fc-221">There is no need to pass around options values.</span></span>

<span data-ttu-id="e26fc-222">Cela répartit bien que si vous souhaitez utiliser l’intergiciel (middleware) même à deux reprises, avec différentes options.</span><span class="sxs-lookup"><span data-stu-id="e26fc-222">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="e26fc-223">Par exemple un intergiciel d’autorisation utilisé dans différentes branches autorisant les différents rôles.</span><span class="sxs-lookup"><span data-stu-id="e26fc-223">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="e26fc-224">Vous ne pouvez pas associer deux objets de différentes options la classe d’une options.</span><span class="sxs-lookup"><span data-stu-id="e26fc-224">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="e26fc-225">La solution consiste à obtenir les objets d’options avec les valeurs d’options réelles dans votre `Startup` classe et transfèrent ces informations directement à chaque instance de votre intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="e26fc-225">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1.  <span data-ttu-id="e26fc-226">Ajouter une deuxième clé à *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="e26fc-226">Add a second key to *appsettings.json*</span></span>

    <span data-ttu-id="e26fc-227">Pour ajouter un deuxième ensemble d’options pour le *appsettings.json* , utilisez une nouvelle clé pour identifier de manière unique :</span><span class="sxs-lookup"><span data-stu-id="e26fc-227">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

    <span data-ttu-id="e26fc-228">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-228">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]</span></span>

2.  <span data-ttu-id="e26fc-229">Récupérer les valeurs des options et les passer à un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="e26fc-229">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="e26fc-230">Le `Use...` méthode d’extension (ce qui ajoute votre intergiciel (middleware) au pipeline) est un emplacement logique pour transmettre les valeurs d’option :</span><span class="sxs-lookup"><span data-stu-id="e26fc-230">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

    <span data-ttu-id="e26fc-231">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-231">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]</span></span>

4.  <span data-ttu-id="e26fc-232">Activer l’intergiciel (middleware) prendre un paramètre options.</span><span class="sxs-lookup"><span data-stu-id="e26fc-232">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="e26fc-233">Fournissez une surcharge de la `Use...` méthode d’extension (qui prend le paramètre options et passe à `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="e26fc-233">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="e26fc-234">Lorsque `UseMiddleware` est appelée avec des paramètres, il transmet les paramètres à votre constructeur d’intergiciel (middleware) lorsqu’il instancie l’objet de l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="e26fc-234">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

    <span data-ttu-id="e26fc-235">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-235">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]</span></span>

    <span data-ttu-id="e26fc-236">Notez comment cela encapsule l’objet d’options dans un `OptionsWrapper` objet.</span><span class="sxs-lookup"><span data-stu-id="e26fc-236">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="e26fc-237">Il implémente `IOptions`, comme prévu par le constructeur d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="e26fc-237">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="e26fc-238">Migration vers le nouveau HttpContext</span><span class="sxs-lookup"><span data-stu-id="e26fc-238">Migrating to the new HttpContext</span></span>

<span data-ttu-id="e26fc-239">Vous savez que la `Invoke` méthode dans votre intergiciel (middleware) prend un paramètre de type `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="e26fc-239">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="e26fc-240">`HttpContext`a changé de manière significative dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e26fc-240">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="e26fc-241">Cette section montre comment traduire les propriétés couramment utilisées de [System.Web.HttpContext](https://docs.microsoft.com/dotnet/api/system.web.httpcontext) au nouveau `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="e26fc-241">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](https://docs.microsoft.com/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="e26fc-242">HttpContext</span><span class="sxs-lookup"><span data-stu-id="e26fc-242">HttpContext</span></span>

<span data-ttu-id="e26fc-243">**HttpContext.Items** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-243">**HttpContext.Items** translates to:</span></span>

<span data-ttu-id="e26fc-244">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-244">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]</span></span>

<span data-ttu-id="e26fc-245">**ID de demande unique (pas d’équivalent System.Web.HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="e26fc-245">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="e26fc-246">Vous donne un id unique pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="e26fc-246">Gives you a unique id for each request.</span></span> <span data-ttu-id="e26fc-247">Très utiles à inclure dans vos journaux.</span><span class="sxs-lookup"><span data-stu-id="e26fc-247">Very useful to include in your logs.</span></span>

<span data-ttu-id="e26fc-248">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-248">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]</span></span>

### <a name="httpcontextrequest"></a><span data-ttu-id="e26fc-249">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="e26fc-249">HttpContext.Request</span></span>

<span data-ttu-id="e26fc-250">**HttpContext.Request.HttpMethod** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-250">**HttpContext.Request.HttpMethod** translates to:</span></span>

<span data-ttu-id="e26fc-251">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-251">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]</span></span>

<span data-ttu-id="e26fc-252">**HttpContext.Request.QueryString** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-252">**HttpContext.Request.QueryString** translates to:</span></span>

<span data-ttu-id="e26fc-253">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-253">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]</span></span>

<span data-ttu-id="e26fc-254">**HttpContext.Request.Url** et **HttpContext.Request.RawUrl** traduire en :</span><span class="sxs-lookup"><span data-stu-id="e26fc-254">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

<span data-ttu-id="e26fc-255">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-255">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]</span></span>

<span data-ttu-id="e26fc-256">**HttpContext.Request.IsSecureConnection** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-256">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

<span data-ttu-id="e26fc-257">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-257">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]</span></span>

<span data-ttu-id="e26fc-258">**HttpContext.Request.UserHostAddress** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-258">**HttpContext.Request.UserHostAddress** translates to:</span></span>

<span data-ttu-id="e26fc-259">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-259">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]</span></span>

<span data-ttu-id="e26fc-260">**HttpContext.Request.Cookies** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-260">**HttpContext.Request.Cookies** translates to:</span></span>

<span data-ttu-id="e26fc-261">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-261">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]</span></span>

<span data-ttu-id="e26fc-262">**HttpContext.Request.RequestContext.RouteData** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-262">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

<span data-ttu-id="e26fc-263">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-263">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]</span></span>

<span data-ttu-id="e26fc-264">**HttpContext.Request.Headers** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-264">**HttpContext.Request.Headers** translates to:</span></span>

<span data-ttu-id="e26fc-265">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-265">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]</span></span>

<span data-ttu-id="e26fc-266">**HttpContext.Request.UserAgent** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-266">**HttpContext.Request.UserAgent** translates to:</span></span>

<span data-ttu-id="e26fc-267">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-267">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]</span></span>

<span data-ttu-id="e26fc-268">**HttpContext.Request.UrlReferrer** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-268">**HttpContext.Request.UrlReferrer** translates to:</span></span>

<span data-ttu-id="e26fc-269">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-269">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]</span></span>

<span data-ttu-id="e26fc-270">**HttpContext.Request.ContentType** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-270">**HttpContext.Request.ContentType** translates to:</span></span>

<span data-ttu-id="e26fc-271">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-271">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]</span></span>

<span data-ttu-id="e26fc-272">**HttpContext.Request.Form** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-272">**HttpContext.Request.Form** translates to:</span></span>

<span data-ttu-id="e26fc-273">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-273">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]</span></span>

> [!WARNING]
> <span data-ttu-id="e26fc-274">Lire les valeurs de formulaire uniquement si le type de contenu sub est *x--www-form-urlencoded* ou *des données de formulaire*.</span><span class="sxs-lookup"><span data-stu-id="e26fc-274">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="e26fc-275">**HttpContext.Request.InputStream** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-275">**HttpContext.Request.InputStream** translates to:</span></span>

<span data-ttu-id="e26fc-276">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-276">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]</span></span>

> [!WARNING]
> <span data-ttu-id="e26fc-277">Utilisez ce code uniquement dans un gestionnaire type intergiciel (middleware), à la fin d’un pipeline.</span><span class="sxs-lookup"><span data-stu-id="e26fc-277">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="e26fc-278">Vous pouvez lire le corps brut comme indiqué plus haut qu’une seule fois par requête.</span><span class="sxs-lookup"><span data-stu-id="e26fc-278">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="e26fc-279">Intergiciel (middleware) tente de lire le corps après la première lecture lira le corps est vide.</span><span class="sxs-lookup"><span data-stu-id="e26fc-279">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="e26fc-280">Cela ne s’applique pas à la lecture d’un formulaire comme indiqué précédemment, qui est effectuée à partir d’une mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="e26fc-280">This does not apply to reading a form as shown earlier, because that is done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="e26fc-281">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="e26fc-281">HttpContext.Response</span></span>

<span data-ttu-id="e26fc-282">**HttpContext.Response.Status** et **HttpContext.Response.StatusDescription** traduire en :</span><span class="sxs-lookup"><span data-stu-id="e26fc-282">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

<span data-ttu-id="e26fc-283">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-283">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]</span></span>

<span data-ttu-id="e26fc-284">**HttpContext.Response.ContentEncoding** et **HttpContext.Response.ContentType** traduire en :</span><span class="sxs-lookup"><span data-stu-id="e26fc-284">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

<span data-ttu-id="e26fc-285">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-285">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]</span></span>

<span data-ttu-id="e26fc-286">**HttpContext.Response.ContentType** sur son propre également se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-286">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

<span data-ttu-id="e26fc-287">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-287">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]</span></span>

<span data-ttu-id="e26fc-288">**HttpContext.Response.Output** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="e26fc-288">**HttpContext.Response.Output** translates to:</span></span>

<span data-ttu-id="e26fc-289">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-289">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]</span></span>

<span data-ttu-id="e26fc-290">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="e26fc-290">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="e26fc-291">Accès à un fichier est décrite [ici](../fundamentals/request-features.md#middleware-and-request-features).</span><span class="sxs-lookup"><span data-stu-id="e26fc-291">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="e26fc-292">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="e26fc-292">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="e26fc-293">Envoi des en-têtes de réponse est compliqué par le fait que si vous leur attribuez une fois que tout a été écrite dans le corps de réponse, ils n’être envoyées.</span><span class="sxs-lookup"><span data-stu-id="e26fc-293">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="e26fc-294">La solution consiste à définir une méthode de rappel qui sera appelée droite avant d’écrire sur le démarrage de la réponse.</span><span class="sxs-lookup"><span data-stu-id="e26fc-294">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="e26fc-295">Cette opération s’effectue mieux au début de la `Invoke` méthode dans votre intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="e26fc-295">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="e26fc-296">Il s’agit de cette méthode de rappel qui définit les en-têtes de réponse.</span><span class="sxs-lookup"><span data-stu-id="e26fc-296">It is this callback method that sets your response headers.</span></span>

<span data-ttu-id="e26fc-297">Le code suivant définit une méthode de rappel appelée `SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="e26fc-297">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="e26fc-298">Le `SetHeaders` méthode de rappel ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="e26fc-298">The `SetHeaders` callback method would look like this:</span></span>

<span data-ttu-id="e26fc-299">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-299">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]</span></span>

<span data-ttu-id="e26fc-300">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="e26fc-300">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="e26fc-301">Les cookies sont transmis au navigateur dans un *Set-Cookie* en-tête de réponse.</span><span class="sxs-lookup"><span data-stu-id="e26fc-301">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="e26fc-302">Par conséquent, envoi de cookies nécessite le rappel de même que celle utilisée pour l’envoi des en-têtes de réponse :</span><span class="sxs-lookup"><span data-stu-id="e26fc-302">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="e26fc-303">Le `SetCookies` méthode de rappel se présente comme suit :</span><span class="sxs-lookup"><span data-stu-id="e26fc-303">The `SetCookies` callback method would look like the following:</span></span>

<span data-ttu-id="e26fc-304">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]</span><span class="sxs-lookup"><span data-stu-id="e26fc-304">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e26fc-305">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e26fc-305">Additional Resources</span></span>

* [<span data-ttu-id="e26fc-306">Vue d’ensemble des Modules HTTP et les gestionnaires HTTP</span><span class="sxs-lookup"><span data-stu-id="e26fc-306">HTTP Handlers and HTTP Modules Overview</span></span>](https://docs.microsoft.com/iis/configuration/system.webserver/)

* [<span data-ttu-id="e26fc-307">Configuration</span><span class="sxs-lookup"><span data-stu-id="e26fc-307">Configuration</span></span>](../fundamentals/configuration.md)

* [<span data-ttu-id="e26fc-308">Démarrage d’une application</span><span class="sxs-lookup"><span data-stu-id="e26fc-308">Application Startup</span></span>](../fundamentals/startup.md)

* [<span data-ttu-id="e26fc-309">Intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="e26fc-309">Middleware</span></span>](../fundamentals/middleware.md)

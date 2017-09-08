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
ms.openlocfilehash: f99c2751138ac789e7105ff256ce7254e280463e
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="migrating-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="dbbc2-103">Migration des gestionnaires HTTP et des modules pour ASP.NET Core intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-103">Migrating HTTP handlers and modules to ASP.NET Core middleware</span></span> 

<span data-ttu-id="dbbc2-104">Par [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-104">By [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span></span>

<span data-ttu-id="dbbc2-105">Cet article explique comment migrer ASP.NET existants [modules HTTP et des gestionnaires de](https://msdn.microsoft.com/library/bb398986.aspx) à ASP.NET Core [intergiciel (middleware)](../fundamentals/middleware.md).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-105">This article shows how to migrate existing ASP.NET [HTTP modules and handlers](https://msdn.microsoft.com/library/bb398986.aspx) to ASP.NET Core [middleware](../fundamentals/middleware.md).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="dbbc2-106">Modules et les gestionnaires revisitées</span><span class="sxs-lookup"><span data-stu-id="dbbc2-106">Modules and handlers revisited</span></span>

<span data-ttu-id="dbbc2-107">Avant de procéder à un intergiciel (middleware) ASP.NET Core, tout d’abord récapitulons le fonctionnement des gestionnaires et des modules HTTP :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-107">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Gestionnaire de modules](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="dbbc2-109">**Les gestionnaires sont :**</span><span class="sxs-lookup"><span data-stu-id="dbbc2-109">**Handlers are:**</span></span>

   * <span data-ttu-id="dbbc2-110">Classes qui implémentent [IHttpHandler](https://msdn.microsoft.com/library/system.web.ihttphandler.aspx)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-110">Classes that implement [IHttpHandler](https://msdn.microsoft.com/library/system.web.ihttphandler.aspx)</span></span>

   * <span data-ttu-id="dbbc2-111">Permet de gérer les demandes avec un nom de fichier donné ou une extension, tel que *report*</span><span class="sxs-lookup"><span data-stu-id="dbbc2-111">Used to handle requests with a given file name or extension, such as *.report*</span></span>

   * <span data-ttu-id="dbbc2-112">[Configuré](https://msdn.microsoft.com/library/46c5ddfy.aspx) dans *Web.config*</span><span class="sxs-lookup"><span data-stu-id="dbbc2-112">[Configured](https://msdn.microsoft.com/library/46c5ddfy.aspx) in *Web.config*</span></span>

<span data-ttu-id="dbbc2-113">**Les modules sont :**</span><span class="sxs-lookup"><span data-stu-id="dbbc2-113">**Modules are:**</span></span>

   * <span data-ttu-id="dbbc2-114">Classes qui implémentent [IHttpModule](https://msdn.microsoft.com/library/system.web.ihttpmodule.aspx)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-114">Classes that implement [IHttpModule](https://msdn.microsoft.com/library/system.web.ihttpmodule.aspx)</span></span>

   * <span data-ttu-id="dbbc2-115">Appelé pour chaque demande</span><span class="sxs-lookup"><span data-stu-id="dbbc2-115">Invoked for every request</span></span>

   * <span data-ttu-id="dbbc2-116">La possibilité de court-circuit (interrompre le traitement d’une demande)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-116">Able to short-circuit (stop further processing of a request)</span></span>

   * <span data-ttu-id="dbbc2-117">En mesure d’ajouter à la réponse HTTP, ou créer leurs propres</span><span class="sxs-lookup"><span data-stu-id="dbbc2-117">Able to add to the HTTP response, or create their own</span></span>

   * <span data-ttu-id="dbbc2-118">[Configuré](https://msdn.microsoft.com/library/ms227673.aspx) dans *Web.config*</span><span class="sxs-lookup"><span data-stu-id="dbbc2-118">[Configured](https://msdn.microsoft.com/library/ms227673.aspx) in *Web.config*</span></span>

<span data-ttu-id="dbbc2-119">**L’ordre dans lequel les modules de traitent les demandes entrantes est déterminé par :**</span><span class="sxs-lookup"><span data-stu-id="dbbc2-119">**The order in which modules process incoming requests is determined by:**</span></span>

   1. <span data-ttu-id="dbbc2-120">Le [cycle de vie d’application](https://msdn.microsoft.com/library/ms227673.aspx), qui est représenté par une série déclenchés par ASP.NET : [BeginRequest](https://msdn.microsoft.com/library/system.web.httpapplication.beginrequest.aspx), [AuthenticateRequest](https://msdn.microsoft.com/library/system.web.httpapplication.authenticaterequest.aspx), etc.. Chaque module peut créer un gestionnaire pour un ou plusieurs événements.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-120">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](https://msdn.microsoft.com/library/system.web.httpapplication.beginrequest.aspx), [AuthenticateRequest](https://msdn.microsoft.com/library/system.web.httpapplication.authenticaterequest.aspx), etc. Each module can create a handler for one or more events.</span></span>

   2. <span data-ttu-id="dbbc2-121">Pour le même événement, l’ordre dans lequel ils sont configurés dans *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-121">For the same event, the order in which they are configured in *Web.config*.</span></span>

<span data-ttu-id="dbbc2-122">En plus des modules, vous pouvez ajouter des gestionnaires pour les événements de cycle de vie à votre *Global.asax.cs* fichier.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-122">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="dbbc2-123">Ces gestionnaires exécutés après les gestionnaires dans les modules configurés.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-123">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="dbbc2-124">À partir des gestionnaires et des modules pour intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-124">From handlers and modules to middleware</span></span>

<span data-ttu-id="dbbc2-125">**Intergiciel (middleware) sont plus simples que les gestionnaires et modules HTTP :**</span><span class="sxs-lookup"><span data-stu-id="dbbc2-125">**Middleware are simpler than HTTP modules and handlers:**</span></span>

   * <span data-ttu-id="dbbc2-126">Modules, gestionnaires, *Global.asax.cs*, *Web.config* (à l’exception de la configuration d’IIS) et le cycle de vie d’application ont disparu</span><span class="sxs-lookup"><span data-stu-id="dbbc2-126">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

   * <span data-ttu-id="dbbc2-127">Les rôles des modules et des gestionnaires ont été prises en charge par l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-127">The roles of both modules and handlers have been taken over by middleware</span></span>

   * <span data-ttu-id="dbbc2-128">Intergiciel (middleware) sont configurés à l’aide de code plutôt que dans *Web.config*</span><span class="sxs-lookup"><span data-stu-id="dbbc2-128">Middleware are configured using code rather than in *Web.config*</span></span>

   * <span data-ttu-id="dbbc2-129">[Créer une branche de pipeline](../fundamentals/middleware.md#middleware-run-map-use) vous permet d’envoyer les demandes à un intergiciel (middleware) de spécifique, en fonction de non seulement l’URL, mais également sur les en-têtes de demande, les chaînes de requête, etc..</span><span class="sxs-lookup"><span data-stu-id="dbbc2-129">[Pipeline branching](../fundamentals/middleware.md#middleware-run-map-use) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="dbbc2-130">**Intergiciel (middleware) sont très semblables aux modules :**</span><span class="sxs-lookup"><span data-stu-id="dbbc2-130">**Middleware are very similar to modules:**</span></span>

   * <span data-ttu-id="dbbc2-131">Appelé en principe pour chaque demande</span><span class="sxs-lookup"><span data-stu-id="dbbc2-131">Invoked in principle for every request</span></span>

   * <span data-ttu-id="dbbc2-132">La possibilité de court-circuit une demande, par [ne pas passer la demande à l’intergiciel (middleware) suivant](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-132">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

   * <span data-ttu-id="dbbc2-133">La possibilité de créer leur propres réponse HTTP</span><span class="sxs-lookup"><span data-stu-id="dbbc2-133">Able to create their own HTTP response</span></span>

<span data-ttu-id="dbbc2-134">**Intergiciel (middleware) et les modules sont traités dans un ordre différent :**</span><span class="sxs-lookup"><span data-stu-id="dbbc2-134">**Middleware and modules are processed in a different order:**</span></span>

   * <span data-ttu-id="dbbc2-135">Ordre des intergiciels (middleware) est basé sur l’ordre dans lequel ils sont insérés dans le pipeline de demande, tandis que l’ordre des modules est principalement basé sur [cycle de vie d’application](https://msdn.microsoft.com/library/ms227673.aspx) événements</span><span class="sxs-lookup"><span data-stu-id="dbbc2-135">Order of middleware is based on the order in which they are inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

   * <span data-ttu-id="dbbc2-136">Ordre de l’intergiciel (middleware) pour les réponses est l’inverse de celui pour les demandes, tandis que l’ordre des modules est le même pour les demandes et réponses</span><span class="sxs-lookup"><span data-stu-id="dbbc2-136">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

   * <span data-ttu-id="dbbc2-137">Consultez [création d’un pipeline de l’intergiciel (middleware) avec IApplicationBuilder](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-137">See [Creating a middleware pipeline with IApplicationBuilder](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Intergiciel (middleware)](http-modules/_static/middleware.png)

<span data-ttu-id="dbbc2-139">Notez comment, dans l’image ci-dessus, l’intergiciel (middleware) d’authentification court-circuité la demande.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-139">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="dbbc2-140">Migration de code de module pour un intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-140">Migrating module code to middleware</span></span>

<span data-ttu-id="dbbc2-141">Un module HTTP existant doit ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-141">An existing HTTP module will look similar to this:</span></span>

<span data-ttu-id="dbbc2-142">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-142">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]</span></span>

<span data-ttu-id="dbbc2-143">Comme indiqué dans le [intergiciel (middleware)](../fundamentals/middleware.md) page, un intergiciel (middleware) ASP.NET Core est une classe qui expose un `Invoke` prise de méthode un `HttpContext` et en retournant un `Task`.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-143">As shown in the [Middleware](../fundamentals/middleware.md) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="dbbc2-144">Votre nouveau middleware doit ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-144">Your new middleware will look like this:</span></span>

<a name=http-modules-usemiddleware></a>

<span data-ttu-id="dbbc2-145">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-145">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]</span></span>

<span data-ttu-id="dbbc2-146">Le modèle d’intergiciel (middleware) ci-dessus a été effectuée à partir de la section sur [l’écriture d’intergiciel (middleware)](../fundamentals/middleware.md#middleware-writing-middleware).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-146">The above middleware template was taken from the section on [writing middleware](../fundamentals/middleware.md#middleware-writing-middleware).</span></span>

<span data-ttu-id="dbbc2-147">Le *MyMiddlewareExtensions* classe d’assistance rend plus facile à configurer votre intergiciel (middleware) dans votre `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-147">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="dbbc2-148">Le `UseMyMiddleware` méthode ajoute votre classe d’intergiciel (middleware) au pipeline de demande.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-148">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="dbbc2-149">Les services requis par l’intergiciel (middleware) injectés dans le constructeur de d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-149">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name=http-modules-shortcircuiting-middleware></a>

<span data-ttu-id="dbbc2-150">Votre module peut mettre fin à une demande, par exemple, si l’utilisateur n’est pas autorisé :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-150">Your module might terminate a request, for example if the user is not authorized:</span></span>

<span data-ttu-id="dbbc2-151">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-151">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]</span></span>

<span data-ttu-id="dbbc2-152">Un intergiciel (middleware) gère cela en appelant ne pas `Invoke` sur l’intergiciel (middleware) suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-152">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="dbbc2-153">Gardez à l’esprit que cela ne termine pas entièrement la demande, car middlewares précédente est toujours appelé lorsque la réponse effectue sa manière via le pipeline.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-153">Keep in mind that this does not fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

<span data-ttu-id="dbbc2-154">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-154">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]</span></span>

<span data-ttu-id="dbbc2-155">Lorsque vous migrez des fonctionnalités de votre module vers votre intergiciel (middleware) de nouveau, vous souhaiterez peut-être que votre code ne se compile pas, car la `HttpContext` classe a été considérablement modifiée dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-155">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="dbbc2-156">[Plus tard](#migrating-to-the-new-httpcontext), vous allez apprendre à migrer à nouveau ASP.NET Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-156">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="dbbc2-157">Migration insertion de module dans le pipeline de demande</span><span class="sxs-lookup"><span data-stu-id="dbbc2-157">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="dbbc2-158">Les modules HTTP sont généralement ajoutés au pipeline de demande à l’aide de *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="dbbc2-158">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

<span data-ttu-id="dbbc2-159">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-159">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]</span></span>

<span data-ttu-id="dbbc2-160">Convertir en [ajoutant votre intergiciel (middleware) nouvelle](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder) vers le pipeline de demande dans votre `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-160">Convert this by [adding your new middleware](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

<span data-ttu-id="dbbc2-161">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-161">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]</span></span>

<span data-ttu-id="dbbc2-162">La position exacte dans le pipeline où vous insérez votre intergiciel (middleware) nouvelle dépend de l’événement qu’il est géré en tant que module (`BeginRequest`, `EndRequest`, etc.) et son ordre dans la liste des modules dans *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-162">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="dbbc2-163">Comme précédemment indiqué, il n’est aucun cycle de vie des applications dans ASP.NET Core et l’ordre dans lequel les réponses sont traitées par un intergiciel (middleware) diffère de l’ordre utilisé par les modules.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-163">As previously stated, there is no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="dbbc2-164">Cela pourrait rendre votre décision de tri plus complexe.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-164">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="dbbc2-165">Si le tri devient un problème, vous pourriez fractionner votre module dans plusieurs composants d’intergiciel (middleware) qui peuvent être classés de manière indépendante.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-165">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="dbbc2-166">Migration de code de gestionnaire pour l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-166">Migrating handler code to middleware</span></span>

<span data-ttu-id="dbbc2-167">Un gestionnaire HTTP ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-167">An HTTP handler looks something like this:</span></span>

<span data-ttu-id="dbbc2-168">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-168">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]</span></span>

<span data-ttu-id="dbbc2-169">Dans votre projet ASP.NET Core, vous serez traduire à un intergiciel (middleware) similaire à ceci :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-169">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

<span data-ttu-id="dbbc2-170">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-170">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]</span></span>

<span data-ttu-id="dbbc2-171">Cet intergiciel (middleware) est très similaire à l’intergiciel (middleware) correspondant aux modules.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-171">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="dbbc2-172">La seule différence est qu’ici n’est pas appelée pour `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-172">The only real difference is that here there is no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="dbbc2-173">C’est logique, car le gestionnaire n’est à la fin du pipeline de demande, il y aura aucune intergiciel (middleware) suivant à appeler.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-173">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="dbbc2-174">Migration insertion de gestionnaire dans le pipeline de demande</span><span class="sxs-lookup"><span data-stu-id="dbbc2-174">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="dbbc2-175">Configuration d’un gestionnaire HTTP est effectuée dans *Web.config* et ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-175">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

<span data-ttu-id="dbbc2-176">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-176">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]</span></span>

<span data-ttu-id="dbbc2-177">Vous pouvez le convertir en ajoutant votre nouvelle intergiciel (middleware) gestionnaire au pipeline de demande dans votre `Startup` (classe), similaire à un intergiciel (middleware) converti à partir de modules.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-177">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="dbbc2-178">Le problème avec cette approche est qu’il envoie toutes les demandes à votre nouvelle intergiciel (middleware) gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-178">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="dbbc2-179">Toutefois, vous seulement souhaitez que les demandes avec une extension donnée pour atteindre votre intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-179">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="dbbc2-180">Qui permet d’obtenir les mêmes fonctionnalités que vous aviez avec votre gestionnaire HTTP.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-180">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="dbbc2-181">Une solution consiste à créer une branche le pipeline pour les demandes avec une extension donnée, à l’aide de la `MapWhen` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-181">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="dbbc2-182">Cela dans le même `Configure` méthode dans laquelle vous ajoutez l’autres intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-182">You do this in the same `Configure` method where you add the other middleware:</span></span>

<span data-ttu-id="dbbc2-183">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-183">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]</span></span>

<span data-ttu-id="dbbc2-184">`MapWhen`utilise ces paramètres :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-184">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="dbbc2-185">Une expression lambda qui prend le `HttpContext` et retourne `true` si la demande doit être transmis vers le bas de la branche.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-185">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="dbbc2-186">Cela signifie que vous pouvez créer une branche de demandes non seulement en fonction de leur extension, mais également sur les en-têtes de demande, les paramètres de chaîne de requête, etc..</span><span class="sxs-lookup"><span data-stu-id="dbbc2-186">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="dbbc2-187">Une expression lambda qui prend un `IApplicationBuilder` et ajoute tous les intergiciels (middleware) de la branche.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-187">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="dbbc2-188">Cela signifie que vous pouvez ajouter des intergiciels (middleware) supplémentaire à la branche devant votre intergiciel (middleware) gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-188">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="dbbc2-189">Intergiciel (middleware) sont ajoutés au pipeline avant la branche sera appelée sur toutes les demandes ; la branche aura aucun impact sur ces derniers.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-189">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="dbbc2-190">Options d’intergiciel (middleware) à l’aide du modèle d’options de chargement</span><span class="sxs-lookup"><span data-stu-id="dbbc2-190">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="dbbc2-191">Certains modules et les gestionnaires offrent des options de configuration qui sont stockées dans *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-191">Some modules and handlers have configuration options that are stored in *Web.config*.</span></span> <span data-ttu-id="dbbc2-192">Toutefois, un nouveau modèle de configuration dans ASP.NET Core est utilisé à la place de *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-192">However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="dbbc2-193">La nouvelle [système de configuration](../fundamentals/configuration.md) vous offre des options suivantes pour résoudre ce problème :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-193">The new [configuration system](../fundamentals/configuration.md) gives you these options to solve this:</span></span>

* <span data-ttu-id="dbbc2-194">Injecter directement les options dans l’intergiciel (middleware), comme indiqué dans le [section suivante](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-194">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="dbbc2-195">Utilisez le [modèle d’options](../fundamentals/configuration.md#options-config-objects):</span><span class="sxs-lookup"><span data-stu-id="dbbc2-195">Use the [options pattern](../fundamentals/configuration.md#options-config-objects):</span></span>

1.  <span data-ttu-id="dbbc2-196">Créez une classe pour contenir les options de l’intergiciel (middleware), par exemple :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-196">Create a class to hold your middleware options, for example:</span></span>

    <span data-ttu-id="dbbc2-197">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-197">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]</span></span>

2.  <span data-ttu-id="dbbc2-198">Stocker les valeurs d’option</span><span class="sxs-lookup"><span data-stu-id="dbbc2-198">Store the option values</span></span>

    <span data-ttu-id="dbbc2-199">Le système de configuration vous permet de vous permet de stocker des valeurs d’option partout où que vous souhaitez.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-199">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="dbbc2-200">Toutefois, les sites plus utiliser *appsettings.json*, donc nous allons cette approche :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-200">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

    <span data-ttu-id="dbbc2-201">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-201">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]</span></span>

    <span data-ttu-id="dbbc2-202">*MyMiddlewareOptionsSection* ici est un nom de section.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-202">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="dbbc2-203">Il ne doit pas être le même que le nom de votre classe d’options.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-203">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="dbbc2-204">Associer les valeurs d’option de la classe d’options</span><span class="sxs-lookup"><span data-stu-id="dbbc2-204">Associate the option values with the options class</span></span>

    <span data-ttu-id="dbbc2-205">Le modèle d’options utilise infrastructure d’injection de dépendance de ASP.NET Core pour associer le type options (telles que `MyMiddlewareOptions`) avec un `MyMiddlewareOptions` présentant les options de.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-205">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="dbbc2-206">Mise à jour votre `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-206">Update your `Startup` class:</span></span>

    1.  <span data-ttu-id="dbbc2-207">Si vous utilisez *appsettings.json*, ajoutez-le au Générateur de configuration dans le `Startup` constructeur :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-207">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      <span data-ttu-id="dbbc2-208">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-208">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]</span></span>

    2.  <span data-ttu-id="dbbc2-209">Configurer le service d’options :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-209">Configure the options service:</span></span>

      <span data-ttu-id="dbbc2-210">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-210">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]</span></span>

    3.  <span data-ttu-id="dbbc2-211">Associer vos options à votre classe d’options :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-211">Associate your options with your options class:</span></span>

      <span data-ttu-id="dbbc2-212">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-212">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]</span></span>

4.  <span data-ttu-id="dbbc2-213">Injecter les options dans votre constructeur d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-213">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="dbbc2-214">Cela est similaire à l’injection d’options dans un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-214">This is similar to injecting options into a controller.</span></span>

  <span data-ttu-id="dbbc2-215">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-215">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]</span></span>

  <span data-ttu-id="dbbc2-216">Le [UseMiddleware](#http-modules-usemiddleware) méthode d’extension qui ajoute votre intergiciel (middleware) pour le `IApplicationBuilder` prend en charge l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-216">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

  <span data-ttu-id="dbbc2-217">Cela n’est pas limitée à `IOptions` objets.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-217">This is not limited to `IOptions` objects.</span></span> <span data-ttu-id="dbbc2-218">De cette manière peut être ajoutée à n’importe quel autre objet nécessitant votre intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-218">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="dbbc2-219">Chargement des options d’intergiciel (middleware) par le biais d’injection directe</span><span class="sxs-lookup"><span data-stu-id="dbbc2-219">Loading middleware options through direct injection</span></span>

<span data-ttu-id="dbbc2-220">Le modèle d’options présente l’avantage qu’il crée le faible couplage entre les valeurs des options et leurs consommateurs.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-220">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="dbbc2-221">Une fois que vous avez associé une classe d’options avec les valeurs d’options réel, toute autre classe peut accéder aux options via l’infrastructure d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-221">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="dbbc2-222">Il n’est pas nécessaire de passer autour des valeurs d’options.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-222">There is no need to pass around options values.</span></span>

<span data-ttu-id="dbbc2-223">Cela répartit bien que si vous souhaitez utiliser l’intergiciel (middleware) même à deux reprises, avec différentes options.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-223">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="dbbc2-224">Par exemple un intergiciel d’autorisation utilisé dans différentes branches autorisant les différents rôles.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-224">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="dbbc2-225">Vous ne pouvez pas associer deux objets de différentes options la classe d’une options.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-225">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="dbbc2-226">La solution consiste à obtenir les objets d’options avec les valeurs d’options réelles dans votre `Startup` classe et transfèrent ces informations directement à chaque instance de votre intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-226">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1.  <span data-ttu-id="dbbc2-227">Ajouter une deuxième clé à *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="dbbc2-227">Add a second key to *appsettings.json*</span></span>

    <span data-ttu-id="dbbc2-228">Pour ajouter un deuxième ensemble d’options pour le *appsettings.json* , utilisez une nouvelle clé pour identifier de manière unique :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-228">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

    <span data-ttu-id="dbbc2-229">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-229">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]</span></span>

2.  <span data-ttu-id="dbbc2-230">Récupérer les valeurs des options et les passer à un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-230">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="dbbc2-231">Le `Use...` méthode d’extension (ce qui ajoute votre intergiciel (middleware) au pipeline) est un emplacement logique pour transmettre les valeurs d’option :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-231">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

    <span data-ttu-id="dbbc2-232">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-232">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]</span></span>

4.  <span data-ttu-id="dbbc2-233">Activer l’intergiciel (middleware) prendre un paramètre options.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-233">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="dbbc2-234">Fournissez une surcharge de la `Use...` méthode d’extension (qui prend le paramètre options et passe à `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-234">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="dbbc2-235">Lorsque `UseMiddleware` est appelée avec des paramètres, il transmet les paramètres à votre constructeur d’intergiciel (middleware) lorsqu’il instancie l’objet de l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-235">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

    <span data-ttu-id="dbbc2-236">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-236">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]</span></span>

    <span data-ttu-id="dbbc2-237">Notez comment cela encapsule l’objet d’options dans un `OptionsWrapper` objet.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-237">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="dbbc2-238">Il implémente `IOptions`, comme prévu par le constructeur d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-238">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="dbbc2-239">Migration vers le nouveau HttpContext</span><span class="sxs-lookup"><span data-stu-id="dbbc2-239">Migrating to the new HttpContext</span></span>

<span data-ttu-id="dbbc2-240">Vous savez que la `Invoke` méthode dans votre intergiciel (middleware) prend un paramètre de type `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="dbbc2-240">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="dbbc2-241">`HttpContext`a changé de manière significative dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-241">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="dbbc2-242">Cette section montre comment traduire les propriétés couramment utilisées de [System.Web.HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.aspx) au nouveau `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-242">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.aspx) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="dbbc2-243">HttpContext</span><span class="sxs-lookup"><span data-stu-id="dbbc2-243">HttpContext</span></span>

<span data-ttu-id="dbbc2-244">**HttpContext.Items** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-244">**HttpContext.Items** translates to:</span></span>

<span data-ttu-id="dbbc2-245">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-245">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]</span></span>

<span data-ttu-id="dbbc2-246">**ID de demande unique (pas d’équivalent System.Web.HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="dbbc2-246">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="dbbc2-247">Vous donne un id unique pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-247">Gives you a unique id for each request.</span></span> <span data-ttu-id="dbbc2-248">Très utiles à inclure dans vos journaux.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-248">Very useful to include in your logs.</span></span>

<span data-ttu-id="dbbc2-249">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-249">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]</span></span>

### <a name="httpcontextrequest"></a><span data-ttu-id="dbbc2-250">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="dbbc2-250">HttpContext.Request</span></span>

<span data-ttu-id="dbbc2-251">**HttpContext.Request.HttpMethod** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-251">**HttpContext.Request.HttpMethod** translates to:</span></span>

<span data-ttu-id="dbbc2-252">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-252">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]</span></span>

<span data-ttu-id="dbbc2-253">**HttpContext.Request.QueryString** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-253">**HttpContext.Request.QueryString** translates to:</span></span>

<span data-ttu-id="dbbc2-254">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-254">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]</span></span>

<span data-ttu-id="dbbc2-255">**HttpContext.Request.Url** et **HttpContext.Request.RawUrl** traduire en :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-255">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

<span data-ttu-id="dbbc2-256">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-256">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]</span></span>

<span data-ttu-id="dbbc2-257">**HttpContext.Request.IsSecureConnection** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-257">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

<span data-ttu-id="dbbc2-258">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-258">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]</span></span>

<span data-ttu-id="dbbc2-259">**HttpContext.Request.UserHostAddress** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-259">**HttpContext.Request.UserHostAddress** translates to:</span></span>

<span data-ttu-id="dbbc2-260">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-260">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]</span></span>

<span data-ttu-id="dbbc2-261">**HttpContext.Request.Cookies** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-261">**HttpContext.Request.Cookies** translates to:</span></span>

<span data-ttu-id="dbbc2-262">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-262">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]</span></span>

<span data-ttu-id="dbbc2-263">**HttpContext.Request.RequestContext.RouteData** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-263">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

<span data-ttu-id="dbbc2-264">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-264">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]</span></span>

<span data-ttu-id="dbbc2-265">**HttpContext.Request.Headers** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-265">**HttpContext.Request.Headers** translates to:</span></span>

<span data-ttu-id="dbbc2-266">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-266">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]</span></span>

<span data-ttu-id="dbbc2-267">**HttpContext.Request.UserAgent** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-267">**HttpContext.Request.UserAgent** translates to:</span></span>

<span data-ttu-id="dbbc2-268">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-268">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]</span></span>

<span data-ttu-id="dbbc2-269">**HttpContext.Request.UrlReferrer** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-269">**HttpContext.Request.UrlReferrer** translates to:</span></span>

<span data-ttu-id="dbbc2-270">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-270">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]</span></span>

<span data-ttu-id="dbbc2-271">**HttpContext.Request.ContentType** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-271">**HttpContext.Request.ContentType** translates to:</span></span>

<span data-ttu-id="dbbc2-272">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-272">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]</span></span>

<span data-ttu-id="dbbc2-273">**HttpContext.Request.Form** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-273">**HttpContext.Request.Form** translates to:</span></span>

<span data-ttu-id="dbbc2-274">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-274">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]</span></span>

> [!WARNING]
> <span data-ttu-id="dbbc2-275">Lire les valeurs de formulaire uniquement si le type de contenu sub est *x--www-form-urlencoded* ou *des données de formulaire*.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-275">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="dbbc2-276">**HttpContext.Request.InputStream** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-276">**HttpContext.Request.InputStream** translates to:</span></span>

<span data-ttu-id="dbbc2-277">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-277">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]</span></span>

> [!WARNING]
> <span data-ttu-id="dbbc2-278">Utilisez ce code uniquement dans un gestionnaire type intergiciel (middleware), à la fin d’un pipeline.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-278">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="dbbc2-279">Vous pouvez lire le corps brut comme indiqué plus haut qu’une seule fois par requête.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-279">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="dbbc2-280">Intergiciel (middleware) tente de lire le corps après la première lecture lira le corps est vide.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-280">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="dbbc2-281">Cela ne s’applique pas à la lecture d’un formulaire comme indiqué précédemment, qui est effectuée à partir d’une mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-281">This does not apply to reading a form as shown earlier, because that is done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="dbbc2-282">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="dbbc2-282">HttpContext.Response</span></span>

<span data-ttu-id="dbbc2-283">**HttpContext.Response.Status** et **HttpContext.Response.StatusDescription** traduire en :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-283">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

<span data-ttu-id="dbbc2-284">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-284">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]</span></span>

<span data-ttu-id="dbbc2-285">**HttpContext.Response.ContentEncoding** et **HttpContext.Response.ContentType** traduire en :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-285">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

<span data-ttu-id="dbbc2-286">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-286">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]</span></span>

<span data-ttu-id="dbbc2-287">**HttpContext.Response.ContentType** sur son propre également se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-287">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

<span data-ttu-id="dbbc2-288">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-288">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]</span></span>

<span data-ttu-id="dbbc2-289">**HttpContext.Response.Output** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-289">**HttpContext.Response.Output** translates to:</span></span>

<span data-ttu-id="dbbc2-290">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-290">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]</span></span>

<span data-ttu-id="dbbc2-291">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="dbbc2-291">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="dbbc2-292">Accès à un fichier est décrite [ici](../fundamentals/request-features.md#middleware-and-request-features).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-292">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="dbbc2-293">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="dbbc2-293">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="dbbc2-294">Envoi des en-têtes de réponse est compliqué par le fait que si vous leur attribuez une fois que tout a été écrite dans le corps de réponse, ils n’être envoyées.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-294">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="dbbc2-295">La solution consiste à définir une méthode de rappel qui sera appelée droite avant d’écrire sur le démarrage de la réponse.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-295">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="dbbc2-296">Cette opération s’effectue mieux au début de la `Invoke` méthode dans votre intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dbbc2-296">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="dbbc2-297">Il s’agit de cette méthode de rappel qui définit les en-têtes de réponse.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-297">It is this callback method that sets your response headers.</span></span>

<span data-ttu-id="dbbc2-298">Le code suivant définit une méthode de rappel appelée `SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="dbbc2-298">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="dbbc2-299">Le `SetHeaders` méthode de rappel ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-299">The `SetHeaders` callback method would look like this:</span></span>

<span data-ttu-id="dbbc2-300">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-300">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]</span></span>

<span data-ttu-id="dbbc2-301">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="dbbc2-301">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="dbbc2-302">Les cookies sont transmis au navigateur dans un *Set-Cookie* en-tête de réponse.</span><span class="sxs-lookup"><span data-stu-id="dbbc2-302">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="dbbc2-303">Par conséquent, envoi de cookies nécessite le rappel de même que celle utilisée pour l’envoi des en-têtes de réponse :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-303">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="dbbc2-304">Le `SetCookies` méthode de rappel se présente comme suit :</span><span class="sxs-lookup"><span data-stu-id="dbbc2-304">The `SetCookies` callback method would look like the following:</span></span>

<span data-ttu-id="dbbc2-305">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]</span><span class="sxs-lookup"><span data-stu-id="dbbc2-305">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbbc2-306">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="dbbc2-306">Additional Resources</span></span>

* [<span data-ttu-id="dbbc2-307">Vue d’ensemble des Modules HTTP et les gestionnaires HTTP</span><span class="sxs-lookup"><span data-stu-id="dbbc2-307">HTTP Handlers and HTTP Modules Overview</span></span>](https://msdn.microsoft.com/library/bb398986.aspx)

* [<span data-ttu-id="dbbc2-308">Configuration</span><span class="sxs-lookup"><span data-stu-id="dbbc2-308">Configuration</span></span>](../fundamentals/configuration.md)

* [<span data-ttu-id="dbbc2-309">Démarrage d’une application</span><span class="sxs-lookup"><span data-stu-id="dbbc2-309">Application Startup</span></span>](../fundamentals/startup.md)

* [<span data-ttu-id="dbbc2-310">Intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="dbbc2-310">Middleware</span></span>](../fundamentals/middleware.md)

---
title: "Injection de dépendance dans les contrôleurs"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bc8b4ba3-e9ba-48fd-b1eb-cd48ff6bc7a1
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: ff0a1a34ee6b025be6312a81f1a0bcdd07026adb
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="dependency-injection-into-controllers"></a><span data-ttu-id="c8bfc-103">Injection de dépendance dans les contrôleurs</span><span class="sxs-lookup"><span data-stu-id="c8bfc-103">Dependency injection into controllers</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="c8bfc-104">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c8bfc-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c8bfc-105">Les contrôleurs MVC ASP.NET Core doivent demander leurs dépendances explicitement par le biais de leurs constructeurs.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-105">ASP.NET Core MVC controllers should request their dependencies explicitly via their constructors.</span></span> <span data-ttu-id="c8bfc-106">Dans certains cas, les actions de contrôleur individuels peuvent nécessiter un service, et il ne peut pas judicieux de demander au niveau du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-106">In some instances, individual controller actions may require a service, and it may not make sense to request at the controller level.</span></span> <span data-ttu-id="c8bfc-107">Dans ce cas, vous pouvez également choisir d’injecter un service en tant que paramètre dans la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-107">In this case, you can also choose to inject a service as a parameter on the action method.</span></span>

<span data-ttu-id="c8bfc-108">[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c8bfc-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="c8bfc-109">Injection de dépendance</span><span class="sxs-lookup"><span data-stu-id="c8bfc-109">Dependency Injection</span></span>

<span data-ttu-id="c8bfc-110">Injection de dépendances est une technique qui suit le [principe d’Inversion de dépendance](http://deviq.com/dependency-inversion-principle/), ce qui permet aux applications de se composer de modules faiblement couplées.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-110">Dependency injection is a technique that follows the [Dependency Inversion Principle](http://deviq.com/dependency-inversion-principle/), allowing for applications to be composed of loosely coupled modules.</span></span> <span data-ttu-id="c8bfc-111">ASP.NET Core a une prise en charge intégrée pour [injection de dépendance](../../fundamentals/dependency-injection.md), ce qui facilite la tester et à gérer des applications.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-111">ASP.NET Core has built-in support for [dependency injection](../../fundamentals/dependency-injection.md), which makes applications easier to test and maintain.</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="c8bfc-112">Injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="c8bfc-112">Constructor Injection</span></span>

<span data-ttu-id="c8bfc-113">Prise en charge intégrée de d’ASP.NET Core pour l’injection de dépendance basés sur le constructeur s’étend à contrôleurs MVC.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-113">ASP.NET Core's built-in support for constructor-based dependency injection extends to MVC controllers.</span></span> <span data-ttu-id="c8bfc-114">En ajoutant simplement un type de service à votre contrôleur en tant que paramètre de constructeur, ASP.NET Core va tenter de résoudre ce type à l’aide de sa propre dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-114">By simply adding a service type to your controller as a constructor parameter, ASP.NET Core will attempt to resolve that type using its built in service container.</span></span> <span data-ttu-id="c8bfc-115">Les services sont en général, mais pas toujours, définis à l’aide des interfaces.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-115">Services are typically, but not always, defined using interfaces.</span></span> <span data-ttu-id="c8bfc-116">Par exemple, si votre application dispose d’une logique métier qui dépend de l’heure actuelle, vous pouvez injecter un service qui Récupère l’heure (plutôt que le codage en dur il), ce qui permettrait de vos tests à passer dans les implémentations qui utilisent une heure spécifique.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-116">For example, if your application has business logic that depends on the current time, you can inject a service that retrieves the time (rather than hard-coding it), which would allow your tests to pass in implementations that use a set time.</span></span>

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


<span data-ttu-id="c8bfc-117">Implémentation d’une interface comme celle-ci afin qu’il utilise l’horloge système lors de l’exécution est simple :</span><span class="sxs-lookup"><span data-stu-id="c8bfc-117">Implementing an interface like this one so that it uses the system clock at runtime is trivial:</span></span>

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


<span data-ttu-id="c8bfc-118">Tout cela en place, nous pouvons utiliser le service dans notre contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-118">With this in place, we can use the service in our controller.</span></span> <span data-ttu-id="c8bfc-119">Dans ce cas, nous avons ajouté une logique pour le `HomeController` `Index` méthode pour afficher un message d’accueil à l’utilisateur en fonction de l’heure du jour.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-119">In this case, we have added some logic to the `HomeController` `Index` method to display a greeting to the user based on the time of day.</span></span>

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

<span data-ttu-id="c8bfc-120">Si nous exécutons l’application maintenant, nous êtes plus susceptible de rencontrer une erreur :</span><span class="sxs-lookup"><span data-stu-id="c8bfc-120">If we run the application now, we will most likely encounter an error:</span></span>

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

<span data-ttu-id="c8bfc-121">Cette erreur se produit lorsque nous n’avons pas configuré un service dans le `ConfigureServices` méthode dans notre `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-121">This error occurs when we have not configured a service in the `ConfigureServices` method in our `Startup` class.</span></span> <span data-ttu-id="c8bfc-122">Pour spécifier que les demandes de `IDateTime` doivent être résolus à l’aide d’une instance de `SystemDateTime`, ajoutez la ligne en surbrillance dans la liste ci-dessous pour votre `ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="c8bfc-122">To specify that requests for `IDateTime` should be resolved using an instance of `SystemDateTime`, add the highlighted line in the listing below to your `ConfigureServices` method:</span></span>

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> <span data-ttu-id="c8bfc-123">Ce service particulier peut être implémenté à l’aide de plusieurs options de durée de vie différente (`Transient`, `Scoped`, ou `Singleton`).</span><span class="sxs-lookup"><span data-stu-id="c8bfc-123">This particular service could be implemented using any of several different lifetime options (`Transient`, `Scoped`, or `Singleton`).</span></span> <span data-ttu-id="c8bfc-124">Consultez [Injection de dépendances](../../fundamentals/dependency-injection.md) de comprendre comment chacune de ces options d’étendue affecte le comportement de votre service.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-124">See [Dependency Injection](../../fundamentals/dependency-injection.md) to understand how each of these scope options will affect the behavior of your service.</span></span>

<span data-ttu-id="c8bfc-125">Une fois que le service a été configuré, l’application en cours d’exécution et en accédant à la page d’accueil doivent s’afficher le message temporels comme prévu :</span><span class="sxs-lookup"><span data-stu-id="c8bfc-125">Once the service has been configured, running the application and navigating to the home page should display the time-based message as expected:</span></span>

![Message d’accueil du serveur](dependency-injection/_static/server-greeting.png)

>[!TIP]
> <span data-ttu-id="c8bfc-127">Consultez [logique du contrôleur de test](testing.md) pour savoir comment demander explicitement les dépendances [http://deviq.com/explicit-dependencies-principle/](http://deviq.com/explicit-dependencies-principle/) dans les contrôleurs rend le code plus facile à tester.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-127">See [Testing Controller Logic](testing.md) to learn how to explicitly request dependencies [http://deviq.com/explicit-dependencies-principle/](http://deviq.com/explicit-dependencies-principle/) in controllers makes code easier to test.</span></span>

<span data-ttu-id="c8bfc-128">Injection de dépendance intégrée d’ASP.NET Core qu’un seul constructeur pour les classes de demande de services prend en charge.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-128">ASP.NET Core's built-in dependency injection supports having only a single constructor for classes requesting services.</span></span> <span data-ttu-id="c8bfc-129">Si vous avez plus d’un constructeur, vous pouvez recevoir une exception indiquant :</span><span class="sxs-lookup"><span data-stu-id="c8bfc-129">If you have more than one constructor, you may get an exception stating:</span></span>

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

<span data-ttu-id="c8bfc-130">Comme le message d’erreur indique, vous pouvez corriger ce problème ayant uniquement un seul constructeur.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-130">As the error message states, you can correct this problem having just a single constructor.</span></span> <span data-ttu-id="c8bfc-131">Vous pouvez également [remplacer la prise en charge de l’injection de dépendances par défaut avec une implémentation d’un tiers](../../fundamentals/dependency-injection.md#replacing-the-default-services-container), la plupart des qui prennent en charge plusieurs constructeurs.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-131">You can also [replace the default dependency injection support with a third party implementation](../../fundamentals/dependency-injection.md#replacing-the-default-services-container), many of which support multiple constructors.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="c8bfc-132">Injection d’action avec FromServices</span><span class="sxs-lookup"><span data-stu-id="c8bfc-132">Action Injection with FromServices</span></span>

<span data-ttu-id="c8bfc-133">Parfois, vous n’avez pas besoin d’un service pour plusieurs actions dans votre contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-133">Sometimes you don't need a service for more than one action within your controller.</span></span> <span data-ttu-id="c8bfc-134">Dans ce cas, il peut être judicieux d’injecter du service en tant que paramètre à la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-134">In this case, it may make sense to inject the service as a parameter to the action method.</span></span> <span data-ttu-id="c8bfc-135">Cela en marquant le paramètre avec l’attribut `[FromServices]` comme indiqué ici :</span><span class="sxs-lookup"><span data-stu-id="c8bfc-135">This is done by marking the parameter with the attribute `[FromServices]` as shown here:</span></span>

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a><span data-ttu-id="c8bfc-136">L’accès aux paramètres d’un contrôleur</span><span class="sxs-lookup"><span data-stu-id="c8bfc-136">Accessing Settings from a Controller</span></span>

<span data-ttu-id="c8bfc-137">L’accès aux paramètres de configuration ou d’application à partir d’un contrôleur est un modèle commun.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-137">Accessing application or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="c8bfc-138">Cet accès doit utiliser le modèle des Options décrit dans [configuration](../../fundamentals/configuration.md).</span><span class="sxs-lookup"><span data-stu-id="c8bfc-138">This access should use the Options pattern described in [configuration](../../fundamentals/configuration.md).</span></span> <span data-ttu-id="c8bfc-139">Vous généralement ne devez pas demander les paramètres directement à partir de votre contrôleur à l’aide de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-139">You generally should not request settings directly from your controller using dependency injection.</span></span> <span data-ttu-id="c8bfc-140">Une meilleure approche consiste à demande un `IOptions<T>` instance, où `T` est la classe de configuration que vous avez besoin.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-140">A better approach is to request an `IOptions<T>` instance, where `T` is the configuration class you need.</span></span>

<span data-ttu-id="c8bfc-141">Pour utiliser le modèle d’options, vous devez créer une classe qui représente les options, comme celle-ci :</span><span class="sxs-lookup"><span data-stu-id="c8bfc-141">To work with the options pattern, you need to create a class that represents the options, such as this one:</span></span>

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

<span data-ttu-id="c8bfc-142">Vous devez configurer l’application pour utiliser le modèle d’options et ajoutez votre classe de configuration à la collection de services dans `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c8bfc-142">Then you need to configure the application to use the options model and add your configuration class to the services collection in `ConfigureServices`:</span></span>

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> <span data-ttu-id="c8bfc-143">Dans la liste ci-dessus, nous examinons la configuration de l’application de lire les paramètres à partir d’un fichier au format JSON.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-143">In the above listing, we are configuring the application to read the settings from a JSON-formatted file.</span></span> <span data-ttu-id="c8bfc-144">Vous pouvez également configurer les paramètres entièrement dans le code, comme indiqué dans le code commenté ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-144">You can also configure the settings entirely in code, as is shown in the commented code above.</span></span> <span data-ttu-id="c8bfc-145">Consultez [Configuration](../../fundamentals/configuration.md) pour d’autres options de configuration.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-145">See [Configuration](../../fundamentals/configuration.md) for further configuration options.</span></span>

<span data-ttu-id="c8bfc-146">Une fois que vous avez spécifié un objet fortement typé de configuration (dans ce cas, `SampleWebSettings`) et ajouté à la collection de services, vous pouvez demander qu’il à partir de n’importe quelle méthode de contrôleur ou d’Action en demandant une instance de `IOptions<T>` (dans ce cas, `IOptions<SampleWebSettings>`) .</span><span class="sxs-lookup"><span data-stu-id="c8bfc-146">Once you've specified a strongly-typed configuration object (in this case, `SampleWebSettings`) and added it to the services collection, you can request it from any Controller or Action method by requesting an instance of `IOptions<T>` (in this case, `IOptions<SampleWebSettings>`).</span></span> <span data-ttu-id="c8bfc-147">Le code suivant montre comment une demande les paramètres à partir d’un contrôleur :</span><span class="sxs-lookup"><span data-stu-id="c8bfc-147">The following code shows how one would request the settings from a controller:</span></span>

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

<span data-ttu-id="c8bfc-148">Suivant le modèle Options permet de paramètres et la configuration être dissocié d’uns et assure le contrôleur est [séparation des préoccupations](http://deviq.com/separation-of-concerns/), car il n’a pas besoin de connaître l’emplacement ou pour rechercher les paramètres plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-148">Following the Options pattern allows settings and configuration to be decoupled from one another, and ensures the controller is following [separation of concerns](http://deviq.com/separation-of-concerns/), since it doesn't need to know how or where to find the settings information.</span></span> <span data-ttu-id="c8bfc-149">Elle simplifie également le contrôleur de test unitaire [logique du contrôleur de test](testing.md), car il est sans [adhésives statique](http://deviq.com/static-cling/) ou direct lors de l’instanciation de classes de paramètres au sein de la classe de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c8bfc-149">It also makes the controller easier to unit test [Testing Controller Logic](testing.md), since there is no [static cling](http://deviq.com/static-cling/) or direct instantiation of settings classes within the controller class.</span></span>

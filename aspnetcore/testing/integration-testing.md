---
title: "Tests d’intégration dans ASP.NET Core"
author: ardalis
description: "Comment utiliser ASP.NET Core tests d’intégration pour vous assurer que les composants d’une application fonctionnent correctement."
keywords: "ASP.NET Core, Razor, tests d’intégration"
ms.author: riande
manager: wpickett
ms.date: 09/25/2017
ms.topic: article
ms.assetid: 40d534f2-89b3-4b09-9c2c-3494bf9991c9
ms.technology: aspnet
ms.prod: asp.net-core
uid: testing/integration-testing
ms.openlocfilehash: 155fd2f0663c6225531a4df6f323ebb30ab1ee73
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2017
---
# <a name="integration-testing-in-aspnet-core"></a><span data-ttu-id="b67ab-104">Tests d’intégration dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b67ab-104">Integration testing in ASP.NET Core</span></span>

<span data-ttu-id="b67ab-105">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b67ab-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b67ab-106">Tests d’intégration garantit que les composants d’une application fonctionnent correctement lors de l’assemblage.</span><span class="sxs-lookup"><span data-stu-id="b67ab-106">Integration testing ensures that an application's components function correctly when assembled together.</span></span> <span data-ttu-id="b67ab-107">ASP.NET Core prend en charge aux tests d’intégration à l’aide des infrastructures de tests unitaires et un hôte web test intégré qui peut être utilisé pour gérer les demandes sans la surcharge du réseau.</span><span class="sxs-lookup"><span data-stu-id="b67ab-107">ASP.NET Core supports integration testing using unit test frameworks and a built-in test web host that can be used to handle requests without network overhead.</span></span>

<span data-ttu-id="b67ab-108">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/testing/integration-testing/sample) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b67ab-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/testing/integration-testing/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="introduction-to-integration-testing"></a><span data-ttu-id="b67ab-109">Introduction aux tests d’intégration</span><span class="sxs-lookup"><span data-stu-id="b67ab-109">Introduction to integration testing</span></span>

<span data-ttu-id="b67ab-110">Tests d’intégration vérifient que les différentes parties d’une application fonctionnent correctement ensemble.</span><span class="sxs-lookup"><span data-stu-id="b67ab-110">Integration tests verify that different parts of an application work correctly together.</span></span> <span data-ttu-id="b67ab-111">Contrairement aux [test unitaire](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test), tests d’intégration impliquent souvent des problèmes d’infrastructure application, par exemple une base de données, système de fichiers, les ressources réseau, ou de requêtes et réponses web.</span><span class="sxs-lookup"><span data-stu-id="b67ab-111">Unlike [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test), integration tests frequently involve application infrastructure concerns, such as a database, file system, network resources, or web requests and responses.</span></span> <span data-ttu-id="b67ab-112">Tests unitaires utilisent fakes ou objets fictifs à la place de ces problèmes, mais l’objectif de tests d’intégration consiste à vérifier que le système fonctionne comme prévu avec ces systèmes.</span><span class="sxs-lookup"><span data-stu-id="b67ab-112">Unit tests use fakes or mock objects in place of these concerns, but the purpose of integration tests is to confirm that the system works as expected with these systems.</span></span>

<span data-ttu-id="b67ab-113">Tests d’intégration, car ils exercent des segments de code de plus grande et car elles reposent sur les éléments d’infrastructure, ont tendance à être beaucoup plus lente que les tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="b67ab-113">Integration tests, because they exercise larger segments of code and because they rely on infrastructure elements, tend to be orders of magnitude slower than unit tests.</span></span> <span data-ttu-id="b67ab-114">Par conséquent, il est judicieux de limiter le nombre de tests intégration que vous écrivez, surtout si vous pouvez tester le comportement de même avec un test unitaire.</span><span class="sxs-lookup"><span data-stu-id="b67ab-114">Thus, it's a good idea to limit how many integration tests you write, especially if you can test the same behavior with a unit test.</span></span>

> [!NOTE]
> <span data-ttu-id="b67ab-115">Si un comportement peut être testé à l’aide d’un test unitaire ou un test d’intégration, préférez le test unitaire, puisqu’il est presque toujours être plus rapide.</span><span class="sxs-lookup"><span data-stu-id="b67ab-115">If some behavior can be tested using either a unit test or an integration test, prefer the unit test, since it will be almost always be faster.</span></span> <span data-ttu-id="b67ab-116">Vous pouvez avoir des dizaines ou des centaines de tests unitaires avec de nombreuses entrées différentes mais quelques tests d’intégration qui couvrent les scénarios plus importantes.</span><span class="sxs-lookup"><span data-stu-id="b67ab-116">You might have dozens or hundreds of unit tests with many different inputs but just a handful of integration tests covering the most important scenarios.</span></span>

<span data-ttu-id="b67ab-117">Pour tester la logique dans vos propres méthodes est généralement le domaine des tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="b67ab-117">Testing the logic within your own methods is usually the domain of unit tests.</span></span> <span data-ttu-id="b67ab-118">Tester le fonctionnement de votre application au sein de son infrastructure, par exemple ASP.NET Core ou avec une base de données est où tests d’intégration entrent en jeu.</span><span class="sxs-lookup"><span data-stu-id="b67ab-118">Testing how your application works within its framework, for example with ASP.NET Core, or with a database is where integration tests come into play.</span></span> <span data-ttu-id="b67ab-119">Il ne faut pas trop de tests d’intégration pour confirmer que vous êtes en mesure d’écrire une ligne de la base de données et les lire.</span><span class="sxs-lookup"><span data-stu-id="b67ab-119">It doesn't take too many integration tests to confirm that you're able to write a row to the database and read it back.</span></span> <span data-ttu-id="b67ab-120">Vous n’avez pas besoin de tester chaque combinaison possible de votre code d’accès aux données, vous devez seulement tester suffisamment pour garantir que votre application fonctionne correctement.</span><span class="sxs-lookup"><span data-stu-id="b67ab-120">You don't need to test every possible permutation of your data access code - you only need to test enough to give you confidence that your application is working properly.</span></span>

## <a name="integration-testing-aspnet-core"></a><span data-ttu-id="b67ab-121">Intégration de test ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b67ab-121">Integration testing ASP.NET Core</span></span>

<span data-ttu-id="b67ab-122">Pour obtenir défini pour les tests d’intégration d’exécution, vous devez créer un projet de test, ajoutez une référence à votre projet de web ASP.NET Core et installer un test runner.</span><span class="sxs-lookup"><span data-stu-id="b67ab-122">To get set up to run integration tests, you'll need to create a test project, add a reference to your ASP.NET Core web project, and install a test runner.</span></span> <span data-ttu-id="b67ab-123">Ce processus est décrit dans la [test unitaire](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation, ainsi que des instructions plus détaillées sur l’exécution de tests et des recommandations pour nommer vos tests et les classes de test.</span><span class="sxs-lookup"><span data-stu-id="b67ab-123">This process is described in the [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation, along with more detailed instructions on running tests and recommendations for naming your tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="b67ab-124">Séparez vos tests unitaires et vos tests d’intégration à l’aide de différents projets.</span><span class="sxs-lookup"><span data-stu-id="b67ab-124">Separate your unit tests and your integration tests using different projects.</span></span> <span data-ttu-id="b67ab-125">Cela permet de s’assurer que vous ne par accident introduire des problèmes d’infrastructure dans vos tests unitaires et vous permet de facilement choisir l’ensemble des tests à exécuter.</span><span class="sxs-lookup"><span data-stu-id="b67ab-125">This helps ensure you don't accidentally introduce infrastructure concerns into your unit tests and lets you easily choose which set of tests to run.</span></span>

### <a name="the-test-host"></a><span data-ttu-id="b67ab-126">L’hôte de Test</span><span class="sxs-lookup"><span data-stu-id="b67ab-126">The Test Host</span></span>

<span data-ttu-id="b67ab-127">ASP.NET Core inclut un hôte de test qui peut être ajouté aux projets de test d’intégration et utilisé pour héberger le ASP.NET Core applications, les demandes de service de test sans la nécessité d’un hôte web réel.</span><span class="sxs-lookup"><span data-stu-id="b67ab-127">ASP.NET Core includes a test host that can be added to integration test projects and used to host ASP.NET Core applications, serving test requests without the need for a real web host.</span></span> <span data-ttu-id="b67ab-128">L’exemple fourni inclut un projet de test d’intégration qui a été configuré pour utiliser [xUnit](https://xunit.github.io) et l’hôte de Test.</span><span class="sxs-lookup"><span data-stu-id="b67ab-128">The provided sample includes an integration test project which has been configured to use [xUnit](https://xunit.github.io) and the Test Host.</span></span> <span data-ttu-id="b67ab-129">Elle utilise le `Microsoft.AspNetCore.TestHost` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="b67ab-129">It uses the `Microsoft.AspNetCore.TestHost` NuGet package.</span></span>

<span data-ttu-id="b67ab-130">Une fois la `Microsoft.AspNetCore.TestHost` package est inclus dans le projet, vous serez en mesure de créer et configurer un `TestServer` dans vos tests.</span><span class="sxs-lookup"><span data-stu-id="b67ab-130">Once the `Microsoft.AspNetCore.TestHost` package is included in the project, you'll be able to create and configure a `TestServer` in your tests.</span></span> <span data-ttu-id="b67ab-131">Le test suivant montre comment vérifier qu’une demande adressée à la racine d’un site renvoie « Hello World ! »</span><span class="sxs-lookup"><span data-stu-id="b67ab-131">The following test shows how to verify that a request made to the root of a site returns "Hello World!"</span></span> <span data-ttu-id="b67ab-132">et doit s’exécuter avec succès par rapport à la valeur par défaut modèle ASP.NET Core Web vide créé par Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b67ab-132">and should run successfully against the default ASP.NET Core Empty Web template created by Visual Studio.</span></span>

[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebDefaultRequestShould.cs?name=snippet_WebDefault&highlight=7,16,22)]

<span data-ttu-id="b67ab-133">Ce test utilise le modèle de disposition Act-Assert.</span><span class="sxs-lookup"><span data-stu-id="b67ab-133">This test is using the Arrange-Act-Assert pattern.</span></span> <span data-ttu-id="b67ab-134">L’étape d’organisation est effectuée dans le constructeur, ce qui crée une instance de `TestServer`.</span><span class="sxs-lookup"><span data-stu-id="b67ab-134">The Arrange step is done in the constructor, which creates an instance of `TestServer`.</span></span> <span data-ttu-id="b67ab-135">Configuré `WebHostBuilder` permet de créer un `TestHost`; dans cet exemple, le `Configure` (méthode) à partir du système sous test (SUT) `Startup` classe est passée à la `WebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b67ab-135">A configured `WebHostBuilder` will be used to create a `TestHost`; in this example, the `Configure` method from the system under test (SUT)'s `Startup` class is passed to the `WebHostBuilder`.</span></span> <span data-ttu-id="b67ab-136">Cette méthode permet de configurer le pipeline de demande de la `TestServer` identique à la façon dont le serveur SUT serait configuré.</span><span class="sxs-lookup"><span data-stu-id="b67ab-136">This method will be used to configure the request pipeline of the `TestServer` identically to how the SUT server would be configured.</span></span>

<span data-ttu-id="b67ab-137">Dans la partie Act du test, une demande est faite pour le `TestServer` instance pour le chemin d’accès « / » et la réponse est en lecture en une chaîne.</span><span class="sxs-lookup"><span data-stu-id="b67ab-137">In the Act portion of the test, a request is made to the `TestServer` instance for the "/" path, and the response is read back into a string.</span></span> <span data-ttu-id="b67ab-138">Cette chaîne est comparée à la chaîne attendue de « Hello World ! ».</span><span class="sxs-lookup"><span data-stu-id="b67ab-138">This string is compared with the expected string of "Hello World!".</span></span> <span data-ttu-id="b67ab-139">S’ils correspondent, le test réussit ; Sinon, elle échoue.</span><span class="sxs-lookup"><span data-stu-id="b67ab-139">If they match, the test passes; otherwise, it fails.</span></span>

<span data-ttu-id="b67ab-140">Vous pouvez maintenant ajouter quelques tests d’intégration pour confirmer que la préparation de la vérification de la fonctionnalité fonctionne via l’application web :</span><span class="sxs-lookup"><span data-stu-id="b67ab-140">Now you can add a few additional integration tests to confirm that the prime checking functionality works via the web application:</span></span>

[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebCheckPrimeShould.cs?name=snippet_CheckPrime)]

<span data-ttu-id="b67ab-141">Notez que vous n’essayez pas réellement tester l’exactitude de l’outil d’analyse de nombres premiers avec ces tests mais plutôt que l’application web fait ce que vous attendez.</span><span class="sxs-lookup"><span data-stu-id="b67ab-141">Note that you're not really trying to test the correctness of the prime number checker with these tests but rather that the web application is doing what you expect.</span></span> <span data-ttu-id="b67ab-142">Vous disposez déjà de la couverture de test unitaire que vous êtes ainsi sûr dans `PrimeService`, comme vous pouvez voir ici :</span><span class="sxs-lookup"><span data-stu-id="b67ab-142">You already have unit test coverage that gives you confidence in `PrimeService`, as you can see here:</span></span>

![Explorateur de tests](integration-testing/_static/test-explorer.png)

<span data-ttu-id="b67ab-144">Plus d’informations sur les tests unitaires dans le [test unitaire](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) l’article.</span><span class="sxs-lookup"><span data-stu-id="b67ab-144">You can learn more about the unit tests in the [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) article.</span></span>


### <a name="integration-testing-mvcrazor"></a><span data-ttu-id="b67ab-145">Intégration de test Mvc/Razor</span><span class="sxs-lookup"><span data-stu-id="b67ab-145">Integration testing Mvc/Razor</span></span>

<span data-ttu-id="b67ab-146">Les projets de test qui contiennent des vues Razor requièrent `<PreserveCompilationContext>` être définie sur true dans le *.csproj* fichier :</span><span class="sxs-lookup"><span data-stu-id="b67ab-146">Test projects that contain Razor views require `<PreserveCompilationContext>` be set to true in the *.csproj* file:</span></span>


```xml
    <PreserveCompilationContext>true</PreserveCompilationContext>
```

<span data-ttu-id="b67ab-147">Projets de cet élément génère une erreur semblable au suivant :</span><span class="sxs-lookup"><span data-stu-id="b67ab-147">Projects missing this element will generate an error similar to the following:</span></span>
```
Microsoft.AspNetCore.Mvc.Razor.Compilation.CompilationFailedException: 'One or more compilation failures occurred:
ooebhccx.1bd(4,62): error CS0012: The type 'Attribute' is defined in an assembly that is not referenced. You must add a reference to assembly 'netstandard, Version=2.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'.
```


## <a name="refactoring-to-use-middleware"></a><span data-ttu-id="b67ab-148">Pour utiliser l’intergiciel (middleware) de refactorisation</span><span class="sxs-lookup"><span data-stu-id="b67ab-148">Refactoring to use middleware</span></span>

<span data-ttu-id="b67ab-149">La refactorisation est le processus de modification d’un code d’application pour améliorer sa conception sans modifier son comportement.</span><span class="sxs-lookup"><span data-stu-id="b67ab-149">Refactoring is the process of changing an application's code to improve its design without changing its behavior.</span></span> <span data-ttu-id="b67ab-150">Il doit être effectuée dans l’idéal, lorsqu’il existe une suite de transmission de tests, étant donné que ces aide garantir que le comportement du système reste le même avant et après les modifications.</span><span class="sxs-lookup"><span data-stu-id="b67ab-150">It should ideally be done when there is a suite of passing tests, since these help ensure the system's behavior remains the same before and after the changes.</span></span> <span data-ttu-id="b67ab-151">Examinez la manière dans laquelle la prime logique de contrôle est implémenté dans l’application web `Configure` (méthode), vous voyez :</span><span class="sxs-lookup"><span data-stu-id="b67ab-151">Looking at the way in which the prime checking logic is implemented in the web application's `Configure` method, you see:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.Run(async (context) =>
    {
        if (context.Request.Path.Value.Contains("checkprime"))
        {
            int numberToCheck;
            try
            {
                numberToCheck = int.Parse(context.Request.QueryString.Value.Replace("?", ""));
                var primeService = new PrimeService();
                if (primeService.IsPrime(numberToCheck))
                {
                    await context.Response.WriteAsync($"{numberToCheck} is prime!");
                }
                else
                {
                    await context.Response.WriteAsync($"{numberToCheck} is NOT prime!");
                }
            }
            catch
            {
                await context.Response.WriteAsync("Pass in a number to check in the form /checkprime?5");
            }
        }
        else
        {
            await context.Response.WriteAsync("Hello World!");
        }
    });
}
```

<span data-ttu-id="b67ab-152">Ce code fonctionne, mais il est loin d’être comment vous voulez implémenter ce genre de fonctionnalités dans une application ASP.NET Core, même simple car il s’agit.</span><span class="sxs-lookup"><span data-stu-id="b67ab-152">This code works, but it's far from how you would like to implement this kind of functionality in an ASP.NET Core application, even as simple as this is.</span></span> <span data-ttu-id="b67ab-153">Imaginez que le `Configure` méthode se présente comme si vous avez besoin pour lui ajouter ce beaucoup de code chaque fois que vous ajoutez un autre point de terminaison URL !</span><span class="sxs-lookup"><span data-stu-id="b67ab-153">Imagine what the `Configure` method would look like if you needed to add this much code to it every time you add another URL endpoint!</span></span>

<span data-ttu-id="b67ab-154">Ajout d’une option à envisager [MVC](xref:mvc/overview) à l’application et la création d’un contrôleur pour gérer la vérification de la prime.</span><span class="sxs-lookup"><span data-stu-id="b67ab-154">One option to consider is adding [MVC](xref:mvc/overview) to the application and creating a controller to handle the prime checking.</span></span> <span data-ttu-id="b67ab-155">Toutefois, si que vous n’avez pas actuellement doivent toutes les autres fonctionnalités MVC, qui sont un peu excessif.</span><span class="sxs-lookup"><span data-stu-id="b67ab-155">However, assuming you don't currently need any other MVC functionality, that's a bit overkill.</span></span>

<span data-ttu-id="b67ab-156">Vous pouvez, toutefois, parti d’ASP.NET Core [intergiciel (middleware)](xref:fundamentals/middleware), ce qui nous aideront à encapsuler la prime logique dans sa propre classe de contrôle et d’obtenir une meilleure [séparation des préoccupations](http://deviq.com/separation-of-concerns/) dans le `Configure` méthode.</span><span class="sxs-lookup"><span data-stu-id="b67ab-156">You can, however, take advantage of ASP.NET Core [middleware](xref:fundamentals/middleware), which will help us encapsulate the prime checking logic in its own class and achieve better [separation of concerns](http://deviq.com/separation-of-concerns/) in the `Configure` method.</span></span>

<span data-ttu-id="b67ab-157">Vous souhaitez autoriser le chemin d’accès de l’intergiciel (middleware) utilise pour être spécifié en tant que paramètre, la classe de l’intergiciel (middleware) attend un `RequestDelegate` et un `PrimeCheckerOptions` instance dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="b67ab-157">You want to allow the path the middleware uses to be specified as a parameter, so the middleware class expects a `RequestDelegate` and a `PrimeCheckerOptions` instance in its constructor.</span></span> <span data-ttu-id="b67ab-158">Si le chemin d’accès de la demande ne correspond pas à ce que cet intergiciel (middleware) est configuré pour attendre, vous simplement appelez l’intergiciel (middleware) suivant dans la chaîne et ne font rien de plus.</span><span class="sxs-lookup"><span data-stu-id="b67ab-158">If the path of the request doesn't match what this middleware is configured to expect, you simply call the next middleware in the chain and do nothing further.</span></span> <span data-ttu-id="b67ab-159">Le reste du code de mise en œuvre dans `Configure` figure désormais dans le `Invoke` (méthode).</span><span class="sxs-lookup"><span data-stu-id="b67ab-159">The rest of the implementation code that was in `Configure` is now in the `Invoke` method.</span></span>

> [!NOTE]
> <span data-ttu-id="b67ab-160">Étant donné que l’intergiciel (middleware) varie selon le `PrimeService` service, vous demandez également une instance de ce service avec le constructeur.</span><span class="sxs-lookup"><span data-stu-id="b67ab-160">Since the middleware depends on the `PrimeService` service, you're also requesting an instance of this service with the constructor.</span></span> <span data-ttu-id="b67ab-161">L’infrastructure fournit ce service via [Injection de dépendance](xref:fundamentals/dependency-injection), en supposant qu’il a été configuré, par exemple dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b67ab-161">The framework will provide this service via [Dependency Injection](xref:fundamentals/dependency-injection), assuming it has been configured, for example in `ConfigureServices`.</span></span>

[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Middleware/PrimeCheckerMiddleware.cs?highlight=39-63)]

<span data-ttu-id="b67ab-162">Cet intergiciel (middleware) n’agit comme un point de terminaison dans la chaîne de délégué demande lorsque son chemin d’accès correspond à, il n’existe aucun appel à `_next.Invoke` lorsque cet intergiciel (middleware) traite la demande.</span><span class="sxs-lookup"><span data-stu-id="b67ab-162">Since this middleware acts as an endpoint in the request delegate chain when its path matches, there is no call to `_next.Invoke` when this middleware handles the request.</span></span>

<span data-ttu-id="b67ab-163">Avec cet intergiciel (middleware) en place et certaines utile méthodes d’extension créés pour faciliter sa configuration, le refactorisé `Configure` méthode ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="b67ab-163">With this middleware in place and some helpful extension methods created to make configuring it easier, the refactored `Configure` method looks like this:</span></span>

[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Startup.cs?highlight=9&range=19-33)]

<span data-ttu-id="b67ab-164">Après cette refactorisation, vous êtes certain que l’application web fonctionne toujours comme avant, étant donné que vos tests d’intégration sont passage.</span><span class="sxs-lookup"><span data-stu-id="b67ab-164">Following this refactoring, you're confident that the web application still works as before, since your integration tests are all passing.</span></span>

> [!NOTE]
> <span data-ttu-id="b67ab-165">Il est conseillé de valider les modifications apportées au contrôle de code source une fois que vous effectuez une opération de refactorisation et vos tests.</span><span class="sxs-lookup"><span data-stu-id="b67ab-165">It's a good idea to commit your changes to source control after you complete a refactoring and your tests pass.</span></span> <span data-ttu-id="b67ab-166">Si vous êtes qui emploient développement piloté par Test [envisagez d’ajouter la validation à votre cycle de rouge-vert-refactoriser](https://ardalis.com/rgrc-is-the-new-red-green-refactor-for-test-first-development).</span><span class="sxs-lookup"><span data-stu-id="b67ab-166">If you're practicing Test Driven Development, [consider adding Commit to your Red-Green-Refactor cycle](https://ardalis.com/rgrc-is-the-new-red-green-refactor-for-test-first-development).</span></span>

## <a name="resources"></a><span data-ttu-id="b67ab-167">Ressources</span><span class="sxs-lookup"><span data-stu-id="b67ab-167">Resources</span></span>

* [<span data-ttu-id="b67ab-168">Tests unitaires</span><span class="sxs-lookup"><span data-stu-id="b67ab-168">Unit testing</span></span>](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="b67ab-169">Intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="b67ab-169">Middleware</span></span>](xref:fundamentals/middleware)
* [<span data-ttu-id="b67ab-170">Test des contrôleurs</span><span class="sxs-lookup"><span data-stu-id="b67ab-170">Testing controllers</span></span>](xref:mvc/controllers/testing)

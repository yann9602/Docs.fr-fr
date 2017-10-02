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
# <a name="integration-testing-in-aspnet-core"></a>Tests d’intégration dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

Tests d’intégration garantit que les composants d’une application fonctionnent correctement lors de l’assemblage. ASP.NET Core prend en charge aux tests d’intégration à l’aide des infrastructures de tests unitaires et un hôte web test intégré qui peut être utilisé pour gérer les demandes sans la surcharge du réseau.

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/testing/integration-testing/sample) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))

## <a name="introduction-to-integration-testing"></a>Introduction aux tests d’intégration

Tests d’intégration vérifient que les différentes parties d’une application fonctionnent correctement ensemble. Contrairement aux [test unitaire](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test), tests d’intégration impliquent souvent des problèmes d’infrastructure application, par exemple une base de données, système de fichiers, les ressources réseau, ou de requêtes et réponses web. Tests unitaires utilisent fakes ou objets fictifs à la place de ces problèmes, mais l’objectif de tests d’intégration consiste à vérifier que le système fonctionne comme prévu avec ces systèmes.

Tests d’intégration, car ils exercent des segments de code de plus grande et car elles reposent sur les éléments d’infrastructure, ont tendance à être beaucoup plus lente que les tests unitaires. Par conséquent, il est judicieux de limiter le nombre de tests intégration que vous écrivez, surtout si vous pouvez tester le comportement de même avec un test unitaire.

> [!NOTE]
> Si un comportement peut être testé à l’aide d’un test unitaire ou un test d’intégration, préférez le test unitaire, puisqu’il est presque toujours être plus rapide. Vous pouvez avoir des dizaines ou des centaines de tests unitaires avec de nombreuses entrées différentes mais quelques tests d’intégration qui couvrent les scénarios plus importantes.

Pour tester la logique dans vos propres méthodes est généralement le domaine des tests unitaires. Tester le fonctionnement de votre application au sein de son infrastructure, par exemple ASP.NET Core ou avec une base de données est où tests d’intégration entrent en jeu. Il ne faut pas trop de tests d’intégration pour confirmer que vous êtes en mesure d’écrire une ligne de la base de données et les lire. Vous n’avez pas besoin de tester chaque combinaison possible de votre code d’accès aux données, vous devez seulement tester suffisamment pour garantir que votre application fonctionne correctement.

## <a name="integration-testing-aspnet-core"></a>Intégration de test ASP.NET Core

Pour obtenir défini pour les tests d’intégration d’exécution, vous devez créer un projet de test, ajoutez une référence à votre projet de web ASP.NET Core et installer un test runner. Ce processus est décrit dans la [test unitaire](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation, ainsi que des instructions plus détaillées sur l’exécution de tests et des recommandations pour nommer vos tests et les classes de test.

> [!NOTE]
> Séparez vos tests unitaires et vos tests d’intégration à l’aide de différents projets. Cela permet de s’assurer que vous ne par accident introduire des problèmes d’infrastructure dans vos tests unitaires et vous permet de facilement choisir l’ensemble des tests à exécuter.

### <a name="the-test-host"></a>L’hôte de Test

ASP.NET Core inclut un hôte de test qui peut être ajouté aux projets de test d’intégration et utilisé pour héberger le ASP.NET Core applications, les demandes de service de test sans la nécessité d’un hôte web réel. L’exemple fourni inclut un projet de test d’intégration qui a été configuré pour utiliser [xUnit](https://xunit.github.io) et l’hôte de Test. Elle utilise le `Microsoft.AspNetCore.TestHost` package NuGet.

Une fois la `Microsoft.AspNetCore.TestHost` package est inclus dans le projet, vous serez en mesure de créer et configurer un `TestServer` dans vos tests. Le test suivant montre comment vérifier qu’une demande adressée à la racine d’un site renvoie « Hello World ! » et doit s’exécuter avec succès par rapport à la valeur par défaut modèle ASP.NET Core Web vide créé par Visual Studio.

[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebDefaultRequestShould.cs?name=snippet_WebDefault&highlight=7,16,22)]

Ce test utilise le modèle de disposition Act-Assert. L’étape d’organisation est effectuée dans le constructeur, ce qui crée une instance de `TestServer`. Configuré `WebHostBuilder` permet de créer un `TestHost`; dans cet exemple, le `Configure` (méthode) à partir du système sous test (SUT) `Startup` classe est passée à la `WebHostBuilder`. Cette méthode permet de configurer le pipeline de demande de la `TestServer` identique à la façon dont le serveur SUT serait configuré.

Dans la partie Act du test, une demande est faite pour le `TestServer` instance pour le chemin d’accès « / » et la réponse est en lecture en une chaîne. Cette chaîne est comparée à la chaîne attendue de « Hello World ! ». S’ils correspondent, le test réussit ; Sinon, elle échoue.

Vous pouvez maintenant ajouter quelques tests d’intégration pour confirmer que la préparation de la vérification de la fonctionnalité fonctionne via l’application web :

[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebCheckPrimeShould.cs?name=snippet_CheckPrime)]

Notez que vous n’essayez pas réellement tester l’exactitude de l’outil d’analyse de nombres premiers avec ces tests mais plutôt que l’application web fait ce que vous attendez. Vous disposez déjà de la couverture de test unitaire que vous êtes ainsi sûr dans `PrimeService`, comme vous pouvez voir ici :

![Explorateur de tests](integration-testing/_static/test-explorer.png)

Plus d’informations sur les tests unitaires dans le [test unitaire](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) l’article.


### <a name="integration-testing-mvcrazor"></a>Intégration de test Mvc/Razor

Les projets de test qui contiennent des vues Razor requièrent `<PreserveCompilationContext>` être définie sur true dans le *.csproj* fichier :


```xml
    <PreserveCompilationContext>true</PreserveCompilationContext>
```

Projets de cet élément génère une erreur semblable au suivant :
```
Microsoft.AspNetCore.Mvc.Razor.Compilation.CompilationFailedException: 'One or more compilation failures occurred:
ooebhccx.1bd(4,62): error CS0012: The type 'Attribute' is defined in an assembly that is not referenced. You must add a reference to assembly 'netstandard, Version=2.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'.
```


## <a name="refactoring-to-use-middleware"></a>Pour utiliser l’intergiciel (middleware) de refactorisation

La refactorisation est le processus de modification d’un code d’application pour améliorer sa conception sans modifier son comportement. Il doit être effectuée dans l’idéal, lorsqu’il existe une suite de transmission de tests, étant donné que ces aide garantir que le comportement du système reste le même avant et après les modifications. Examinez la manière dans laquelle la prime logique de contrôle est implémenté dans l’application web `Configure` (méthode), vous voyez :

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

Ce code fonctionne, mais il est loin d’être comment vous voulez implémenter ce genre de fonctionnalités dans une application ASP.NET Core, même simple car il s’agit. Imaginez que le `Configure` méthode se présente comme si vous avez besoin pour lui ajouter ce beaucoup de code chaque fois que vous ajoutez un autre point de terminaison URL !

Ajout d’une option à envisager [MVC](xref:mvc/overview) à l’application et la création d’un contrôleur pour gérer la vérification de la prime. Toutefois, si que vous n’avez pas actuellement doivent toutes les autres fonctionnalités MVC, qui sont un peu excessif.

Vous pouvez, toutefois, parti d’ASP.NET Core [intergiciel (middleware)](xref:fundamentals/middleware), ce qui nous aideront à encapsuler la prime logique dans sa propre classe de contrôle et d’obtenir une meilleure [séparation des préoccupations](http://deviq.com/separation-of-concerns/) dans le `Configure` méthode.

Vous souhaitez autoriser le chemin d’accès de l’intergiciel (middleware) utilise pour être spécifié en tant que paramètre, la classe de l’intergiciel (middleware) attend un `RequestDelegate` et un `PrimeCheckerOptions` instance dans son constructeur. Si le chemin d’accès de la demande ne correspond pas à ce que cet intergiciel (middleware) est configuré pour attendre, vous simplement appelez l’intergiciel (middleware) suivant dans la chaîne et ne font rien de plus. Le reste du code de mise en œuvre dans `Configure` figure désormais dans le `Invoke` (méthode).

> [!NOTE]
> Étant donné que l’intergiciel (middleware) varie selon le `PrimeService` service, vous demandez également une instance de ce service avec le constructeur. L’infrastructure fournit ce service via [Injection de dépendance](xref:fundamentals/dependency-injection), en supposant qu’il a été configuré, par exemple dans `ConfigureServices`.

[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Middleware/PrimeCheckerMiddleware.cs?highlight=39-63)]

Cet intergiciel (middleware) n’agit comme un point de terminaison dans la chaîne de délégué demande lorsque son chemin d’accès correspond à, il n’existe aucun appel à `_next.Invoke` lorsque cet intergiciel (middleware) traite la demande.

Avec cet intergiciel (middleware) en place et certaines utile méthodes d’extension créés pour faciliter sa configuration, le refactorisé `Configure` méthode ressemble à ceci :

[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Startup.cs?highlight=9&range=19-33)]

Après cette refactorisation, vous êtes certain que l’application web fonctionne toujours comme avant, étant donné que vos tests d’intégration sont passage.

> [!NOTE]
> Il est conseillé de valider les modifications apportées au contrôle de code source une fois que vous effectuez une opération de refactorisation et vos tests. Si vous êtes qui emploient développement piloté par Test [envisagez d’ajouter la validation à votre cycle de rouge-vert-refactoriser](https://ardalis.com/rgrc-is-the-new-red-green-refactor-for-test-first-development).

## <a name="resources"></a>Ressources

* [Tests unitaires](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Intergiciel (middleware)](xref:fundamentals/middleware)
* [Test des contrôleurs](xref:mvc/controllers/testing)

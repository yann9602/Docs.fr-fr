---
title: "Unité de Pages Razor et les tests d’intégration dans ASP.NET Core"
author: guardrex
description: "Découvrez comment créer des tests unitaires et l’intégration pour les applications de Pages Razor."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 11/27/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: testing/razor-pages-testing
ms.openlocfilehash: 5891b236306cd3790cbba14919796d6aa894ad53
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="razor-pages-unit-and-integration-testing-in-aspnet-core"></a>Unité de Pages Razor et les tests d’intégration dans ASP.NET Core

Par [Luke Latham](https://github.com/guardrex)

ASP.NET Core prend en charge l’unité et le test d’intégration des applications de Pages Razor. Test de la couche d’accès aux données (DAL), les modèles de page et les composants de la page intégrée permet de s’assurer :

* Parties d’une application de Pages Razor fonctionnent indépendamment et ensemble en tant qu’unité pendant la construction de l’application.
* Classes et méthodes ont limité de zones de responsabilité.
* Documentation supplémentaire existe sur le comportement de l’application.
* Les régressions sont des erreurs par les mises à jour le code, sont trouvent pendant le déploiement et de génération automatisée.

Cette rubrique suppose que vous avez une compréhension de base des applications de Pages Razor, tests unitaires et l’intégration de test. Si vous n’êtes pas familiarisé avec les applications de Pages Razor ou les concepts de tests, consultez les rubriques suivantes :

* [Présentation des pages Razor](xref:mvc/razor-pages/index)
* [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Test unitaire C# dans .NET Core, à l’aide de xUnit et dotnet test](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Tests d’intégration](xref:testing/integration-testing)

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/testing/razor-pages-testing/sample/) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

L’exemple de projet se compose de deux applications :

| Application         | Dossier du projet                        | Description |
| ----------- | ------------------------------------- | ----------- |
| Application de message | *src/RazorPagesTestingSample*         | Permet à un utilisateur à ajouter, supprimer une, supprimez tous les et analyser les messages. |
| Application de test    | *tests/RazorPagesTestingSample.Tests* | Utilisé pour tester l’application de message.<ul><li>Tests unitaires : couche d’accès aux données (DAL), modèle de page d’Index</li><li>Tests d’intégration : page d’Index</li></ul> |

Les tests peuvent être exécutés à l’aide des fonctionnalités de test intégrées d’un bus IDE, comme [Visual Studio](https://www.visualstudio.com/vs/). Si vous utilisez [Visual Studio Code](https://code.visualstudio.com/) ou la ligne de commande, exécutez la commande suivante à une invite de commandes dans le *tests/RazorPagesTestingSample.Tests* dossier :

```console
dotnet test
```

## <a name="message-app-organization"></a>Organisation d’application de message

L’application de message est un simple système de messages de Pages Razor avec les caractéristiques suivantes :

* La page d’Index de l’application (*Pages/Index.cshtml* et *Pages/Index.cshtml.cs*) fournit une interface utilisateur et la page des méthodes de modèle de contrôle de l’ajout, suppression et analyse des messages (mots moyenne par message) .
* Un message est décrite par la `Message` classe (*Data/Message.cs*) avec deux propriétés : `Id` (clé) et `Text` (message). Le `Text` propriété est obligatoire et est limitée à 200 caractères.
* Les messages sont stockés à l’aide de [base de données en mémoire d’Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* L’application contient une couche d’accès aux données (DAL) dans sa classe de contexte de base de données, `AppDbContext` (*Data/AppDbContext.cs*). Les méthodes de la couche DAL sont marquées `virtual`, ce qui permet la simulation les méthodes à utiliser dans les tests.
* Si la base de données est vide au démarrage de l’application, la banque de messages est initialisée avec trois messages. Ces *amorcée messages* sont également utilisées dans le test.

&#8224; La rubrique EF [test avec InMemory](/ef/core/miscellaneous/testing/in-memory), explique comment utiliser une base de données en mémoire pour les tests avec MSTest. Cette rubrique utilise le [xUnit](https://xunit.github.io/) infrastructure de test. Concepts de tests et de test implémentations différentes infrastructures de tests sont similaires mais non identiques.

Bien que l’application n’utilise pas le [modèle de référentiel](http://martinfowler.com/eaaCatalog/repository.html) et n’est pas un exemple effectif de la [modèle d’unité de travail (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Pages Razor prend en charge de ces modèles de développement. Pour plus d’informations, consultez [conception de la couche de persistance infrastructure](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design), [mise en œuvre du référentiel et les modèles d’unité de travail dans une Application ASP.NET MVC](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application), et [test logique de contrôleur](/aspnet/core/mvc/controllers/testing) (l’exemple implémente le modèle de référentiel).

## <a name="test-app-organization"></a>Organisation de l’application de test

L’application de test est une application console à l’intérieur de la *tests/RazorPagesTestingSample.Tests* dossier :

| Dossier d’application de test    | Description |
| ------------------ | ----------- |
| *IntegrationTests* | <ul><li>*IndexPageTest.cs* contient les tests d’intégration pour la page d’Index.</li><li>*TestFixture.cs* crée l’hôte de test pour tester l’application de message.</li></ul> |
| *UnitTests*        | <ul><li>*DataAccessLayerTest.cs* contient les tests unitaires pour la couche DAL.</li><li>*IndexPageTest.cs* contient les tests unitaires pour le modèle de page d’Index.</li></ul> |
| *Utilities*        | *Utilities.cs* contient le :<ul><li>`TestingDbContextOptions`méthode utilisée pour créer les options de contexte pour chaque test unitaire de la couche DAL de base de données afin que la base de données est réinitialisée à son état initial pour chaque test.</li><li>`GetRequestContentAsync`méthode utilisée pour préparer le `HttpClient` et le contenu pour les demandes qui sont envoyées à l’application de message pendant le test d’intégration.</li></ul>

L’infrastructure de test est [xUnit](https://xunit.github.io/). Est de l’objet de simulation framework [Moq](https://github.com/moq/moq4). Tests d’intégration sont effectuées à l’aide de la [hôte de Test ASP.NET Core](xref:testing/integration-testing#the-test-host).

## <a name="unit-testing-the-data-access-layer-dal"></a>La couche d’accès aux données (DAL) de tests unitaires

L’application de message a une couche DAL avec quatre méthodes contenues dans le `AppDbContext` classe (*src/RazorPagesTestingSample/Data/AppDbContext.cs*). Chaque méthode possède un ou deux tests unitaires dans l’application de test.

| Méthode de la couche DAL               | Fonction                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Obtient un `List<Message>` à partir de la base de données trié par le `Text` propriété. |
| `AddMessageAsync`        | Ajoute un `Message` à la base de données.                                          |
| `DeleteAllMessagesAsync` | Supprime tous les `Message` entrées à partir de la base de données.                           |
| `DeleteMessageAsync`     | Supprime une `Message` à partir de la base de données par `Id`.                      |

Tests unitaires de la couche DAL nécessitent [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) lors de la création d’un `AppDbContext` pour chaque test. Une approche de création du `DbContextOptions` pour chaque test est d’utiliser un [DbContextOptionsBuilder](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Le problème avec cette approche est que chaque test reçoit la base de données dans l’état dans lequel le test précédent laissé. Cela peut être problématique lorsque vous tentez d’écrire des tests d’unité atomique qui n’interfèrent pas avec eux. Pour forcer la `AppDbContext` pour utiliser un nouveau contexte de base de données pour chaque test, vous devez fournir un `DbContextOptions` instance qui est basé sur un nouveau fournisseur de services. L’application de test montre comment effectuer cette opération à l’aide de son `Utilities` méthode de classe `TestingDbContextOptions` (*tests/RazorPagesTestingSample.Tests/Utilities/Utilities.cs*) :

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/Utilities/Utilities.cs?name=snippet1)]

À l’aide de la `DbContextOptions` dans l’unité de la couche DAL tests permet à chaque test exécuter de façon atomique avec une une instance de la nouvelle base de données :

```csharp
using (var db = new AppDbContext(Utilities.TestingDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Chaque méthode de test dans le `DataAccessLayerTest` classe (*UnitTests/DataAccessLayerTest.cs*) suit un modèle semblable Assert Act de disposition :

1. Réorganiser : La base de données est configuré pour le test ou le résultat attendu est défini.
1. ACT : Le test est exécuté.
1. Assertion : Assertions sont effectuées pour déterminer si le résultat de test est une réussite.

Par exemple, le `DeleteMessageAsync` méthode est responsable de la suppression d’un message unique identifié par son `Id` (*src/RazorPagesTestingSample/Data/AppDbContext.cs*) :

[!code-csharp[Main](razor-pages-testing/sample/src/RazorPagesTestingSample/Data/AppDbContext.cs?name=snippet4)]

Il existe deux tests pour cette méthode. Un test vérifie que la méthode supprime un message lorsque le message est présent dans la base de données. Méthode des tests qui ne change pas la base de données si le message `Id` pour suppression n’existe pas. Le `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` méthode est indiquée ci-dessous :

[!code-csharp[Main](razor-pages-testing/sample_snapshot/tests/RazorPagesTestingSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Tout d’abord, la méthode exécute l’étape d’organisation, où la préparation de l’étape d’action a lieu. Les messages d’amorçage sont obtenues et contenues dans `seedMessages`. Les messages d’amorçage sont enregistrées dans la base de données. Le message avec un `Id` de `1` est définie pour la suppression. Lorsque le `DeleteMessageAsync` méthode est exécutée, les messages attendus doivent avoir tous les messages à l’exception de celui avec un `Id` de `1`. Le `expectedMessages` variable représente ce résultat attendu.

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

La méthode agit : le `DeleteMessageAsync` méthode est exécutée en passant le `recId` de `1`:

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Enfin, la méthode obtient le `Messages` à partir du contexte et le compare à la `expectedMessages` déclaration que les deux valeurs sont égales :

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Pour pouvoir comparer que deux `List<Message>` sont les mêmes :

* Les messages sont classés par `Id`.
* Paires de messages sont comparées sur la `Text` propriété.

Une méthode de test similaire, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` vérifie le résultat de la tentative de suppression d’un message qui n’existe pas. Dans ce cas, les messages attendus dans la base de données doivent être égales aux messages réels après le `DeleteMessageAsync` méthode est exécutée. Il ne doit y avoir aucune modification au contenu de la base de données :

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-testing-the-page-model-methods"></a>Les méthodes de modèle de page de test unitaire

Un autre jeu de tests unitaires est responsable du test des méthodes de modèle de page. Dans l’application de message, les modèles de page d’Index sont trouvent dans le `IndexModel` classe dans *src/RazorPagesTestingSample/Pages/Index.cshtml.cs*.

| Méthode de modèle de page | Fonction |
| ----------------- | -------- | 
| `OnGetAsync` | Obtient les messages à partir de la couche DAL pour l’interface utilisateur à l’aide de la `GetMessagesAsync` (méthode). |
| `OnPostAddMessageAsync` | Si le `ModelState` est valide, les appels `AddMessageAsync` pour ajouter un message à la base de données. | 
| `OnPostDeleteAllMessagesAsync` | Appels `DeleteAllMessagesAsync` pour supprimer tous les messages dans la base de données. |
| `OnPostDeleteMessageAsync` | Exécute `DeleteMessageAsync` pour supprimer un message avec le `Id` spécifié. |
| `OnPostAnalyzeMessagesAsync` | Si un ou plusieurs messages se trouvent dans la base de données, calcule le nombre moyen de mots par message. |

Les méthodes de modèle de page sont testés à l’aide de sept tests dans le `IndexPageTest` classe (*tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs*). Les tests utilisent le modèle de disposition Assert Act familier. Ces tests vous concentrer sur :

* Déterminer si les méthodes suivent le comportement correct lorsque la `ModelState` n’est pas valide.
* Confirmer les méthodes de produire le bon `IActionResult`.
* Vérification que les affectations des valeurs de propriété sont effectuées correctement.

Ce groupe de tests simuler souvent les méthodes de la couche DAL pour produire des données attendu pour l’étape d’action dans laquelle une méthode de modèle de page est exécutée. Par exemple, le `GetMessagesAsync` méthode de la `AppDbContext` est fictive pour produire la sortie. Lorsqu’une méthode de modèle page exécute cette méthode, le fictifs retourne le résultat. Les données ne provient pas de la base de données. Cela crée des conditions de test prévisible et fiable pour l’utilisation de la couche DAL dans les tests de modèle de page.

Le `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test montre comment la `GetMessagesAsync` méthode est fictive pour le modèle de page :

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs?name=snippet1&highlight=3-4)]

Lorsque le `OnGetAsync` méthode est exécutée dans l’étape d’action, elle appelle le modèle de page `GetMessagesAsync` (méthode).

Étape d’action de test unitaire (*tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs*) :

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs?name=snippet2)]

`IndexPage`de modèle page `OnGetAsync` (méthode) (*src/RazorPagesTestingSample/Pages/Index.cshtml.cs*) :

[!code-csharp[Main](razor-pages-testing/sample/src/RazorPagesTestingSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Le `GetMessagesAsync` méthode dans la couche DAL ne retourne pas le résultat de cet appel de méthode. La version factices de la méthode retourne le résultat.

Dans le `Assert` étape, les messages réels (`actualMessages`) sont affectés à partir de la `Messages` propriété du modèle de la page. Une vérification de type est également effectuée lorsque les messages sont affectés. Les messages attendues et réelles sont comparées à leurs `Text` propriétés. Le test déclare que les deux `List<Message>` instances contiennent les mêmes messages.

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs?name=snippet3)]

Autres tests de ce groupe Créer page objets de modèle qui incluent le `DefaultHttpContext`, le `ModelStateDictionary`, un `ActionContext` pour établir le `PageContext`, un `ViewDataDictionary`et un `PageContext`. Ceux-ci sont utiles pour effectuer des tests. Par exemple, l’application message établit un `ModelState` erreur avec `AddModelError` pour vérifier que valide `PageResult` est retourné lorsque `OnPostAddMessageAsync` est exécutée :

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="integration-testing-the-app"></a>Test de l’application d’intégration

L’intégration teste le focus sur le test qui coopèrent de composants de l’application. Tests d’intégration sont effectuées à l’aide de la [hôte de Test ASP.NET Core](xref:testing/integration-testing#the-test-host). Le traitement de requête-réponse complète du cycle de vie est testé. Ces tests déclarer que la page génère le code d’état correcte et `Location` en-tête, si définie.

Un exemple de l’exemple de test d’intégration vérifie le résultat de la page d’Index de l’application de message de demande (*tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs*) :

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs?name=snippet1)]

Il n’existe aucune étape d’organisation. Le `GetAsync` méthode est appelée sur le `HttpClient` pour envoyer une demande GET au point de terminaison. Le test déclare que le résultat est un code d’état de 200-OK.

Toute demande POST à l’application de message doit satisfaire la vérification est effectuée automatiquement par l’application de côté [côté système de protection des données](xref:security/data-protection/introduction). Afin de disposer de la demande de publication d’un test, l’application de test doit :

1. Effectuez une demande pour la page.
1. Analyser le cookie côté et un jeton de validation de demande à partir de la réponse.
1. Vérifiez la requête POST avec la validation de cookie et une demande côtée jeton en place.

Le `Post_AddMessageHandler_ReturnsRedirectToRoot` méthode de test :

* Il prépare un message et le `HttpClient`.
* Effectue une demande POST à l’application.
* Vérifie que la réponse est une redirection vers la page d’Index.

`Post_AddMessageHandler_ReturnsRedirectToRoot `méthode (*tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs*) :

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs?name=snippet2)]

Le `GetRequestContentAsync` méthode utilitaire gère la préparation du client avec le cookie côté et un jeton de demande de vérification. Notez comment la méthode reçoit un `IDictionary` qui permet à la méthode de test appelant pour transférer des données à la demande pour encoder en même temps que le jeton de demande de vérification (*tests/RazorPagesTestingSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/Utilities/Utilities.cs?name=snippet2&highlight=1-2,8-9,29)]

Tests d’intégration peuvent également passer des données incorrectes à l’application pour tester le comportement de la réponse de l’application. L’application de message limite la longueur du message 200 caractères (*src/RazorPagesTestingSample/Data/Message.cs*) :

[!code-csharp[Main](razor-pages-testing/sample/src/RazorPagesTestingSample/Data/Message.cs?name=snippet1&highlight=7)]

Le `Post_AddMessageHandler_ReturnsSuccess_WhenMessageTextTooLong` test `Message` explicitement passe en texte avec 201 caractères « X ». Cela entraîne une `ModelState` erreur. La publication n’est pas redirigée vers la page d’Index. Elle retourne un 200-OK avec un `null` `Location` en-tête (*tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs*) :

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs?name=snippet3&highlight=7,16-17)]

## <a name="see-also"></a>Voir aussi

* [Test unitaire C# dans .NET Core, à l’aide de xUnit et dotnet test](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Tests d’intégration](xref:testing/integration-testing)
* [Test des contrôleurs](xref:mvc/controllers/testing)
* [Votre Code de Test unitaire](/visualstudio/test/unit-test-your-code) (Visual Studio)
* [xUnit.net](https://xunit.github.io/)
* [Prise en main de xUnit.net (.NET Core/ASP.NET Core)](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Démarrage rapide de Moq](https://github.com/Moq/moq4/wiki/Quickstart)

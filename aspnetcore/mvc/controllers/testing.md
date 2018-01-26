---
title: "Pour tester la logique de contrôleur dans ASP.NET Core"
author: ardalis
description: "En savoir plus sur la logique du contrôleur de test dans ASP.NET Core avec Moq et xUnit."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/testing
ms.openlocfilehash: f27e7ec43cd17e249dd646a7dfbce5df69d59664
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="testing-controller-logic-in-aspnet-core"></a>Pour tester la logique de contrôleur dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

Contrôleurs dans les applications ASP.NET MVC doivent être petit et se concentrent sur les problèmes d’interface utilisateur. Contrôleurs de grande taille qui traitent des problèmes d’interface utilisateur sont plus difficiles à tester et mettre à jour.

[Afficher ou télécharger l’exemple à partir de GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample)

## <a name="testing-controllers"></a>Contrôleurs de tests

Contrôleurs sont une partie centrale d’une application ASP.NET MVC de base. Par conséquent, vous devez avoir confiance qu’ils se comportent comme prévu pour votre application. Tests automatisés peuvent vous fournir cette confiance et peuvent détecter les erreurs avant qu’ils atteignent la production. Il est important d’éviter de placer des responsabilités inutiles dans vos contrôleurs et vérifiez votre focus tests uniquement sur les responsabilités de contrôleur.

Logique de contrôleur doit être minime et n’est ne pas être axée sur les problèmes métier logique ou d’infrastructure (par exemple, accès aux données). Tester la logique du contrôleur, pas l’infrastructure. Test comment le contrôleur *se comporte* basé sur les entrées valides ou non valides. En fonction du résultat de l’opération métier qu'il effectue des réponses du contrôleur de test.

Responsabilités du contrôleur classique :

* Vérifiez `ModelState.IsValid`.
* Retourne une réponse d’erreur si `ModelState` n’est pas valide.
* Récupérer une entité commerciale de persistance.
* Effectuer une action sur l’entité métier.
* Enregistrer l’entité métier à la persistance.
* Renvoyer un approprié `IActionResult`.

## <a name="unit-testing"></a>Test unitaire

[Tests unitaires](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) implique le test d’une partie d’une application de manière isolée de son infrastructure et des dépendances. Lorsque la logique du contrôleur, seul le contenu d’une seule action de test unitaire est testé, pas le comportement de ses dépendances ou de l’infrastructure elle-même. En tant qu’unité vous vos actions de contrôleur de test, assurez-vous que vous concentrer uniquement sur son comportement. Un test unitaire de contrôleur évite des éléments tels que les [filtres](filters.md), [routage](../../fundamentals/routing.md), ou [liaison de modèle](../models/model-binding.md). En se concentrant sur la seule chose, les tests unitaires sont généralement simple à écrire et rapides à exécuter. Un ensemble bien écrit de tests unitaires peut être exécuté souvent sans la quantité de surcharge. Toutefois, les tests unitaires ne pas détecter les problèmes dans l’interaction entre les composants, qui est l’objectif de [test d’intégration](xref:mvc/controllers/testing#integration-testing).

Si vous écrivez des filtres personnalisés, itinéraires, etc., vous devez le test unitaire leur, mais pas dans le cadre de vos tests sur une action de contrôleur spécifique. Ils doivent être testées de manière isolée.

> [!TIP]
> [Créer et exécuter des tests unitaires avec Visual Studio](https://docs.microsoft.com/visualstudio/test/unit-test-your-code).

Pour illustrer les tests unitaires, passez en revue le contrôleur suivant. Il affiche la liste des sessions de réflexion et permet de nouvelles sessions sont créés avec une publication de réflexion :

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?highlight=12,16,21,42,43)]

Le contrôleur est après le [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/), attendu de lui fournir une instance de l’injection de dépendance `IBrainstormSessionRepository`. Cela rend relativement facile à tester à l’aide d’une infrastructure d’objets fictifs, tel que [Moq](https://www.nuget.org/packages/Moq/). Le `HTTP GET Index` méthode ne dispose d’aucune en boucle ou de création de branche et uniquement appelle une méthode. Pour effectuer ce test `Index` (méthode), nous devons vérifier qu’un `ViewResult` est retourné, avec une `ViewModel` à partir du référentiel `List` (méthode).

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?highlight=17-18&range=1-33,76-95)]

Le `HomeController` `HTTP POST Index` (méthode) (illustré ci-dessus) devez vérifier :

* La méthode d’action retourne une erreur demande incorrecte `ViewResult` avec les données appropriées lorsque `ModelState.IsValid` est`false`

* Le `Add` méthode sur le référentiel est appelée et un `RedirectToActionResult` est retourné avec les arguments appropriés lorsque `ModelState.IsValid` a la valeur true.

État de modèle non valide peut être testé en ajoutant des erreurs à l’aide de `AddModelError` comme indiqué dans le premier test ci-dessous.

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?highlight=8,15-16,37-39&range=35-75)]

Le premier test confirme lorsque `ModelState` n’est pas valide, le même `ViewResult` est retourné en tant que pour un `GET` demande. Notez que le test ne tente pas de passer d’un modèle non valide. Cela ne fonctionnera pas tout de même, car la liaison de modèle n’est pas en cours d’exécution (si un [test d’intégration](xref:mvc/controllers/testing#integration-testing) utiliserait la liaison de modèle exercice). Dans ce cas, liaison de modèle n’est pas en cours de test. Ces tests unitaires Testez uniquement le code dans la méthode d’action.

Le deuxième test vérifie que quand `ModelState` est valide, un nouveau `BrainstormSession` est ajouté (via le référentiel), et la méthode retourne un `RedirectToActionResult` avec les propriétés attendues. Les appels factices qui ne sont pas appelés sont normalement ignoré, mais en appelant `Verifiable` à la fin de l’installation appel lui permet de le vérifier dans le test. Cette opération s’effectue avec l’appel à `mockRepo.Verify`, ce qui échoue au test si la méthode attendue n’a pas été appelée.

> [!NOTE]
> La bibliothèque Moq utilisée dans cet exemple permet de facilement permet de combiner des simulacres vérifiables, ou « stricts », avec des simulacres non vérifiable (également appelés « libres » mocks ou stubs). En savoir plus sur [personnalisation du comportement fictifs avec Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

Un autre contrôleur dans l’application affiche les informations liées à une séance particulier. Il inclut une logique pour traiter les valeurs d’id non valide :

[!code-csharp[Main](./testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?highlight=19,20,21,22,25,26,27,28)]

L’action du contrôleur a trois cas à tester, une pour chaque `return` instruction :

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?highlight=27,28,29,46,47,64,65,66,67,68)]

L’application expose les fonctionnalités comme web API (il s’agit d’une liste d’idées associé à une session de réflexion et une méthode pour ajouter de nouvelles idées à une session) :

<a name="ideas-controller"></a>

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?highlight=21,22,27,30,31,32,33,34,35,36,41,42,46,52,65)]

Le `ForSession` méthode retourne une liste de `IdeaDTO` types. Éviter de retourner les entités de votre domaine métier directement par le biais d’appels d’API, depuis la fréquence à laquelle ils incluent plus de données que le client API a besoin, et leur associent inutilement modèle de domaine interne de votre application avec l’API vous exposez à l’extérieur. Mappage entre les entités de domaine et les types que vous revenez sur le réseau peut être effectuée manuellement (à l’aide de LINQ `Select` comme indiqué ici) ou à l’aide d’une bibliothèque, par exemple [AutoMapper](https://github.com/AutoMapper/AutoMapper)

Les tests unitaires pour le `Create` et `ForSession` méthodes API :

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?highlight=18,23,29,33,38-39,43,50,58-59,68-70,76-78&range=1-83,121-135)]

Comme indiqué précédemment, pour tester le comportement de la méthode lorsque `ModelState` n’est pas valide, ajoutez une erreur de modèle au contrôleur en tant que partie du test. N’essayez pas de tester la liaison de modèle ou de la validation de modèle dans vos tests unitaires : tester simplement le comportement de votre méthode d’action une fois confronté à un particulier `ModelState` valeur.

Le deuxième test dépend de l’espace de stockage retournant null, donc le référentiel fictif est configuré pour retourner une valeur null. Il est inutile de créer une base de données de test (dans la mémoire ou autre) et construire une requête qui retourne ce résultat - il peut être effectué dans une instruction unique comme indiqué.

Le dernier test vérifie que l’espace de stockage `Update` méthode est appelée. Comme nous l’avons fait précédemment, le fictifs est appelée avec `Verifiable` et puis factices du référentiel `Verify` méthode est appelée pour vérifier que la méthode vérifiable a été exécutée. Il n’est pas une responsabilité de test unitaire pour vous assurer que le `Update` méthode enregistré les données ; cela peut se faire avec un test d’intégration.

## <a name="integration-testing"></a>Tests d’intégration

[Tests d’intégration](../../testing/integration-testing.md) est effectuée pour garantir correctement ensemble de modules distincts au sein de votre travail de l’application. En règle générale, tout ce que vous pouvez tester avec un test unitaire, vous pouvez également tester avec un test d’intégration, mais l’inverse n’est pas vrai. Toutefois, les tests d’intégration tendent à être beaucoup plus lent que les tests unitaires. Par conséquent, il est préférable de tester ce que vous pouvez avec des tests unitaires et utilisez des tests d’intégration pour les scénarios qui impliquent plusieurs collaborateurs.

Bien qu’elles peuvent toujours être utiles, les objets factices sont rarement utilisées dans les tests d’intégration. Dans les tests unitaires, objets factices sont un moyen efficace de contrôler le comportement de collaborateurs en dehors de l’unité en cours de test pour les besoins du test. Dans un test d’intégration, les collaborateurs réels sont utilisés pour vérifier que le sous-système complet ensemble fonctionne correctement.

### <a name="application-state"></a>État de l’application

Un facteur important lorsque vous effectuez des tests d’intégration consiste à définir l’état de votre application. Les tests doivent être exécutés indépendamment des autres, et par conséquent, chaque test doit commencer par l’application dans un état connu. Si votre application n’utiliser une base de données de persistance, cela peut être pas un problème. Toutefois, la plupart des applications réelles conservent leur état à un certain type de magasin de données, donc toutes les modifications apportées par un seul test peut affecter un autre test, sauf si le magasin de données est réinitialisé. À l’aide de la fonction intégrée `TestServer`, il est très simple pour les applications ASP.NET Core hôte dans nos tests d’intégration, mais qui n’accorde pas nécessairement l’accès aux données qu’il utilisera. Si vous utilisez une base de données réelle, une approche consiste à avoir à l’application de se connecter à une base de données de test, lequel vos tests peuvent accéder à et vérifiez est réinitialisé à un état connu avant l’exécution de chaque test.

Dans cet exemple d’application, j’utilise prise en charge de InMemoryDatabase d’Entity Framework Core, donc impossible de me connecter simplement à ce dernier à partir de mon projet de test. Au lieu de cela, j’ai exposer une `InitializeDatabase` méthode à partir de l’application `Startup` (classe), laquelle appeler au démarrage de l’application si elle se trouve dans le `Development` environnement. Mes tests d’intégration automatiquement tirer parti de cela, que leur définir l’environnement `Development`. Je n’ai pas à vous soucier de la réinitialisation de la base de données, dans la mesure où le InMemoryDatabase est réinitialisé chaque fois que l’application redémarre.

La `Startup` classe :

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Startup.cs?highlight=19,20,34,35,43,52)]

Vous verrez la `GetTestSession` méthode fréquemment utilisée dans les tests d’intégration ci-dessous.

### <a name="accessing-views"></a>L’accès aux vues

Chaque classe de test d’intégration configure le `TestServer` qui exécutera l’application ASP.NET Core. Par défaut, `TestServer` héberge l’application web dans le dossier dans lequel il est en cours d’exécution - dans ce cas, le dossier de projet de test. Par conséquent, lorsque vous essayez d’actions qui retournent du contrôleur de test `ViewResult`, vous pouvez voir cette erreur :

```
The view 'Index' wasn't found. The following locations were searched:
(list of locations)
```

Pour corriger ce problème, vous devez configurer la racine du contenu du serveur, afin de pouvoir localiser les affichages pour le projet en cours de test. Pour cela, un appel à `UseContentRoot` dans le `TestFixture` (classe), illustré ci-dessous :

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/TestFixture.cs?highlight=30,33)]

Le `TestFixture` classe est responsable de la configuration et la création la `TestServer`, paramétrage un `HttpClient` pour communiquer avec le `TestServer`. Chacune de l’intégration de tests utilise le `Client` propriété pour se connecter au serveur de test et effectuer une demande.

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/HomeControllerTests.cs?highlight=20,26,29,30,31,35,38,39,40,41,44,47,48)]

Dans le premier test ci-dessus, le `responseString` contient le rendu HTML à partir de la vue, qui peut être inspectée pour confirmer qu’il contient les résultats attendus.

Le deuxième test construit une publication de formulaire avec un nom de session unique et envoie à l’application, puis vérifie que la redirection attendue est retournée.

### <a name="api-methods"></a>Méthodes de l’API

Si votre application expose web API, il judicieux d’avoir des tests automatisés confirmer qu’ils s’exécutent comme prévu. La fonction intégrée `TestServer` facilite l’API web de test. Si vos méthodes de l’API sont à l’aide de liaison de modèle, vous devez toujours vérifier `ModelState.IsValid`, et les tests d’intégration sont au bon endroit pour confirmer le fonctionne de la validation de votre modèle.

L’ensemble de la cible de tests suivant le `Create` méthode dans le [IdeasController](xref:mvc/controllers/testing#ideas-controller) classe ci-dessus :

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/ApiIdeasControllerTests.cs)]

Contrairement aux tests d’intégration d’actions qui retourne des vues HTML, les méthodes API web qui retournent des résultats peuvent généralement être désérialisés en tant qu’objets fortement typés, comme le montre le dernier test ci-dessus. Dans ce cas, le test désérialise le résultat à une `BrainstormSession` d’instance et confirme que l’idée a été correctement ajoutée à la collection des idées.

Vous trouverez des exemples supplémentaires de tests d’intégration dans cet article [exemple de projet](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample).

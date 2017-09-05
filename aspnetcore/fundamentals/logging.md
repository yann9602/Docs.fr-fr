---
title: Journalisation dans ASP.NET Core
author: ardalis
description: "Présente les fonctionnalités de journalisation dans ASP.NET Core. Inclut une section pour chaque fournisseur de journalisation intégrés et des liens vers des fournisseurs tiers populaires."
keywords: "ASP.NET Core, la journalisation, les fournisseurs de journalisation, Microsoft.Extensions.Logging, ILogger, ILoggerFactory, LogLevel, WithFilter, TraceSource, journal des événements, EventSource, des étendues"
ms.author: tdykstra
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ac27ac68-d76a-4f8e-b8ab-ea045803e5f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/logging
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30e00e2a442225bbe04be0d343f7048efe484477
ms.sourcegitcommit: 4e84d8bf5f404bb77f3d41665cf7e7374fc39142
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2017
---
# <a name="introduction-to-logging-in-aspnet-core"></a>Introduction à la journalisation dans ASP.NET Core

Par [Steve Smith](http://ardalis.com) et [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core prend en charge une API de journalisation qui fonctionne avec une gamme de fournisseurs de journalisation. Les fournisseurs intégrés vous permettent d’envoyer les journaux à une ou plusieurs destinations, et vous pouvez incorporer dans une infrastructure de journalisation de l’application tierce. Cet article explique comment utiliser les API de journalisation intégrés et les fournisseurs dans votre code.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample2)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample)

---

## <a name="how-to-create-logs"></a>Comment créer des journaux

Pour créer des journaux, vous devez obtenir un `ILogger` de l’objet à partir de la [injection de dépendance](dependency-injection.md) conteneur :

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Puis appelez les méthodes de journalisation sur cet objet de l’enregistreur d’événements :

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Cet exemple crée des journaux avec le `TodoController` classe en tant que le *catégorie*.  Catégories sont expliquées [plus loin dans cet article](#log-category).

ASP.NET Core ne fournit pas asynchrone des méthodes de journalisation, car la journalisation doit être donc rapide, il n’est pas important du coût d’utilisation d’async. Si vous êtes dans une situation où qui n’est pas vrai, pensez à la manière de que vous connecter.  Si votre banque de données est lente, écrire les messages du journal dans un magasin rapide tout d’abord, puis les déplacer vers un magasin lent ultérieurement. Par exemple, connectez-vous à une file d’attente qui est lues et conservé dans le stockage lent par un autre processus.

## <a name="how-to-add-providers"></a>Comment ajouter des fournisseurs

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Un fournisseur de journalisation extrait les messages que vous créez avec un `ILogger` de l’objet et affiche les stocke. Par exemple, le fournisseur de la Console affiche des messages sur la console, et le fournisseur de services d’application Azure de les stocker dans le stockage blob Azure.

Pour utiliser un fournisseur, appelez du fournisseur `Add<ProviderName>` méthode d’extension dans *Program.cs*:

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=16,17)]

Le modèle de projet par défaut configure de la journalisation de la façon de vous voir dans le code précédent, mais le `ConfigureLogging` appel est effectué le `CreateDefaultBuilder` (méthode). Voici le code *Program.cs* qui est créé par les modèles de projet :

[!code-csharp[](logging/sample2/Program.cs?name=snippet_TemplateCode&highlight=7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Un fournisseur de journalisation extrait les messages que vous créez avec un `ILogger` de l’objet et affiche les stocke. Par exemple, le fournisseur de la Console affiche des messages sur la console, et le fournisseur de services d’application Azure de les stocker dans le stockage blob Azure.

Pour utiliser un fournisseur, installez le package NuGet et appeler la méthode d’extension du fournisseur sur une instance de `ILoggerFactory`, comme illustré dans l’exemple suivant.

[!code-csharp[](logging/sample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

ASP.NET Core [injection de dépendance](dependency-injection.md) (DI) fournit le `ILoggerFactory` instance. Le `AddConsole` et `AddDebug` méthodes d’extension sont définies dans le [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) et [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages. Chaque méthode d’extension appelle la `ILoggerFactory.AddProvider` méthode, en passant une instance du fournisseur. 

> [!NOTE]
> L’exemple d’application pour cet article ajoute des fournisseurs de journalisation dans le `Configure` méthode de la `Startup` classe. Si vous souhaitez obtenir la sortie du journal à partir du code qui s’exécute plus tôt, ajouter des fournisseurs de journalisation dans le `Startup` constructeur de classe à la place. 

---

Vous trouverez des informations sur chacune d’elles [fournisseur de journalisation intégrés](#built-in-logging-providers) et des liens vers [modules fournisseurs d’informations de tiers](#third-party-logging-providers) plus loin dans l’article.

## <a name="sample-logging-output"></a>Exemple de sortie de journalisation

Avec l’exemple de code indiqué dans la section précédente, vous verrez des journaux dans la console lorsque vous exécutez à partir de la ligne de commande. Voici un exemple de sortie de la console :

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```
 
Ces journaux ont été créés en accédant à `http://localhost:5000/api/todo/0`, ce qui déclenche l’exécution des deux `ILogger` appels indiqués dans la section précédente.

Voici un exemple des mêmes journaux telles qu’elles apparaissent dans la fenêtre de débogage lorsque vous exécutez l’exemple d’application dans Visual Studio :

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404 
```

Les fichiers journaux qui ont été créés par le `ILogger` appels indiqués dans la section précédente commencent par « TodoApi.Controllers.TodoController ». Les fichiers journaux qui commencent par catégories de « Microsoft » sont à partir de ASP.NET. ASP.NET Core lui-même et votre code d’application sont à l’aide de la même API de journalisation et les mêmes fournisseurs de journalisation.

Le reste de cet article explique certains détails et les options de journalisation.

## <a name="nuget-packages"></a>Packages NuGet

Le `ILogger` et `ILoggerFactory` sont des interfaces dans [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), et les implémentations par défaut pour les [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/).

## <a name="log-category"></a>Catégorie de journaux

A *catégorie* est inclus dans chaque journal que vous créez.  Vous spécifiez la catégorie lorsque vous créez un `ILogger` objet. La catégorie peut être n’importe quelle chaîne, mais une convention est d’utiliser le nom qualifié complet de la classe à partir de laquelle les journaux sont écrits.  Par exemple : « TodoApi.Controllers.TodoController ».

Vous pouvez spécifier la catégorie sous forme de chaîne ou utiliser une méthode d’extension qui dérive de la catégorie du type. Pour spécifier la catégorie sous forme de chaîne, appelez `CreateLogger` sur une `ILoggerFactory` de l’instance, comme indiqué ci-dessous.

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

La plupart du temps, il sera plus facile à utiliser `ILogger<T>`, comme dans l’exemple suivant.

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Cela équivaut à appeler la méthode `CreateLogger` avec le nom de type qualifié complet `T`.

## <a name="log-level"></a>Niveau du journal

Chaque fois que vous écrivez un journal, spécifiez son [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel). Le niveau de journal indique le degré de gravité ou l’importance.  Par exemple, vous pouvez écrire un `Information` journal lorsqu’une méthode se termine normalement, un `Warning` lorsqu’une méthode retourne une erreur 404 code de retour et un journal `Error` ouvrir une session lorsque vous interceptez une exception inattendue.

Dans l’exemple de code suivant, les noms des méthodes (par exemple, `LogWarning`) spécifier le niveau de journal.  Le premier paramètre est la [connecter l’ID d’événement](#log-event-id) (décrite plus loin dans cet article).  Les paramètres restants de construire une chaîne de message de journal.

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Les méthodes de journal qui incluent le niveau dans le nom de la méthode sont [méthodes d’extension pour ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions). En arrière-plan, ces méthodes appellent un `Log` méthode qui accepte un `LogLevel` paramètre. Vous pouvez appeler la `Log` méthode directement au lieu de ces méthodes d’extension, mais la syntaxe est relativement complexe. Pour plus d’informations, consultez la [ILogger interface](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) et [extensions de l’enregistreur de code source](https://github.com/aspnet/Logging/blob/master/src/Microsoft.Extensions.Logging.Abstractions/LoggerExtensions.cs).

ASP.NET Core définit les éléments suivants [niveaux de journal](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel), ordonnés ici de gravité le plus élevée au moins.

* Trace = 0

  Pour plus d’informations qui est utiles uniquement pour un développeur de débogage d’un problème. Ces messages peuvent contenir des données d’application sensibles et ne doivent donc pas être activées dans un environnement de production. *Désactivé par défaut.* Exemple : `Credentials: {"User":"someuser", "Password":"P@ssword"}`

* Déboguer = 1

  Pour plus d’informations ayant une utilité à court terme pendant le développement et le débogage. Exemple : `Entering method Configure with flag set to true.` vous généralement ne permettrait pas `Debug` niveau se connecte en production, sauf si vous dépannez, en raison du volume élevé de journaux.

* Informations = 2

  Pour le suivi du flux général de l’application. Ces journaux ont généralement une valeur à long terme. Exemple : `Request received for path /api/todo`

* Avertissement = 3

  Pour les événements anormaux ou inattendues dans le flux d’application. Il peut s’agir d’erreurs ou autres conditions qui ne provoquent pas l’arrêt de l’application, mais qui peut être nécessaire d’envisager. Des exceptions sont un emplacement commun à utiliser le `Warning` niveau de journal. Exemple : `FileNotFoundException for file quotes.txt.`

* Erreur = 4

  Pour les erreurs et exceptions qui ne peuvent pas être gérées. Ces messages indiquent un échec de l’activité actuelle ou l’opération (par exemple, la requête HTTP actuelle), pas un échec de l’application. Exemple de message de journal :`Cannot insert record due to duplicate key violation.`

* Critique = 5

  Pour les défaillances qui nécessitent une attention immédiate. Exemples : perte de données, espace disque insuffisant.

Vous pouvez utiliser le niveau de journal pour contrôler la quantité sortie du journal est écrit dans un support de stockage particulier ou fenêtre d’affichage. Par exemple, dans la production vous souhaiterez tous les journaux de `Information` niveau et vers le bas pour accéder à un magasin de données de volume et tous les journaux de `Warning` niveau et versions ultérieures pour accéder à un magasin de données de valeur. Pendant le développement, vous pouvez normalement envoyer des journaux de `Warning` ou la gravité supérieure à la console. Lorsque vous avez besoin résoudre les problèmes, vous pouvez alors ajouter `Debug` niveau. Le [filtrage de journal](#log-filtering) section plus loin dans cet article explique comment contrôler les niveaux de journalisation d’un fournisseur gère.

L’infrastructure ASP.NET Core écrit `Debug` niveau des journaux des événements de framework. Les exemples de journal plus haut dans cet article exclus journaux ci-dessous `Information` niveau, par conséquent, aucune `Debug` niveau journaux ont été affichés. Voici un exemple de journaux de la console si vous exécutez l’exemple d’application configuré pour afficher `Debug` et journaux plus élevées pour le fournisseur de la console.

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>ID d’événement de journal

Chaque fois que vous écrivez un journal, vous pouvez spécifier une *ID d’événement*. L’exemple d’application pour cela, à l’aide de-définies localement `LoggingEvents` classe :

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](logging/sample//Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Un ID d’événement est une valeur entière qui vous permet d’associer un ensemble d’événements enregistrés avec l’autre. Par exemple, un journal pour l’ajout d’un élément dans un panier d’achat peut être l’ID d’événement 1000 et un journal d’exécution d’un fournisseur peut être l’événement ID 1001.

Dans la sortie de journalisation, l’ID d’événement peut-être être stocké dans un champ ou inclus dans le message texte, selon le fournisseur.  Le fournisseur de débogage n’affiche pas les ID d’événement, mais le fournisseur de console présente les crochets après la catégorie :

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-format-string"></a>Chaîne de format de message de journal

Chaque fois que vous écrivez un journal, vous fournissez un message texte. La chaîne de message peut contenir des espaces réservés nommés, dans l’argument qui sont placées les valeurs, comme dans l’exemple suivant :

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour eux. Par exemple, si vous avez le code suivant :

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Le message du journal résultante ressemble à ceci :

```
Parameter values: parm1, parm2
```

L’infrastructure de journalisation de message de cette façon à permettre aux fournisseurs de journalisation implémenter la mise en forme [journalisation sémantique, également appelée enregistrement structuré](http://programmers.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Étant donné que les arguments eux-mêmes sont transmis au système de journalisation, pas seulement la chaîne de message mis en forme, les fournisseurs de journalisation peuvent stocker les valeurs de paramètre en tant que champs en plus de la chaîne de message. Par exemple, si vous envoyez votre journal de sortie pour le stockage de Table Azure et votre appel de méthode enregistreur d’événements ressemble à ceci :

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

Chaque entité de Table Azure peut avoir `ID` et `RequestTime` propriétés, qui seraient simplifient les requêtes sur les données du journal. Vous pouvez rechercher tous les journaux dans un particulier `RequestTime` plage, sans avoir à analyser le délai d’expiration du message texte.

## <a name="logging-exceptions"></a>Enregistrement des exceptions

Les méthodes de l’enregistreur d’événements ont des surcharges qui vous permettent de passer d’une exception, comme dans l’exemple suivant :

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Différents fournisseurs de gérer des informations sur l’exception de différentes façons. Voici un exemple de sortie de fournisseur de débogage de code ci-dessus.

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrage de journal

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Vous pouvez spécifier un niveau de journal minimale pour une catégorie et un fournisseur spécifique ou pour tous les fournisseurs ou toutes les catégories.  Tous les journaux sous le niveau minimal ne sont pas transmis à ce fournisseur, donc n’obtenir affichées ou stockées. 

Si vous souhaitez supprimer tous les journaux, vous pouvez spécifier `LogLevel.None` en tant que le niveau de journalisation minimale. La valeur entière de `LogLevel.None` est 6, ce qui est supérieur à `LogLevel.Critical` (5).

**Créer des règles de filtre dans la configuration**

Les modèles de projet créent du code qui appelle `CreateDefaultBuilder` pour configurer la journalisation pour les fournisseurs de Console et de débogage. Le `CreateDefaultBuilder` méthode définit également la journalisation pour rechercher la configuration dans un `Logging` section, à l’aide de code comme suit :

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=15)]

Les données de configuration spécifient des niveaux de journalisation minimale par fournisseur et par catégorie, comme dans l’exemple suivant :

[!code-json[](logging/sample2/appsettings.json)]

Ce JSON crée les six règles de filtre, une pour le fournisseur de débogage, 4 pour le fournisseur de la Console et celle qui s’applique à tous les fournisseurs. Vous verrez plus tard comment une de ces règles est choisie pour chaque fournisseur lorsqu’un `ILogger` objet est créé.

**Règles de filtre dans le code**

Vous pouvez enregistrer les règles de filtre dans le code, comme indiqué dans l’exemple suivant :

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

La seconde `AddFilter` Spécifie le fournisseur de débogage à l’aide de son nom de type. Le premier `AddFilter` s’applique à tous les fournisseurs, car il ne spécifie pas un type de fournisseur.

**Fonctionnement des règles de filtrage sont appliquées.**

Les données de configuration et le `AddFilter` code indiqué dans les exemples précédents créer les règles affichées dans le tableau suivant. Les six premiers proviennent de l’exemple de configuration et les deux derniers proviennent de l’exemple de code.

Nombre|Fournisseur|Catégories qui commencent par|Niveau de journalisation minimale|
------|--------|--------------------------|-----------------|
1|Déboguer|Toutes les catégories|Information|
2|Console|Microsoft.AspNetCore.Mvc.Razor.Internal|Warning|
3|Console|Microsoft.AspNetCore.Mvc.Razor.Razor|Déboguer|
4|Console|Microsoft.AspNetCore.Mvc.Razor|Erreur|
5|Console|Toutes les catégories|Information|
6|Tous les fournisseurs|Toutes les catégories|Warning
7|Tous les fournisseurs|Système|Déboguer
8|Déboguer|Microsoft|Suivi

Lorsque vous créez un `ILogger` objet à écrire des journaux, le `ILoggerFactory` objet sélectionne une règle unique par le fournisseur à appliquer à ce journal. Tous les messages rédigés par ce `ILogger` objet sont filtrées en fonction des règles sélectionnées. La règle la plus spécifique possible pour chaque paire de catégorie et le fournisseur est sélectionnée dans les règles disponibles.

L’algorithme suivant est utilisé pour chaque fournisseur lorsqu’un `ILogger` est créé pour une catégorie donnée :

* Sélectionnez toutes les règles qui correspondent au fournisseur ou son alias.  Si aucune n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.
* À partir du résultat de l’étape précédente, sélectionnez des règles avec la plus longue correspondance de préfixe de catégorie. Si aucune n’est trouvée, sélectionnez toutes les règles qui ne spécifient pas une catégorie.
* Si plusieurs règles sont sélectionnées prendre le **dernière** une.
* Si aucune règle n’est sélectionné, utilisez `MinimumLevel`.
 
Par exemple, supposons que vous disposez de la liste précédente des règles et que vous créez un `ILogger` objet pour la catégorie « Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine » :

* Le fournisseur de débogage, les règles 1, 6 et 8 s’appliquent. Règle 8 est la plus spécifique, qui est sélectionné.
* Le fournisseur de la Console, les règles 3, 4, 5 et 6 s’appliquent. La règle 3 est plus spécifique.

Lorsque vous créez des journaux avec un `ILogger` pour la catégorie « Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine », les journaux de `Trace` niveau et ci-dessus passe auprès du fournisseur de débogage et les journaux de `Debug` niveau et ci-dessus passe au fournisseur de Console.

**Alias de fournisseur**

Vous pouvez utiliser le nom de type pour spécifier un fournisseur de configuration, mais chaque fournisseur définit un plus court *alias* qui est plus facile à utiliser. Pour les fournisseurs intégrés, utilisez les alias suivants :

- Console
- Déboguer
- Journal des événements
- AzureAppServices
- TraceSource
- EventSource

**Niveau minimal de valeur par défaut**

Il existe un paramètre de niveau minimal qui entre en vigueur uniquement si aucune règle de configuration ou de code s’appliquent à une catégorie et un fournisseur donné. L’exemple suivant montre comment définir le niveau minimal :

[!code-csharp[](logging/sample2/Program.cs?name=snippet_MinLevel&highlight=3)]

Si vous ne définissez pas explicitement le niveau minimal, la valeur par défaut est `Information`, ce qui signifie que `Trace` et `Debug` journaux sont ignorés.

**Fonctions de filtrage**

Vous pouvez écrire du code dans une fonction de filtre à appliquer les règles de filtrage. Une fonction de filtre est appelée pour tous les fournisseurs et les catégories qui n’ont pas de règles attribués par la configuration ou du code. Code de la fonction a accès à un type de fournisseur, catégorie et niveau de journal pour déterminer si un message doit être connecté. Exemple :

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Certains fournisseurs de journalisation vous permettent de spécifier quand des journaux doivent être écrites dans un support de stockage ou ignorés en fonction de la catégorie et le niveau de journal.

Le `AddConsole` et `AddDebug` les méthodes d’extension fournissent des surcharges qui vous permettent de passer dans les critères de filtrage. L’exemple de code suivant provoque le fournisseur de la console ignorer les journaux ci-dessous `Warning` de niveau, alors que le fournisseur de débogage ignore les journaux générés par l’infrastructure.

[!code-csharp[](logging/sample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

Le `AddEventLog` méthode a une surcharge qui prend un `EventLogSettings` instance, ce qui peut contenir une fonction de filtrage dans son `Filter` propriété. Le fournisseur de TraceSource ne fournit aucune de ces surcharges, étant donné que son niveau de journalisation et d’autres paramètres sont basés sur le `SourceSwitch` et `TraceListener` qu’il utilise.

Vous pouvez définir des règles de filtrage pour tous les fournisseurs qui sont inscrits auprès d’un `ILoggerFactory` instance à l’aide de la `WithFilter` méthode d’extension. L’exemple suivant limite journaux framework (catégorie commence par « Microsoft » ou « Système ») pour les avertissements tout en laissant le journal d’application au niveau de débogage.

[!code-csharp[](logging/sample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

Si vous souhaitez utiliser le filtrage pour empêcher que tous les journaux en cours d’écriture pour une catégorie particulière, vous pouvez spécifier `LogLevel.None` en tant que le niveau de journalisation minimale pour cette catégorie. La valeur entière de `LogLevel.None` est 6, ce qui est supérieur à `LogLevel.Critical` (5).

Le `WithFilter` méthode d’extension est fournie par le [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) package NuGet. La méthode retourne un nouveau `ILoggerFactory` instance qui permettra de filtrer les messages du journal transmis à tous les fournisseurs d’enregistreur d’événements enregistrés avec celui-ci. Il n’affecte pas d’autres `ILoggerFactory` instances, y compris l’original `ILoggerFactory` instance.

---

## <a name="log-scopes"></a>Étendues de journal

Vous pouvez regrouper un ensemble d’opérations logiques dans un *étendue* pour pouvoir attacher les mêmes données pour chaque journal est créé dans le cadre de cet ensemble.  Par exemple, vous pourriez chaque journal créé dans le cadre du traitement d’une transaction pour inclure l’ID de transaction.

Une étendue est un `IDisposable` type qui est retourné par la `ILogger.BeginScope<TState>` méthode et dure jusqu'à ce qu’elle est supprimée. Vous utilisez une étendue en intégrant votre enregistreur d’événements appelle dans un `using` bloquer, comme illustré ici :

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Le code suivant permet d’étendues pour le fournisseur de la console :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Dans *Program.cs*:

[!code-csharp[](logging/sample2/Program.cs?name=snippet_Scopes&highlight=4)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Dans *Startup.cs*:

[!code-csharp[](logging/sample/Startup.cs?name=snippet_Scopes&highlight=6)]

---

Chaque message du journal inclut les informations incluses dans l’étendue :

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Fournisseurs de journalisation intégrés

ASP.NET Core fourni les fournisseurs suivants :

* [Console](#console)
* [Déboguer](#debug)
* [EventSource](#eventsource)
* [EventLog](#eventlog)
* [TraceSource](#tracesource)
* [Azure App Service](#appservice)

<a id="console"></a>
### <a name="the-console-provider"></a>Le fournisseur de la console

Le [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package de fournisseur envoie la sortie de journal dans la console. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddConsole()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddConsole()
```

[Les surcharges AddConsole](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.consoleloggerextensions) permettent de vous transmettez un un niveau de journalisation minimale, une fonction de filtre et une valeur booléenne qui indique si les étendues sont prises en charge.  Une autre option consiste à passer dans un `IConfiguration` objet, que vous pouvez spécifier des niveaux d’enregistrement et de prise en charge des étendues. 

Si vous envisagez de la console de fournisseur pour une utilisation en production, n’oubliez pas qu’il a un impact significatif sur les performances.

Lorsque vous créez un nouveau projet dans Visual Studio, le `AddConsole` méthode ressemble à ceci :

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

Ce code fait référence à la `Logging` section de la *appSettings.json* fichier :

[!code-json[](logging/sample//appsettings.json)]

Les paramètres des journaux de framework limite aux avertissements tout en autorisant l’application pour se connecter au niveau du débogage, comme expliqué dans la [filtrage de journal](#log-filtering) section. Pour plus d’informations, consultez [Configuration](configuration.md).

---

<a id="debug"></a>
### <a name="the-debug-provider"></a>Le fournisseur de débogage

Le [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package de fournisseur écrit la sortie du journal à l’aide de la [System.Diagnostics.Debug](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.debug#System_Diagnostics_Debug) classe (`Debug.WriteLine` les appels de méthode).

Sur Linux, ce fournisseur écrit des journaux sur */var/log/message*.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddDebug()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddDebug()
```

[Les surcharges AddDebug](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.debugloggerfactoryextensions) permettent de vous transmettez un niveau de journal minimale ou une fonction de filtre.

---

<a id="eventsource"></a>
### <a name="the-eventsource-provider"></a>Le fournisseur de source d’événement

Pour les applications qui ciblent ASP.NET Core 1.1.0 ou une version ultérieure, le [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) package de fournisseur peut implémenter le suivi d’événements. Sous Windows, il utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803). Le fournisseur est inter-plateformes, mais il en existe aucun événement collecte et l’affichage des outils pour Linux ou macOS. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddEventSourceLogger()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventSourceLogger()
```

---

Un bon moyen de collecter et d’afficher les journaux consiste à utiliser le [PerfView utilitaire](https://www.microsoft.com/download/details.aspx?id=28567). Il existe d’autres outils pour l’affichage des journaux ETW, mais PerfView fournit la meilleure expérience pour travailler avec les événements ETW émis par ASP.NET. 

Pour configurer PerfView pour collecter les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la **des fournisseurs supplémentaires** liste. (Ne pas manquer de l’astérisque au début de la chaîne).

![Perfview des fournisseurs supplémentaires](logging/_static/perfview-additional-providers.png)

Capture des événements sur Nano Server requiert une installation supplémentaire :

* Se connecter à la communication à distance PowerShell à Nano Server :

  ```powershell
  Enter-PSSession [name]
  ```

* Créer une session ETW :

  ```powershell
  New-EtwTraceSession -Name "MyAppTrace" -LocalFilePath C:\trace.etl
  ```

* Ajouter des fournisseurs ETW pour [CLR](https://msdn.microsoft.com/library/ff357718), ASP.NET Core et autres en fonction des besoins. Le fournisseur ASP.NET Core GUID est `3ac73b97-af73-50e9-0822-5da4367920d0`. 

  ```powershell
  Add-EtwTraceProvider -Guid "{e13c0d23-ccbc-4e12-931b-d9cc2eee27e4}" -SessionName MyAppTrace
  Add-EtwTraceProvider -Guid "{3ac73b97-af73-50e9-0822-5da4367920d0}" -SessionName MyAppTrace
  ```

* Exécuter le site et faire autant d’actions que vous voulez des informations de traçage pour.

* Arrêter la session de suivi est terminée :

  ```powershell
  Stop-EtwTraceSession -Name "MyAppTrace"
  ```

Résultant *C:\trace.etl* fichier peut être analysé avec PerfView comme sur les autres éditions de Windows.

<a id="eventlog"></a>
### <a name="the-windows-eventlog-provider"></a>Le fournisseur du journal des événements Windows

Le [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) package de fournisseur envoie la sortie de journal dans le journal des événements Windows.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddEventLog()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventLog()
```

[Les surcharges de méthode AddEventLog](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.eventloggerfactoryextensions) permettent de vous transmettez `EventLogSettings` ou un niveau de journalisation minimale.

---

<a id="tracesource"></a>
### <a name="the-tracesource-provider"></a>Le fournisseur de TraceSource

Le [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) fournisseur package utilise le [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) bibliothèques et les fournisseurs.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddTraceSource(sourceSwitchName);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

---

[Les surcharges AddTraceSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.tracesourcefactoryextensions) permettent de vous transmettez un changement de source et un écouteur de suivi.

Pour utiliser ce fournisseur, une application doit s’exécuter sur le .NET Framework (plutôt que du .NET Core). Le permet de fournisseur pour router des messages à une variété de [écouteurs](https://msdn.microsoft.com/library/4y5y10s7), telles que la [TextWriterTraceListener](https://msdn.microsoft.com/library/system.diagnostics.textwritertracelistener) utilisé dans l’exemple d’application.

L’exemple suivant configure un `TraceSource` fournisseur qui enregistre des `Warning` et des messages plus élevées dans la fenêtre de console.

[!code-csharp[](logging/sample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

<a id="appservice"></a>
### <a name="the-azure-app-service-provider"></a>Le fournisseur de services d’application Azure

Le [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) package de fournisseur écrit des journaux dans des fichiers texte dans le système de fichiers d’une application de Service d’applications Azure et en [stockage d’objets blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure. Le fournisseur est disponible uniquement pour les applications qui ciblent ASP.NET Core 1.1.0 ou ultérieure. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

> [!NOTE]
> ASP.NET Core 2.0 est en version préliminaire.  Les applications créées avec la dernière version de l’aperçu peut ne pas fonctionner lors du déploiement vers Azure App Service. Quand ASP.NET Core 2.0 est publiée, Azure App Service s’exécutera 2.0 applications et le Service d’application Azure fournisseur fonctionne comme indiqué ici.

Vous n’êtes pas obligé d’installer le package du fournisseur ou appelez le `AddAzureWebAppDiagnostics` méthode d’extension.  Le fournisseur est automatiquement disponible pour votre application lorsque vous déployez l’application sur Azure App Service.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

Un `AddAzureWebAppDiagnostics` surcharge vous permet de transmettre dans [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs), avec laquelle vous pouvez remplacer les paramètres par défaut tels que le modèle de sortie de journalisation, nom d’objet blob et limite de taille de fichier. (*Modèle sortie* est une chaîne de format de message qui est appliquée à tous les journaux sur celui que vous fournissez lorsque vous appelez un `ILogger` méthode.)  

---

Lorsque vous déployez une application de Service d’applications, votre application respecte les paramètres dans le [journaux de Diagnostic](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/#enablediag) section de la **du Service d’applications** page du portail Azure. Lorsque vous modifiez ces paramètres, les modifications prennent effet immédiatement sans redémarrer l’application ou de redéployer le code à ce dernier. 

![Paramètres de journalisation d’Azure](logging/_static/azure-logging-settings.png)

L’emplacement par défaut pour les fichiers journaux est le *D:\\domestique\\LogFiles\\Application* dossier et le nom de fichier par défaut est *yyyymmdd.txt de diagnostics*. La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal par défaut des fichiers conservés est 2. Le nom d’objet blob par défaut est *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*. Pour plus d’informations sur le comportement par défaut, consultez [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs).

Le fournisseur fonctionne uniquement lorsque votre projet s’exécute dans l’environnement Windows Azure.  Il n’a aucun effet lorsque vous exécutez localement &mdash; il n’écrit pas dans les fichiers locaux ou de stockage de développement local pour les objets BLOB.

## <a name="third-party-logging-providers"></a>Modules fournisseurs d’informations de tiers

Voici certaines infrastructures de journalisation de l’application tierce qui fonctionnent avec ASP.NET Core :

* [ELMAH.IO](https://github.com/elmahio/Elmah.Io.Extensions.Logging) -fournisseur pour le service Elmah.Io

* [JSNLog](http://jsnlog.com) -enregistre les exceptions JavaScript et autres événements côté client dans votre journal côté serveur.

* [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) -fournisseur pour le service Loggr

* [NLog](https://github.com/NLog/NLog.Extensions.Logging) -fournisseur pour la bibliothèque NLog

* [Serilog](https://github.com/serilog/serilog-framework-logging) -fournisseur pour la bibliothèque Serilog

Certaines infrastructures tierces faire [journalisation sémantique, également appelée enregistrement structuré](http://programmers.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

À l’aide d’une infrastructure tierce est semblable à celle des fournisseurs intégrés : ajouter un package NuGet à votre projet et appeler une méthode d’extension sur `ILoggerFactory`. Pour plus d’informations, consultez la documentation de chaque framework.

Vous pouvez créer vos propres fournisseurs personnalisés, pour prendre en charge d’autres infrastructures de journalisation ou vos propres exigences de journalisation.

---
title: Journalisation dans ASP.NET Core
author: ardalis
description: "Découvrez plus en détail le framework de journalisation dans ASP.NET Core. Apprenez à utiliser les fournisseurs de journalisation intégrés et découvrez-en plus sur les fournisseurs tiers les plus courants."
manager: wpickett
ms.author: tdykstra
ms.date: 12/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/logging/index
ms.openlocfilehash: c8152b94311acb672e9810828b634c744cb46eae
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="introduction-to-logging-in-aspnet-core"></a>Introduction à la journalisation dans ASP.NET Core

Article rédigé par [Steve Smith](https://ardalis.com/) et [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core prend en charge une API de journalisation qui fonctionne avec divers fournisseurs de journalisation. Les fournisseurs intégrés vous permettent d’envoyer des journaux à une ou plusieurs destinations. Vous pouvez aussi incorporer un framework de journalisation tiers. Cet article explique comment utiliser les API et fournisseurs de journalisation intégrés dans votre code.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/sample2) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

---

## <a name="how-to-create-logs"></a>Comment créer des journaux

Pour créer des journaux, prenez un objet `ILogger` dans le conteneur [injection de dépendance](xref:fundamentals/dependency-injection) :

[!code-csharp[](index/sample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Appelez ensuite des méthodes de journalisation sur cet objet logger :

[!code-csharp[](index/sample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Cet exemple crée des journaux en utilisant la classe `TodoController` comme *catégorie*. Les catégories sont décrites [plus loin dans cet article](#log-category).

ASP.NET Core ne fournit pas de méthodes logger asynchrones, car la journalisation étant normalement très rapide, l’utilisation de méthodes asynchrones est inutile. Si ce n’est pas votre cas, envisagez plutôt de changer de processus de journalisation. Si votre magasin de données est lent, écrivez d’abord les messages de journal dans un magasin plus rapide, puis déplacez-les ensuite dans un magasin lent. Par exemple, enregistrez les messages dans une file d’attente qui est lue et conservée dans le stockage lent par un autre processus.

## <a name="how-to-add-providers"></a>Comment ajouter des fournisseurs

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Un fournisseur de journalisation prend les messages que vous créez avec un objet `ILogger`, puis les affiche ou les enregistre. Par exemple, le fournisseur Console affiche les messages dans la console, tandis que le fournisseur Azure App Service les enregistre dans le stockage Blob Azure.

Pour utiliser un fournisseur, appelez la méthode d’extension `Add<ProviderName>` du fournisseur dans *Program.cs* :

[!code-csharp[](index/sample2/Program.cs?name=snippet_ExpandDefault&highlight=16,17)]

Le modèle de projet par défaut active la journalisation avec la méthode [CreateDefaultBuilder](https://docs.microsoft.com/ dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder?view=aspnetcore-2.0#Microsoft_AspNetCore_WebHost_CreateDefaultBuilder_System_String___) :

[!code-csharp[](index/sample2/Program.cs?name=snippet_TemplateCode&highlight=7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Un fournisseur de journalisation prend les messages que vous créez avec un objet `ILogger`, puis les affiche ou les enregistre. Par exemple, le fournisseur Console affiche les messages dans la console, tandis que le fournisseur Azure App Service les enregistre dans le stockage Blob Azure.

Pour utiliser un fournisseur, installez son package NuGet et appelez la méthode d’extension du fournisseur sur une instance de `ILoggerFactory`, comme dans l’exemple suivant.

[!code-csharp[](index/sample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

Le conteneur [injection de dépendance](xref:fundamentals/dependency-injection) dans ASP.NET Core fournit l’instance `ILoggerFactory`. Les méthodes d’extension `AddConsole` et `AddDebug` sont définies dans les packages [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) et [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/). Chaque méthode d’extension appelle la méthode `ILoggerFactory.AddProvider`, en passant une instance du fournisseur. 

> [!NOTE]
> L’exemple d’application utilisé dans cet article ajoute des fournisseurs de journalisation dans la méthode `Configure` de la classe `Startup`. Si vous souhaitez obtenir la sortie de journal du code exécuté plus haut, ajoutez les fournisseurs de journalisation dans le constructeur de classe `Startup` à la place. 

---

Vous trouverez des informations sur chaque [fournisseur de journalisation intégré](#built-in-logging-providers) et des liens vers des [fournisseurs de journalisation tiers](#third-party-logging-providers) plus loin dans cet article.

## <a name="sample-logging-output"></a>Exemple de sortie de la journalisation

Avec l’exemple de code présenté dans la section précédente, les journaux s’affichent dans la console quand vous exécutez l’application à partir de la ligne de commande. Voici un exemple de sortie de la console :

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
 
Ces journaux ont été créés en accédant à `http://localhost:5000/api/todo/0`, qui déclenche l’exécution des deux appels `ILogger` montrés dans la section précédente.

Voici un exemple des mêmes journaux affichés dans la fenêtre Débogage quand vous exécutez l’exemple d’application dans Visual Studio :

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404 
```

Les enregistrements de journal qui ont été créés par les appels de `ILogger` illustrés dans la section précédente commencent par « TodoApi.Controllers.TodoController ». Ceux qui commencent par les catégories « Microsoft » proviennent d’ASP.NET Core. ASP.NET Core et votre code d’application utilisent la même API de journalisation et les mêmes fournisseurs de journalisation.

Les autres sections de cet article détaillent certains points et présentent les options de journalisation.

## <a name="nuget-packages"></a>Packages NuGet

Les interfaces `ILogger` et `ILoggerFactory` se trouvent dans [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), et leurs implémentations par défaut se trouvent dans [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/).

## <a name="log-category"></a>Catégorie de journal

Une *catégorie* est incluse dans chaque journal que vous créez. Vous spécifiez la catégorie quand vous créez un objet `ILogger`. La catégorie peut être n’importe quelle chaîne, mais par convention, utilisez le nom complet de la classe à partir de laquelle les journaux sont écrits. Exemple : « TodoApi.Controllers.TodoController ».

Vous pouvez spécifier la catégorie sous forme de chaîne ou utiliser une méthode d’extension qui dérive la catégorie du type. Pour spécifier la catégorie sous forme de chaîne, appelez `CreateLogger` sur une instance `ILoggerFactory`, comme indiqué ci-dessous.

[!code-csharp[](index/sample//Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

L’utilisation de `ILogger<T>` est généralement plus simple, comme le montre l’exemple suivant.

[!code-csharp[](index/sample//Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Cela équivaut à appeler `CreateLogger` avec le nom de type complet `T`.

## <a name="log-level"></a>Niveau du journal

Vous spécifiez un niveau [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel) pour chaque enregistrement écrit dans le journal. Le niveau de journalisation indique le degré de gravité ou d’importance. Par exemple, vous pouvez écrire un enregistrement `Information` quand une méthode se termine normalement, un enregistrement `Warning` quand une méthode retourne un code d’erreur 404 et un enregistrement `Error` quand le code intercepte une exception inattendue.

Dans l’exemple de code suivant, les noms des méthodes (par exemple, `LogWarning`) spécifient le niveau de journalisation. Le premier paramètre est [l’ID de l’événement de journal](#log-event-id). Le deuxième paramètre est un [modèle de message](#log-message-template) qui contient des espaces réservés pour les valeurs d’argument fournies par les autres paramètres de méthode. Les paramètres de méthode sont expliqués en détail plus loin dans cet article.

[!code-csharp[](index/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Les méthodes de journal qui incluent le niveau dans leur nom sont des [méthodes d’extension pour ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions). En arrière-plan, ces méthodes appellent une méthode `Log` qui a un paramètre `LogLevel`. Vous pouvez appeler la méthode `Log` directement au lieu d’appeler ces méthodes d’extension, mais la syntaxe est relativement complexe. Pour plus d’informations, consultez [l’interface ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) et le [code source des extensions logger](https://github.com/aspnet/Logging/blob/master/src/Microsoft.Extensions.Logging.Abstractions/LoggerExtensions.cs).

ASP.NET Core définit les [niveaux de journalisation](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel) suivants, classés selon leur degré de gravité (du plus faible au plus élevé).

* Trace = 0

  Fournit des informations qui sont uniquement destinées aux développeurs pour le débogage. Ces messages peuvent contenir des données d’application sensibles. Ils ne doivent donc pas être activés dans un environnement de production. *Niveau désactivé par défaut.* Exemple : `Credentials: {"User":"someuser", "Password":"P@ssword"}`

* Debug = 1

  Fournit des informations ayant une utilité à court terme durant le développement et le débogage. Exemple : `Entering method Configure with flag set to true.` En règle générale, vous n’activez pas les journaux de niveau `Debug` dans un environnement de production, sauf si vous en avez besoin pour résoudre des problèmes, en raison du grand nombre d’enregistrements générés.

* Information = 2

  Fournit des informations permettant d’effectuer le suivi du flux général de l’application. Ces journaux ont généralement une utilité à long terme. Exemple : `Request received for path /api/todo`

* Warning = 3

  Fournit des messages d’avertissement sur les événements anormaux ou inattendus dans le flux de l’application. Il peut s’agir d’erreurs ou d’autres conditions qui ne provoquent pas l’arrêt de l’application, mais qui doivent être examinées. Le niveau de journalisation `Warning` est généralement utilisé pour les exceptions gérées. Exemple : `FileNotFoundException for file quotes.txt.`

* Error = 4

  Fournit des informations sur des erreurs et des exceptions qui ne peuvent pas être gérées. Ces messages indiquent un échec de l’activité ou de l’opération en cours (par exemple, la requête HTTP actuellement exécutée), pas un échec au niveau de l’application. Exemple de message de journal : `Cannot insert record due to duplicate key violation.`

* Critical = 5

  Fournit des informations sur des échecs qui nécessitent un examen immédiat. Exemples : perte de données, espace disque insuffisant.

Le niveau de journalisation vous permet de contrôler la taille de la sortie de journal qui est écrite sur un média de stockage ou dans une fenêtre d’affichage. Par exemple, dans un environnement de production, vous allez peut-être choisir d’écrire tous les enregistrements de journal des niveaux `Information` et inférieurs dans un magasin de données de volume, mais d’écrire tous les enregistrements des niveaux `Warning` et supérieurs dans un magasin de données de valeur. Durant le développement, vous écrivez en principe les enregistrements des niveaux `Warning` ou supérieurs dans la console. Ensuite, pendant la phase de débogage, vous pouvez ajouter le niveau `Debug`. La section [Filtrage de journal](#log-filtering) plus loin dans cet article explique comment déterminer les niveaux de journalisation gérés par un fournisseur.

Le framework ASP.NET Core écrit des enregistrements de niveau `Debug` pour les événements de framework. Dans les exemples de journaux présentés plus haut dans cet article, les enregistrements du niveau `Debug` n’y figuraient pas puisque ceux en dessous du niveau `Information` étaient exclus. Voici un exemple de journal dans la console si vous exécutez l’application configurée pour afficher les enregistrements de niveaux `Debug` et supérieurs pour le fournisseur Console.

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

Vous pouvez spécifier un *ID d’événement* pour chaque enregistrement écrit dans le journal. L’exemple d’application effectue cette opération à l’aide d’une classe `LoggingEvents` définie localement :

[!code-csharp[](index/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/sample//Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Un ID d’événement est une valeur entière qui vous permet d’associer deux événements dans un ensemble d’événements enregistrés. Par exemple, un enregistrement correspondant à l’ajout d’un article à un panier d’achat peut avoir l’ID d’événement 1000, et celui pour le paiement d’un achat avoir l’ID d’événement 1001.

Dans la sortie de journal, l’ID d’événement peut être stocké dans un champ ou être inclus dans le message texte, selon le fournisseur. Le fournisseur Debug n’affiche pas les ID d’événement, à l’inverse du fournisseur Console qui les affiche entre crochets à la suite de la catégorie :

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Modèle de message de journal

Quand vous écrivez un message de journal, vous fournissez un modèle de message. Le modèle de message peut être une chaîne, ou contenir des espaces réservés nommés dans lesquels les valeurs d’argument sont placées. Le modèle n’est pas une chaîne de format. Les espaces réservés doivent être nommés, pas numérotés.

[!code-csharp[](index/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour fournir leurs valeurs. Si vous avez le code suivant :

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Le message de journal obtenu ressemble à ceci :

```
Parameter values: parm1, parm2
```

Le framework de journalisation met en forme les messages de cette façon pour permettre aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Étant donné que les arguments eux-mêmes sont passés au système de journalisation, et pas seulement le modèle de message mis en forme, les fournisseurs de journalisation peuvent stocker les valeurs de paramètre sous forme de champs en plus du modèle de message. Si vous envoyez votre sortie de journal vers le Stockage Table Azure, votre appel de la méthode logger ressemble à ceci :

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

Chaque entité Table Azure peut avoir les propriétés `ID` et `RequestTime`, ce qui simplifie les requêtes sur les données de journal. Vous pouvez rechercher tous les enregistrements de journal compris dans une plage `RequestTime` spécifique sans avoir besoin d’analyser le délai d’expiration du message texte.

## <a name="logging-exceptions"></a>Journalisation des exceptions

Les méthodes logger ont des surcharges qui vous permettent de passer une exception, comme dans l’exemple suivant :

[!code-csharp[](index/sample//Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Tous les fournisseurs ne gèrent pas les informations sur les exceptions de la même façon. Voici un exemple de sortie du fournisseur Debug extrait du code montré plus haut.

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrage de journal

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Vous pouvez spécifier un niveau de journalisation minimum pour une catégorie ou un fournisseur spécifique, ou pour l’ensemble des fournisseurs ou des catégories. Les enregistrements de journal en dessous du niveau minimum ne sont pas passés à ce fournisseur, et ne sont donc pas affichés ou stockés. 

Si vous souhaitez supprimer tous les enregistrements de journal, vous pouvez spécifier `LogLevel.None` comme niveau de journal minimum. La valeur entière de `LogLevel.None` est 6, soit un niveau supérieur à `LogLevel.Critical` (5).

**Créer des règles de filtre dans la configuration**

Les modèles de projet créent du code qui appelle `CreateDefaultBuilder` afin de configurer la journalisation pour les fournisseurs Console et Debug. La méthode `CreateDefaultBuilder` définit également la journalisation pour rechercher la configuration dans une section `Logging`, à l’aide de code similaire à celui-ci :

[!code-csharp[](index/sample2/Program.cs?name=snippet_ExpandDefault&highlight=15)]

Les données de configuration spécifient des niveaux de journalisation minimum par fournisseur et par catégorie, comme dans l’exemple suivant :

[!code-json[](index/sample2/appsettings.json)]

Ce code JSON crée six règles de filtre : une pour le fournisseur Debug, quatre pour le fournisseur Console et une commune à tous les fournisseurs. Vous verrez plus tard de quelle façon une seule de ces règles est choisie pour chaque fournisseur au moment de la création d’un objet `ILogger`.

**Règles de filtre dans le code**

Vous pouvez ajouter des règles de filtre dans le code, comme indiqué dans l’exemple suivant :

[!code-csharp[](index/sample2/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

Le second `AddFilter` spécifie le fournisseur Debug par son nom de type. Le premier `AddFilter` s’applique à tous les fournisseurs, car il ne spécifie aucun type de fournisseur.

**Mode d’application des règles de filtre**

Les données de configuration et le code `AddFilter` contenus dans les exemples précédents créent les règles présentées dans le tableau suivant. Les six premières proviennent de l’exemple de configuration et les deux dernières, de l’exemple de code.

| nombre | Fournisseur      | Catégories commençant par...          | Niveau de journalisation minimum |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Débogage         | Toutes les catégories                          | Information       |
| 2      | Console       | Microsoft.AspNetCore.Mvc.Razor.Internal | Warning           |
| 3      | Console       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Débogage             |
| 4      | Console       | Microsoft.AspNetCore.Mvc.Razor          | Error             |
| 5      | Console       | Toutes les catégories                          | Information       |
| 6      | Tous les fournisseurs | Toutes les catégories                          | Débogage             |
| 7      | Tous les fournisseurs | Système                                  | Débogage             |
| 8      | Débogage         | Microsoft                               | Suivi             |

Quand vous créez un objet `ILogger` d’écriture d’enregistrements de journal, l’objet `ILoggerFactory` sélectionne une seule règle par fournisseur à appliquer à cet objet. Tous les messages écrits par cet objet `ILogger` sont filtrés selon les règles sélectionnées. La règle la plus spécifique pouvant être appliquée à chaque paire catégorie/fournisseur est sélectionnée parmi les règles disponibles.

L’algorithme suivant est utilisé pour chaque fournisseur quand un objet `ILogger` est créé pour une catégorie donnée :

* Sélectionnez toutes les règles qui correspondent au fournisseur ou à son alias. Si aucune règle n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.
* À partir du résultat de l’étape précédente, sélectionnez les règles ayant le plus long préfixe de catégorie correspondant. Si aucune règle n’est trouvée, sélectionnez toutes les règles qui ne spécifient aucune catégorie.
* Si plusieurs règles sont sélectionnées, utilisez la **dernière**.
* Si aucune règle n’est sélectionnée, utilisez `MinimumLevel`.
 
Par exemple, supposons que vous utilisez la liste de règles précédente et que vous créez un objet `ILogger` pour la catégorie « Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine » :

* Les règles 1, 6 et 8 s’appliquent au fournisseur Debug. La règle 8 est sélectionnée, car c’est la plus spécifique.
* Les règles 3, 4, 5 et 6 s’appliquent au fournisseur Console. La règle 3 est la plus spécifique.

Quand vous créez des enregistrements de journal avec un objet `ILogger` pour la catégorie « Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine », les enregistrements des niveaux `Trace` et supérieurs sont envoyés au fournisseur Debug, et ceux des niveaux `Debug` et supérieurs sont envoyés au fournisseur Console.

**Alias de fournisseur**

Vous pouvez utiliser le nom de type pour spécifier un fournisseur dans une configuration, mais chaque fournisseur définit un *alias* plus court et plus simple d’emploi. Pour les fournisseurs intégrés, utilisez les alias suivants :

- Console
- Débogage
- EventLog
- AzureAppServices
- TraceSource
- EventSource

**Niveau minimum par défaut**

Un paramètre de niveau minimum est utilisé uniquement si aucune règle de la configuration ou du code ne s’applique à une catégorie ou un fournisseur spécifique. L’exemple suivant montre comment définir le niveau minimum :

[!code-csharp[](index/sample2/Program.cs?name=snippet_MinLevel&highlight=3)]

Si vous ne définissez pas explicitement le niveau minimum, la valeur par défaut est `Information`, ce qui signifie que les niveaux `Trace` et `Debug` sont ignorés.

**Fonctions de filtre**

Vous pouvez écrire du code dans une fonction de filtre pour appliquer des règles de filtrage. Une fonction de filtre est appelée pour tous les fournisseurs et toutes les catégories pour lesquels la configuration ou le code n’applique aucune règle. Le code dans la fonction a accès au type de fournisseur, à la catégorie et au niveau de journalisation pour déterminer si un message doit ou non être enregistré. Exemple :

[!code-csharp[](index/sample2/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Certains fournisseurs de journalisation vous permettent de spécifier quand des enregistrements de journal doivent être écrits sur un média de stockage, ou au contraire ignorés, en fonction de la catégorie et du niveau de journalisation.

Les méthodes d’extension `AddConsole` et `AddDebug` fournissent des surcharges qui vous permettent de passer des critères de filtrage. Dans l’exemple de code suivant, le fournisseur Console ignore les enregistrements en dessous du niveau `Warning`, et le fournisseur Debug ignore les enregistrements créés par le framework.

[!code-csharp[](index/sample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

La méthode `AddEventLog` a une surcharge qui accepte une instance `EventLogSettings`, dont la propriété `Filter` peut contenir une fonction de filtre. Le fournisseur TraceSource ne fournit aucune de ces surcharges, étant donné que son niveau de journalisation et d’autres paramètres dépendent des `SourceSwitch` et `TraceListener` qu’il utilise.

Vous pouvez définir des règles de filtrage pour tous les fournisseurs qui sont inscrits auprès d’une instance `ILoggerFactory` à l’aide de la méthode d’extension `WithFilter`. L’exemple suivant limite les enregistrements du framework (ceux dont la catégorie commence par « Microsoft » ou « System ») aux avertissements, mais active ceux de l’application au niveau Debug.

[!code-csharp[](index/sample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

Si vous souhaitez utiliser le filtrage pour empêcher l’écriture de tous les enregistrements correspondant à une catégorie particulière, spécifiez `LogLevel.None` comme niveau de journalisation minimum pour cette catégorie. La valeur entière de `LogLevel.None` est 6, soit un niveau supérieur à `LogLevel.Critical` (5).

La méthode d’extension `WithFilter` est fournie par le package NuGet [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter). La méthode retourne une nouvelle instance `ILoggerFactory` qui filtre les messages de journal passés à tous les fournisseurs de journalisation inscrits dans cette méthode. Cela n’affecte pas les autres instances `ILoggerFactory`, y compris l’instance `ILoggerFactory` initiale.

---

## <a name="log-scopes"></a>Étendues de journal

Vous pouvez regrouper un ensemble d’opérations logiques dans une *étendue* pour joindre les mêmes données à chaque journal qui est créé dans cet ensemble. Par exemple, vous pouvez spécifier que chaque enregistrement créé pendant le traitement d’une transaction contienne l’ID de la transaction.

Une étendue est un type `IDisposable` qui est retourné par la méthode `ILogger.BeginScope<TState>`. Elle s’applique tant qu’elle n’est pas supprimée. Pour utiliser une étendue, incluez les appels de l’objet logger dans un bloc `using`, comme illustré ici :

[!code-csharp[](index/sample//Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Le code suivant active les étendues pour le fournisseur Console :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Dans *Program.cs* :

[!code-csharp[](index/sample2/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> La configuration de l’option logger `IncludeScopes` pour la console est nécessaire pour activer la journalisation basée sur des étendues. La configuration de `IncludeScopes` à l’aide des fichiers de configuration *appsettings* sera possible dans la version ASP.NET Core 2.1.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Dans *Startup.cs* :

[!code-csharp[](index/sample/Startup.cs?name=snippet_Scopes&highlight=6)]

---

Chaque message de journal fournit les informations incluses dans l’étendue :

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Fournisseurs de journalisation intégrés

ASP.NET Core contient les fournisseurs suivants :

* [Console](#console)
* [Déboguer](#debug)
* [EventSource](#eventsource)
* [EventLog](#eventlog)
* [TraceSource](#tracesource)
* [Azure App Service](#appservice)

<a id="console"></a>
### <a name="the-console-provider"></a>Fournisseur Console

Le package de fournisseur [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envoie la sortie de journal dans la console. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddConsole()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddConsole()
```

Les [surcharges AddConsole](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.consoleloggerextensions) vous permettent de passer un niveau de journalisation minimum, une fonction de filtre et une valeur booléenne qui indique si les étendues sont prises en charge. Une autre option consiste à passer un objet `IConfiguration`, qui permet de spécifier la prise en charge des étendues et les niveaux de journalisation. 

Si vous envisagez d’utiliser le fournisseur Console dans un environnement de production, sachez qu’il impacte fortement les performances.

Quand vous créez un projet dans Visual Studio, la méthode `AddConsole` ressemble à ceci :

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

Ce code fait référence à la section `Logging` du fichier *appSettings.json* :

[!code-json[](index/sample//appsettings.json)]

Les paramètres définis limitent les enregistrements du framework aux avertissements, mais active ceux de l’application au niveau Debug, comme cela est expliqué dans la section [Filtrage de journal](#log-filtering). Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).

---

<a id="debug"></a>
### <a name="the-debug-provider"></a>Fournisseur Debug

Le package de fournisseur [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) écrit la sortie de journal à l’aide de la classe [System.Diagnostics.Debug](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.debug#System_Diagnostics_Debug) (appels de la méthode `Debug.WriteLine`).

Sur Linux, ce fournisseur écrit la sortie de journal dans */var/log/message*.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddDebug()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddDebug()
```

Les [surcharges AddDebug](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.debugloggerfactoryextensions) vous permettent de passer un niveau de journal minimum ou une fonction de filtre.

---

<a id="eventsource"></a>
### <a name="the-eventsource-provider"></a>Fournisseur EventSource

Pour les applications qui ciblent ASP.NET Core 1.1.0 ou une version ultérieure, le package de fournisseur [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) peut implémenter le suivi des événements. Sur Windows, il utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803). Le fournisseur est multiplateforme, mais il ne prend pas en charge la collection d’événements ni les outils d’affichage pour Linux ou macOS. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddEventSourceLogger()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventSourceLogger()
```

---

[L’utilitaire PerfView](https://www.microsoft.com/download/details.aspx?id=28567) est très utile pour collecter et afficher les journaux. Il existe d’autres outils d’affichage des journaux ETW, mais PerfView est l’outil recommandé pour gérer les événements ETW générés par ASP.NET. 

Pour configurer PerfView afin qu’il collecte les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la liste des **fournisseurs supplémentaires**. (N’oubliez pas d’inclure l’astérisque au début de la chaîne.)

![Fournisseurs supplémentaires dans PerfView](index/_static/perfview-additional-providers.png)

La collecte d’événements sur Nano Server nécessite une configuration supplémentaire :

* Établissez une communication à distance PowerShell sur Nano Server :

  ```powershell
  Enter-PSSession [name]
  ```

* Créez une session ETW :

  ```powershell
  New-EtwTraceSession -Name "MyAppTrace" -LocalFilePath C:\trace.etl
  ```

* Ajoutez des fournisseurs ETW pour [CLR](https://docs.microsoft.com/dotnet/framework/performance/clr-etw-providers), ASP.NET Core et autres types en fonction de vos besoins. Le GUID du fournisseur ASP.NET Core est `3ac73b97-af73-50e9-0822-5da4367920d0`. 

  ```powershell
  Add-EtwTraceProvider -Guid "{e13c0d23-ccbc-4e12-931b-d9cc2eee27e4}" -SessionName MyAppTrace
  Add-EtwTraceProvider -Guid "{3ac73b97-af73-50e9-0822-5da4367920d0}" -SessionName MyAppTrace
  ```

* Exécutez le site et effectuez toutes les actions pour lesquelles vous voulez collecter des informations de suivi.

* Arrêtez la session de suivi quand vous avez terminé :

  ```powershell
  Stop-EtwTraceSession -Name "MyAppTrace"
  ```

Vous pouvez analyser le fichier *C:\trace.etl* obtenu avec PerfView de la même manière que sur les autres éditions de Windows.

<a id="eventlog"></a>
### <a name="the-windows-eventlog-provider"></a>Fournisseur EventLog Windows

Le package de fournisseur [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envoie la sortie de journal dans le journal des événements Windows.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddEventLog()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventLog()
```

Les [surcharges AddEventLog](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.eventloggerfactoryextensions) vous permettent de passer `EventLogSettings` ou un niveau de journalisation minimum.

---

<a id="tracesource"></a>
### <a name="the-tracesource-provider"></a>Fournisseur TraceSource

Le package de fournisseur [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) utilise les bibliothèques et fournisseurs [System.Diagnostics.TraceSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracesource).

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddTraceSource(sourceSwitchName);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

---

Les [surcharges AddTraceSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.tracesourcefactoryextensions) vous permettent de passer un commutateur de source et un écouteur de suivi.

Pour utiliser ce fournisseur, l’application doit s’exécuter sur .NET Framework (au lieu de .NET Core). Le fournisseur vous permet d’envoyer les messages vers différents [écouteurs](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/trace-listeners), tels que le [TextWriterTraceListener](https://docs.microsoft.com/dotnet/api/system.diagnostics.textwritertracelistenerr) utilisé dans l’exemple d’application.

L’exemple suivant configure un fournisseur `TraceSource` qui enregistre les messages `Warning` et de niveau supérieur dans la fenêtre de la console.

[!code-csharp[](index/sample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

<a id="appservice"></a>
### <a name="the-azure-app-service-provider"></a>Fournisseur Azure App Service

Le package de fournisseur [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) écrit les enregistrements de journal dans des fichiers texte dans le système de fichiers d’une application Azure App Service, et dans un [stockage Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure. Le fournisseur est disponible uniquement pour les applications qui ciblent ASP.NET Core 1.1.0 ou version ultérieure. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Si vous ciblez .NET Core, vous n’êtes pas obligé d’installer le package du fournisseur ou d’appeler explicitement `AddAzureWebAppDiagnostics`. Le fournisseur est automatiquement disponible pour une application qui est déployée sur Azure App Service.

Si vous ciblez le .NET Framework, ajoutez le package du fournisseur dans votre projet et appelez `AddAzureWebAppDiagnostics` :

```csharp
logging.AddAzureWebAppDiagnostics();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

Une surcharge `AddAzureWebAppDiagnostics` vous permet de passer [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs) pour substituer des paramètres par défaut, tels que le modèle de sortie de journalisation, le nom du blob et la limite de taille de fichier. (Le *modèle de sortie* est un modèle de message qui est appliqué à tous les enregistrements de journal au-dessus de celui fourni quand vous appelez une méthode `ILogger`.)

---

Quand vous effectuez un déploiement sur une application App Service, votre application applique les paramètres définis dans la section [Journaux de diagnostic](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/#enablediag) de la page **App Service** du portail Azure. Quand vous modifiez ces paramètres, les modifications prennent effet immédiatement. Vous n’avez pas à redémarrer l’application ni à redéployer le code sur cette dernière. 

![Paramètres de journalisation Azure](index/_static/azure-logging-settings.png)

L’emplacement par défaut des fichiers journaux est le dossier *D:\\racine\\LogFiles\\Application*, et le nom de fichier par défaut est *diagnostics-aaaammjj.txt*. La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal de fichiers conservés par défaut est de 2. Le nom par défaut du blob est *{app-name}{timestamp}/aaaa/mm/jj/hh/{guid}-applicationLog.txt*. Pour plus d’informations sur le comportement par défaut, consultez [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs).

Le fournisseur est activé uniquement si votre projet est exécuté dans l’environnement Azure. Il n’a aucun effet si vous l’exécutez localement &mdash; il n’écrit pas d’enregistrements dans les fichiers locaux ou dans le stockage de développement local pour les objets blob.

## <a name="third-party-logging-providers"></a>Fournisseurs de journalisation tiers

Voici des frameworks de journalisation tiers qui sont pris en charge dans ASP.NET Core :

* [elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging) : fournisseur pour le service Elmah.Io

* [JSNLog](http://jsnlog.com) : enregistre les exceptions JavaScript et d’autres événements côté client dans votre journal côté serveur.

* [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) : fournisseur pour le service Loggr

* [NLog](https://github.com/NLog/NLog.Extensions.Logging) : fournisseur pour la bibliothèque NLog

* [Serilog](https://github.com/serilog/serilog-extensions-logging) : fournisseur pour la bibliothèque Serilog

Certains frameworks tiers prennent en charge la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

L’utilisation d’un framework tiers est semblable à celle des fournisseurs intégrés : vous ajoutez un package NuGet à votre projet, puis vous appelez une méthode d’extension sur `ILoggerFactory`. Pour plus d’informations, consultez la documentation du framework.

Vous pouvez également créer vos propres fournisseurs personnalisés pour prendre en charge d’autres frameworks de journalisation ou répondre à des besoins de journalisation particuliers.

## <a name="azure-log-streaming"></a>Streaming des journaux Azure

Le streaming des journaux Azure vous permet d’afficher l’activité de journalisation en temps réel à partir des emplacements suivants : 

* Serveur d’applications 
* Serveur web
* Suivi des demandes ayant échoué 

Pour configurer le streaming des journaux Azure : 

* Accédez à la page **Journaux de diagnostic** à partir du portail de votre application.
* Définissez l’option **Journal des applications (Système de fichiers)** sur Activé. 

![Page Journaux de diagnostic du portail Azure](index/_static/azure-diagnostic-logs.png)

Accédez à la page **Streaming des journaux** pour afficher les messages d’application. Les messages sont enregistrés par application dans l’interface `ILogger`. 

![Streaming des journaux d’application dans le portail Azure](index/_static/azure-log-streaming.png)


## <a name="see-also"></a>Voir aussi

[Journalisation avancée avec LoggerMessage](xref:fundamentals/logging/loggermessage)

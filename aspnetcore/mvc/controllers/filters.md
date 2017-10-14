---
title: Filtres
author: ardalis
description: "Découvrez comment * filtres * travail et comment les utiliser dans ASP.NET MVC de base."
keywords: "ASP.NET Core, filtres, filtres mvc, filtres d’action, les filtres d’autorisation, les filtres de ressource, filtres de résultat, les filtres d’exception"
ms.author: tdykstra
manager: wpickett
ms.date: 12/12/2016
ms.topic: article
ms.assetid: 531bda08-aa5b-4471-8f08-96add22c8683
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/filters
ms.openlocfilehash: 34a5be6e77f8558c9b3c257575272e167ed95ea4
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="filters"></a>Filtres

Par [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)

*Filtres* dans ASP.NET MVC de base vous autorise à exécuter du code avant ou après certaines étapes du pipeline de traitement de la demande.

 Les filtres intégrés handle des tâches telles que l’autorisation (empêche l’accès aux ressources de pour qu'un utilisateur n’est pas autorisé), de vous assurer que toutes les demandes utiliser HTTPS et réponse (le pipeline de requête pour retourner une réponse mise en cache de court-circuit) de la mise en cache. 

Vous pouvez créer des filtres personnalisés pour gérer les problèmes transversaux pour votre application. Chaque fois que vous souhaitez éviter la duplication de code entre les actions, les filtres sont la solution. Par exemple, vous pouvez consolider les code dans un filtre d’exception de la gestion des erreurs.

[Afficher ou télécharger l’exemple à partir de GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

## <a name="how-do-filters-work"></a>Fonctionnement des filtres

Filtres sont exécutés dans le *pipeline d’invocation d’action MVC*, parfois appelé le *pipeline des filtres*.  Le pipeline de filtre s’exécute après que MVC sélectionne l’action à exécuter.

![La demande est traitée via un autre intergiciel (middleware), routage intergiciel (middleware), la sélection de l’Action et le Pipeline de Invocation d’Action MVC. Le traitement de la requête se poursuit par le biais de sélection d’Action et routage intergiciel (middleware) intergiciel (middleware) autres différentes avant de devenir une réponse envoyée au client.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Types de filtres

Chaque type de filtre est exécutée sur une autre étape du pipeline de filtre.

* [Filtres d’autorisation](#authorization-filters) exécutés en premier et sont utilisées pour déterminer si l’utilisateur actuel est autorisé pour la requête actuelle. Ils peuvent court-circuit le pipeline si une demande n’est pas autorisée. 

* [Filtres de ressources](#resource-filters) sont les premières à traiter une demande après l’autorisation.  Ils peuvent exécuter le code avant le reste du pipeline de filtre et une fois que le reste du pipeline est terminé. Ils ne sont utiles pour implémenter la mise en cache ou de court-circuit sinon le pipeline de filtre pour des raisons de performances. Dans la mesure où ils s’exécuter avant la liaison de modèle, ils ne sont pas utiles pour tout élément qui a besoin pour influencer la liaison de modèle.

* [Filtres d’action](#action-filters) peut exécuter du code juste avant et après l’appel d’une méthode d’action individuelle. Ils peuvent être utilisés pour manipuler les arguments transmis à une action et le résultat retourné à partir de l’action.

* [Filtres d’exception](#exception-filters) sont utilisées pour appliquer des stratégies globales pour les exceptions non gérées qui se produisent avant tout élément a été écrit dans le corps de réponse.

* [Filtres de résultat](#result-filters) peut exécuter du code juste avant et après l’exécution de résultats d’action individuelles. Ils s’exécutent uniquement lorsque la méthode d’action a été exécutée correctement et sont utiles pour la logique qui doit entourer l’affichage ou le module de formatage de l’exécution.

Le diagramme suivant illustre l’interagissent entre ces types de filtres dans le pipeline de filtre.

![La demande est traitée par le biais d’autorisation filtres, ressources, liaison de modèle, filtres d’Action, l’exécution d’Action et Conversion de résultat d’Action, les filtres d’Exception, filtres de résultat et l’exécution du résultat. Sur la sortie, la demande est traitée uniquement par les filtres de résultat et de ressource avant de devenir une réponse envoyée au client.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implémentation

Filtres prennent en charge les implémentations synchrones et asynchrones via des définitions d’interface différente. Choisissez la synchronisation ou async variant en fonction du type de tâche, que vous devez effectuer. 

Synchrones filtres qui peuvent s’exécuter à la fois du code avant et après leur phase de pipeline est défini sur*étape*en cours d’exécution et*étape*exécutée de méthodes. Par exemple, `OnActionExecuting` est appelé avant d’appeler la méthode d’action, et `OnActionExecuted` est appelée après le retour de la méthode d’action.

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?highlight=6,8,13)]

Les filtres asynchrones définissent un seul sur*étape*ExecutionAsync méthode. Cette méthode prend un *FilterType*délégué ExecutionDelegate qui exécute l’étape de pipeline du filtre. Par exemple, `ActionExecutionDelegate` appelle la méthode d’action et vous pouvez exécuter du code avant et après l’appel à elle.

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleAsyncActionFilter.cs?highlight=6,8-10,13)]

Vous pouvez implémenter des interfaces pour plusieurs étapes de filtre dans une classe unique. Par exemple, le [ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute) classe abstraite implémente à la fois `IActionFilter` et `IResultFilter`, ainsi que leurs équivalents asynchrones.

> [!NOTE]
> Implémentez **soit** synchrones ou la version asynchrone d’une interface de filtre, pas les deux. Le framework vérifie tout d’abord si le filtre implémente l’interface asynchrone, et si tel est le cas, il appelle qui. Si ce n’est pas le cas, il appelle les méthodes de l’interface synchrone. Si vous étiez à implémenter les interfaces sur une classe, seule la méthode async est appelée. Lors de l’utilisation des classes abstraites comme [ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute) vous devez remplacer uniquement les méthodes synchrones ou la méthode async pour chaque type de filtre.

### <a name="ifilterfactory"></a>IFilterFactory

L'objet `IFilterFactory` implémente l'objet `IFilter`. Par conséquent, un `IFilterFactory` instance peut être utilisée comme un `IFilter` instance n’importe où dans le pipeline de filtre. Lorsque le framework prépare appeler le filtre, il tente d’effectuer un cast en un `IFilterFactory`. Si cette conversion réussit, le `CreateInstance` méthode est appelée pour créer le `IFilter` instance qui sera appelé. Cela fournit une conception très flexible, étant donné que le pipeline de filtre précis n’a pas besoin d’être défini explicitement, lorsque l’application démarre.

Vous pouvez implémenter `IFilterFactory` sur vos propres implémentations d’attribut en tant qu’une autre approche pour la création de filtres :

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

### <a name="built-in-filter-attributes"></a>Attributs de filtre intégré

Le framework inclut les filtres intégrés basée sur les attributs que vous pouvez créer une sous-classe et personnaliser. Par exemple, le filtre de résultat suivant ajoute un en-tête à la réponse.

<a name="add-header-attribute"></a>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/AddHeaderAttribute.cs?highlight=5,16)]

Attributs autorise les filtres accepter des arguments, comme indiqué dans l’exemple ci-dessus. Vous serez ajoutez cet attribut à une méthode de contrôleur ou d’action et spécifiez le nom et la valeur de l’en-tête HTTP :

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

Le résultat de la `Index` action est illustrée ci-dessous, les en-têtes de réponse sont affichés dans la partie inférieure droite.

![Developer Tools de Microsoft Edge affichant les en-têtes de réponse, notamment l’auteur de Steve Smith@ardalis](filters/_static/add-header.png)

Plusieurs des interfaces de filtre ont des attributs correspondants qui peuvent être utilisés comme classes de base pour les implémentations personnalisées.

Attributs de filtre :

* `ActionFilterAttribute`
* `ExceptionFilterAttribute`
* `ResultFilterAttribute`
* `FormatFilterAttribute`
* `ServiceFilterAttribute`
* `TypeFilterAttribute`

`TypeFilterAttribute`et `ServiceFilterAttribute` sont expliquées [plus loin dans cet article](#dependency-injection).

## <a name="filter-scopes-and-order-of-execution"></a>Les étendues de filtre et ordre d’exécution

Un filtre peut être ajouté au pipeline à un des trois *étendues*. Vous pouvez ajouter un filtre à une méthode d’action particulier ou à une classe de contrôleur à l’aide d’un attribut. Ou vous pouvez enregistrer un filtre global (pour tous les contrôleurs et les actions) en l’ajoutant à la `MvcOptions.Filters` collection dans le `ConfigureServices` méthode dans la `Startup` classe :

[!code-csharp[Main](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

### <a name="default-order-of-execution"></a>Ordre par défaut de l’exécution

Lorsqu’il existe plusieurs filtres pour une étape spécifique du pipeline, la portée détermine l’ordre par défaut de l’exécution du filtre.  Filtres globaux entourent les filtres de classe, qui à son tour entourent les filtres de la méthode. Cela est parfois appelée l’imbrication « Poupée russe », car chaque augmentation dans l’étendue est autour de l’étendue précédente, comme un [d’imbrication poupée](https://wikipedia.org/wiki/Matryoshka_doll). En règle générale, vous obtenez le comportement de substitution souhaité sans avoir à déterminer explicitement le classement.

À la suite de cette imbrication, le *après* code de filtres s’exécute dans l’ordre inverse de la *avant* code. La séquence ressemble à ceci :

* Le *avant* code de filtres appliqués globalement
  * Le *avant* code de filtres appliqués à des contrôleurs
    * Le *avant* code des filtres appliqués aux méthodes d’action
    * Le *après* code des filtres appliqués aux méthodes d’action
  * Le *après* code de filtres appliqués à des contrôleurs
* Le *après* code de filtres appliqués globalement
  
Voici un exemple qui illustre l’ordre dans le filtre les méthodes sont appelées synchrones filtres d’Action.

| Séquence | Portée du filtre | Filter (méthode) |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | Contrôleur | `OnActionExecuting` |
| 3 | Méthode | `OnActionExecuting` |
| 4 | Méthode | `OnActionExecuted` |
| 5 | Contrôleur | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

Cette séquence montre que le filtre de la méthode est imbriqué dans le filtre de contrôleur et le filtre de contrôleur est imbriqué dans le filtre global. Pour placer une autre façon, si vous êtes à l’intérieur d’un filtre async du*étape*ExecutionAsync méthode, tous les filtres avec une portée plus étroite s’exécuter pendant que votre code est sur la pile.

> [!NOTE]
> Chaque contrôleur qui hérite de la `Controller` inclut de la classe de base `OnActionExecuting` et `OnActionExecuted` méthodes. Ces méthodes encapsulent les filtres qui s’exécutent pour une action donnée : `OnActionExecuting` est appelée avant que les filtres, et `OnActionExecuted` est appelée après tous les filtres.

### <a name="overriding-the-default-order"></a>Substitution de l’ordre par défaut

Vous pouvez remplacer la séquence par défaut de l’exécution en implémentant `IOrderedFilter`. Cette interface expose un `Order` propriété qui est prioritaire sur l’étendue afin de déterminer l’ordre d’exécution. Un filtre avec une faible `Order` valeur aura son *avant* code exécuté avant que d’un filtre avec une valeur plus élevée de `Order`. Un filtre avec une faible `Order` valeur aura son *après* le code exécuté par la suite d’un filtre avec un degré plus élevé `Order` valeur. Vous pouvez définir le `Order` propriété à l’aide d’un paramètre de constructeur :

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Si vous avez la même indiqués dans le précédent exemple mais le même jeu de filtres d’Action 3 le `Order` propriété du contrôleur et globaux filtres à 1 et 2, respectivement, l’ordre d’exécution est inversé.

| Séquence | Portée du filtre | Propriété `Order` | Filter (méthode) |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Méthode | 0 | `OnActionExecuting` |
| 2 | Contrôleur | 1  | `OnActionExecuting` |
| 3 | Global | 2  | `OnActionExecuting` |
| 4 | Global | 2  | `OnActionExecuted` |
| 5 | Contrôleur | 1  | `OnActionExecuted` |
| 6 | Méthode | 0  | `OnActionExecuted` |

Le `Order` propriété éclipsent mille paires d’étendue lors de la détermination de l’ordre dans lequel les filtres seront exécutent. Les filtres sont triés en premier par ordre, puis étendue permet de rompre les liens. Tous les filtres intégrés implémentent `IOrderedFilter` et définissez la valeur par défaut `Order` la valeur 0, la portée détermine l’ordre, sauf si vous définissez `Order` à une valeur différente de zéro.

## <a name="cancellation-and-short-circuiting"></a>L’annulation et court-circuit

Vous pouvez court-circuit le pipeline de filtre à tout moment en définissant le `Result` propriété sur le `context` paramètre fourni à la méthode de filtre. Par exemple, le filtre de ressource suivant empêche le reste du pipeline de l’exécution.

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?highlight=12,13,14,15)]

Dans le code suivant, à la fois le `ShortCircuitingResourceFilter` et `AddHeader` cible de filtre la `SomeResource` méthode d’action. Toutefois, étant donné que la `ShortCircuitingResourceFilter` s’exécute en premier (, car il s’agit d’un filtre de ressources et `AddHeader` est un filtre d’Action) et court-circuite le reste du pipeline, le `AddHeader` filtre ne s’exécute jamais pour le `SomeResource` action. Ce comportement serait identique si les deux filtres ont été appliquées au niveau de la méthode action, fourni le `ShortCircuitingResourceFilter` a exécuté la première (en raison de son type de filtre, ou explicite, utilisez des `Order` propriété, par exemple).

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Injection de dépendance

Filtres peuvent être ajoutés par type ou par instance. Si vous ajoutez une instance, cette instance sera utilisée pour chaque demande. Si vous ajoutez un type, il sera activé par type, ce qui signifie une instance sera créée pour chaque demande et toutes les dépendances de constructeur seront remplies par [injection de dépendance](../../fundamentals/dependency-injection.md) (DI). Ajout d’un filtre par type équivaut à `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`.

Les filtres qui sont implémentées en tant qu’attributs et ajoutés directement à des classes de contrôleur ou de méthodes d’action ne peut pas avoir de dépendances de constructeur fournis par [injection de dépendance](../../fundamentals/dependency-injection.md) (DI). Il s’agit, car les attributs doivent avoir leurs paramètres du constructeur fournis où elles sont appliquées. Il s’agit d’une limitation du mode de fonctionnement des attributs.

Si vos filtres ont des dépendances dont vous avez besoin pour accéder à partir de DI, il existe plusieurs approches pris en charge. Vous pouvez appliquer le filtre à une méthode de classe ou une action à l’aide d’une des opérations suivantes :

* `ServiceFilterAttribute`
* `TypeFilterAttribute`
* `IFilterFactory`implémenté sur votre attribut

> [!NOTE]
> Une dépendance, que vous souhaiterez peut-être obtenir à partir de DI est un enregistreur d’événements. Toutefois, évitez la création et l’utilisation de filtres uniquement à des fins de journalisation, étant donné que la [des fonctionnalités de journalisation de l’infrastructure intégrée](../../fundamentals/logging.md) offrent parfois ce dont vous avez besoin. Si vous souhaitez ajouter à vos filtres, il doit se concentrer sur les problèmes de domaine d’entreprise ou de comportement spécifique à votre filtre, plutôt que des actions de MVC ou d’autres événements de framework.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

A `ServiceFilter` récupère une instance du filtre DI. Vous ajoutez le filtre sur le conteneur dans `ConfigureServices`et à le référencer dans un `ServiceFilter` attribut

[!code-csharp[Main](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=11)]

[!code-csharp[Main](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

À l’aide de `ServiceFilter` sans enregistrer les résultats de type de filtre dans une exception :

```
System.InvalidOperationException: No service for type
'FiltersSample.Filters.AddHeaderFilterWithDI' has been registered.
```

`ServiceFilterAttribute`implémente `IFilterFactory`, qui expose une méthode unique pour la création d’un `IFilter` instance. Dans le cas de `ServiceFilterAttribute`, le `IFilterFactory` l’interface `CreateInstance` méthode est implémentée pour charger le type spécifié à partir du conteneur de services (DI).

### <a name="typefilterattribute"></a>TypeFilterAttribute

`TypeFilterAttribute`est très similaire à `ServiceFilterAttribute` (et implémente également `IFilterFactory`), mais son type n’est pas résolu directement à partir du conteneur DI. Au lieu de cela, il instancie le type à l’aide de `Microsoft.Extensions.DependencyInjection.ObjectFactory`.

En raison de cette différence, les types qui sont référencés à l’aide de la `TypeFilterAttribute` n’avez pas besoin être enregistrée avec le conteneur (mais toujours auront leurs dépendances remplies par le conteneur). En outre, `TypeFilterAttribute` peut éventuellement accepter des arguments de constructeur pour le type en question. L’exemple suivant montre comment passer des arguments à un type à l’aide de `TypeFilterAttribute`:

[!code-csharp[Main](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

Si vous avez un filtre qui ne nécessite pas d’arguments, mais qui a des dépendances de constructeur qui doivent être remplies par DI, vous pouvez utiliser votre propre attribut nommé sur les classes et méthodes au lieu de `[TypeFilter(typeof(FilterType))]`). Le filtre suivant montre comment cela peut être implémenté :

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Ce filtre peut être appliqué aux classes ou méthodes à l’aide de la `[SampleActionFilter]` syntaxe, au lieu de devoir utiliser `[TypeFilter]` ou `[ServiceFilter]`.

## <a name="authorization-filters"></a>Filtres d’autorisation

*Filtres d’autorisation* contrôler l’accès aux méthodes d’action et le premier filtre à exécuter dans le pipeline de filtre. Ils ont uniquement un avant de méthode, contrairement à la plupart des filtres qui prennent en charge avant et après les méthodes. Vous devez uniquement écrire un filtre d’autorisation personnalisée si vous écrivez votre propre infrastructure d’autorisation. Préférez la configuration de vos stratégies d’autorisation ou de l’écriture d’une stratégie d’autorisation personnalisée sur l’écriture d’un filtre personnalisé. L’implémentation de filtre intégré est simplement chargée d’appeler le système d’autorisation.

Notez que vous ne devez pas lever d’exceptions dans les filtres d’autorisation, étant donné que rien ne gère l’exception (filtres d’exception ne sont pas les gérer). Au lieu de cela, émettre une demande d’accès ou d’autre moyen.

En savoir plus sur [autorisation](../../security/authorization/index.md).

## <a name="resource-filters"></a>Filtres de ressources

*Filtres de ressources* implémentent la `IResourceFilter` ou `IAsyncResourceFilter` interface et leur exécution encapsule la majeure partie du pipeline de filtre. (Uniquement [filtres d’autorisation](#authorization-filters) exécuter avant eux.) Filtres de ressources sont particulièrement utiles si vous avez besoin pour la plupart du travail effectue une demande de court-circuit. Par exemple, un filtre de mise en cache peut éviter le reste du pipeline si la réponse est déjà dans le cache.

Le [court filtre ressource court-circuit](#short-circuiting-resource-filter) présentée précédemment est un exemple d’un filtre de ressource. Un autre exemple est [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs), ce qui empêche la liaison de modèle à partir de l’accès aux données de formulaire. Il est utile pour les cas où vous savez que vous allez recevoir le téléchargement de fichiers volumineux et pour empêcher que le formulaire qui est lue en mémoire.

## <a name="action-filters"></a>Filtres d’action

*Filtres d’action* implémentent la `IActionFilter` ou `IAsyncActionFilter` interface et leur exécution entoure l’exécution de méthodes d’action.

Voici un exemple de filtre d’action :

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet_ActionFilter)]

Le [ActionExecutingContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutingcontext) fournit les propriétés suivantes :

* `ActionArguments`-vous permet de manipuler les entrées de l’action.
* `Controller`-vous permet de manipuler l’instance du contrôleur. 
* `Result`-la définition de cette court-circuite l’exécution de la méthode d’action et les filtres d’action suivants. Lever une exception également empêche l’exécution de la méthode d’action et les autres filtres, mais est traité comme une erreur au lieu d’un résultat réussi.

Le [ActionExecutedContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutedcontext) fournit `Controller` et `Result` ainsi que les propriétés suivantes :

* `Canceled`-la valeur est True si l’exécution de l’action a été court-circuité par un autre filtre.
* `Exception`-sera non null si l’action ou un filtre d’action suivants a levé une exception. Définition de cette propriété sur null efficacement 'handles' une exception, et `Result` sera exécutée comme si elle a été retournée à partir de la méthode d’action normalement.

Pour un `IAsyncActionFilter`, un appel à la `ActionExecutionDelegate` exécute tous les filtres d’action suivants et la méthode d’action, en retournant un `ActionExecutedContext`. Pour de court-circuit, assignez `ActionExecutingContext.Result` à un résultat de l’instance et n’appelez pas la `ActionExecutionDelegate`.

Le framework fournit un résumé `ActionFilterAttribute` que vous pouvez créer une sous-classe. 

Vous pouvez utiliser un filtre d’action automatiquement valider l’état du modèle et retourner des erreurs si l’état n’est pas valide :

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/ValidateModelAttribute.cs)]

Le `OnActionExecuted` s’exécute la méthode après la méthode d’action et peut voir et de manipuler les résultats de l’action via le `ActionExecutedContext.Result` propriété. `ActionExecutedContext.Canceled`définira sur true si l’exécution de l’action a été court-circuité par un autre filtre. `ActionExecutedContext.Exception`est fixé à une valeur non null si l’action ou un filtre d’action suivants a levé une exception. Paramètre `ActionExecutedContext.Exception` NULL efficacement une exception, 'handles' et `ActionExectedContext.Result` est exécutée comme si elle a été retournée à partir de la méthode d’action normalement.

## <a name="exception-filters"></a>Filtres d’exception

*Filtres d’exception* implémentent la `IExceptionFilter` ou `IAsyncExceptionFilter` interface. Ils peuvent être utilisés pour implémenter les erreurs courantes la gestion des stratégies pour une application. 

L’exemple de filtre d’exception suivant utilise une erreur développeur personnalisé pour afficher des détails sur les exceptions qui se produisent lorsque l’application est en cours de développement :

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=1,14)]

Filtres d’exception n’ont pas de deux événements (avant ou après) : ils implémentent uniquement `OnException` (ou `OnExceptionAsync`). 

Gérer les filtres d’exception des exceptions non gérées qui se produisent dans la création du contrôleur, [liaison de modèle](../models/model-binding.md), filtres d’action ou des méthodes d’action. Ils ne sont pas intercepter des exceptions qui se produisent dans les filtres de ressources, les filtres de résultat ou l’exécution du résultat de MVC.

Pour gérer une exception, définissez la `ExceptionContext.ExceptionHandled` la propriété ou écrire une réponse. Cela arrête la propagation de l’exception. Notez qu’un filtre d’Exception ne peut pas activer une exception dans un « succès ». Un filtre d’Action peut le faire.

> [!NOTE]
> Dans la version 1.1 d’ASP.NET, la réponse n’est pas envoyée si vous définissez `ExceptionHandled` true **et** écrire une réponse. Dans ce scénario, ASP.NET Core 1.0 envoie la réponse et ASP.NET Core 1.1.2 retournera le comportement de 1.0. Pour plus d’informations, consultez [émettre #5594](https://github.com/aspnet/Mvc/issues/5594) dans le référentiel GitHub. 

Filtres d’exception sont adaptés pour intercepter les exceptions qui se produisent dans les actions de MVC, mais ils ne sont pas aussi flexibles que l’intergiciel (middleware) de gestion des erreurs. Préférez l’intergiciel (middleware) pour le cas général et utiliser des filtres uniquement où vous avez besoin pour gérer les erreurs *différemment* en fonction de l’action MVC a été choisie. Par exemple, votre application peut comporter des méthodes d’action pour les deux points de terminaison d’API et de vues/HTML. Les points de terminaison API peut retourner des informations d’erreur au format JSON, tandis que les actions basée sur une vue peut retourner une page d’erreur au format HTML.

Le framework fournit un résumé `ExceptionFilterAttribute` que vous pouvez créer une sous-classe. 

## <a name="result-filters"></a>Filtres de résultat

*Filtres de résultat* implémentent le le `IResultFilter` ou `IAsyncResultFilter` interface et leur exécution entoure l’exécution de résultats d’action. 

Voici un exemple de filtre de résultat qui ajoute un en-tête HTTP.

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Le type de résultat en cours d’exécution dépend de l’action en question. Une action MVC retournant une vue inclut tous les razor de traitement dans le cadre de la `ViewResult` en cours d’exécution. Une méthode d’API peut effectuer une sérialisation dans le cadre de l’exécution du résultat. En savoir plus sur [résultats d’action](actions.md)

Filtres de résultat sont exécutées uniquement pour les résultats réussis - lorsque l’action ou les filtres d’action produisent un résultat d’action. Filtres de résultat ne sont pas exécutées lorsque les filtres d’exception gérer une exception.

Le `OnResultExecuting` méthode pouvez court-circuit l’exécution du résultat d’action et les filtres de résultats suivants en définissant `ResultExecutingContext.Cancel` sur true. Vous devez généralement écrire à l’objet de réponse lors de court-circuit pour éviter de générer une réponse vide. Lever une exception sera également empêcher l’exécution du résultat d’action et des autres filtres, mais sera traité comme un échec au lieu d’un résultat réussi.

Lorsque le `OnResultExecuted` s’exécute (méthode), la réponse a probablement été envoyé au client et ne peut pas être modifiée plus (sauf si une exception a été levée). `ResultExecutedContext.Canceled`définira sur true si l’exécution de résultat d’action a été court-circuité par un autre filtre.

`ResultExecutedContext.Exception`est fixé à une valeur non null si le résultat d’action ou un filtre de résultats suivants a levé une exception. Paramètre `Exception` à null 'handles' une exception efficacement et empêche l’exception qui est à nouveau levée par MVC plus loin dans le pipeline. Lorsque vous traitez une exception dans un filtre de résultat, vous n’êtes peut-être pas en mesure d’écrire des données dans la réponse. Si le résultat d’action génère en cours via son exécution, et les en-têtes ont déjà été vidées sur le client, il n’existe aucun mécanisme fiable pour envoyer un code d’échec.

Pour un `IAsyncResultFilter` un appel à `await next()` sur la `ResultExecutionDelegate` exécute tous les filtres de résultats suivants et le résultat d’action. Pour de court-circuit, définissez `ResultExecutingContext.Cancel` à true et n’appelez pas la `ResultExectionDelegate`.

Le framework fournit un résumé `ResultFilterAttribute` que vous pouvez créer une sous-classe. Le [AddHeaderAttribute](#add-header-attribute) classe ci-dessus est un exemple d’un attribut de filtre de résultat.

## <a name="using-middleware-in-the-filter-pipeline"></a>À l’aide d’intergiciel (middleware) dans le pipeline de filtre

Filtres de ressources fonctionnent comme [intergiciel (middleware)](../../fundamentals/middleware.md) dans la mesure où ils entourent l’exécution de tous les éléments qui est fourni plus loin dans le pipeline. Diffèrent des filtres à partir de l’intergiciel (middleware) dans la mesure où ils font partie de MVC, ce qui signifie qu’ils ont accès au contexte MVC et constructions.

Dans ASP.NET Core 1.1, vous pouvez utiliser l’intergiciel (middleware) dans le pipeline de filtre. Vous pouvez souhaiter faire si vous avez un composant d’intergiciel (middleware) qui doit accéder aux données d’itinéraire MVC ou qui doit s’exécuter que pour certains contrôleurs ou les actions.

Pour utiliser l’intergiciel (middleware) en tant que filtre, créez un type avec un `Configure` méthode qui spécifie l’intergiciel (middleware) que vous souhaitez insérer dans le pipeline de filtre. Voici un exemple qui utilise l’intergiciel de localisation pour établir la culture actuelle pour une demande :

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

Vous pouvez ensuite utiliser le `MiddlewareFilterAttribute` pour exécuter l’intergiciel (middleware) pour un contrôleur sélectionné ou l’action ou globalement :

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Filtres d’intergiciel (middleware) s’exécutent à la même étape du pipeline de filtre en tant que ressource filtre, avant la liaison de modèle et après le reste du pipeline.

## <a name="next-actions"></a>Actions suivantes

À faire des essais avec des filtres [télécharger, tester et modifier l’exemple](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

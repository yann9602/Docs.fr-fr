---
title: "Injection de dépendances dans ASP.NET Core"
author: ardalis
description: "Découvrez comment ASP.NET Core implémente injection de dépendance et comment l’utiliser."
keywords: "ASP.NET Core, injection de dépendance, di"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: fccd69be-7ad1-47fb-b203-b3633b6b9a9b
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/dependency-injection
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5dfebeddb3a9394fd1faf798e029a9312201089
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-dependency-injection-in-aspnet-core"></a>Introduction à l’Injection de dépendances dans ASP.NET Core

<a name=fundamentals-dependency-injection></a>

Par [Steve Smith](https://ardalis.com/) et [Scott Addie](https://scottaddie.com)

ASP.NET Core est conçu de toutes pièces pour prendre en charge et de tirer parti de l’injection de dépendances. Les applications ASP.NET Core peuvent tirer parti des services d’infrastructure intégrée par comportant injectées dans les méthodes de la classe de démarrage, et les services d’application peuvent être configurés pour l’injection d’également. Le conteneur de services par défaut fourni par ASP.NET Core fournit une fonctionnalité minimale définie et n’est pas destinée à remplacer les autres conteneurs.

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/sample)

## <a name="what-is-dependency-injection"></a>Nouveautés d’Injection de dépendance ?

Injection de dépendance (DI) est une technique de couplage lâche entre les objets et leurs collaborateurs et les dépendances. Au lieu d’instancier directement des collaborateurs, ou à l’aide de références statiques, les objets de qu'une classe a besoin pour effectuer ses actions sont fournies à la classe d’une certaine manière. En règle générale, les classes déclarer leurs dépendances via leur constructeur, ce qui leur permet de suivre les [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/). Cette approche est appelée « injection de constructeur ».

Lorsque les classes conçues avec DI, n’oubliez pas, ils sont plus faiblement couplés, car ils n’ont pas de dépendances directes, codé en dur sur leurs collaborateurs. Cela suit le [principe d’Inversion de dépendance](http://deviq.com/dependency-inversion-principle/), ce qui indique que *« modules de niveau élevés ne doivent pas dépendre des modules de niveau faibles ; les deux doivent dépendre des abstractions ».* Au lieu de référencer des implémentations spécifiques, classes demande abstractions (généralement `interfaces`) qui sont fourni lors de la construction de classe. Extraction des dépendances dans les interfaces et en fournissant des implémentations de ces interfaces en tant que paramètres sont également un exemple de la [modèle de conception de stratégie](http://deviq.com/strategy-design-pattern/).

Lorsqu’un système est conçu pour utiliser DI, avec de nombreuses classes demande leurs dépendances via son constructeur (ou propriétés), il est utile de disposer d’une classe dédiée à la création de ces classes avec leurs dépendances associées. Ces classes sont appelés *conteneurs*, ou plus spécifiquement, [Inversion de contrôle (IoC)](http://deviq.com/inversion-of-control/) conteneurs ou conteneurs d’Injection de dépendance (DI). Un conteneur est essentiellement une fabrique qui est chargée de fournir des instances de types qui sont demandées à partir de celui-ci. Si un type donné a déclaré qu’il possède des dépendances et le conteneur a été configuré pour fournir les types de dépendances, il crée les dépendances dans le cadre de la création de l’instance demandée. De cette façon, les graphiques de dépendance complexe peuvent être fournis aux classes sans nécessiter de n’importe quel construction d’objet de codé en dur. En plus de la création d’objets avec leurs dépendances, les conteneurs gèrent généralement durée de vie des objets dans l’application.

ASP.NET Core inclut un simple conteneur intégré (représenté par la `IServiceProvider` interface) qui prend en charge l’injection de constructeur par défaut, et ASP.NET rend certains services disponibles via DI. ASP. Conteneur de NET fait référence aux types qu’il gère en tant que *services*. Dans le reste de cet article, *services* fait référence aux types qui sont gérés par le conteneur d’inversion de contrôle d’ASP.NET Core. Vous configurez les services du conteneur intégré dans le `ConfigureServices` méthode dans votre application `Startup` classe.

> [!NOTE]
> Martin Fowler a écrit un article complet sur [conteneurs d’Inversion de contrôle et le modèle d’Injection de dépendance](https://www.martinfowler.com/articles/injection.html). Microsoft Patterns and Practices a également une excellente description de [Injection de dépendance](https://msdn.microsoft.com/library/hh323705.aspx).

> [!NOTE]
> Cet article couvre l’Injection de dépendance telle qu’elle s’applique à toutes les applications ASP.NET. Injection de dépendances au sein des contrôleurs MVC est couvert dans [Injection de dépendance et des contrôleurs de](../mvc/controllers/dependency-injection.md).

### <a name="constructor-injection-behavior"></a>Comportement de l’Injection de constructeur

Injection de constructeur requiert que le constructeur en question *public*. Sinon, votre application génère une `InvalidOperationException`:

> Un constructeur approprié pour le type 'YourType' n’a pas pu être localisé. Vérifiez le type est concrète et les services sont inscrits pour tous les paramètres d’un constructeur public.


Injection de constructeur requiert ce qu’un seul constructeur applicable existe. Surcharges de constructeur sont pris en charge, mais qu’une surcharge peut exister dont les arguments peuvent tous être satisfaites par injection de dépendances. Si plusieurs existe, votre application lève une `InvalidOperationException`:

> Plusieurs constructeurs accepter tous les types d’argument donné ont été trouvées dans le type 'YourType'. Doit être un constructeur applicable.

Les constructeurs peuvent accepter des arguments qui ne sont pas fournies par injection de dépendances, mais il doivent prendre en charge les valeurs par défaut. Exemple :

```csharp
// throws InvalidOperationException: Unable to resolve service for type 'System.String'...
public CharactersController(ICharacterRepository characterRepository, string title)
{
    _characterRepository = characterRepository;
    _title = title;
}

// runs without error
public CharactersController(ICharacterRepository characterRepository, string title = "Characters")
{
    _characterRepository = characterRepository;
    _title = title;
}
```

## <a name="using-framework-provided-services"></a>À l’aide des Services fournis par le Framework

Le `ConfigureServices` méthode dans la `Startup` classe est chargé de définir les services de l’application utilise, notamment les fonctionnalités de plateforme comme Entity Framework Core et ASP.NET MVC de base. Au départ, la `IServiceCollection` fourni à `ConfigureServices` a les services suivants définis (en fonction de [la configuration de l’hôte](xref:fundamentals/hosting)) :

| Type de service | Durée de vie |
| ----- | ------- |
| [Microsoft.AspNetCore.Hosting.IHostingEnvironment](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) | Singleton |
| [Microsoft.Extensions.Logging.ILoggerFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.iloggerfactory) | Singleton |
| [Microsoft.Extensions.Logging.ILogger&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) | Singleton |
| [Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | Temporaire |
| [Microsoft.AspNetCore.Http.IHttpContextFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.ihttpcontextfactory) | Temporaire |
| [Microsoft.Extensions.Options.IOptions&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) | Singleton |
| [System.Diagnostics.DiagnosticSource](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.diagnosticsource) | Singleton |
| [System.Diagnostics.DiagnosticListener](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.diagnosticlistener) | Singleton |
| [Microsoft.AspNetCore.Hosting.IStartupFilter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.istartupfilter) | Temporaire |
| [Microsoft.Extensions.ObjectPool.ObjectPoolProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.objectpool.objectpoolprovider) | Singleton |
| [Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.iconfigureoptions-1) | Temporaire |
| [Microsoft.AspNetCore.Hosting.Server.IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) | Singleton |
| [Microsoft.AspNetCore.Hosting.IStartup](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.istartup) | Singleton |
| [Microsoft.AspNetCore.Hosting.IApplicationLifetime](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | Singleton |

Voici un exemple montrant comment ajouter des services supplémentaires pour le conteneur à l’aide d’un nombre de méthodes d’extension comme `AddDbContext`, `AddIdentity`, et `AddMvc`.

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=5-6,8-10,12&range=39-56)]

Intergiciel (middleware) fournies par ASP.NET, tels que MVC, les fonctionnalités de respecter une convention d’à l’aide d’un ajout unique*ServiceName* méthode d’extension pour inscrire tous les services requis par cette fonctionnalité.

>[!TIP]
> Vous pouvez demander à certains services fournis par le framework dans `Startup` méthodes via leurs listes de paramètres - voir [démarrage de l’Application](startup.md) pour plus d’informations.

## <a name="registering-your-own-services"></a>L’inscription de vos propres Services

Vous pouvez enregistrer vos propres services d’application comme suit. Le premier type générique représente le type (en général une interface) qui vous est demandé à partir du conteneur. Le deuxième type générique représente le type concret qui sera instancié par le conteneur et utilisé pour répondre à ces demandes.

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?range=53-54)]

> [!NOTE]
> Chaque `services.Add<ServiceName>` méthode d’extension ajoute (et potentiellement configure) services. Par exemple, `services.AddMvc()` ajoute les services MVC requiert. Il est recommandé de suivre cette convention, placer des méthodes d’extension dans le `Microsoft.Extensions.DependencyInjection` espace de noms pour encapsuler des groupes d’enregistrements de service.

Le `AddTransient` méthode est utilisée pour mapper les types abstraits aux services concrets qui sont instanciés séparément pour chaque objet qui en a besoin. Il s’agit du service *durée de vie*, et les options de durée de vie supplémentaires sont décrites ci-dessous. Il est important de choisir une durée de vie appropriée pour chacun des services que vous inscrivez. Une nouvelle instance du service doit être fournie pour chaque classe qui le demande ? Une instance doit être utilisée dans une requête web donné ? Ou, si une seule instance doit être utilisée pour la durée de vie de l’application ?

Dans l’exemple de cet article, il existe un contrôleur simple qui affiche les noms de caractères, appelées `CharactersController`. Son `Index` la méthode affiche la liste actuelle des caractères qui ont été stockés dans l’application et initialise la collection avec un certain nombre de caractères si aucune n’existe. Notez que bien que cette application utilise Entity Framework Core et `ApplicationDbContext` classe de leur persistance, aucun des qui est visible dans le contrôleur. Au lieu de cela, le mécanisme d’accès de données spécifique a été conçu comme derrière une interface, `ICharacterRepository`, qui suit le [modèle de référentiel](http://deviq.com/repository-pattern/). Une instance de `ICharacterRepository` est demandée via le constructeur et attribué à un champ privé, ce qui est ensuite utilisé pour accéder aux caractères que nécessaire.

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Controllers/CharactersController.cs?highlight=3,5,6,7,8,14,21-27&range=8-36)]

Le `ICharacterRepository` définit les deux méthodes, le contrôleur doit fonctionner avec `Character` instances.

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Interfaces/ICharacterRepository.cs?highlight=8,9)]

Cette interface est ensuite implémentée par un type concret, `CharacterRepository`, qui est utilisé lors de l’exécution.

> [!NOTE]
> La méthode DI est utilisée avec la `CharacterRepository` classe est un modèle général que vous pouvez suivre pour tous les services de votre application, pas seulement dans « référentiels » ou les classes d’accès aux données.

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Models/CharacterRepository.cs?highlight=9,11,12,13,14)]

Notez que `CharacterRepository` demandes un `ApplicationDbContext` dans son constructeur. Il n’est pas rare pour l’injection de dépendance à utiliser de manière chaînée comme celui-ci, avec chaque demande à son tour de ses propres dépendances de dépendance requises. Le conteneur est responsable de la résolution de toutes les dépendances dans le graphique et en retournant le service entièrement résolu.

> [!NOTE]
> Création de l’objet demandé et tous les objets qu’il nécessite et tous les objets que ceux nécessitent, est parfois appelé un *graphique d’objets*. De même, l’ensemble de dépendances qui doivent être résolus est généralement appelé une *arborescence des dépendances* ou *graphique de dépendance*.

Dans ce cas, les deux `ICharacterRepository` et à son tour `ApplicationDbContext` doit être inscrite auprès du conteneur services dans `ConfigureServices` dans `Startup`. `ApplicationDbContext`est configuré avec l’appel à la méthode d’extension `AddDbContext<T>`. Le code suivant illustre l’inscription de le `CharacterRepository` type.

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Startup.cs?highlight=3-5,11&range=16-32)]

Contextes d’Entity Framework doivent être ajoutés pour le conteneur de services à l’aide de la `Scoped` durée de vie. Cela est prise en charge automatiquement si vous utilisez les méthodes d’assistance comme indiqué ci-dessus. Les dépôts qui utilisera Entity Framework doivent utiliser la même durée de vie.

>[!WARNING]
> Risque de vous méfier du principal résout un `Scoped` service depuis un singleton. Il est probable dans ce cas que le service aura état incorrect lors du traitement des demandes suivantes.

Les services qui ont des dépendances doivent les enregistrer dans le conteneur. Si le constructeur d’un service nécessite un type primitif, comme un `string`, cela peut être ajoutée à l’aide de la [des options de modèle et configuration](configuration.md).

## <a name="service-lifetimes-and-registration-options"></a>Durée de vie du service et les Options d’enregistrement

Les services ASP.NET peuvent être configurés avec les durées de vie suivantes :

**Temporaire**

Les services de durée de vie temporaires sont créés chaque fois qu’ils sont demandés. Cette durée de vie convient le mieux pour les services légers, sans état.

**Une étendue**

Les services de durée de vie étendue sont créés une fois par demande.

**Singleton**

Services de durée de vie singleton sont créés à la première fois qu’ils sont demandés (ou lorsque `ConfigureServices` est exécuté si vous spécifiez une instance il) et ensuite toutes les requêtes suivantes utiliseront la même instance. Si votre application requiert un comportement singleton, ce qui permet le conteneur des services gérer la durée de vie du service est recommandé au lieu d’implémenter le modèle de conception singleton et gestion de durée de vie de votre objet dans la classe.

Les services peuvent être inscrits avec le conteneur de plusieurs façons. Nous avons déjà vu comment inscrire une implémentation de service avec un type donné en spécifiant le type concret à utiliser. En outre, une fabrique peut être spécifiée, qui sera ensuite être utilisé pour créer l’instance à la demande. La troisième méthode consiste à spécifier directement l’instance du type à utiliser, dans laquelle le conteneur de cas ne tentera jamais créer une instance (ni s’il dispose de l’instance).

Pour illustrer la différence entre ces options d’enregistrement et de la durée de vie, considérez une interface simple qui représente une ou plusieurs tâches en tant qu’un *opération* avec un identificateur unique, `OperationId`. Selon la façon dont nous configurons la durée de vie pour ce service, le conteneur fournit les identiques ou différentes instances du service à la classe de demande. Pour indiquer clairement à quelle durée de vie est demandée, nous allons créer un type par l’option de durée de vie :

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Interfaces/IOperation.cs?highlight=5-8)]

Nous implémentent ces interfaces à l’aide d’une seule classe, `Operation`, qui accepte un `Guid` dans son constructeur, ou utilise une nouvelle `Guid` si aucun n’est fourni.

Ensuite, dans `ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommé :

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Startup.cs?range=26-32)]

Notez que la `IOperationSingletonInstance` service utilise une instance spécifique avec un ID connu de `Guid.Empty` pour qu’il puisse clair lorsque ce type est en cours d’utilisation (son Guid sera zéros). Nous avons également inscrit un `OperationService` qui dépend de chacune des autres `Operation` types, de sorte qu’il soit clair dans une demande si ce service est mise en route de la même instance que le contrôleur, ou un autre, pour chaque type d’opération. Ce service n’est exposer ses dépendances en tant que propriétés, afin qu’ils peuvent être affichés dans la vue.

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Services/OperationService.cs)]

Pour illustrer les durées de vie d’objet dans et entre les demandes individuelles distinctes à l’application, l’exemple inclut un `OperationsController` que les demandes chaque type de `IOperation` type comme `OperationService`. Le `Index` action affiche tous les du contrôleur et du service `OperationId` valeurs.

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Controllers/OperationsController.cs)]

Maintenant deux demandes distinctes sont apportées à cette action de contrôleur :

![La vue des opérations de l’application web exemple d’Injection de dépendance en cours d’exécution dans Microsoft Edge présentant les valeurs d’ID d’opération (GUID) pour temporaire, inclus dans l’étendue, Singleton et contrôleur de l’Instance et fonctionnement des opérations de Service pour la première demande.](dependency-injection/_static/lifetimes_request1.png)

![Les opérations afficher indiquant les valeurs de l’ID d’opération pour une deuxième demande.](dependency-injection/_static/lifetimes_request2.png)

Observez parmi les `OperationId` valeurs différentes dans une demande et entre les demandes.

* *Transitoire* objets sont toujours différents ; une nouvelle instance est fournie pour chaque contrôleur et tous les services.

* *Portée* objets sont les mêmes au sein d’une demande, mais diffère entre les différentes demandes

* *Singleton* objets sont les mêmes pour tous les objets et toutes les demandes (quel que soit la fourniture d’une instance dans `ConfigureServices`)

## <a name="request-services"></a>Services de requêtes

Les services disponibles dans ASP.NET demande à partir de `HttpContext` sont exposées via la `RequestServices` collection.

![HttpContext demande Services Intellisense contextuel boîte de dialogue indiquant que la demande de Services Obtient ou définit le IServiceProvider qui fournit l’accès au conteneur de service de la demande.](dependency-injection/_static/request-services.png)

Les Services de requêtes représentent les services de configuration et de la demande dans le cadre de votre application. Lorsque vos objets de spécifient les dépendances, ceux-ci sont satisfaites par les types trouvés dans `RequestServices`, et non `ApplicationServices`.

En règle générale, vous ne devez pas utiliser ces propriétés directement, préférant à la place demander les types de vos classes que vous avez besoin via votre constructeur de classe et vous permettant du framework injecter ces dépendances. Cela génère des classes qui sont plus faciles à tester (voir [test](../testing/index.md)) et sont plus faiblement couplées.

> [!NOTE]
> Préférez demandant des dépendances en tant que paramètres du constructeur à l’accès à la `RequestServices` collection.

## <a name="designing-your-services-for-dependency-injection"></a>Conception de vos Services pour l’Injection de dépendance

Vous devez concevoir vos services à l’injection de dépendances permet d’obtenir leurs collaborateurs. Cela signifie que ce qui évite l’utilisation d’appels de méthode statique avec état (ce qui entraîne une odeur de code appelée [adhésives statique](http://deviq.com/static-cling/)) et l’instanciation directe de classes dépendantes dans vos services. Il peut être utile de se souvenir de l’expression suivante : [New est de type Glue](https://ardalis.com/new-is-glue), lorsque vous choisissez s’il faut pour instancier un type ou à la demande via l’injection de dépendances. En suivant le [solide principes d’objet de conception orientée](http://deviq.com/solid/), vos classes seront naturellement tendent à être petit et bien factorisée facilement testées.

Que se passe-t-il si vous trouvez que vos classes ont tendance à être moyen trop de dépendances est injectés ? Il s’agit généralement d’un signe que votre classe essaie de faire trop et qu’il est probablement violent les stratégies de restriction logicielle - le [principe de responsabilité unique](http://deviq.com/single-responsibility-principle/). Voir si vous pouvez refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe. N’oubliez pas que votre `Controller` classes doivent se concentrer sur les problèmes de l’interface utilisateur, afin de l’entreprise les données et les règles d’accès détails d’implémentation doivent être conservés dans les classes appropriées à ces [diviser les problèmes](http://deviq.com/separation-of-concerns/).

En ce qui concerne l’accès aux données en particulier, vous pouvez injecter la `DbContext` dans vos contrôleurs de (en supposant que vous avez ajouté EF au conteneur services dans `ConfigureServices`). Certains développeurs préfèrent utiliser une interface de référentiel pour la base de données plutôt que d’injecter la `DbContext` directement. À l’aide d’une interface pour encapsuler les données logique d’accès dans un seul emplacement peut réduire le nombre d’entrées vous devrez change lorsque votre base de données est modifiée.

### <a name="disposing-of-services"></a>Suppression des services

Le conteneur appelle `Dispose` pour `IDisposable` les types qu’il crée. Toutefois, si vous ajoutez une instance au conteneur vous-même, il ne sera pas supprimée.

Exemple :

```csharp
// Services implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // container will create the instance(s) of these types and will dispose them
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();

    // container did not create instance so it will NOT dispose it
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

> [!NOTE]
> Dans la version 1.0, le conteneur appelé dispose sur *tous les* `IDisposable` objets, y compris ceux qu’il n’a pas créé.

## <a name="replacing-the-default-services-container"></a>En remplaçant le conteneur de services par défaut

Le conteneur de services intégrés est destiné à répondre à des besoins de base de l’infrastructure et la plupart des applications consommateur construites dessus. Toutefois, les développeurs peuvent remplacer le conteneur intégré avec leur conteneur par défaut. Le `ConfigureServices` méthode retourne généralement `void`, mais si sa signature est modifié afin de retourner `IServiceProvider`, un autre conteneur peut être configuré et retourné. Il existe de nombreux conteneurs d’inversion de contrôle pour .NET. Dans cet exemple, le [Autofac](https://autofac.org/) package est utilisé.

Tout d’abord, installez les packages de conteneur approprié :

* `Autofac`
* `Autofac.Extensions.DependencyInjection`

Ensuite, configurez le conteneur dans `ConfigureServices` et renvoyer un `IServiceProvider`:

```csharp
public IServiceProvider ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    // Add other framework services

    // Add Autofac
    var containerBuilder = new ContainerBuilder();
    containerBuilder.RegisterModule<DefaultModule>();
    containerBuilder.Populate(services);
    var container = containerBuilder.Build();
    return new AutofacServiceProvider(container);
}
```

> [!NOTE]
> Lorsque vous utilisez un conteneur de DI tiers, vous devez modifier `ConfigureServices` afin qu’elle retourne `IServiceProvider` au lieu de `void`.

Enfin, normalement dans la configuration Autofac `DefaultModule`:

```csharp
public class DefaultModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
    }
}
```

Lors de l’exécution, Autofac servira pour résoudre les types et d’injecter des dépendances. [En savoir plus sur l’utilisation de Autofac et ASP.NET Core](http://docs.autofac.org/en/latest/integration/aspnetcore.html).

### <a name="thread-safety"></a>Sécurité des threads

Services de singleton doivent être thread-safe. Si un service singleton a une dépendance sur un service temporaire, le service temporaire deviez également être thread-safe en fonction de son utilisation par le singleton.

## <a name="recommendations"></a>Recommandations

Lorsque vous travaillez avec l’injection de dépendances, gardez à l’esprit les recommandations suivantes :

* DI est pour les objets qui ont des dépendances complexes. Contrôleurs, des services, des adaptateurs et des référentiels sont des exemples d’objets qui peuvent être ajoutés à DI.

* Évitez de stocker des données et configuration directement dans DI. Par exemple, panier d’achat d’un utilisateur ne doit pas généralement ajouté au conteneur de services. Configuration doit utiliser le [Options modèle](configuration.md#options-config-objects). De même, évitez d’objets « détenteur de données » qui n’existent que pour autoriser l’accès à un autre objet. Il est préférable de demander l’élément réel nécessaire via DI, si possible.

* Évitez l’accès aux services statiques.

* Évitez d’emplacement du service dans votre code d’application.

* Évitez l’accès statique à `HttpContext`.

> [!NOTE]
> Comme tous les jeux de recommandations, vous pouvez rencontrer des situations où en ignorant un est requise. Nous avons trouvé des exceptions être rares--cas principalement très spéciaux au sein de l’infrastructure elle-même.

N’oubliez pas, l’injection de dépendance est un *autre* pour les modèles d’accès objet static/global. Vous ne pourrez pas bénéficier des avantages du DI si vous la combiner avec l’accès aux objets statiques.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Démarrage d’une application](startup.md)

* [Test](../testing/index.md)

* [L’écriture de Code propre ASP.NET Core avec l’Injection de dépendances (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)

* [Conception de l’Application gérée par le conteneur, prélude : Où vous ne le conteneur appartient ?](https://blogs.msdn.microsoft.com/nblumhardt/2008/12/26/container-managed-application-design-prelude-where-does-the-container-belong/)

* [Principe des dépendances explicites](http://deviq.com/explicit-dependencies-principle/)

* [Inversion des conteneurs de contrôle et le modèle d’Injection de dépendance](https://www.martinfowler.com/articles/injection.html) (Fowler)

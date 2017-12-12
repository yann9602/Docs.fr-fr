---
title: "Modèle d’options dans ASP.NET Core"
author: guardrex
description: "Découvrez comment utiliser le modèle d’options pour représenter les groupes de paramètres associés dans les applications ASP.NET Core."
ms.author: riande
manager: wpickett
ms.custom: mvc
ms.date: 11/28/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/configuration/options
ms.openlocfilehash: 7d89416626433bf737b63eda4b17e65b089ae142
ms.sourcegitcommit: 8f42ab93402c1b8044815e1e48d0bb84c81f8b59
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="options-pattern-in-aspnet-core"></a>Modèle d’options dans ASP.NET Core

Par [Luke Latham](https://github.com/guardrex)

Le modèle d’options utilise les classes d’options pour représenter les groupes de paramètres associés. Lorsque les paramètres de configuration sont isolées par fonctionnalité dans les classes d’options distinctes, l’application est conforme aux deux principes important de l’ingénierie logicielle :

* Le [principe de répartition d’Interface (ISP)](http://deviq.com/interface-segregation-principle/): fonctionnalités (classes) qui dépendent des paramètres de configuration varient uniquement sur les paramètres de configuration qu’ils utilisent.
* [Séparation des préoccupations](http://deviq.com/separation-of-concerns/): paramètres pour les différentes parties de l’application ne sont pas dépendants ou couplée à un autre.

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample)) cet article est plus facile à suivre avec l’exemple d’application.

## <a name="basic-options-configuration"></a>Configuration des options de base

Configuration des options de base est présentée comme exemple &num;1 dans le [exemple d’application](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

Classe d’options doit être non abstrait avec un constructeur sans paramètre public. La classe suivante, `MyOptions`, a deux propriétés, `Option1` et `Option2`. Définition des valeurs par défaut est facultative, mais le constructeur de classe dans l’exemple suivant définit la valeur par défaut `Option1`. `Option2`a la valeur par défaut définie par l’initialisation de la propriété directement (*Models/MyOptions.cs*) :

[!code-csharp[Main](options/sample/Models/MyOptions.cs?name=snippet1)]

Le `MyOptions` classe est ajoutée au conteneur de service avec [IConfigureOptions&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) et lié à la configuration :

[!code-csharp[Main](options/sample/Startup.cs?name=snippet_Example1)]

La page suivante utilise des modèles [injection de dépendances constructeur](xref:fundamentals/dependency-injection#what-is-dependency-injection) avec [IOptions&lt;TOptions&gt; ](/dotnet/api/Microsoft.Extensions.Options.IOptions-1) pour accéder aux paramètres (*Pages/Index.cshtml.cs*):

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example1)]

L’exemple *appsettings.json* fichier spécifie des valeurs pour `option1` et `option2`:

[!code-json[Main](options/sample/appsettings.json)]

Lorsque l’application est exécutée, le modèle de page `OnGet` méthode retourne une chaîne indiquant les valeurs de la classe option :

```html
option1 = value1_from_json, option2 = -1
```

## <a name="configure-simple-options-with-a-delegate"></a>Configurer les options simples avec un délégué

Configuration des options simples avec un délégué est illustré comme exemple &num;2 dans le [exemple d’application](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

Utilisez un délégué pour définir les valeurs des options. L’application d’exemple utilise le `MyOptionsWithDelegateConfig` classe (*Models/MyOptionsWithDelegateConfig.cs*) :

[!code-csharp[Main](options/sample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Dans le code suivant, une seconde `IConfigureOptions<TOptions>` service est ajouté au conteneur de service. Il utilise un délégué pour configurer la liaison avec `MyOptionsWithDelegateConfig`:

[!code-csharp[Main](options/sample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Vous pouvez ajouter plusieurs fournisseurs de configuration. Fournisseurs de configuration sont disponibles dans les packages NuGet. Elles sont appliquées afin que ces dernières sont enregistrées.

Chaque appel à [configurer&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1.configure) ajoute un `IConfigureOptions<TOptions>` service au conteneur de service. Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont tous deux spécifiés dans *appsettings.json*, mais les valeurs de `Option1` et `Option2` sont remplacées par le délégué configuré.

Lorsque plusieurs services de configuration est activée, la dernière source de configuration spécifié *wins* et définit la valeur de configuration. Lorsque l’application est exécutée, le modèle de page `OnGet` méthode retourne une chaîne indiquant les valeurs de la classe option :

```html
delegate_option1 = value1_configured_by_delgate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Configuration des sous-Options

Configuration des sous-Options est illustrée comme exemple &num;3 dans le [exemple d’application](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

Applications doivent créer des classes d’options qui se rapportent à des groupes de fonctionnalité spécifique (classes) dans l’application. Les parties de l’application qui requièrent des valeurs de configuration doivent ont uniquement accès aux valeurs de configuration qu’ils utilisent.

Lors de la liaison des options de configuration, chaque propriété du type d’options est liée à une clé de configuration sous la forme `property[:sub-property:]`. Par exemple, le `MyOptions.Option1` propriété est liée à la clé `Option1`, qui est lu à partir du `option1` propriété dans *appsettings.json*.

Dans le code suivant, une troisième `IConfigureOptions<TOptions>` service est ajouté au conteneur de service. Il lie `MySubOptions` à la section `subsection` de la *appsettings.json* fichier :

[!code-csharp[Main](options/sample/Startup.cs?name=snippet_Example3)]

Le `GetSection` requiert de la méthode d’extension du [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package NuGet. Si l’application utilise le [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) metapackage, le package est automatiquement inclus.

L’exemple *appsettings.json* fichier définit un `subsection` membres avec des clés pour `suboption1` et `suboption2`:

[!code-json[Main](options/sample/appsettings.json?highlight=4-7)]

Le `MySubOptions` classe définit les propriétés, `SubOption1` et `SubOption2`, pour conserver les valeurs d’option sous-chemin (*Models/MySubOptions.cs*) :

[!code-csharp[Main](options/sample/Models/MySubOptions.cs?name=snippet1)]

Le modèle de page `OnGet` méthode retourne une chaîne avec les valeurs d’option sous-chemin (*Pages/Index.cshtml.cs*) :

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Lorsque l’application est exécutée, la `OnGet` méthode retourne une chaîne indiquant la sous-option les valeurs de classe :

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Options fournies par un modèle d’affichage ou avec l’injection de vue directe

Les options fournies par un modèle d’affichage ou avec l’injection de vue directe est illustré comme exemple &num;4 dans le [exemple d’application](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

Options peuvent être fournies dans un modèle d’affichage ou en injectant `IOptions<TOptions>` directement dans une vue (*Pages/Index.cshtml.cs*) :

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Pour l’injection directe, injecter `IOptions<MyOptions>` avec un `@inject` la directive :

[!code-cshtml[Main](options/sample/Pages/Index.cshtml?range=1-10&highlight=5)]

Lorsque l’application est exécutée, les valeurs d’option sont affichés dans la page rendue :

![Options de valeurs Option1 : value1_from_json et Option2 : -1 sont chargés à partir du modèle et par injection dans la vue.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Recharger les données de configuration avec IOptionsSnapshot

Recharger les données de configuration avec `IOptionsSnapshot` est illustré dans l’exemple &num;5 dans le [exemple d’application](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

*Nécessite ASP.NET Core 1.1 ou version ultérieure.*

[IOptionsSnapshot](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1) prend en charge de rechargement des options avec une charge de traitement minimal. Dans ASP.NET Core 1.1, `IOptionsSnapshot` est un instantané de [IOptionsMonitor&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) et mises à jour automatiquement chaque fois que le moniteur déclenche les modifications en fonction de la source de données modifiées. Dans ASP.NET Core 2.0 et versions ultérieures, les options sont calculées une fois par demande lorsque accessibles et mis en cache pour la durée de vie de la demande.

L’exemple suivant montre comment un nouveau `IOptionsSnapshot` est créée après *appsettings.json* modifications (*Pages/Index.cshtml.cs*). Plusieurs demandes au serveur de retournent des valeurs constantes fournies par le *appsettings.json* jusqu'à ce que le fichier est modifié et les rechargements de la configuration du fichier.

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example5)]

L’illustration suivante montre la première `option1` et `option2` valeurs chargées à partir de la *appsettings.json* fichier :

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Modifiez les valeurs dans le *appsettings.json* le fichier `value1_from_json UPDATED` et `200`. Enregistrer le *appsettings.json* fichier. Actualisez le navigateur pour voir que les valeurs des options sont mis à jour :

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Nommé prise en charge des options avec IConfigureNamedOptions

Nommé prise en charge des options avec [IConfigureNamedOptions](/dotnet/api/microsoft.extensions.options.iconfigurenamedoptions-1) est montré dans l’exemple &num;6 dans le [exemple d’application](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

*Nécessite un cœur d’ASP.NET 2.0 ou version ultérieure.*

*Nommé options* prise en charge permet à l’application de faire la distinction entre les configurations d’options nommée. Dans l’exemple d’application, nommées options sont déclarées avec le [ConfigureNamedOptions&lt;TOptions&gt;. Configurer](/dotnet/api/microsoft.extensions.options.configurenamedoptions-1.configure) méthode :

[!code-csharp[Main](options/sample/Startup.cs?name=snippet_Example6)]

L’exemple d’application accède aux options nommées avec [IOptionsSnapshot&lt;TOptions&gt;. Obtenir](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1.get) (*Pages/Index.cshtml.cs*) :

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example6)]

L’exemple d’application en cours d’exécution, les options nommées sont retournées :

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`les valeurs sont fournies à partir de la configuration, qui sont chargé à partir de la *appsettings.json* fichier. `named_options_2`les valeurs sont fournies par :

* Le `named_options_2` déléguer dans `ConfigureServices` pour `Option1`.
* La valeur par défaut `Option2` fournie par la `MyOptions` classe.

Configurez toutes les instances nommées d’options avec les [OptionsServiceCollectionExtensions.ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) (méthode). Le code suivant configure `Option1` pour toutes les instances de configuration avec une valeur commune nommées. Ajoutez le code suivant manuellement en la `Configure` méthode :

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

L’exemple d’application en cours d’exécution après avoir ajouté le code produit le résultat suivant :

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Dans ASP.NET Core 2.0 et versions ultérieures, toutes les options sont des instances nommées. Existant `IConfigureOption` instances sont traitées comme ciblant le `Options.DefaultName` instance, qui est `string.Empty`. `IConfigureNamedOptions`implémente également `IConfigureOptions`. L’implémentation par défaut de la [IOptionsFactory&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) ([source de référence](https://github.com/aspnet/Options/blob/release/2.0.0/src/Microsoft.Extensions.Options/OptionsFactory.cs)) possède une logique à utiliser chacune de manière appropriée. Le `null` option nommée est utilisée pour toutes les instances nommées spécifiques à une instance nommée au lieu de cibler ([ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) et [PostConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) utilisent cette convention).

## <a name="ipostconfigureoptions"></a>IPostConfigureOptions

*Nécessite un cœur d’ASP.NET 2.0 ou version ultérieure.*

Définir postconfiguration avec [IPostConfigureOptions&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1). Postconfiguration s’exécute une fois toutes les [IConfigureOptions&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) configuration se produit :

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

[PostConfigure&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1.postconfigure) est disponible pour configurer les options nommées après :

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Utilisez [PostConfigureAll&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) post-configurer tous les nommé instances de configuration :

```csharp
services.PostConfigureAll<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="options-factory-monitoring-and-cache"></a>Fabrique d’options, de surveillance et de cache

[IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) est utilisé pour les notifications lorsque `TOptions` modification des instances. `IOptionsMonitor`prend en charge des options reloadable, notifications de modifications, et `IPostConfigureOptions`.

[IOptionsFactory&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) (noyaux d’ASP.NET 2.0 ou version ultérieure) est chargé pour la création de nouvelles options d’instances. Il dispose d’un seul [créer](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1.create) (méthode). L’implémentation par défaut est enregistrée toutes les `IConfigureOptions` et `IPostConfigureOptions` et exécute tous les le configure en premier, suivi par le post-traitement configure. Il fait la distinction entre `IConfigureNamedOptions` et `IConfigureOptions` et n’appelle l’interface appropriée.

[IOptionsMonitorCache&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1) (noyaux d’ASP.NET 2.0 ou version ultérieure) est utilisé par `IOptionsMonitor` au cache `TOptions` instances. Le `IOptionsMonitorCache` invalide les instances des options dans l’analyse afin que la valeur est recalculée ([TryRemove](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryremove)). Les valeurs peuvent être manuellement introduites ainsi par [TryAdd](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryadd). Le [clair](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.clear) méthode est utilisée lorsque toutes les instances nommées doivent être recréés à la demande.

## <a name="see-also"></a>Voir aussi

* [Configuration](xref:fundamentals/configuration/index)

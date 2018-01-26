---
title: "Détecte les modifications avec modification de jetons dans ASP.NET Core"
author: guardrex
description: "Découvrez comment utiliser des jetons de modification pour effectuer le suivi des modifications."
manager: wpickett
ms.author: riande
ms.date: 11/10/2017
ms.devlang: csharp
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/primitives/change-tokens
ms.openlocfilehash: 94bf356fcbfab3930804485c1b65e4a0f4c52b8e
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Détecte les modifications avec modification de jetons dans ASP.NET Core

Par [Luke Latham](https://github.com/guardrex)

A *modifier le jeton* est un bloc de construction à usage général de bas niveau est utilisé pour effectuer le suivi des modifications.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/primitives/change-tokens/sample/) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Interface de IChangeToken

[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) propage les notifications qu’une modification s’est produite. `IChangeToken`réside dans le [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) espace de noms. Pour les applications qui n’utilisent pas le [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) metapackage, référence le [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) package NuGet dans le fichier projet.

`IChangeToken`a deux propriétés :

* [ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) indiquer si le jeton déclenche proactive des rappels. Si `ActiveChangedCallbacks` a la valeur `false`, un rappel n’est jamais appelé, et l’application doit interroger `HasChanged` des modifications. Il est également possible pour un jeton de ne jamais être annulé si aucune modification se produit ou si l’écouteur de modifications sous-jacent est supprimé ou désactivé.
* [HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) Obtient une valeur qui indique si une modification a eu lieu.

L’interface a une méthode, [RegisterChangeCallback (Action&lt;objet&gt;, objet)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), qui inscrit un rappel qui est appelé lorsque le jeton a été modifiée. `HasChanged`doit être définie avant le rappel est appelé.

## <a name="changetoken-class"></a>Classe de ChangeToken

`ChangeToken`une classe statique est utilisée pour propager des notifications a changé. `ChangeToken`réside dans le [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) espace de noms. Pour les applications qui n’utilisent pas le [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) metapackage, référence le [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) package NuGet dans le fichier projet.

Le `ChangeToken` [OnChange (Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) méthode registres une `Action` pour appeler chaque fois que le jeton change :
* `Func<IChangeToken>`génère le jeton.
* `Action`est appelé lorsque le jeton est modifiée.

`ChangeToken`a un [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) surcharge qui accepte un supplémentaires`TState`paramètre est passé dans le consommateur de jeton `Action`.

`OnChange`Retourne un [IDisposable](/dotnet/api/system.idisposable). Appel de [Dispose](/dotnet/api/system.idisposable.dispose) arrête le jeton de l’écoute des modifications supplémentaires et libère les ressources du jeton.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Exemples d’utilisation de jetons de modification dans ASP.NET Core

Les jetons de modification sont utilisés dans les zones principales d’ASP.NET Core surveiller les modifications apportées aux objets :

* Pour surveiller les modifications apportées aux fichiers, [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)de [espion](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) méthode crée un `IChangeToken` pour les fichiers ou dossier à surveiller.
* `IChangeToken`les jetons peuvent être ajoutés aux entrées du cache pour déclencher des suppressions dans le cache des modifications.
* Pour `TOptions` change, la valeur par défaut [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) implémentation de [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) a une surcharge qui accepte un ou plusieurs [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1)instances. Chaque instance retourne un `IChangeToken` pour inscrire un rappel de notification de modification des options de suivi des modifications.

## <a name="monitoring-for-configuration-changes"></a>Surveiller les modifications de configuration

Par défaut, utilisent les modèles ASP.NET Core [les fichiers de configuration JSON](xref:fundamentals/configuration/index#json-configuration) (*appsettings.json*, *appsettings. Development.JSON*, et *appsettings. Production.JSON*) pour charger les paramètres de configuration d’application.

Ces fichiers sont configurés à l’aide de la [AddJsonFile (IConfigurationBuilder, chaîne, booléen, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile?view=aspnetcore-2.0#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) méthode d’extension sur [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) qui accepte un `reloadOnChange` paramètre (ASP.NET Noyaux 1.1 et ultérieure). `reloadOnChange`Indique si la configuration doit être rechargée sur les modifications de fichier. Ce paramètre dans le [WebHost](/dotnet/api/microsoft.aspnetcore.webhost) méthode pratique [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) ([source de référence](https://github.com/aspnet/MetaPackages/blob/rel/2.0.3/src/Microsoft.AspNetCore/WebHost.cs#L152-L193)) :

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
```

Configuration basée sur le fichier représentée par [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource). `FileConfigurationSource`utilise [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) ([source de référence](https://github.com/aspnet/FileSystem/blob/patch/2.0.1/src/Microsoft.Extensions.FileProviders.Abstractions/IFileProvider.cs)) pour analyser les fichiers.

Par défaut, le `IFileMonitor` est fournie par un [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) ([source de référence](https://github.com/aspnet/Configuration/blob/patch/2.0.1/src/Microsoft.Extensions.Configuration.FileExtensions/FileConfigurationSource.cs#L82)), qui utilise [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) pour analyser le fichier de configuration modifications.

L’exemple d’application illustre les deux implémentations de surveillance des modifications de configuration. Si le *appsettings.json* les modifications de fichier ou la version de l’environnement du fichier change, chaque implémentation exécute du code personnalisé. L’exemple d’application écrit un message dans la console.

Un fichier de configuration `FileSystemWatcher` peut déclencher plusieurs rappels de jeton pour une modification de fichier de configuration unique. Implémentation de l’exemple protège contre ce problème en vérifiant les hachages de fichier sur les fichiers de configuration. Vérification des fichiers à hacher garantit qu’au moins un des fichiers de configuration a changé avant d’exécuter le code personnalisé. L’exemple utilise le fichier de hachage SHA1 (*Utilities/Utilities.cs*) :

   [!code-csharp[Main](change-tokens/sample/Utilities/Utilities.cs?name=snippet1)]

   Une nouvelle tentative est implémentée avec une interruption exponentielle. La nouvelle tentative est présente, car le verrouillage de fichier peut se produire qui empêche temporairement le calcul de hachage de nouveau sur l’un des fichiers.

### <a name="simple-startup-change-token"></a>Jeton de modification de démarrage simple

Inscrire un consommateur de jeton `Action` rappel pour les notifications de modification pour le jeton de rechargement de configuration (*Startup.cs*) :

[!code-csharp[Main](change-tokens/sample/Startup.cs?name=snippet2)]

`config.GetReloadToken()`fournit le jeton. Le rappel est le `InvokeChanged` méthode :

[!code-csharp[Main](change-tokens/sample/Startup.cs?name=snippet3)]

Le `state` du rappel est utilisé pour transmettre le `IHostingEnvironment`. Cela est utile pour déterminer la bonne *appsettings* fichier de configuration de JSON à analyser, *appsettings.&lt; Environnement&gt;.json*. Fichiers à hacher sont utilisés pour empêcher la `WriteConsole` instruction de s’exécuter plusieurs fois en raison de plusieurs rappels jeton lorsque le fichier de configuration a été modifiée uniquement une seule fois.

Ce système s’exécute en tant que l’application est en cours d’exécution et ne peut pas être désactivée par l’utilisateur.

### <a name="monitoring-configuration-changes-as-a-service"></a>Surveillance des modifications de configuration en tant que service

L’exemple implémente :

* Analyse de jeton du démarrage de base.
* Analyse en tant que service.
* Un mécanisme pour activer et désactiver l’analyse.

L’exemple établit une `IConfigurationMonitor` interface (*Extensions/ConfigurationMonitor.cs*) :

[!code-csharp[Main](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Le constructeur de la classe implémentée, `ConfigurationMonitor`, inscrit un rappel pour les notifications de modification :

[!code-csharp[Main](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`fournit le jeton. `InvokeChanged`est la méthode de rappel. Le `state` dans cette instance est une chaîne qui décrit l’état d’analyse. Deux propriétés sont utilisées :

* `MonitoringEnabled`Indique si le rappel doit s’exécuter son code personnalisé.
* `CurrentState`Décrit l’état en cours d’analyse pour une utilisation dans l’interface utilisateur.

Le `InvokeChanged` méthode est similaire à l’approche précédente, sauf qu’elle :

* Ne s’exécute pas son code, sauf si `MonitoringEnabled` est `true`.
* Définit le `CurrentState` chaîne de propriété et un message descriptif qui enregistre l’heure que le code s’exécute.
* Notes actuel `state` dans son `WriteConsole` sortie.

[!code-csharp[Main](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Une instance `ConfigurationMonitor` est inscrit en tant que service dans `ConfigureServices` de *Startup.cs*:

[!code-csharp[Main](change-tokens/sample/Startup.cs?name=snippet1)]

La page d’Index offre à l’utilisateur de contrôler l’analyse de configuration. L’instance de `IConfigurationMonitor` est injecté dans le `IndexModel`:

[!code-csharp[Main](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet1)]

Un bouton active et désactive la surveillance :

[!code-cshtml[Main](change-tokens/sample/Pages/Index.cshtml?range=35)]

[!code-csharp[Main](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet2)]

Lorsque `OnPostStartMonitoring` est déclenchée, l’analyse est activée, et l’état actuel est désactivée. Lorsque `OnPostStopMonitoring` est déclenchée, l’analyse est désactivée et l’état est défini pour indiquer que l’analyse n’est pas produit.

## <a name="monitoring-cached-file-changes"></a>Surveillance des modifications de fichier mis en cache

Contenu du fichier peut être mis en cache à l’aide de mémoire [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache). La mise en cache en mémoire est décrite dans le [mise en cache](xref:performance/caching/memory) rubrique. Sans prendre des mesures supplémentaires, telles que l’implémentation décrite ci-dessous, *obsolètes* données (obsolètes) sont retournées à partir d’un cache si la source de données change.

Sans tenir compte l’état d’un fichier source de mise en cache lors du renouvellement un [expiration décalée](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) période aboutit à des données du cache obsolètes. Chaque demande des données renouvelle la période d’expiration décalée, mais le fichier n’est jamais rechargé dans le cache. Toutes les fonctionnalités d’application qui utilisent le contenu du fichier mis en cache sont soumis aux éventuellement recevoir du contenu obsolète.

L’utilisation de jetons de modification dans un scénario de mise en cache de fichier empêche contenu du fichier obsolètes dans le cache. L’exemple d’application illustre une implémentation de l’approche.

L’exemple utilise `GetFileContent` pour :

* Retourne le contenu du fichier.
* Implémenter un algorithme de nouvelle tentative avec interruption exponentielle pour couvrir les cas où un verrou de fichier temporaire empêche un fichier en cours de lecture.

*Utilities/Utilities.cs*:

[!code-csharp[Main](change-tokens/sample/Utilities/Utilities.cs?name=snippet2)]

A `FileService` est créé pour gérer les recherches de fichier mis en cache. Le `GetFileContent` appel de méthode du service tente d’obtenir le contenu du fichier à partir du cache en mémoire et le retourner à l’appelant (*Services/FileService.cs*).

Si le contenu mis en cache n’est trouvé à l’aide de la clé de cache, les actions suivantes sont exécutées :

1. Le contenu du fichier est obtenu à l’aide de `GetFileContent`.
1. Un jeton de modification est obtenu à partir du fournisseur du fichier avec [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch). Rappel du jeton est déclenchée lorsque le fichier est modifié.
1. Le contenu du fichier est mis en cache avec un [expiration décalée](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) période. Le jeton de modification est attaché avec [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) pour supprimer l’entrée du cache si le fichier change pendant qu’il est mis en cache.

[!code-csharp[Main](change-tokens/sample/Services/FileService.cs?name=snippet1)]

Le `FileService` est enregistré dans le conteneur de service, ainsi que le service de mise en cache (*Startup.cs*) :

[!code-csharp[Main](change-tokens/sample/Startup.cs?name=snippet4)]

Le modèle de page charge le contenu du fichier à l’aide du service (*Pages/Index.cshtml.cs*) :

[!code-csharp[Main](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Classe de CompositeChangeToken

Pour représenter un ou plusieurs `IChangeToken` instances dans un seul objet, utilisez le [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken) classe ([source de référence](https://github.com/aspnet/Common/blob/patch/2.0.1/src/Microsoft.Extensions.Primitives/CompositeChangeToken.cs)).

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        { 
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged`dans les rapports de jeton composites `true` si les représenté jeton `HasChanged` est `true`. `ActiveChangeCallbacks`dans les rapports de jeton composites `true` si les représenté jeton `ActiveChangeCallbacks` est `true`. Si plusieurs modifications simultanées se produisent, le rappel de modification composite est appelé exactement une fois.

## <a name="see-also"></a>Voir aussi

* [Mise en cache en mémoire](xref:performance/caching/memory)
* [Utilisation d’un cache distribué](xref:performance/caching/distributed)
* [Détecter les modifications à l’aide de jetons de modification](xref:fundamentals/primitives/change-tokens)
* [Mise en cache des réponses](xref:performance/caching/response)
* [Intergiciel de mise en cache des réponses](xref:performance/caching/middleware)
* [Tag Helper de cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [Tag Helper de cache distribué](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)

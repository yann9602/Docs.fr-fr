---
title: Configuration dans ASP.NET Core
author: rick-anderson
description: "Utilisez l’API de configuration pour configurer une application ASP.NET Core selon plusieurs méthodes."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 11/01/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/configuration/index
ms.openlocfilehash: b662e66ab5b4c46d1a8d10eb7c38bf4064b5b927
ms.sourcegitcommit: 12e5194936b7e820efc5505a2d5d4f84e88eb5ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="configure-an-aspnet-core-app"></a>Configurer une application ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Mark Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

L’API de configuration fournit un moyen de configurer une application web ASP.NET Core basé sur une liste de paires nom/valeur. La configuration est lue au moment de l’exécution à partir de plusieurs sources. Vous pouvez regrouper ces paires nom/valeur dans une hiérarchie à plusieurs niveaux. 

Il existe des fournisseurs de configuration pour les éléments suivants :

* Formats de fichiers (INI, JSON et XML)
* Arguments de ligne de commande
* Variables d’environnement
* Objets .NET en mémoire
* Magasin d’utilisateurs chiffrés
* [Azure Key Vault](xref:security/key-vault-configuration)
* Fournisseurs personnalisés (installés ou créés)

Chaque valeur de configuration correspond à une clé de chaîne. Une liaison intégrée est prise en charge pour désérialiser les paramètres dans un objet [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) personnalisé (une classe .NET simple avec des propriétés).

Le modèle d’options utilise des classes d’options pour représenter les groupes de paramètres associés. Pour plus d’informations sur l’utilisation du modèle d’options, consultez la rubrique [Options](xref:fundamentals/configuration/options).

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/index/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="json-configuration"></a>Configuration JSON

L’application console suivante utilise le fournisseur de configuration JSON :

[!code-csharp[Main](index/sample/ConfigJson/Program.cs)]

L’application lit et affiche les paramètres de configuration suivants :

[!code-json[Main](index/sample/ConfigJson/appsettings.json)]

La configuration se compose d’une liste hiérarchique des paires nom/valeur dans laquelle les nœuds sont séparés par un signe deux-points. Pour récupérer une valeur, accédez à l’indexeur `Configuration` avec la clé de l’élément correspondant :

```csharp
Console.WriteLine($"option1 = {Configuration["subsection:suboption1"]}");
```

Pour utiliser des tableaux dans des sources de configuration au format JSON, utilisez un index de tableau comme partie d’une chaîne séparée par des signes deux-points. L’exemple suivant obtient le nom du premier élément dans le tableau `wizards` précédent :

```csharp
Console.Write($"{Configuration["wizards:0:Name"]}, ");
```

Les paires nom/valeur écrites dans les fournisseurs `Configuration` intégrés ne sont **pas** conservées. Toutefois, vous pouvez créer un fournisseur personnalisé qui enregistre les valeurs. Consultez la section relative à la création d’un [fournisseur de configuration personnalisé](xref:fundamentals/configuration/index#custom-config-providers).

L’exemple précédent utilise l’indexeur de configuration pour lire des valeurs. Pour accéder à la configuration en dehors de `Startup`, utilisez le *modèle d’options*. Pour plus d’informations, consultez la rubrique [Options](xref:fundamentals/configuration/options).

Il est courant d’avoir des paramètres de configuration différents pour différents environnements, par exemple pour l’environnement de développement, de test et de production. La méthode d’extension `CreateDefaultBuilder` dans une application ASP.NET Core 2.x (ou l’utilisation de `AddJsonFile` et de `AddEnvironmentVariables` directement dans une application ASP.NET Core 1.x) ajoute des fournisseurs de configuration pour la lecture des fichiers JSON et des sources de configuration système :

* *appsettings.json*
* *appsettings.\<nom_environnement>.json*
* Variables d’environnement

Consultez [AddJsonFile](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions) pour obtenir une explication des paramètres. `reloadOnChange` est pris en charge uniquement dans ASP.NET Core 1.1 et ultérieur. 

Les sources de configuration sont lues dans l’ordre où elles sont spécifiées. Dans le code ci-dessus, les variables d’environnement sont lues en dernier. Toutes les valeurs de configuration définies dans l’environnement remplacent celles définies dans les deux fournisseurs précédents.

L’environnement est généralement défini sur `Development`, `Staging` ou `Production`. Pour plus d’informations, consultez [Utilisation de plusieurs environnements](xref:fundamentals/environments).

Points à prendre en considération pour la configuration :

* `IOptionsSnapshot` peut recharger les données de configuration quand elles changent. Pour plus d’informations, consultez [IOptionsSnapshot](xref:fundamentals/configuration/options#reload-configuration-data-with-ioptionssnapshot).
* Les clés de configuration ne respectent pas la casse.
* Spécifiez les variables d’environnement en dernier afin que l’environnement local puisse substituer les paramètres spécifiés dans les fichiers de configuration déployés.
* Ne stockez **jamais** des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair. N’utilisez aucun secret de production dans vos environnements de développement ou de test. Spécifiez plutôt les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans votre référentiel. Découvrez des informations supplémentaires sur l’[Utilisation de plusieurs environnements](xref:fundamentals/environments) et la gestion du [Stockage sécurisé des secrets d’application lors du développement](xref:security/app-secrets).
* S’il n’est pas possible d’utiliser un signe deux-points (`:`) dans les variables d’environnement de votre système, remplacez le signe deux-points (`:`) par un double trait de soulignement (`__`).

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a>Fournisseur en mémoire et liaison à une classe POCO

L’exemple suivant montre comment utiliser le fournisseur en mémoire et le lier à une classe :

[!code-csharp[Main](index/sample/InMemory/Program.cs)]

Les valeurs de configuration sont retournées sous forme de chaînes, mais la liaison permet la construction d’objets. En effet, la liaison vous permet de récupérer des objets POCO ou même des graphes d’objets entiers.

### <a name="getvalue"></a>GetValue

L’exemple suivant illustre la méthode d’extension [GetValue&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) :

[!code-csharp[Main](index/sample/InMemoryGetValue/Program.cs?highlight=27-29)]

La méthode `GetValue<T>` de ConfigurationBinder vous permet de spécifier une valeur par défaut (80 dans l’exemple). `GetValue<T>` est destiné aux scénarios simples et n’établit pas de liaison à des sections entières. `GetValue<T>` obtient les valeurs scalaires de `GetSection(key).Value` converties en un type spécifique.

## <a name="bind-to-an-object-graph"></a>Établir une liaison à un graphe d’objets

Vous pouvez établir une liaison récursive à chaque objet d’une classe. Considérez la classe `AppSettings` suivante :

[!code-csharp[Main](index/sample/ObjectGraph/AppSettings.cs)]

L’exemple suivant crée une liaison à la classe `AppSettings` :

[!code-csharp[Main](index/sample/ObjectGraph/Program.cs?highlight=15-16)]

**ASP.NET Core 1.1** et les versions ultérieures peuvent utiliser `Get<T>`, qui fonctionne avec des sections entières. Il peut être plus pratique d’utiliser `Get<T>` que `Bind`. Le code suivant montre comment utiliser `Get<T>` avec l’exemple ci-dessus :

```csharp
var appConfig = config.GetSection("App").Get<AppSettings>();
```

En utilisant le fichier *appsettings.json* suivant :

[!code-json[Main](index/sample/ObjectGraph/appsettings.json)]

Le programme affiche `Height 11`.

Le code suivant peut être utilisé pour effectuer un test unitaire sur la configuration :

```csharp
[Fact]
public void CanBindObjectTree()
{
    var dict = new Dictionary<string, string>
        {
            {"App:Profile:Machine", "Rick"},
            {"App:Connection:Value", "connectionstring"},
            {"App:Window:Height", "11"},
            {"App:Window:Width", "11"}
        };
    var builder = new ConfigurationBuilder();
    builder.AddInMemoryCollection(dict);
    var config = builder.Build();

    var settings = new AppSettings();
    config.GetSection("App").Bind(settings);

    Assert.Equal("Rick", settings.Profile.Machine);
    Assert.Equal(11, settings.Window.Height);
    Assert.Equal(11, settings.Window.Width);
    Assert.Equal("connectionstring", settings.Connection.Value);
}
```

<a name="custom-config-providers"></a>

## <a name="create-an-entity-framework-custom-provider"></a>Créer un fournisseur personnalisé Entity Framework

Dans cette section, un fournisseur de configuration de base qui lit des paires nom/valeur à partir d’une base de données utilisant Entity Framework est créé. 

Définissez une entité `ConfigurationValue` pour le stockage des valeurs de configuration dans la base de données :

[!code-csharp[Main](index/sample/CustomConfigurationProvider/ConfigurationValue.cs)]

Ajoutez un `ConfigurationContext` pour stocker les valeurs configurées et y accéder :

[!code-csharp[Main](index/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]

Créez une classe qui implémente [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource) :

[!code-csharp[Main](index/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]

Créez le fournisseur de configuration personnalisé en héritant de [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider).  Le fournisseur de configuration initialise la base de données quand elle est vide :

[!code-csharp[Main](index/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]

Les valeurs en surbrillance provenant de la base de données ("value_from_ef_1" et "value_from_ef_2") sont affichées quand l’exemple est exécuté.

Vous pouvez ajouter une méthode d’extension `EFConfigSource` pour l’ajout de la source de configuration :

[!code-csharp[Main](index/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]

Le code suivant montre comment utiliser l’`EFConfigProvider` personnalisé :

[!code-csharp[Main](index/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]

Notez que l’exemple ajoute l’`EFConfigProvider` personnalisé après le fournisseur JSON. Par conséquent, tous les paramètres de la base de données substituent les paramètres du fichier *appsettings.json*.

En utilisant le fichier *appsettings.json* suivant :

[!code-json[Main](index/sample/CustomConfigurationProvider/appsettings.json)]

Le résultat suivant s’affiche :

```console
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

## <a name="commandline-configuration-provider"></a>Fournisseur de configuration CommandLine

Le [fournisseur de configuration CommandLine](/aspnet/core/api/microsoft.extensions.configuration.commandline.commandlineconfigurationprovider) reçoit des paires clé/valeur d’arguments de ligne de commande pour la configuration au moment de l’exécution.

[Afficher ou télécharger l’exemple de configuration CommandLine](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/index/sample/CommandLine)

### <a name="setup-and-use-the-commandline-configuration-provider"></a>Configurer et utiliser le fournisseur de configuration CommandLine

# <a name="basic-configurationtabbasicconfiguration"></a>[Configuration de base](#tab/basicconfiguration)

Pour activer la configuration en ligne de commande, appelez la méthode d’extension `AddCommandLine` sur une instance de [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder) :

[!code-csharp[Main](index/sample_snapshot//CommandLine/Program.cs?highlight=18,21)]

Quand le code est exécuté, la sortie suivante s’affiche :

```console
MachineName: MairaPC
Left: 1980
```

Le passage de paires clé/valeur d’arguments sur la ligne de commande modifie les valeurs de `Profile:MachineName` et de `App:MainWindow:Left` :

```console
dotnet run Profile:MachineName=BartPC App:MainWindow:Left=1979
```

La fenêtre de console s’affiche :

```console
MachineName: BartPC
Left: 1979
```

Pour substituer la configuration fournie par d’autres fournisseurs de configuration avec la configuration en ligne de commande, appelez `AddCommandLine` en dernier sur `ConfigurationBuilder` :

[!code-csharp[Main](index/sample_snapshot//CommandLine/Program2.cs?range=11-16&highlight=1,5)]

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Les applications ASP.NET Core 2.x classiques utilisent la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte :

[!code-csharp[Main](index/sample_snapshot//Program.cs?highlight=12)]

`CreateDefaultBuilder` charge une configuration facultative à partir d’*appsettings.json*, d’*appsettings.{Environment}.json*, de [secrets d’utilisateur](xref:security/app-secrets) (dans l’environnement `Development`), de variables d’environnement et d’arguments de ligne de commande. Le fournisseur de configuration CommandLine est appelé en dernier. Le fait d’appeler le fournisseur en dernier permet aux arguments de ligne de commande passés au moment de l’exécution de substituer la configuration définie par les autres fournisseurs de configuration appelés précédemment.

Notez que, pour les fichiers *appsettings*, `reloadOnChange` est activé. Les arguments de ligne de commande sont substitués si une valeur de configuration correspondante dans un fichier *appsettings* a été modifiée après le démarrage de l’application.

> [!NOTE]
> Pour remplacer l’utilisation de la méthode `CreateDefaultBuilder`, la création d’un hôte avec [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) et la génération manuelle de la configuration avec [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder) sont prises en charge dans ASP.NET Core 2.x. Pour plus d’informations, consultez l’onglet ASP.NET Core 1.x.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Créez un [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder) et appelez la méthode `AddCommandLine` pour utiliser le fournisseur de configuration CommandLine. Le fait d’appeler le fournisseur en dernier permet aux arguments de ligne de commande passés au moment de l’exécution de substituer la configuration définie par les autres fournisseurs de configuration appelés précédemment. Appliquez la configuration à [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) avec la méthode `UseConfiguration` :

[!code-csharp[Main](index/sample_snapshot//CommandLine/Program2.cs?highlight=11,15,19)]

---

### <a name="arguments"></a>Arguments

Les arguments passés sur la ligne de commande doivent être conformes à l’un des deux formats indiqués dans le tableau suivant.

| Format d’argument                                                     | Exemple        |
| ------------------------------------------------------------------- | :------------: |
| Argument unique : une paire clé/valeur séparée par un signe égal (`=`) | `key1=value`   |
| Séquence de deux arguments : une paire clé/valeur séparée par un espace    | `/key1 value1` |

**Argument unique**

La valeur doit suivre un signe égal (`=`). La valeur peut être null (par exemple, `mykey=`).

La clé peut avoir un préfixe.

| Préfixe de clé               | Exemple         |
| ------------------------ | :-------------: |
| Aucun préfixe                | `key1=value1`   |
| Un seul tiret (`-`)&#8224; | `-key2=value2`  |
| Deux tirets (`--`)        | `--key3=value3` |
| Barre oblique (`/`)      | `/key4=value4`  |

&#8224;Une clé avec un préfixe composé d’un seul préfixe (`-`) doit être fournie dans les [correspondances de commutateur](#switch-mappings), décrits ci-dessous.

Exemple de commande :

```console
dotnet run key1=value1 -key2=value2 --key3=value3 /key4=value4
```

Remarque : Si `-key1` n’est pas présent dans les [correspondances de commutateur](#switch-mappings) donnés au fournisseur de configuration, un `FormatException` est levé.

**Séquence de deux arguments**

La valeur ne peut pas être null et doit suivre la clé, séparée par un espace.

La clé doit avoir un préfixe.

| Préfixe de clé               | Exemple         |
| ------------------------ | :-------------: |
| Un seul tiret (`-`)&#8224; | `-key1 value1`  |
| Deux tirets (`--`)        | `--key2 value2` |
| Barre oblique (`/`)      | `/key3 value3`  |

&#8224;Une clé avec un préfixe composé d’un seul préfixe (`-`) doit être fournie dans les [correspondances de commutateur](#switch-mappings), décrits ci-dessous.

Exemple de commande :

```console
dotnet run -key1 value1 --key2 value2 /key3 value3
```

Remarque : Si `-key1` n’est pas présent dans les [correspondances de commutateur](#switch-mappings) donnés au fournisseur de configuration, un `FormatException` est levé.

### <a name="duplicate-keys"></a>Clés en double

Si des clés en double sont fournies, la dernière paire clé/valeur est utilisée.

### <a name="switch-mappings"></a>Correspondances de commutateur

Lors de la génération manuelle d’une configuration avec `ConfigurationBuilder`, vous pouvez éventuellement fournir un dictionnaire de correspondances de commutateur pour la méthode `AddCommandLine`. Les correspondances de commutateur vous permettent de fournir une logique de remplacement des noms de clés.

Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande. Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire (le remplacement de la clé) est repassée pour définir la configuration. Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).

Règles des clés du dictionnaire de correspondances de commutateur :

* Les commutateurs doivent commencer par un tiret (`-`) ou un double tiret (`--`).
* Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.

Dans l’exemple suivant, la méthode `GetSwitchMappings` permet à vos arguments de ligne de commande d’utiliser un préfixe de clé composé d’un tiret unique (`-`) et d’éviter les préfixes de sous-clés.

[!code-csharp[Main](index/sample/CommandLine/Program.cs?highlight=10-19,32)]

Si aucun argument de ligne de commande n’est spécifié, le dictionnaire fourni à `AddInMemoryCollection` définit les valeurs de configuration. Exécutez l’application avec la commande suivante :

```console
dotnet run
```

La fenêtre de console s’affiche :

```console
MachineName: RickPC
Left: 1980
```

Utilisez le code suivant pour passer des paramètres de configuration :

```console
dotnet run /Profile:MachineName=DahliaPC /App:MainWindow:Left=1984
```

La fenêtre de console s’affiche :

```console
MachineName: DahliaPC
Left: 1984
```

Une fois le dictionnaire de correspondances de commutateur créé, il contient les données affichées dans le tableau suivant.

| Touche            | Value                 |
| -------------- | --------------------- |
| `-MachineName` | `Profile:MachineName` |
| `-Left`        | `App:MainWindow:Left` |

Pour illustrer le remplacement des clés à l’aide du dictionnaire, exécutez la commande suivante :

```console
dotnet run -MachineName=ChadPC -Left=1988
```

Les clés de ligne de commande sont remplacées. La fenêtre de console affiche les valeurs de configuration pour `Profile:MachineName` et `App:MainWindow:Left` :

```console
MachineName: ChadPC
Left: 1988
```

## <a name="the-webconfig-file"></a>Le fichier web.config

Un fichier *web.config* est nécessaire pour héberger l’application dans IIS ou IIS Express. Les paramètres de *web.config* permettent au [module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) de lancer l’application et de configurer d’autres modules et paramètres IIS. Si le fichier *web.config* n’est pas présent et que le fichier projet contient `<Project Sdk="Microsoft.NET.Sdk.Web">`, la publication du projet crée un fichier *web.config* dans la sortie publiée (le dossier *publish*). Pour plus d’informations, consultez [Héberger ASP.NET Core sur Windows avec IIS](xref:host-and-deploy/iis/index#webconfig).

## <a name="additional-notes"></a>Remarques supplémentaires

* L’injection de dépendances n’est pas définie tant que `ConfigureServices` n’est pas appelé.
* Le système de configuration ne prend pas en charge l’injection de dépendances.
* `IConfiguration` a deux spécialisations :
  * `IConfigurationRoot` Utilisé pour le nœud racine. Peut déclencher un rechargement.
  * `IConfigurationSection` Représente une section de valeurs de configuration. Les méthodes `GetSection` et `GetChildren` retournent un `IConfigurationSection`.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Options](xref:fundamentals/configuration/options)
* [Utilisation de plusieurs environnements](xref:fundamentals/environments)
* [Stockage sécurisé des secrets de l’application durant le développement](xref:security/app-secrets)
* [Hébergement dans ASP.NET Core](xref:fundamentals/hosting)
* [Injection de dépendances](xref:fundamentals/dependency-injection)
* [Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration)

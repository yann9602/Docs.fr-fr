---
title: Configuration dans ASP.NET Core
author: rick-anderson
description: "Découvrez comment utiliser l’API de Configuration pour configurer une application ASP.NET Core à partir de plusieurs sources."
keywords: ASP.NET Core, configuration, JSON, configuration
ms.author: riande
manager: wpickett
ms.date: 6/24/2017
ms.topic: article
ms.assetid: b3a5984d-e172-42eb-8a48-547e4acb6806
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/configuration
ms.openlocfilehash: 041bb04a3a3699a166a03338865da154403d8c07
ms.sourcegitcommit: f535ce61c6a5e615bc6399b5d763c734396231f4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2017
---
<a name=fundamentals-configuration></a>

# <a name="configuration-in-aspnet-core"></a>Configuration dans ASP.NET Core

[Rick Anderson](https://twitter.com/RickAndMSFT), [marque Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), et [Michel Roth](https://github.com/danroth27)

L’API de Configuration permet de configurer une application basée sur une liste de paires nom-valeur. Configuration est en lecture lors de l’exécution de plusieurs sources. Les paires nom-valeur peuvent être regroupées en une hiérarchie à plusieurs niveaux. Il existe des fournisseurs de configuration pour :

* Formats de fichier (INI, JSON et XML)
* Arguments de ligne de commande
* Variables d’environnement
* Objets de mémoire .NET
* Un magasin d’utilisateur chiffré
* [Coffre de clés Azure](xref:security/key-vault-configuration)
* Fournisseurs personnalisés, que vous pouvez installer ou créer

Chaque valeur de configuration correspond à une clé de chaîne. Prise en charge de la liaison intégrée pour désérialiser les paramètres dans un personnalisé [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objet (une classe .NET simple avec des propriétés).

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample)

## <a name="simple-configuration"></a>Configuration simple

L’application console suivante utilise le fournisseur de configuration JSON :

[!code-csharp[Main](configuration/sample/ConfigJson/Program.cs)]

L’application lit et affiche les paramètres de configuration suivants :

[!code-json[Main](configuration/sample/ConfigJson/appsettings.json)]

Configuration se compose d’une liste hiérarchique des paires nom-valeur dans lequel les nœuds sont séparés par un signe deux-points. Pour récupérer une valeur, accédez à la `Configuration` indexeur avec la clé de l’élément correspondant :

```csharp
Console.WriteLine($"option1 = {Configuration["subsection:suboption1"]}");
```

Pour utiliser des tableaux dans les sources de configuration d’au format JSON, utilisez un index de tableau en tant que partie d’une chaîne séparée par des deux-points. L’exemple suivant obtient le nom du premier élément dans l’exemple précédent `wizards` tableau :

```csharp
Console.Write($"{Configuration["wizards:0:Name"]}, ");
```

Les paires nom-valeur écrites dans la génération dans `Configuration` fournisseurs sont **pas** rendu persistant, toutefois, vous pouvez créer un fournisseur personnalisé qui enregistre les valeurs. Consultez [fournisseur de configuration personnalisé](xref:fundamentals/configuration#custom-config-providers).

L’exemple précédent utilise l’indexeur de la configuration pour lire des valeurs. Accéder à la configuration en dehors de `Startup`, utilisez le [modèle d’options](xref:fundamentals/configuration#options-config-objects). Le *modèle d’options* est indiqué plus loin dans cet article.

Il est courant d’avoir des paramètres de configuration différents pour différents environnements, par exemple, développement, test et production. Le `CreateDefaultBuilder` méthode d’extension dans une application de 2.x ASP.NET Core (ou à l’aide de `AddJsonFile` et `AddEnvironmentVariables` directement dans une application de 1.x ASP.NET Core) ajoute des fournisseurs de configuration pour la lecture des fichiers au format JSON et système de sources de configuration :

* *appsettings.json*
* *appSettings. \<EnvironmentName > .json*
* variables d'environnement

Consultez [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) pour obtenir une explication des paramètres. `reloadOnChange`est pris en charge uniquement dans la version 1.1 de ASP.NET Core et versions ultérieures. 

Sources de configuration sont lues dans l’ordre qu’ils sont spécifiés. Dans le code ci-dessus, les variables d’environnement sont lus en dernier. Les valeurs de configuration définies par l’environnement remplacez celles définies dans les deux fournisseurs précédentes.

L’environnement est généralement défini à une des `Development`, `Staging`, ou `Production`. Consultez [fonctionne avec plusieurs environnements](environments.md) pour plus d’informations.

Considérations relatives à la configuration :

* `IOptionsSnapshot`pouvez recharger les données de configuration quand il change. Utilisez `IOptionsSnapshot` si vous avez besoin recharger les données de configuration.  Consultez [IOptionsSnapshot](#ioptionssnapshot) pour plus d’informations.
* Clés de configuration sont en minuscules.
* Il est recommandé est pour spécifier des variables d’environnement, afin que l’environnement local peut substituer tout élément défini dans les fichiers de configuration déployée.
* **Jamais** stocker les mots de passe ou d’autres données sensibles dans le code de fournisseur de configuration ou dans les fichiers de configuration en texte brut. N’utilisez des secrets de fabrication dans votre développement ou environnements de test. Au lieu de cela, spécifiez les clés secrètes à l’extérieur de l’arborescence du projet, afin qu’ils ne peuvent pas être validées accidentellement dans votre référentiel. En savoir plus sur [fonctionne avec plusieurs environnements](environments.md) et la gestion des [stockage sécurisé des secrets d’application pendant le développement](../security/app-secrets.md).
* Si `:` ne peut pas être utilisé dans les variables d’environnement dans votre système, remplacez `:` avec `__` (double trait de soulignement).

<a name=options-config-objects></a>

## <a name="using-options-and-configuration-objects"></a>À l’aide des Options et des objets de configuration

Le modèle d’options utilise les classes d’options personnalisées pour représenter un groupe de paramètres associés. Nous vous recommandons de créer découplée de classes pour chaque fonctionnalité dans votre application. Classes de découplée suivent :

* Le [principe de répartition d’Interface (ISP)](http://deviq.com/interface-segregation-principle/) : Classes dépendent uniquement les paramètres de configuration qu’ils utilisent.
* [Séparation des préoccupations](http://deviq.com/separation-of-concerns/) : paramètres pour les différentes parties de votre application ne sont pas dépendants ou associés à un autre.

La classe d’options doit être non abstrait avec un constructeur sans paramètre public. Exemple :

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MyOptions.cs)]

<a name=options-example></a>

Dans le code suivant, le fournisseur de configuration JSON est activé. La `MyOptions` classe est ajoutée au conteneur de service et lié à la configuration.

[!code-csharp[Main](configuration/sample/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-21)]

Les éléments suivants [contrôleur](../mvc/controllers/index.md) utilise [constructeur Injection de dépendance](xref:fundamentals/dependency-injection#what-is-dependency-injection) sur [ `IOptions<TOptions>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) pour accéder aux paramètres :

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController.cs?name=snippet1)]

Par le code suivant *appsettings.json* fichier :

[!code-json[Main](configuration/sample/UsingOptions/appsettings1.json)]

Le `HomeController.Index` méthode renvoie `option1 = value1_from_json, option2 = 2`.

Les applications classiques ne lier l’ensemble de la configuration dans un fichier d’options unique. Plus tard, je vais montrer comment utiliser `GetSection` pour lier à une section.

Dans le code suivant, une seconde `IConfigureOptions<TOptions>` service est ajouté au conteneur de service. Il utilise un délégué pour configurer la liaison avec `MyOptions`.

[!code-csharp[Main](configuration/sample/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]

Vous pouvez ajouter plusieurs fournisseurs de configuration. Fournisseurs de configuration sont disponibles dans les packages NuGet. Elles sont appliquées dans l’ordre qu’ils sont inscrits.

Chaque appel à `Configure<TOptions>` ajoute un `IConfigureOptions<TOptions>` service au conteneur de service. Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont tous deux spécifiés dans *appsettings.json* --mais la valeur de `Option1` est remplacée par le délégué configuré. 

Lorsque plusieurs services de configuration est activée, la dernière configuration la source spécifiée « remporte » (qui définit la valeur de configuration). Dans le code précédent, le `HomeController.Index` méthode renvoie `option1 = value1_from_action, option2 = 2`.

Lorsque vous liez des options de configuration, chaque propriété dans votre type d’options est liée à une clé de configuration sous la forme `property[:sub-property:]`. Par exemple, le `MyOptions.Option1` propriété est liée à la clé `Option1`, qui est lu à partir du `option1` propriété dans *appsettings.json*. Un exemple de sous-propriété est présenté plus loin dans cet article.

Dans le code suivant, une troisième `IConfigureOptions<TOptions>` service est ajouté au conteneur de service. Il lie `MySubOptions` à la section `subsection` de la *appsettings.json* fichier :

[!code-csharp[Main](configuration/sample/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]

Remarque : Cette méthode d’extension nécessite le `Microsoft.Extensions.Options.ConfigurationExtensions` package NuGet.

À l’aide de ce qui suit *appsettings.json* fichier :

[!code-json[Main](configuration/sample/UsingOptions/appsettings.json)]

La `MySubOptions` classe :

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MySubOptions.cs?name=snippet1)]

Par le code suivant `Controller`:

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]

`subOption1 = subvalue1_from_json, subOption2 = 200`est retourné.

Vous pouvez également fournir des options dans un modèle d’affichage ou injecter `IOptions<TOptions>` directement dans une vue :

[!code-html[Main](configuration/sample/UsingOptions/Views/Home/Index.cshtml?highlight=3-4,16-17,20-21)]

<a name=in-memory-provider></a>

## <a name="ioptionssnapshot"></a>IOptionsSnapshot

*Nécessite ASP.NET Core 1.1 ou une version ultérieure.*

`IOptionsSnapshot`prend en charge le rechargement des données de configuration lorsque le fichier de configuration a été modifiée. Il a une surcharge minimale. À l’aide de `IOptionsSnapshot` avec `reloadOnChange: true`, les options sont liées aux `IConfiguration` rechargées en cas de modification.

L’exemple suivant montre comment un nouveau `IOptionsSnapshot` est créée après *config.json* modifications. Les demandes vers le serveur retournera la même heure *config.json* a **pas** modifié. La première demande après *config.json* les modifications prendront effet une nouvelle heure.

[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]

L’illustration suivante montre le résultat du serveur :

![affichage d’image de navigateur « dernière mise à jour : 22/11/2016 16 h 43 »](configuration/_static/first.png)

Actualiser le navigateur ne change pas la valeur de message ou l’heure affichée (lorsque *config.json* n’a pas changé).

Modifier et enregistrer le *config.json* , puis actualisez le navigateur :

![affichage d’image de navigateur « dernière mise à jour à synchroniser : 22/11/2016 4:53 PM »](configuration/_static/change.png)

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a>Fournisseur en mémoire et la liaison à une classe POCO

L’exemple suivant montre comment utiliser le fournisseur en mémoire et le lier à une classe :

[!code-csharp[Main](configuration/sample/InMemory/Program.cs)]

Les valeurs de configuration sont renvoyées sous forme de chaînes, mais la liaison permet la construction d’objets. Liaison vous permet de récupérer les objets POCO ou des graphiques d’objets complets. L’exemple suivant montre comment lier à `MyWindow` et utiliser le modèle d’options avec une application ASP.NET MVC de base :

[!code-csharp[Main](configuration/sample/WebConfigBind/MyWindow.cs)]

[!code-json[Main](configuration/sample/WebConfigBind/appsettings.json)]

Lier la classe personnalisée dans `ConfigureServices` lors de la création de l’ordinateur hôte :

[!code-csharp[Main](configuration/sample/WebConfigBind/Program.cs?name=snippet1&highlight=3-4)]

Afficher les paramètres à partir de la `HomeController`:

[!code-csharp[Main](configuration/sample/WebConfigBind/Controllers/HomeController.cs)]

### <a name="getvalue"></a>GetValue

L’exemple suivant montre comment la [GetValue<T> ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) méthode d’extension :

[!code-csharp[Main](configuration/sample/InMemoryGetValue/Program.cs?highlight=27-29)]

De la ConfigurationBinder `GetValue<T>` méthode vous permet de spécifier une valeur par défaut (80 dans l’exemple). `GetValue<T>`pour des scénarios simples et n’est pas lié à des sections entières. `GetValue<T>`Obtient les valeurs scalaires de `GetSection(key).Value` converti en un type spécifique.

## <a name="binding-to-an-object-graph"></a>Liaison à un graphique d’objet

Vous pouvez lier de manière récursive à chaque objet dans une classe. Considérez les éléments suivants `AppOptions` classe :

[!code-csharp[Main](configuration/sample/ObjectGraph/AppOptions.cs)]

L’exemple suivant crée une liaison avec la `AppOptions` classe :

[!code-csharp[Main](configuration/sample/ObjectGraph/Program.cs?highlight=15-16)]

**ASP.NET Core 1.1** et utiliser une valeur supérieure `Get<T>`, qui fonctionne avec des sections entières. `Get<T>`peut être plus convienent que l’utilisation de `Bind`. Le code suivant montre comment utiliser `Get<T>` avec l’exemple ci-dessus :

```csharp
var appConfig = config.GetSection("App").Get<AppOptions>();
```

À l’aide de ce qui suit *appsettings.json* fichier :

[!code-json[Main](configuration/sample/ObjectGraph/appsettings.json)]

Le programme affiche `Height 11`.

Le code suivant peut être utilisé pour l’unité la configuration de test :

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

    var options = new AppOptions();
    config.GetSection("App").Bind(options);

    Assert.Equal("Rick", options.Profile.Machine);
    Assert.Equal(11, options.Window.Height);
    Assert.Equal(11, options.Window.Width);
    Assert.Equal("connectionstring", options.Connection.Value);
}
```

<a name=custom-config-providers></a>

## <a name="basic-sample-of-entity-framework-custom-provider"></a>Exemple de base du fournisseur personnalisé de Entity Framework

Dans cette section, un fournisseur de configuration de base qui lit les paires nom-valeur à partir d’une base de données à l’aide de EF est créé. 

Définir un `ConfigurationValue` entité pour le stockage des valeurs de configuration dans la base de données :

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationValue.cs)]

Ajouter un `ConfigurationContext` pour stocker et accéder aux valeurs configurées :

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]

Créez une classe qui implémente [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]

Créer le fournisseur de configuration personnalisé en héritant de [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider).  Le fournisseur de configuration initialise la base de données lorsqu’elle est vide :

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]

Les valeurs en surbrillance à partir de la base de données (« value_from_ef_1 » et « value_from_ef_2 ») sont affichées lors de l’exemple est exécuté.

Vous pouvez ajouter un `EFConfigSource` méthode d’extension pour l’ajout de la source de configuration :

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]

Le code suivant montre comment utiliser personnalisée `EFConfigProvider`:

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]

Notez que l’exemple ajoute personnalisé `EFConfigProvider` lorsque le fournisseur JSON, par conséquent, tous les paramètres de la base de données remplace les paramètres à partir de la *appsettings.json* fichier.

À l’aide de ce qui suit *appsettings.json* fichier :

[!code-json[Main](configuration/sample/CustomConfigurationProvider/appsettings.json)]

Affiche les informations suivantes :

```console
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

## <a name="commandline-configuration-provider"></a>Fournisseur de configuration de ligne de commande

L’exemple suivant active le fournisseur de configuration de ligne de commande dernière :

[!code-csharp[Main](configuration/sample/CommandLine/Program.cs)]

Passer des paramètres de configuration, utilisez les éléments suivants :

```console
dotnet run /Profile:MachineName=Bob /App:MainWindow:Left=1234
```

Qui s’affiche :

```console
Hello Bob
Left 1234
```

Le `GetSwitchMappings` méthode vous permet d’utiliser `-` plutôt que `/` et il supprime les préfixes de sous-clés au début. Exemple :

```console
dotnet run -MachineName=Bob -Left=7734
```

Affiche :

```console
Hello Bob
Left 7734
```

Arguments de ligne de commande doivent inclure une valeur (il peut être null). Exemple :

```console
dotnet run /Profile:MachineName=
```

Est OK, mais

```console
dotnet run /Profile:MachineName
```

génère une exception. Une exception est levée si vous spécifiez un préfixe de commutateur de ligne de commande - ou--pour lequel il n’existe aucun mappage de commutateur correspondant.

## <a name="the-webconfig-file"></a>Le fichier web.config

A *web.config* fichier est requis lorsque vous hébergez l’application dans IIS ou IIS Express. *Web.config* Active le AspNetCoreModule dans IIS pour lancer votre application. Paramètres de *web.config* activer le AspNetCoreModule dans IIS pour lancer votre application et de configurer d’autres modules et les paramètres IIS. Si vous utilisez Visual Studio, supprimez *web.config*, Visual Studio crée un nouveau.

### <a name="additional-notes"></a>Remarques supplémentaires

* Injection de dépendance (DI) n’est pas définie jusqu'à après `ConfigureServices` est appelé.
* Le système de configuration n’est pas DI prenant en charge.
* `IConfiguration`a deux spécialisations :
  * `IConfigurationRoot`Utilisé pour le nœud racine. Peut déclencher un rechargement.
  * `IConfigurationSection`Représente une section de valeurs de configuration. Le `GetSection` et `GetChildren` méthodes retournent un `IConfigurationSection`.

### <a name="additional-resources"></a>Ressources supplémentaires

* [Utilisation de plusieurs environnements](environments.md)
* [Stockage sécurisé des secrets de l’application durant le développement](../security/app-secrets.md)
* [Injection de dépendances](dependency-injection.md)
* [Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration)

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

# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="c8697-104">Configuration dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8697-104">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="c8697-105">[Rick Anderson](https://twitter.com/RickAndMSFT), [marque Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), et [Michel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c8697-105">[Rick Anderson](https://twitter.com/RickAndMSFT), [Mark Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c8697-106">L’API de Configuration permet de configurer une application basée sur une liste de paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="c8697-106">The Configuration API provides a way of configuring an app based on a list of name-value pairs.</span></span> <span data-ttu-id="c8697-107">Configuration est en lecture lors de l’exécution de plusieurs sources.</span><span class="sxs-lookup"><span data-stu-id="c8697-107">Configuration is read at runtime from multiple sources.</span></span> <span data-ttu-id="c8697-108">Les paires nom-valeur peuvent être regroupées en une hiérarchie à plusieurs niveaux.</span><span class="sxs-lookup"><span data-stu-id="c8697-108">The name-value pairs can be grouped into a multi-level hierarchy.</span></span> <span data-ttu-id="c8697-109">Il existe des fournisseurs de configuration pour :</span><span class="sxs-lookup"><span data-stu-id="c8697-109">There are configuration providers for:</span></span>

* <span data-ttu-id="c8697-110">Formats de fichier (INI, JSON et XML)</span><span class="sxs-lookup"><span data-stu-id="c8697-110">File formats (INI, JSON, and XML)</span></span>
* <span data-ttu-id="c8697-111">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="c8697-111">Command-line arguments</span></span>
* <span data-ttu-id="c8697-112">Variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="c8697-112">Environment variables</span></span>
* <span data-ttu-id="c8697-113">Objets de mémoire .NET</span><span class="sxs-lookup"><span data-stu-id="c8697-113">In-memory .NET objects</span></span>
* <span data-ttu-id="c8697-114">Un magasin d’utilisateur chiffré</span><span class="sxs-lookup"><span data-stu-id="c8697-114">An encrypted user store</span></span>
* [<span data-ttu-id="c8697-115">Coffre de clés Azure</span><span class="sxs-lookup"><span data-stu-id="c8697-115">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
* <span data-ttu-id="c8697-116">Fournisseurs personnalisés, que vous pouvez installer ou créer</span><span class="sxs-lookup"><span data-stu-id="c8697-116">Custom providers, which you install or create</span></span>

<span data-ttu-id="c8697-117">Chaque valeur de configuration correspond à une clé de chaîne.</span><span class="sxs-lookup"><span data-stu-id="c8697-117">Each configuration value maps to a string key.</span></span> <span data-ttu-id="c8697-118">Prise en charge de la liaison intégrée pour désérialiser les paramètres dans un personnalisé [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objet (une classe .NET simple avec des propriétés).</span><span class="sxs-lookup"><span data-stu-id="c8697-118">There's built-in binding support to deserialize settings into a custom [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) object (a simple .NET class with properties).</span></span>

[<span data-ttu-id="c8697-119">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="c8697-119">View or download sample code</span></span>](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample)

## <a name="simple-configuration"></a><span data-ttu-id="c8697-120">Configuration simple</span><span class="sxs-lookup"><span data-stu-id="c8697-120">Simple configuration</span></span>

<span data-ttu-id="c8697-121">L’application console suivante utilise le fournisseur de configuration JSON :</span><span class="sxs-lookup"><span data-stu-id="c8697-121">The following console app uses the JSON configuration provider:</span></span>

[!code-csharp[Main](configuration/sample/ConfigJson/Program.cs)]

<span data-ttu-id="c8697-122">L’application lit et affiche les paramètres de configuration suivants :</span><span class="sxs-lookup"><span data-stu-id="c8697-122">The app reads and displays the following configuration settings:</span></span>

[!code-json[Main](configuration/sample/ConfigJson/appsettings.json)]

<span data-ttu-id="c8697-123">Configuration se compose d’une liste hiérarchique des paires nom-valeur dans lequel les nœuds sont séparés par un signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="c8697-123">Configuration consists of a hierarchical list of name-value pairs in which the nodes are separated by a colon.</span></span> <span data-ttu-id="c8697-124">Pour récupérer une valeur, accédez à la `Configuration` indexeur avec la clé de l’élément correspondant :</span><span class="sxs-lookup"><span data-stu-id="c8697-124">To retrieve a value, access the `Configuration` indexer with the corresponding item's key:</span></span>

```csharp
Console.WriteLine($"option1 = {Configuration["subsection:suboption1"]}");
```

<span data-ttu-id="c8697-125">Pour utiliser des tableaux dans les sources de configuration d’au format JSON, utilisez un index de tableau en tant que partie d’une chaîne séparée par des deux-points.</span><span class="sxs-lookup"><span data-stu-id="c8697-125">To work with arrays in JSON-formatted configuration sources, use a array index as part of the colon-separated string.</span></span> <span data-ttu-id="c8697-126">L’exemple suivant obtient le nom du premier élément dans l’exemple précédent `wizards` tableau :</span><span class="sxs-lookup"><span data-stu-id="c8697-126">The following example gets the name of the first item in the preceding `wizards` array:</span></span>

```csharp
Console.Write($"{Configuration["wizards:0:Name"]}, ");
```

<span data-ttu-id="c8697-127">Les paires nom-valeur écrites dans la génération dans `Configuration` fournisseurs sont **pas** rendu persistant, toutefois, vous pouvez créer un fournisseur personnalisé qui enregistre les valeurs.</span><span class="sxs-lookup"><span data-stu-id="c8697-127">Name-value pairs written to the built in `Configuration` providers are **not** persisted, however, you can create a custom provider that saves values.</span></span> <span data-ttu-id="c8697-128">Consultez [fournisseur de configuration personnalisé](xref:fundamentals/configuration#custom-config-providers).</span><span class="sxs-lookup"><span data-stu-id="c8697-128">See [custom configuration provider](xref:fundamentals/configuration#custom-config-providers).</span></span>

<span data-ttu-id="c8697-129">L’exemple précédent utilise l’indexeur de la configuration pour lire des valeurs.</span><span class="sxs-lookup"><span data-stu-id="c8697-129">The preceding sample uses the configuration indexer to read values.</span></span> <span data-ttu-id="c8697-130">Accéder à la configuration en dehors de `Startup`, utilisez le [modèle d’options](xref:fundamentals/configuration#options-config-objects).</span><span class="sxs-lookup"><span data-stu-id="c8697-130">To access configuration outside of `Startup`, use the [options pattern](xref:fundamentals/configuration#options-config-objects).</span></span> <span data-ttu-id="c8697-131">Le *modèle d’options* est indiqué plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="c8697-131">The *options pattern* is shown later in this article.</span></span>

<span data-ttu-id="c8697-132">Il est courant d’avoir des paramètres de configuration différents pour différents environnements, par exemple, développement, test et production.</span><span class="sxs-lookup"><span data-stu-id="c8697-132">It's typical to have different configuration settings for different environments, for example, development, test, and production.</span></span> <span data-ttu-id="c8697-133">Le `CreateDefaultBuilder` méthode d’extension dans une application de 2.x ASP.NET Core (ou à l’aide de `AddJsonFile` et `AddEnvironmentVariables` directement dans une application de 1.x ASP.NET Core) ajoute des fournisseurs de configuration pour la lecture des fichiers au format JSON et système de sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="c8697-133">The `CreateDefaultBuilder` extension method in an ASP.NET Core 2.x app (or using `AddJsonFile` and `AddEnvironmentVariables` directly in an ASP.NET Core 1.x app) adds configuration providers for reading JSON files and system configuration sources:</span></span>

* <span data-ttu-id="c8697-134">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="c8697-134">*appsettings.json*</span></span>
* <span data-ttu-id="c8697-135">*appSettings. \<EnvironmentName > .json*</span><span class="sxs-lookup"><span data-stu-id="c8697-135">*appsettings.\<EnvironmentName>.json*</span></span>
* <span data-ttu-id="c8697-136">variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="c8697-136">environment variables</span></span>

<span data-ttu-id="c8697-137">Consultez [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) pour obtenir une explication des paramètres.</span><span class="sxs-lookup"><span data-stu-id="c8697-137">See [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) for an explanation of the parameters.</span></span> <span data-ttu-id="c8697-138">`reloadOnChange`est pris en charge uniquement dans la version 1.1 de ASP.NET Core et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="c8697-138">`reloadOnChange` is only supported in ASP.NET Core 1.1 and higher.</span></span> 

<span data-ttu-id="c8697-139">Sources de configuration sont lues dans l’ordre qu’ils sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="c8697-139">Configuration sources are read in the order they are specified.</span></span> <span data-ttu-id="c8697-140">Dans le code ci-dessus, les variables d’environnement sont lus en dernier.</span><span class="sxs-lookup"><span data-stu-id="c8697-140">In the code above, the environment variables are read last.</span></span> <span data-ttu-id="c8697-141">Les valeurs de configuration définies par l’environnement remplacez celles définies dans les deux fournisseurs précédentes.</span><span class="sxs-lookup"><span data-stu-id="c8697-141">Any configuration values set through the environment would replace those set in the two previous providers.</span></span>

<span data-ttu-id="c8697-142">L’environnement est généralement défini à une des `Development`, `Staging`, ou `Production`.</span><span class="sxs-lookup"><span data-stu-id="c8697-142">The environment is typically set to one of `Development`, `Staging`, or `Production`.</span></span> <span data-ttu-id="c8697-143">Consultez [fonctionne avec plusieurs environnements](environments.md) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="c8697-143">See [Working with Multiple Environments](environments.md) for more information.</span></span>

<span data-ttu-id="c8697-144">Considérations relatives à la configuration :</span><span class="sxs-lookup"><span data-stu-id="c8697-144">Configuration considerations:</span></span>

* <span data-ttu-id="c8697-145">`IOptionsSnapshot`pouvez recharger les données de configuration quand il change.</span><span class="sxs-lookup"><span data-stu-id="c8697-145">`IOptionsSnapshot` can reload configuration data when it changes.</span></span> <span data-ttu-id="c8697-146">Utilisez `IOptionsSnapshot` si vous avez besoin recharger les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="c8697-146">Use `IOptionsSnapshot` if you need to reload configuration data.</span></span>  <span data-ttu-id="c8697-147">Consultez [IOptionsSnapshot](#ioptionssnapshot) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="c8697-147">See [IOptionsSnapshot](#ioptionssnapshot) for more information.</span></span>
* <span data-ttu-id="c8697-148">Clés de configuration sont en minuscules.</span><span class="sxs-lookup"><span data-stu-id="c8697-148">Configuration keys are case insensitive.</span></span>
* <span data-ttu-id="c8697-149">Il est recommandé est pour spécifier des variables d’environnement, afin que l’environnement local peut substituer tout élément défini dans les fichiers de configuration déployée.</span><span class="sxs-lookup"><span data-stu-id="c8697-149">A best practice is to specify environment variables last, so that the local environment can override anything set in deployed configuration files.</span></span>
* <span data-ttu-id="c8697-150">**Jamais** stocker les mots de passe ou d’autres données sensibles dans le code de fournisseur de configuration ou dans les fichiers de configuration en texte brut.</span><span class="sxs-lookup"><span data-stu-id="c8697-150">**Never** store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="c8697-151">N’utilisez des secrets de fabrication dans votre développement ou environnements de test.</span><span class="sxs-lookup"><span data-stu-id="c8697-151">Don't use production secrets in your development or test environments.</span></span> <span data-ttu-id="c8697-152">Au lieu de cela, spécifiez les clés secrètes à l’extérieur de l’arborescence du projet, afin qu’ils ne peuvent pas être validées accidentellement dans votre référentiel.</span><span class="sxs-lookup"><span data-stu-id="c8697-152">Instead, specify secrets outside the project tree, so they cannot be accidentally committed into your repository.</span></span> <span data-ttu-id="c8697-153">En savoir plus sur [fonctionne avec plusieurs environnements](environments.md) et la gestion des [stockage sécurisé des secrets d’application pendant le développement](../security/app-secrets.md).</span><span class="sxs-lookup"><span data-stu-id="c8697-153">Learn more about [Working with Multiple Environments](environments.md) and managing [safe storage of app secrets during development](../security/app-secrets.md).</span></span>
* <span data-ttu-id="c8697-154">Si `:` ne peut pas être utilisé dans les variables d’environnement dans votre système, remplacez `:` avec `__` (double trait de soulignement).</span><span class="sxs-lookup"><span data-stu-id="c8697-154">If `:` cannot be used in environment variables in your system,  replace `:`  with `__` (double underscore).</span></span>

<a name=options-config-objects></a>

## <a name="using-options-and-configuration-objects"></a><span data-ttu-id="c8697-155">À l’aide des Options et des objets de configuration</span><span class="sxs-lookup"><span data-stu-id="c8697-155">Using Options and configuration objects</span></span>

<span data-ttu-id="c8697-156">Le modèle d’options utilise les classes d’options personnalisées pour représenter un groupe de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="c8697-156">The options pattern uses custom options classes to represent a group of related settings.</span></span> <span data-ttu-id="c8697-157">Nous vous recommandons de créer découplée de classes pour chaque fonctionnalité dans votre application.</span><span class="sxs-lookup"><span data-stu-id="c8697-157">We recommended that you create decoupled classes for each feature within your app.</span></span> <span data-ttu-id="c8697-158">Classes de découplée suivent :</span><span class="sxs-lookup"><span data-stu-id="c8697-158">Decoupled classes follow:</span></span>

* <span data-ttu-id="c8697-159">Le [principe de répartition d’Interface (ISP)](http://deviq.com/interface-segregation-principle/) : Classes dépendent uniquement les paramètres de configuration qu’ils utilisent.</span><span class="sxs-lookup"><span data-stu-id="c8697-159">The [Interface Segregation Principle (ISP)](http://deviq.com/interface-segregation-principle/) : Classes depend only on the configuration settings they use.</span></span>
* <span data-ttu-id="c8697-160">[Séparation des préoccupations](http://deviq.com/separation-of-concerns/) : paramètres pour les différentes parties de votre application ne sont pas dépendants ou associés à un autre.</span><span class="sxs-lookup"><span data-stu-id="c8697-160">[Separation of Concerns](http://deviq.com/separation-of-concerns/) : Settings for different parts of your app are not dependent or coupled with one another.</span></span>

<span data-ttu-id="c8697-161">La classe d’options doit être non abstrait avec un constructeur sans paramètre public.</span><span class="sxs-lookup"><span data-stu-id="c8697-161">The options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="c8697-162">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c8697-162">For example:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MyOptions.cs)]

<a name=options-example></a>

<span data-ttu-id="c8697-163">Dans le code suivant, le fournisseur de configuration JSON est activé.</span><span class="sxs-lookup"><span data-stu-id="c8697-163">In the following code, the JSON configuration provider is enabled.</span></span> <span data-ttu-id="c8697-164">La `MyOptions` classe est ajoutée au conteneur de service et lié à la configuration.</span><span class="sxs-lookup"><span data-stu-id="c8697-164">The `MyOptions` class is added to the service container and bound to configuration.</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-21)]

<span data-ttu-id="c8697-165">Les éléments suivants [contrôleur](../mvc/controllers/index.md) utilise [constructeur Injection de dépendance](xref:fundamentals/dependency-injection#what-is-dependency-injection) sur [ `IOptions<TOptions>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) pour accéder aux paramètres :</span><span class="sxs-lookup"><span data-stu-id="c8697-165">The following [controller](../mvc/controllers/index.md)  uses [constructor Dependency Injection](xref:fundamentals/dependency-injection#what-is-dependency-injection) on [`IOptions<TOptions>`](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) to access settings:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="c8697-166">Par le code suivant *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="c8697-166">With the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/UsingOptions/appsettings1.json)]

<span data-ttu-id="c8697-167">Le `HomeController.Index` méthode renvoie `option1 = value1_from_json, option2 = 2`.</span><span class="sxs-lookup"><span data-stu-id="c8697-167">The `HomeController.Index` method returns `option1 = value1_from_json, option2 = 2`.</span></span>

<span data-ttu-id="c8697-168">Les applications classiques ne lier l’ensemble de la configuration dans un fichier d’options unique.</span><span class="sxs-lookup"><span data-stu-id="c8697-168">Typical apps won't bind the entire configuration to a single options file.</span></span> <span data-ttu-id="c8697-169">Plus tard, je vais montrer comment utiliser `GetSection` pour lier à une section.</span><span class="sxs-lookup"><span data-stu-id="c8697-169">Later on I'll show how to use `GetSection` to bind to a section.</span></span>

<span data-ttu-id="c8697-170">Dans le code suivant, une seconde `IConfigureOptions<TOptions>` service est ajouté au conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="c8697-170">In the following code, a second `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="c8697-171">Il utilise un délégué pour configurer la liaison avec `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="c8697-171">It uses a delegate to configure the binding with `MyOptions`.</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]

<span data-ttu-id="c8697-172">Vous pouvez ajouter plusieurs fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="c8697-172">You can add multiple configuration providers.</span></span> <span data-ttu-id="c8697-173">Fournisseurs de configuration sont disponibles dans les packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="c8697-173">Configuration providers are available in NuGet packages.</span></span> <span data-ttu-id="c8697-174">Elles sont appliquées dans l’ordre qu’ils sont inscrits.</span><span class="sxs-lookup"><span data-stu-id="c8697-174">They are applied in order they are registered.</span></span>

<span data-ttu-id="c8697-175">Chaque appel à `Configure<TOptions>` ajoute un `IConfigureOptions<TOptions>` service au conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="c8697-175">Each call to `Configure<TOptions>` adds an `IConfigureOptions<TOptions>` service to the service container.</span></span> <span data-ttu-id="c8697-176">Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont tous deux spécifiés dans *appsettings.json* --mais la valeur de `Option1` est remplacée par le délégué configuré.</span><span class="sxs-lookup"><span data-stu-id="c8697-176">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* -- but the value of `Option1` is overridden by the configured delegate.</span></span> 

<span data-ttu-id="c8697-177">Lorsque plusieurs services de configuration est activée, la dernière configuration la source spécifiée « remporte » (qui définit la valeur de configuration).</span><span class="sxs-lookup"><span data-stu-id="c8697-177">When more than one configuration service is enabled, the last configuration source specified "wins" (sets the configuration value).</span></span> <span data-ttu-id="c8697-178">Dans le code précédent, le `HomeController.Index` méthode renvoie `option1 = value1_from_action, option2 = 2`.</span><span class="sxs-lookup"><span data-stu-id="c8697-178">In the preceding code, the `HomeController.Index` method returns `option1 = value1_from_action, option2 = 2`.</span></span>

<span data-ttu-id="c8697-179">Lorsque vous liez des options de configuration, chaque propriété dans votre type d’options est liée à une clé de configuration sous la forme `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="c8697-179">When you bind options to configuration, each property in your options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="c8697-180">Par exemple, le `MyOptions.Option1` propriété est liée à la clé `Option1`, qui est lu à partir du `option1` propriété dans *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c8697-180">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span> <span data-ttu-id="c8697-181">Un exemple de sous-propriété est présenté plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="c8697-181">A sub-property sample is shown later in this article.</span></span>

<span data-ttu-id="c8697-182">Dans le code suivant, une troisième `IConfigureOptions<TOptions>` service est ajouté au conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="c8697-182">In the following code, a third `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="c8697-183">Il lie `MySubOptions` à la section `subsection` de la *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="c8697-183">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="c8697-184">Remarque : Cette méthode d’extension nécessite le `Microsoft.Extensions.Options.ConfigurationExtensions` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="c8697-184">Note: This extension method requires the `Microsoft.Extensions.Options.ConfigurationExtensions` NuGet package.</span></span>

<span data-ttu-id="c8697-185">À l’aide de ce qui suit *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="c8697-185">Using the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/UsingOptions/appsettings.json)]

<span data-ttu-id="c8697-186">La `MySubOptions` classe :</span><span class="sxs-lookup"><span data-stu-id="c8697-186">The `MySubOptions` class:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="c8697-187">Par le code suivant `Controller`:</span><span class="sxs-lookup"><span data-stu-id="c8697-187">With the following `Controller`:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]

<span data-ttu-id="c8697-188">`subOption1 = subvalue1_from_json, subOption2 = 200`est retourné.</span><span class="sxs-lookup"><span data-stu-id="c8697-188">`subOption1 = subvalue1_from_json, subOption2 = 200` is returned.</span></span>

<span data-ttu-id="c8697-189">Vous pouvez également fournir des options dans un modèle d’affichage ou injecter `IOptions<TOptions>` directement dans une vue :</span><span class="sxs-lookup"><span data-stu-id="c8697-189">You can also supply options in a view model or inject `IOptions<TOptions>` directly into a view:</span></span>

[!code-html[Main](configuration/sample/UsingOptions/Views/Home/Index.cshtml?highlight=3-4,16-17,20-21)]

<a name=in-memory-provider></a>

## <a name="ioptionssnapshot"></a><span data-ttu-id="c8697-190">IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="c8697-190">IOptionsSnapshot</span></span>

<span data-ttu-id="c8697-191">*Nécessite ASP.NET Core 1.1 ou une version ultérieure.*</span><span class="sxs-lookup"><span data-stu-id="c8697-191">*Requires ASP.NET Core 1.1 or higher.*</span></span>

<span data-ttu-id="c8697-192">`IOptionsSnapshot`prend en charge le rechargement des données de configuration lorsque le fichier de configuration a été modifiée.</span><span class="sxs-lookup"><span data-stu-id="c8697-192">`IOptionsSnapshot` supports reloading configuration data when the configuration file has changed.</span></span> <span data-ttu-id="c8697-193">Il a une surcharge minimale.</span><span class="sxs-lookup"><span data-stu-id="c8697-193">It has minimal overhead.</span></span> <span data-ttu-id="c8697-194">À l’aide de `IOptionsSnapshot` avec `reloadOnChange: true`, les options sont liées aux `IConfiguration` rechargées en cas de modification.</span><span class="sxs-lookup"><span data-stu-id="c8697-194">Using `IOptionsSnapshot` with `reloadOnChange: true`, the options are bound to `IConfiguration` and reloaded when changed.</span></span>

<span data-ttu-id="c8697-195">L’exemple suivant montre comment un nouveau `IOptionsSnapshot` est créée après *config.json* modifications.</span><span class="sxs-lookup"><span data-stu-id="c8697-195">The following sample demonstrates how a new `IOptionsSnapshot` is created after *config.json* changes.</span></span> <span data-ttu-id="c8697-196">Les demandes vers le serveur retournera la même heure *config.json* a **pas** modifié.</span><span class="sxs-lookup"><span data-stu-id="c8697-196">Requests to the server will return the same time when *config.json* has **not** changed.</span></span> <span data-ttu-id="c8697-197">La première demande après *config.json* les modifications prendront effet une nouvelle heure.</span><span class="sxs-lookup"><span data-stu-id="c8697-197">The first request after *config.json* changes will show a new time.</span></span>

[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]

<span data-ttu-id="c8697-198">L’illustration suivante montre le résultat du serveur :</span><span class="sxs-lookup"><span data-stu-id="c8697-198">The following image shows the server output:</span></span>

![affichage d’image de navigateur « dernière mise à jour : 22/11/2016 16 h 43 »](configuration/_static/first.png)

<span data-ttu-id="c8697-200">Actualiser le navigateur ne change pas la valeur de message ou l’heure affichée (lorsque *config.json* n’a pas changé).</span><span class="sxs-lookup"><span data-stu-id="c8697-200">Refreshing the browser doesn't change the message value or time displayed (when *config.json* has not changed).</span></span>

<span data-ttu-id="c8697-201">Modifier et enregistrer le *config.json* , puis actualisez le navigateur :</span><span class="sxs-lookup"><span data-stu-id="c8697-201">Change and save the  *config.json* and then refresh the browser:</span></span>

![affichage d’image de navigateur « dernière mise à jour à synchroniser : 22/11/2016 4:53 PM »](configuration/_static/change.png)

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a><span data-ttu-id="c8697-203">Fournisseur en mémoire et la liaison à une classe POCO</span><span class="sxs-lookup"><span data-stu-id="c8697-203">In-memory provider and binding to a POCO class</span></span>

<span data-ttu-id="c8697-204">L’exemple suivant montre comment utiliser le fournisseur en mémoire et le lier à une classe :</span><span class="sxs-lookup"><span data-stu-id="c8697-204">The following sample shows how to use the in-memory provider and bind to a class:</span></span>

[!code-csharp[Main](configuration/sample/InMemory/Program.cs)]

<span data-ttu-id="c8697-205">Les valeurs de configuration sont renvoyées sous forme de chaînes, mais la liaison permet la construction d’objets.</span><span class="sxs-lookup"><span data-stu-id="c8697-205">Configuration values are returned as strings, but binding enables the construction of objects.</span></span> <span data-ttu-id="c8697-206">Liaison vous permet de récupérer les objets POCO ou des graphiques d’objets complets.</span><span class="sxs-lookup"><span data-stu-id="c8697-206">Binding allows you to retrieve POCO objects or even entire object graphs.</span></span> <span data-ttu-id="c8697-207">L’exemple suivant montre comment lier à `MyWindow` et utiliser le modèle d’options avec une application ASP.NET MVC de base :</span><span class="sxs-lookup"><span data-stu-id="c8697-207">The following sample shows how to bind to `MyWindow` and use the options pattern with a ASP.NET Core MVC app:</span></span>

[!code-csharp[Main](configuration/sample/WebConfigBind/MyWindow.cs)]

[!code-json[Main](configuration/sample/WebConfigBind/appsettings.json)]

<span data-ttu-id="c8697-208">Lier la classe personnalisée dans `ConfigureServices` lors de la création de l’ordinateur hôte :</span><span class="sxs-lookup"><span data-stu-id="c8697-208">Bind the custom class in `ConfigureServices` when building the host:</span></span>

[!code-csharp[Main](configuration/sample/WebConfigBind/Program.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="c8697-209">Afficher les paramètres à partir de la `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="c8697-209">Display the settings from the `HomeController`:</span></span>

[!code-csharp[Main](configuration/sample/WebConfigBind/Controllers/HomeController.cs)]

### <a name="getvalue"></a><span data-ttu-id="c8697-210">GetValue</span><span class="sxs-lookup"><span data-stu-id="c8697-210">GetValue</span></span>

<span data-ttu-id="c8697-211">L’exemple suivant montre comment la [GetValue<T> ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="c8697-211">The following sample demonstrates the [GetValue<T>](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) extension method:</span></span>

[!code-csharp[Main](configuration/sample/InMemoryGetValue/Program.cs?highlight=27-29)]

<span data-ttu-id="c8697-212">De la ConfigurationBinder `GetValue<T>` méthode vous permet de spécifier une valeur par défaut (80 dans l’exemple).</span><span class="sxs-lookup"><span data-stu-id="c8697-212">The ConfigurationBinder's `GetValue<T>` method allows you to specify a default value (80 in the sample).</span></span> <span data-ttu-id="c8697-213">`GetValue<T>`pour des scénarios simples et n’est pas lié à des sections entières.</span><span class="sxs-lookup"><span data-stu-id="c8697-213">`GetValue<T>` is for simple scenarios and does not bind to entire sections.</span></span> <span data-ttu-id="c8697-214">`GetValue<T>`Obtient les valeurs scalaires de `GetSection(key).Value` converti en un type spécifique.</span><span class="sxs-lookup"><span data-stu-id="c8697-214">`GetValue<T>` gets scalar values from `GetSection(key).Value` converted to a specific type.</span></span>

## <a name="binding-to-an-object-graph"></a><span data-ttu-id="c8697-215">Liaison à un graphique d’objet</span><span class="sxs-lookup"><span data-stu-id="c8697-215">Binding to an object graph</span></span>

<span data-ttu-id="c8697-216">Vous pouvez lier de manière récursive à chaque objet dans une classe.</span><span class="sxs-lookup"><span data-stu-id="c8697-216">You can recursively bind to each object in a class.</span></span> <span data-ttu-id="c8697-217">Considérez les éléments suivants `AppOptions` classe :</span><span class="sxs-lookup"><span data-stu-id="c8697-217">Consider the following `AppOptions` class:</span></span>

[!code-csharp[Main](configuration/sample/ObjectGraph/AppOptions.cs)]

<span data-ttu-id="c8697-218">L’exemple suivant crée une liaison avec la `AppOptions` classe :</span><span class="sxs-lookup"><span data-stu-id="c8697-218">The following sample binds to the `AppOptions` class:</span></span>

[!code-csharp[Main](configuration/sample/ObjectGraph/Program.cs?highlight=15-16)]

<span data-ttu-id="c8697-219">**ASP.NET Core 1.1** et utiliser une valeur supérieure `Get<T>`, qui fonctionne avec des sections entières.</span><span class="sxs-lookup"><span data-stu-id="c8697-219">**ASP.NET Core 1.1** and higher can use  `Get<T>`, which works with entire sections.</span></span> <span data-ttu-id="c8697-220">`Get<T>`peut être plus convienent que l’utilisation de `Bind`.</span><span class="sxs-lookup"><span data-stu-id="c8697-220">`Get<T>` can be more convienent than using `Bind`.</span></span> <span data-ttu-id="c8697-221">Le code suivant montre comment utiliser `Get<T>` avec l’exemple ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="c8697-221">The following code shows how to use `Get<T>` with the sample above:</span></span>

```csharp
var appConfig = config.GetSection("App").Get<AppOptions>();
```

<span data-ttu-id="c8697-222">À l’aide de ce qui suit *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="c8697-222">Using the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/ObjectGraph/appsettings.json)]

<span data-ttu-id="c8697-223">Le programme affiche `Height 11`.</span><span class="sxs-lookup"><span data-stu-id="c8697-223">The program displays `Height 11`.</span></span>

<span data-ttu-id="c8697-224">Le code suivant peut être utilisé pour l’unité la configuration de test :</span><span class="sxs-lookup"><span data-stu-id="c8697-224">The following code can be used to unit test the configuration:</span></span>

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

## <a name="basic-sample-of-entity-framework-custom-provider"></a><span data-ttu-id="c8697-225">Exemple de base du fournisseur personnalisé de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="c8697-225">Basic sample of Entity Framework custom provider</span></span>

<span data-ttu-id="c8697-226">Dans cette section, un fournisseur de configuration de base qui lit les paires nom-valeur à partir d’une base de données à l’aide de EF est créé.</span><span class="sxs-lookup"><span data-stu-id="c8697-226">In this section, a basic configuration provider that reads name-value pairs from a database using EF is created.</span></span> 

<span data-ttu-id="c8697-227">Définir un `ConfigurationValue` entité pour le stockage des valeurs de configuration dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="c8697-227">Define a `ConfigurationValue` entity for storing configuration values in the database:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationValue.cs)]

<span data-ttu-id="c8697-228">Ajouter un `ConfigurationContext` pour stocker et accéder aux valeurs configurées :</span><span class="sxs-lookup"><span data-stu-id="c8697-228">Add a `ConfigurationContext` to store and access the configured values:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="c8697-229">Créez une classe qui implémente [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span><span class="sxs-lookup"><span data-stu-id="c8697-229">Create an class that implements [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]

<span data-ttu-id="c8697-230">Créer le fournisseur de configuration personnalisé en héritant de [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider).</span><span class="sxs-lookup"><span data-stu-id="c8697-230">Create the custom configuration provider by inheriting from [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider).</span></span>  <span data-ttu-id="c8697-231">Le fournisseur de configuration initialise la base de données lorsqu’elle est vide :</span><span class="sxs-lookup"><span data-stu-id="c8697-231">The configuration provider initializes the database when it's empty:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]

<span data-ttu-id="c8697-232">Les valeurs en surbrillance à partir de la base de données (« value_from_ef_1 » et « value_from_ef_2 ») sont affichées lors de l’exemple est exécuté.</span><span class="sxs-lookup"><span data-stu-id="c8697-232">The highlighted values from the database ("value_from_ef_1" and "value_from_ef_2") are displayed when the sample is run.</span></span>

<span data-ttu-id="c8697-233">Vous pouvez ajouter un `EFConfigSource` méthode d’extension pour l’ajout de la source de configuration :</span><span class="sxs-lookup"><span data-stu-id="c8697-233">You can add an `EFConfigSource` extension method for adding the configuration source:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]

<span data-ttu-id="c8697-234">Le code suivant montre comment utiliser personnalisée `EFConfigProvider`:</span><span class="sxs-lookup"><span data-stu-id="c8697-234">The following code shows how to use the custom `EFConfigProvider`:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]

<span data-ttu-id="c8697-235">Notez que l’exemple ajoute personnalisé `EFConfigProvider` lorsque le fournisseur JSON, par conséquent, tous les paramètres de la base de données remplace les paramètres à partir de la *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="c8697-235">Note the sample adds the custom `EFConfigProvider` after the JSON provider, so any settings from the database will override settings from the *appsettings.json* file.</span></span>

<span data-ttu-id="c8697-236">À l’aide de ce qui suit *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="c8697-236">Using the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/CustomConfigurationProvider/appsettings.json)]

<span data-ttu-id="c8697-237">Affiche les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c8697-237">The following is displayed:</span></span>

```console
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

## <a name="commandline-configuration-provider"></a><span data-ttu-id="c8697-238">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="c8697-238">CommandLine configuration provider</span></span>

<span data-ttu-id="c8697-239">L’exemple suivant active le fournisseur de configuration de ligne de commande dernière :</span><span class="sxs-lookup"><span data-stu-id="c8697-239">The following sample enables the CommandLine configuration provider last:</span></span>

[!code-csharp[Main](configuration/sample/CommandLine/Program.cs)]

<span data-ttu-id="c8697-240">Passer des paramètres de configuration, utilisez les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="c8697-240">Use the following to pass in configuration settings:</span></span>

```console
dotnet run /Profile:MachineName=Bob /App:MainWindow:Left=1234
```

<span data-ttu-id="c8697-241">Qui s’affiche :</span><span class="sxs-lookup"><span data-stu-id="c8697-241">Which displays:</span></span>

```console
Hello Bob
Left 1234
```

<span data-ttu-id="c8697-242">Le `GetSwitchMappings` méthode vous permet d’utiliser `-` plutôt que `/` et il supprime les préfixes de sous-clés au début.</span><span class="sxs-lookup"><span data-stu-id="c8697-242">The `GetSwitchMappings` method allows you to use `-` rather than `/` and it strips the leading subkey prefixes.</span></span> <span data-ttu-id="c8697-243">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c8697-243">For example:</span></span>

```console
dotnet run -MachineName=Bob -Left=7734
```

<span data-ttu-id="c8697-244">Affiche :</span><span class="sxs-lookup"><span data-stu-id="c8697-244">Displays:</span></span>

```console
Hello Bob
Left 7734
```

<span data-ttu-id="c8697-245">Arguments de ligne de commande doivent inclure une valeur (il peut être null).</span><span class="sxs-lookup"><span data-stu-id="c8697-245">Command-line arguments must include a value (it can be null).</span></span> <span data-ttu-id="c8697-246">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c8697-246">For example:</span></span>

```console
dotnet run /Profile:MachineName=
```

<span data-ttu-id="c8697-247">Est OK, mais</span><span class="sxs-lookup"><span data-stu-id="c8697-247">Is OK, but</span></span>

```console
dotnet run /Profile:MachineName
```

<span data-ttu-id="c8697-248">génère une exception.</span><span class="sxs-lookup"><span data-stu-id="c8697-248">results in an exception.</span></span> <span data-ttu-id="c8697-249">Une exception est levée si vous spécifiez un préfixe de commutateur de ligne de commande - ou--pour lequel il n’existe aucun mappage de commutateur correspondant.</span><span class="sxs-lookup"><span data-stu-id="c8697-249">An exception will be thrown if you specify a command-line switch prefix of - or -- for which there's no corresponding switch mapping.</span></span>

## <a name="the-webconfig-file"></a><span data-ttu-id="c8697-250">Le fichier web.config</span><span class="sxs-lookup"><span data-stu-id="c8697-250">The web.config file</span></span>

<span data-ttu-id="c8697-251">A *web.config* fichier est requis lorsque vous hébergez l’application dans IIS ou IIS Express.</span><span class="sxs-lookup"><span data-stu-id="c8697-251">A *web.config* file is required when you host the app in IIS or IIS-Express.</span></span> <span data-ttu-id="c8697-252">*Web.config* Active le AspNetCoreModule dans IIS pour lancer votre application.</span><span class="sxs-lookup"><span data-stu-id="c8697-252">*web.config* turns on the AspNetCoreModule in IIS to launch your app.</span></span> <span data-ttu-id="c8697-253">Paramètres de *web.config* activer le AspNetCoreModule dans IIS pour lancer votre application et de configurer d’autres modules et les paramètres IIS.</span><span class="sxs-lookup"><span data-stu-id="c8697-253">Settings in *web.config* enable the AspNetCoreModule in IIS to launch your app and configure other IIS settings and modules.</span></span> <span data-ttu-id="c8697-254">Si vous utilisez Visual Studio, supprimez *web.config*, Visual Studio crée un nouveau.</span><span class="sxs-lookup"><span data-stu-id="c8697-254">If you are using Visual Studio and delete *web.config*, Visual Studio will create a new one.</span></span>

### <a name="additional-notes"></a><span data-ttu-id="c8697-255">Remarques supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c8697-255">Additional notes</span></span>

* <span data-ttu-id="c8697-256">Injection de dépendance (DI) n’est pas définie jusqu'à après `ConfigureServices` est appelé.</span><span class="sxs-lookup"><span data-stu-id="c8697-256">Dependency Injection (DI) is not set up until after `ConfigureServices` is invoked.</span></span>
* <span data-ttu-id="c8697-257">Le système de configuration n’est pas DI prenant en charge.</span><span class="sxs-lookup"><span data-stu-id="c8697-257">The configuration system is not DI aware.</span></span>
* <span data-ttu-id="c8697-258">`IConfiguration`a deux spécialisations :</span><span class="sxs-lookup"><span data-stu-id="c8697-258">`IConfiguration` has two specializations:</span></span>
  * <span data-ttu-id="c8697-259">`IConfigurationRoot`Utilisé pour le nœud racine.</span><span class="sxs-lookup"><span data-stu-id="c8697-259">`IConfigurationRoot`  Used for the root node.</span></span> <span data-ttu-id="c8697-260">Peut déclencher un rechargement.</span><span class="sxs-lookup"><span data-stu-id="c8697-260">Can trigger a reload.</span></span>
  * <span data-ttu-id="c8697-261">`IConfigurationSection`Représente une section de valeurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="c8697-261">`IConfigurationSection`  Represents a section of configuration values.</span></span> <span data-ttu-id="c8697-262">Le `GetSection` et `GetChildren` méthodes retournent un `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="c8697-262">The `GetSection` and `GetChildren` methods return an `IConfigurationSection`.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="c8697-263">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c8697-263">Additional resources</span></span>

* [<span data-ttu-id="c8697-264">Utilisation de plusieurs environnements</span><span class="sxs-lookup"><span data-stu-id="c8697-264">Working with Multiple Environments</span></span>](environments.md)
* [<span data-ttu-id="c8697-265">Stockage sécurisé des secrets de l’application durant le développement</span><span class="sxs-lookup"><span data-stu-id="c8697-265">Safe storage of app secrets during development</span></span>](../security/app-secrets.md)
* [<span data-ttu-id="c8697-266">Injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="c8697-266">Dependency Injection</span></span>](dependency-injection.md)
* [<span data-ttu-id="c8697-267">Fournisseur de configuration Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c8697-267">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)

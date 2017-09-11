---
title: "Migration d’ASP.NET vers ASP.NET Core 2.0"
author: isaac2004
description: "Ce document de référence fournit de l’aide sur la migration d’applications ASP.NET MVC ou API web ASP.NET existantes vers ASP.NET Core 2.0."
keywords: ASP.NET Core,MVC,migration
ms.author: scaddie
manager: wpickett
ms.date: 08/27/2017
ms.topic: article
ms.assetid: 3155cc9e-d0c9-424b-886c-35c0ec6f9f4e
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/proper-to-2x/index
ms.openlocfilehash: b9170878e4797c729a94caa47c045c3ca3a3d9b8
ms.sourcegitcommit: 4693cb02d845adf2efa00e07ad432c81867bfa12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2017
---
# <a name="migrating-from-aspnet-to-aspnet-core-20"></a>Migration d’ASP.NET vers ASP.NET Core 2.0

De [Isaac Levin](https://isaaclevin.com)

Cet article sert de guide de référence pour la migration d’applications ASP.NET vers ASP.NET Core 2.0.

## <a name="prerequisites"></a>Conditions préalables

* [Kit .NET Core 2.0.0 SDK](https://dot.net/core) ou ultérieur.

## <a name="target-frameworks"></a>Versions cibles de .NET Framework
Les projets ASP.NET Core 2.0 permettent aux développeurs de cibler le .NET Core, le .NET Framework ou les deux. Consultez [Choix entre le .NET Core et le .NET Framework pour les applications serveur](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) afin de déterminer quel est le framework cible le plus approprié.

Quand vous ciblez le .NET Framework, les projets doivent référencer des packages NuGet individuels.

Le ciblage du .NET Core vous permet d’éliminer de nombreuses références de packages explicites, grâce au [métapackage](xref:fundamentals/metapackage) ASP.NET Core 2.0. Installez le métapackage `Microsoft.AspNetCore.All` dans votre projet :

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
</ItemGroup>
```

Quand le métapackage est utilisé, aucun package référencé dans le métapackage n’est déployé avec l’application. Le magasin de runtimes du .NET Core inclut ces composants. Ceux-ci sont précompilés pour améliorer les performances. Pour plus d’informations, consultez [Métapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.x](xref:fundamentals/metapackage).

## <a name="project-structure-differences"></a>Différences de structure de projet
Le format de fichier *.csproj* a été simplifié dans ASP.NET Core. Voici certains changements notables :
- Il n’est pas nécessaire d’inclure explicitement les fichiers pour qu’ils soient considérés comme faisant partie du projet. Cela réduit le risque de conflits de fusion XML quand vous travaillez avec des équipes de grande taille.
- Il n’existe aucune référence basée sur un GUID à d’autres projets, ce qui améliore la lisibilité du fichier.
- Vous pouvez modifier le fichier sans devoir le décharger dans Visual Studio :

    ![Option de menu contextuel de modification du CSPROJ dans Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Remplacement du fichier Global.asax
ASP.NET Core a introduit un nouveau mécanisme pour le démarrage d’une application. Le point d’entrée des applications ASP.NET est le fichier *Global.asax*. Les tâches telles que la configuration de l’itinéraire ou l’inscription des filtres et des zones sont traitées dans le fichier *Global.asax*.

[!code-csharp[Main](samples/globalasax-sample.cs)]

Cette approche couple l’application au serveur sur lequel elle est déployée d’une manière qui interfère avec l’implémentation. Pour y remédier, [OWIN](http://owin.org/) a donc été introduit afin d’optimiser l’utilisation de plusieurs frameworks à la fois. OWIN fournit un pipeline qui permet d’ajouter uniquement les modules nécessaires. L’environnement d’hébergement utilise une fonction [Startup](xref:fundamentals/startup) pour configurer les services et le pipeline de requêtes de l’application. `Startup` inscrit un ensemble d’intergiciels (middleware) auprès de l’application. Pour chaque requête, l’application appelle chacun des composants intergiciels (middleware) à l’aide du pointeur d’en-tête d’une liste liée à un ensemble existant de gestionnaires. Chaque composant intergiciel (middleware) peut ajouter un ou plusieurs gestionnaires au pipeline de traitement des requêtes. Pour ce faire, une référence doit être retournée au gestionnaire qui représente le nouvel en-tête de la liste. Chaque gestionnaire doit mémoriser et appeler le prochain gestionnaire de la liste. Avec ASP.NET Core, comme le point d’entrée d’une application est `Startup`, vous n’avez plus de dépendance relative à *Global.asax*. Quand vous employez OWIN avec le .NET Framework, utilisez l’exemple de code suivant en tant que pipeline :

[!code-csharp[Main](samples/webapi-owin.cs)]

Cela permet de configurer vos itinéraires par défaut, et de privilégier XmlSerialization à JSON. Ajoutez d’autres intergiciels (middleware) à ce pipeline selon les besoins (services de chargement, paramètres de configuration, fichiers statiques, etc.).

ASP.NET Core utilise une approche similaire mais n’a pas besoin d’OWIN pour prendre en charge l’entrée. À la place, l’opération est effectuée via la méthode `Main` de *Program.cs* (un peu comme pour les applications console) et `Startup` est chargé à partir de là.

[!code-csharp[Main](samples/program.cs)]

`Startup` doit inclure une méthode `Configure`. Dans `Configure`, ajoutez l’intergiciel (middleware) nécessaire au pipeline. Dans l’exemple suivant (provenant du modèle de site web par défaut), plusieurs méthodes d’extension permettent de configurer le pipeline pour une prise en charge des éléments ci-dessous :

* [BrowserLink](http://vswebessentials.com/features/browserlink)
* Pages d’erreur
* Fichiers statiques
* ASP.NET Core MVC
* Identité

[!code-csharp[Main](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

L’hôte et l’application ont été découplés, ce qui permet de passer facilement plus tard à une autre plateforme.

**Remarque :** Pour obtenir des informations de référence plus approfondies sur le démarrage des applications dans ASP.NET Core et sur les intergiciels (middleware), consultez [Démarrage dans ASP.NET Core](xref:fundamentals/startup)

## <a name="storing-configurations"></a>Stockage des configurations
ASP.NET prend en charge le stockage des paramètres. Ces paramètres sont utilisés, par exemple, pour prendre en charge l’environnement sur lequel les applications ont été déployées. Habituellement, toutes les paires clé-valeur personnalisées sont stockées dans la section `<appSettings>` du fichier *Web.config* :

[!code-xml[Main](samples/webconfig-sample.xml)]

Les applications lisent ces paramètres à l’aide de la collection `ConfigurationManager.AppSettings` dans l’espace de noms `System.Configuration` :

[!code-csharp[Main](samples/read-webconfig.cs)]

ASP.NET Core peut stocker les données de configuration de l’application dans un fichier et les charger dans le cadre du démarrage d’un intergiciel (middleware). Le fichier par défaut utilisé dans les modèles de projet est *appsettings.json* :

[!code-json[Main](samples/appsettings-sample.json)]

Le chargement de ce fichier dans une instance de `IConfiguration` au sein de votre application s’effectue dans *Startup.cs* :

[!code-csharp[Main](samples/startup-builder.cs)]

L’application lit `Configuration` pour obtenir les paramètres :

[!code-csharp[Main](samples/read-appsettings.cs)]

Il existe des extensions à cette approche pour rendre le processus plus robuste, par exemple en utilisant l’[injection de dépendances](xref:fundamentals/dependency-injection) pour charger un service avec ces valeurs. L’approche par injection de dépendances fournit un ensemble d’objets de configuration fortement typés.

````csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
````

**Remarque :** Pour obtenir des informations de référence plus approfondies sur la configuration d’ASP.NET Core, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration).

## <a name="native-dependency-injection"></a>Injection de dépendances native
Quand vous générez des applications majeures et scalables, il est important d’avoir un couplage faible entre les composants et les services. L’[injection de dépendances](xref:fundamentals/dependency-injection) est une technique répandue qui permet d’y parvenir. Elle représente un composant natif d’ASP.NET Core.

Dans les applications ASP.NET, les développeurs s’appuient sur une bibliothèque tierce pour implémenter l’injection de dépendances. L’une de ces bibliothèques, [Unity](https://github.com/unitycontainer/unity), est fournie par Microsoft Patterns & Practices. 

L’implémentation de `IDependencyResolver` qui inclut `UnityContainer` dans un wrapper est un exemple de configuration d’une injection de dépendances avec Unity :

[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample8.cs)]

Créez une instance de `UnityContainer`, inscrivez votre service, puis définissez le programme de résolution de dépendances de `HttpConfiguration` en fonction de la nouvelle instance de `UnityResolver` pour votre conteneur :

[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample9.cs)]

Injectez `IProductRepository` aux emplacements nécessaires :

[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample5.cs)]

Dans la mesure où l’injection de dépendances fait partie d’ASP.NET Core, vous pouvez ajouter votre service à la méthode `ConfigureServices` de *Startup.cs* :

[!code-csharp[Main](samples/configure-services.cs)]

Vous pouvez injecter le dépôt à l’emplacement de votre choix, comme c’était le cas avec Unity.

**Remarque :** Pour obtenir des informations de référence sur l’injection de dépendances dans ASP.NET Core, consultez [Injection de dépendances dans ASP.NET Core](xref:fundamentals/dependency-injection#replacing-the-default-services-container)

## <a name="serving-static-files"></a>Traitement des fichiers statiques
Une partie importante du développement web réside dans la capacité de traitement des composants statiques, côté client. Les fichiers HTML, CSS, JavaScript et image sont les exemples les plus courants de fichiers statiques. Ces fichiers doivent être enregistrés à l’emplacement publié de l’application (ou CDN) et référencés pour pouvoir être chargés par une requête. Ce processus a changé avec ASP.NET Core.

Avec ASP.NET, les fichiers statiques sont stockés dans différents répertoires et référencés dans des vues.

Avec ASP.NET Core, les fichiers statiques sont stockés à la « racine web » (*&lt;racine du contenu&gt;/wwwroot*), sauf si la configuration est différente. Les fichiers sont chargés dans le pipeline de requêtes via l’appel de la méthode d’extension `UseStaticFiles` à partir de `Startup.Configure` :

[!code-csharp[Main](../../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]

**Remarque :** Si vous ciblez le .NET Framework, installez le package NuGet `Microsoft.AspNetCore.StaticFiles`.

Par exemple, un composant image dans le dossier *wwwroot/images* est accessible au navigateur à un emplacement tel que `http://<app>/images/<imageFileName>`.

**Remarque :** Pour obtenir des informations de référence plus approfondies sur le traitement des fichiers statiques dans ASP.NET Core, consultez [Présentation de l’utilisation des fichiers statiques dans ASP.NET Core](xref:fundamentals/static-files).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Portage des bibliothèques vers le .NET Core](https://docs.microsoft.com/dotnet/core/porting/libraries)

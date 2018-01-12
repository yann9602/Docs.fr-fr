---
title: Fournisseurs de fichier dans ASP.NET Core
author: ardalis
description: "Découvrez comment ASP.NET Core résume les accès au système de fichiers via l’utilisation de fournisseurs de fichier."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 1e35d362-0005-4f84-a187-274ca203a787
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/file-providers
ms.openlocfilehash: fd847db992b20ab096b54378418d2b9bccff67be
ms.sourcegitcommit: 2b263e87217658caa42eedc4f9d2d21ef0ab5d59
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="file-providers-in-aspnet-core"></a>Fournisseurs de fichier dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

ASP.NET Core résume l’accès au système de fichiers via l’utilisation de fournisseurs de fichier.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/file-providers/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="file-provider-abstractions"></a>Abstractions de fournisseur de fichier

Les fournisseurs de fichier sont une abstraction sur les systèmes de fichiers. L’interface principale est `IFileProvider`. `IFileProvider`expose des méthodes pour obtenir des informations de fichier (`IFileInfo`), les informations de répertoire (`IDirectoryContents`) et pour configurer des notifications de modification (à l’aide un `IChangeToken`).

`IFileInfo`Fournit des méthodes et propriétés sur des fichiers individuels ou des répertoires. Il a deux propriétés booléennes, `Exists` et `IsDirectory`, ainsi que les propriétés de description du fichier `Name`, `Length` (en octets), et `LastModified` date. Vous pouvez lire à partir du fichier en utilisant son `CreateReadStream` (méthode).

## <a name="file-provider-implementations"></a>Implémentations de fournisseur de fichiers

Trois implémentations de `IFileProvider` sont disponibles : physique, incorporé et Composite. Le fournisseur physique permet d’accéder aux fichiers du système réel. Le fournisseur incorporé est utilisé pour accéder aux fichiers incorporés dans des assemblys. Le fournisseur composite est utilisé pour fournir l’accès aux fichiers et répertoires à partir d’un ou plusieurs fournisseurs.

### <a name="physicalfileprovider"></a>PhysicalFileProvider

Le `PhysicalFileProvider` fournit l’accès au système de fichiers physique. Elle encapsule le `System.IO.File` type (pour le fournisseur physique), l’étendue de tous les chemins d’accès à un répertoire et de ses enfants. Cette portée limite l’accès à un répertoire particulier et ses enfants, ce qui empêche l’accès au système de fichiers en dehors de cette limite. Lors de l’instanciation de ce fournisseur, vous devez fournir un chemin de répertoire qui sert de chemin de base pour toutes les demandes effectuées auprès de ce fournisseur (ce qui limite l’accès en dehors de ce chemin d’accès). Dans une application ASP.NET Core, vous pouvez instancier un `PhysicalFileProvider` fournisseur directement, ou vous pouvez demander un `IFileProvider` dans un contrôleur ou le constructeur du service via [injection de dépendance](dependency-injection.md). Cette dernière approche génère habituellement une solution plus souple et testable.

L’exemple ci-dessous montre comment créer un `PhysicalFileProvider`.


```csharp
IFileProvider provider = new PhysicalFileProvider(applicationRoot);
IDirectoryContents contents = provider.GetDirectoryContents(""); // the applicationRoot contents
IFileInfo fileInfo = provider.GetFileInfo("wwwroot/js/site.js"); // a file under applicationRoot
```

Vous pouvez parcourir le contenu du répertoire ou obtenir des informations d’un fichier spécifique en fournissant un sous-chemin.

Pour demander un fournisseur à partir d’un contrôleur, spécifiez-le dans le constructeur du contrôleur et l’assigner à un champ local. Utilisez l’instance locale de vos méthodes d’action :

[!code-csharp[Main](file-providers/sample/src/FileProviderSample/Controllers/HomeController.cs?highlight=5,7,12&range=6-19)]

Ensuite, créez le fournisseur dans l’application `Startup` classe :

[!code-csharp[Main](file-providers/sample/src/FileProviderSample/Startup.cs?highlight=35,40&range=1-43)]

Dans le *Index.cshtml* afficher, parcourir le `IDirectoryContents` fourni :

[!code-html[Main](file-providers/sample/src/FileProviderSample/Views/Home/Index.cshtml?highlight=2,7,9,11,15)]

Le résultat :

![Fournisseur exemples application liste physique de fichiers et des dossiers de fichiers](file-providers/_static/physical-directory-listing.png)

### <a name="embeddedfileprovider"></a>EmbeddedFileProvider

Le `EmbeddedFileProvider` est utilisé pour accéder aux fichiers incorporés dans des assemblys. Dans le .NET Core, vous incorporez des fichiers dans un assembly avec le `<EmbeddedResource>` élément dans le *.csproj* fichier :

[!code-json[Main](file-providers/sample/src/FileProviderSample/FileProviderSample.csproj?range=13-18)]

Vous pouvez utiliser [les modèles de la globalisation](#globbing-patterns) lors de la spécification des fichiers à incorporer dans l’assembly. Ces modèles peuvent être utilisés pour faire correspondre un ou plusieurs fichiers.

> [!NOTE]
> Il est improbable que vous souhaitez jamais réellement incorporer chaque fichier .js dans votre projet dans l’assembly ; l’exemple ci-dessus est uniquement à des fins de démonstration.

Lorsque vous créez un `EmbeddedFileProvider`, passez l’assembly il lira à son constructeur.

```csharp
var embeddedProvider = new EmbeddedFileProvider(Assembly.GetEntryAssembly());
```

L’extrait de code ci-dessus montre comment créer un `EmbeddedFileProvider` avec accès à l’assembly en cours d’exécution.

Mise à jour de l’exemple d’application à utiliser un `EmbeddedFileProvider` génère la sortie suivante :

![Fichier d’application d’exemple de fournisseur qui répertorie les fichiers incorporés](file-providers/_static/embedded-directory-listing.png)

> [!NOTE]
> Les ressources incorporées n’exposent pas les répertoires. Au lieu de cela, le chemin d’accès à la ressource (par l’intermédiaire de son espace de noms) est incorporé dans sa à l’aide du nom de fichier `.` séparateurs.

> [!TIP]
> Le `EmbeddedFileProvider` constructeur accepte un facultatif `baseNamespace` paramètre. Les appels à la spécification de ce étend sa portée `GetDirectoryContents` à ces ressources sous l’espace de noms fourni.

### <a name="compositefileprovider"></a>CompositeFileProvider

Le `CompositeFileProvider` combine `IFileProvider` instances, l’exposition d’une interface unique pour l’utilisation des fichiers à partir de plusieurs fournisseurs. Lorsque vous créez le `CompositeFileProvider`, vous passez un ou plusieurs `IFileProvider` instances à son constructeur :

[!code-csharp[Main](file-providers/sample/src/FileProviderSample/Startup.cs?highlight=3&range=35-37)]

Mise à jour de l’exemple d’application à utiliser un `CompositeFileProvider` qui inclut les deux fournisseurs physiques et incorporés configurées précédemment, génère la sortie suivante :

![Fichier fournisseur exemple d’application qui répertorie les fichiers physiques et des dossiers et des fichiers incorporés](file-providers/_static/composite-directory-listing.png)

## <a name="watching-for-changes"></a>La surveillance des modifications

Le `IFileProvider` `Watch` méthode offre un moyen d’observer un ou plusieurs fichiers ou répertoires pour les modifications. Cette méthode accepte une chaîne de chemin d’accès, ce qui permet de [les modèles de la globalisation](#globbing-patterns) pour spécifier plusieurs fichiers et retourne un `IChangeToken`. Ce jeton expose un `HasChanged` propriété qui peut être inspectée, et un `RegisterChangeCallback` méthode qui est appelée lorsque des modifications sont détectées dans la chaîne de chemin d’accès spécifié. Notez que chaque jeton de modification appelle uniquement son rappel associé en réponse à une modification. Pour activer une surveillance permanente, vous pouvez utiliser un `TaskCompletionSource` comme indiqué ci-dessous, ou recréez `IChangeToken` instances en réponse aux modifications.

Dans l’exemple de cet article, une application console est configurée pour afficher un message chaque fois qu’un fichier texte est modifié :

[!code-csharp[Main](file-providers/sample/src/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Le résultat, après avoir enregistré le fichier plusieurs fois :

![Fenêtre de commande une fois l’exécution dotnet exécuter montre les modifications du fichier quotes.txt d’analyse des applications et que le fichier a changé de cinq fois.](file-providers/_static/watch-console.png)

> [!NOTE]
> Certains systèmes de fichiers, tels que les conteneurs Docker et les partages réseau, peut ne pas fiable à envoyer des notifications de modification. Définir le `DOTNET_USE_POLLINGFILEWATCHER` variable d’environnement `1` ou `true` pour interroger le système de fichiers pour les modifications à toutes les 4 secondes.

## <a name="globbing-patterns"></a>Modèles de la globalisation

Chemins d’accès de système de fichiers utilisent des modèles à caractère générique appelées *les modèles de la globalisation*. Ces modèles simples peuvent être utilisés pour spécifier les groupes de fichiers. Les deux caractères génériques sont `*` et `**`.

**`*`**

   Correspond à quoi que ce soit au niveau du dossier actuel, ou n’importe quel nom de fichier ou de n’importe quelle extension de fichier. Correspondances sont terminent par `/` et `.` caractères dans le chemin d’accès de fichier.

<strong><code>**</code></strong>

   Met en correspondance n’est pas défini sur plusieurs niveaux de répertoire. Peut être utilisé pour de manière récursive correspond à plusieurs fichiers au sein d’une hiérarchie de répertoires.

### <a name="globbing-pattern-examples"></a>Exemples de modèle de la globalisation

**`directory/file.txt`**

   Correspond à un fichier spécifique dans un répertoire spécifique.

**<code>directory/*.txt</code>**

   Correspond à tous les fichiers avec `.txt` extension dans un répertoire spécifique.

**`directory/*/bower.json`**

   Correspond à toutes les `bower.json` fichiers dans les répertoires exactement un niveau en dessous du `directory` active.

**<code>directory/&#42;&#42;/&#42;.txt</code>**

   Correspond à tous les fichiers avec `.txt` extension se trouve sous le `directory` active.

## <a name="file-provider-usage-in-aspnet-core"></a>L’utilisation du fournisseur de fichiers dans ASP.NET Core

Plusieurs parties d’ASP.NET Core utilisent des fournisseurs de fichier. `IHostingEnvironment`expose la racine du contenu de l’application et la racine web en tant que `IFileProvider` types. L’intergiciel (middleware) des fichiers statiques utilise des fournisseurs de fichier pour localiser les fichiers statiques. Razor utilise de façon intensive `IFileProvider` pour la localisation de vues. Dotnet publie des fournisseurs de fichier utilise des fonctionnalités et les modèles de la globalisation pour spécifier les fichiers qui doivent être publiés.

## <a name="recommendations-for-use-in-apps"></a>Recommandations pour une utilisation dans les applications

Si votre application ASP.NET Core nécessite l’accès au système de fichiers, vous pouvez demander une instance de `IFileProvider` par injection de dépendance, puis utilisez ses méthodes pour y accéder, comme illustré dans cet exemple. Cela vous permet de vous permet de configurer le fournisseur d’une seule fois, lorsque l’application démarre et réduit le nombre de types d’implémentation instancie de votre application.

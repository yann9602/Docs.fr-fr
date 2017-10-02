---
title: "Composants d’application ASP.NET Core"
author: ardalis
description: "Découvrez comment utiliser des parties de l’application, qui sont abstrations sur les ressources d’une application, pour configurer votre application pour découvrir ou d’éviter les fonctionnalités de chargement à partir d’un assembly."
keywords: "ASP.NET Core, partie de l’application, de la part de l’application"
ms.author: riande
manager: wpickett
ms.date: 01/04/2017
ms.topic: article
ms.assetid: b355a48e-a15c-4d58-b69c-899963613a98
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/extensibility/app-parts
ms.openlocfilehash: a260675e7461105d4f6a0c61fd13971663c268f2
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2017
---
# <a name="application-parts-in-aspnet-core"></a>Composants d’application ASP.NET Core

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))

Un *partie Application* est une abstraction sur les ressources d’une application, à partir de laquelle les fonctionnalités de MVC tels que les contrôleurs, les composants de la vue, ou programmes d’assistance de balise peuvent être découvertes. Un exemple d’une partie de l’application est un AssemblyPart, qui encapsule une référence d’assembly et expose les types et les références de compilation. *Fournisseurs de fonctionnalités* fonctionnent avec les composants d’application pour remplir les fonctionnalités d’une application ASP.NET MVC de base. Cas d’usage principal pour les parties de l’application est à vous permettent de configurer votre application pour découvrir (ou éviter le chargement) fonctionnalités MVC à partir d’un assembly.

## <a name="introducing-application-parts"></a>Présentation des composants d’Application

Les applications MVC chargement leurs fonctionnalités à partir de [parties de l’application](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart). En particulier, la [AssemblyPart](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) classe représente une partie de l’application qui est sauvegardée par un assembly. Vous pouvez utiliser ces classes pour découvrir et charger des fonctionnalités MVC, telles que les contrôleurs, affichage des composants, les programmes d’assistance de balise et sources de compilation razor. Le [ApplicationPartManager](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) est chargé de suivre les parties de l’application et les fournisseurs de fonctionnalités disponibles pour l’application MVC. Vous pouvez interagir avec les `ApplicationPartManager` dans `Startup` lorsque vous configurez MVC :

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => p.ApplicationParts.Add(part));
```

Par défaut MVC recherche l’arborescence des dépendances et rechercher des contrôleurs (même dans d’autres assemblys). Pour charger un assembly arbitraire (par exemple, à partir d’un plug-in qui n’est pas référencé au moment de la compilation), vous pouvez utiliser une partie de l’application.

Vous pouvez utiliser les composants d’application pour *éviter* pour les contrôleurs dans un assembly particulier ou un emplacement de la recherche. Vous pouvez contrôler (ou les assemblys) sont disponibles pour l’application en modifiant le `ApplicationParts` collection de la `ApplicationPartManager`. L’ordre des entrées dans le `ApplicationParts` collection n’est pas importante. Il est important de configurer complètement le `ApplicationPartManager` avant de l’utiliser pour configurer les services dans le conteneur. Par exemple, vous devez entièrement configurer le `ApplicationPartManager` avant d’appeler `AddControllersAsServices`. Ne parvient pas à le faire, signifie que les contrôleurs dans les composants d’application ajoutés après que l’appel de méthode n’est pas affecté (ne sera pas obtenir inscrit en tant que services) qui peut entraîner une bevavior incorrecte de votre application.

Si vous avez un assembly qui contient des contrôleurs que vous ne souhaitez pas être utilisé, supprimez-le de la `ApplicationPartManager`:

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p =>
    {
        var dependentLibrary = p.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           p.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

En plus de l’assembly du projet et ses assemblys dépendants, la `ApplicationPartManager` inclura des composants pour `Microsoft.AspNetCore.Mvc.TagHelpers` et `Microsoft.AspNetCore.Mvc.Razor` par défaut.

## <a name="application-feature-providers"></a>Fournisseurs de fonctionnalités d’application

Fournisseurs de fonctionnalités d’application examiner des parties de l’application et fournissent des fonctionnalités pour les parties. Il existe des fournisseurs de fonctionnalités intégrées pour les composants MVC suivants :

* [Contrôleurs](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Référence de métadonnées](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [Tag Helpers](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Affichage des composants](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Héritent des fournisseurs de fonctionnalités `IApplicationFeatureProvider<T>`, où `T` est le type de la fonctionnalité. Vous pouvez implémenter votre propre fonction pour un des types de fonctions de MVC, les fournisseurs répertoriés ci-dessus. L’ordre des fournisseurs de fonctionnalités dans le `ApplicationPartManager.FeatureProviders` collection peut être importante, étant donné que les fournisseurs ultérieure peuvent réagir aux actions effectuées par les fournisseurs précédents.

### <a name="sample-generic-controller-feature"></a>Exemple : Fonctionnalité du contrôleur générique

Par défaut, ASP.NET Core MVC ignore les contrôleurs génériques (par exemple, `SomeController<T>`). Cet exemple utilise un fournisseur de fonctionnalité du contrôleur qui s’exécute lorsque le fournisseur par défaut et ajoute des instances de contrôleur générique pour une liste spécifiée de types (défini dans `EntityTypes.Types`) :

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

Les types d’entités :

[!code-csharp[Main](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

Le fournisseur de fonctionnalités est ajouté dans `Startup`:

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p => 
        p.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

Par défaut, les noms de contrôleur générique utilisés pour le routage serait sous la forme *GenericController'1 [Widget]* au lieu de *Widget*. L’attribut suivant est utilisé pour modifier le nom afin qu’ils correspondent au type générique utilisé par le contrôleur :

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

La `GenericController` classe :

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

Le résultat, lorsqu’un itinéraire correspondant est demandé :

![Exemple de sortie à partir de l’exemple d’application lit, « Bonjour à partir d’un contrôleur Sproket générique ».](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a>Exemple : Fonctionnalités d’affichage disponibles

Vous pouvez parcourir les fonctionnalités remplies disponibles à votre application en demandant une `ApplicationPartManager` via [injection de dépendance](../../fundamentals/dependency-injection.md) et l’utiliser pour remplir les instances des fonctionnalités appropriées :

[!code-csharp[Main](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

Exemple de sortie :

![Exemple de sortie de l’exemple d’application](app-parts/_static/available-features.png)

---
title: "Injection de dépendance dans les contrôleurs"
author: ardalis
description: 
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 946d695c572379c3ebc2eda1569f186f25ab9bfc
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="dependency-injection-into-controllers"></a>Injection de dépendance dans les contrôleurs

<a name="dependency-injection-controllers"></a>

Par [Steve Smith](https://ardalis.com/)

Les contrôleurs MVC ASP.NET Core doivent demander leurs dépendances explicitement par le biais de leurs constructeurs. Dans certains cas, les actions de contrôleur individuels peuvent nécessiter un service, et il ne peut pas judicieux de demander au niveau du contrôleur. Dans ce cas, vous pouvez également choisir d’injecter un service en tant que paramètre dans la méthode d’action.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="dependency-injection"></a>Injection de dépendance

Injection de dépendances est une technique qui suit le [principe d’Inversion de dépendance](http://deviq.com/dependency-inversion-principle/), ce qui permet aux applications de se composer de modules faiblement couplées. ASP.NET Core a une prise en charge intégrée pour [injection de dépendance](../../fundamentals/dependency-injection.md), ce qui facilite la tester et à gérer des applications.

## <a name="constructor-injection"></a>Injection de constructeurs

Prise en charge intégrée de d’ASP.NET Core pour l’injection de dépendance basés sur le constructeur s’étend à contrôleurs MVC. En ajoutant simplement un type de service à votre contrôleur en tant que paramètre de constructeur, ASP.NET Core va tenter de résoudre ce type à l’aide de sa propre dans le conteneur de service. Les services sont en général, mais pas toujours, définis à l’aide des interfaces. Par exemple, si votre application dispose d’une logique métier qui dépend de l’heure actuelle, vous pouvez injecter un service qui Récupère l’heure (plutôt que le codage en dur il), ce qui permettrait de vos tests à passer dans les implémentations qui utilisent une heure spécifique.

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


Implémentation d’une interface comme celle-ci afin qu’il utilise l’horloge système lors de l’exécution est simple :

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


Tout cela en place, nous pouvons utiliser le service dans notre contrôleur. Dans ce cas, nous avons ajouté une logique pour le `HomeController` `Index` méthode pour afficher un message d’accueil à l’utilisateur en fonction de l’heure du jour.

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

Si nous exécutons l’application maintenant, nous êtes plus susceptible de rencontrer une erreur :

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

Cette erreur se produit lorsque nous n’avons pas configuré un service dans le `ConfigureServices` méthode dans notre `Startup` classe. Pour spécifier que les demandes de `IDateTime` doivent être résolus à l’aide d’une instance de `SystemDateTime`, ajoutez la ligne en surbrillance dans la liste ci-dessous pour votre `ConfigureServices` méthode :

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> Ce service particulier peut être implémenté à l’aide de plusieurs options de durée de vie différente (`Transient`, `Scoped`, ou `Singleton`). Consultez [Injection de dépendances](../../fundamentals/dependency-injection.md) de comprendre comment chacune de ces options d’étendue affecte le comportement de votre service.

Une fois que le service a été configuré, l’application en cours d’exécution et en accédant à la page d’accueil doivent s’afficher le message temporels comme prévu :

![Message d’accueil du serveur](dependency-injection/_static/server-greeting.png)

>[!TIP]
> Consultez [logique du contrôleur de test](testing.md) pour savoir comment demander explicitement les dépendances [http://deviq.com/explicit-dependencies-principle/](http://deviq.com/explicit-dependencies-principle/) dans les contrôleurs rend le code plus facile à tester.

Injection de dépendance intégrée d’ASP.NET Core qu’un seul constructeur pour les classes de demande de services prend en charge. Si vous avez plus d’un constructeur, vous pouvez recevoir une exception indiquant :

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

Comme le message d’erreur indique, vous pouvez corriger ce problème ayant uniquement un seul constructeur. Vous pouvez également [remplacer la prise en charge de l’injection de dépendances par défaut avec une implémentation d’un tiers](../../fundamentals/dependency-injection.md#replacing-the-default-services-container), la plupart des qui prennent en charge plusieurs constructeurs.

## <a name="action-injection-with-fromservices"></a>Injection d’action avec FromServices

Parfois, vous n’avez pas besoin d’un service pour plusieurs actions dans votre contrôleur. Dans ce cas, il peut être judicieux d’injecter du service en tant que paramètre à la méthode d’action. Cela en marquant le paramètre avec l’attribut `[FromServices]` comme indiqué ici :

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a>L’accès aux paramètres d’un contrôleur

L’accès aux paramètres de configuration ou d’application à partir d’un contrôleur est un modèle commun. Cet accès doit utiliser le modèle des Options décrit dans [configuration](xref:fundamentals/configuration/index). Généralement, vous ne doit pas demander les paramètres directement à partir de votre contrôleur à l’aide de l’injection de dépendances. Une meilleure approche consiste à demande un `IOptions<T>` instance, où `T` est la classe de configuration que vous avez besoin.

Pour utiliser le modèle d’options, vous devez créer une classe qui représente les options, comme celle-ci :

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

Vous devez configurer l’application pour utiliser le modèle d’options et ajoutez votre classe de configuration à la collection de services dans `ConfigureServices`:

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> Dans la liste ci-dessus, nous examinons la configuration de l’application de lire les paramètres à partir d’un fichier au format JSON. Vous pouvez également configurer les paramètres entièrement dans le code, comme indiqué dans le code commenté ci-dessus. Consultez [Configuration](xref:fundamentals/configuration/index) pour d’autres options de configuration.

Une fois que vous avez spécifié un objet fortement typé de configuration (dans ce cas, `SampleWebSettings`) et ajouté à la collection de services, vous pouvez demander qu’il à partir de n’importe quelle méthode de contrôleur ou d’Action en demandant une instance de `IOptions<T>` (dans ce cas, `IOptions<SampleWebSettings>`) . Le code suivant montre comment une demande les paramètres à partir d’un contrôleur :

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

Suivant le modèle Options permet de paramètres et la configuration être dissocié d’uns et assure le contrôleur est [séparation des préoccupations](http://deviq.com/separation-of-concerns/), car il n’a pas besoin de connaître l’emplacement ou pour rechercher les paramètres plus d’informations. Elle simplifie également le contrôleur de test unitaire [logique du contrôleur de test](testing.md), car il est sans [adhésives statique](http://deviq.com/static-cling/) ou direct lors de l’instanciation de classes de paramètres au sein de la classe de contrôleur.

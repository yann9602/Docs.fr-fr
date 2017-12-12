---
uid: signalr/overview/advanced/dependency-injection
title: "Injection de dépendances dans SignalR | Documents Microsoft"
author: MikeWasson
description: "Versions du logiciel utilisé dans cette rubrique Visual Studio 2013 .NET 4.5 SignalR les versions précédentes de la version 2 de cette rubrique pour plus d’informations sur les versions antérieures de..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: a14121ae-02cf-4024-8af0-9dd0dc810690
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/advanced/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: 3732b5d0ea6de841a6c402bfd5ef4dfb7b7a9162
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="dependency-injection-in-signalr"></a>Injection de dépendances dans SignalR
====================
par [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

> ## <a name="software-versions-used-in-this-topic"></a>Versions du logiciel utilisées dans cette rubrique
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - .NET 4.5
> - SignalR version 2
>   
> 
> 
> ## <a name="previous-versions-of-this-topic"></a>Versions précédentes de cette rubrique
> 
> Pour plus d’informations sur les versions antérieures de SignalR, consultez [SignalR des Versions antérieures](../older-versions/index.md).
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer dans les commentaires en bas de la page. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


Injection de dépendance est un moyen pour supprimer des dépendances codées en dur entre les objets, facilitant ainsi la pour remplacer des dépendances d’un objet, soit pour le test (à l’aide d’objets fictifs) ou pour modifier le comportement d’exécution. Ce didacticiel montre comment effectuer l’injection de dépendances sur les concentrateurs SignalR. Il montre également comment utiliser des conteneurs d’inversion de contrôle avec SignalR. Un conteneur inversion de contrôle est une infrastructure générale pour l’injection de dépendance.

## <a name="what-is-dependency-injection"></a>Nouveautés d’Injection de dépendance ?

Ignorez cette section si vous êtes déjà familiarisé avec l’injection de dépendances.

*Injection de dépendance* (DI) est un modèle où les objets ne sont pas chargées de créer leurs propres dépendances. Voici un exemple simple motiver DI. Supposons que vous avez un objet qui a besoin d’enregistrer des messages. Vous pouvez définir une interface de journalisation :

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

Dans votre objet, vous pouvez créer un `ILogger` pour enregistrer des messages :

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

Cela fonctionne, mais il n’est pas la meilleure conception. Si vous souhaitez remplacer `FileLogger` avec un autre `ILogger` mise en œuvre, vous devez modifier `SomeComponent`. Supposer que beaucoup d’autres objets utilisent `FileLogger`, vous devez modifier tous les. Ou si vous décidez d’apporter `FileLogger` un singleton, vous devez également apporter des modifications à l’application.

Une meilleure approche consiste à « injecter » un `ILogger` dans l’objet, par exemple, en utilisant un argument de constructeur :

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

À présent l’objet n’est pas responsable de la sélection qui `ILogger` à utiliser. Vous pouvez swich `ILogger` implémentations sans modifier les objets qui en dépendent.

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

Ce modèle est appelé [injection de constructeur](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection). Un autre modèle est l’injection de setter, où vous définissez la dépendance via une méthode setter ou une propriété.

## <a name="simple-dependency-injection-in-signalr"></a>Injection de dépendances simple dans SignalR

Envisagez de l’application de conversation à partir du didacticiel [mise en route avec SignalR](../getting-started/tutorial-getting-started-with-signalr.md). Voici la classe de concentrateur à partir de cette application :

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

Supposons que vous souhaitez stocker les messages de conversation sur le serveur avant de les envoyer. Vous pouvez définir une interface qui s’approprie cette fonctionnalité et utiliser DI injecter l’interface dans le `ChatHub` classe.

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

Le seul problème est qu’une application SignalR ne crée pas directement les concentrateurs ; SignalR crée pour vous. Par défaut, SignalR attend une classe de concentrateur à avoir un constructeur sans paramètre. Toutefois, vous pouvez facilement inscrire une fonction pour créer des instances de concentrateur et utiliser cette fonction pour effectuer DI. Inscription de la fonction en appelant **GlobalHost.DependencyResolver.Register**.

[!code-csharp[Main](dependency-injection/samples/sample7.cs)]

Maintenant SignalR appelle cette fonction anonyme chaque fois qu’il a besoin créer un `ChatHub` instance.

## <a name="ioc-containers"></a>Conteneurs d’inversion de contrôle

Le code précédent est tout indiquée pour les cas simples. C’est toujours écrire ceci :

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

Dans une application complexe avec de nombreuses dépendances, vous devrez écrire un lot de ce code « connexion ». Ce code peut être difficile à maintenir, particulièrement si les dépendances sont imbriquées. Il est également difficile de test unitaire.

Une solution consiste à utiliser un conteneur inversion de contrôle. Un conteneur inversion de contrôle est un composant logiciel qui est chargé de gérer les dépendances. Vous inscrivez les types avec le conteneur et ensuite utilisez le conteneur pour créer des objets. Le conteneur effectue automatiquement les relations de dépendance. De nombreux conteneurs d’inversion de contrôle vous permettent également de contrôler les éléments tels que la durée de vie et la portée.

> [!NOTE]
> « IoC » signifie « d’inversion de contrôle », qui est un modèle général où une infrastructure appelle du code d’application. Un conteneur IoC construit vos objets, lequel « inverse » le flux habituel de contrôle.


## <a name="using-ioc-containers-in-signalr"></a>À l’aide de conteneurs d’inversion de contrôle dans SignalR

L’application de la conversation est probablement trop simple pour tirer parti d’un conteneur inversion de contrôle. Au lieu de cela, examinons la [StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample) exemple.

L’exemple StockTicker définit deux classes principales :

- `StockTickerHub`: La classe de concentrateur, qui gère les connexions clientes.
- `StockTicker`: Un singleton qui conserve des actions et les met à jour régulièrement.

`StockTickerHub`contient une référence à la `StockTicker` singleton, tandis que `StockTicker` contient une référence à la **IHubConnectionContext** pour le `StockTickerHub`. Il utilise cette interface pour communiquer avec `StockTickerHub` instances. (Pour plus d’informations, consultez [serveur de diffusion avec ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).)

Nous pouvons utiliser un conteneur inversion de contrôle à ces dépendances un peu. Tout d’abord, nous allons simplifier le `StockTickerHub` et `StockTicker` classes. Dans le code suivant, j’ai mis en commentaire les parties que nous n’avez pas besoin.

Supprimez le constructeur sans paramètre à partir de `StockTickerHub`. Au lieu de cela, nous allons utiliser toujours DI pour créer le concentrateur.

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

Pour StockTicker, supprimez l’instance de singleton. Une version ultérieure, nous allons utiliser le conteneur inversion de contrôle pour contrôler la durée de vie StockTicker. En outre, rendez le constructeur public.

[!code-csharp[Main](dependency-injection/samples/sample10.cs?highlight=7)]

Ensuite, nous pouvons refactoriser le code en créant une interface pour `StockTicker`. Nous allons utiliser cette interface pour découpler le `StockTickerHub` à partir de la `StockTicker` classe.

Visual Studio apporte facilement ce type de refactorisation. Ouvrez le fichier StockTicker.cs, avec le bouton droit sur le `StockTicker` déclaration de classe, puis sélectionnez **refactoriser** ... **Extraire l’Interface**.

![](dependency-injection/_static/image1.png)

Dans le **extraire l’Interface** boîte de dialogue, cliquez sur **sélectionner tout**. Laissez les valeurs par défaut. Cliquez sur **OK**.

![](dependency-injection/_static/image2.png)

Visual Studio crée une nouvelle interface nommée `IStockTicker`et modifie également `StockTicker` dériver `IStockTicker`.

Ouvrez le fichier IStockTicker.cs et de modifier l’interface à **public**.

[!code-csharp[Main](dependency-injection/samples/sample11.cs?highlight=1)]

Dans le `StockTickerHub` de classe, modifiez les deux instances de `StockTicker` à `IStockTicker`:

[!code-csharp[Main](dependency-injection/samples/sample12.cs?highlight=4,6)]

Création d’un `IStockTicker` interface n’est pas strictement nécessaire, mais je souhaitais montrer comment les DI peut aider à réduire le couplage entre les composants de votre application.

## <a name="add-the-ninject-library"></a>Ajoutez la bibliothèque Ninject

Il existe de nombreux conteneurs d’inversion de contrôle open source pour .NET. Pour ce didacticiel, je vais utiliser [Ninject](http://www.ninject.org/). (Incluent d’autres bibliothèques populaires [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Unity](https://github.com/unitycontainer/unity), et [StructureMap ](http://docs.structuremap.net).)

Utilisez le Gestionnaire de Package NuGet pour installer le [Ninject bibliothèque](https://nuget.org/packages/Ninject/3.0.1.10). Dans Visual Studio, à partir de la **outils** menu Sélectionnez **Gestionnaire de Package de bibliothèque** | **Package Manager Console**. Dans la fenêtre de Console du Gestionnaire de Package, entrez la commande suivante :

[!code-powershell[Main](dependency-injection/samples/sample13.ps1)]

## <a name="replace-the-signalr-dependency-resolver"></a>Remplacez le résolveur de dépendance SignalR

Pour utiliser Ninject dans SignalR, créez une classe qui dérive de **DefaultDependencyResolver**.

[!code-csharp[Main](dependency-injection/samples/sample14.cs)]

Cette classe substitue le **GetService** et **GetServices** méthodes de **DefaultDependencyResolver**. SignalR appelle ces méthodes pour créer divers objets lors de l’exécution, y compris les instances de concentrateur, ainsi que divers services utilisés en interne par SignalR.

- Le **GetService** méthode crée une instance unique d’un type. Substituez cette méthode pour appeler le noyau de Ninject **TryGet** (méthode). Si cette méthode retourne la valeur null, revenir au programme de résolution par défaut.
- Le **GetServices** méthode crée une collection d’objets d’un type spécifié. Substituez cette méthode pour concaténer les résultats de Ninject avec les résultats à partir du programme de résolution par défaut.

## <a name="configure-ninject-bindings"></a>Configurer les liaisons de Ninject

Maintenant, nous allons utiliser Ninject pour déclarer des liaisons de type.

Ouvrez votre classe de l’application Startup.cs (que vous avez soit créé manuellement selon les instructions de package de `readme.txt`, ou qui a été créé en ajoutant l’authentification à votre projet). Dans le `Startup.Configuration` (méthode), créez le conteneur de Ninject Ninject appelle la *noyau*.

[!code-csharp[Main](dependency-injection/samples/sample15.cs)]

Créez une instance de notre programme de résolution de dépendance personnalisée :

[!code-csharp[Main](dependency-injection/samples/sample16.cs)]

Créer une liaison pour `IStockTicker` comme suit :

[!code-csharp[Main](dependency-injection/samples/sample17.cs)]

Ce code indique deux choses. Premier, chaque fois que l’application a besoin un `IStockTicker`, le noyau doit créer une instance de `StockTicker`. Ensuite, la `StockTicker` classe doit être créé en tant qu’objet singleton. Ninject crée une instance de l’objet et retournent la même instance pour chaque demande.

Créer une liaison pour **IHubConnectionContext** comme suit :

[!code-csharp[Main](dependency-injection/samples/sample18.cs)]

Ce code de creatres une fonction anonyme qui retourne un **IHubConnection**. Le **WhenInjectedInto** méthode indique Ninject pour utiliser cette fonction uniquement lors de la création `IStockTicker` instances. La raison est que SignalR crée **IHubConnectionContext** instances en interne, et nous ne souhaitez pas remplacer la SignalR les crée. Cette fonction s’applique uniquement à notre `StockTicker` classe.

Passez le résolveur de dépendance dans le **MapSignalR** méthode en ajoutant une configuration de concentrateur :

[!code-csharp[Main](dependency-injection/samples/sample19.cs)]

Mettre à jour de la méthode Startup.ConfigureSignalR dans la classe de démarrage de l’exemple avec le nouveau paramètre :

[!code-csharp[Main](dependency-injection/samples/sample20.cs)]

Présent SignalR utilise le programme de résolution spécifié dans **MapSignalR**, au lieu du résolveur par défaut.

Voici le code complet pour `Startup.Configuration`.

[!code-csharp[Main](dependency-injection/samples/sample21.cs)]

Pour exécuter l’application StockTicker dans Visual Studio, appuyez sur F5. Dans la fenêtre du navigateur, accédez à `http://localhost:*port*/SignalR.Sample/StockTicker.html`.

![](dependency-injection/_static/image3.png)

L’application a exactement les mêmes fonctionnalités qu’avant. (Pour obtenir une description, consultez [serveur de diffusion avec ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).) Nous n’avons pas de modifier le comportement ; simplement rend le code plus facile à tester, gérer et faire évoluer.

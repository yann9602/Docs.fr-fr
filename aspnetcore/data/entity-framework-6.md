---
title: Mise en route avec ASP.NET Core et Entity Framework 6
author: tdykstra
description: Cet article explique comment utiliser Entity Framework 6 dans une application ASP.NET Core.
manager: wpickett
ms.author: tdykstra
ms.date: 02/24/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: data/entity-framework-6
ms.openlocfilehash: 7407fe8a976978d7d5077d5e5ac6cc264565621d
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="getting-started-with-aspnet-core-and-entity-framework-6"></a>Mise en route avec ASP.NET Core et Entity Framework 6

Par [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), et [Tom Dykstra](https://github.com/tdykstra)

Cet article explique comment utiliser Entity Framework 6 dans une application ASP.NET Core.

## <a name="overview"></a>Vue d'ensemble

Pour utiliser Entity Framework 6, votre projet doit compiler par rapport à .NET Framework, comme Entity Framework 6 ne prend pas en charge le .NET Core. Si vous avez besoin de fonctionnalités d’inter-plateformes, vous devrez mettre à niveau vers [Entity Framework Core](https://docs.microsoft.com/ef/).

La méthode recommandée pour utiliser Entity Framework 6 dans une application ASP.NET Core consiste à placer le contexte EF6 et classes de modèle dans une bibliothèque de classes du projet qui cible le framework complet. Ajoutez une référence à la bibliothèque de classes à partir du projet ASP.NET Core. Consultez l’exemple [solution Visual Studio avec des projets EF6 et ASP.NET Core](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).

Vous ne pouvez pas mettre un contexte EF6 dans un projet ASP.NET Core, car les projets .NET Core ne prend pas en charge toutes les fonctionnalités EF6 commandes telles que *Enable-Migrations* requièrent.

Quel que soit le type de projet dans lequel vous localisez votre contexte EF6, EF6 uniquement les outils de ligne de commande fonctionnent avec un contexte EF6. Par exemple, `Scaffold-DbContext` est disponible uniquement dans Entity Framework Core. Si vous devez à l’ingénierie à une base de données dans un modèle EF6, consultez [Code First pour une base de données](https://msdn.microsoft.com/jj200620).

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>Framework complet de référence et EF6 dans le projet ASP.NET Core

Votre projet ASP.NET Core doit faire référence à .NET framework et EF6. Par exemple, le *.csproj* fichier de votre projet ASP.NET Core doit ressembler à l’exemple suivant (uniquement les parties pertinentes du fichier sont affichés).

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

Lorsque vous créez un nouveau projet, utilisez le **Application ASP.NET Core Web (.NET Framework)** modèle.

## <a name="handle-connection-strings"></a>Traiter les chaînes de connexion

Les outils de ligne de commande EF6 que vous allez utiliser dans le projet de bibliothèque de classes EF6 nécessitent un constructeur par défaut afin qu’ils ne peuvent instancier le contexte. Mais vous souhaiterez probablement spécifier la chaîne de connexion à utiliser dans le projet ASP.NET Core, auquel cas votre constructeur de contexte doit avoir un paramètre qui vous permet de passer la chaîne de connexion. Voici un exemple.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

Puisque le contexte EF6 n’a pas un constructeur sans paramètre, votre projet EF6 doit fournir une implémentation de [IDbContextFactory](https://msdn.microsoft.com/library/hh506876). Les outils de ligne de commande EF6 recherchera et utiliser cette implémentation afin qu’ils ne peuvent instancier le contexte. Voici un exemple.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

Dans cet exemple de code, la `IDbContextFactory` implémentation transmet une chaîne de connexion codées en dur. Il s’agit de la chaîne de connexion qui utilisent les outils de ligne de commande. Que vous souhaitez implémenter une stratégie pour vous assurer que la bibliothèque de classes utilise la même chaîne de connexion qui utilise de l’application appelante. Par exemple, vous pouvez obtenir la valeur d’une variable d’environnement dans les deux projets.

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>Configurer l’injection de dépendances dans le projet ASP.NET Core

Dans le projet de base *Startup.cs* fichier, définissez le contexte EF6 pour l’injection de dépendance (DI) `ConfigureServices`. Doivent porter les objets de contexte EF pour une durée de vie de chaque demande.

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

Vous pouvez ensuite obtenir une instance du contexte de vos contrôleurs à l’aide de DI. Le code est similaire à ce que vous pourriez écrire pour un contexte EF :

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a>Exemple d’application

Pour un exemple d’application fonctionnel, consultez le [exemple de solution de Visual Studio](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) qui accompagne cet article.

Cet exemple peut être créé à partir de zéro par les étapes suivantes dans Visual Studio :

* Créer une solution.

* **Ajouter un nouveau projet > Web > ASP.NET Core Web Application (.NET Framework)**

* **Ajouter un nouveau projet > de bureau Windows classique > classe de bibliothèque (.NET Framework)**

* Dans **Package Manager Console** (PMC) pour les deux projets, exécutez la commande `Install-Package Entityframework`.

* Dans le projet de bibliothèque de classes, créer des classes de modèle de données et une classe de contexte et l’implémentation de `IDbContextFactory`.

* Dans PMC pour le projet de bibliothèque de classes, exécutez les commandes `Enable-Migrations` et `Add-Migration Initial`. Si vous avez défini le projet ASP.NET Core comme projet de démarrage, ajoutez `-StartupProjectName EF6` à ces commandes.

* Dans le projet de base, ajoutez une référence de projet au projet de bibliothèque de classes.

* Dans le projet de base, dans *Startup.cs*, enregistrer le contexte pour DI.

* Dans le projet de base, dans *appsettings.json*, ajoutez la chaîne de connexion.

* Dans le projet de base, ajoutez un contrôleur et les vues pour vérifier que vous pouvez lire et écrire des données. (Notez que la structure de base de ASP.NET MVC ne fonctionne pas avec le contexte EF6 référencé à partir de la bibliothèque de classes).

## <a name="summary"></a>Récapitulatif

Cet article a fourni des conseils de base pour l’utilisation d’Entity Framework 6 dans une application ASP.NET Core.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Entity Framework - Configuration basée sur le Code](https://msdn.microsoft.com/data/jj680699.aspx)

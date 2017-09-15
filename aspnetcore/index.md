---
title: "Présentation d’ASP.NET Core"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: index
ms.openlocfilehash: d8516643393cdf9175adc78ab98420dc1dc5d1d9
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-aspnet-core"></a>Présentation d’ASP.NET Core

Par [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Shaun Luttin](https://twitter.com/dicshaunary)

ASP.NET Core est un framework multiplateforme à hautes performances et [open source](https://github.com/aspnet/home) pour créer des applications cloud modernes et connectées à Internet. Avec ASP.NET Core, vous pouvez :

* Créer des applications et des services web, des applications IoT et des back-ends mobiles.
* Utilisez vos outils de développement préférés sur Windows, macOS et Linux.
* Déployer sur le cloud ou localement
* Exécuter sur [.NET Core ou .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-use-aspnet-core"></a>Pourquoi utiliser ASP.NET Core ?

Des millions de développeurs ont utilisé ASP.NET (et continuer de l’utiliser) pour créer des applications web. ASP.NET Core est une reconception d’ASP.NET, avec des modifications d’architecture qui aboutissent à un framework plus léger et modulaire.

ASP.NET Core offre les avantages suivants :

* Un scénario unifié pour créer une interface utilisateur web et des API web.
* Intégration de [frameworks modernes côté client](xref:client-side/index) et de workflows de développement.
* Un [système de configuration](xref:fundamentals/configuration) prêt pour le cloud et basé sur les environnements.
* [Injection de dépendances](xref:fundamentals/dependency-injection) intégrée.
* Un pipeline des requêtes HTTP léger, à hautes performances et modulaire.
* Capacité d’hébergement sur IIS ou d’auto-hébergement dans votre propre processus.
* Peut s’exécuter sur [.NET Core](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server), qui prend en charge une gestion réelle des versions des applications côte à côte.
* Outils qui simplifient le développement web moderne.
* Capacité à générer et à exécuter sur Windows, macOS et Linux.
* Open source et centré sur la communauté.

ASP.NET Core est fourni entièrement sous forme de packages [NuGet](https://www.nuget.org/). Ceci vous permet d’optimiser votre application en y incluant seulement les packages NuGet dont vous avez besoin. Les avantages des applications avec une surface d’exposition plus petite sont une sécurité accrue, une maintenance réduite et des performances améliorées.

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Créer des API web et une interface utilisateur web en utilisant le modèle MVC d’ASP.NET Core

Le modèle MVC d’ASP.NET Core fournit des fonctionnalités qui vous aident à créer des [API web](xref:tutorials/index#building-web-apis) et des [applications web](xref:tutorials/index#building-web-applications) :

* Le [modèle MVC (Modèle-Vue-Contrôleur)](xref:mvc/overview) permet de rendre vos API web et vos applications web [testables](testing/index.md).
* [Razor Pages](xref:mvc/razor-pages/index) (nouveauté dans 2.0) est un modèle de programmation basé sur les pages qui rend plus facile et plus productive la création d’une interface utilisateur web.
* La [syntaxe Razor](xref:mvc/views/razor) fournit un langage efficace pour [Razor Pages](xref:mvc/razor-pages/index) et les [vues MVC](xref:mvc/views/overview).
* Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les fichiers Razor.
* La prise en charge intégrée de [plusieurs formats de données et de la négociation de contenu](mvc/models/formatting.md) permet à vos API web d’être utilisées par un large éventail de clients, notamment des navigateurs et des appareils mobiles.
* La [liaison de modèle](xref:mvc/models/model-binding) mappe automatiquement les données des requêtes HTTP aux paramètres des méthodes d’action.
* La [validation de modèle](xref:mvc/models/validation) effectue automatiquement la validation côté client et côté serveur.

## <a name="client-side-development"></a>Développement côté client

ASP.NET Core est conçu pour s’intégrer parfaitement à différents frameworks côté client, notamment [AngularJS](xref:client-side/angular), [KnockoutJS](xref:client-side/knockout) et [Bootstrap](xref:client-side/bootstrap). Pour plus d’informations, consultez [Développement côté client](client-side/index.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, reportez-vous aux ressources suivantes :

* [Didacticiels ASP.NET Core](xref:tutorials/index)
* [Notions de base d’ASP.NET Core](xref:fundamentals/index)
* [Le point hebdomadaire de la communauté ASP.NET](https://live.asp.net/) couvre l’avancement et les plans de l’équipe, et comprend de nouveaux blogs et des logiciels de tiers.

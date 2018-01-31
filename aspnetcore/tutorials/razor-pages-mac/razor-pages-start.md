---
title: "Bien démarrer avec des pages Razor dans ASP.NET Core sur Mac"
author: rick-anderson
description: "Bien démarrer avec des pages Razor dans ASP.NET Core à l’aide de Visual Studio pour Mac"
manager: wpickett
ms.author: riande
ms.date: 07/27/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages-mac/razor-pages-start
ms.openlocfilehash: 9e7d1db47e4cc9d753b1629e20345ca1f4403b2f
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="getting-started-with-razor-pages-in-aspnet-core-with-visual-studio-for-mac"></a>Bien démarrer avec des pages Razor dans ASP.NET Core avec Visual Studio pour Mac

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web de pages Razor dans ASP.NET Core. Nous vous recommandons de revoir [Présentation des pages Razor](xref:mvc/razor-pages/index) avant de commencer ce didacticiel. L’utilisation de pages Razor est la méthode recommandée pour générer l’IU d’applications web dans ASP.NET Core.

## <a name="prerequisites"></a>Prérequis

Installez les éléments suivants :

* [SDK .NET Core 2.0.0 ](https://www.microsoft.com/net/core) ou version ultérieure
* [Visual Studio pour Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-a-razor-web-app"></a>Créer une application web Razor

À partir d’un terminal, exécutez les commandes suivantes :

```console
dotnet new razor -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

Les commandes précédentes utilisent le [CLI .NET Core](https://docs.microsoft.com/dotnet/core/tools/dotnet) pour créer et exécuter un projet de pages Razor. Ouvrez un navigateur à l’adresse http://localhost:5000 pour afficher l’application.

![Page d’accueil ou page d’index](../razor-pages/razor-pages-start/_static/home.png)

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

## <a name="open-the-project"></a>Ouvrir le projet

Appuyez sur Ctrl+C pour arrêter l’application.

Dans Visual Studio, sélectionnez **Fichier > Ouvrir**, puis sélectionnez le fichier *RazorPagesMovie.csproj*.

### <a name="launch-the-app"></a>Lancer l’application

Dans Visual Studio, sélectionnez **Exécuter > Exécuter sans débogage** pour lancer l’application. Visual Studio démarre [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview), lance un navigateur, puis accède à `http://localhost:5000`.

Dans le prochain didacticiel, nous allons ajouter un modèle au projet.

>[!div class="step-by-step"]
[Suivant : Ajout d’un modèle](xref:tutorials/razor-pages-mac/model)

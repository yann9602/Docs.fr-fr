---
title: "Bien démarrer avec des pages Razor dans ASP.NET Core avec Visual Studio Code"
author: rick-anderson
description: "Bien démarrer avec des pages Razor dans ASP.NET Core à l’aide de Visual Studio Code"
ms.author: riande
manager: wpickett
ms.date: 08/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages-vsc/razor-pages-start
ms.openlocfilehash: 727c3d5c8bed0aef3094816b7e5f0a940aea654c
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="getting-started-with-razor-pages-in-aspnet-core-with-visual-studio-code"></a>Bien démarrer avec des pages Razor dans ASP.NET Core avec Visual Studio Code

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web de pages Razor dans ASP.NET Core. Nous vous recommandons d’effectuer l’étape [Présentation des pages Razor](xref:mvc/razor-pages/index) avant de commencer ce didacticiel. L’utilisation de pages Razor est la méthode recommandée pour générer l’IU d’applications web dans ASP.NET Core.

## <a name="prerequisites"></a>Prérequis

Installez les éléments suivants :

* [SDK .NET Core 2.0.0 ](https://www.microsoft.com/net/core) ou version ultérieure
* [Visual Studio Code](https://code.visualstudio.com)
* [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) VS Code 

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

Dans Visual Studio Code (VS Code), sélectionnez **Fichier > Ouvrir le dossier**, puis sélectionnez le dossier *RazorPagesMovie*.

- Sélectionnez **Oui** quand une boîte de dialogue **Avertissement** s’affiche en indiquant le message suivant : « Les composants nécessaires à la build et au débogage sont manquants dans ‘RazorPagesMovie’. Faut-il les ajouter ? »
- Sélectionnez **Restaurer** quand une boîte de dialogue **Informations** s’affiche en indiquant qu’il existe des dépendances non résolues.

### <a name="launch-the-app"></a>Lancer l’application

Appuyez sur Ctrl+F5 pour démarrer l’application sans débogage. Vous pouvez également aller dans le menu **Déboguer**, puis sélectionner **Exécuter sans débogage**.

Dans le prochain didacticiel, nous allons ajouter un modèle au projet. 

>[!div class="step-by-step"]
[Suivant : Ajout d’un modèle](xref:tutorials/razor-pages-vsc/model)  

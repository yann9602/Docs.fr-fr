---
title: "Bien démarrer avec des pages Razor dans ASP.NET Core"
author: rick-anderson
description: "Bien démarrer avec des pages Razor dans ASP.NET Core"
manager: wpickett
ms.author: riande
ms.date: 12/22/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 54fa970d136de3903ae08b710b55f15f96f9a012
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="get-started-with-razor-pages-in-aspnet-core"></a>Bien démarrer avec des pages Razor dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web de pages Razor dans ASP.NET Core. L’utilisation de pages Razor est la méthode recommandée pour générer l’interface utilisateur d’applications web dans ASP.NET Core.

Il existe trois versions de ce didacticiel :

* Windows : ce didacticiel
* Mac OS : [Bien démarrer avec les pages Razor avec Visual Studio pour Mac](xref:tutorials/razor-pages-mac/razor-pages-start)
* Mac OS, Linux et Windows : [Bien démarrer avec les pages Razor dans ASP.NET Core avec Visual Studio Code](xref:tutorials/razor-pages-vsc/razor-pages-start)

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="create-a-razor-web-app"></a>Créer une application web Razor

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Créez une application web ASP.NET Core. Nommez le projet **RazorPagesMovie**. Il est important de nommer le projet *RazorPagesMovie* pour que les espaces de noms correspondent quand vous copiez/collez du code.
  ![Nouvelle application web ASP.NET Core](../../mvc/razor-pages/index/_static/np.png)
* Sélectionnez **ASP.NET Core 2.0** dans la liste déroulante, puis sélectionnez **Application web**.

  [!INCLUDE[install 2.0](../../includes/dotnetcore-on-dotnetfx-vs.md)]

Le modèle Visual Studio crée un projet de démarrage :

![Explorateur de solutions](razor-pages-start/_static/se.png)

Appuyez sur **F5** pour exécuter l’application en mode débogage ou sur **Ctrl+F5** pour l’exécuter sans attachement du débogueur

![Page d’accueil ou page d’index](razor-pages-start/_static/home.png)

* Visual Studio démarre [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute votre application. La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. En effet, `localhost` est le nom d’hôte standard de votre ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web. Dans l’image précédente, le numéro de port est 5 000. Quand vous exécutez l’application, vous voyez un autre numéro de port.
* Si vous lancez l’application avec **Ctrl+F5** (mode sans débogage), vous pouvez effectuer des modifications du code, enregistrer le fichier, actualiser le navigateur et afficher les modifications du code. De nombreux développeurs préfèrent utiliser le mode sans débogage pour lancer rapidement l’application et afficher les changements.

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

>[!div class="step-by-step"]
[Suivant : Ajout d’un modèle](xref:tutorials/razor-pages/model)

>[!div class="step-by-step"]
[Suivant : Ajout d’un modèle](xref:tutorials/razor-pages/model)

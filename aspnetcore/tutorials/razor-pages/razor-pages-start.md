---
title: "Bien démarrer avec des pages Razor dans ASP.NET Core"
author: rick-anderson
description: "Bien démarrer avec des pages Razor dans ASP.NET Core"
keywords: ASP.NET Core,pages Razor,Razor,MVC
ms.author: riande
manager: wpickett
ms.date: 08/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 8c6e281e761e69908fc742d1f19c14a00de4bd46
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2017
---
# <a name="getting-started-with-razor-pages-in-aspnet-core"></a>Bien démarrer avec des pages Razor dans ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web de pages Razor dans ASP.NET Core. Nous vous recommandons d’effectuer l’étape [Présentation des pages Razor](xref:mvc/razor-pages/index) avant de commencer ce didacticiel. L’utilisation de pages Razor est la méthode recommandée pour générer l’IU d’applications web dans ASP.NET Core.

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="create-a-razor-web-app"></a>Créer une application web Razor

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau > Projet**.
* Créez une application web ASP.NET Core. Nommez le projet **RazorPagesMovie**. Il est important de nommer le projet *RazorPagesMovie* pour que les espaces de noms correspondent quand vous copiez/collez du code.
 ![Nouvelle application web ASP.NET Core](../../mvc/razor-pages/index/_static/np.png)
* Sélectionnez **ASP.NET Core 2.0** dans la liste déroulante, puis sélectionnez **Application web**.
 ![Application web (pages Razor)](../../mvc/razor-pages/index/_static/np2.png)

Le modèle Visual Studio crée un projet de démarrage :

![Explorateur de solutions](razor-pages-start/_static/se.png)

Appuyez sur **F5** pour exécuter l’application en mode débogage ou sur **Ctrl+F5** pour l’exécuter sans attachement du débogueur

![Page d’accueil ou page d’index](razor-pages-start/_static/home.png)

* Visual Studio démarre [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute votre application. La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. En effet, `localhost` est le nom d’hôte standard de votre ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web. Dans l’image précédente, le numéro de port est 5 000. Quand vous exécutez l’application, vous voyez un autre numéro de port.
* Si vous lancez l’application avec **Ctrl+F5** (mode sans débogage), vous pouvez effectuer des modifications du code, enregistrer le fichier, actualiser le navigateur et afficher les modifications du code. De nombreux développeurs préfèrent utiliser le mode sans débogage pour lancer rapidement l’application et afficher les changements.

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

>[!div class="step-by-step"]
[Suivant : Ajout d’un modèle](xref:tutorials/razor-pages/modelz)  

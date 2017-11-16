---
title: "Bien démarrer avec ASP.NET Core MVC et Visual Studio"
author: rick-anderson
description: "Bien démarrer avec ASP.NET Core MVC et Visual Studio"
keywords: ASP.NET Core, MVC
ms.author: riande
manager: wpickett
ms.date: 10/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 4b86eb22e367d2305b7995421aec6f37008c5637
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="getting-started-with-aspnet-core-mvc-and-visual-studio"></a>Bien démarrer avec ASP.NET Core MVC et Visual Studio

De [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[consider RP](../../includes/razor.md)]

Il existe trois versions de ce didacticiel :

* macOS : [Créer une application ASP.NET Core MVC avec Visual Studio pour Mac](xref:tutorials/first-mvc-app-mac/start-mvc)
* Windows : [Créer une application ASP.NET Core MVC avec Visual Studio](xref:tutorials/first-mvc-app/start-mvc)
* macOS, Linux et Windows : [Créer une application ASP.NET Core MVC avec Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)

## <a name="install-visual-studio-and-net-core"></a>Installer Visual Studio et .NET Core

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Installez Visual Studio Community 2017. Sélectionnez le téléchargement Community. Ignorez cette étape si Visual Studio 2017 est installé sur votre ordinateur.

* [Page d’accueil du programme d’installation de Visual Studio 2017](https://www.visualstudio.com/)

Exécutez le programme d’installation et sélectionnez les charges de travail suivantes :

* **ASP.NET et développement web** (sous **Web et cloud**)
* **Développement multiplateforme .NET Core** (sous **Autres ensembles d’outils**)

![**ASP.NET et développement web** (sous **Web et cloud**)](start-mvc/_static/web_workload.png)

![**Développement multiplateforme .NET Core** (sous **Autres ensembles d’outils**)](start-mvc/_static/x_plat_wl.png)

---

## <a name="create-a-web-app"></a>Créer une application web

Dans Visual Studio, sélectionnez **Fichier > Nouveau > Projet**.

![Fichier > Nouveau > Projet](start-mvc/_static/alt_new_project.png)

Renseignez la boîte de dialogue **Nouveau projet** :

* Dans le volet gauche, cliquez sur **.NET Core**.
* Dans le volet central, cliquez sur **Application web ASP.NET Core (.NET Core)**.
* Nommez le projet « MvcMovie » (ceci est important pour que l’espace de noms corresponde quand vous copierez le code).
* Appuyez sur **OK**.

![Boîte de dialogue Nouveau projet, .Net Core dans le volet gauche, web ASP.NET Core ](start-mvc/_static/new_project2.png)


# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Renseignez la boîte de dialogue **Nouvelle application web ASP.NET Core (.NET Core) - MvcMovie** :

* Dans la zone de liste déroulante du sélecteur de version, sélectionnez **ASP.NET Core 2.-**.
* Sélectionnez **Application web (Model-View-Controller)**
* Appuyez sur **OK**.

![Boîte de dialogue Nouveau projet, .Net Core dans le volet gauche, web ASP.NET Core ](start-mvc/_static/new_project22.png)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Renseignez la boîte de dialogue **Nouvelle application web ASP.NET Core (.NET Core) - MvcMovie** :

* Dans la zone de liste déroulante du sélecteur de version, appuyez sur **ASP.NET Core 1.1**.
* Appuyez sur **Application web**.
* Conservez la valeur par défaut **Aucune authentification**.
* Appuyez sur **OK**.

![Nouvelle application web ASP.NET Core](start-mvc/_static/p3.png)

---

Visual Studio a utilisé un modèle par défaut pour le projet MVC que vous venez de créer. Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options. Il s’agit d’un projet de démarrage simple qui constitue un bon point de départ.

Appuyez sur **F5** pour exécuter l’application en mode débogage ou sur **Ctrl-F5** pour l’exécuter en mode non-débogage.
<!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![application en cours d’exécution](start-mvc/_static/1.png)

* Visual Studio démarre [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute votre application. Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`. C’est parce que `localhost` est le nom d’hôte standard de votre ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web. Dans l’image ci-dessus, le numéro de port est 5000. Quand vous exécuterez l’application, un numéro de port différent sera affiché.
* Si vous lancez l’application avec **Ctrl+F5** (mode sans débogage), vous pouvez effectuer des modifications du code, enregistrer le fichier, actualiser le navigateur et afficher les modifications du code. De nombreux développeurs préfèrent utiliser le mode non-débogage pour lancer rapidement l’application et voir les modifications.
* Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :

![Menu Déboguer](start-mvc/_static/debug_menu.png)

* Vous pouvez déboguer l’application en appuyant sur le bouton **IIS Express**.

![IIS Express](start-mvc/_static/iis_express.png)

Le modèle par défaut donne des liens **Accueil, À propos** et **Contact** fonctionnels. L’image de navigateur ci-dessus ne montre pas ces liens. En fonction de la taille de votre navigateur, vous devrez peut-être cliquer sur l’icône de navigation pour les afficher.

![Icône de navigation en haut à droite](start-mvc/_static/2.png)

Si vous exécutiez l’application en mode débogage, appuyez sur **Maj-F5** pour arrêter le débogage.

Dans la prochaine partie de ce didacticiel, nous allons découvrir MVC et commencer à écrire du code.

>[!div class="step-by-step"]
[Next](adding-controller.md)  

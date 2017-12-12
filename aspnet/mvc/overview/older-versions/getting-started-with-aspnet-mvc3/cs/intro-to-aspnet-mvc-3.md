---
uid: mvc/overview/older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3
title: "Présentation d’ASP.NET MVC 3 (c#) | Documents Microsoft"
author: Rick-Anderson
description: "Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/12/2011
ms.topic: article
ms.assetid: 86a80b35-88bd-4b7c-bd58-f6e7997197d4
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3
msc.type: authoredcontent
ms.openlocfilehash: bbeaad9e52db1fd85ef166052a377e8b6732a90a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="intro-to-aspnet-mvc-3-c"></a>Présentation d’ASP.NET MVC 3 (c#)
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> > [!NOTE]
> > Une version mise à jour de ce didacticiel est disponible [ici](../../../getting-started/introduction/getting-started.md) qui utilise ASP.NET MVC 5 et Visual Studio 2013. Il est plus sécurisé, beaucoup plus simple à suivre et illustre plusieurs fonctionnalités.
> 
> 
> Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est une version gratuite de Microsoft Visual Studio. Avant de commencer, assurez-vous que vous avez installé les composants requis répertoriés ci-dessous. Vous pouvez installer tous les en cliquant sur le lien suivant : [Web Platform Installer](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack). Vous pouvez également installer individuellement les conditions préalables à l’aide des liens suivants :
> 
> - [Conditions préalables requises de Visual Studio Web Developer Express SP1](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
> - [Mettre à jour des outils ASP.NET MVC 3](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
> - [SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)(runtime + outils prennent en charge)
> 
> Si vous utilisez Visual Studio 2010 au lieu de Visual Web Developer 2010, installez les composants requis en cliquant sur le lien suivant : [composants requis de Visual Studio 2010](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack).
> 
> Un projet de Visual Web Developer avec code source c# est disponible pour accompagner cette rubrique. [Télécharger la version c#](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098). Si vous préférez Visual Basic, basculez vers le [version Visual Basic](../vb/intro-to-aspnet-mvc-3.md) de ce didacticiel.


## <a name="what-youll-build"></a>Ce que vous allez générer

Vous allez implémenter une application de liste de films simple qui prend en charge la création, modification et affichage des films à partir d’une base de données. Vous trouverez ci-dessous deux captures d’écran de l’application que vous allez générer. Il comprend une page qui affiche une liste de films à partir d’une base de données :

![MoviesWithVariousSm](intro-to-aspnet-mvc-3/_static/image1.png)

L’application vous permet également à ajouter, modifier et supprimer des films, ainsi que de voir les détails sur certains d'entre eux individuels. Tous les scénarios de saisie de données incluent la validation pour vous assurer que les données stockées dans la base de données sont correctes.

![](intro-to-aspnet-mvc-3/_static/image2.png)

## <a name="skills-youll-learn"></a>Vous allez apprendre des compétences

Voici ce que vous allez apprendre :

- Comment créer un nouveau projet ASP.NET MVC.
- La création d’ASP.NET MVC contrôleurs et les vues.
- Comment créer une nouvelle base de données à l’aide du paradigme classique d’Entity Framework Code First.
- Comment récupérer et afficher des données.
- Comment modifier des données et activer la validation de données.

## <a name="getting-started"></a>Commencer

Commencez par exécuter Visual Web Developer 2010 Express (« Visual Web Developer » pour la forme courte) et sélectionnez **nouveau projet** à partir de la **Démarrer** page.

Visual Web Developer est un environnement de développement intégré ou IDE. Tout comme vous utilisez Microsoft Word pour écrire des documents, vous utiliserez un bus IDE pour créer des applications. Dans Visual Web Developer, il existe une barre d’outils en haut affichant les différentes options disponibles pour vous. Il existe également un menu qui fournit un autre moyen pour effectuer des tâches dans l’IDE. (Par exemple, au lieu de sélectionner **nouveau projet** à partir de la **Démarrer** page, vous pouvez utiliser le menu et sélectionnez **fichier** &gt; **denouveauprojet**.)

[![](intro-to-aspnet-mvc-3/_static/image4.png)](intro-to-aspnet-mvc-3/_static/image3.png)

## <a name="creating-your-first-application"></a>Créer votre première Application

Vous pouvez créer des applications à l’aide de Visual Basic ou Visual c# comme langage de programmation. Sélectionnez Visual c# sur la gauche, puis sélectionnez **ASP.NET MVC 3 Web Application**. Nommez votre projet « MvcMovie », puis **OK**. (Si vous préférez Visual Basic, basculez vers le [version Visual Basic](../vb/intro-to-aspnet-mvc-3.md) de ce didacticiel.)

![](intro-to-aspnet-mvc-3/_static/image5.png)

Dans le **nouveau projet ASP.NET MVC 3** boîte de dialogue, sélectionnez **Application Internet**. Vérifiez **utilisez HTML5 balisage** et laisser **Razor** en tant que le moteur d’affichage par défaut.

![](intro-to-aspnet-mvc-3/_static/image6.png)

Cliquez sur **OK**. Visual Web Developer a utilisé un modèle par défaut pour le projet ASP.NET MVC que vous venez de créer, afin que vous ayez une application fonctionne maintenant sans rien faire ! Il s’agit d’un simple « Hello World ! » projet et il l’un bon point de départ de votre application.

[![](intro-to-aspnet-mvc-3/_static/image8.png)](intro-to-aspnet-mvc-3/_static/image7.png)

Dans le menu **Déboguer**, sélectionnez **Démarrer le débogage**.

![](intro-to-aspnet-mvc-3/_static/image9.png)

Remarquez que le raccourci clavier pour démarrer le débogage F5.

F5 provoque Visual Web Developer pour démarrer un serveur web de développement et d’exécuter votre application web. Ensuite, Visual Web Developer lance un navigateur et ouvre la page d’accueil de l’application. Notez que la barre d’adresses du navigateur indique `localhost` et pas quelque chose comme `example.com`. C’est parce que `localhost` pointe toujours sur votre ordinateur local, qui dans ce cas est exécutée dans l’application que vous venez de créer. Lorsque Visual Web Developer exécute un projet web, un port aléatoire est utilisé pour le serveur web. Dans l’image ci-dessous, le numéro de port aléatoire est 43246. Lorsque vous exécutez l’application, vous verrez probablement un numéro de port différent.

![](intro-to-aspnet-mvc-3/_static/image10.png)

Emploi ce modèle par défaut vous donne deux pages à visiter et une page de connexion de base. L’étape suivante consiste à modifier le fonctionnement de cette application et en savoir un peu ASP.NET MVC dans le processus. Fermez votre navigateur et nous allons modifier du code.

>[!div class="step-by-step"]
[Next](adding-a-controller.md)

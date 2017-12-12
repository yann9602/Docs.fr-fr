---
uid: mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part1
title: "Présentation d’ASP.NET MVC | Documents Microsoft"
author: shanselman
description: "Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/14/2010
ms.topic: article
ms.assetid: bf4a1c19-0a94-4208-b268-a96ddcf26946
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part1
msc.type: authoredcontent
ms.openlocfilehash: b884c3c535929038f047e2c4ceedf9e7ca543292
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="intro-to-aspnet-mvc"></a>Présentation d’ASP.NET MVC
====================
par [Scott Hanselman](https://github.com/shanselman)

> > [!NOTE]
> > Une version mise à jour si ce didacticiel est disponible [ici](../../getting-started/introduction/getting-started.md) à l’aide de [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads). Le nouveau didacticiel utilise ASP.NET MVC 5, qui fournit de nombreuses améliorations de ce didacticiel.
> 
> 
> Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données. Visitez le [centre d’apprentissage ASP.NET MVC](../../../index.md) pour rechercher d’autres ASP.NET MVC didacticiels et exemples.


Nous allons en créer votre première Application Web ASP.NET MVC à l’aide [Visual Web Developer 2010 Express](https://www.microsoft.com/express/Web/). Nous allons effectuer une petite application de liste de films qui nous crée et liste de films.

## <a name="what-youll-build"></a>Ce que vous allez générer

Voici deux captures d’écran de l’application que vous allez générer. Vous aurez une table simple des films à l’aide de diverses colonnes.

[![Liste de films - Windows Internet Explorer (12)](getting-started-with-mvc-part1/_static/image2.png)](getting-started-with-mvc-part1/_static/image1.png)

Et vous aurez un formulaire de création afin que nous pouvons ajouter films à la liste.

[![Créer un film - Windows Internet Explorer (2)](getting-started-with-mvc-part1/_static/image4.png)](getting-started-with-mvc-part1/_static/image3.png)

## <a name="skills-youll-learn"></a>Vous allez apprendre des compétences

Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une Application de Web ASP.NET MVC à l’aide de Visual Studio. Vous apprendrez à :

- Comment créer un nouveau projet ASP.NET MVC
- Comment créer une nouvelle base de données avec SQL Server
- Comment créer des vues et les contrôleurs MVC ASP.NET
- Comment récupérer et afficher des données
- Comment modifier des données et activer la validation de données
- Comment mettre à jour le schéma de base de données

## <a name="get-started"></a>Bien démarrer

Commencer par exécuter Visual Web Developer 2010 Express (je vais l’appeler « VWD » par la suite), puis sélectionnez Nouveau projet à partir de l’écran d’accueil.

Visual Web Developer est un environnement de développement intégré ou IDE. Tout comme vous utilisez Microsoft Word pour écrire des documents, vous utiliserez un bus IDE pour créer des applications. Il existe une barre d’outils en haut affichant les différentes options disponibles pour vous, ainsi que le menu vous pouvez également avoir utilisé pour sélectionner le fichier | Nouveau projet.

[![Microsoft Visual Web Developer 2010 Express](getting-started-with-mvc-part1/_static/image6.png)](getting-started-with-mvc-part1/_static/image5.png)

## <a name="creating-your-first-application"></a>Créer votre première Application

Vous pouvez créer des applications à l’aide de Visual Basic ou Visual c#. Pour l’instant, sélectionnez Visual c# sur la gauche, puis choisissez « Application de Web ASP.NET MVC 2 ». Nommez votre projet « Films », puis cliquez sur OK.

[![Nouveau projet](getting-started-with-mvc-part1/_static/image8.png)](getting-started-with-mvc-part1/_static/image7.png)

Dans la partie droite est l’Explorateur de solutions affichant tous les fichiers et dossiers dans votre application. La fenêtre big au milieu est où vous modifiez votre code et passez la majeure partie de votre temps. Visual Studio a utilisé un modèle par défaut pour le projet ASP.NET MVC que vous venez de créer, afin que vous ayez une application fonctionne maintenant sans rien faire ! Il s’agit d’un simple « Hello World ! projet, qui est un bon point de départ pour notre application.

[![Microsoft Visual Web Developer 2010 Express](getting-started-with-mvc-part1/_static/image10.png)](getting-started-with-mvc-part1/_static/image9.png)

Sélectionnez le bouton « lecture » pour la barre d’outils.

![Démarrer le débogage](getting-started-with-mvc-part1/_static/image11.png)

Il s’agit d’une flèche verte pointant vers la droite qui compiler votre programme et démarrez votre application dans un navigateur web.

*Remarque : Vous pouvez à la place de votre clavier, appuyez sur la touche F5 ou sélectionnez Debug -&gt;démarrer le débogage à partir du menu « Debug ».*

Cela entraîne Visual Web Developer pour démarrer un serveur web de développement et d’exécuter notre application web (il n’y aucune configuration ou les étapes manuelles requises pour activer cette option). Puis il lance un navigateur et le configurer pour parcourir la page d’accueil de l’application. Ci-dessous, notez que la barre d’adresses du navigateur indique « localhost » et pas quelque chose comme exemple.com. C’est parce que localhost pointe toujours sur votre ordinateur local - qui dans ce cas est exécutée dans l’application que nous vient d’être générées.

[![Page d’accueil](getting-started-with-mvc-part1/_static/image13.png)](getting-started-with-mvc-part1/_static/image12.png)

En dehors de la zone de ce modèle par défaut donne vous deux pages à visiter et une page de connexion de base. Nous allons modifier le fonctionnement de cette application et en savoir un peu ASP.NET MVC dans le processus. Fermez votre navigateur et vous permet de modifier du code.

>[!div class="step-by-step"]
[Next](getting-started-with-mvc-part2.md)

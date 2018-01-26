---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: "Didacticiel : Mise en route avec SignalR 2 | Documents Microsoft"
author: pfletcher
description: "Ce didacticiel montre comment utiliser SignalR pour créer une application de conversation en temps réel. Vous allez ajouter SignalR à l’application web ASP.NET vide et créer une adresse de fournisseur HTML..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 8be851f5a2b1cca39f5f8f284ff1c002c486d7e8
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="tutorial-getting-started-with-signalr-2"></a>Didacticiel : Mise en route avec SignalR 2
====================
par [Patrick Fletcher](https://github.com/pfletcher)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)

> Ce didacticiel montre comment utiliser SignalR pour créer une application de conversation en temps réel. Vous ajoutez SignalR à l’application web ASP.NET vide et créer une page HTML pour envoyer et afficher les messages. 
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - .NET 4.5
> - SignalR version 2
>   
> 
> 
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a>À l’aide de Visual Studio 2012 avec ce didacticiel
> 
> 
> Pour utiliser Visual Studio 2012 avec ce didacticiel, procédez comme suit :
> 
> - Mise à jour votre [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) vers la dernière version.
> - Installer le [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
> - Dans Web Platform Installer, recherchez et installez **ASP.NET et 2013.1 des outils Web pour Visual Studio 2012**. Il installe les modèles Visual Studio pour les classes de SignalR comme **Hub**.
> - Certains modèles (tel que **classe de démarrage OWIN**) ne sera pas disponible ; dans ce cas, utilisez un fichier de classe à la place.
> 
> 
> ## <a name="tutorial-versions"></a>Versions du didacticiels
> 
> Pour plus d’informations sur les versions antérieures de SignalR, consultez [SignalR des Versions antérieures](../older-versions/index.md).
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer dans les commentaires en bas de la page. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


## <a name="overview"></a>Vue d'ensemble

Ce didacticiel présente le développement de SignalR en montrant comment créer une application simple conversation basée sur navigateur. Vous ajoute la bibliothèque SignalR à l’application web ASP.NET vide, créez une classe de concentrateur pour envoyer des messages aux clients et créer une page HTML qui permet aux utilisateurs d’envoyer et recevoir des messages de conversation. Pour obtenir un didacticiel similaire qui montre comment créer une application de conversation dans MVC 5 à l’aide d’une vue MVC, consultez [prise en main SignalR 2 et MVC 5](tutorial-getting-started-with-signalr-and-mvc.md).

> [!NOTE]
> Ce didacticiel montre comment créer des applications SignalR dans la version 2. Pour plus d’informations sur les modifications entre SignalR 1.x et 2, consultez [SignalR de la mise à niveau les projets 1.x](../releases/upgrading-signalr-1x-projects-to-20.md) et [Notes de publication Visual Studio 2013](../../../visual-studio/overview/2013/release-notes.md#TOC13).

SignalR est une bibliothèque de .NET open source pour la création d’applications web qui requièrent une intervention de l’utilisateur en direct ou mises à jour des données en temps réel. Exemples applications sociales, jeux multi-utilisateur, météo de collaboration et de news, entreprise ou des applications de mise à jour financière. Il s’agit souvent d’applications en temps réel.

SignalR simplifie le processus de création d’applications en temps réel. Elle inclut une bibliothèque de serveur ASP.NET et d’une bibliothèque cliente de JavaScript pour le rendre plus facile à gérer les connexions client-serveur et d’envoyer des mises à jour de contenu aux clients. Vous pouvez ajouter la bibliothèque SignalR pour une application ASP.NET existante pour bénéficier de fonctionnalités en temps réel.

Le didacticiel présente les tâches de développement SignalR suivantes :

- Ajout de la bibliothèque de SignalR pour une application web ASP.NET.
- Création d’une classe de concentrateur pour transmettre le contenu aux clients.
- Création d’une classe de démarrage OWIN pour configurer l’application.
- À l’aide de la bibliothèque jQuery SignalR dans une page web pour envoyer des messages et d’afficher les mises à jour à partir du concentrateur.

La capture d’écran suivante montre l’application de conversation en cours d’exécution dans un navigateur. Chaque nouvel utilisateur peut publier des commentaires et voir les commentaires ajoutés après que l’utilisateur joint à la conversation.

![Instances de conversation](tutorial-getting-started-with-signalr/_static/image1.png)

Sections :

- [Le projet](#setup)
- [Exécuter l’exemple](#run)
- [Examinez le Code](#code)
- [Étapes suivantes](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>Le projet

Cette section montre comment utiliser Visual Studio 2013 et SignalR version 2 pour créer une application web ASP.NET vide, ajoutez SignalR et créer l’application de la conversation.

Conditions préalables :

- Visual Studio 2013. Si vous n’avez pas Visual Studio, consultez [téléchargements ASP.NET](https://www.asp.net/downloads) pour obtenir le Visual Studio 2013 Express outil de développement gratuit.

Les étapes suivantes utilisent Visual Studio 2013 pour créer une Application de Web ASP.NET vide et ajouter la bibliothèque SignalR :

1. Dans Visual Studio, créez une Application Web ASP.NET.

    ![Créer le web](tutorial-getting-started-with-signalr/_static/image2.png)
2. Dans le **nouveau projet ASP.NET** fenêtre, laissez le champ **vide** sélectionné, cliquez sur **créer un projet de**.

    ![Créer le site web vide](tutorial-getting-started-with-signalr/_static/image3.png)
3. Dans **l’Explorateur de solutions**, cliquez sur le projet, sélectionnez **ajouter | Classe de concentrateur SignalR (v2)**. Nom de la classe **ChatHub.cs** et ajoutez-le au projet. Cette étape crée la **ChatHub** de classe et l’ajoute au projet un ensemble de fichiers de script et les références d’assembly qui prennent en charge SignalR.

    > [!NOTE]
    > Vous pouvez également ajouter SignalR pour un projet en ouvrant le **outils | Gestionnaire de Package de bibliothèque | Console du Gestionnaire de package** et en exécutant une commande :

    `install-package Microsoft.AspNet.SignalR`

    Si vous utilisez la console pour ajouter SignalR, créer la classe de concentrateur SignalR comme une étape distincte après avoir ajouté SignalR.

    > [!NOTE]
    > Si vous utilisez Visual Studio 2012, le **classe de concentrateur SignalR (v2)** modèle ne sera pas disponible. Vous pouvez ajouter un brut **classe** appelée `ChatHub` à la place.
4. Dans **l’Explorateur de solutions**, développez le nœud Scripts. Bibliothèques de scripts de jQuery et SignalR sont visibles dans le projet.
5. Remplacez le code dans la nouvelle **ChatHub** classe par le code suivant.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. Dans **l’Explorateur de solutions**, cliquez sur le projet, puis cliquez sur **ajouter | Classe de démarrage OWIN**. Nommez la nouvelle classe `Startup` et cliquez sur OK.

    > [!NOTE]
    > Si vous utilisez Visual Studio 2012, le **classe de démarrage OWIN** modèle ne sera pas disponible. Vous pouvez ajouter un brut **classe** appelée `Startup` à la place.
7. Modifiez le contenu de la nouvelle classe de démarrage comme suit.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. Dans **l’Explorateur de solutions**, cliquez sur le projet, puis cliquez sur **ajouter | Page HTML**. Nommez la nouvelle page `index.html`.
    >[!NOTE]
    >Vous devrez peut-être modifier les numéros de version pour les références aux bibliothèques JQuery et SignalR
9. Dans **l’Explorateur de solutions**, avec le bouton droit de la page HTML que vous venez de créer, cliquez sur **définir comme Page de démarrage**.
10. Remplacez le code par défaut dans la page HTML par le code suivant.

    > [!NOTE]
    > Une version ultérieure des scripts SignalR peut-être être installée par le Gestionnaire de package. Vérifiez que les références de script ci-dessous correspondent aux versions des fichiers de script dans le projet (ils sera différents si vous avez ajouté SignalR à l’aide de NuGet au lieu d’ajouter un concentrateur.)

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]
11. **Enregistrer tous les** pour le projet.

<a id="run"></a>

## <a name="run-the-sample"></a>Exécuter l’exemple

1. Appuyez sur F5 pour exécuter le projet en mode débogage. Charge de la page HTML dans une instance du navigateur et les invite à entrer un nom d’utilisateur.

    ![Entrer un nom d'utilisateur](tutorial-getting-started-with-signalr/_static/image4.png)
2. Entrez un nom d’utilisateur.
3. Copiez l’URL de la ligne d’adresse du navigateur et l’utiliser pour ouvrir les deux instances du navigateur plus. Dans chaque instance du navigateur, entrez un nom d’utilisateur unique.
4. Dans chaque instance du navigateur, ajoutez un commentaire, puis cliquez sur **envoyer**. Les commentaires doivent s’afficher dans toutes les instances du navigateur.

    > [!NOTE]
    > Cette application de conversation simple ne conserve pas le contexte de la discussion sur le serveur. Le concentrateur diffuse des commentaires à tous les utilisateurs actuels. Les utilisateurs qui participer à la conversation ultérieurement voient des messages ajoutés à partir du moment qu'ils se connectent.

    La capture d’écran suivante montre l’application de la conversation en cours d’exécution dans les trois instances de navigateur, qui sont mis à jour lorsqu’une instance envoie un message :

    ![Navigateurs de conversation](tutorial-getting-started-with-signalr/_static/image5.png)
5. Dans **l’Explorateur de solutions**, inspecter la **Documents de Script** nœud pour l’application en cours d’exécution. Il existe un fichier de script nommé **concentrateurs** que la bibliothèque SignalR génère dynamiquement lors de l’exécution. Ce fichier gère la communication entre le script jQuery et le code côté serveur.

    ![](tutorial-getting-started-with-signalr/_static/image6.png)

<a id="code"></a>

## <a name="examine-the-code"></a>Examinez le Code

L’application de la conversation SignalR illustre deux tâches de développement SignalR base : création d’un hub en tant que l’objet principal de coordination sur le serveur et à l’aide de la bibliothèque jQuery SignalR pour envoyer et recevoir des messages.

### <a name="signalr-hubs"></a>Concentrateurs SignalR

Dans l’exemple de code la **ChatHub** classe dérive de la **Microsoft.AspNet.SignalR.Hub** classe. Dérivation à partir de la **Hub** classe est une méthode utile pour générer une application SignalR. Vous pouvez créer des méthodes publiques sur votre classe de concentrateur et puis accéder à ces méthodes en les appelant à partir de scripts dans une page web.

Dans le code de la conversation, les clients appellent le **ChatHub.Send** méthode pour envoyer un message. Le concentrateur à son tour envoie le message à tous les clients en appelant **Clients.All.broadcastMessage**.

Le **envoyer** méthode illustre plusieurs concepts de hub :

- Déclarer des méthodes publiques sur un concentrateur, afin que les clients peuvent appeler les.
- Utilisez le **Microsoft.AspNet.SignalR.Hub.Clients** propriétés dynamiques pour accéder à tous les clients connectés à ce concentrateur.
- Appeler une fonction sur le client (telles que le `broadcastMessage` (fonction)) pour mettre à jour des clients.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery"></a>SignalR et jQuery

La page HTML dans l’exemple de code montre comment utiliser la bibliothèque jQuery SignalR pour communiquer avec un concentrateur SignalR. Les tâches essentielles dans le code déclarez un proxy pour référencer le concentrateur, déclarez une fonction qui peut appeler le serveur pour envoyer aux clients et démarrage d’une connexion pour envoyer des messages au concentrateur.

Le code suivant déclare une référence à un concentrateur proxy.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> Dans JavaScript, la référence à la classe de serveur et de ses membres est en casse mixte. L’exemple de code fait référence à c# **ChatHub** classe en JavaScript en tant que **chatHub**.


Le code suivant est la façon dont vous créez une fonction de rappel dans le script. La classe de concentrateur sur le serveur appelle cette fonction pour transmettre les mises à jour à chaque client. Les deux lignes que HTML encoder le contenu avant de les afficher sont facultatifs et affichent un moyen simple pour empêcher l’injection de script.

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

Le code suivant montre comment ouvrir une connexion avec le concentrateur. Le code démarre la connexion et qu’il passe ensuite une fonction pour gérer l’événement de clic sur le **envoyer** bouton dans la page HTML.

> [!NOTE]
> Cette approche garantit que la connexion est établie avant l’exécution du Gestionnaire d’événements.


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

<a id="next"></a>

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris que SignalR est une infrastructure pour la génération d’applications web en temps réel. Vous avez également appris à plusieurs tâches de développement SignalR : comment ajouter SignalR pour une application ASP.NET, la création d’une classe de concentrateur et comment envoyer et recevoir des messages à partir du concentrateur.

Pour une procédure pas à pas sur la façon de déployer l’exemple d’application SignalR dans Azure, consultez [à l’aide de SignalR avec les applications Web dans Azure App Service](../deployment/using-signalr-with-azure-web-sites.md). Pour plus d’informations sur la façon de déployer un projet web Visual Studio sur un Site Web de Windows Azure, consultez [créer une application web ASP.NET dans Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).

Pour en savoir plus avancées concepts de développements SignalR, visitez les sites suivants pour le code source de SignalR et ressources :

- [Projet SignalR](http://signalr.net)
- [SignalR Github et exemples](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)
